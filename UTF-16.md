---
layout: post
title: UTF-16
---

UTF-16 est un standard de codage à longueur variable pour Unicode, successeur de UCS-2. Il a été introduit par le standard ISO/IEC 10646 afin de dépasser les limitations de UCS-2 en matière de caractères représentables. Il est en train d'être définitivement remplacé par [UTF-8](UTF-8).


## Unicode et UCS-2

Unicode est un standard permettant de représenter les symboles d'une grande partie des alphabets connus, plus d'autres utilisés dans différentes branches (mathématique, ingénierie, etc.).

À chaque symbole Unicode est associé un entier, appelé en anglais son *code point*, dans l'intervalle de `0x0` à `0x10FFFF` (`0x` indique un nombre hexadécimal). Les caractères Unicode sont souvent représentés par `U+` suivi du code point, par exemple `U+0058` est le caractère 'X' de l'alphabet latin.

Unicode est subdivisé en dix-sept *plans* (*planes* en anglais), de $$2^{16}$$ code points chacun, ce qui fait un total de 1.112.114 codes possibles (en ce moment, environ 10% de ces codes sont utilisés). Parmi les plans on distingue le *Basic Multilingual Plane (BMP)*, contenant les symboles les plus courants et dont les code points vont de `U+0000` à `U+FFFF`, le *Supplementary Multilingual Plane*, allant de `U+10000` à `U+1FFFF`, et le *Supplementary Ideographic Plane*, allant de `U_20000` à `U+2FFFF`. Les symboles ASCII, plus précisément ceux du jeu ISO-8859-1, font partie du Basic Multilingual Plane et ont code points de `U+0000` à `U+00FF`.

L'encodage UCS-2 est capable de représenter seulement les codes points du BMP, et fait cela en encodant les caractères par leur code point sur deux octets.


## Codes subrogés et UTF-16

UTF-16 est un standard de codage qui permet de représenter tous les code points de Unicode. Les codes points du BMP sont représentés sur 2 octets, comme dans UCS-2, les codes points des plans supplémentaires sont représentés sur 4 octets.

Afin de permettre un décodage unique, une partie du BMP, la plage allant de `U+D800` à `U+DFFF`, est réservée pour ce que l'on appelle *codes subrogés*. Ces codes ne représentent aucun vrai symbole : ils sont utilisés pour encoder les code points des symboles des plans supplémentaires.

La présence des codes subrogés fait de UTF-16 un *code instantané*, en ce sens que n'importe quelle paire d'octets au milieu d'un flux UTF-16 peut être immédiatement reconnue comme étant un symbole du BMP, ou bien un code subrogé (*haut* ou *bas*) faisant partie d'un symbole encodé sur 4 octets.


### Algorithme

La longueur variable impose la nécessité de définir un algorithme de codage/décodage pour UTF-16. Étant donné un code point Unicode, son encodage UTF-16 est encodé sur 2 ou 4 octets comme suit:

- Si le code point appartient au BMP, il est écrit tel quel dans 2 octets. Ceci inclut tous les code points de `U+0000` à `U+D7FF` et de `U+E000` à `U+FFFF`, mais exclut les codes subrogés de `U+D800` à `U+DFFF` qui sont réservés pour un usage interne.

- Si le code point n'appartient pas au BMP, sont code est compris entre `U+10000` et `U+10FFFF`. L'algorithme procède ainsi:

    - On soustrait `0x10000` du code point, obtenant un code compris entre `0x0` et `0xFFFFF`.
    - Le résultat de l'opération précédente tient sur 20 bits. On coupe les 20 bits en deux blocs de 10 bits chacun, appelés *partie haute* (les bits plus significatifs) et *partie basse* (les bits moins significatifs).
    - On ajoute les bits haut à `0xD800`, en obtenant ainsi un code subrogé compris entre `U+D800` et `U+DBFF`, appelé *subrogé de tête*.
    - On ajoute les bits bas à `0xDC00`, en obtenant ainsi un code subrogé compris entre `U+DC00` et `U+DFFF`, appelé *subrogé de queue*.

    Le symbole Unicode est encodé par le subrogé de tête écrit sur 2 octets, suivi par le subrogé de queue écrit sur 2 octets.


### Exemple

Les symboles '0' (zero) et '€' (euro) font partie du BMP et on codes, respectivement, `U+0030` et `U+20AC`. Ils sont encodés par UTF-16 sur deux octets, respectivement (`0x00`, `0x30`) et (`0x20`, `0xAC`).

Le symbole 😃 a pour code point `U+1F603`. Voici l'algorithme appliqué pas par pas.

- On soustrait `0x10000`, ce qui donne `0xF603`.
- On coupe `0xF603` en deux moitiés de 10 bits chacune. La partie haute donne `0x03D`, et la partie basse `0x203`.
- Le subrogé de tête est obtenu en additionnant `0x03D` à `0xD800`, ce qui donne le subrogé `U+D83D`.
- Le subrogé de queue est obtenu en additionnant `0x203` à `0xDC00`, ce qui donne le subrogé `U+DE03`.

Le symbole 😃 est alors encodé par la suite de quatre octets (`0xD8`, `0x3D`, `0xDE`, `0x03`).

