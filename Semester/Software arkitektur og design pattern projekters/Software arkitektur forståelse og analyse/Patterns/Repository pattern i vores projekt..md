# Repository Pattern, Interfaces og Infrastructure som Plugin-model

Dette indlæg forklarer tre tæt forbundne dele af dit projekts arkitektur:

- **Repository Pattern (DDD-versionen)**
- **Interface-abstraktion (Application → Infrastructure)**
- **Infrastructure som “plugin-lag”**

De tre ting hænger uløseligt sammen og er fundamentale i vores Clean Architecture + DDD-opsætning.

De skaber:

- løs kobling
- testbarhed
- ren domænelogik
- udskiftelig database
- modulær arkitektur
- klar afhængighedsretning
- integration med CQRS og Vertical Slice

---

# 1) Repository Pattern i DDD


### I DDD betyder Repository:

> “En samling af aggregates, som du kan hente og gemme, som var det en samling i hukommelsen.”

Et repository repræsenterer domænets syn på persistence — ikke databasens.

---

##  I vores projekt betyder det:

###  Interface i Application-laget

(Hvorfor? Fordi Application bestemmer, _hvad_ der skal kunne persisteres.)

Eksempler:

- `IContentRepository`
- `IReviewRepository`
- `IMembershipRepository`
- `IUserRepository`


###  Implementation i Infrastructure

(Hvorfor? Fordi Infrastructure bestemmer _hvordan_ det persisteres.)

Eksempler:

- `EfContentRepository`
- `EfReviewRepository`
- `EfMembershipRepository`
- `EfUserRepository`

---

# 2) Hvorfor Repository Pattern er nødvendigt i vores arkitektur

###  1. Domain skal ikke vide noget om EF Core

Entities må ikke kalde DB.

###  2. Application skal ikke bruge teknologier direkte

Det må kun kommunikere via interfaces.

###  3. Infrastructure skal være udskifteligt

EF Core kan erstattes af Dapper, SQL, Cosmos DB eller en fil — uden at ændre Application.

###  4. Domain kan testes uden database

Vi kan mocke alle repos i tests.

###  5. Vertical Slice handler-pipelinen bliver ren

Hver handler:

1. Modtager command/query
2. Kalder interface
3. Manipulerer domain
4. Kalder interface for at gemme

Alt er rent og let at læse og teste.

# 3) Hvad et Repository _ikke_ er i DDD

Mange laver fejl her.

Et repository er **ikke**:

- en generel CRUD-service
- en databasetilgangsklasse
- et sted med forretningslogik
- en “helper-klasse”

Et repository er en **abstraktion af et domæne-aggregat**.

Eksempel:

> “Content-aggregatet består af Content + Comments + Rules. Repositoriet håndterer dette samlede objekt.”

Det betyder:

- kun aggregate roots eksponeres
- konsistensregler bevares
- domain forbliver i kontrol
- EF Core modeller holder sig i Infrastructure



# 4) Interface-abstraktion: Application bestemmer _hvad_, Infrastructure bestemmer _hvordan_

Den vigtigste regel i Clean Architecture:

> “Afhængigheder går indad.”

Det betyder:

- Application må ikke afhænge af EF, AI-klienter, email osv.
- Domain må ikke kende noget udenfor sig selv
- Infrastructure må gerne afhænge af Domain/Application

### Derfor ligger interfaces i Application.

Flow:

`Application     ↓ Interface (IContentRepository)    ↓ Infrastructure    ↓ EfContentRepository implementerer interfacet`

---

# 5) Infrastructure som plugin-lag

Dette er essensen af Clean Architecture:

Infrastructure skal kunne **skiftes ud** uden at ændre domænet eller use cases.

I dette projekt betyder det, at alle disse ting kan udskiftes:
- database (Postgres → SQL → InMemory)
- AI-service (Python → andre modeller)
- Email service
- Logging service
- Notifikationsservice
- Identity (kun adapter, domain er ikke afhængig)


# 6) Hvordan Repository, Interfaces og Plugin-model bruges i vores projekt

### I Domain:

Ingen database — kun Entities, Value Objects og Events.

### I Application:

- Commands/Queries
- Use Case Handlers
- Interfaces

### I Infrastructure:

- konkrete repos → `EfContentRepository`
- databasekode (EF Core)
- AI-adapter
- Emailservice
- Logging
- Identity-adapter

### I Web (Vertical Slices):

En handler kalder kun interfaces.  
UI har ingen ide om _hvordan_ data ender i DB.


# 7) Hvorfor denne model er bedre end alternativer (i vores projekt)

## ▶ Alternativ 1: Direkte EF Core brug i Application

→ hurtigere at bygge, men umuligt at teste  
→ bryder Clean Architecture  
→ domain bliver framework-afhængigt

## ▶ Alternativ 2: Et stort “DataService” eller “ContentService”

→ service-grød  
→ svært at forstå, teste og udvide  
→ alle use cases blandet sammen

## ▶ Alternativ 3: Infrastructure i Domain

→ ren anti-pattern  
→ domain er ikke længere domæne  
→ alt bliver koblet sammen

## ▶ Alternativ 4: Microservices repos / polyglot persistence

→ alt for tungt for dit projekt  
→ kræver event-bus, saga, orchestrator  
→ 3–4 gange kompleksiteten

**Valget vi tog er langt det mest balancerede ift. arkitektur & kompleksitet.**
