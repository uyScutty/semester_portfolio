
# **1. Sikkerhed i botten (det vi allerede HAR tænkt ind)**

Der er **tre typer sikkerhed** i en RAG-chatbot:

### **A) Indholds-sikkerhed (Content Safety)**

→ handler om at undgå farlige svar

**Dette HAR vi lavet:**

✔ _Medicinsk filter_

- botten må ikke svare på dosering, diagnoser, symptomer.
    
- automatisk “venligt afslag” → sikrer compliance.
    

✔ _Off-topic filter_

- botten svarer ikke på politiske, farlige eller irrelevante emner.
    

✔ _Nonsense filter_

- botten undgår at svare med hallucinationer.
    

✔ _Prompt-restriktioner_

- “brug kun konteksten”
    
- “sig ærligt hvis du ikke ved det”
    
- “ingen skræmmende formuleringer”
    
- “ingen medicinske råd”
    

**→ Dette er indholdssikkerhed og det er 100% implementeret.**

---

### **B) System-sikkerhed (model-kontrol)**

→ beskytter mod at modellen løber udenfor rammerne

✔ _Tone & adfærd defineret i strict prompt_  
✔ _Må ikke opfinde nyt_  
✔ _Må ikke give råd uden for hjemmesiden_  
✔ _Afgrænset til websiteets tema (healingsprocesser, nervesystem, mildhed)_

**→ Denne sikkerhed er vigtig, og vi har fuldt styr på den.**

---

### **C) RAG-sikkerhed (Retrieval Guardrails)**

→ beskytter mod at modellen svarer uden valide dokumenter

✔ _Similarity check (distance-threshold)_

- hvis dokumenter er for irrelevante → botten SKIFTER til fallback
    
- dette er en af de vigtigste sikkerhedsforanstaltninger i RAG
    

✔ _Ingen dokumenter = ingen frie svar_

- botten hallucinerer ikke
    
- den svarer venligt, men uden at finde på nyt
    

**→ Dette er kerne-sikkerhed i RAG og vi har implementeret det korrekt.**

---

# ✅ **2. Ratelimit = også en form for sikkerhed**

Yes — **rate limit er system-sikkerhed**.

Det beskytter:

- OpenAI API
    
- din konto (økonomi)
    
- beskytter serveren mod spam
    
- gør systemet stabilt
    

Med rate-limiting forhindrer vi:

❌ 500 requests/sekund  
❌ bot-angreb  
❌ bots der spammer API’et (dyrt!)  
❌ fejl der kører loops

Du _behøver ikke implementere det nu_, men du kan sige:

> “Vi har planlagt at tilføje rate-limiting som ekstra sikkerhed, så API’en ikke misbruges eller overskrides.”