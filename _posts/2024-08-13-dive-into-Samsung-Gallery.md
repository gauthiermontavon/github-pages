---
title: "Mobile Forensics - Dive into Samsung Gallery"
date: 2024-08-13
---

#Intro
Durant l'une de mes investigations sur un téléphone Samsung, j'ai cherché à obtenir plus d'informations sur les éléments effacés de la galerie photos. Je suis très vite tomber sur un article intéressant sur le blog "Cheeky4n6monkey" qui détaille les informations pertinentes contenues dans la base de données local.db associée à l'application Gallery 3D de Samsung.

On y découvre notamment une table "log" qui contient des infos sur les médias effacées (path + date). Ces informations sont encodées en base64 et un script python fourni sur le blog permet de parser la table et de fournir les résultats décodés dans un fichier de sortie. Malheureusement pour moi (c'eut été trop facile), les scripts fournis sont adaptés aux versions v10.0.21.5, v10.2.00.21 et v11.5.05.1 (l'article date de janvier 2022), alors que je suis confronté à la version 14.

