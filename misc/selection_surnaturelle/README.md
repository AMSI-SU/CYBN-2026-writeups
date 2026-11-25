# Sélection surnaturelle

__*Catégorie :*__ MISC  
__*Difficulté :*__ Easy
__*Flag :*__ `CYBN{j41_p4ss3_l3_c4st1ng_m4m4n_v4_3tr3_f13r3}`  

> Description  
> Le Conseil des Mages en avait marre d'attendre l'Élu. Du coup, ils ont viré l'oracle et confié le recrutement à une IA.
>
> Résultat : une regex sortie des enfers, 42 000 candidats recalés, et le dernier a avoir essayé aurait déclaré : "C'est une dinguerie, frère."
>
> Pourtant, il paraît qu'il existe une candidature qui passe le filtre. À toi de trouver laquelle, parce que pour l'instant, même le Grand Mage est en PLS.
>
>```
> \ACYBN\{(?=.{40}\}\Z)(?=[^}]*_p4ss3_)(?=[^}]*_v4_)(?![^}]*[A-Z])(?:(?=j)j)(?P<digit>[0-9])(?:1)_(?:p(?P=digit))(((((?P<letter>[a-z])))))(?P=letter)(?:(?![48675291])\d)_l[3]_(?:c(?P=digit))(?:s(?:t))1(?:n)g_(?:(?:m(?P=digit))){2}n_(?:v(?P=digit))_3(?:tr)3_f(?=13r3\})1(?:3)r3\}\Z
> ```
> 
>Format : CYBN{...}


---

## Partie 1 

Pour obtenir le flag, il suffit de trouver une chaîne acceptée par la regex de l'énoncé, on utilise https://regex101.com/ qui permet d'avoir une description de la regex élément par élément.

En les décortiquant, on obtient :

```
\ACYBN\{                    ==> doit commencer par CYBN{ 
(?=.{40}\}\Z)               ==> doit contenir 40 caractères terminés par un }
(?=[^}]_p4ss3_)             ==> doit contenir _p4ss3_
(?=[^}]_v4_)                ==> doit contenir _v4_
(?![^}]*[A-Z])              ==> aucune majuscule
(?:(?=j)j)                  ==> doit contenir un j
(?P<digit>[0-9])            ==> doit contenir un chiffre (défini dans le groupe digit)
(?:1)                       ==> doit contenir un 1
_                           ==> _
(?:p(?P=digit))             ==> doit contenir p suivi du chiffre contenu dans le groupe "digit" 
(((((?P<letter>[a-z])))))   ==> doit contenir une lettre (définie dans le groupe "letter")
(?P=letter)                 ==> doit contenir la lettre du groupe "letter"
(?:(?![48675291])\d)        ==> chiffre non inclus dans [48675291]
_l                          ==> _l
[3]                         ==> 3
_                           ==> _
(?:c(?P=digit))             ==> doit contenir c suivi du chiffre du groupe "digit"
(?:s(?:t))                  ==> doit contenir st
1                           ==> 1
(?:n)                       ==> doit contenir n
g_                          ==> g_
(?:(?:m(?P=digit))){2}      ==> doit contenir m suivi du chiffre du groupe "digit" 2 fois de suite
n_                          ==> n_
(?:v(?P=digit))             ==> doit contenir v suivi du chiffre du groupe "digit"
_3                          ==> _3
(?:tr)                      ==> doit contenir tr
3_f                         ==> 3_f
(?=13r3\})                  ==> doit contenir 13r3}
1                           ==> 1 
(?:3)                       ==> doit contenir 3
r3\}\Z                      ==> doit finir par r3} 
```

En recomposant tout "naïvement" et en supposant que le groupe letter est "s" et que le groupe digit est "4" (ça se devine assez facilement en faisant des tests pour avoir des mots cohérents), on obtient :
`CYBN{_p4ss3__v4_j41_p4ss3_l3_c4st1ng_m4m4n_v4_3tr3_f13r3}13r3}`

En enlevant les doublons et en réarrangeant, on obtient le flag :
`CYBN{j41_p4ss3_l3_c4st1ng_m4m4n_v4_3tr3_f13r3}`

