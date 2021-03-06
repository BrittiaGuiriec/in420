---
layout: post
title: DM2 – Décodage par syndrome
---

Ce DM se place dans la continuité du TD sur les [Codes linéaires](Codes linéaires). 
Si vous avez fait ce TD en entier, l'effort nécessaire à
compléter ce DM va être minimal. Il vous est tout de même possible de
réussir ce DM sans avoir résolu l'intégralité du TD, en effet vous
pouvez vous focaliser sur le décodage du fichier qui vous est fourni
dans le
[challenge](http://swift.prism.uvsq.fr:2401/in420-dms.py/syndrome)
sans nécessairement écrire du code qui marche pour n'importe quel code
de Hamming.

## Le syndrome

Le syndrome est un objet fondamental dans le décodage des codes
linéaires. Dans la suite on suppose donné un code linéaire $$C$$ de
paramètres $$[n,k,d]$$, de matrice de parité $$H$$ et de matrice
génératrice $$G$$. Pour nos exemples, nous allons considérer le code de
paramètres $$[6,3,3]$$ défini par les matrices suivantes

$$G=\begin{pmatrix}
1&0&0\\
0&1&0\\
0&0&1\\
1&1&0\\
1&0&1\\
0&1&1
\end{pmatrix}
,\qquad
H=\begin{pmatrix}
1&1&0&1&0&0\\
1&0&1&0&1&0\\
0&1&1&0&0&1
\end{pmatrix}.$$

On rappelle qu'un message $$v$$ est encodé avec le mot de code
$$c=Gv$$. Dans notre exemple, le message $$011$$ est encodé par

$$c = Gv = \begin{pmatrix}
1&0&0\\
0&1&0\\
0&0&1\\
1&1&0\\
1&0&1\\
0&1&1
\end{pmatrix}
\begin{pmatrix}
0\\1\\1
\end{pmatrix}
=
\begin{pmatrix}
0\\1\\1\\1\\1\\0
\end{pmatrix}.
$$

Le destinataire reçoit un mot bruité $$y=c + e$$, où $$e$$ est un vecteur
d'erreur contenant au plus $$(d-1)/2$$ positions d'erreur. Le
**syndrome** du mot $$y$$ est la valeur

$$s = Hy = H(c+e) = Hc + He = He,$$

où les égalités découlent de la linéarité de la multiplication de
matrices et du fait que $$H$$ est la matrice de parité du code (et que
donc $$Hc=0$$ pour tout mot de code $$c$$). Dans notre exemple, en
supposant qu'il y ait eu une erreur en quatrième position, on a

$$s = Hy = \begin{pmatrix}
1&1&0&1&0&0\\
1&0&1&0&1&0\\
0&1&1&0&0&1
\end{pmatrix}
\begin{pmatrix}
0\\1\\1\\0\\1\\0
\end{pmatrix}
=
H
\begin{pmatrix}
0\\1\\1\\1\\1\\0
\end{pmatrix}
+
H\begin{pmatrix}
0\\0\\0\\1\\0\\0
\end{pmatrix}
=
\begin{pmatrix}
1\\0\\0
\end{pmatrix}.$$


## Décodage par syndrome

Comme on a vu ci-dessus, le syndrome d'un mot $$y=c+e$$ ne dépend que de
l'erreur $$e$$ et pas du tout du mot de code $$c$$. Même sans connaître
l'erreur, on peut donc calculer son syndrome en calculant le produit
$$s=Hy$$.

Une fois calculé le syndrome $$s$$, on trouve l'erreur $$e$$ par recherche
sur tous les mots d'erreur possibles. Comme notre code est
1-correcteur, les seules erreurs qu'on peut corriger sont celles de
poids au plus 1, c'est à dire les erreurs suivantes

$$\begin{pmatrix}
0\\0\\0\\0\\0\\0
\end{pmatrix},\quad
\begin{pmatrix}
1\\0\\0\\0\\0\\0
\end{pmatrix},\quad
\begin{pmatrix}
0\\1\\0\\0\\0\\0
\end{pmatrix},\quad
\begin{pmatrix}
0\\0\\1\\0\\0\\0
\end{pmatrix},\quad
\begin{pmatrix}
0\\0\\0\\1\\0\\0
\end{pmatrix},\quad
\begin{pmatrix}
0\\0\\0\\0\\1\\0
\end{pmatrix},\quad
\begin{pmatrix}
0\\0\\0\\0\\0\\1
\end{pmatrix},$$

qui correspondent aux syndromes suivants

$$\begin{pmatrix}
0\\0\\0
\end{pmatrix},\quad
\begin{pmatrix}
1\\1\\0
\end{pmatrix},\quad
\begin{pmatrix}
1\\0\\1
\end{pmatrix},\quad
\begin{pmatrix}
0\\1\\1
\end{pmatrix},\quad
\begin{pmatrix}
1\\0\\0
\end{pmatrix},\quad
\begin{pmatrix}
0\\1\\0
\end{pmatrix},\quad
\begin{pmatrix}
0\\0\\1
\end{pmatrix}.$$

Dans notre exemple, on était tombés sur le syndrome $$100$$, qui
correspond à l'erreur $$000100$$, c'est à dire une erreur en quatrième
position. 

Pour les codes 1-correcteurs, comme celui de notre exemple, on peut
trouver l'erreur directement sans énumérer tous les syndromes
possibles. Si le syndrome est zéro, alors nécessairement $$e=0$$ et il
n'y a pas eu d'erreurs. Sinon, on note $$e_i$$ le vecteur contenant un
$$1$$ à la position $$i$$ et $$0$$ partout ailleurs; souvenez vous qu'alors
$$s=He_i=H_i$$, où $$H_i$$ est la $$i$$-ème colonne de $$H$$. Par exemple

$$He_4=\begin{pmatrix}
1&1&0&1&0&0\\
1&0&1&0&1&0\\
0&1&1&0&0&1
\end{pmatrix}
\begin{pmatrix}
0\\0\\0\\1\\0\\0
\end{pmatrix}
=
\begin{pmatrix}
1\\0\\0
\end{pmatrix}.$$

On peut donc trouver l'erreur $$e$$ simplement en cherchant dans $$H$$ la
colonne qui correspond au syndrome.

Une fois trouvée l'erreur $$e$$, on retrouve le mot de code $$c=y+e$$. Il
ne reste plus qu'à trouver le mot d'origine $$v$$ tel que $$c=Gv$$. Mais,
puisque $$G$$ est sous forme systématique, ceci est immédiat: le mot
d'origine apparaît tel quel dans les $$k$$ premières composantes du mot
de code. Dans notre exemple

$$c = \begin{pmatrix}
0\\1\\1\\
\hline
1\\1\\0
\end{pmatrix}
=
\begin{pmatrix}
1&0&0\\
0&1&0\\
0&0&1\\
\hline
1&1&0\\
1&0&1\\
0&1&1
\end{pmatrix}
\begin{pmatrix}
0\\1\\1
\end{pmatrix}
= Gv.$$


Pour résumer, voici l'algorithme de décodage par syndrome.

> **Entrée:** Un mot bruité $$y$$, la matrice de parité $$H$$.
> 
> **Sortie:** Le message d'origine $$v$$.
>
> 1. Calculer le syndrome $$s=Hy$$;
> 2. Chercher l'erreur $$e$$ telle que $$s=He$$;
> 3. Calculer le mot de code $$c=y+e$$;
> 4. Le message d'origine se trouve dans les $$k$$ premières composantes de $$c$$.


## À vos `javac`

Votre devoir consiste à écrire un programme qui encode et décode un fichier binaire en utilisant un code de Hamming et le décodage par syndrome. On donne ici des instructions pour vous aider à structurer votre code, vous n'êtes cependant pas obligés de les suivre à la lettre.

1. Écrivez deux fonctions:
    
    - Une fonction qui lit un fichier octet par octet et retourne un tableau de bits (par exemple en utilisant l'objet `Bit` du TD);
    - Une fonction qui prend un tableau de bits et l'écrit dans un fichier. Si la longueur du tableau n'est pas un multiple de 8, ajoutez assez de 0 **à la fin**.

Quelques mots sur le codage du fichier: vous ne devez pas le traiter
comme s'il s'agissait d'un fichier de texte. Vous devez le lire en
mode binaire: **octet par octet**. Les lectures octet par octet peuvent être faites en Java par une boucle comme ceci (le `BufferedInputStream` est optionnel, mais il ajoute de l'efficacité sans rendre la programmation plus compliquée):

~~~
BufferedInputStream in = new BufferedInputStream(new FileInputStream("source.txt"));

int i;
while ((i = in.read()) != -1) {
    byte octet = (byte)i;
    ...
}
~~~

De manière analogue, les écritures octet par octet se font avec

~~~
BufferedOutputStream out = new BufferedOutputStream(new FileOutputStream("dest.txt"));

while (...) {
    byte octet = ...
    out.write(octet);
}
~~~

Pour plus d'informations sur les lectures/écritures de fichiers en mode binaire, allez
lire la page [Entrées-Sorties en Java](Entrées-Sorties en Java). 

2. Écrivez une fonction qui:

    1. Prend en entrée un tableau de bits de longeur arbitraire;
    2. Le découpe en blocs de $$4$$ bits;
    3. Code chaque bloc en utilisant le code de Hamming $$(7,4)$$;
    4. Renvoie un tableau de bits qui est l'enchaînement des blocs codés

Encore quelques mots sur l'enchaînement des fonctions que vous avez écrites jusqu'à maintenant. Chaque octet du fichier d'origine vous fournit huit bits, ce qui fait deux blocs de 4 bits. Par exemple, le fichier codé en ASCII
que vous pouvez télécharger [ici](misc/dm2_ham.txt) contient le texte

~~~
Ham
~~~

Ce qui correspond aux trois octets (écrits en hexadécimal)

~~~
0x48 0x61 0x6d
~~~

Cela fait 6 blocs de 4 octets (écrits en binaire, pour changer):

~~~
0100 1000 0110 0001 0110 1101
~~~

Chaque bloc est encodé sur sept bits, et les résultats sont concaténés, ce qui donne

~~~
0100101 1000110 0110110 0001111 0110110 1101100
~~~

On obtient $$6\times 7=42$$ bits au total, ce qui n'est pas un multiple
de 8. Pour encoder ceci dans un fichier, on ajoute assez de 0 à droite
jusqu'à obtenir un multiple de 8 (le multiple suivant est 48). Le
résultat est donc le fichier, que vous pouvez télécharger [ici](misc/dm2_ham.bin), contenant les octets suivants

~~~
01001011 00011001 10110000 11110110 11011011 00000000
~~~

soit, en héxadécimal,

~~~
0x4b 0x19 0xb0 0xf6 0xdb 0x00
~~~

Ceci n'est évidemment pas un fichier de texte lisible (voici par exemple le caractère `0xd0`: Ð). Vous ne pourrez
donc récupérer le texte d'origine qu'après avoir implanté le décodage. Pour vous aider à débugger votre programme, vous pouvez utiliser un *hex editor* comme <http://www.wxhexeditor.org/>: les *hex editors* sont des programmes qui affichent le contenu binaire d'un fichier quelconque sous forme hexadécimale. Sous linux la commande `hd` fait la même chose.


3. Écrivez une fonction qui prend un tableau de bits et qui y ajoute
   une erreur au plus tous les 7 bits. Vous pouvez utiliser la fonction
   
   ~~~
   int alea = (int)(Math.random() * 7);
   ~~~
   
   pour obtenir un entier aléatoire entre 0 et 7.

4. Écrivez une fonction qui

    1. Prend en entrée un tableau de bits de longeur arbitraire;
    2. Le découpe en blocs de $$7$$ bits;
    3. Decode chaque bloc en utilisant un décodage par syndrome;
    4. Renvoie un tableau de bits qui est l'enchaînement des blocs décodés

Vérifiez votre programme en encodant un fichier, en y ajoutant des erreurs et en décodant (avec et sans erreurs ajoutées).

5. Votre mission: visitez
[cette page](http://swift.prism.uvsq.fr:2401/in420-dms.py/syndrome) et
décodez le fichier binaire qui vous est proposé. **ATTENTION:** le
fichier a été encodé avec un code de Hamming comme décrit précédemment
(découpage des octes en bits, concaténation, etc.), mais pas forcément
avec le code $$(7,4)$$. Vous devez donc faire en sorte que votre code
puisse gérer des codes de Hamming différents (ce qui à priori est
gagné si vous avez complété le TD sur les [Codes linéaires](Codes linéaires)).

Déposez votre code source et le texte déchiffré dans la boîte de dépôt
sur [e-campus 2](http://e-campus2.uvsq.fr/) (si e-campus ne devait pas
marcher, envoyez-les par mail au
[professeur](http://www.prism.uvsq.fr/~dfl)).  La date limite pour
envoyer vos fichiers est le jeudi 5 Avril à 4h du matin. Un point
de pénalité pour chaque heure de retard: le 5 Avril à 23h59 c'est votre dernière chance!
