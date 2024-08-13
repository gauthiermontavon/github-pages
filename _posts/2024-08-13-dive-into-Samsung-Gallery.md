---
title: "Mobile Forensics - Dive into Samsung Gallery"
date: 2024-08-13
---

# Intro
Durant l'une de mes investigations sur un téléphone Samsung, j'ai cherché à obtenir plus d'informations sur les éléments effacés de la galerie photos. Je suis très vite tombé sur un article intéressant sur le blog "Cheeky4n6monkey" qui détaille les informations pertinentes contenues dans la base de données local.db associée à l'application Gallery 3D de Samsung.

On y découvre notamment une table "log" qui contient des infos sur les médias effacées (path + date). Ces informations sont encodées en base64 et un script python fourni sur le blog permet de parser la table et de fournir les résultats décodés dans un fichier de sortie. Malheureusement pour moi (c'eut été trop facile), les scripts fournis sont adaptés aux versions v10.0.21.5, v10.2.00.21 et v11.5.05.1 (l'article date de janvier 2022), alors que je suis confronté à la version 14.1.04.2.

# Différences v.11.5.05.1 - v.14.01.04.2
L'article "Cheeky4n6monkey" illustre la table "log" contenant l'information clé suivante (colonne "log"):
```
[MOVE_TO_TRASH_SINGLE][1][0][location://timeline?position=6&mediaItem=data%3A%2F%2FmediaItem%2F-1566891466&from_expand=false][oKHi/x4pePL+KXj3N0b3Lil49h4pePZ2XimIUvZW3imIV14pePbOKYhWHil4904pePZeKYhWTimIUv4pePMC9E4piFQ0nimIVNL+KYhUZh4pePY+KYhWXil49ib2/imIVr4pePL+KXj0ZCX+KYhUnil49N4pePR1/imIUx4piFNeKYhTfimIU4NOKYhTnimIUw4piFNzTimIU1N+KXjzPimIUy4piFLuKXj2rimIVwZw==ST1puy1]
```
alors que la version 14.01.04.2 contient :
```
[MOVE_TO_TRASH_SINGLE] Req Total[1] LI[1] CI[0] LCI[0] LV[0] CV[0] LCV[0] Success LI[1] CI[0] LCI[0] LV[0] CV[0] LCV[0] Fail LI[0] CI[0] LCI[0] LV[0] CV[0] LCV[0] FP[ #G$E2XK5HUAG8VFpcuF5KH7xHWhfyAxA3nnpyXJ5SUhavUhBB7QENQ+kBDyzvAg5C6QsRGa1UF0HpAAtF9A==  ] PP[ #G$E35b/e67itzeHlqcfxpq3e4a7jmquOj+PJ5Y/L6a++yw==  ] LD[1] CD[0] MF[0] AB[0] ABR[false] RSS[0] [location://timeline?position=174&media_item=data%3A%2F%2FmediaItem%2F-309796036&from_expand=false]
```
Le contenu est relativement similaire mais plus complet. Ensuite, chaque valeur paraît être préfixée par un acronyme, à l'exception de la première valeur ([MOVE_TO_TRASH_SINGLE]) et de la dernière valeur (location://). Finalement le contenu codée en base64 est plus court dans la nouvelle version, ou alors réparti dans les clés FP (FilePath?) et PP (??). Ces valeurs semblent être préfixées de "_#G$" et suffixées de "__"

# Reverse Enginneering Gallery v.14.01.04.2


