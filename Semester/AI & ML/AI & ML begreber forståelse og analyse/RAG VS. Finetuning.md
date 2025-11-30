# **Læringsside – Uddybning til Etape “RAG vs. Fine-tuning”**

_(skrevet som om det er dig, i samme tone som dine øvrige blogs)_

---

## 🔎 Min uddybede forståelse af RAG og Fine-tuning

I denne etape begyndte jeg at arbejde mere konkret med forskellen mellem RAG og fine-tuning, og hvad der faktisk giver mening i forhold til de tre forskellige agenter vi skal bygge i projektet. Det blev hurtigt tydeligt for mig, at selvom begge begreber ofte nævnes sammen, så løser de to ting meget forskellige problemer – og har meget forskellige fordele.

---

## 🧠 RAG – det jeg arbejder mest med

RAG (Retrieval Augmented Generation) er i bund og grund en tilgang hvor modellen ikke kun baserer sit svar på det, den allerede er trænet på, men også på ny information, som jeg giver den via lokale dokumenter.

Processen i RAG har jeg efterhånden fået godt styr på:

1. Brugeren spørger om noget
    
2. Der hentes relevante tekststykker fra mine egne dokumenter (via embeddings og vector-database)
    
3. Disse tekststykker bliver sendt ind som kontekst
    
4. Modellen svarer ud fra både prompten _og_ de dokumenter jeg har valgt
    

Det er også denne tilgang, jeg arbejder med i mit Python-projekt lige nu – hvor jeg bruger Chroma som vektor-database og embeddings til at finde relevant viden.  
Jeg bruger også few-shot prompting til at styre tonen og formen på svarene.

Det er en fleksibel løsning, fordi jeg kan opdatere alt indholdet når som helst, uden at skulle træne en model om.

---

## 🧪 Fine-tuning – forstået i forhold til mit projekt

Fine-tuning er en anden tilgang, hvor man tager en eksisterende model og træner den videre på specifik data.  
Modellen ændrer sig altså reelt, når man fine-tuner den.

Det giver fordele ved:

- meget faste opgaver
    
- specifik skrivestil
    
- gentagne formater
    
- klassifikationsopgaver
    

Men i mit projekt er behovet noget andet.  
Jeg har mange tekster, der ændrer sig over tid, og tre forskellige chatagenter, der hver især skal kunne håndtere både generelle og domænespecifikke spørgsmål.  
Hvis jeg skulle fine-tune, skulle jeg gentage processen hver gang jeg opdaterer viden, og det giver ikke mening i mit setup.

Det blev derfor tydeligere for mig, at fine-tuning ikke rigtig løser det behov jeg har.  
Til gengæld kan jeg godt bruge few-shot prompting til at styre stil og svar uden at skulle træne modellen.

---

## 🩺 Relevans for de tre agenter i projektet

Når jeg kobler RAG og fine-tuning sammen med mine konkrete scenarier, giver det her billede:

### **1) Sundhedsagenten**

Skal kunne trække korrekt viden fra mange tekster.  
Det er helt oplagt at bruge RAG her, så svarene bygger på dokumenterne og ikke på modellens egne gæt.

### **2) Den halvspecialiserede agent**

Fx til velvære, coaching eller lignende.  
Her kan jeg genbruge RAG-modellen og bare tilføje nye dokumenter.

### **3) Navigationsagenten (den jeg selv har ansvar for)**

Denne agent skal kunne pege brugeren rundt på siden og forklare indholdet.  
Teksterne vil ændre sig løbende, og derfor er RAG langt mere fleksibel end fine-tuning.

---

## 🎯 Min egen konklusion

Efter at have arbejdet med begge metoder – og især efter at have implementeret dele af RAG selv – giver det mest mening at fortsætte på den vej.  
Fine-tuning virker som en større og tungere proces, og den løser ikke noget, jeg ikke allerede kan gøre med:

- RAG
    
- få-shot prompts
    
- god kontekst
    
- embeddings
    
- og opdaterbare dokumenter
    

Derfor giver RAG mest mening til alle tre agenttyper i projektet, og det er også den tilgang jeg fortsætter med i mit Python-projekt.