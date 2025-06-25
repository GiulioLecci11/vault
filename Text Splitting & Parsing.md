## Levels of Text Splitting

  
Source: [Github - RetrievalTutorials](https://github.com/FullStackRetrieval-com/RetrievalTutorials/blob/main/tutorials/LevelsOfTextSplitting/5_Levels_Of_Text_Splitting.ipynb)

## Level 1: Character Splitting

- Simple static character chunks of data

  

## Level 2: Recursive Character Text Splitting

- Recursive chunking based on a list of separators

  

## Level 3: Document Specific Splitting

- Various chunking methods for different document types (PDF, Python, Markdown)

  

**IMPORTANTE**: I needed to play with the chunk size to get a clean result like that. You'll likely need to do the same for yours which is why using evaluations to determine optimal chunk sizes is crucial.

  

**IMPORTANTE per PDF**: A very convenient way to do this is with Unstructured (permette pure di vedere il testo come html e di processare immagini nel testo), a library dedicated to making your data LLM ready. (Anche se comunque gemini dovrebbe oneshottare tutto)

  

## Level 4: Semantic Splitting

- Embedding walk based chunking (ci sono diversi modi di farlo e sono tutti handmade quindi no singole funzioni di libreria, di uno di questi c'è il codice)

- Versione generalizzata del metodo 4: https://python.langchain.com/docs/how_to/semantic-chunker/

  

## Level 5: Agentic Splitting

- Experimental method of splitting text with an agent-like system

- Good for if you believe that token cost will trend to $0.00

- Taking level 4 even further - can we instruct an LLM to do this task (esempio abbastanza chiarificatore)

  

## *Bonus Level:* Alternative Representation Chunking + Indexing

- Derivative representations of your raw text that will aid in retrieval and indexing

- When you index, you're making a choice about how you want to represent your data in your knowledge base

- Qui si parla di Multi-Vector Indexing - This is when you do semantic search for a vector that is derived from something other than your raw text:

- **Summaries**: A summary of your chunk (Instead of embedding your raw text, embed a summary of your raw text which will have more dense information)

- **Hypothetical questions**: Good for chat messages used as knowledge base (This is especially helpful when you have sparse unstructured data, like chat messages. Say you were to build a bot that uses slack conversations as a knowledge base. Trying to do semantic search on raw chat messages might not have the greatest results. However, if you were to generate hypothetical questions that the slack messages would answer, then when you get a new question in, you'll likely have a better chance matching. The code for this will be the same as the summary code, but instead of asking the LLM to make a summary, you'll ask it for hypothetical questions.)

- **Child Documents - Parent Document Retriever**: The hypothesis with the PDR is that smaller chunks have a higher likelihood of being matched semantically with a potential query. However, those smaller chunks may not have all the context they need, so instead of passing the smaller chunks to your LLM, get the parent chunk of the smaller chunk. This means you get a larger chunk which the smaller chunk is placed in.

- **Graph Based Chunking**: Transposing your raw text into a graph structure, useful if your data is rich with entities, relationships, and connections



#AI 
[[RAG Concepts]]
