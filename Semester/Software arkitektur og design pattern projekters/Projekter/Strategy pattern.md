
**Strategy Pattern — Step 1: Hvad handler det om?

> Strategy Pattern bruges, når man vil **skifte en algoritme eller adfærd dynamisk**, uden at ændre koden i den klasse der bruger den.


![[Pasted image 20251121150415.png]]

I vores lille spike handler det om:

- **hvordan indhold vises for forskellige brugertyper**  
    (Guest, Member, Admin )
    Her repræsenteret af DisplayStrategy klasserne, der alle implementerer interfacet IContentDisplayStrategy
    
    De har alle forskellige regler for implmenteringen af interfacet.
    Kun strategy klasser kender til reglerne for implmenteringen af de forskellige strategier. Ikke contenViewer.

![[Pasted image 20251121230910.png]]
I GuestDisplayStrategy, der er der begrænsning på hvad der vises, da d


    
![[Pasted image 20251121230827.png]]
I memberDisplayStrategy klassen, der vises alt indhold via Display content og der er her ingen begrænsniger på.


![[Pasted image 20251121231417.png]]
Kotekst klassen ContentViewer får en strategy fra en af strategy klasserne og viser den via ShowContent metoden.

Derefter udfører strategy klassen sin logik i metoden DisplayContent.

Det smarte ved dette pattern er at der kan skiftes strategi og udførsel hele tiden, alt efter hvilken regler strategy klassen har. 

HVorfor strategy pattern:
Den kan håndtere udskiftelige regler.
Kan nemt håndtere den samme type handling, men på forskellige måder.

Strategy Pattern lærte mig, hvordan adfærd kan udskiftes uden at ændre selve klassen.  
Men i det rigtige projekt blev jeg klar over, at rollebaseret visning hører til i Identity og UI’et — ikke i strategi-klasser.  
Derfor brugte vi princippet, men ikke Strategy Pattern direkte.
I et rigtigt Blazor/Identity-system giver denne tilgang mere mening:

- `if (User.IsInRole("Admin"))`
- `[Authorize(Roles="Member")]`
- UI der skjuler/shower elementer
- backend der beskytter endpoints

Det håndterer præcis det Strategy skulle — bare bedre.

Jeg havde i overvejelserne at der kunne være behov for mønstret ved brug af forskellige notifications strategier - hvilket vi dog ikke implementerede

Hvis der skulle være personlige anbefalinger, eller filter på søgninger, men igen er det ikke noget vi har kunnet gøre brug af i det her lille projekt..



