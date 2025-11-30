
## **1) Mini-projekt: Embedding Generator**

 Formål

At lære hvordan tekst → embeddings fungerer i praksis.

projektet
I dette projekt sætter jeg et isoleret Python-miljø op og anvender SentenceTransformer-modellen _all-MiniLM-L6-v2_ til at generere embeddings af tekst.  
En embedding er en talbaseret repræsentation af tekst, som modellen bruger til at forstå betydning og kontekst.  
Jeg tester pipeline-opsætningen ved at indlæse modellen, encodere en dansk tekst og udskrive embedding-dimensionen (384) samt de første værdier.  
Dette bekræfter, at AI-miljøet fungerer korrekt og er klar til at blive brugt i en vector-database og i et senere RAG-baseret chatbot-system.


For at køre projektet så laver vi et virtuelt python miljø i powershell for at have et arbejdsmiljø hvor vi kan afprøve vores projekt.
![[Pasted image 20251124162153.png]]

Når .venv vises så er miljøet klart og oppe og køre.
Her kan vi arbejde med pakker, scripts og konfigurere miljøet alt efter behov.

For at arbejde med huggingface bibliotekkerne sentecetransformer og køre det i vores python miljø, så har jeg opsat et script i vscode, hvor jeg importerer model fra biblioteket og afprøver modellen.
Her henter vi modellen "MiniML-L6-v2"

![[Pasted image 20251124162834.png]]

 ved at køre script filen "embeddings_setup.py" så kan vi teste og se modellen der aktivt laver en embedding af en tekst via RAG.
 Den omdanner her den korte "text" til matematiske vektorer, der vil agere som modellens forståelse.
 ![[Pasted image 20251124163409.png]]
først vises længden som er en vektor med 384 tal.
De første 10 værdier er et udsnit af de første 10 værdier, der ligger til grund for modellens forståelse af teksten..

**Lærer dig:**

- Hvad embeddings er
- Hvordan SentenceTransformer virker
- Hvordan Python-moduller importeres
- Hvordan tekst → vektor fungerer



## **2) afprøvning af lokal model (mini, mini, mini projekt)


Jeg startede med at hente en Ollama model til dette projekt for at afprøve en af de lidt større og meget brugte modeller til offline projekter.
![[Pasted image 20251124164653.png]]
Planen her er at teste modellen lokalt.

![[Pasted image 20251124164955.png]]
Først hentede jeg modellen llama3 og fik den til at køre og stillede den et spørgsmål og modtog et svar fra modellen.
Det er her Ollama, men det kunne ligeså godt være mistral, eller andre modeller. Der på samme vis kan fungere


## **3) Mini-projekt: In-Memory Vector Search**

I første forsøg ønskede jeg at bruge ChromaDB til at indeksere mine embeddings.  
Under installationen opstod en række fejl, fordi Chroma kræver C++ build tools og hnswlib-kompilering, hvilket ikke understøttes stabilt på Windows uden avanceret build-miljø.  
Derfor valgte jeg at skifte til FAISS, som er en in-memory vektor-søgemaskine udviklet af Meta. FAISS kræver ingen kompilering på Windows og fungerer perfekt til læringsformål.  
Dette gjorde det muligt at gennemføre projektet og udføre semantic search uden problemer.
Chroma skulle være mest stabilt over docker og det er derfor relevant at se om det ikke vil fungere fint til den tid når AI delen til projektet skal op og køre i en docker container.

![[Pasted image 20251124170046.png]]
Planen var ellers at importere en model fra "sentencetransformers" og via en embedding model gemme i chromaDB.


Istedet valgte jeg en anden aproach hvor jeg via FAISS, der fungerer på windows og kan søge og vise en in-memory-search bare uden chromaDB.

![[Pasted image 20251124170647.png]]
Her importerer vi fra huggingface biblioteket og faiss.
Laver noget tekst der skal søges i via "documents".
Henter transformer model.
Laver embeddings af teksterne.
Laver et index via FAISS i Ram. 
Leder i rammet efter en tekst tæt på vores forespørgsel.
Og faiss returnerer:
![[Pasted image 20251124171213.png]]
Her kan vi igens se de 384 dimensioner/tal som modellen omdanner teksten til.
Her gør den det samme med alle 4 tekster i forhold til de 384 tal i en vektor pr. tal.


**Lærer dig:**

- at oprette en collection
- tilføje tekster
- søge i embeddings
- hvad en vektor database gør

# **Afsluttende gennemgang af Mini-Projekt: FAISS Vector Search**

I dette mini-projekt byggede vi en helt enkel, men fuldt fungerende **semantisk søgemaskine**, der kører lokalt i RAM uden database eller cloud. Formålet var at forstå de grundlæggende elementer i **vector search**, som senere skal bruges i et større RAG-system.

---

## **1. Vi omdannede almindelige sætninger til embeddings**

Ved hjælp af modellen _SentenceTransformer (“all-MiniLM-L6-v2”)_ konverterede vi fire korte tekster til **vektorer med 384 dimensioner**.

Det betyder, at hver tekst nu lever som et sæt tal, der repræsenterer betydningen af teksten — ikke ordene.

→ Derfor så du output som:

`Embedding shape: (4, 384)`

Dette betyder simpelthen: 4 tekster × 384 tal hver.

---

## **2. Vi lagde dem ind i et FAISS-index**

FAISS er Meta’s bibliotek til hurtig vector search.

Vi byggede et L2-baseret index:

`index = faiss.IndexFlatL2(384) index.add(embeddings)`

Dette index fungerer som et “kartotek” over teksternes betydninger.

---

## **3. Vi søgte efter et spørgsmål**

Du gav prompten:

> “How do computers learn?”

Det blev også omdannet til en embedding og sammenlignet med de 4, vi havde lagret.

FAISS fandt de to bedste matches:

`1. Machine learning helps computers learn patterns. (distance: 0.72) 2. AI is transforming the world. (distance: 1.44)`

En **lavere distance betyder større semantisk lighed**, derfor blev maskinlærings-sætningen nummer 1.

---

## **4. Hvad vi har lært i dette projekt**

Dette mini-projekt demonstrerer:

- hvordan tekst → embedding fungerer
    
- hvordan embeddings kan sammenlignes matematisk
    
- hvordan vector search finder relevante tekster ud fra betydning
    
- hvordan man bygger en simpel RAG-lignende funktion uden database
    
- at alt kan køre lokalt uden OpenAI, API-nøgler eller cloud
    

---

## **5. Hvorfor er dette vigtigt?**

FAISS-projektet udgør fundamentet for dit kommende:

- lokale AI-system
    
- dokument-assistent
    
- søgefunktion til sundhedsplatformen
    
- agent-baserede features
    
- integration med Llama3 på Ollama
    
- (senere) RAG med persistente vektor-databaser
    

Det er her, du går fra “embedding-demo” → “rigtig AI-arkitektur”.


I en kommende docker implementering af cahtbotten, så vil der være et miljø, der bliver sat op med samme versioner og uden konflikter "regner jeg med" så vi der undgår de problemer der var under dette projekt med chroma