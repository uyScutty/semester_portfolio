# **Blog – Etape: LLM-modeller og deres styrker, svagheder og anvendelse**

## 🎯 Mål for etapen

Jeg ville i denne etape skabe et overblik over forskellige LLM-modeller og forstå, hvad de hver især er gode til.  
Det er vigtigt i forhold til projektet, fordi vores tre chatagenter kan have forskellige behov, både i forhold til sprog, domæne, pris og præcision.

---

## 📚 Hvad jeg lærte

Jeg er stødt på en del forskellige modeller efterhånden, og det er blevet tydeligt for mig, at de ikke fungerer ens. Både deres arkitektur, størrelse, træningsdata og tokenisering gør en forskel.

Her er et samlet overblik over de modeller, jeg har arbejdet med eller lært om indtil nu:

---

### 🔹 **OpenAI (GPT-modellerne)**

OpenAI-modellerne er meget stærke generelle modeller. De er gode til:

- formidling
    
- forklaringer
    
- logik
    
- generering af længere svar
    

De håndterer det meste rigtig godt, men de er også de dyreste at bruge.  
Til gengæld er de stabile og pålidelige, og de fungerer rigtig godt til sprog som dansk og engelsk.

---

### 🔹 **Claude (Anthropic)**

Claude er især god til lange dokumenter og komplekse sammenhænge. Den har et meget stort kontekstvindue og gode evner til at bevare struktur i længere svar.

Den virker også rigtig god til opsummering og at holde styr på detaljer.  
Den kan dog være lidt dyrere, og jeg er ikke sikker på hvor god den er på meget domænespecifik sundhedsdata.

---

### 🔹 **DeepSeek**

DeepSeek-modellerne er open-source og gratis, og de overraskede mig positivt.  
De virker stærke til logik, reasoning og især kodning.  
De kan godt være lidt mere ustabile i svarene, men til gengæld er de økonomisk rigtig gode, og de kan køre lokalt.

Til projektet giver DeepSeek mest mening i udviklingsfasen og prototyper, men måske ikke som primær sundhedsagent.

---

### 🔹 **Llama / Mistral / Gemma (open-source modeller)**

Disse modeller er gratis og kan køre lokalt via fx Ollama.  
De er rigtig gode, hvis man vil have fuld kontrol, lave test, eller bygge noget der ikke skal koste penge i API-kald.

De kan dog variere meget i kvalitet, især på dansk og sundhedsrelaterede emner.  
Men nogle af dem (fx Mistral) er blevet meget stærke i almindelig generering.

---

### 🔹 **BERT og lignende encoder-modeller**

BERT er en helt anden type model, som jeg har lært bruges primært til:

- klassifikation
    
- forståelse
    
- søgning
    
- embeddings
    

Den genererer ikke svar og er ikke en chatbot.  
Men den er vigtig i RAG-systemer, fordi encoder-modeller bruges til at generere embeddings.  
Det gør den indirekte relevant for mit Python-projekt.

---

## 💡 Refleksion

Når jeg ser på alle modellerne samlet, giver det mere mening for mig, hvorfor man ikke bare kan “vælge en tilfældig LLM”.  
Der er forskelle i:

- hvor gode de er til dansk
    
- hvor dybe deres svar er
    
- om de er gode til sundhedsrelevante spørgsmål
    
- hvor dyre de er at bruge
    
- om de kan køre lokalt
    
- og hvor stabile de er
    

OpenAI og Claude virker som stærke valg til en sundheds- eller informationsagent, fordi de har stabilitet, sprogforståelse og gode forklaringer.  
DeepSeek og open-source modellerne virker bedre som udviklingsværktøjer eller billige alternativer, men måske ikke til det mest følsomme indhold.

---

## 💡 Projekt specifikt

Jeg kan allerede se, at vores tre chatagenter måske ender med at bruge forskellige modeller – eller i hvert fald have forskellige styrker.

- **Guider-agenten** vil drage fordel af stabile modeller, der kan forklare ting klart.
    
- **Sundhedsagenten** kræver en model, der er god til at forstå fagtermer og som giver mindre risiko for fejl.
    
- **Træningsplan-agenten** skal nok bruge en model, der kan generere strukturerede svar, og her kan finetuning komme på tale.
    

Det er tydeligt for mig, at modelvalg hænger sammen med både RAG, tokenisering, transformer-arkitekturen og den måde jeg bygger Python-projektet på.  
Det hele begynder at hænge sammen nu, og jeg føler, at jeg har fået en mere realistisk forståelse af forskellene mellem modellerne og deres styrker i praksis