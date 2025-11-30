


## **Mål for projektet**

I dette projekt ville jeg bygge et simpelt API-lag (FastAPI) oven på det RAG-system, jeg tidligere lavede i terminalen.  
Formålet er at:

- lave en **rigtig API-gateway**

- afprøve RAG-endpoints

- bruge **testdata**

- bygge en MVP, som klienten (frontend) senere kan kalde

- forstå, hvordan embeddings, FAISS og Python API spiller sammen


Dette er endnu et skridt mod den chatbot, der senere skal integreres på hjemmesiden.


### **1. FastAPI-server (main.py)**


![[Pasted image 20251125130641.png]]
Her opretter jeg først en main.py fil, der er vores API. (FastAPI-server)


ved at køre filen i mit python miljø i terminalen i VSCode, så kan jeg se at den fungerer og returnerer et svar..
![[Pasted image 20251125131744.png]]
Og den kører live på htttp-adressen
![[Pasted image 20251125131835.png]]

Jeg oprettede en lille server, der kan:

- køre 
- returnere “API kører”
- tage imod queries

Eksempel:

`@app.get("/ask") def ask(query: str):     results = search_docs(query)     return {"query": query, "results": results}`



### **2. RAG-motoren (rag_engine.py) – _kun retrieval_ på dette tidspunkt**

I dette projekt returnerer RAG-motoren **kun tekstchunks**, ikke AI-svar.

Flowet:

1. embed spørgsmålet
2. find relevante tekststykker i FAISS
3. returner:
    - tekst
    - kategori
    - titel
    - distance-score

Dette var en ren “retriever” – den genererer ikke endnu svar med AI.

bagefter laver jeg en ny fil, der skal være chat-endpoint, hvor spørgsmål kan stilles til vores model.
rag_engine.py.

![[Pasted image 20251125140812.png]]



derefter udvider jeg main.py med et chat endpoint og importer RAG søgning til at gennemgå dokumenterne fra [[Chatbot i python test 2. (Persitens)]] 
modellen laver similarity search og retrieval via FAISS og returnerer relevante dokumenter den finder via embeddings vi tidligere har lavet.
![[Pasted image 20251125141312.png]]

Den kan nu modtage en query og svarer i forhold til dokumenterne
![[Pasted image 20251125141703.png]]

Derefter installerer vi Ollama-klienten i miljøet, der kan kalde Llama-3 modellen.
Så der kan genereres rigtige ai svar baseret på vores dokumenter.
Det bliver oprettet i en ny fil kaldet llm_engine.py
![[Pasted image 20251125144748.png]]
Denne del af projektet opretter en ny fil, `llm_engine.py`, som står for **selve AI-genereringen**.  
Her forbinder vi Python-koden med den lokale **Llama-3-model** via Ollama-klienten.

Funktionen `generate_answer` gør tre ting:

1. **Bygger en prompt**  
    – den samlede prompt bliver sat sammen af:
    
    - konteksten (de tekstbidder RAG fandt)
    - brugerens spørgsmål
    - en klar rollebeskrivelse ("du er en hjælpsom guide…")
    - en instruks om _kun_ at bruge den givne kontekst.
2. **Sender prompten til Llama-3**  
    – modellen kører lokalt via Ollama (`client.chat`)  
    – prompten sendes som en “user”-besked  
    – Llama genererer et svar baseret på vores regler + konteksten.
    
3. **Returnerer kun selve svaret**  
    – vi trækker AI-modellens svar ud af JSON-responsen  
    (`response["message"]["content"]`).
    

Det betyder, at denne fil for første gang gør systemet i stand til at levere  
**et færdigt, sprogligt formuleret AI-svar baseret på vores egen tekstkontekst.**


vi ændrer nu vores main fil.
![[Pasted image 20251125145612.png]]
### **Hvad der sker her**

I `main.py` binder vi hele RAG-flowet sammen i ét API-endpoint:

1. **FastAPI modtager brugerens spørgsmål**  
    → `query` sendes ind i systemet.
    
2. **RAG finder relevante dokumenter**  
    `search_docs(query)` laver embedding-søgning i FAISS og returnerer de tekster, der matcher bedst.
    
3. **Vi bygger kontekst til modellen**  
    Alle fundne tekststumper samles til én kontekststreng, som vi kan sende til Llama-3.
    
4. **AI’en genererer et svar**  
    `generate_answer()` bruger konteksten + spørgsmålet til at få Llama-3 (via Ollama) til at lave et færdigt svar.
    
5. **Vi returnerer samlet resultat**  
    – spørgsmålet  
    – AI-svaret  
    – og de dokumenter, der blev brugt som kilder.




![[Pasted image 20251125145541.png]]
## **Hvad man ser i outputtet**

Her kan vi se det færdige resultat af hele chat-flowet:

1. **`query`** → det spørgsmål brugeren stillede.
2. **`answer`** → det svar som Llama-modellen genererede, baseret udelukkende på konteksten fra vores tekster.
3. **`sources`** → de tekststykker fra FAISS, som systemet fandt mest relevante.
    - hver kilde har både _tekst_ og en _distance-score_
    - en lav distance betyder, at teksten minder meget om brugerens spørgsmål.

Det viser, at systemet laver en fuld RAG-proces:  
**retrieval af tekster → byggede kontekst → AI-svar → returner kildeteksterne.**


# **Hvad projekt 4 lærte mig**

_(teknisk, præcist og baseret på det du faktisk lavede)_

I dette projekt arbejdede jeg videre på de tidlige tests, men nu med fokus på at bygge det første rigtige **chat-endpoint**, der kombinerer:

- simpel RAG-søgning
    
- FastAPI
    
- FAISS
    
- og for første gang: **integration med en generativ AI-model (Llama via Ollama)**
- *En simpel systemprompt og let rollebeskrivelse og prompt-template*

Selvom jeg stadig brugte små testdata, lærte jeg flere vigtige ting teknisk

