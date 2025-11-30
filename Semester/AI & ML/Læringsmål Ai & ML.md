

### **1. Opnå forståelse for fundamentale begreber inden for AI og ML**

Målet er at kunne redegøre for centrale principper i kunstig intelligens, machine learning og generativ AI, samt forstå forskellen mellem ML-elementerne i projektet (embeddings, similarity scoring, tekstklassifikation) og AI-delen (RAG, prompting, sikkerhedslag, agent-flow).

---

### **2. Opnå viden om forskellen mellem NLP og LLM-baseret sprogforståelse**

Målet er at forstå, hvad klassisk NLP indebærer (tekstopdeling, tokenisering, simpel klassifikation og semantiske repræsentationer), og hvordan det adskiller sig fra LLM’er, der arbejder med reasoning, kontekstforståelse og generering af tekst. Der lægges vægt på at kunne forklare, at tokenisering indgår i begge, men anvendes forskelligt.

---

### **3. Opnå indsigt i, hvordan tekst behandles i et LLM- og RAG-system**

Målet er at forstå, hvordan tekst konverteres til tokens og embeddings, hvordan dokumenter opdeles i mindre dele (chunking), og hvordan disse bruges i en vektordatabase til relevanshentning. Der lægges vægt på viden om kontekststyring, dvs. hvordan embeddings, metadata og relevansscoring tilsammen bestemmer, hvilken viden modellen får adgang til, og hvordan korrekt kontekstopbygning reducerer hallucinationer og øger præcisionen i et RAG-system.

---

### **4. Opnå grundlæggende viden om styrker og begrænsninger ved forskellige LLM-modeller**

Målet er at forstå på et basalt niveau, at modeller varierer i præcision, hastighed og kvalitet, og have praktisk indsigt i hvordan modelvalg påvirker et RAG- eller agent-setup – uden behov for dyb teknisk viden om arkitekturer.

---

### **5. Opnå forståelse for prompt engineering og strukturerede prompt-strategier**

Målet er at lære, hvordan systeminstrukser, roller, constraints og prompt-skabeloner påvirker en LLM’s output, og hvorfor strukturerede prompts er nødvendige for stabile og mere kontrollerede AI-svar.

---

### **6. Opnå viden om AI-agenter, reasoning og sikkerhedslag**

Målet er at forstå forskellen mellem en simpel chatbot og et agent-baseret system, herunder de centrale trin i en agent-pipeline: klassificering, sikkerhedslag, dokumenthentning, modelgenerering og output-validering. Der lægges vægt på forståelsen af nonsens-detektion, filtrering og fallback-logik.


### **7. Opnå grundlæggende viden om Python til brug i AI-integration og pipeline-styring**

Målet er at kunne forstå, hvordan Python anvendes til at opbygge en RAG-pipeline, håndtere embedding-generering, udføre async-kald, arbejde med en vektordatabase og styre agent-flowet.



# **Konklusion – hvordan læringsmålene er dækket**

Mit AI-projekt dækker alle læringsmålene gennem den samlede RAG- og agent-arkitektur, jeg har bygget. Arbejdet med embeddings, similarity scoring og tekstklassifikation har givet praktisk forståelse for de ML-elementer, der indgår i projektet, mens RAG, prompting, sikkerhedslag og agent-flowet giver indsigt i centrale AI-begreber.

Forskellen mellem klassisk NLP og LLM-baseret sprogforståelse opleves direkte i systemet: NLP-delen håndterer tekstopdeling, tokenisering og semantiske repræsentationer, mens LLM’en står for reasoning, kontekstforståelse og generering af svar.

Jeg har desuden arbejdet konkret med, hvordan tekst behandles i et RAG-system — fra chunking og embeddings til relevanshentning i vektordatabasen og opbygning af korrekt kontekst for at reducere hallucinationer. Modelvalg mellem Llama og Mistral har givet erfaring med forskelle i ydeevne og præcision.

Derudover har udviklingen af prompt builder, roller og constraints givet praktisk erfaring med prompt engineering, og agent-flowet (klassificering → sikkerhedslag → RAG → generering → fallback) har givet indsigt i forskellen mellem en simpel chatbot og et fuldt agent-baseret system.

Endelig har Python-delen givet hands-on erfaring med at opbygge en pipeline, håndtere embedding-generering, arbejde med en vektordatabase og strukturere hele agent-processen.