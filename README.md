# Esame

https://github.com/TdP-esami/2023-05-30-simulazione/tree/soluzione
PRIMA PARTE

```java
private Graph<?,?> graph;
private List<Node> allNodes; 
private Dao dao;
//private Map<Integer, Node> idMap; // Only if necessary!

public Model() { 
    dao = new Dao(); 
}

public void creaGrafo() {
    graph = new Graph<>(Edge.class); 
    Graphs.addAllVertices(graph, allNodes);

    //TODO load Edges
    //i vertici devono essere diversi, quindi o si fa nelle query o qui

    System.out.println("Il grafo creato ha " + graph.vertexSet().size() + " e " + graph.edgeSet().size() + " archi\n");

}
```

ALTRI METODI UTILI PER LA PRIMA PARTE

```java
public String getMax() { // nodo con più vicini
  People max = null;
	int maxn = 0;
	for(People p: graph.vertexSet()) {
		if(Graphs.neighborListOf(graph, p).size() > maxn) {
			maxn = Graphs.neighborListOf(graph, p).size();
			max = p;
		}
	}	
  return "\n " + max.toString() + " con grado massimo " + maxn;
}
```

```java
public String getConnesse() { // numero di componenti connesse
  ConnectivityInspector<People, DefaultEdge> inspect = new ConnectivityInspector<>(graph);
  
  int n = inspect.connectedSets().size();
  
  return "Ci sono " + n + " componenti connesse"; 
}
```

METODO PER NODI CON NUMERO DI VICINI MASSIMO
```java
public ArrayList<String> getMax() { 
		lista = new ArrayList<>();
		int max = 0;
		for(Location l: graph.vertexSet()) {
			if(max< Graphs.neighborListOf(graph, l).size()) {
				max = Graphs.neighborListOf(graph, l).size();
				lista.clear();
				lista.add(l);
				l.setVicini(max);
			}else if(max == Graphs.neighborListOf(graph, l).size()) {
				lista.add(l);
				l.setVicini(max);
			}
		}
		ArrayList<String> nomi = new ArrayList<>();
		for(Location l1: lista) {
			nomi.add("\n" + l1.getNome() + " ha " + l1.getVicini() + " vicini");
		}
		return nomi;
	}
```

METODO PER ARCHI MAGGIORI DELLA MEDIA
```java
public ArrayList<Edge> getMigliori(){
		double peso = 0;
		for(Edge e: archi) {
			peso += e.getPeso();
		}
		peso = peso/archi.size();
		ArrayList<Edge> migliori = new ArrayList<>();
		for(Edge e: archi) {
			if(e.getPeso() > peso)
				migliori.add(e);
		}
		Collections.sort(migliori);
		return migliori;
	}
```
Consigli sulle query:
-se c'è scritto "almeno" per calcolare gli archi = poni tutti gli attributi uguali e metti i 2 nodi diversi
-ricorda distinct/distinctrow
-se devi verificare dati 2 nodi, quali elementi hanno in comune o metti un count nel select e nel where nodo.elemento = nodo.elemento2 oppure usi la idmap e 
aggiungi una lista agli attributi del nodo.

METODO PER ORDINARE GLI ARCHI IN BASE AL PESO

```java
List<DefaultWeightedEdge> edgeList = new ArrayList<>(graph.edgeSet());

edgeList.sort(Comparator.comparingDouble(graph::getEdgeWeight));

for (DefaultWeightedEdge edge : edgeList) {
    double weight = graph.getEdgeWeight(edge);
    String sourceVertex = graph.getEdgeSource(edge);
    String targetVertex = graph.getEdgeTarget(edge);

    System.out.println("Arco: " + sourceVertex + " -> " + targetVertex + ", Peso: " + weight);
}
```

SECONDA PARTE


RICORSIONE
È importante inizializzare best all’inizio di trova percorso e i vari parametri
Impostare la condizione di terminazione
Controllare il caso in cui parziale.size è uguale a 0
Controllare che non sia già presente l’oggetto in parziale
Nel caso si parla di neighboorlistof ricorda di cancellare quelli che non puoi usare o che sono già presenti


ESEMPIO
```java
public String trovaPercorso() {
		best = new ArrayList<>();
		lista = new ArrayList<>();
		alto = 0;
		for(People p: graph.vertexSet()) {
			lista.add(p);
		}
		List<People> parziale = new ArrayList<>();
		int parz = 0;
		cerca(parziale, parz);
		String s = "\nIl percorso è: \n";
		for(People p: best) {
			s += p.toString() + "\n";
		}
		s += "Costo: " + alto;
		return s;
	}
	private void cerca(List<People> parziale, int parz) {
		if(lista.size() == 0)
			return;
		if(parz > alto) {
			best = new ArrayList<>(parziale);
			alto = parz;
		}
		for(People p: graph.vertexSet()) {
			List<People> vicini = new ArrayList<>();
			if(parziale.size() == 0 || (!parziale.contains(p) && lista.contains(p))) {
				parziale.add(p);
				parz += p.getSalary();
				vicini = Graphs.neighborListOf(graph, parziale.get(parziale.size()-1)); 
				lista.removeAll(vicini);
				cerca(parziale, parz);
				lista.addAll(vicini);
				parziale.remove(p);
				parz -= p.getSalary();
			}
				
		}
	}
```

SIMULAZIONE
```java
//variabili iniziali




//variabili di aggiornamento
PriorityQueue<Event> queue;

//variabili di output



public Simulazione(...){
  this.queue = new PriorityQueue<Event>();
  //...
}

public void init(){
  LocalDate ora = this.inizio;
  while(ora.isBefore(fine)){
			this.queue.add(new Event(ora, EventType.RIFORNIMENTO, rif)) ;
			ora = ora.plusMonths(1);
		}
}

public void run(){
  while(!this.queue.isEmpty()) {
			Event e = this.queue.poll() ;
			System.out.println(e) ;
			
			processEvent(e) ;
		}
}

private void processEvent(Event e) {
		LocalDate date = e.getDate() ;
		switch(e.getType()) {
		case RIFORNIMENTO:
		
			break;
		case PRELIEVO:
			
			break;
		}
	}
```

La classe event di solito è così
```java
package it.polito.tdp.gosales.model;


import java.time.LocalDate;

public class Event implements Comparable<Event> {
	
	public enum EventType {
		RIFORNIMENTO,
		PRELIEVO
	}
	
	private LocalDate date ;
	private EventType type ;
	private int quantita;
	
	public Event(LocalDate date, EventType type, int quantita) {
		this.date = date;
		this.type = type;
		this.quantita = quantita;
		
	}
	
	public int getQuantita() {
		return quantita;
	}
	
	public LocalDate getDate() {
		return date;
	}

	public EventType getType() {
		return type;
	}


	@Override
	public int compareTo(Event other) {
		return this.date.compareTo(other.date) ;
	}

	@Override
	public String toString() {
		return date + "-" + type;
	}

	
}
```

Una cosa importante con i set è che è possibile fare l'intersezione con retainAll

FXML 
```java

``try {
    		int anno = Integer.parseInt(txtYear.getText());
    		if(anno > 2020 || anno < 1870) {
    			txtResult.setText("Inserire un anno tra il 1871 e il 2019");
    			return;
    		}
    		int salary = Integer.parseInt(txtSalary.getText());
    		btnConnesse.setDisable(false);
    		btnDreamTeam.setDisable(false);
    		btnGradoMassimo.setDisable(false);
    		
    		model.creaGrafo(anno, salary);
    	}catch(NumberFormatException e){
    		txtResult.setText("Anno inserito in modo scorretto");
    	}`
