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
Le contenu est relativement similaire mais plus complet dans la version 14.01.04.02. Ensuite, chaque valeur paraît être préfixée par un acronyme, à l'exception de la première valeur ([MOVE_TO_TRASH_SINGLE]) et de la dernière valeur (location://). Finalement le contenu codée en base64 est plus court dans la nouvelle version, ou alors réparti dans les clés FP (FilePath?) et PP (??). Ces valeurs semblent être préfixées de "_#G$" et suffixées de "__"

# Reverse Enginneering Gallery v.14.01.04.2

## Installation de Jadx
https://github.com/skylot/jadx

## Chercher les opérations d'effacement
Après avoir décompiler l'apk, un type enum "TrashLogType" est vite trouvé (com.samsung.android.gallery.module.trash.abstraction), il enumère tous les types d'effacement à loguer, soit ceux que l'on retrouve dans la table "log" (DELETE_SINGLE, MOVE_TO_TRASH_SINGLE). En remontant les appels, on trouve que la classe qui nous intéresse est TrashHelper, qui utilise la classe TrashLogger pour construire la "string" à écrire dans la base de données.

Finalement, comme dans l'article "Cheeky4n6monkey", la classe Logger.getEncodedString(...), appelée TrashDeleteLogger.getDetail, contient l'algorithme d'encodage des données. FP= FilePath , PP= ParentPath
(int i10 => Random number 0-4).
```
    public static String encodeV2(String str, int i10) {
        if (TextUtils.isEmpty(str)) {
            return "null";
        }
        return " #G$E" + i10 + Base64.getEncoder().encodeToString(xorWithKey(str.getBytes(), sEncV2Key[i10])) + " ";
    }

```

sEncV2Key est un tableau de tableaux de byte qui contient plusieurs clé d'encodage utilisé pour faire un XOR avec le texte, le tout encodé en base64. Ainsi les données contenues dans FP et PP sont composées de :
#G$E<span style="color:Tomato">i</span>ENCODED_STRING

```
EncV2Key = new byte[][]
{
	new byte[]{17, 34, -1, -33}, //11 22 ff df
	new byte[]{-33, 52, 34, -47}, //df 34 22 D1
	new byte[]{115, -35, 51, 63}, //73 dd 33 3f
	new byte[]{-54, -52, -86, -124}, //ca cc  aa 84
	new byte[]{21, 33, -33, -33}, //15 21 df df
	new byte[]{81, 18, -1, -3}, //51 12 ff fd
	new byte[]{52, 51, 63, 115}, //34 33 3f 73
	new byte[]{95, -14, 31, -22} //5F f2 1f ea
};
```
Pour un usage et une lecture plus pratique dans le futur script python, je le convertis directement en tableau de valeur hexa décimal:

```
EncV2key = [
    '1122ffdf',
    'df3422d1',
    '73dd333f',
    'caccaa84',
    '1521dfdf',
    '5112fffd',
    '34333f73',
    '5ff21fea']

### to be continued


