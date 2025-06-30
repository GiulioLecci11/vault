[[Back End- Deep dive]]
#AI 
## New agents (datapizzai >= 3.0.0)

Essenzialmente NON esistono più AgentInput, AgentOutput (result per adesso è già un text, non c'è più bisogno di fare result.text), @on_call ecc.. 

Adesso per realizzare ciò che prima si faceva con una @on_call si scrive una funzione che al suo interno chiami la invoke (o a_invoke) dell'agente. 
Per esempio dall'aggregator agent di abb (vediamo anche l'init):

```python
from datapizzai.agents import Agent
from datapizzai.clients import AzureOpenAIClient


class AggregatorAgent(Agent):
    def __init__(self):
        client = AzureOpenAIClient(
            api_key=settings.azure_openai.azure_openai_api_key,
            azure_endpoint=settings.azure_openai.azure_openai_endpoint,
            api_version=settings.azure_openai.azure_openai_api_version,
            model=settings.azure_openai.model_name,
            temperature=settings.azure_openai.temperature,
        )
        super().__init__(
            name="aggregator_agent",
            system_prompt="""
            You are an expert in legal document analysis.
            You are given a list of policies and a list of extracted information.
            You need to aggregate the information policy wise. 
            
            More specifically, you need to give an explation for each policy what is the result FILE-WISE, i.e.
            Policy <policy_name>:
                File <file_name>: <result>
                    Explanation: <explanation>
            
            Example:
            Policy "Privacy Policy":
                File "privacy_policy.pdf": <span style="color: green;">Compliant</span>
                    Explanation: The document contains all the required information.
                File "terms_of_service.pdf": <span style="color: red;">Not Compliant</span>
                    Explanation: The document does not contain the required information.
            """,
            client=client,
        )

async def call_aggregator_agent(self, commercial_conditions_details: Dict) -> str:
        """
        Function to call the aggregator agent.
        Args:
            commercial_conditions_details: the commercial conditions details with the following structure:
            {
                'commercial_conditions_details': {
                        commercial_condition_id: {
                                    file_name: {
                                                    'extracted_information': {
                                                                            'information_content':
                                                                            'information_page':
                                                                            },
                                                    'checking_result':
                                                    'comment':
                                                }
                                    }
                            }
            }
        Returns:
            aggregated_results: the aggregated results containing the overview and the commercial conditions details
        """
        commercial_conditions_details_text = json.dumps(commercial_conditions_details)
        results = await self.a_invoke(commercial_conditions_details_text)
        # self._log_results(results)
        aggregated_results = json.dumps(
            {
                "overview": results,
                "commercial_conditions_details": commercial_conditions_details[
                    "commercial_conditions_details"
                ],
            }
        )
        return aggregated_results
```

## Parallelismo sulle istanze + async

```python
tasks = []

for commercial_conditions_cluster in commercial_conditions_clusters:
    cluster_agent = BaseClusterAgent(doc, commercial_conditions_cluster)
    tasks.append(cluster_agent.call_cluster_agent())

cluster_result = await asyncio.gather(*tasks) # Gather è ciò che crea il parallelismo, facendo partire i vari task dentro tasks in parallelo e mettendo i risultati dentro cluster_result come una lista di risultati

# Siccome usiamo await, la cpu viene liberata mentre si aspettano i risultati di tasks e quindi possiamo eseguire altro, quando poi cluster_result sarà riempito l'esecuzione riprenderà
```
Utilizzo nel file ai_baseline_manager.py, sia nella funzione '*async def run_policy_checking_pipeline'*  sia nella *'analyze_doc'* e questo perché abbiamo un **doppio** livello di parallelizzazione, quindi parallelizziamo due funzioni async
```python
import asyncio

async def _analyze_doc(self, doc, commercial_conditions_ids) -> str:
        """
        Analyze a document for a given set of commercial conditions.
        Args:
            doc: The document to analyze.
            commercial_conditions_ids: The IDs of the commercial conditions to check.
        Returns:
            doc_result: The result of the policy checking pipeline for the given document.
        """
        commercial_conditions_clusters = create_clusters(commercial_conditions_ids)
        cluster_result = []
        # Create a factory for the cluster agents
        factory = ClusterAgentFactory(doc, commercial_conditions_clusters)
        tasks = []
        for cluster_index in range(len(commercial_conditions_clusters)):
            cluster_agent = factory.create_cluster_agent(cluster_index)
            tasks.append(cluster_agent.call_cluster_agent())
        # gather create parallelism and automatically aggregate the results
        cluster_result = await asyncio.gather(*tasks)
        doc_result = self._aggregate_cluster_results(cluster_result)
        return json.dumps(doc_result)


async def run_policy_checking_pipeline(
        self,
        commercial_conditions_ids: Sequence[int],
        analysis_mode: str = "baseline",
        check_session_id: int = None,
        crud: CRUD | None = None,
        user: User | None = None,
        file_handler: FileHandler = None,
    ) -> Dict:
        """
        Run the policy checking pipeline.
        Args:
            commercial_conditions_ids: The IDs of the commercial conditions to check.
            analysis_mode: The modality of the policy checking pipeline.
            check_session_id: The ID of the check session to update.
            crud: The CRUD instance to update the database.
        Returns:
            result:The result of the policy checking pipeline.
        """
        """
        load docs() -> n * analyze doc() -> aggregator_agent()
        """
        with ContextTracing().trace("run_policy_checking_pipeline"):
            docs = file_handler.read_files()
            docs_results = []
            tasks = []
            for doc in docs:
                tasks.append(self._analyze_doc(doc, commercial_conditions_ids))
            docs_results = await asyncio.gather(*tasks)  # gather create parallelism
            aggregated_doc_results = self._aggregate_doc_results(docs_results)
            aggregator_agent = AggregatorAgent()
            aggregated_final_result = await aggregator_agent.call_aggregator_agent(
                aggregated_doc_results
            )
            final_result = json.loads(aggregated_final_result)
        return final_result
```
## Tracing
Utilizzo base (ai_baseline_manager.py, prima della funzione async def run_policy_checking_pipeline):
```python
from datapizzai.tracing import ContextTracing
# Poi subito prima della funzione da tracciare, mettere
with ContextTracing().trace("run_policy_checking_pipeline"):
	[resto della funzione]

```
Dentro il file app.py mettere questo per "loggare" il tracing su zipkin (SERVE CHE FEDERAFFO PASSI QUALCOSA DA METTERE SU DOCKER DESKTOP)
```python
from opentelemetry import trace
from opentelemetry.exporter.zipkin.json import ZipkinExporter
from opentelemetry.sdk.trace.export import SimpleSpanProcessor
from opentelemetry.sdk.resources import Resource
from opentelemetry.semconv.resource import ResourceAttributes
from opentelemetry.sdk.trace import TracerProvider
resource = Resource.create(
    {
        ResourceAttributes.SERVICE_NAME: "abb",
    }
)
trace.set_tracer_provider(TracerProvider(resource=resource))
zipkin_url = "http://localhost:9411/api/v2/spans"
zipkin_exporter = ZipkinExporter(
    endpoint=zipkin_url,
)
tracer_provider = trace.get_tracer_provider()
span_processor = SimpleSpanProcessor(zipkin_exporter)
trace.get_tracer_provider().add_span_processor(span_processor)
```