
For at genopfriske læringen omkring observer pattern fra tidligere semestre og se hvordan vi kan bruge det i vores projekt, så har jeg valgt at lave et lille projekt og se hvordan det kan bruges i en situation der passer til vores projekt i forhold til at observere ved oprettelse af ny content via post hvor der så bliver lyttet samt reageret på eventet, hvilket resulterer i udsendelsen af en email.

#  **Observer Pattern 

### Idé:

Et objekt (subject) holder styr på flere observers.  
Når subject ændrer sig, notificerer den alle observers direkte.

### Karakteristika:

- direkte reference mellem subject og observer
- tæt kobling
- objekter kender ofte hinanden
- typisk synkront
- er bedst i UI og simple apps
- fungerer som “hvem lytter på mig?”-model


#  **Domain Events (DDD)**

### Idé:

Domænet udsender en hændelse — men ved _ikke_ hvem der reagerer på den.

### Karakteristika:

- **ingen references** mellem domain og event handlers
- **lav coupling**
- **rent domæne**
- hændelser beskriver forretningssituationer (ikke UI-tilstande)
- Application-laget håndterer eventet
- Infrastructure kan håndtere sideeffekter

### Observer Pattern


Her ses opbygningen af mit observer spikeprojekt.
![[Pasted image 20251121115652.png]]



### **1) Subject → ContentPublisher**

Den der udsender beskeder / hændelser.  
ContentPublisher implementerer **ISubject**, som er kontrakten for et subject.  
Her kan observers blive **tilmeldt (Attach)** eller **frameldt (Detach)**.  
Når noget sker, kalder subject **Notify**, som sender en besked til alle observers.

![[Pasted image 20251121121805.png]]

### **2) Observers → EmailSubscriber / PushSubscriber**

Modtagere af beskeder.  
De implementerer **IObserver**, som er kontrakten for hvordan observers reagerer.  
Når de modtager `Update(message)`, håndterer de hændelsen på hver deres måde.


![[Pasted image 20251121123919.png]]
### **3) Interfaces → ISubject og IObserver**

Kontrakten for hvordan subject og observers skal opføre sig.  
ISubject styrer notifikationerne.  
IObserver styrer hvordan subscribers reagerer.
![[Pasted image 20251121122737.png]]

ISubject er kontrakten for publishers (ContentPublisher) og udsendelsen af beskeder og hændelser
![[Pasted image 20251121123007.png]]


### **Flowet i min kode**

1. Observers tilmelder sig via `Attach()`
2. ContentPublisher publicerer nyt content og kalder `Notify()`
3. Notify kalder `Update(message)` på alle observers
4. Email og Push reagerer hver for sig


**Pointen med Observer Pattern:**  
Det giver løs kobling: Subject kender ikke implementeringen af observers, kun interfacet.  
Det gør systemet fleksibelt og udvideligt.



### Domain event

Her har jeg afprøvet et domain event implementering for at sammenligne med Observer pattern.
Jeg har i dette Spike Project ikke brugt MediatR til at styre handlers for at få et bedre indblik i hvordan DDD-event fungerer reelt.

Her er projektets opbygning.
![[Pasted image 20251121134220.png]]
### **Application-laget**

Her ligger klasser, der **håndterer hændelser** og styrer, hvad der skal ske i systemet.

- **DomainEventBus.cs**  
    Event-bussen der sender domain events videre til de rigtige handlers.
    
- **IDomainEventHandler.cs**  
    Interface for alle handlers. Bestemmer hvordan en handler skal reagere på et event.
    
- **PostPublishedHandler.cs**  
    Selve handleren for PostPublishedEvent. Kører application-logik som email og kald til domænet.
    

 _Application-laget reagerer på events og binder domæne og infrastruktur sammen._

---

### **Domain-laget**

Her ligger alt, der hører til **selve domænelogikken** — altså kernefunktionalitet.

- **ContentService.cs**  
    Domæneservice med forretningslogik. Den genererer nye Domain Events.
    
- **IDomainEvent.cs**  
    Kontrakten for alle domain events.
    
- **PostPublishedEvent.cs**  
    Et konkret domain event. Repræsenterer “post er publiceret”.
    

 _Domain-laget er systemets kerne og indeholder regler, entiteter og events._

---

### **Infrastructure-laget**

Her ligger kode, der taler med eksterne systemer eller ressourcer.

- **EmailNotifier.cs**  
    En simpel infrastrukturservice for at sende e-mails.
    

 _Infrastructure er afhængigt af eksterne ressourcer og bruges af application layer._

EventBus
EventBus er programmets central, der styrer indkomne events.
Den ved hvordan de kaldes og den ved hvilke handlers der håndterer hvilke events og kan derfor distriuere dem til den korrekte handler. I dette projekt har vi DomainEventBus som vores eventBus.


![[Pasted image 20251121134623.png]]
Priavte readonly List..- Her laves listen over alle typer eventhandlers.
i RegisterHandler metoden - Her registreres en handler og bliver gemt til et domain event. Det er her den korrekte handler registreres. Den der lytter efter eventet.
PublishAsync metoden - Her sendes eventet ud i systemet og metoden udføres på eventet.



![[Pasted image 20251121135800.png]]

I PostPublishedEvent - Der implmenteres kontrakten fra IDomainEventHandler.
Den lytter altså efter PostPublishedEvent og bliver deror kaldt af DomainEventBus.

De 3 services fra EmailNotifier, ContentService og DomainEventBus bliver injiceret i constructoren, så de kan bruges.
EmailNotifier - Sender email ved event.
ContentService - kan lave nye events
DomainEventBus - Kan publisere event fra domænet.

I handleAsync metode - Kaldes automatisk når Der er et PostPublishedEvent i DomainEventBus.
Den udfører logikken for EmailNotifier og ContentService.

Til sidst bliver der via foreach - Foreach tager hvert event, som domænet selv har skabt, og sender det tilbage ind i event-systemet, så de også bliver håndteret.
Her bruges liste som standard for at kunne håndtere flere events. Hvis flere events udløses som fx. notifikationer eller logging.
![[Pasted image 20251121140922.png]]



ContentService
![[Pasted image 20251121144458.png]]
Her kan der skabes events i _pendingEvents
Og via publishPost() - publiseres post og genereres et nyt domæne event.

ContentSerivce ligger i domænelaget, da den publiserer post og domæne events.

Dette kan derefter bruges af PostPublishHandler - Der kan hente listen og sende events videre i EventBus.
ContentService udfører forretningslogik og genererer nye Domain Events, som gemmes i en liste, så de senere kan sendes videre til EventBus og blive håndteret.

![[Pasted image 20251121145221.png]]
PostPublishedEvent - Indeholder de data handlers bruger, her i form af author og title og den repræsenterer hændelsen at " Et indlæg blev publiceret"
Den repræsenterer hvad der sker i domænet og hører derfor til i domænelaget.

EmailNotifier - simulerer sending af email og ligger i infrastructure da den hører til ydre services der kobles på systemet.


# **Domain Events i  The Way of Coherence

I _The Way of Coherence_ anvender vi Domain Events for at sikre en klar og fleksibel adskillelse mellem domænelogikken og de procesflows, der skal køre efter en forretningshændelse. Domænehændelserne bruges til at markere, at noget betydningsfuldt er sket i systemets kerne, uden at domænelaget behøver at kende de konkrete reaktioner eller eksterne konsekvenser.

Det passer direkte ind i både Clean Architecture og jeres Vertical Slice-struktur. Domænet rejser hændelsen, og den enkelte slice i Application-laget håndterer den del af logikken, der vedrører netop det forløb.

---

## **1. Hvad er et Domain Event i projektet?**

Et Domain Event er en lille model, der beskriver en vigtig forretningshændelse. Når et indlæg bliver udgivet, er dette en central handling i systemets Content-domæne. I stedet for at lade domæneobjekterne selv stå for logging, notifikationer eller statistik, rejser de blot et Domain Event.

På den måde kan andre dele af systemet reagere, uden at domænet skal ændres eller opnå nye afhængigheder.

---

## **2. Hvorfor bruger vi Domain Events?**

Domain Events anvendes for at opnå:

- Lav kobling mellem lagene.
- Et domænelag uden tekniske afhængigheder til logging, e-mails, UI eller applikationsservices.
- En fleksibel arkitektur, hvor nye reaktioner kan tilføjes uden ændringer i domænemodellerne.
- En vertikal slice-opdeling, hvor hver slice kan reagere på hændelser, uden at det påvirker andre features.
- Lettest mulig testbarhed, da domænelogik kan testes isoleret.

 Vi valgte Domain Events i stedet for Observer Pattern, fordi Domain Events giver løs kobling, rent domæne, høj testbarhed og perfekt integration med Clean Architecture, DDD og Vertical Slice Architecture.  
> Observer Pattern ville have skabt coupling i domænet og brudt arkitekturens afhængighedsregler.

---

## **3. Hvor rejser vi hændelsen?**

Når et indlæg udgives via domænemetoden _Publish()_, registrerer objektet selv en Domain Event. Domænelaget reagerer altså ikke på hændelsen – det fortæller blot, at den er sket.

Dette følger principperne i Domain-Driven Design:  
Domænet kender reglerne og ved, hvornår noget er betydningsfuldt, men det kender ikke resten af systemet.

---

## **4. Hvordan kommer hændelsen videre i systemet?**

Når indlægget gemmes via repositoryet, opsamler infrastrukturen alle Domain Events, der er rejst af dette objekt.  
Disse events sendes videre gennem en Event Dispatcher, som er placeret i Infrastructure-laget.

Dispatcheren sender hændelserne ind i MediatR-pipelinen, hvor hver hændelse omsættes til en notifikation, som Application-laget kan reagere på.

Domænet skal ikke kende MediatR og har ingen afhængighed til tekniske biblioteker.

---

## **5. Hvordan reagerer Application-laget?**

I Application-laget ligger event handlers i de relevante vertical slices.  
For eksempel i _Features/Posts/EventHandlers_ håndteres hændelsen om, at et indlæg er blevet udgivet.

Her kan vi udføre sideeffekter som:

- opdatere statistikker
- skrive audit-logs
- opdatere dashboardet
- sende beskeder videre til andre systemdele

Det er kun Application-laget, der foretager disse opgaver.  
Domænelaget har ingen viden om, hvad der sker efterfølgende.

---

## **6. Hvordan passer det ind i Vertical Slice Architecture?**

Vertical Slice betyder, at hver feature (fx Posts) indeholder alt, der vedrører den funktion: commands, queries, validators, event handlers osv.

Domain Events passer godt ind, fordi:

- En event handler ligger i den slice, som er relevant for hændelsen.
- Hver slice kan reagere på events uden at få afhængigheder til andre slices.
- Nye slices kan tilføje flere handlers på den samme event uden at røre eksisterende kode.

Det giver en naturlig, isoleret og let at udvide implementering.

## **7. Afslutning

Ved at kombinere Domain Events med Vertical Slice og Clean Architecture opnår projektet:

- En stærk adskillelse mellem forretningslogik og efterfølgende handlinger.
- Et domænelag uden teknisk støj og uden afhængigheder.
- Et application-lag der kan udvides slice for slice.
- Fleksibilitet til at bygge nye forløb, der reagerer på eksisterende hændelser.
- En arkitektur, der er robust, skalerbar og let at teste.



#  Domain Events vs Observer Pattern (og hvorfor vi valgte Domain Events)**

I dette indlæg går vi i dybden med to patterns, der ligner hinanden i idé, men fungerer fundamentalt forskelligt i praksis:

- **Observer Pattern**
- **Domain Events (DDD)**


Begge bruges til at reagere på hændelser.  
Men de gør det på _vidt forskellige måder_, og valget har stor betydning for arkitektur, koblingsgrad og domænedesign.

I vores projekt valgte vi **Domain Events** — og her forklarer vi præcis hvorfor.

---

