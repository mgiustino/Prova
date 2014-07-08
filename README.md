Sviluppatori:

Giacomo Forresu
Riccardo Atzori
Alberto Collu
Luca Macis
Matteo Giustino

Presentiamo un piccolo elenco delle modiiche più rilevanti e dell'implementazione del giocatore automatico.

Game2048.java
====================
Nella classe Game2048 abbiamo introdotto una menuBar con un menù contenente un toogleButton il cui stato avrebbe tenuto traccia dell'attivazione o meno del giocatore automatico. Inoltre è stato necessario modificare il metodo addKeyHandler per fare in modo che all'attivazione del giocatore automatico non fosse più possibile, per l'utente, premere i tasti direzionali della tastiera per poter iniziare/continuare a giocare. Tuttavia è sempre possibile disattivare il bottone per ripresente l'esecuzione manuale del gioco.
Sono presenti, inoltre, due finestre modali contenenti le regole del gioco e le informazioni riguardanti gli sviluppatori del giocatore automatico.

GameManager.java
====================
All'interno della classe GameManager.java abbiamo introdotto uno dei metodi più importanti per il corretto funzionamento del giocatore automatico, ovvero, il metodo "getGameGrid". L'introduzione di questo metodo è dovuta al fatto che la griglia di gioco originale non è altro che una mappa le cui chiavi e i cui valori sono rispettivamente di "Location" e "Tile", mentre, nel nostro caso, la griglia di gioco doveva esser composta da "Location" ed "Integer". Ecco spiegato per cui è stato necessario ricostruirci la griglia di gioco secondo le modalità appena descritte. 


Player.java
====================
La classe in questione è stata introdotta per gestire il thread del giocatore automatico. Infatti, come si può notare dall'handler del toogleButton presente nella classe Game2048.java, viene creato un nuovo thread, il cui costruttore accetta come parametro un oggetto di tipo Runnable. Tale oggetto non è altro che la classe Player. Tuttavia per fare in modo che quest'ultima potesse svolgere l'obbiettivo da noi desiderato è stato necessario implementare l'interfaccia Runnable (condizione necessaria per il corretto funzionamento del thread). Inoltre in questa classe è presente una piccola particolarità: non essendo possibile modificare degli elementi di JavaFx mediante un comune thread, si è reso necessario invocare il metodo statico “runLater”, della classe Platform”, per far in modo che ogni istanza del thread, venga messa in una coda ed eseguita appena possibile. 
Tutto questo procedimento, appena descritto, ci ha consentito di creare un thread secondario che agisce solamente su una parte della finestra principale, permettendoci di modificare lo stato della griglia di gioco in base ai criteri implementati nella classe MyGiocatoreAutomatico.java


MyGiocatoreAutomatico.java
====================
Quest'ultima non è altro che il fulcro del nostro sistema di calcolo e gestione della prossima mossa e concettualmente la si può pensare come divisa in due parti. Infatti, in un ipotetico diagramma di flusso, la prima parte è composta da tutti quei metodi che effettuano una simulazione del gioco in un determinato stato della griglia per una determinata direzione, mentre la seconda parte è composta da tutti quei metodi che, basandosi su tale simulazione, riescono a determinare quale possa essere la miglior mossa da effettuare. Nello specifico, i metodi più coinvolti nella simulazione di gioco sono i seguenti: “findFarthestLocation”, “tileForDirection” “moveToLastPosition” e “merge”. Tuttavia occorre far notare che i metodo appena citati non agiscono sulla griglia di gioco originale, bensì su una copia della griglia e vengono invocati dal metodo "shift". Tale metodo è colui che svolge il ruolo più importante nella simulazione di una mossa di gioco.
Successivamente, dopo aver effettuato tale simulazione, il controllo del giocatore automatico viene affidato ai metodi "computerScore", "playerScore" ed "heuristicEvaluation". Concettualmente l'operato dei metodi "computerScore" e "playerScore" agisce su un albero di gioco (albero delle mosse disponibili) ricercando ricorsivamente, per ogni livello di profondità, la mossa migliore. Di conseguenza, più la ricerca avviene in profondità più è facile predire un certo numero di mosse future per una determinata direzione. Tuttavia, in un contesto come il nostro, si doveva tener presente il fattore casualità, in quanto non si poteva sapere a priori se sarebbe uscito un 2 oppure un 4 e sempre di conseguenza l'albero di gioco presentava dei nodi di casualità, ovvero, dei nodi in cui il valore non poteva essere determinato con certezza. Quindi la soluzione che abbiamo deciso di implementare si basa sul fatto di attribuire una ben nota probabilità ai due valori che potrebbero uscire, in modo da inserire tali valori all'interno della griglia di gioco e simulare lo spostamento del nostro giocatore automatico, attraverso il metodo "playerScore", e valutare tale griglia in base al risultato di una valutazione euristica e di tale probabilità.
La valutazione euristica, appena citata, ci consente di stimare la bontà di una griglia di gioco, prendendo in considerazione la disposizione delle celle. Per far ciò abbiamo utilizzato un gradiente, facendo in modo che ad ogni locazione della griglia di gioco venga assegnato un determinato peso che ci consenta una disposizione ottimale delle celle al fine di raggiungere 2048. Infatti, attraverso l'uso del medesimo, la combinazione delle celle con valore più grande (anche attraverso reazioni a catena di combinazioni) era facilitata, in quanto si riduceva la probabilità che delle celle con valori piccoli si trovino in mezzo a celle con valori più grandi.
In ultima analisi, si può notare che nel metodo "prossimaMossa", a secondo del numero di celle vuote, la ricerca viene effettuata per due differenti livelli. Infatti, com'è possibile notare, la ricerca inizia con un albero di profondità pari a 2, ma più si va avanti nel gioco, quindi meno celle vuote sono presenti, più la ricerva avviene in profondità, per un livello di profondità massimo pari a 3.


