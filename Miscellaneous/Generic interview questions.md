[[Generic non technical interview questions]]
Inheritance:Permette a una classe di ereditare attributi e metodi da un'altra classe, favorendo il riutilizzo del codice e l'organizzazione delle gerarchie.
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        pass  # Metodo che verrà definito nelle sottoclassi

class Dog(Animal):  # Dog eredita da Animal
    def speak(self):
        return f"{self.name} says Woof!"

class Cat(Animal):  # Cat eredita da Animal
    def speak(self):
        return f"{self.name} says Meow!"

# Uso
dog = Dog("Buddy")
cat = Cat("Whiskers")
print(dog.speak())  # Output: Buddy says Woof!
print(cat.speak())  # Output: Whiskers says Meow!



Polymorphism:Consente a funzioni o metodi di comportarsi in modo diverso a seconda del tipo di dato o oggetto con cui lavorano. Favorisce la flessibilità del codice.
def animal_sound(animal):
    print(animal.speak())

# Uso con diversi tipi di oggetti
animal_sound(dog)  # Output: Buddy says Woof!
animal_sound(cat)  # Output: Whiskers says Meow!

In questo esempio, la funzione animal_sound accetta un parametro animal e chiama il metodo speak() su di esso. Anche se dog e cat sono oggetti di classi diverse (Dog e Cat), entrambi rispondono al metodo speak in base alla loro implementazione specifica.



Encapsulation:Nasconde i dettagli interni di una classe, consentendo l'accesso ai dati solo attraverso metodi definiti, proteggendo l'integrità dei dati.
class BankAccount:
    def __init__(self, balance):
        self.__balance = balance  # Attributo privato

    def deposit(self, amount):
        if amount > 0:
            self.__balance += amount

    def withdraw(self, amount):
        if 0 < amount <= self.__balance:
            self.__balance -= amount
            return amount
        else:
            print("Insufficient balance")
            return 0

    def get_balance(self):
        return self.__balance

# Uso
account = BankAccount(100)
account.deposit(50)
print(account.get_balance())  # Output: 150
account.withdraw(30)
print(account.get_balance())  # Output: 120

In questo esempio, __balance è un attributo privato e non può essere modificato direttamente dall’esterno della classe. I metodi deposit, withdraw e get_balance offrono un’interfaccia sicura per accedere e modificare il bilancio.



Introspection: Permette al codice di ispezionare e interagire con se stesso. In Python, ad esempio, puoi usare type(), dir(), e getattr() per analizzare gli attributi e i metodi di un oggetto.

# Controlliamo il tipo e gli attributi di `dog`
print(type(dog))          # Output: <class '__main__.Dog'>
print(isinstance(dog, Animal))  # Output: True
print(dir(dog))           # Mostra tutti gli attributi e metodi dell'oggetto dog
print(getattr(dog, 'name'))  # Accesso all'attributo 'name', Output: Buddy

# Controllo se `dog` ha un certo metodo
if hasattr(dog, 'speak'):
    print(dog.speak())    # Output: Buddy says Woof!


3-Tier Architecture: Si compone di tre livelli — presentazione (UI), logica di business, e database. Ciascuno è isolato e può risiedere su server separati.
Vantaggi: Migliora la scalabilità e la manutenzione, separando la logica di business dal database e dalla UI, che consente aggiornamenti indipendenti senza influenzare altri livelli.
 
Problemi nello sviluppo di una piattaforma distribuita
Problemi comuni includono latenza di rete, <<sincronizzazione dei dati>>, gestione degli errori di comunicazione, e sicurezza. La complessità nella gestione dei fallimenti parziali e della coerenza dei dati aumenta con i sistemi distribuiti.

Che test si performano nella fase di test del codice?
Unit Testing: Verifica singole funzioni o metodi.
Integration Testing: Verifica l'interazione tra vari componenti.
Requirements based testing: ogni singolo requirement (high lvl, low lvl, system lvl; preso da un documento che li racchiude) viene testato.
User Acceptance Testing (UAT): Valuta se il software è pronto per l’uso finale.

UML (Unified Modeling Language): Un linguaggio di modellazione usato per rappresentare graficamente i componenti di un sistema software.
Diagrammi principali: Diagrammi delle classi, delle attività, dei casi d'uso, di sequenza e di stato.

Il Class Diagram è uno dei diagrammi più utilizzati in UML per rappresentare la struttura statica di un sistema software. Questo diagramma descrive le classi presenti nel sistema, i loro attributi, i metodi e le relazioni tra di esse. Il suo scopo è dare una visione d’insieme della struttura del codice, evidenziando i componenti principali e le loro interazioni statiche.

Elementi chiave del Class Diagram
Classi: Rappresentate con rettangoli suddivisi in tre sezioni:
Nome della classe (in alto).
Attributi (al centro) – descrivono le proprietà della classe.
Metodi (in basso) – descrivono le funzioni o comportamenti della classe.

Relazioni:
Associazione: Collegamento tra due classi che collaborano.
Aggregazione: Relazione "parte-di" dove una classe è parte di un’altra (es. una macchina ha delle ruote).
Composizione: Relazione più forte dell’aggregazione, dove la parte non può esistere senza il tutto (es. una stanza esiste solo all’interno di una casa).
Ereditarietà: Indica che una classe deriva da un’altra.
Dipendenza: Indica che una classe usa temporaneamente un'altra classe.
Esempio di Class Diagram: Sistema di Gestione di una Biblioteca
Immaginiamo un sistema per la gestione di una biblioteca con tre classi principali: Book, Member, e Library.

Book

Attributi:
title: String
author: String
isbn: String
isAvailable: Boolean
Metodi:
checkOut(): void
returnBook(): void

2)Member

Attributi:
name: String
memberID: String
Metodi:
borrowBook(book: Book): Boolean
returnBook(book: Book): void

3)Library

Attributi:
name: String
books: List<Book>
Metodi:
addBook(book: Book): void
removeBook(book: Book): void
findBookByTitle(title: String): Book

Relazioni tra le Classi
Library ha una relazione di composizione con i suoi Book: la biblioteca possiede e gestisce i libri, quindi i libri fanno parte della biblioteca.
Member ha una dipendenza da Book: un membro può prendere in prestito un libro, ma non lo possiede.
Rappresentazione Grafica del Class Diagram
Nel diagramma:

Library è una classe che contiene una lista di Book.
Member può prendere in prestito un libro, quindi ha una relazione di dipendenza con Book.
Ecco una rappresentazione descrittiva del diagramma:

    +-------------+
    |   Library   |
    +-------------+
    | name: String|
    | books: List<Book> |
    +-------------+
    | + addBook(book: Book): void   |
    | + removeBook(book: Book): void |
    | + findBookByTitle(title: String): Book |
    +-------------+
           |
          (composizione)
           |
    +-------------+
    |    Book     |
    +-------------+
    | title: String |
    | author: String |
    | isbn: String   |
    | isAvailable: Boolean |
    +-------------+
    | + checkOut(): void |
    | + returnBook(): void |
    +-------------+
    
    +-------------+
    |   Member    |
    +-------------+
    | name: String |
    | memberID: String |
    +-------------+
    | + borrowBook(book: Book): Boolean |
    | + returnBook(book: Book): void |
    +-------------+
          |
        (dipendenza)
           |

Questo diagramma mostra chiaramente la struttura di un sistema biblioteca: come le classi sono correlate e i metodi principali che potrebbero implementare per gestire le operazioni di prestito e restituzione dei libri.



MVC (Model-View-Controller): Un pattern di progettazione per organizzare il codice in tre componenti: Modello (gestisce i dati), Vista (interfaccia utente), e Controller (gestisce la logica e l’interazione tra modello e vista).


Policy Manager: Gestisce regole e politiche di configurazione e controllo all’interno di un sistema.

Persistency Manager: Responsabile della gestione dei dati salvati o persistenti, spesso interagendo con un database.

Business Object: Un oggetto che rappresenta un’entità del dominio, incapsulando la logica di business.



Design pattern:

Object Factory: Fornisce un’interfaccia per creare oggetti senza specificare il tipo concreto di oggetto da creare.

Singleton: Garantisce che una classe abbia una sola istanza e fornisce un punto di accesso globale.

Builder: Separa la costruzione di un oggetto complesso dalla sua rappresentazione.

Prototype: Permette la creazione di nuovi oggetti copiando un’istanza esistente.

Facade: Fornisce un'interfaccia semplificata a un sistema complesso di classi.

Decorator: Aggiunge dinamicamente responsabilità a un oggetto.

Adapter: Permette l'interfacciamento tra due classi incompatibili.

Composite: Gestisce gerarchie di oggetti dove oggetti singoli e composti sono trattati uniformemente.

Proxy: Fornisce un oggetto surrogato per controllare l'accesso a un altro oggetto.

Strategy: Definisce una famiglia di algoritmi, incapsulandoli, e rendendoli intercambiabili.

Template: Definisce lo "skeleton" di un algoritmo in una classe, permettendo di sovrascrivere alcuni passaggi.

Iterator: Fornisce un modo di accedere agli elementi di un oggetto aggregato senza esporne la rappresentazione.

Command: Incapsula una richiesta come un oggetto, permettendo la parametrizzazione e il logging.

Observer: Consente a oggetti di osservare e reagire a eventi.



Scheduler Rate Monotonic: Scheduler pre-emptive basato su priorità che tiene conto della durata e del periodo di ogni processo e dà priorità maggiore ai processi con periodo più breve, che quindi vengono eseguiti più spesso di altri.


PXI interface: Si basa sullo standard PCI, ma è progettata specificamente per strumentazione, automazione industriale e test automatizzati.
Protocolli annessi: SCPI, VISA per automatizzare i test e leggere da oscilloscopi, generatori di segnale ecc..


UART: Interfaccia seriale asincrona dove sono previsti dati in TX e dati in RX, il baud rate deve essere condiviso tra i dispositivi che comunicano e per N dispositivi collegati in punto-punto servono N-1 UART. Altrimenti si può usare il multiplexing (più dispositivi sulla stessa UART ma mutuamente esclusivi) o la topologia a BUS (RS-485 per esempio)
Protocolli annessi: ModBus RTU, NMEA 0183


GIT COMMAND LINE 
Git commit: Sposta il mio puntatore locale sul commit appena creato con i miei file in locale
Git push: allinea anche il puntatore remoto (si può usare con --f per forzare un push).
Git status: mostra lo stato dei cambiamenti dei miei file locali rispetto a quelli puntati dal puntatore remoto.
Git switch: permette al puntatore HEAD di spostarsi tra i vari branch.
Git branch: mostra dove si trova il puntatore e può essere usato per creare nuovi branch fornendo un nome o eliminarli (--D)
Git reset: utilizzato per far tornare indietro nel tempo un puntatore remoto (sposta sia HEAD che origin/); da usare con --hard
Git stash: salva i cambiamenti in maniera temporanea in una sorta di stack, git stash pop riprende i cambiamenti dallo stack.
Git fetch: scarica da remoto eventuali aggiornamenti rispetto alla nostra repository locale.
Git pull: allinea il mio puntatore locale col nuovo aggiornamento remoto (scarica le nuove versioni dei file). Utilizzabile solo dopo fetch.
Git add: aggiunge i singoli file al nostro commit, da utilizzare prima di commit e push.


Un programma python può avere diversi thread?
Sì, Python supporta più thread, ma a causa del Global Interpreter Lock (GIL), il multi-threading non sfrutta completamente il parallelismo della CPU per operazioni CPU-bound. Per queste, meglio il multi-processing.


Nell'implementare una libreria di python 3, come si gestisce un unexpected behavior?
In caso di comportamento inatteso, è comune usare raise per lanciare un'eccezione specifica o gestire l’errore. 
sys.exit() è usato per terminare un programma quando il recupero non è possibile.

def divide_numbers(numerator, denominator):
    if denominator == 0:
        raise ValueError("Il denominatore non può essere zero.")
    return numerator / denominator

try:
    result = divide_numbers(10, 0) # Codice che potrebbe generare un'eccezione
except ValueError as e:     #NOTA CHE FUORI DA PYTHON QUESTO SI CHIAMA CATCH ECCO PERCHE' <<TRY CATCH>>
    print(f"Errore: {e}") # Codice per gestire l'errore di divisione per zero



Teoria delle code per lo scheduling:
La teoria delle code è un ramo della matematica applicata che studia i processi di attesa e viene utilizzata per analizzare e ottimizzare i sistemi in cui ci sono risorse limitate e molte richieste per tali risorse. Questa teoria è particolarmente utile nello scheduling dei processi, nella gestione delle code di clienti o in scenari di rete e telecomunicazioni.

Principi Fondamentali della Teoria delle Code
Un sistema di code è costituito da vari elementi chiave:

Clienti: Le entità che necessitano di un servizio. Possono essere processi in esecuzione, utenti, pacchetti di dati, etc.
Risorse o server: Le entità che forniscono il servizio richiesto dai clienti.
Coda di attesa: I clienti che non possono essere serviti immediatamente attendono in una coda.
Politica di scheduling: La regola che determina l’ordine con cui i clienti vengono serviti.

Modelli di Coda
La teoria delle code include vari modelli che rappresentano scenari di attesa e distribuzione di risorse, tra cui i più comuni sono:

Modello M/M/1:
M/M indica che gli <<intervalli di arrivo e di servizio>> seguono una distribuzione esponenziale (ovvero un processo di Poisson).
1 rappresenta il numero di server (risorse) disponibili.
Questo modello è usato per descrivere sistemi con una singola risorsa e arrivi casuali, come una singola stampante utilizzata da molti utenti.

Modello M/M/c:
Come l’M/M/1, ma con c server (es. più banconi di una banca).
Riduce il tempo di attesa poiché i clienti possono essere serviti da più risorse.

Modello M/G/1:
La prima M indica un <<processo di Poisson per gli arrivi>>, ma <<la distribuzione del tempo di servizio è Generica (G)>>.
È usato quando il tempo di servizio ha una distribuzione arbitraria, non necessariamente esponenziale.

Principali Parametri delle Code
La teoria delle code utilizza vari parametri per analizzare le prestazioni di un sistema, tra cui:
Tempo medio di attesa in coda: Tempo medio che un cliente trascorre in attesa prima di essere servito.
Tempo medio di servizio: Durata media del servizio per un cliente.
Tempo medio nel sistema: Tempo totale medio che un cliente trascorre nel sistema (attesa + servizio).
Probabilità di blocco o rifiuto: Probabilità che un cliente venga rifiutato per mancanza di risorse.

Politiche di Scheduling
La politica di scheduling definisce l’ordine di servizio dei clienti. Tra le principali ci sono:

FIFO (First-In, First-Out): I clienti vengono serviti nell’ordine di arrivo. È il metodo più semplice e intuitivo.
LIFO (Last-In, First-Out): L’ultimo cliente ad arrivare viene servito per primo.
Round-Robin (RR): Ogni cliente riceve un piccolo intervallo di tempo e poi viene rimesso in coda. Questo metodo è spesso usato nei sistemi operativi.
Priority Scheduling: I clienti con priorità più alta vengono serviti per primi. Questo può causare il problema della starvation per i clienti con priorità bassa.

Applicazioni della Teoria delle Code
Sistemi di elaborazione dati: Ottimizzazione della gestione dei processi nei sistemi operativi, utilizzando algoritmi di scheduling per minimizzare il tempo di attesa dei processi.
Reti di telecomunicazione: Gestione del traffico di rete, come la distribuzione dei pacchetti su una rete con router che possono avere code di pacchetti in attesa.
Call center: Ottimizzazione della distribuzione delle chiamate in entrata, per minimizzare i tempi di attesa dei clienti e massimizzare l’utilizzo degli operatori.
Sistemi di produzione e logistica: Gestione delle code di attesa nelle linee di produzione e dei flussi nei magazzini.

Formule Principali della Teoria delle Code (Modello M/M/1)
Per un sistema M/M/1 con arrivo di clienti secondo una distribuzione di Poisson, il numero medio di clienti nel sistema 
L, e il tempo medio trascorso nel sistema W sono:

Numero medio di clienti nel sistema (L):
𝐿=𝜆/(𝜇−𝜆)			dove λ è la media del tasso di arrivo e μ il tasso medio di servizio.

Tempo medio nel sistema (W):
𝑊=1/(𝜇−𝜆)

Tempo medio di attesa in coda (Wq):
𝑊𝑞=𝜆/(𝜇*(𝜇 − 𝜆)
 
Queste formule sono utili per calcolare le performance di sistemi di code in ambiti diversi e possono essere estese a modelli più complessi.

Vantaggi e Svantaggi della Teoria delle Code

Vantaggi: Consente di ottimizzare l’uso delle risorse, ridurre i tempi di attesa e migliorare la soddisfazione degli utenti.
Svantaggi: Le ipotesi dei modelli standard (come l’arrivo casuale secondo un processo di Poisson) possono non rispecchiare sempre la realtà. Sistemi complessi spesso richiedono modelli avanzati o simulazioni.
La teoria delle code è un pilastro in molte aree dell'ingegneria del software e della gestione dei sistemi, aiutando a bilanciare efficienza e risorse.



Semafori busy waiting e non:
I semafori sono strumenti fondamentali nella gestione della concorrenza nei sistemi operativi e nei sistemi distribuiti. Vengono usati per coordinare l'accesso a risorse condivise da parte di più processi o thread, prevenendo situazioni di race condition e garantendo la sincronizzazione.

Esistono due principali tipi di semafori in base alla modalità di attesa del processo o thread in attesa della risorsa:

1. Semafori con Busy Waiting
Il busy waiting, o attesa attiva, è una tecnica in cui un processo o thread attende continuamente (in un ciclo, in polling) finché la risorsa desiderata non diventa disponibile. Durante l'attesa, il thread consuma cicli della CPU per controllare ripetutamente se la risorsa è libera, risultando quindi in un notevole consumo di risorse.

Come Funziona
In busy waiting, un thread controlla ripetutamente il valore del semaforo, senza rilasciare il controllo della CPU. Ad esempio, se un thread desidera accedere a una risorsa ma trova il semaforo con valore 0 (occupato), entra in un ciclo continuo, eseguendo verifiche fino a quando il semaforo non torna a 1 (disponibile).

Vantaggi
Bassa latenza: Appena la risorsa diventa disponibile, il thread in attesa la acquisisce senza ulteriori ritardi.
Semplicità di implementazione: Non richiede operazioni complesse di gestione della coda di attesa del processo.

Svantaggi
Elevato consumo di CPU: Durante l'attesa, il thread continua a consumare risorse della CPU.
Non adatto a sistemi multiutente o multitasking: Se molti thread o processi adottano il busy waiting, la CPU è occupata senza progresso effettivo, causando inefficienza.

Esempio di Busy Waiting
Il busy waiting è spesso utilizzato nei lock spinlock. In un spinlock, un thread “spins” (gira) in attesa che il lock sia rilasciato. 
In Python, si può simulare con un ciclo while:

while not lock_acquired:
    pass  # busy waiting: il thread continua a “girare” finché il lock non è libero

2. Semafori senza Busy Waiting (Non-Busy Waiting)
Nei semafori non-busy waiting, o attesa passiva, il thread in attesa viene sospeso o messo in stato di attesa, rilasciando così il controllo della CPU fino a quando la risorsa non diventa disponibile. Quando un thread cerca di acquisire un semaforo occupato, viene messo in una coda di attesa e la CPU è libera per altre attività.

Come Funziona
Quando un thread trova un semaforo occupato, invece di continuare a eseguire cicli di attesa, invoca un'istruzione che lo sospende. Il sistema operativo lo inserisce in una coda associata al semaforo, e il thread rimane inattivo fino a quando non viene svegliato (notificato) che la risorsa è ora disponibile.

Vantaggi
Migliore efficienza della CPU: La CPU non viene sprecata in attesa; può invece essere assegnata ad altri processi o thread.
Scalabilità: Funziona meglio in sistemi con molteplici processi o thread, consentendo una gestione efficiente della concorrenza.

Svantaggi
Maggiore latenza rispetto al busy waiting: Il tempo necessario per rimuovere un thread dalla coda di attesa e assegnargli la CPU può introdurre un ritardo.
Più complesso da implementare: Richiede il supporto da parte del sistema operativo per sospendere e risvegliare i processi.

Esempio di Non-Busy Waiting
In sistemi come UNIX o Windows, le chiamate di sistema per i semafori (ad esempio, sem_wait e sem_post in POSIX) consentono al sistema operativo di sospendere il thread in attesa senza occupare la CPU. In Python, con il modulo threading, è possibile usare i semafori in modalità non busy waiting:

from threading import Semaphore

# Creazione di un semaforo con un valore iniziale di 1 (un'unità disponibile)
sem = Semaphore(1)

def access_resource():
    # Acquisisce il semaforo senza busy waiting
    sem.acquire()
    try:
        # codice per accedere alla risorsa protetta
        print("Risorsa acquisita")
    finally:
        # Rilascia il semaforo dopo l'uso
        sem.release()

Confronto tra Busy Waiting e Non-Busy Waiting

Aspetto		Busy Waiting				Non-Busy Waiting
Consumo CPU	Alto					Basso
Efficienza	Scarsa nei sistemi multi-utente		Elevata
Latenza		Bassa					Potenzialmente alta
Complessità	Semplice				Complessa (richiede supporto OS)
Esempio		Spinlock				Semafori di sistema (come sem_wait)

Uso Appropriato
Busy Waiting: Preferibile solo per brevi attese in ambienti dove il cambio di contesto è più costoso dell’attesa attiva stessa, come nei sistemi real-time o in sistemi con risorse limitate (es. firmware).
Non-Busy Waiting: Ideale per sistemi con molti thread o processi, dove la CPU deve essere utilizzata in modo efficiente.




Can bus: Il CAN Bus (Controller Area Network) è un protocollo di comunicazione seriale progettato specificamente per sistemi embedded che richiedono comunicazioni affidabili e robuste tra diversi componenti. È ampiamente utilizzato nell'industria automobilistica (in cui è stato originariamente sviluppato), ma trova applicazione anche in altri settori, come quello aerospaziale, marittimo, ferroviario, industriale, e in applicazioni robotiche.

Storia e Motivazioni del CAN Bus
Il protocollo CAN è stato sviluppato da Bosch negli anni '80 per risolvere il problema della comunicazione tra numerose unità elettroniche (ECU) in veicoli. Prima dell'introduzione del CAN, ogni modulo aveva una connessione dedicata verso gli altri, con conseguenti problemi di ingombro e complessità. CAN ha semplificato la comunicazione, riducendo il numero di cavi e migliorando l'affidabilità.

Caratteristiche Principali del CAN Bus
Affidabilità e Robustezza: Il CAN è progettato per essere robusto e immune al rumore elettrico, garantendo un'elevata affidabilità anche in ambienti difficili, come l'interno di un motore.

Multicast: Tutti i nodi collegati possono ricevere ogni messaggio, permettendo una comunicazione più efficiente e una distribuzione immediata delle informazioni a più moduli.

Indirizzamento Basato sul Contenuto: A differenza di altri protocolli di comunicazione, CAN non indirizza i messaggi a specifici nodi. Invece, ogni messaggio contiene un identificatore che specifica il tipo di dato trasmesso.

Arbitraggio Non Distruttivo: Il CAN utilizza una tecnica di arbitraggio basata sul contenuto che permette a più nodi di trasmettere simultaneamente. Solo il messaggio con la priorità più alta verrà trasmesso, senza perdita di dati per i messaggi non prioritari.

Velocità e Distanza: Il CAN può operare a velocità diverse, con una massima di 1 Mbps su brevi distanze (40 metri). Con distanze maggiori, la velocità massima si riduce.

Struttura dei Messaggi CAN
I messaggi CAN hanno una struttura fissa composta da diversi campi:

Identifier (ID): Indica la priorità del messaggio e il tipo di dato. I messaggi con ID più basso hanno priorità più alta.
Control Field: Contiene informazioni come la lunghezza del dato.
Data Field: Campo dati variabile da 0 a 8 byte.
CRC (Cyclic Redundancy Check): Campo di controllo per la rilevazione di errori.
ACK (Acknowledgment): Campo in cui i nodi ricevitori possono confermare la ricezione senza errori.

Tipi di CAN
Esistono due standard principali per i messaggi CAN:

Standard CAN (CAN 2.0A): Definisce un ID a 11 bit, consentendo fino a 2048 identificatori.

Extended CAN (CAN 2.0B): Consente ID a 29 bit, aumentando il numero di possibili messaggi unici.

Protocollo di Arbitraggio del CAN
Il CAN usa un meccanismo di arbitraggio non distruttivo per risolvere i conflitti quando più nodi tentano di trasmettere contemporaneamente. Ogni nodo verifica il bus durante la trasmissione: se rileva un livello diverso da quello inviato, interrompe la trasmissione. Questo metodo permette al messaggio con la priorità più alta di essere trasmesso per primo, senza causare collisioni o perdita di dati.

Vantaggi del CAN Bus
Riduzione della Complessità del Cablaggio: Permette ai componenti di condividere una linea di comunicazione comune.

Affidabilità Elevata: CAN è progettato per essere molto tollerante agli errori e agli ambienti rumorosi, garantendo l'affidabilità della comunicazione.

Efficienza del Protocollo: Il meccanismo di arbitraggio prioritario e la struttura dei messaggi riducono il rischio di collisioni.

Supporto di Diagnostica e Manutenzione: Il protocollo CAN consente di diagnosticare e rilevare errori in tempo reale, aiutando nella manutenzione.

Limitazioni del CAN Bus
Lunghezza Massima Limitata: La distanza massima è limitata dalla velocità di trasmissione, quindi non è adatto per reti molto estese.

Dimensione del Dato Limitata: Il campo dati è limitato a 8 byte per messaggio, quindi non è adatto a trasferire grandi quantità di dati.

Velocità Limitata: Rispetto ad altri protocolli di comunicazione più recenti, la velocità massima è limitata a 1 Mbps, che può essere insufficiente in alcune applicazioni moderne.

CAN FD (Flexible Data-rate)
Per superare alcune delle limitazioni del CAN standard, è stato introdotto il CAN FD (Flexible Data-rate), che consente una velocità di trasmissione più alta e messaggi con una lunghezza di dati fino a 64 byte. Questo è utile per applicazioni che richiedono una maggiore larghezza di banda e trasferimenti di dati più veloci, come l'integrazione di più sistemi elettronici nei veicoli moderni.

Applicazioni del CAN Bus
Automotive: È utilizzato in auto per collegare ECU (Engine Control Units), airbag, sistemi ABS, sistemi di infotainment, e altri componenti elettronici.

Industriale: Utilizzato in macchinari e sistemi di controllo industriali per comunicare tra sensori e attuatori.

Aerospace: Adoperato nei sistemi di controllo di volo, dove la robustezza e l'affidabilità della comunicazione sono cruciali.

Robotica: Utilizzato nei robot per coordinare i movimenti tra diversi motori e sensori.

Diagnostica tramite CAN Bus
Il CAN Bus supporta funzioni di diagnostica, rendendolo ideale per il monitoraggio e la manutenzione. Ad esempio, nei veicoli, la porta diagnostica OBD-II utilizza il CAN Bus per comunicare informazioni sugli errori e lo stato del motore. I tecnici possono connettersi a questa porta per leggere i codici di errore e valutare il funzionamento del veicolo, facilitando la diagnostica e la riparazione.

Esempio di Implementazione in Python
In Python, le interfacce per il CAN sono spesso gestite da librerie esterne come python-can, che permette di inviare e ricevere messaggi su un CAN Bus.

import can

# Creazione di un bus virtuale CAN
bus = can.interface.Bus(bustype='socketcan', channel='vcan0', bitrate=500000)

# Invio di un messaggio CAN
msg = can.Message(arbitration_id=0x123, data=[0x01, 0x02, 0x03, 0x04], is_extended_id=False)
bus.send(msg)

# Ricezione di un messaggio CAN
received_msg = bus.recv()
print(f"Received message: {received_msg}")

In sintesi, il CAN Bus è un protocollo estremamente affidabile e versatile per applicazioni embedded, garantendo una comunicazione robusta, efficiente e ben gestibile anche in ambienti difficili.



Heap vs Stack:

Stack: Utilizzato per gestire variabili locali e chiamate di funzione. Overflow quando esaurisce la memoria disponibile.

Heap: Usato per la memoria dinamica. Overflow quando l’applicazione richiede più memoria di quella disponibile.


Smart pointers: sono un tipo avanzato di puntatore usato principalmente in linguaggi come C++ per gestire la memoria in modo sicuro ed efficiente. A differenza dei puntatori normali (raw pointers), che devono essere esplicitamente gestiti e liberati, gli smart pointers gestiscono automaticamente l’allocazione e la deallocazione della memoria, riducendo così i rischi di memory leak, dangling pointers e altri problemi di gestione della memoria.

Perché Usare gli Smart Pointers?
Tradizionalmente, i puntatori vengono utilizzati per l'allocazione dinamica della memoria (tramite new in C++). Tuttavia, ciò comporta il rischio di non liberare correttamente la memoria, il che può causare problemi come:

Memory leaks: Occorre liberare esplicitamente la memoria con delete, e se ciò non viene fatto, la memoria allocata rimane inutilizzata.

Dangling pointers: Si verifica quando un puntatore punta a una memoria che è stata già liberata, portando a comportamenti indefiniti.

Duplicazione della gestione della memoria: Quando più puntatori puntano allo stesso oggetto e non è chiaro chi sia responsabile della sua deallocazione.

Gli smart pointers risolvono questi problemi rendendo automatica la gestione della memoria e riducendo la possibilità di errori.

Tipi di Smart Pointers in C++
C++ include diversi tipi di smart pointers standard, ognuno con caratteristiche specifiche per diversi casi d’uso. 

Ecco i principali:

std::unique_ptr

Il std::unique_ptr è un puntatore esclusivo che gestisce un'istanza singola di un oggetto. Solo un unique_ptr può puntare a un determinato oggetto alla volta.
Ownership esclusiva: Non permette la copia, ma solo il trasferimento della proprietà tramite l'operatore std::move().
Usi: È ideale per risorse che non devono essere condivise e per la gestione della memoria in contesti in cui si sa esattamente chi possiede l'oggetto.
Esempio:

#include <memory>

std::unique_ptr<int> ptr = std::make_unique<int>(5); // Alloca un intero
std::unique_ptr<int> ptr2 = std::move(ptr);          // Trasferisce la proprietà
// ptr non è più valido dopo std::move(ptr)


std::shared_ptr

Il std::shared_ptr è un puntatore a risorse condivise. Consente a più shared_ptr di puntare allo stesso oggetto.
Reference counting: Tiene traccia del numero di puntatori che fanno riferimento all'oggetto. Quando il conteggio arriva a zero, l’oggetto viene deallocato.
Usi: È utile in situazioni in cui più parti del codice devono accedere alla stessa risorsa, come nella programmazione concorrente.
Esempio:

#include <memory>

std::shared_ptr<int> ptr1 = std::make_shared<int>(10); // Crea un oggetto condiviso
std::shared_ptr<int> ptr2 = ptr1;                      // Aumenta il conteggio dei riferimenti
// L'oggetto viene deallocato solo quando il conteggio dei riferimenti scende a zero


std::weak_ptr

Il std::weak_ptr è un puntatore debole, progettato per risolvere i problemi di ciclo di riferimento che possono verificarsi con shared_ptr.
Non incrementa il conteggio dei riferimenti: È una "copia debole" di un shared_ptr, quindi non impedisce la deallocazione della memoria.
Usi: È comunemente usato per creare collegamenti tra oggetti in strutture complesse come grafi o alberi, dove un ciclo di riferimento può causare un memory leak.
Esempio:

#include <memory>

std::shared_ptr<int> sharedPtr = std::make_shared<int>(20);
std::weak_ptr<int> weakPtr = sharedPtr; // weakPtr punta all'oggetto, ma non ne incrementa il reference count
if (auto tempPtr = weakPtr.lock()) {
    // Usa tempPtr solo se l'oggetto esiste ancora
}

Altri Tipi di Smart Pointers (Custom Smart Pointers)
In C++, è possibile creare smart pointers personalizzati per gestire casi d'uso specifici, come ad esempio puntatori che gestiscono risorse non allocate dinamicamente.

Come Funzionano gli Smart Pointers
Gli smart pointers sono implementati come classi template che sovraccaricano gli operatori di accesso (* e ->) per comportarsi come puntatori tradizionali. In particolare, il loro distruttore gestisce la deallocazione automatica dell’oggetto a cui puntano. Per esempio, shared_ptr usa un reference counter (conteggio dei riferimenti) per tenere traccia di quanti puntatori condivisi fanno riferimento allo stesso oggetto.

Vantaggi degli Smart Pointers
Gestione automatica della memoria: Libera automaticamente la memoria quando non è più necessaria.

Riduzione dei memory leak: Gli oggetti vengono deallocati solo quando non ci sono più riferimenti a essi.

Codice più sicuro e leggibile: Elimina la necessità di chiamare delete esplicitamente, riducendo il rischio di errori di programmazione.


Svantaggi degli Smart Pointers
Overhead aggiuntivo: Per esempio, shared_ptr introduce un overhead per il conteggio dei riferimenti.

Potenziale di cattiva gestione: Un uso improprio può ancora causare memory leaks (ad esempio, cicli di riferimento con shared_ptr).

Compatibilità con C: Poiché gli smart pointers sono una feature di C++, non sono compatibili con le funzioni di C che richiedono raw pointers.


Smart Pointers vs Raw Pointers

Smart Pointers: gestiscono la memoria automaticamente e riducono il rischio di errori di gestione della memoria.

Raw Pointers: richiedono una gestione manuale, ma hanno meno overhead e possono essere più veloci in contesti specifici.

Gli smart pointers sono un'importante caratteristica del C++ moderno, che consente una gestione sicura e automatica della memoria, rendendo il codice più sicuro e riducendo significativamente il rischio di errori dovuti alla gestione manuale dei puntatori.


Memory Mapped Areas:
Scrivere su un'area di memoria di un dispositivo memory-mapped può fallire per una serie di motivi legati sia all'architettura del sistema che a eventuali restrizioni hardware o software. La memory-mapped I/O è una tecnica in cui alcune aree di memoria sono direttamente mappate ai registri dei dispositivi hardware, come porte di comunicazione, dispositivi di archiviazione o schede di rete. Invece di inviare comandi tramite un’interfaccia di comunicazione separata, il processore può leggere e scrivere direttamente in queste aree di memoria per interagire con l’hardware.

Cos'è la Memory-Mapped I/O
Nella memory-mapped I/O, parte dello spazio di indirizzamento della memoria viene riservato ai dispositivi hardware. Ad esempio, leggendo o scrivendo in un certo intervallo di indirizzi, si può controllare un dispositivo periferico senza bisogno di chiamate di sistema. Questa mappatura può avvenire tramite registri speciali nei dispositivi che mappano fisicamente le operazioni di lettura/scrittura a indirizzi di memoria specifici, gestiti dal sistema operativo e dal driver del dispositivo.

Possibili Cause di Fallimento nella Scrittura

Permessi Insufficienti:
Non tutti i processi hanno i permessi per accedere a ogni area di memoria. Le aree memory-mapped spesso richiedono permessi speciali di lettura/scrittura, e se il processo non dispone dei permessi adeguati, la scrittura fallirà.
Questo problema può essere gestito tramite l’uso di privilegi elevati o l'accesso come amministratore/root, oppure assicurandosi che il codice esegua con il contesto di permessi corretto.

Hardware Occupato o Non Disponibile:
Un dispositivo potrebbe essere occupato con un'altra operazione e potrebbe non rispondere immediatamente a una nuova scrittura. Ad esempio, se un dispositivo di I/O sta già elaborando un comando, potrebbe non accettare altri comandi fino al termine dell’operazione corrente.
Alcuni dispositivi espongono registri di stato che è necessario verificare prima di eseguire un'operazione, assicurandosi che il dispositivo sia pronto per la nuova operazione di scrittura.

Problemi di Sincronizzazione:
Le operazioni di scrittura su memory-mapped I/O possono essere soggette a problemi di sincronizzazione, soprattutto in sistemi multicore. Se più processi o thread cercano di scrivere contemporaneamente nella stessa area, si può verificare un race condition, causando risultati imprevedibili o errori di scrittura.
Per evitare questi problemi, è comune usare meccanismi di sincronizzazione come semafori o mutex.

Problematiche legate specificatamente all'hardware:
Alcuni tipi di hardware non permettono scritture successive rapide, ad esempio le EEPROM necessitano di almeno 15ms tra una scrittura e un'altra

Cache della CPU sulla quale forzare flush:
Alcuni processori cachano le operazioni di lettura/scrittura sulla memoria per migliorare le prestazioni, ma ciò può introdurre problemi quando si lavora con dispositivi memory-mapped. Se la cache non viene aggiornata o non si utilizza una memoria non-cacheable, il valore scritto può non riflettersi immediatamente (o per niente) sul dispositivo.
Per evitare questo problema, molte architetture hardware forniscono una memoria non-cacheable per le operazioni memory-mapped, o offrono comandi di "flush" della cache che permettono di forzare la scrittura immediata sui dispositivi.

Page Fault o Accesso a Memoria non Valida:
Se il puntatore usato per l'accesso a un'area memory-mapped è incorretto o se l’area è stata deallocata o resa non valida dal sistema operativo, l’operazione di scrittura può fallire. Ad esempio, una pagina di memoria può essere deallocata per mancanza di utilizzo o per cambio di stato del dispositivo.
Gli errori di tipo page fault possono generare eccezioni o segmentation fault, che indicano che l’accesso alla memoria non è più valido.

Errori Hardware o Malfunzionamenti del Dispositivo:
Il dispositivo stesso può avere problemi interni, come errori hardware o malfunzionamenti, che impediscono l’esecuzione della scrittura. Ad esempio, un dispositivo guasto potrebbe non rispondere o restituire errori di stato.

In questi casi, è consigliabile gestire l'errore e, se possibile, reinizializzare o ripristinare il dispositivo.

Esempio di Codice per Scrivere su Memory-Mapped I/O in C

#include <fcntl.h>
#include <sys/mman.h>
#include <unistd.h>
#include <stdint.h>
#include <stdio.h>

#define MMIO_BASE_ADDR 0x3F200000 // Esempio di indirizzo di base
#define MMIO_SIZE 4096            // Dimensione dell'area di memoria

int main() {
    int fd = open("/dev/mem", O_RDWR | O_SYNC); // Apertura del dispositivo memory-mapped
    if (fd < 0) {
        perror("Errore nell'aprire /dev/mem");
        return -1;
    }

    // Mappatura dell'area di memoria
    volatile uint32_t *mmio = mmap(NULL, MMIO_SIZE, PROT_READ | PROT_WRITE, MAP_SHARED, fd, MMIO_BASE_ADDR);
    if (mmio == MAP_FAILED) {
        perror("Errore nel mappare la memoria");
        close(fd);
        return -1;
    }

    // Scrittura su un registro del dispositivo
    mmio[0] = 0x1; // Scrittura di un valore nel primo registro

    // Chiusura e pulizia
    munmap((void *)mmio, MMIO_SIZE);
    close(fd);
    return 0;
}

In questo esempio:

L’indirizzo MMIO_BASE_ADDR rappresenta un indirizzo di base della memoria associata al dispositivo.
La chiamata a mmap() mappa quell’area di memoria in modo che il programma possa accedervi come se fosse memoria normale. Se mmap() fallisce, significa che l'accesso alla memoria non è possibile, magari a causa di mancanza di permessi o indisponibilità del dispositivo.

Conclusioni
Le scritture sulla memoria di un dispositivo memory-mapped possono fallire per problemi di permessi, protezione del sistema operativo, gestione delle cache, sincronizzazione, o problemi hardware. La corretta gestione della memoria memory-mapped richiede attenzione ai permessi, alla sincronizzazione e al controllo degli errori, che sono fondamentali per garantire che le operazioni avvengano correttamente.



Docker: Software non è solo il codice, ma anche l'ambiente dove il codice gira, le librerie che usa ecc.. Docker mette tutto questo all'interno di un <<container>> e lo rende condivisibile per intero in qualsiasi ambiente. 
Il docker engine è il programma che si usa per far girare i containers su qualsiasi sistema. 
La docker Image è come una foto del programma (e del suo ambiente) nel tempo.
Il docker file è invece un file di testo con delle istruzioni su come buildare la docker image

Invece Conda/pipenv è un gestore di dipendenze a livello di 'python'. Gli dici quale libreria python vuoi.

Cosa si intende per CI/CD: continous integration continous development, fattibile tramite strumenti come github

Cosa succede quando scrivo il nome di un sito nella barra di ricerca e premo invio?
Dns traduce nome del sito in indirizzo IP -> Viene richiesta una Tcp Connection a quell’ip -> Vengono richieste le pagine tramite Https request -> Html and js is sent -> the page renders

Leetcode:
https://algomap.io/

Hai elemento salvato su linkedin con un'infinità di corsi online gratuiti
+ Deep learning specialization 
Deeplearning.ai coursera (7 gg gratuiti)

Fatti suggerire da GPT qualcosa da fare con YOLOV8

TEST: Implementa classe con un push e pop su un array stack

#miscellaneous 