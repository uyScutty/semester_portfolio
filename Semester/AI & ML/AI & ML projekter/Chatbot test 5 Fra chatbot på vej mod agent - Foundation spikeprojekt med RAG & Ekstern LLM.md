
Her bygges videre på det der er lært i de tidligere projekter og henimod den endelige bot til hjemmesiden.


Jeg vil arbejde videre med nogen af de skabeloner jeg lavede i tidligere projekter, især i [[Chatbot test 4 (Rest API(Fast API) Gateway.]]  og bruge dette som kontekst og inspiration og følge min læring fra disse projekter.




Derudover skifter jeg for første gang fra testdata → **rigtige tekster** fra The Way of Coherence.




## 1. relevant data hentning, chunking og embedding.
Jeg startede med at hente html indholdet fra den originale side www.thewayofcoherence.com og ekstrahere teksten fra siderne og renset det via AI.
Jeg har hentet en række blog indlæg og informationer som about, consultation, contact mv.
De er hentet i HTML format og det er derefter blevet struktureret i et json-objekt på følgende måde.

![[Pasted image 20251125174337.png]]

bagefter sakl vi lave vores embeddings og vector index via:

- **Chunking**  
    – lange tekster deles i mindre bidder (fx 200–300 ord).  
    → det gør søgningen mere præcis.
    
- **Embeddings**  
    – hver chunk bliver til et tal-vektor-aftryk (384 tal).  
    → modellen kan _beregne_, hvad der ligner hinanden.
    
- **FAISS index**  
    – en lokal database, der kan finde _de mest relevante stykker tekst_  
    → bruges til RAG før vi spørger modellen.



Jeg oprettede derfor en ny python fil kaldet build_vector_store.py

![[Pasted image 20251126000552.png]]
Modellen laver her chunks af teksten og laver dem derefter om til vektor og laver embeddings ud af chunks delene af teksten.

Der bliver herefter oprettet et FAISS-Index hvor alle embeddings bliver lagt ind og der kan søges i dem ved queries.
Det bliver herefter gemt lokalt.
### `vector_store.bin`

→ selve embeddings-søgemaskinen

### `vector_store_docs.npy`

→ metadata (title, kategori, selve chunket)

Dette gør, at:

✔ Serveren kan genstarte uden at miste data  
✔ Du kan loade indexet direkte i FastAPI  
✔ Du behøver ikke lave embeddings hver gang


Ved kørsel af filen build_vector_store.py så bliver datasættet lavet til 28 chunks.
![[Pasted image 20251127155330.png]]


Der er nu bygget en fuld RAG-motor med chunking, embeddings og FAISS.  
Næste skridt er at forbinde denne motor til OpenAI og bygge den faktiske chatbot-adfærd (guiding, forslag, svar, handlinger).”



Den nuværende rag_engine.py står på nuværende tidspunkt for at håndtere RAG med lokalt AI genereret svar, dog uden prompt builder og derfor skal vi have tilføjet rolle til at styre dens agering og svar til brugeren.


![[Pasted image 20251126115026.png]]

Her har vi nu en skabelonen til en mere relevant chatbot, der kan læse de relevante chunks, forstå dem, formulere et kort svar ud fra disse og guide på siden. Det bliver til **Retriever → Prompt Builder → Generator**.  
Det er ren RAG-arkitektur.

`rag_engine.py` blev udvidet, så den ikke kun håndterer vektorsøgning (FAISS), men også selve AI-svaret.  
Den oprindelige version fungerede som en ren “retriever”, der ud fra embeddings og FAISS-index kun returnerede relevante tekst-chunks fra hjemmesiden.  
For at gøre systemet til en egentlig chatbot blev der tilføjet:

- en promptsamler (`build_prompt`)
- en kald-funktion til Llama via Ollama (`generate_ai_answer`)

Disse to komponenter bygger den nødvendige sammenhæng mellem RAG-søgeresultaterne og den generative model.  
Search-funktionen er uændret og står fortsat kun for retrieval.




Vi ændrer **main.py**, så:

1. Brugeren sender et spørgsmål
    
2. API’en søger i FAISS
    
3. Llama får kontekst
    
4. Llama returnerer et kort guidesvar
    
5. API’en returnerer både AI-svar og kontekstdokumenter
    

Vi tager det stille og roligt


![[Pasted image 20251126115608.png]]


# Hvad denne version nu gør

⚡ **Brugeren spørger → http://localhost:8000/ask?query=noget**

API’en gør:

### 1️⃣ `search_docs(query)`

→ Finder relevante tekstbider fra The Way of Coherence-siden.

### 2️⃣ `generate_ai_answer(query, docs)`

→ Bygger prompt  
→ Sender til Llama3 (via Ollama)

![[Pasted image 20251126130533.png]]

Efter at have arbejdet med den lokale version af chatbotten fra ollama, så går jeg nu over til implmentation af OpenAI API inegration. 
Først henter jeg en key fra openAI og ligger i en .env fil.
Det er fra denne fil vores rag_engine.py fil skal kunne læse API nøglen og bruge den aktivt.

![[Pasted image 20251126135849.png]]
Den er nu blevet ændret til at importere OpenAI modellen og loade klienten.


Så ændrer jeg koden til  funktionen til at generere AI svar. og ændrer det fra at bruge ollama til openAI.

Få “helpful assistant mode” sat op**

Efter skiftet til OpenAI lavede vi en helt ny prompt-struktur, så modellen ikke bare “taler”, men opfører sig som en _guide-bot_.

Vi lagde en systembesked ind:
![[Pasted image 20251126140122.png]]
generate_ai_answer = **sender beskeden** til modellen og **henter svaret**.

System-beskeden er **den vigtigste besked i hele prompt-strukturen**.

Den fortæller modellen:

- **hvem den skal være**
- **hvordan den skal opføre sig**
- **hvilken adfærd den skal følge**
- **hvilke regler der gælder**

System-beskeden er _øverst i hierarkiet_.  
Modellen **prioriterer den over alt andet**.

Derfor virker det som “helpful assistant mode” — du aktiverer en bestemt _personlighed_ og _funktion_ i modellen.

User-beskeden indeholder:

- den prompt-tekst, _du_ (eller din code) har bygget
- kontekst fra RAG
- spørgsmålet
- alt det dynamiske indhold

Det er altså **den del, modellen skal svare på**.

System = regler og rolle  
User = spørgsmålet + konteksten

## ** Det nye i projekt 5**

### **1️ Ny data → ny chunking → nye embeddings**

I dette projekt hentede jeg:

- HTML og tekst fra den rigtige hjemmeside
- about, services, consultations
- blog posts
- kontaktinfo
- generelle helingstekster

Jeg rensede dem via AI, strukturerede dem til JSON og chunkede dem i 200–300 ord.

Fordi dataset er **helt nyt**, skulle jeg:

- lave **ny chunking**
- lave **nye embeddings**
- bygge **nyt FAISS-index**

Derfor findes der nye persistensfiler:

- `vector_store.bin`
- `vector_store_docs.npy`

---

### **2️⃣ RAG + Prompt Builder + Generator (AI-svar)**


`Retriever → Prompt Builder → Generator`

####  Retriever

Bruger FAISS og mine embeddings til at finde relevante tekststykker.

#### Prompt Builder

Her instruerer jeg modellen:

- rolle
- tone
- regler
- hvad den må / ikke må
- kontekst (fra RAG)
- brugerens spørgsmål

Eksempel fra build_prompt:

> “Du er en rolig og empatisk guide på The Way of Coherence.  
> Du skal bruge konteksten der findes via RAG, ellers generer et svar udfra egen viden.

#### Generator

Modellen (Llama eller OpenAI) får prompten og genererer et svar.

Dette giver:

- korte, venlige, kundeservice-agtige svar
- ud fra de rigtige tekster først, ellers fra egen kontekst

---

###  Integration med OpenAI (GPT-4o mini)**

Dernæst testede jeg OpenAI:

- hentede API-nøgle
- lagde den i .env
- importerede OpenAI klient
- skrev generate_answer så den kunne bruge OpenAI

Det gav mulighed for at sammenligne lokal vs. cloud-model.

