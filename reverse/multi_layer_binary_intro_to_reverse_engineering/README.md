# Multi Layer Binary - Intro to reverse engineering

__*Catégorie :*__ REVERSE  
__*Difficulté :*__ Intro  
__*Flag :*__ `CYBN{35SY_P3ASY}`  

> Description  
> Petit challenge de mise en bouche. Trouve le flag dans ce binaire.
> Format du flag : `CYBN{...}`

---

## Analyse sommaire et récupération du flag
On nous fourni un seul binaire à télécharger. Un rapide `file` dessus nous indique à quoi nous avons affaire :  
![file](images/file.png)

Étant donné que c’est un chall d’intro, je sais que je vais devoir faire appel au meilleur outil de rétro-ingéniérie : `strings` !  

<img src="images/meme.png" alt="meme" width="50%">

![strings](images/strings.png)

First blood, 2 minutes après le début du CTF !  

<img src="images/first-blood.png" alt="first-blood" width="50%">
