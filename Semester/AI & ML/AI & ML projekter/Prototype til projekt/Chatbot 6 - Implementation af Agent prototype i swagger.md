# **Beskrivelse af agent 


Målet med projektet er at udvikle en intelligent guide-agent til websitet _The Way of Coherence_.  
Botten skal ikke være en generel chatbot, men en fokuseret assistent**, der kun bruger indholdet fra hjemmesiden til at hjælpe brugeren med at navigere, finde artikler og forstå centrale temaer omkring heling og nervesystemet.


Projektet bygger på en kombination af og er en videreudvikling af [[Chatbot test 5 Fra chatbot på vej mod agent - Foundation spikeprojekt med RAG & Ekstern LLM]]:

- **RAG (Retrieval-Augmented Generation)**
- **OpenAI’s GPT-model**
- **egen vektordatabase** baseret på FAISS
- **embedding-model til tekstforståelse**
- **stramme regler for tone, adfærd og begrænsninger**

Planen er:

1. At hente sidens tekster, splitte dem op i mindre chunks og lagre dem som embeddings.

2. At lade et Python-backend API hente relevante tekststykker baseret på brugerens spørgsmål.
 
3. At give AI-modellen præcise instruktioner om tone, stil og begrænsninger, så svarene passer til hjemmesidens budskab.

4. At sikre, at botten **ikke hallucinerer**, men i stedet svarer venligt og guider brugeren, hvis noget ikke findes i dokumentationen.
    1. At implementere fallback-logik:

    - Off-topic
        
    - For brede spørgsmål
        
    - Medicinske spørgsmål
        
    - Småsnak
        
    - Ukendt indhold
        
6. At gøre løsningen klar til senere at kunne kobles på en UI-widget på hjemmesiden.


Vi har lavet vores chunks og embeddings i det forrige projekt og Vi havde vores rag_engine.py fil i [[Chatbot test 5 Fra chatbot på vej mod agent - Foundation spikeprojekt med RAG & Ekstern LLM]] som jeg her vil ændre til at passe til den endelige version af prototype chatbotten. 
![[Pasted image 20251127003344.png]]
”**
#  **1. PROMPT BUILDER — “Hvordan botten taler”**

 Dette handler om TONEN, ROLLERNE, HVAD den må og ikke må, og hvordan hele prompten bygges.

**Hører til build_prompt():**

### Indeholder:

- tone: rolig, empatisk, menneskelig
    
- rolle: “du er en guide på The Way of Coherence”
    
- regler:
    
    - ingen medicinske diagnoser/dosering
        
    - brug KUN konteksten
        
    - sig ærligt hvis noget mangler
        
    - hold dig til nervesystem, mildhed, heling, selvomsorg
        
- brug context_text
    
- generér et venligt, kort svar


### Formål:

 **Styre botten sprogligt og adfærdsmæssigt**  
 gøre den “The Way of Coherence”-agtig  
 sikre at botten ikke hallucinerer

“Prompt builderen bestemmer **hvordan** botten taler og måden hvorpå svaret bliver givet,men ikke **hvad** den svarer præcist.”


#  **2. KLASSIFIKATION — “Hvilken type spørgsmål er dette?”**

 Dette er funktionen **classify_query(query)**.

Den analyserer kun spørgsmålet.  
Ingen AI. Kun simple if-regler.

![[Pasted image 20251128121251.png]]
### Indeholder:

- nonsense → for korte spørgsmål (“??” eller 1 tegn)
- smalltalk → “hej”, “hvem er du”, “hvad kan du”
- medical → diagnose, dosering, medicinord
- off_topic → ikke relatere til site
- normal → alt andet


![[Pasted image 20251128121515.png]]

i ovenstående billede bliver de hvert topic defineret udfra hvad der vil falde i den kategori og udløse at agenten opfatter det som værende fx. smalltalk eller medicinsk indhold. Hvilket afleder dens reaktions mønster og svar generering.


### Formål:

 **Bestemme hvilken kategori spørgsmålet tilhører**  
 **Stoppe farlige eller irrelevante spørgsmål tidligt**  
 Beslutte om vi skal bruge fallback eller sende videre til RAG


“Klassifikationen beslutter **om** botten må besvare spørgsmålet, og hvilken type svar der er passende.”


#  **3. FALLBACK SYSTEM — “Hvad svarer vi, hvis vi ikke kan bruge RAG?”**

 Dette er funktionen **handle_fallback(query, category)**.

Den bruges når:

- spørgsmålet er nonsens
- smalltalk
- medicinsk
- off-topic
- eller hvis RAG ingen relevante dokumenter fandt

### Indeholder:

- standard-svar til nonsense
- venlig hilsen til smalltalk
- medicinsk afvisning
- off-topic svar (send brugeren tilbage til sidens tema)
- fallback→fallback hvis noget helt uventet sker
-

![[Pasted image 20251128121814.png]]

### Formål:

 **At give sikre, venlige, afgrænsede svar**  
 ERSTATTER RAG/LLM når spørgsmålet ikke er relevant  
 Giver botten “kundeservice”-opførsel

### Kort forklaring:

“Fallback-systemet bestemmer **hvilket standardsvar** brugeren får, når botten _ikke_ må bruge indholdet.”

Under "Fallback systemet" har jeg også implmenteret et "safety layer"
![[Pasted image 20251128142320.png]]
Denne del af koden er et ’safety layer’.  
Hvis RAG ikke finder nogen relevante dokumenter, springer vi AI’en over og returnerer et sikkert, manuelt fallback-svar.  
Det forhindrer hallucinationer og gør oplevelsen mere stabil.  
Det hører derfor ikke under prompt-builderen eller rolle-instrukserne, men under systemets overordnede sikkerheds- og fallback-logik

## sikkerhedslag
Systemet bruger to sikkerhedslag før AI’en får lov at svare. Først klassificerer vi spørgsmålet (smalltalk, medicinsk, nonsense eller normal). Hvis det ikke er “normal”, stopper vi og giver et special-fallback. Hvis det er normalt, men RAG ikke finder dokumenter, giver vi et sikkert fallback-svar. Kun når begge tests er bestået, bygger vi prompten og sender spørgsmålet til AI’en.

Her gør funktionen det helt grundlæggende arbejde med at styre, hvordan der skal svares.  
Først bliver spørgsmålet vurderet for at se, om det er smalltalk, medicinsk, off-topic eller bare nonsens. Hvis det falder i en af de kategorier, går funktionen straks i fallback-mode og returnerer et sikkert, manuelt skrevet svar i stedet for at bruge AI. Derfor bliver OpenAI-delen helt sprunget over, og dokumenterne bliver heller ikke brugt.

Hvis spørgsmålet er normalt, fortsætter funktionen til næste trin, hvor systemet filtrerer dokumenterne efter relevans. Her bruges en threshold-værdi, som betyder at kun dokumenter med en lav afstand (altså god match) bliver beholdt. De rå FAISS-resultater bliver gemt i docs_before, og de filtrerede i docs_after. Hvis der ikke er nogen dokumenter tilbage efter filteret, vælges fallback-svaret igen, så brugeren får en venlig besked om at der ikke findes noget præcist match.

Kun hvis spørgsmålet er normalt _og_ der findes relevante dokumenter efter filteret, bliver der bygget en prompt og sendt et kald til OpenAI. Resultatet bliver derefter returneret som AI-svar.  
På den måde styrer funktionen om der skal faldes tilbage til et sikkert standardsvar, eller om AI’en skal bruges, og den sørger for at kun dokumenter med reel relevans bliver sendt videre.


## Rate-limit
Denne funktion står for selve kaldet til OpenAI-modellen, og den indeholder en lille indbygget beskyttelse mod rate-limit fejl. Først forsøger den at sende forespørgslen normalt. Hvis OpenAI svarer med en rate-limit fejl, betyder det, at der er sendt for mange kald på kort tid. I stedet for at lade systemet crashe, venter funktionen et øjeblik og prøver automatisk igen. Det gør integrationen mere stabil og sikrer, at brugeren stadig får et svar, selv når OpenAI er presset.


Video gennemgang af agenten.



# Hvorfor vi nu har en agent (og ikke længere en chatbot)

I de tidligere versioner af systemet fungerede løsningen som en klassisk chatbot. Modellen reagerede direkte på brugerens input, og svaret blev alene styret af rolle-prompting og kontekst. Systemet tog ingen selvstændige beslutninger – det genererede blot tekst baseret på det prompt, vi sendte. Det er kendetegnende for en almindelig chatbot: én pipeline, ingen valg, ingen kontrol.

I det sidste projekt ændrede vi arkitekturen fundamentalt. Vi indførte et beslutningslag, hvor systemet selv tager stilling til, hvordan et spørgsmål skal behandles. Spørgsmålet bliver først klassificeret (normal, smalltalk, medicinsk, off-topic eller nonsense). Denne vurdering styrer straks, om AI’en overhovedet må bruges. Hvis henvendelsen ikke er “normal”, stopper systemet automatisk den generative model og returnerer et sikkert fallback-svar. Dermed har systemet nu autonom logik, der beskytter mod hallucinationer, misforståelser og forkerte svar.

Dernæst kontrollerer systemet, om RAG-delen faktisk har fundet relevante dokumenter. Finder vi ingen dokumenter under en bestemt relevanstærskel, vælger systemet igen et fallback-svar og undlader helt at bruge OpenAI. Det betyder, at modellen ikke længere styrer outputtet alene; systemet vælger selv strategi og beslutter, om AI’en overhovedet må aktiveres.

Først når begge kontroller er bestået – korrekt kategori og reelt relevant kontekst – får agenten lov til at bygge en prompt og kalde OpenAI. Det er altså systemet, der styrer hvornår, hvordan og _om_ modellen må bruges. Den generative model er kun ét værktøj, der først aktiveres efter en række autonome valg.

Det er netop denne evne til at analysere input, vælge mellem flere mulige handlingsstrategier og udføre forskellige flows, der definerer en AI-agent. Systemet svarer ikke længere bare – det træffer beslutninger.