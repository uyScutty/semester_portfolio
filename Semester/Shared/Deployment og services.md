### ⭐ 5) Infrastruktur er udskiftelig

- database
    
- email
    
- AI
    
- busser
    
- API'er
    

Kan ændres uden at påvirke Domain eller Application.

### ⭐ 6) Mulighed for fremtidig opsplitning til microservices

Modulær monolith + Clean Architecture = naturlig vej til microservices senere.

### ⭐ 7) AI-delen er teknisk isoleret

Så AI aldrig kan påvirke forretningsregler eller database direkte.

---

# ⭐ **Samlet konklusion – den korte, stærke sætning**

> Vi startede med en simpel monolith og layered architecture, men efterhånden som projektets domæne voksede og fik dybere forretningslogik, domain events, AI-moduler og flere bounded contexts, blev layered for begrænsende.
> 
> Derfor skiftede vi til en modulær monolith med Clean Architecture og Vertical Slice Pattern, fordi det giver rene afhængigheder, klar domain-adskillelse, bedre testbarhed, isoleret AI-integration og langt bedre skalerbarhed og vedligehold på lang sigt.


Udgangspunktet – vi startede simpelt

Monlith med layered architecture var første indskydelse.

I begyndelsen havde projektet bare:

- brugere
- simple blogposts
- et lille dashboard

Man kunne i princippet have lavet alt i _ét stort projekt_.  
Men meget hurtigt havde du flere krav:

- anmeldelser (reviews)
- medlemskaber
- AI-chat
- profiler
- paywalled health-tips

→ Systemet voksede i funktionalitet.

Dette gjorde at vi gik fra monolith med layered N-tier arkitektur gik til clean architecture lagdeling.
# Vi valgte Clean Architecture for at holde kerne-logikken ren

Vi ønskede:

- klare grænser mellem lag
- minimal coupling
- testbarhed
- adskillelse mellem _domæne_ og _infrastruktur_

Derfor endte vi med:

- **Domain** (entities, regler, events)
    
- **Application** (use cases, interfaces)
    
- **Infrastructure** (DB, repos, email, gateways)
    
- **Web** (UI, vertical slices)
    

Clean Architecture var naturligt her, fordi det:

 holder domænet rent  
 sikrer klare grænser  
 gør det let at vokse senere  
 er moderne og forstået af virksomheder

---

## 3️⃣ Behavior voksede – Vertical Slice Architecture var nødvendig

Blazor-delen var begyndt at få:

- Create/Edit/Delete
    
- moderationsflows
    
- paywall-checks
    
- sub-features (video review, image review, text review)
    
- chat integration
    

Det blev for tungt at lægge alt i standard MVC- eller mappe-opdeling.  
Derfor valgte vi **Vertical Slice Architecture** for Web-laget:

- `/Posts/Create`
    
- `/Reviews/Owner/CreateImage`
    
- `/Membership/Activate`
    
- osv.
    

Hver "feature" fik sin egen lille mini-mappe med:

- sin egen component
    
- sin egen handler
    
- sin egen route
    
- sin egen validation
    

→ Dette er modulær monolit i praksis.

---

## 4️⃣ Skulle det være microservices? → Nej, og det blev et bevidst valg

Vi diskuterede microservices flere gange:

**FOR microservices:**

- isoleret skalering
    
- teams kan arbejde uafhængigt
    
- god til meget store systemer
    

**IMOD microservices:**

- du skal have _orchestration_
    
- distributed tracing
    
- logging på tværs
    
- event-bus
    
- for meget kompleksitet til dit behov
    

Dit projekt havde **ét team (dig selv)** og ét domæne.

→ Microservices ville være overkill og gøre projektet langsommere og mere komplekst.

---

## 5️⃣ AI-delen passede ikke i .NET

Da du begyndte at arbejde med AI-chat, embeddings og vektor-databaser, opstod spørgsmålet:

**Skal AI-delen bygges i samme .NET-projekt?**

➡️ Svaret blev NEJ, fordi:

- Python har _langt bedre bibliotek-støtte_ (Chroma, huggingface, LLM-agenter, etc.)
    
- Det kører bedre isoleret
    
- Det kan skaleres separat
    
- Det kan køre lokalt eller i Docker uden at påvirke appen
    

Derfor blev AI-delen sin egen **eksterne service**.

→ Dette er ikke microservices, men **external service integration**.

---

## 6️⃣ Derfor endte vi med en **modulær monolit**

Summen af alle beslutninger:

|Punkt|Consequence|
|---|---|
|Du vil gerne holde intern kompleksitet nede|Monolit i kernen|
|Du vil gerne holde features isoleret|Vertical Slices|
|Du vil følge Clean Architecture som skole + industristandard|Domain/Application/Infrastructure|
|Du vil have AI i Python|Ekstern service|
|Du vil have noget, der kan dockeriseres|Moduler + services i compose|
|Du vil skalere AI uden at skalere .NET|Python separat|

Den bedste kombination blev:

### 🟩 **Modulær monolit (Clean Architecture + Vertical Slices)**

### 🟦 **Ekstern AI-service i Python**

### 🟧 **Postgres database som fælles persistence-lag**

Dette er moderne arkitektur.  
Det er præcis det, mange startups bruger i dag, inkl. dem der senere skalerer til microservices.

---
# Hvorfor det er en modulær monolit

### ✔ 1. _Alt domænet er samlet i ét system_

Du har:

- **Domain**
    
- **Application**
    
- **Infrastructure**
    
- **Web (Blazor Server)**
    

→ Alt er i én codebase og én samlet deploybar enhed.

### ✔ 2. _Men det er opdelt i moduler_

I Web-laget har du Vertical Slice feature-mapper:

`/Posts /Reviews /Profiles /Membership /Chat`

Hver “feature” fungerer som et lille modul med sine egne handlers, DTO'er, components, repo-calls osv.

Det er definitionen på modulær monolit.

### ✔ 3. _Ingen services er selvstændigt deployet_

Du deployer systemet som _ét system_, ikke som 10 mikroservices.

---

# Hvorfor det **ikke** er en mikroservice-arkitektur

- Ingen selvstændige bounded context services.
    
- Ingen event-bus mellem services.
    
- Ingen independent deployment.
    
- Database deles i ét EF Core AppDbContext.
    

Du har dog en _lille_ service:

### ✔ Python AI-agenten → **en ekstern service**, ikke en microservice-arkitektur

Den kører som:

- et selvstændigt API / container
    
- men uden eget bounded domain  
    → mere som et **external integration service**, ikke en microservice.
    

---

# Hvorfor det **heller ikke** er en klassisk monolith

En klassisk monolith = ét projekt, typisk lagdelt, uden modulopdeling.

Din arkitektur:

- Clean Architecture (Domain, Application, Infrastructure)
    
- Vertical Slices i Web
    
- AI-gateway ud i Python-container  
    → det er _modulært opbygget_, ikke ét stort sammenklumpet projekt.
    

---

# Hvordan projektet bliver deployet

Den deployment-model vi snakkede mest om:

### **.NET (hele Christians Side) → som én container**

- Domain + Application + Infrastructure + Web (Blazor Server)
    
- EF Core database (Postgres i egen container)
    

### **Python AI-service → egen container**

- Sammen med egen vektorstore (Chroma eller Postgres embeddings)
    

### → Docker Compose orkestrerer dem:

`app (Blazor Server) db (Postgres) ai-service (Python) ai-vector-db (Postgres/Chroma)`

Det er klassisk **modulær monolit + eksterne services**.