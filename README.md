Sviluppatori:

Giacomo Forresu
Riccardo Atzori
Alberto Collu
Luca Macis
Matteo Giustino

Presentiamo un piccolo elenco delle modiiche più rilevanti e dell'implementazione del giocatore automatico.

Game2048.java
====================
Nella classe Game2048 abbiamo introdotto una menuBar con un menù contenente un toogleButton, il cui stato tiene traccia dell'attivazione o meno del giocatore automatico. E’ stato necessario modificare il metodo “addKeyHandler" per fare in modo che all'attivazione del giocatore automatico, l’utente non possa più premere i tasti direzionali della tastiera per iniziare/continuare a giocare. Tuttavia è sempre possibile disattivare il bottone per riprendere l'esecuzione manuale del gioco. Sono presenti, inoltre, due finestre modali contenenti le regole del gioco e le informazioni riguardanti gli sviluppatori del giocatore automatico.

GameManager.java
====================
All'interno della classe GameManager.java abbiamo introdotto uno dei metodi più importanti per il corretto funzionamento del giocatore automatico, ovvero, il metodo "getGameGrid". L'introduzione di questo metodo si deve al fatto che la griglia di gioco originale è una mappa le cui chiavi e i cui valori sono rispettivamente di "Location" e "Tile", mentre, nel nostro caso, la griglia di gioco deve esser composta da "Location" ed "Integer". Per questo motivo abbiamo ricostruito la griglia di gioco secondo le modalità appena descritte.


Player.java
====================
La classe “Player” è stata introdotta per gestire il thread del giocatore automatico. Infatti, come si può notare dall'handler del toogleButton presente nella classe Game2048.java, viene creato un nuovo thread, il cui costruttore accetta come parametro un oggetto di tipo Runnable. Tale oggetto non è altro che la classe Player. Tuttavia, per far si che la classe svolga il nostro obbiettivo, abbiamo implementato l'interfaccia Runnable (condizione necessaria per il corretto funzionamento del thread). All’interno di questa classe, inoltre, è presente una particolarità: non essendo possibile modificare gli elementi di JavaFx mediante un comune thread, abbiamo invocato il metodo statico “runLater” della classe “Platform”, per far in modo che ogni istanza del thread venga messa in una coda ed eseguita appena possibile. Questo procedimento ci ha consentito di creare un thread secondario che agisce solamente su una parte della finestra principale, permettendoci di modificare lo stato della griglia di gioco in base ai criteri implementati nella classe MyGiocatoreAutomatico.java.


MyGiocatoreAutomatico.java
====================
Quest'ultima classe è il fulcro del nostro sistema di calcolo e gestione della mossa successiva. Considerando un ipotetico diagramma di flusso, la classe si può pensare divisa in due parti: la prima composta da tutti quei metodi che effettuano una simulazione del gioco in un determinato stato della griglia per una determinata direzione, la seconda composta da tutti quei metodi che, basandosi su tale simulazione, riescono a determinare la miglior mossa da effettuare. Nello specifico, i metodi maggiormente coinvolti nella simulazione sono i seguenti: “findFarthestLocation”, “tileForDirection” “moveToLastPosition” e “merge”. Questi metodi, però, non agiscono sulla griglia di gioco originale, bensì su una sua copia e vengono invocati dal metodo "shift", che ha il ruolo più importante. Dopo aver effettuato la simulazione, il controllo del giocatore automatico viene affidato ai metodi "computerScore", "playerScore" ed "heuristicEvaluation". Concettualmente l'operato dei primi due agisce su un albero di gioco (albero delle mosse disponibili) ricercando ricorsivamente, per ogni livello di profondità, la mossa migliore. Di conseguenza, più la ricerca avviene in profondità, più è facile predire un certo numero di mosse per una determinata direzione. Tuttavia, nel nostro contesto, si deve tener presente il fattore casualità, ovvero l’impossibilità di sapere a priori quale numero possa uscire tra 2 e 4. Di conseguenza l'albero di gioco presenta nodi di casualità, cioè nodi in cui il valore non può essere determinato con certezza. Quindi la soluzione che abbiamo deciso di implementare si basa sull’attribuzione di una nota probabilità ai due valori che potrebbero uscire, in modo da inserire tali valori all'interno della griglia di gioco, simulare lo spostamento del nostro giocatore automatico (attraverso il metodo "playerScore") e valutare la griglia in base al risultato di una valutazione euristica e della valutazione delle probabilità. La valutazione euristica ci consente di stimare la bontà di una griglia di gioco, prendendo in considerazione la disposizione delle celle. Per far ciò abbiamo utilizzato un gradiente, facendo in modo che ad ogni locazione della griglia venga assegnato un determinato peso che ci consenta una disposizione ottimale delle celle, al fine di raggiungere 2048. Infatti, in questo modo, la combinazione delle celle con valore più grande (attraverso reazioni a catena di combinazioni) è facilitata, in quanto si riduce la probabilità che celle con valori piccoli si trovino in mezzo a celle con valori più grandi. In ultima analisi, si può notare che nel metodo "prossimaMossa", a secondo del numero di celle vuote, la ricerca viene effettuata per due differenti livelli di profondità. Infatti la ricerca inizia con un albero di profondità pari a 2 e man mano che si va avanti nel gioco e sono presenti meno celle vuote, la ricerca aumenta la sua profondità, portandosi ad un livello pari a 3.

