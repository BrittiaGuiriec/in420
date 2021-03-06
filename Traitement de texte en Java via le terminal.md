---
layout: post
title: Traitement de texte en Java via le terminal
scripts: ['js/show-after.js']
---

Java est un langage destiné principalement aux interfaces graphiques et à la programmation de serveurs. Néanmoins, il offre les mêmes facilités que le C, voire un peu de bibliothèques en plus, pour le traitement de données au terminal. D'autres langages de scripting, tel Perl, Python, Lua, Ruby, offrent beaucoup plus de facilités dans ce sens, mais le but de ce cours étant aussi la consolidation de notre connaissance du Java, nous n'en parlerons pas.


## Le terminal

Il y a deux façons de traiter le texte, et plus généralement les données. Tout le monde connaît les fichiers, paquets de données binaires destinées à être stockées sur disque dûr et/ou chargées en mémoire vive afin d'être traités par les logiciels. L'autre moyen, moins pratiqué par les non-experts, est le **terminal**, ou **console**, véritable couteau suisse de l'informaticien.

Dans les systèmes Unix (ce qui inclut Linux, BSD, MacOS, ...), un terminal est souvent facilement disponible (MacOS peut demander un peu de configuration pour l'afficher). Dans windows 7, un terminal est disponible sous le nom, très impropre, d'*invite de commandes*. Trouvez ce logiciel dans votre système et créez un raccourci vers celui-ci.

Lorsque vous exécutez un terminal, celui ci vous place dans un *répertoire initial*, vous affiche un *prompt* (c'est à dire une ligne de texte contenant des informations telles le répertoire courant, l'utilisateur, etc.), et se met en attente de vos commandes. Sous linux vous pouvez demander à afficher le contenu du répertoire courant en tapant `ls` ; la même chose peut être obtenue sous Windows avec la commande `dir`.

Voici un exemple de dialogue avec un terminal linux :

    Linux proust 3.2.0-4-amd64 #1 SMP Debian 3.2.35-2 x86_64
    
    The programs included with the Debian GNU/Linux system are free software;
    the exact distribution terms for each program are described in the
    individual files in /usr/share/doc/*/copyright.
    
    Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
    permitted by applicable law.
    You have mail.
    Last login: Fri Jan 25 18:17:14 2013 from 85-170-121-50.rev.numericable.fr
    dfl@proust:~$ ls
    apo      Downloads           Fiche de renseignement PStage.odt  texmf   travail
    bin      enseignement        keats                              tmp     www
    Desktop  examen-13-1-10.pdf  prism_www                          towers
    dfl@proust:~$ cd ..
    dfl@proust:/home$ ls
    dfl  lost+found
{: .no-highlight}

La ligne `dfl@proust:~$` est le *prompt*, indiquant l'utilisateur (`dfl`), le nom de l'ordinateur (`proust`) et le répértoire courant (`~`, un alias pour le répertoire initial `/home/dfl`).

Vous pouvez vous déplacer dans le répertoires, aussi bien sous UNIX que sous Windows, avec la commande `cd` suivie du nom du répertoire. Le nom `..` est un alias pour le nom du répertoire parent.

Si vous avez un fichier de texte dans votre répertoire courant, vous pouvez l'afficher dans le terminal avec la commande `cat` (`type` sous windows) suivie du nom du fichier.

1. Déplacez-vous dans un dossier contenant un fichier de texte et affichez-le avec la commande `cat` (`type`).

N'hésitez pas à test l'usage des flèches haut/bas (en général, rappel des dernières commandes) et la touche tab (en général, auto-complétion) dans le terminal. De nombreux autres raccourcis clavier sont aussi disponibles selon votre terminal, à vous de les découvrir.


## Java et le terminal

On va refaire pour la n-ième Hello world en Java.

1. Dans le répertoire de votre choix, avec votre éditeur préféré (pas
   notepad, svp), créez un fichier `Hello.java` contenant le texte
   suivant
   
   ~~~
   public class Hello {
       public static void main(String[] args) {
           System.out.println("Hello world!");
       }
   }
   ~~~

   il est important d'écrire `Hello` avec la même combinaison de
   majuscules et minuscules dans le nom du fichier et dans le nom de
   la classe.

2. Java est un langage compilé/interprété, ce qui veut dire qu'avant
   de pouvoir exécuter `Hello.java` il va falloir le compiler dans un
   fichier `Hello.class`. Certains IDE (par exemple Eclipse) le font
   automatiquement pour vous, mais nous allons quand même faire au
   moins une fois la démarche de compiler à la main. Dans le terminal
   naviguez jusqu'au répertoire contenant `Hello.java`. Au prompt,
   tapez la commande
   
   ~~~
   javac Hello.java
   ~~~

   Si tout va bien, ceci va créer le fichier `Hello.class`, vérifiez
   sa présence avec la commande `ls` (`dir`). Il se pourrait que cette
   commande échoue parce que le système n'arrive pas à trouver le
   programme `javac`. Ceci est très improbable sous Linux ; sous
   Windows, au contraire, cela arrivera presque sûrement. Pour régler
   ce problème, allez voir [cette page](Configurer PATH et CLASSPATH).

3. Maintenant vous pouvez exécuter votre programme. Tapez
   
   ~~~
   java Hello
   ~~~
   
   et observez le résultat.



## Prendre des entrées sur la ligne de commande

L'intérêt de la console c'est qu'elle permet de passer avec simplicité des entrées aux programmes java, voir même le contenu d'un fichier entier. Pour passer des paramètres à un programme à travers la console, il suffit de les écrire dans la ligne de commande à la suite du nom du programme, séparés par des espaces, comme ceci

~~~
java Hello parametre1 parametre2
~~~

Chaque langage a sa façon de rendre ces paramètres disponibles au programme. En C, ils sont passés à la fonction `main` dans un tableau de chaînes de caractères, comme ceci

~~~
int main (int argc, char** argv) {
    ...
}
~~~

où `argc` est un entier donnant le nombre de paramètres plus un, `argv` est le tableau de chaînes, contenant le nom du programme en `argv[0]`, les paramètres dans les cases suivantes. Java se comporte de façon très similaire au C, en passant les paramètres au `main` dans un tableau de `String`, comme ceci

~~~
public static void main(String [] args) {
    ...
}
~~~

Contrairement au C, `args[0]` contient le première paramètre, le nom du programme étant toujours déductible du nom de la classe contenant le `main`.

1. Écrivez un programme `Echo.java` qui prend en entrée un nombre arbitraire de paramètres et qui les affiche un par un.


## Codage ASCII et Unicode

On va écrire un programme permettant d'afficher les codes correspondants aux caractères, et inversement. Java code ses caractères en Unicode, dont les 

1. Créez un fichier `Symboles.java`, qui prend un paramètre sur la ligne de commande et qui en affiche le premier caractère. La méthode `.charAt()` de la classe `String` permet d'extraire un caractère d'une chaîne.

1. Un `char` n'est autre que des octets de données, un `int` en
   contient quatre. Par conséquent, java permet d'assigner le contenu
   du premier au deuxième sans problèmes (mais pas l'inverse). Vous
   pouvez ainsi écrire
   
   ~~~
   char c = 'a';
   int  d = c;
   ~~~
   
   modifiez le programme `Symboles.java` pour qu'il affiche la valeur numérique du caractère entré.

1. Lorsqu'on affiche un `int`, Java montre sa valeur en base 10. Mais avec les caractères, on a plutôt l'habitude d'afficher leur codes en base 16. La fonction ` Integer.toString(d, 16)` permet d'afficher l'entier `d` en base 16. Modifiez `Symboles.java` pour qu'il affiche la valeur hexadécimale à côté de la valeur décimale.

Voici un exemple d'exécution du programme

~~~
dfl@deagol:~$ java Symboles a
a 97 61
~~~
{:.no-highlight}

Essayez avec plusieurs symboles courant. Essayez avec des symboles exotiques, comme par exemple le symbole d'euro €. Que constatez-vous?

4. Une convention courante pour les programmes destinés à être
   utilisés par la console consiste à accepter des options sous la
   forme d'un code précédé par un tiret `-`. Modifier le programme en
   sorte que lorsqu'il est invoqué comme ceci
   
   ~~~
   java Symboles -u A2
   ~~~
   
   il fonctionne en mode inverse, c'est à dire qu'il affiche le
   caractère Unicode correspondant au code hexadécimal `A2`. Si
   l'option `-u` n'est pas donnée, le programme fonctionne comme
   avant. Vous pouvez utiliser la fonction `Integer.parseInt(s, 16)`
   pour convertir un `String` en l'entier qu'il représente en base 16.
   
   **Note :** `parseInt` donne un `int`, qui est un type de données
   tenant sur 4 octets. Les caractères Unicode sont représentés en
   Java par des `char`, qui tiennent sur 2 octets. Convertir un `int`
   en `char` est donc une opération nécessairement dangereuse, qui
   risque de perdre des données, par conséquent Java va se rebeller si
   vous essayez de faire
   
   ~~~
   int d = 2;
   char c = d;
   ~~~
   
   Pour expliquer à Java que vous savez ce que vous êtres en train de faire, vous devez utiliser un *cast explicite* :
   
   ~~~
   int d = 2;
   char c = (char)d;
   ~~~

5. Ajoutez une fonction
   
   ~~~
   public static String toHexString(int d) {
       ...
   }
   ~~~

   à la classe. La fonction doit prendre en entrée un entier `d` et
   renvoyer sa représentation en hexadécimal sous forme de
   `String`. Utilisez l'algorithme de conversion de base vu au
   semestre précédent, ne vous servez pas de
   `Integer.toString`. Remplacez l'appel à `Integer.toString` par
   cette nouvelle fonction.

6. Dans le même esprit, ajoutez une fonction `static int fromHexString(String s)` réalisant la conversion inverse. Remplacez les appels à Integer.parseInt par cette nouvelle fonction.

7. Modifiez les fonctions `toHexString` et `fromHexString` pour qu'elles fassent la conversion en utilisant une base arbitraire à la place de 16. Elles doivent prendre un deuxième paramètre de type `int` qui représente la base.

<div class="show-after" data-show-after="2014-01-29">

## Solution

Cliquez sur la boîte ci-dessous pour découvrir la solution.

~~~
public class Symboles {  // Cliquez ici pour voir la solution
    /*
       Ecrit un entier en base b. Recursif, juste pour le plaisir.
     */
    public static String toString(int d, int b) {
        if (b > 36 || b < 2)
            throw new NumberFormatException("Base too small or too large: " + b);
        if (d < 0) {
            return "-" + toString(-d, b);
        } else {
            // Le chiffre le plus a gauche de d, comme un entier
            int chiffre = d % b;
            // Conversion du chiffre en code Unicode
            char c = (char)(chiffre < 10 ? 
        		    '0' + chiffre :
        		    'A' + chiffre - 10);
            // Concatenation avec le reste du nombre
            return (d >= b ? toString(d / b, b) : "") + c;
        }
    }

    /*
       Lit un entier en base b. Recursif, comme auparavant.
     */
    public static int fromString(String s, int b) {
        if (s.length() == 0)
            return 0;

        // Le code Unicode du chiffre le plus a gauche
        char c = s.charAt(s.length() - 1);
        // Conversion du code Unicode a un entier
        int chiffre;
        if (c >= '0' && c <= '9')
            chiffre = c - '0';
        else if (c >= 'a' && c <= 'z')
            chiffre = c - 'a' + 10;
        else if (c >= 'A' && c <= 'Z')
            chiffre = c - 'A' + 10;
        else
            throw new NumberFormatException("Cannot interpret string as number: " + s);

        // On controle que le nombre est coherent avec la base
        if (chiffre >= b)
            throw new NumberFormatException("Cannot interpret string as number: " + s);

        return fromString(s.substring(0, s.length() - 1), b) * b + chiffre;
    }

    public static void main(String[] args) {
        if (args.length == 0) {
            return;
        } else if (args[0].startsWith("-u") && args.length > 1) {
            // Un ajout par rapport au sujet: on prend la base en parametre
            int b = fromString(args[0].substring(2), 10);
            b = b == 0 ? 16 : b;
            int c = fromString(args[1], b);
            System.out.println((char)c + " " +
        		       toString(c, 10) + " " +
        		       toString(c, 16));
        } else {
            char c = args[0].charAt(0);
            System.out.println(c + " " + 
        		       toString(c, 10) + " " + 
        		       toString(c, 16));
        }
    }
}
~~~
{: .collapsible .collapsed}

</div>
