Målet:  
➡️ Vi tager **de dokument-matches** du finder via FAISS  
➡️ Vi sender dem ind som **kontekst** til **Llama 3** (via Ollama)  
➡️ Modellen genererer et svar baseret på _din egen videnbase_

Dette er første gang du kobler:

- embeddings
    
- similarity search
    
- RAG prompt
    
- en rigtig LLM
    
- og returnerer et AI-svar

# **Hvad vi bygger nu (super enkelt som trin 1):**

1️⃣ Du skriver et spørgsmål i terminalen  
2️⃣ Python embedder dit spørgsmål  
3️⃣ Python spørger FAISS → finder top-matches  
4️⃣ Vi bygger en _RAG-prompt_:

> “Her er noget relevant kontekst: … Brug det til at svare på spørgsmålet …”  
> 5️⃣ Python kalder **Llama 3** via Ollama  
> 6️⃣ Terminalen viser et _rigtigt AI-svar baseret på dine data_


Til dette projekt har jeg oprettet et nyt script.
![[Pasted image 20251124235219.png]]
# Hvad sker der her?

**Kort forklaring:**

- Du loader dit gemte FAISS-index
    
- Du loader metadata (dine dokumenter)
    
- Du loader embedding-modellen
    
- Du opretter en Ollama-client (for at tale med Llama 3)
    

Vi sørger bare for grundstrukturen — ingen query eller LLM endnu

![[Pasted image 20251124235316.png]]# **Hvad sker der i Trin 2?** (kort forklaret)

|Trin|Hvad sker der?|
|---|---|
|**5**|Du skriver et spørgsmål i terminalen|
|**6**|Query embeddes til en vektor|
|**7**|FAISS laver similarity search|
|**8**|Systemet slår tekster op i metadata og viser dem|

Du får nu det samme output som i Projekt 3 — men denne gang skal vi **bruge det videre til RAG**.



![[Pasted image 20251124235427.png]]

Perfekt — så laver vi **Trin 3: Selve RAG-prompten**.

Dette er den del der forvandler dit retrieval-system til en **rigtig AI-assistent**, fordi vi nu kombinerer:

### Vi bygger en robust RAG-prompt

Prompten fortæller modellen:

- _brug kun konteksten_
    
- _vær ærlig hvis konteksten ikke har svaret_
    
- _her er dokumenterne_
    
- _her er spørgsmålet_
    

### ✔ Vi gør klar til at sende prompten til Llama 3


![[Pasted image 20251124235545.png]]# **Hvad sker der her? (kort og teknisk)**

### ✔ **client.generate(...)**

Dette kalder Ollama-serveren på:

`http://localhost:11434`

Og sender din RAG-prompt ind i modellen:

- Prompten indeholder dine _retrieved documents_
    
- og dit _spørgsmål_
    

### ✔ **Modellen genererer et svar**

Llama 3 bruger:

- din kontekst (dine dokumenter)
    
- og selve spørgsmålet
    

til at lave et **nyt, kontekstbaseret svar**.

### ✔ **Vi udtrækker svaret**

`ai_answer = response['response']`

### ✔ **Og printer det**

Terminalen viser nu dit første _rigtige RAG-svar_.

![[Pasted image 20251125001729.png]]
Her ses tydeligt at der tages udgangspunkt i vores dokumenter fra [[Chatbot i python test 2. (Persitens)]]
Modellen kan nemt besvare spørgsmålet via teksterne, da det er præcis samme kontekst og vidensområde det omhandler.


Jeg testede også modellen med et spørgsmål der ikke helt giver mening i forhold til de tekster den henter via RAG. og her blev svaret tydeligt påvirket af modellens egen viden. Her bliver dokumenterne taget med som kontekst, men her er der en langt større påvirkning fra anden viden, da det træder lidt ud over de simple teksters kontekst.
![[Pasted image 20251125124725.png]]
Denne model fungerer via RAG stadigvæk med egen fantasi


# **Teknisk resume af dit RAG-system (Projekt 4)**

## **1) Dokumenterne omdannes til embeddings**

- Du har nogle små tekststykker (dine dokumenter).
    
- De sendes gennem en embedding-model (MiniLM).
    
- Resultatet er vektorer (fx længde 384), som placeres i en FAISS-database.
    

Dette gør det muligt at **sammenligne betydning**, ikke ord.

---

## **2) FAISS bruges til at finde de mest relevante dokumenter**

Når du stiller et spørgsmål:

→ Spørgsmålet embeddes til en vektor  
→ FAISS laver vektor-sammenligning (L2 distance)  
→ De nærmeste dokumenter findes (Top-K resultater)

Det er dine "fundne relevante dokumenter".

---

## **3) Disse dokumenter sendes som kontekst til Llama3**

Dine dokumenter kommer ind før spørgsmålet:

> “Her er nogle dokumenter. Brug dem til at svare så godt som muligt.”

Det betyder at modellen **forstår, hvad dit projekt handler om**, før den svarer.

---

## **4) Modellen bruger både:**

- **Dine dokumenter (RAG-kontekst)**
    
- **Sin egen træningsviden**
    

RAG påvirker svaret kraftigt, men modellen supplerer med sin almindelige viden.

Derfor får du:

- Relevante pointer fra dine dokumenter
    
- Ekstra detaljer fra modellens generelle viden
    

---

## **5) Output er et AI-svar, der er _forankret_ i dine dokumenter – men ikke begrænset til dem**

Derfor gav dit spørgsmål “kan maskinlæring hjælpe på sundhed?” et langt, rigtigt sundhedssvar:

- Dokumenterne gjorde svaret **relevant**
    
- Modellen fyldte selv på med detaljer