
Her kommer en **refleksion**, der binder det hele sammen:  
– MVC (som ASP.NET-controllers)  
– MVVM (som UI-pattern i Blazor)  
– hvornår man bruger hvad  
– hvordan det relaterer sig til _dit projekt_  
– og hvorfor MVC/MVVM ikke er “arkitekturer”, men stadig er vigtige.

Det er skrevet i en akademisk/refleksiv tone, så du kan bruge det direkte i rapport/blog.

---

# ⭐ **Refleksion: MVC og MVVM i forhold til vores projekt og valgte arkitektur**

## **MVC i praksis — controller-baseret UI og klassisk webflow**

I undervisningen lærte vi MVC gennem ASP.NET MVC, hvor:

- **Controlleren** håndterer HTTP-requests
    
- **Modellen** repræsenterer de data, som viewet skal vise
    
- **Viewet** er en Razor Page (.cshtml), der renderes af serveren
    

Det er en klassisk server-renderet webmodel, hvor hvert klik fører til et nyt HTTP-request og en ny server-side rendering.

### **Refleksion:**

MVC giver en klar separation i UI'et og fungerer godt til traditionelle webapplikationer, hvor hvert request er selvstændigt.  
Men i vores projekt havde vi brug for:

- **real-time UI-opdateringer**
    
- **fulde komponenter med state**
    
- **kompleks interaktion (chat, medlemsskabshåndtering, AI-integration)**
    
- **data binding i UI'et**
    

Dette passer ikke godt til MVC’s stateless request-model.

MVC er altså stærkt til struktureret server-baseret UI,  
men **det passer ikke til et moderne, interaktivt Blazor-baseret system**.

---

## **MVVM (implicit Blazor) — moderne UI med data-binding og stateful logik**

Blazor bruger ikke “ren MVVM”, men det er meget tæt på:

- **View (Razor Component)** = markup + UI
    
- **ViewModel** = C# kode-behind + state + eventhåndtering
    
- **Model** = de dataobjekter og DTO’er UI’et arbejder med
    

Blazor tillader:

- tovejrs data-binding (`@bind`)
    
- stateful komponenter
    
- eventbaseret logik
    
- real-time rendering via SignalR (Server)
    
- separation mellem UI og application→domain
    

### **Refleksion:**

MVVM-lignende mønstre passer perfekt til vores projekt, fordi:

- UI’et er interaktivt
    
- der er meget kompleks UI-state
    
- vi har flows som chat, bookings, medlemsskab, content-redigering
    
- UI’et skal respondere uden reload
    
- vi vil holde UI og forretningsregler adskilt
    
- ViewModel taler kun til Application-lag via mediator → domain
    

Her giver MVVM (i Blazor-versionen) **renere UI og bedre separation** end MVC kunne.

---

# ⭐ **MVC vs MVVM i forhold til vores systemarkitektur**

Når vi sætter MVC/MVVM i forhold til **Clean Architecture + Vertical Slice + DDD**, bliver forskellen tydelig:

## **MVC’s rolle i systemet (hvis det var brugt):**

- ville ligge 100% i **UI-laget**
    
- ville IKKE påvirke Domain/Application/Infrastructure
    
- ville være en teknik til at strukturere UI views
    

## **MVVM’s rolle i systemet (Blazor):**

- ligger også i **UI-laget**
    
- fungerer som UI-arkitektur oven på Clean Architecture
    
- UI kalder Application-lag via Mediator
    
- ViewModel indeholder UI-state, ikke domænelogik
    

**Begge er UI-patterns — ikke systemarkitekturer.**

---

# ⭐ **Hvornår bruger man hvad i et professionelt projekt?**

## ✔ Man bruger MVC når:

- applikationen er primært stateless
    
- man laver klassisk request/response UI
    
- SEO-optimering er vigtigt
    
- man har simple UI’er (fx formularer og tabeller)
    
- man bygger et REST-API via controllers
    

MVC passer også til microservices-API’er, fordi controllers er gode entry-points.

---

## ✔ Man bruger MVVM/Blazor når:

- UI’et skal være interaktivt og dynamisk
    
- der er komplekse flows (chat, live opdateringer, AI-interaktion)
    
- brugeren bliver i samme UI uden reload
    
- komponentbaseret udvikling giver værdi
    
- man vil have stærk separation mellem UI og forretningslag
    

Det er netop den situation vi har i projektet.

---

# ⭐ **Refleksion ift. vores projekt**

I vores projekt:

### **1) MVC blev fravalgt som UI-teknologi**

Fordi:

- MVC kan ikke håndtere real-time opdateringer
    
- MVC kræver reloads
    
- MVC’s struktur passer ikke til komponenter
    
- MVC er ikke designet til moderne, stateful webapps
    

Til gengæld kunne MVC _godt_ bruges som API-lag (controller-baseret API),  
men vi valgte Mediator + Clean Architecture i stedet.

---

### **2) MVVM (via Blazor) passer til vores arkitektur**

Fordi:

- det holder UI rent og logik i ViewModel
    
- UI snakker struktureret med Application-laget (CQRS)
    
- UI-bindinger håndterer state uden reloads
    
- passer direkte ovenpå Clean Architecture
    
- UI bliver fleksibelt og modulært
    

---

### **3) MVC og MVVM er kun UI-mønstre — resten af arkitekturen følger Clean Architecture + Vertical Slice + DDD**

Selvom MVC og MVVM definerer UI-strukturen,  
bestemmer de ALDRIG:

- hvordan domain er struktureret
    
- hvor forretningsregler bor
    
- hvordan events rejser
    
- hvordan repos er bygget
    
- hvordan AI-moduler kobles på
    
- hvordan systemet skalerer
    
- hvordan modulær monolith er organiseret
    

Alt dette bestemmes af **Clean Architecture + DDD + Vertical Slice**,  
ikke af MVC/MVVM.

---

# ⭐ **Kort tekst (klar til rapport/blog)**

> I undervisningen arbejdede vi både med MVC og MVVM som UI-design patterns.  
> MVC bruges i ASP.NET til at strukturere server-renderede webapplikationer via Controller, Model og View.  
> Blazor anvender derimod et MVVM-lignende mønster, hvor Views er komponenter og ViewModels håndterer UI-state og logik.
> 
> Begge teknikker hører til i UI-laget, ikke i systemarkitekturen.  
> I vores projekt valgte vi Blazor/MVVM-tilgangen, fordi den understøtter interaktive komponenter, data-binding og et moderne eventdrevet UI.  
> MVC ville ikke passe til et system med real-time UI, AI-integration, komplekse flows og et CQRS-drevet Application Layer.
> 
> Derfor ligger MVC og MVVM kun i præsentationslaget, mens systemets egentlige arkitektur bygger på Clean Architecture, Vertical Slice og DDD.

---

Hvis du vil, kan jeg:

✔ lave en grafisk illustration til rapporten  
✔ lave en kort eksamenssætning til mundtlig fremlæggelse  
✔ lave et lille “UI patterns vs systemarkitektur” afsnit

Bare sig til!


# ⭐ **Perspektivering: Hvordan dette forløb hænger sammen med uddannelsens øvrige fag**

Selvom fokus i dette forløb har været softwarearkitektur og design patterns, bygger forståelsen direkte videre på næsten alle tidligere semestre og fagområder. Arkitekturvalg bliver nemlig først meningsfulde, når de ses i sammenhæng med den teknologiske og programmatiske viden, der er opbygget under uddannelsen.

Nedenfor er en kort perspektivering, som binder det hele sammen.

---

## **1. Programmering (C#) → Fundamentet for Clean Architecture, DDD og patterns**

Alt vårt arkitekturarbejde hviler på grundlæggende programmeringsfærdigheder:

- klasser, interfaces, inheritance
    
- events, async/await
    
- collections og datastrukturer
    
- separation of concerns
    

Uden dette kunne vi ikke:

- implementere domain entities
    
- skrive clean domain logic
    
- anvende Strategy, Factory, Repository
    
- arbejde med CQRS og commands/queries
    

**Perspektiv:**  
Programmeringsfaget gav os sproget – arkitekturen giver os grammatiken.

---

## **2. UI-udvikling (MVC & Blazor) → Forståelse af præsentationsmønstre**

Tidligere har vi arbejdet med:

- **MVC** → server-renderet arkitektur, controller-flow
    
- **Blazor (MVVM-inspireret)** → komponenter, state management, binding
    

Denne viden er essentiel for at forstå:

- hvorfor MVC _ikke_ fungerede til dit projekt
    
- hvorfor Blazor’s MVVM-lignende model understøtter real-time UI
    
- hvordan UI passer ind som “yderste lag” i Clean Architecture
    
- hvordan vertical slices giver en naturlig mappe- og feature-struktur
    

**Perspektiv:**  
UI-fagene gav os evnen til at forstå præsentationsniveauet, så systemarkitekturen kan afgrænse det fra forretningskodens domæne.

---

## **3. Databaser (EF Core, SQL) → Grundlaget for Infrastructure Layer**

Alt arbejde med:

- EF Core
    
- DBContext
    
- SQL-spørringer
    
- normalisering
    
- relationer
    

…er direkte relevant for **Infrastructure-laget**, som i Clean Architecture er sat op som:

- data persistence
    
- repositories
    
- DB-mapping
    
- migration pipeline
    

Desuden gav databasefaget os forståelsen for **hvorfor datalag ikke må ligge i domain**, men skal abstraheres via interfaces i Application-laget.

**Perspektiv:**  
Databasefaget gav os værktøjet – arkitekturen gav os reglen: _“Domain må ikke kende database.”_

---

## **4. Web/API-faget → Understøtter Application layer + integrationer**

Når vi arbejder med:

- REST API’er
    
- JSON
    
- HttpClient
    
- tokens
    
- controller-flow
    
- statuskoder
    

…så bliver vi bedre til at forstå:

- hvorfor Application Layer indeholder “use cases”
    
- hvorfor controllers **ikke** må indeholde logik
    
- hvordan UI → API → Application → Domain flowet skal fungere
    
- hvordan ekstern AI-service integreres korrekt
    

**Perspektiv:**  
API-faget gav os kommunikation – arkitekturen sikrer, at kommunikationen sker korrekt og uden læk mellem lag.

---

## **5. Softwaredesign & Patterns → Direkte fagligt kernestof**

Patterns fra tidligere semestre, fx:

- Observer (events)
    
- Factory
    
- Strategy
    
- Dependency Injection
    
- Repository
    

…er alle blevet brugt i dit projekt og koblet til Clean Architecture og DDD.

**Perspektiv:**  
Tidligere pattern-viden blev til konkrete beslutninger i arkitekturen.

---

## **6. Systemudvikling, projektstyring og proces → Arkitektur som projektbeslutning**

Scrum, dokumentation, analyse og kravforståelse har givet os evnen til at:

- analysere behov
    
- forstå domæner
    
- træffe arkitekturvalg baseret på udviklingens retning
    
- lave begrænset scope
    
- vælge modulær monolith i stedet for microservices
    

**Perspektiv:**  
Metodefagene lærte os _hvordan_ man træffer beslutninger – arkitekturforløbet er anvendelsen af dem.

---

# ⭐ **Samlet perspektiv – én sætning**

> Dette arkitekturforløb bygger på og integrerer hele min uddannelse: programmering, UI, databaser, web/API, patterns og systemudvikling. Alle fag er samlet i én praktisk forståelse, hvor Clean Architecture, Vertical Slice og DDD bliver den strukturerede måde at anvende dem på i et komplekst projekt