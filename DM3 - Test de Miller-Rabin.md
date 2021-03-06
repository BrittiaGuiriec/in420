---
layout: post
title: DM3 – Test de Miller-Rabin
---


Nous avons vu que des nombreux protocole cryptographiques nécessitent de grands nombres premiers pour être mis en place. Mais comment fait-on pour produire des nombres premiers? L'objet de ce DM est le test de primalité de Miller-Rabin, qui permet de déterminer très efficacement si un entier est composé ou "probablement premier".

## Test de Fermat

Le test de Fermat est basé sur le petit théorème de Fermat qui dit, rappelons-le, que pour tout nombre premier $$p$$ et pour tout $$a \ne 0 \mod p$$.

$$a^{p-1} = 1 \mod p.$$

Cette propriété caractéristique des nombres premiers, et il y a peu de chances en général qu'elle soit vérifiée par d'autres nombres. Supposons qu'on ait un nombre $$n$$ dont on ne sait pas s'il est premier ou composé. Pour le vérifier on peut choisir un entier $$a$$ au hasard et vérifier si

$$a^{n-1} \ne 1 \mod n.$$

Si tel est le cas, on conclut que $$n$$ n'est pas premier; sinon on ne peut rien dire, mais on affirme que $$n$$ à une chance d'être premier.

Par exemple, voici la valeur de $$a^{14} \mod 15$$ pour $$a$$ allant de $$1$$ jusqu'à $$14$$:

$$1, 4, 9, 1, 10, 6, 4, 4, 6, 10, 1, 9, 4, 1.$$

Il n'y a donc que les valeurs $$a=1,4,11,14$$ qui risquent de nous induire en erreur.

Si un entier $$a$$ est tel que $$a^{n-1}\ne 1 \mod n$$, on dit que $$a$$ est un **témoin de composition** pour $$n$$. Si $$n$$ n'est pas premier et $$a$$ est tel que $$a^{n-1} = 1$$, on dit que $$a$$ est un **menteur** pour $$n$$. Remarquez que $$1$$ et $$n-1$$ sont des mauvais choix: le premier est toujours un menteur, le deuxième est un menteur pour tout $$n$$ impair.

Quelques répétitions du test de Fermat sont en général suffisantes à décider correctement de la primalité d'un nombre. Il existe cependant des nombres, dits [de Carmichael](http://fr.wikipedia.org/wiki/Nombre_de_Carmichael), pour lesquels tout entier est un menteur. Le plus petit nombre de Carmichael est $$561 = 3 \cdot 11 \cdot 17$$.

## Test de Miller-Rabin

Le test de Miller-Rabin est un raffinement du test de Fermat. En plus d'exploiter le petit théorème de Fermat, il exploite cette propriété spécifique aux nombres premiers qui suit: il est toujours vrai que

$$1^2 = -1^2 = 1 \mod n$$

pour tout entier $$n$$, premier ou non. Ce qui, par contre, est vrai seulement pour les nombres premiers $$p$$ c'est que $$-1$$ et $$1$$ sont les **deux seules** racines carrées de $$1$$. Par exemple on a

$$1^2 = -1^2 = 4^2 = 11^2 = 1 \mod 15.$$

Étant donné un entier $$n$$ à tester, le test de Miller-Rabin procède comme suit:

1. On écrit $$n-1 = 2^s d$$ avec $$d$$ impair;

2. On choisit $$1 < a < n-1$$ au hasard;

3. On calcule $$a^d\mod n$$, si c'est égal à $$1$$ ou $$-1$$ on ne peut rien dire et on arrête;

4. Pour $$i$$ allant de $$1$$ jusqu'à $$s$$ on calcule $$a^{d2^i}\mod n$$:
    
    - si $$a^{d2^i} = -1 \mod n$$ on ne peut rien dire et on arrête;

    - si $$a^{d2^i} = 1 \mod n$$ on a trouvé une racine carrée de l'unité différente de $$1$$ et $$-1$$, on conclut que $$n$$ est composé et on arrête;

5. Si on est arrivés à la fin de la boucle et que $$a^{d2^s}\ne 1\mod n$$, on conclut que $$n$$ est composé, comme d'après le test de Fermat.


Le test de Miller-Rabin est très efficace: on peut montrer que pour tout $$n$$ la probabilité qu'un $$a$$ pris au hasard soit un menteur est au plus $$1/4$$ (et souvent en pratique beaucoup plus faible). Si on répète $$k$$ fois le test de Miller-Rabin (avec des $$a$$ différents au hasard) la probabilité d'identifier erronément un nombre composé comme étant premier est bornée par $$1/4^k$$.

Dans les applications pratiques on estime souvent qu'une vingtaine de répétitions du test de Miller-Rabin suffisent à affirmer avec confiance qu'un nombre est premier. Notez, tout de même, qu'il existe d'autres algorithmes qui permettent de **certifier** qu'un nombre est premier!


## La classe `BigInteger`

Java possède plusieurs types primitifs d'entiers: `byte`, `short`, `char`, `int`, `long`. Tous ces types sont à *précision fixe*: ils tiennent dans un nombre prédéterminé d'octets et ne peuvent pas dépasser cette borne. Le type le plus grand est `long`, qui code les entiers sur 64 bits.

Cependant, 64 bits correspondent à peine 20 chiffres décimaux: largement en dessous des tailles d'entiers nécessaires à la cryptographie (plutôt 1024 bits ou 300 chiffres décimaux). La classe [java.math.BigInteger](http://docs.oracle.com/javase/6/docs/api/java/math/BigInteger.html) offre des entiers à précision arbitraire: leur occupation en mémoire grandit au fur et à mesure que les calculs l'exigent. Plus de soucis à se faire sur les dépassements de bornes!

Les `BigInteger` sont des objets immuables, comme les `String`: c'est à dire qu'une fois qu'il a été crée, un `BigInteger` ne peut plus être changé.

Pour créer un `BigInteger`, rien de plus simple: on peut en créer à partir d'une chaîne de caractères

~~~
BigInteger a = new BigInteger("12345678901234567890");
~~~

ou, de façon un peu plus travaillée, à partir d'un tableau d'octets

~~~
BigInteger b = new BigInteger({0xff, 0xff, 0xff, 0xff, 0xff});
~~~

Des constantes aux noms qui parlent de soi sont aussi prédéfinies:

~~~
BigInteger.ZERO
BigInteger.ONE
BigInteger.TEN
~~~


L'arithmétique est aussi simple que la création, on peut regretter tout de même que Java n'offre pas la surcharge des opérateurs

~~~
BigInteger afoisb = a.multiply(b);
BigInteger c = a.add(b);
c = c.multiply(c);
~~~

Pour chaque opération arithmétique standard sur les entiers, la classe `BigInteger` définit une méthode qui lui correspond. Allez voir la [doc](http://docs.oracle.com/javase/6/docs/api/java/math/BigInteger.html) pour la liste complète.


## À vos `javac`

Le but de ce DM est d'implanter le test de Miller-Rabin pour des grands entiers, cependant la classe `BigInteger` implante déjà tout le DM. Pour que ce ne soit pas trop simple, il vous est interdit de vous servir des méthodes suivantes: `BigInteger.modInverse()`, `BigInteger.modPow()`, `BigInteger.gcd()`, `BigInteger.isProbablePrime()`, `BigInteger.nextProbablePrime()`.

1. Écrivez un programme Java qui prend en entrée deux entiers $$a$$ et $$n$$ et qui vérifie si $$a$$ est un témoin de composition pour $$n$$.

2. Écrivez un programme qui prend en entrée un entier $$n$$ et optionnellement un nombre $$k$$ d'itérations et qui fait $$k$$ tests de Miller-Rabin avec des témoins choisis au hasard.

Vérifiez votre programme avec des petits nombres premiers et composés. Un conseil: testez votre programme avec le nombre de Carmichael $$651693055693681 = 72931 \cdot 87517 \cdot 102103$$, si votre programme trouve qu'il est premier, vous avez certainement un bug (vous avez mal testé les conditions sur les racines carrées de l'unité).

3. Votre mission: visitez [cette page](http://swift.prism.uvsq.fr:2401/in420-dms.py/primes) et utilisez le test de Miller-Rabin pour sauver le monde! Faites attention: ces nombres sont bien trop gros pour vous passer de la classe `BigInteger`...

Déposez votre code source et le texte déchiffré dans la boîte de dépôt
sur [e-campus 2](http://e-campus2.uvsq.fr/) (si e-campus ne devait pas
marcher, envoyez-les par mail au
[professeur](http://www.prism.uvsq.fr/~dfl)).  La date limite pour
envoyer vos fichiers est le vendredi 4 Mai à 4h du matin. Un point
de pénalité pour chaque heure de retard: le 4 Mai à 23h59 c'est votre dernière chance!
