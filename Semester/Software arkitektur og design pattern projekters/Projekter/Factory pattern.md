# **Factory Pattern — Formål (helt kort)**

> Factory Pattern bruges til at **oprette objekter**, uden at den kode der bruger dem, behøver at vide _hvilken konkret klasse_ der bliver lavet.

Det handler om:

- at samle objekt-oprettelse ét sted
- at undgå `new` i hele projektet
- at kunne udvide systemet med nye varianter uden at ændre eksisterende kode

Projektet.
![[Pasted image 20251121233759.png]]
Interfacet IContent er kontrakten for Article, HealthTip og VideoPost typerne.
De implementerer hver især metoden - Void Display(); fra IContent.
![[Pasted image 20251121234038.png]]


Her implementer Article interfacet og dets regler.
Alle klasser kan implementere det på sin egen måde og derefter kan de laves via ContentFactory
![[Pasted image 20251121234150.png]]


ContentFactory
![[Pasted image 20251121234311.png]]Her bliver de hver især lavet af contenFactory - der er fabrikken hvorpå det besluttes hvilken en der skal laves via Create metoden.


> Programmet siger “giv mig indhold”, fabrikken beslutter hvilken type, og programmet bruger objektet via interfacet.



## **Hvorfor Factory er nyttigt

- Man slipper for if/switch alle steder
- Man undgår afhængigheder til konkrete klasser
- Man kan tilføje nye content-typer uden at ændre i resten af systemet
- Koden bliver langt nemmere at vedligeholde og teste



# **Factory Pattern (hvordan vi bruger det, og hvorfor det er nødvendigt i vores projekt)**

Factory Pattern er en af de mest anvendelige konstruktioner i DDD‐centrerede systemer.  
Det bruges når et objekt:

- har komplekse regler for oprettelse
- kræver forskellige strategier
- skal skabes på en konsistent måde
- ikke må konstrueres manuelt i UI eller Application
- afhænger af domænet (incl. value objects, invariants og events)

I dette projekt opstår disse behov ofte — inden for

- content creation

Det er derfor her vi valgte at bruge det.



# **Formålet med ContentFactory**

Formålet var:

> **At Application-laget kan skabe de rigtige Content-objekter (BlogPost, VideoPost, ReviewPost) uden at skrive `new` og uden at kende de konkrete klasser.**

Det vil sige:

- Application siger kun: _“Opret en blog”, “Opret et review”, “Opret en video”_
- Domain.Factory vælger **det korrekte domæneobjekt**
- Domænelogik ligger centralt og rent

---

#  **Hvad gjorde vores ContentFactory?**

Den havde _overloads_ som:

`CreateBlog(title, authorId, body, imageUrl) CreateVideo(title, authorId, videoUrl, description) CreateReview(title, authorId, productName, rating, reviewText)`

Hver metode:

 tager simple primitive parametre  
 returnerer det korrekte domæneobjekt:

- `BlogPost`
- `VideoPost
- `ReviewPost`

**→ UI og Application-laget behøvede aldrig at sige `new BlogPost(...)`.**  
**→ Domain styrede oprettelsen ét sted.**
![[Pasted image 20251128160901.png]]

---




# 2) Hvorfor Factory Pattern er vigtigt i projektet


##  A) Domænet har regler ved oprettelse

Eksempler:

- content skal valideres og evt. modereres ved oprettelse
- membership‐activation skal rejse domain events
- reviews skal skabe AI-baserede triggers afhængigt af type
- content body og title er value objects → kræver driftssikre konstruktioner

Hvis Application bare kører:

`var content = new Content(...);`

… så risikerer du:

- forvirring om hvor invariants ligger
- use cases der opretter objekter forskelligt
- duplikeret logik
- fejl ved manglende domæneregler

Factory Pattern løser dette.

---

##  B) Nye typer skal kunne tilføjes uden at ændre eksisterende kode

Eksempler:

- nyt content‐type (fx audio, pdf, embed, guide, premium-content)
- nye membership‐levels
- nye AI‐strategier

Factory Pattern følger OCP (Open/Closed Principle):

→ Du kan tilføje en ny type objekt uden at ændre eksisterende factory.  
→ Du registrerer blot en ny implementation, og DI finder den.




#  Hvordan Factory Pattern passer ind i Clean Architecture

###  Domain:

Entities og Value Objects forbliver rene.  
Factories kan ligge i Domain eller Application afhængigt af reglerne.

###  Application:

Commands/Handlers bruger factories til at skabe objekter korrekt.

###  Infrastructure:

Strategy-implementeringer og factories kan registreres via DI.

# **Hvordan vi bruger og implementerer ContentFactory i projektet**

I _The Way of Coherence_ har vi flere forskellige typer indhold, såsom blogindlæg, videoer og produktanmeldelser. Hver type har sine egne domæne-regler og felter, men vi vil ikke have, at Application-laget eller UI’et skal kende til de konkrete klasser eller skrive `new` for at oprette dem.

For at løse det bruger vi **Factory Pattern** i domænelaget.

Vi har oprettet en **ContentFactory**, som fungerer som en central indgang til at oprette alle typer af content-objekter. Når et nyt indhold skal oprettes, sender Application-laget kun de nødvendige primitive værdier – f.eks. titel, forfatter-id, tekst, video-url eller produktnavn. Ud fra dette vælger fabrikken den korrekte domæneklasse og bygger det rette objekt.

På den måde skjuler vi konstruktionen af BlogPost, VideoPost og ReviewPost, så resten af systemet ikke kender til konkrete implementeringer. Det gør domænelaget mere robust og udvidelsesvenligt, fordi vi kan tilføje nye content-typer senere uden at ændre kode i Application-laget.

Når et indhold er oprettet via fabrikken, bliver objektet typisk pakket ind i et _Post_-aggregate, hvor vi tilføjer metadata som kategori. Herefter kan vi udløse et _PostPublishedEvent_, som domænelaget bruger til sideeffekter som logging eller notifikationer.

Samlet set giver ContentFactory os:

- ét sted at styre al konstruktion af indhold
- lavere coupling mellem lagene
- renere domænemodeller
- mulighed for at tilføje nye content-typer uden at ændre eksisterende kode


Det er derfor en simpel, men effektiv brug af **Factory Pattern** i domænelaget.


#  Konklusion

> Factory Pattern er uundværligt i vores projekt, fordi det giver en ensartet, regelstyret måde at skabe domæneobjekter på.
> 
> Det kombinerer perfekt med Strategy Pattern, Domain Events, DDD’s value objects og Clean Architecture’s afhængighedsregler.
> 
> Resultatet er rene handlers, testbare domæneflows og et system, der er let at udvide uden at ændre eksisterende logik.
