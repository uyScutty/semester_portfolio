
# AI og ML i projektet – hvad bruger vi, og hvordan?

I projektet arbejder vi både med **AI** og **ML**, men de spiller meget forskellige roller.  
Den korte realitet er:

- **AI står for langt størstedelen af funktionaliteten**
    
- **ML bruges primært bag kulissen til nogle få specifikke teknikker**
    

Det er vigtigt at skelne mellem **at træne modeller** (klassisk maskinlæring)  
og **at bruge modeller** (AI-inference).  
I vores projekt gør vi det sidste.

---

# 🔷 AI – den centrale del af systemet

Langt hovedparten af projektets “intelligens” kommer fra en **stor sprogmodel (LLM)**, som vi bruger til at:

- forstå brugerens spørgsmål
    
- følge en bestemt tone og personlighed
    
- generere naturlige og relevante svar
    
- kombinere spørgsmål med kontekstdokumenter (RAG)
    
- håndhæve regler som “ingen medicinske diagnoser” eller “brug kun dokumenteret kontekst”
    

I praksis betyder det, at stort set hele chatbot-oplevelsen —  
det sproglige, det forstående, det forklarende —  
stammer fra AI-modellens evner.

### AI bruges især i:

- prompt-builderen (tone, stil, regler)
    
- selve chatfunktionaliteten
    
- RAG-svarene
    
- Python API’et der håndterer forespørgsler
    
- den måde modellen forbinder kontekst og spørgsmål
    

AI-modellen er altså motoren, der producerer svarene og styrer hele samtalen.

---

# 🔶 ML – en mindre, men vigtig teknisk komponent

Selvom projektet ikke træner egne modeller, bruger vi stadig nogle **maskinlæringsteknikker** i baggrunden.

Der er især tre steder, hvor ML er i spil:

### 1️⃣ Embeddings

Teksten fra vores dokumenter omdannes til **vektorer** via en embedding-model (som selv er trænet gennem ML).  
De vektorer bruges til at måle betydning og lighed mellem tekststykker.

### 2️⃣ Vektor-søgning i Chroma

Når chatbotten skal finde relevant kontekst, laver Chroma:

- nearest neighbor search
    
- cosine similarity
    
- rangering af de mest relevante dokumenter
    

Det er ML-inspirerede teknikker, men vi træner ikke noget selv.

### 3️⃣ Tokenisering

Al brug af LLM’er involverer en ML-trænet tokeniseringsmodel, som opdeler tekst i tokens.  
Det sker automatisk og er en del af pipelines.

---

# 🧩 Hvordan AI og ML spiller sammen

Man kan opsummere det sådan her:

- **ML hjælper systemet med at finde den rigtige viden**  
    (embeddings, vector search)
    
- **AI bruger den viden til at skrive et meningsfuldt svar**  
    (sprogforståelse, tone, reasoning)
    

Det er altså en samarbejdsmodel mellem to teknologier, hvor AI’en står for det meste af brugeroplevelsen, og ML hjælper med strukturering og hentning af viden bag scenen.

---

# 📊 Fordelingen i praksis

Selv om man ofte taler om AI og ML som to lige store dele, ser det anderledes ud i virkelige projekter som vores:

- **AI (≈ 98%)**  
    Hele sprogforståelsen og genereringen kommer fra en LLM, som vi ikke selv træner.
    
- **ML (≈ 2%)**  
    Bruges til embeddings, tokens og vector search – teknikker vi anvender, men ikke udvikler.
    

Denne fordeling er typisk i moderne RAG-systemer, hvor man bygger ovenpå færdigtrænede modeller.





[[Ai & ML/Læringsmål]]