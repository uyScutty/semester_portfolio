# **Arkitektur i projektet**

Arkitekturen i projektet er bygget op omkring tre centrale valg:

1. **Clean Architecture** som lag- og afhængighedsstruktur
    
2. **Vertical Slice Architecture** som metode til at organisere funktionalitet
    
3. **DDD-principper** som grundlag for domænemodellering
    

Disse valg giver samlet set et system, der er opdelt, stabilt og nemt at udvide. Nedenfor gennemgår jeg, hvad hver af disse betyder i _mit_ projekt, og hvordan jeg konkret har implementeret dem.

---

# ⭐ **1. Clean Architecture – mit lag og mine afhængigheder**

For mig betyder Clean Architecture, at systemet er opdelt i lag, hvor domænet ligger i centrum, og alle afhængigheder går indad.  
Det vigtigste er:

- Domænelaget (Entities, Domain Events, Value Objects) kender ikke tekniske detaljer.
    
- Application-laget definerer use cases via Commands, Queries og Event Handlers.
    
- Infrastruktur implementerer interfaces fra Application — aldrig omvendt.
    
- UI (Blazor) taler kun med Application.
    

I praksis giver denne opdeling et system, hvor jeg kan ændre teknologi (database, API, UI) uden at ændre domænet.

### Diagram (rapportvenligt)

                     `Presentation (Blazor)                               ↓                Application (CQRS + Handlers)                               ↓                      Domain (Core Logic)                               ↑                     Infrastructure (EF, API)`

→ Dette er min kernearkitektur.

---

# ⭐ **2. Vertical Slice Architecture – sådan organiserer jeg funktioner**

Hvor Clean Architecture definerer _lag_, definerer Vertical Slice Architecture _funktionalitet_.  
For mig betyder Vertical Slice:

- Hver feature har sine egne mapper
    
- Commands, Queries, Handlers og Validation ligger samlet under én feature
    
- Man skal ikke lede rundt i projektet efter logik, fordi alt tilhørende ligger ét sted
    

Dette bruger jeg i **Application-laget**, hvor jeg har slices som:

- Membership
    
- Content
    
- Reviews
    
- Notifications
    

Hver slice indeholder sit eget:

- Command
    
- CommandHandler
    
- Query
    
- QueryHandler
    
- EventHandlers
    
- Interfaces
    

### Eksempel

`Application/  ├─ Membership/  │     ├─ Commands/  │     ├─ Queries/  │     └─ Handlers/  ├─ Content/  ├─ Reviews/`

Vertical Slice giver mig et modulært system, hvor én del kan udvikles eller ændres uden at påvirke de andre.

---

# ⭐ **3. Domain-Driven Design – hvad det er for mig og hvordan jeg bruger det**

Jeg har valgt at bruge DDD **som modelleringsmetode**, ikke som arkitektur.

I mit projekt betyder DDD:

- Jeg opdeler systemet i **domæner** som content, membership og reviews
    
- Jeg bruger **Entities** og **Value Objects** til at sikre tydelige modeller
    
- Jeg bruger **Domain Events** til at reagere på ændringer i domænet
    
- Jeg tænker i **bounded contexts**, selvom det ligger i én kodebase
    

Men — og det er vigtigt for mig — **DDD er ikke min arkitektur**.  
DDD hjælper mig med at forstå domænet og skabe rene modeller, men det er Clean Architecture og Vertical Slice, der strukturerer systemet.

Jeg bruger eksempelvis Domain Events når:

- et review bliver oprettet
    
- et content-indlæg bliver publiceret
    
- et medlemskab ændres
    

Event-handlers ligger i Application-laget, hvilket følger Clean Architectures “indadgående afhængigheder”.

---

# ⭐ **4. Hvordan de tre valg hænger sammen i projektet**

Den samlede arkitektur kan beskrives sådan:

- **Systemform:** Modulær monolith
    
- **Lagstruktur:** Clean Architecture (Domain → Application → Infrastructure → UI)
    
- **Feature-opdeling:** Vertical Slice Architecture
    
- **Domænemodellering:** DDD-principper (Entities, Value Objects, Domain Events)
    

### Diagram (for rapport – samlet model)

`┌───────────────────────────────────────────────────────┐ │                     Presentation                       │ │                      (Blazor UI)                      │ └─────────────────────────────↓──────────────────────────┘ ┌───────────────────────────────────────────────────────┐ │                Application (Vertical Slices)           │ │  ├─ Membership: Commands, Queries, Handlers            │ │  ├─ Content: Commands, Queries, Handlers               │ │  ├─ Reviews: Commands, Queries, Handlers               │ │  └─ Notifications: Event Handlers                      │ └─────────────────────────────↓──────────────────────────┘ ┌───────────────────────────────────────────────────────┐ │                      Domain                            │ │   Entities, Value Objects, Domain Events, Services     │ └─────────────────────────────↑──────────────────────────┘ ┌───────────────────────────────────────────────────────┐ │                    Infrastructure                      │ │  EF Core, Repositories, External API Services, Logging │ └───────────────────────────────────────────────────────┘`

---

# ⭐ **Kort afslutning**

Arkitekturen i projektet er derfor en kombination af:

- **Clean Architecture**, som definerer lag og afhængigheder
    
- **Vertical Slice Architecture**, som organiserer funktionalitet
    
- **DDD-principper**, som strukturerer domænet
    

Denne kombination har givet et system, der er let at udvide, nemt at navigere i, og som holder forretningslogik fri af tekniske detaljer — præcis som god arkitektur skal.


![[Pasted image 20251129135424.png]]“Her ses mine 4 lag. Domænet er i centrum. Application består af Vertical Slices. Infrastruktur er tekniske detaljer. Blazor ligger i Presentation.”


“Domænet indeholder kerneforretningsmodeller og events. Der er ingen afhængigheder til database eller tekniske detaljer.”

![[Pasted image 20251129135535.png]]
![[Pasted image 20251129135616.png]]
I domænelaget er enums, domain events og entities samlet.


Her er apllications laget.
![[Pasted image 20251129140022.png]]
![[Pasted image 20251129140606.png]]

**Abstractions**  
Her ligger alle _interfaces_, som Application har brug for — men som implementeres i Infrastructure.  
Det adskiller logik fra teknik.

**Contracts**  
Kontrakter/aftaler mellem Application og eksterne systemer. Ingen logik — kun definitioner.

**Gateways**  
Interfaces for adgang til eksterne systemer eller tekniske ressourcer (AI, Cache, Email, Identity).

**Hver fil kort:**

- **IAIChatGateway** – interface for AI-chat (Application kender ikke den konkrete AI-teknologi).
    
- **ICache** – abstraktion for caching (Memory/Redis bestemmes af Infrastructure).
    
- **IEmailNotifier** – abstraktion for e-mail-afsendelse.
    
- **IDomainEventDispatcher** – interface til at dispatchere domain events.
    
- **IIdentityService** – abstraktion for bruger/identity-opslag.
    
- **IUserLookupService** – abstraktion for at hente brugerinfo uden at kende Identity-implementeringen.
    
- **Dtos/** – små modeller der bruges som datakontrakter udadtil.
    

**Kort konklusion:**

> _Application definerer hvad den skal bruge gennem interfaces. Infrastructure implementerer det. Det holder arkitekturen ren og afhængighederne indad)._



![[Pasted image 20251129140218.png]]“Hver feature er samlet ét sted — command, query, handler, interfaces. Det er Vertical Slice Architecture og giver lav coupling.”


# Infrastrukturmappen – kort forklaring (rapportvenlig)

Din _Infrastructure/_-mappe er opbygget helt korrekt efter Clean Architecture.

Her ligger **alle konkrete tekniske implementeringer**, mens Application kun kender interfaces.

Her er en meget kort forklaring af dine undermapper:

### **Dependencies/**

- Her samler du dine extension methods eller registreringer, der binder Application-interfaces til Infrastructure-implementeringer.
    
- Bruges ofte til `AddInfrastructureServices()` i Program.cs.
    

### **Events/**

- Indeholder håndtering af domæneevents **fra Infrastructure's perspektiv**, f.eks. persistence-logik eller integrationer der skal reagere på events.
    

### **Gateways/**

- Konkrete implementationer af dine _Abstractions → Contracts → Gateways_.
    
- Eksempel: `AIChatGateway`, `EmailNotifier`, `Cache`, osv.
    

### **Identity/**

- Implementeringer relateret til autentificering/autorisation.
    
- Typisk adaptere til ASP.NET Identity eller custom IdentityService.
    

### **Persistence/**

- EF Core DbContext
    
- Repositories
    
- Migrations
    
- Database-konfiguration
    
- Alle data-relaterede tekniske detaljer
    

### **appConfigurationOptions.cs**

- Konfigurationsklasser til binding af appsettings.
    

### **ServiceCollectionExtensions.cs**

- Extension methods som:


![[Pasted image 20251130125913.png]]

“Her kan man se, at Application kun definerer IPostRepository som kontrakt.  
Infrastructure implementerer den konkrete PostRepository, som bruger EF Core og databasen.  
Det viser Clean Architectures dependency rule: Application afhænger af abstraktioner, ikke konkrete tekniske implementeringer.”


#### Web (Presentation-laget)
“UI-laget er implementeret i Blazor. Det indeholder kun Pages og Components til præsentation.  
UI’et kalder Application-laget via Commands/Queries og indeholder ingen forretningslogik.  
Dette følger Clean Architecture’s princip om, at UI ligger i yderste lag og afhænger af Application.”