# Cybermon 1 - Le collectionneur banni

__*Catégorie :*__ WEB  
__*Difficulté :*__ Easy  
__*Flag :*__ `CYBN{f39014a2-7476-4e39-aaac-65391ff415f4}`  

> Un collectionneur affirmait posséder une carte issue du Projet Black Deck. Accusé de trafic de cartes interdites, son compte a été banni et sa collection rendue privée.  
> Son profil existe toujours sur Cybermon Online, et certaines de ses données semblent encore accessibles. Retrouvez sa collection et récupérez la première carte interdite.


---

## Partie 1 — Reconnaissance
La page **Collectionneurs** liste 12 profils, aux identifiants **séquentiels** (01 à 12). Un seul se distingue : le **#09 DecksterMorgan**, marqué « Banni · Collection privée ». Sa bio nargue : « Ma plus rare est toujours à sa place. »

![Liste des collectionneurs, #09 banni et privé](images/01-collectionneurs.png)

## Partie 2 — Exploitation de l'IDOR
Le profil #09 affiche « Cette collection est privée » et ne propose **aucun** bouton « Ouvrir la collection ».

![Profil privé de DecksterMorgan, sans bouton d'accès](images/02-profil-prive.png)

À l'inverse, un profil public (CardyMcCardface, #01) expose bien ce bouton, qui mène à l'endpoint d'affichage d'une collection — paramétré par l'identifiant du collectionneur.

En reprenant cette URL et en remplaçant l'identifiant `1` par `9`, le serveur renvoie un **200** et sert l'intégralité de la collection privée (**46 cartes**). Le badge « Album privé » reste affiché : le serveur connaît l'état privé mais ne vérifie pas que le demandeur en est propriétaire. C'est un **IDOR / Broken Object Level Authorization** (OWASP API1:2023, CWE-639).

![Collection privée servie malgré le badge « Album privé »](images/03-collection-privee.png)

## Partie 3 — Récupération du flag
La première carte de la collection est la **carte Suprême**. Elle porte le flag.

![Carte Suprême portant le flag](images/04-flag.png)

```
CYBN{f39014a2-7476-4e39-aaac-65391ff415f4}
```
