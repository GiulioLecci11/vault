# AWS - Concetti principali

## Account e accesso

- Si consiglia di utilizzare un account condiviso come root account per evitare la perdita di accesso quando qualcuno lascia l'azienda.
- I servizi preferiti vengono salvati nei cookie come "favorites".

## Regioni AWS

- Una regione AWS è una "posizione fisica nel mondo dove i data center sono raggruppati"
- La regione attuale è indicata nell'angolo in alto a destra della console
- La scelta della regione influisce su:
    - Velocità (si consiglia di scegliere la regione più vicina al cliente)
    - Disponibilità dei servizi (non tutti sono disponibili in ogni regione)
    - Conformità legale (alcuni dati devono risiedere in regioni specifiche)
- Northern Virginia ha tutti i servizi ed è spesso usata per formazione
- Ogni regione AWS comprende due o più Availability Zones (AZ)
    - Le AZ sono aree separate all'interno di una regione che possono contenere più data center
    - Le AZ forniscono ridondanza e tolleranza ai guasti
    - Sono interconnesse con bassa latenza
- Le Edge Locations sono connesse alle regioni AWS e funzionano come cache per migliorare la velocità

## Modello di pagamento

- Pay-as-you-go: nessun contratto a lungo termine, paghi solo ciò che usi
- Risparmio con impegno: sconti per utilizzo a lungo termine
- Economia di scala: aumentando l'uso aumentano gli sconti
- AWS Cost Explorer: strumento per monitorare i costi
- I costi possono variare in base alla regione

## Tipi di servizi

- Servizi globali: disponibili in diverse regioni
- Servizi regionali: disponibili in una sola regione (es. EC2)
    - Possibile avere servizi regionali in più regioni usando "global view"
- Attenzione: quando si interrompe un servizio come EC2, si continua a pagare per lo storage ma non per il calcolo

## Cloud Computing

- Definizione: fornitura on-demand di risorse IT via internet con pagamento a consumo
- Modelli principali:
    1. Infrastructure as a Service (IaaS): EC2, RDS, S3
        - S3 conserva 3 copie in 3 diverse AZ per ridondanza
    2. Platform as a Service (PaaS): AWS Elastic Beanstalk
    3. Software as a Service (SaaS): prodotti software completi gestiti dal fornitore

## Sicurezza

- Modello di responsabilità condivisa:
    - AWS è responsabile della sicurezza DEL cloud (infrastruttura fisica, rete)
    - Il cliente è responsabile della sicurezza NEL cloud (dati, configurazioni)
- AWS Well Architected Tool: guida per gestire l'architettura secondo le best practice
- AWS Pricing Calculator: calcola il costo totale di proprietà (TCO)

## Architettura a microservizi

- Le applicazioni sono costruite con parti diverse (database, GUI, server) collegate in modo flessibile
- Se una parte fallisce, il resto continua a funzionare
- Questo è l'approccio utilizzato per costruire app con AWS

## Servizi principali

### S3 (Simple Storage Service)

- Simile a Google Drive, ma i file hanno URL accessibili direttamente se i permessi lo consentono
- I permessi si gestiscono tramite policy JSON

### EC2 (Elastic Compute Cloud)

- Permette di lanciare macchine virtuali configurabili
- Elimina la necessità di gestire l'hardware
- Opzioni di acquisto:
    1. Istanze on-demand: pagamento al secondo, senza impegno
    2. Saving plans: riservate per periodi più lunghi
    3. Host dedicati: macchine fisiche dedicate esclusivamente all'utente
    4. Istanze spot: capacità EC2 inutilizzata a basso costo (può terminare con 2 minuti di preavviso)

### VPC (Virtual Private Cloud)

- Rete privata all'interno di AWS
- Un account riceve una VPC di default
- Uso comune: macchina EC2 con sito web in subnet pubblica e database RDS in subnet privata

### RDS (Relational Database Service)

- Creazione di database di varie dimensioni con diversi motori
- Configurabile e gestibile

### IAM (Identity and Access Management)

- Creazione di identità, gruppi di utenti e definizione delle policy di accesso

### Lambda

- Creazione di funzioni accessibili via URL o tramite trigger
- Esecuzione di codice senza server

### CloudWatch

- Creazione di allarmi per vari eventi (superamento soglie di spesa, uso eccessivo di CPU)

## Risorse di storage

### Tipi di storage

- Storage on-premises: server locali accessibili via intranet
- Cloud storage: server remoti accessibili via internet
    - Vantaggi: efficienza dei costi, sicurezza, accessibilità, scalabilità, gestione semplificata, backup in diverse AZ

### Tipi di cloud storage

- Block storage: file divisi in blocchi salvati singolarmente
    - Servizio AWS: Amazon Elastic Block Store (EBS)
    - Scelta tra HDD (più economici) e SSD (più veloci)
- File storage: struttura gerarchica di dati
    - Servizio AWS: Amazon Elastic File System (EFS)
- Object storage: file memorizzati come oggetti con attributi, chiavi e metadati
    - Servizio AWS: Amazon S3
    - Classi di storage in base alla frequenza di accesso (standard, accesso frequente, accesso infrequente, Glacier)
    - Intelligent Tiering per dati con accesso variabile
    - Gestisce versioning e controllo accessi

## Risorse di calcolo

### Vantaggi del cloud computing

- Pagamento solo per l'uso effettivo
- Nessun investimento iniziale
- Scalabilità rapida (autoscaling disponibile)
- Servizi affidabili con tolleranza ai guasti

### Metodi di calcolo

- Istanze: macchine virtuali completamente configurabili
- Container: virtualizzazione di app e risorse senza sistema operativo completo
- Serverless computing: esecuzione di codice senza gestione dei server
- Hybrid: approccio che collega elementi cloud con on-premises

### EC2 Instances

- Organizzazione: Region → VPC → Availability zone → Subnet → Security group
- Subnet: insieme di IP in una VPC (pubblico o privato)
- Security group: firewall virtuale che controlla traffico in entrata/uscita
- AMI (Amazon Machine Image): immagine della macchina con OS, app, permessi
- Instance types: configurazioni predefinite di capacità e memoria
    - Formato: t3.large (famiglia t, generazione 3, dimensione large)
- Scaling:
    - Verticale: aumentare potenza della singola istanza (richiede stop/restart)
    - Orizzontale: aggiungere più istanze (può essere automatizzato)
- Elastic Load Balancing: distribuzione del traffico tra istanze
- Storage:
    - EBS (Elastic Block Store): storage persistente
    - EFS (Elastic File System): storage condiviso tra istanze
    - Instance store: storage fisico non persistente

## Networking

### Componenti di rete

- Network: 2+ nodi (host/server o client)
- Dispositivi di connessione:
    - Switch: collega dispositivi inviando pacchetti a destinatari specifici
    - Router: dispositivi intelligenti che filtrano dati tra reti

### Modello OSI

1. Fisico: collegamento elettrico
2. Data link: rilevamento errori, switch
3. Network: router, connessione tra LAN
4. Trasporto: protocolli di trasferimento, controllo flusso, ACK
5. Session: apertura/chiusura sessioni
6. Presentation: formattazione informazioni
7. Application: interazione con l'utente

### Indirizzi IP

- IPv4: 32 bit (4 cifre max 255)
- IPv6: 128 bit (8 gruppi alfanumerici)
- Subnet mask: indica quali cifre dell'IPv4 possono variare
    - /24 significa che i primi 24 bit (3 cifre) sono fissi
    - /32 è un indirizzo fisso, /0 permette a tutti i bit di cambiare

### Amazon VPC

- Servizio specifico per regione
- Permette di creare reti nel cloud con altri servizi
- Caratteristiche:
    - Cost-effective: si paga solo per i servizi utilizzati
    - Monitorabile tramite VPC flow logs
    - Una VPC non può estendersi su più regioni
    - Una subnet non può estendersi su più AZ
    - Massimo 5 VPC per regione
- Componenti:
    - CIDR block: range di indirizzi IP (/16 a /28)
    - Subnet: 5 indirizzi riservati per AWS
    - Gateway: punto di uscita verso internet o VPN
    - Tabelle di routing: specificano percorsi nella rete
    - Security group: firewall a livello di istanza (stateful)
    - Network ACL: firewall a livello di subnet (stateless)

## Database

### Modelli di dati

- Dati strutturati: salvati in tabelle correlate
- Dati non strutturati: salvati in file senza struttura
- Dati semi-strutturati: struttura flessibile

### Proprietà

- ACID (database strutturati): Atomicità, Consistenza, Isolamento, Durabilità
- BASE: Basically Available, Soft state, Eventually consistent

### Tipi di elaborazione

- OLTP: elaborazione transazioni online (operazioni semplici)
- OLAP: elaborazione analitica online (analisi multidimensionale)

### Tipi di database

- Relazionali (dati strutturati):
    - Organizzati in tabelle con chiavi primarie e foreign key
    - Linguaggio SQL
    - Vantaggi: facilità d'uso, affidabilità, riduzione ridondanza
- Non relazionali:
    - Dinamici, vari formati (chiave-valore, documenti, grafi)
    - Vantaggi: scalabilità, flessibilità, API funzionali

### Amazon RDS

- Servizio cost-efficient con amministrazione automatizzata
- Autoscaling integrato
- Opzioni: on-demand o reserved
- Servizio fully managed (AWS gestisce patching, backup, hardware)
- Motori supportati: Microsoft, MySQL, MariaDB, Oracle, PostgreSQL, Amazon Aurora
- Funzionalità:
    - Multi-AZ: copia secondaria sincronizzata per disponibilità
    - Read-replicas: copie asincrone per operazioni di lettura
    - Backup: automatici (0-35 giorni) o manuali (S3)

## Operazioni cloud

### Strumenti di gestione

- AWS WA Tool: framework per valutare architetture
- AWS Cost Management: monitoraggio e ottimizzazione costi
    - Cost Explorer: visualizzazione costi con grafici e previsioni
    - Billing Console: gestione metodi pagamento
    - AWS Budgets: notifiche su soglie di spesa
- AWS Support: piani di supporto (basic, developer, business, enterprise on-ramp, enterprise)
- AWS Quota: limiti di risorse allocabili per servizio/regione
- AWS Trusted Advisor: confronto infrastruttura con best practice
- AWS Health Dashboard: informazioni sulla disponibilità dei servizi
- Amazon CloudWatch: raccolta metriche e log
- AWS CloudTrail: monitoraggio chiamate API
- AWS Systems Manager: sicurezza per ambienti cloud
- AWS Config: registrazione e normalizzazione configurazioni
- AWS EventBridge: creazione applicazioni event-driven
- AWS Organizations: gestione più account con ruoli e gruppi

## Sicurezza

### Principi di sicurezza

- Least privilege: utenti hanno solo i permessi minimi necessari
- IAM: gestione autenticazione e autorizzazione

### Livelli di sicurezza

1. Perimeter: guardie, tecnologie anti-intrusione
2. Environment: posizionamento data center
3. Infrastructure: alimentazione, rilevamento incendi
4. Data: protezione dati cliente

### IAM (Identity and Access Management)

- Users: identità singole (persone o applicazioni)
- Groups: insiemi di utenti con permessi condivisi
- Roles: permessi temporanei senza credenziali associate
- Policies: file JSON che definiscono permessi
- Credenziali:
    - Console: username/password per accesso web
    - Programmatic: chiavi pubbliche/private per codice
- Policy multiple:
    - In caso di conflitti prevale la più restrittiva
    - Deny esplicito > Allow esplicito > (nessuna menzione)

### Altri servizi di sicurezza

- Amazon Cognito: autenticazione per web app
- AWS KMS: gestione chiavi crittografiche
- AWS Secrets Manager: gestione credenziali e segreti
- AWS Shield: protezione DDoS
- Amazon Inspector: scansione sicurezza applicazioni
- Amazon GuardDuty: rilevamento minacce con ML

## Serverless

### Architetture

- Approccio monolitico (tightly coupled): scalabilità complessa, più punti di fallimento
- Microservizi (loosely coupled): servizi indipendenti via API

### Serverless computing

- Sviluppo senza gestione server
- Architetture event-driven:
    - Event producer: genera l'evento
    - Event router: filtra e instrada eventi
    - Event consumer: agisce in base all'evento

### AWS Lambda

- Esecuzione codice senza gestione server
- Linguaggi supportati: Node.js, Python, Java, ecc.
- Invocazione: HTTP(S) API o eventi AWS
- Monitoraggio: CloudWatch o X-Ray
- Pagamento solo per chiamate effettive
- Modelli di invocazione:
    - Synchronous: attende risposta
    - Asynchronous: non attende risposta
    - Polling: simile a sincrone con gestione errori
- Fasi execution environment: INIT, INVOKE, SHUTDOWN
- Limiti:
    - Memoria (fino a 10GB)
    - Timeout (max 15 minuti)
    - Concurrency (invocazioni simultanee)

### Altri servizi serverless

- Amazon SQS: code di messaggi
- Amazon SNS: notifiche cloud (publish-subscribe)
- Lambda@Edge: applicazioni distribuite globalmente
- API Gateway: gestione API
- AWS Fargate: tecnologie container
- AWS Step Functions: workflow visuale per processi
#miscellaneous