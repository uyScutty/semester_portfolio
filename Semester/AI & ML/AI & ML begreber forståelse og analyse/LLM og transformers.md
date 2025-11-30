
# **Uddybning til Dagbog Etape 2 (LLM’er & Transformers)**

_(Dette er den version du kan linke til fra din dagbog.)_

---

## 🧠 **Hvad er en LLM egentlig?**

En Large Language Model (LLM) er en maskinlæringsmodel, der er trænet til at forstå og generere menneskeligt sprog.  
Den arbejder ikke med “ord” som mennesker, men bruger matematiske repræsentationer, der gør det muligt at:

- forstå mening
    
- finde sammenhænge
    
- svare, opsummere, forklare, analysere
    
- forudsige næste token i en sætning
    

LLM’er er en slags “tekstmotor” bygget ovenpå transformer-arkitekturen.

---

## 🧩 **Transformers – den underliggende arkitektur**

Transformers er den teknologi som moderne LLM’er (GPT, DeepSeek, Claude, Llama, Gemma osv.) bygger på.  
Tre ting gør transformers specielle:

---

### **1) Attention (fokusmekanismen)**

Attention gør det muligt for modellen at:

- læse hele sætningen på én gang
    
- forstå relationer mellem ord
    
- fokusere på vigtige detaljer
    
- holde styr på kontekst
    

Eksempel:  
I sætningen _“patienten tog medicinen fordi hun følte svimmelhed”_  
skal modellen forstå at “hun” refererer til “patienten” og at “svimmelhed” er årsagen.

Det kræver **attention**.

---

### **2) Parallel læsning**

Ældre modeller (RNN, LSTM) læste tekst ét ord ad gangen.  
Transformers læser _alt samtidigt_, hvilket giver:

- hurtigere modeller
    
- bedre forståelse af lange tekster
    
- bedre logiske svar
    

---

### **3) Lag på lag af forståelse**

Transformers består af mange lag, og hvert lag lærer noget forskelligt:

- nogle lag lærer grammatik
    
- nogle lærer fakta
    
- nogle lærer logik
    
- nogle lærer sammenhænge mellem begreber
    

Det gør dem fleksible og anvendelige på mange områder — også sundhedsdata.

---

## 🧱 **Tre vigtige arkitekturer: Encoder, Decoder og Encoder–Decoder**

---

### **🔹 Encoder-only modeller (f.eks. BERT)**

Disse modeller er rigtig gode til:

- at forstå tekst
    
- klassifikation
    
- sentiment
    
- søgning
    
- embeddings til RAG
    

De genererer **ikke tekst**.

→ I dit projekt bruges encoder-lignende funktionalitet indirekte gennem _embeddings_ i RAG.

---

### **🔹 Decoder-only modeller (f.eks. GPT, Claude, DeepSeek)**

De kan:

- generere tekst
    
- føre samtaler
    
- svare på spørgsmål
    
- løse opgaver
    
- forklare komplekse ting
    

De er de klassiske chatbot-modeller.

→ I dit projekt er dette den type du bruger til din **guider-agent** og sundhedsagent.

---

### **🔹 Encoder–decoder modeller (f.eks. T5, FLAN-T5)**

De er bedst til:

- oversættelse
    
- opsummering
    
- konvertering af tekst fra én form til en anden
    

→ Ikke lige så relevante for selve chatbotdelen, men nyttige til databehandling.

---

## 🌐 Hvorfor modellernes forskelle betyder noget i praksis

Du nævner i din dagbog, at modeller opfører sig forskelligt.

Her er **hvorfor**:

### 🔸 De er trænet på forskellige datasæt

Nogle har meget sundhedsdata.  
Nogle har næsten intet.  
Nogle modeller “forstår” bedre dansk eller engelsk end andre.

### 🔸 De bruger forskellige tokenizere

Danske eller medicinske ord kan være “svære” for visse modeller.

Eksempel:  
“kronisk sygdom” kan blive 3 tokens i én model og 6 tokens i en anden.

→ Påvirker pris og kvalitet.

### 🔸 De har forskellige styrker

- OpenAI → stærk til reasoning, forklaringer
    
- Claude → stærk til lange dokumenter
    
- DeepSeek → stærk til logik og kode
    
- Mistral/Llama → stærke lokale modeller
    
- BERT → stærk til forståelse, ikke generering
    

→ Derfor skal du vælge model efter agentens opgave.

---

## 🏥 Relevans for dit sundhedsprojekt

Du nævner i blog 2, at dine chatagenter skal forstå sundhedsdata.

Det stiller krav:

### ✔️ Modellen skal forstå fagtermer

Som “akut svimmelhed”, “beta-histidin”, “blodtryk”, “migraine aura”, osv.

### ✔️ Modellen skal være god til kontekstforståelse

F.eks. når brugerens sætning indeholder symptomer, årsager og kontekst.

### ✔️ Modellen må ikke “hallucinere” for meget

Derfor er _modelforståelse_ og transformer-kendskab vigtigt.

### ✔️ Derfor skal du teste forskellige LLM’er

— især dem med god træning på sundhedsrelateret indhold.