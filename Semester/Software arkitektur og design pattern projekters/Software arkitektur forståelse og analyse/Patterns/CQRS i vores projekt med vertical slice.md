# **CQRS + Vertical Slice Architecture (og hvorfor det passer perfekt til projektet

CQRS og Vertical Slice Architecture hænger tæt sammen.  
Faktisk giver CQRS først _mening_ i praksis, når man bruger vertical slice eller eventuelle variationer af “feature-based architecture”.

Vertical Slice er UI + Application struktur.  
CQRS er handler-mønstret i Application-laget.

Her forklarer vi:

- hvad CQRS er
- hvordan det hænger sammen med vertical slice
- hvordan vi bruger det i projektet
- hvad vi opnår
- hvad vi undgår
- hvad alternativerne er

---

# 1) Grundidéen i CQRS

CQRS betyder:

> **Command Query Responsibility Segregation**  
> → Adskil “read” og “write”.

Det betyder:

- **Command = noget der ændrer tilstand**  
    Create, update osv..

- **Query = noget der læser data**  
    Get

I vores projekt:

- **Vertical Slice → én mappe per feature**
- **CQRS → én handler per operation**



# 2) Hvorfor CQRS passer perfekt til Vertical Slice

Vertical Slice siger:

- hver feature er sin egen “slice”
- UI + Command/Query + Handler + Validator + Repo + Domain-logik
- alt ligger samlet ét sted → høj cohesion

CQRS siger:

- “hver operation skal være sin egen handler”


**Vertical Slice bestemmer strukturen.  
CQRS bestemmer handler-metoden.**

Det giver:

- ingen store services
- ingen blanding af use cases
- ingen kæmpe “god class” eller service klasse.
- rene flows

# 3) Hvorfor CQRS blev nødvendigt i _dit_ projekt

Use cases var flere end først antaget og nogle af domænerne har mange use cases 

Hvis alt dette lå i én “ContentService”:

- mange linjer af kode
- afhængigheder på kryds og tværs
- ingen forståelig arkitektur

CQRS løser dette:

Det er simpelt, testbart, og Clean Architecture-kompatibelt.

---

# 4) Hvordan CQRS bruges i projektet

Kontaktformularen i projektet er bygget efter CQRS-princippet, hvor **kommandoer står for ændringer**, og **queries står for læsning**.  
Når en bruger sender en kontaktbesked, håndteres processen som en _Command_, der udfører en række opgaver i en klar og adskilt sekvens.

### 1. **Commanden skabes i UI-laget**

Når en bruger indsender kontaktformularen, skabes en kommando, som repræsenterer intentionen:  
“Send denne kontaktbesked”.  
UI'et sender ikke domæneobjekter videre – kun de rene værdier, der skal bruges til at udføre handlingen.

### 2. **Commanden modtages i Application-laget**

I stedet for at UI kalder en service direkte, opfanges kommandoen af en handler i Application-laget.  
Dette sikrer, at al logik for at _ændre systemets tilstand_ ligger ét sted, isoleret i sin egen vertical slice.  
Handleren er ansvarlig for forretningsflowet, ikke UI’et.

### 3. **Domæneobjektet oprettes**

I handleren skabes der et ContactMessage-domæneobjekt.  
Dette objekt indeholder al den nødvendige information og er ansvarlig for at håndhæve domænets regler, fx at nye beskeder automatisk får status som “ny”.

### 4. **Persistens udføres gennem et repository**

Application-laget gemmer domæneobjektet via et repository-interface.  
Dette betyder, at Application-laget ikke ved noget om databasen, EF Core, SQL eller noget andet teknisk — kun at objektet skal gemmes.  
CQRS sikrer her en ren adskillelse mellem intentionen og implementeringen.

### 5. **Sideeffekter håndteres efter persistens**

Efter beskeden er gemt, udfører handleren de nødvendige sideeffekter — i dette tilfælde at sende en e-mail til ejeren af siden.  
Denne opgave håndteres via en e-mail-notifikationsservice, som er abstraheret bag et interface.  
CQRS sikrer dermed, at kommandoens ansvar er tydeligt:

- først ændres systemets tilstand
- derefter udføres nødvendige opfølgende handlinger

### 6. **Domænelaget forbliver rent**

ContactMessage-domæneobjektet har ingen afhængighed til applikationslogik, databaser eller notifikationer.  
Det indeholder kun data og regler.  
Dette er i tråd med både CQRS og Clean Architecture, hvor domænet skal være helt teknologiuafhængigt.

### 7. **Ingen læselogik blandes ind**

De beskeder, der bliver gemt, hentes senere via rene Query-operationer.  
Disse ligger i deres egen slice, adskilt fra Commands.  
CQRS-princippet sikrer dermed, at  
"send besked"  
og  
"hent beskeder"  
er to fuldstændigt adskilte flows.

#  Hvad vi opnår i projektet ved at bruge CQRS

*  1. Ren separering af logik

Read- og write-operationer blandes ikke sammen.

*  2. Små, klare, testbare klasser

En handler med 15–40 linjer i stedet for én service med 500.

*  3. Bedre domain-model

Hver handler styrer ét use case → klarere domæneregler.

* 4. Passer perfekt med domain events

Commands = raise events  
Queries = ingen events  
Det giver ren forretningslogik.

* 5. Passer perfekt med Clean Architecture

UI → Application → Domain → Infrastructure er klart opdelt.

6. Passer perfekt med modulær monolith

Hver bounded context får sine egne commands/queries.

#  Konklusion

> CQRS og Vertical Slice Architecture fungerer som to sider af samme mønt i projektet.  
> Vertical Slice styrer strukturen, CQRS styrer logikken.  
> Resultatet er rene use cases, løs kobling, høj testbarhed og en langt mere vedligeholdelig modulær monolith.