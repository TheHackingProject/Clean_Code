# Les Mocks
Comprendre comment fonctionne les mocks sous RSpec.

## 1. Introduction
Même si [BetterSpecs](https://www.betterspecs.org/) recommande de se tenir éloigner le plus possible des mocks, il est évident que tu as quand même besoin de savoir comment cela fonctionne.

## 2.Double ? ou Mock ?
En effet, je parle de mocks depuis le début, car c'est le terme générique consacré dans beaucoup de frameworks de tests. Et pas seulement RSpec.
Néanmoins RSpec parle plutôt de `double`. Et un `double` peut se comporter de différentes manière... notamment se comporter comme un mock.

Tu vas voir c'est assez simple.

### 2.1 Une doublure.
Je ne parle pas ici de ton blouson favoris, mais bien d'une doublure de cinéma.
Au ciné, un "stunt double" (une doublure) est une personne qui se fait passer pour l'acteur ou l'actrice et qui reçoit un coup de poing, tombe d'une falaise, saute sur un train en marche depuis un hélicoptère, bref fait des cascade.

En Rspec, un "test double" c'est pareil. C'est un objet qui va remplacer l'objet que tu veux tester, et qui va prendre ~cher~ toutes les instructions qu'on souhaite.

Ce faisant, cela permet d'isoler des parties entière de ton app :
- qui seraient autrement difficile à atteindre : comme la gestion d'erreur d'un code tiers par exemple.
- pour une couche de l'app qui n'est pas encore créée mais dont tu as besoin.
et bien d'autres raisons encore.

Pour initialiser un `double` il suffit dans un fichier spec d'écrire :
```ruby
mario = double('Mario')
```

### 2.2 Les différents types de double
Un double peut avoir plusieurs usages et chacun de ces usages porte un nom différent :
- stub : Retourne une réponse prédéfinie
- mock : S'attend à ce qu'on lui appelle une méthode et lancera une erreur si ce n'est pas le cas.
- Null Object : Retourne systématiquement lui même, quelque soit ce qu'on lui fait.
- Spy : Retiens tout ce qu'on lui demande afin de mieux cafter plus tard.

Ce qui nous amène obligatoirement à la classe que possède le `double`. Car, de manière cachée et en fonction de comment tu défini ton double, RSpec a plusieurs class d'objets adaptées :
- Pure double : c'est à toi de définir totalement le comportement de cet objet.
- Partial double : c'est un objet qui existe déjà dans ton app, à qui tu rajoute des comportements custom pour tester.
- Verifying double : c'est un objet de test qui va vérifier que l'objet réel que tu veux tester se comporte bien comme tu le décris.
- Stubbed constant : c'est une constante qui prend la place d'un module ou d'une classe le temps d'un seul test.

Et tout ces types d'objets peuvent avoir tous les types de comportements décrits précédemment (mock, stub, spy...).

Tu comprends maintenant ma réticence à te l'expliquer trop tôt.

## 3. Techniquement et concrêtement

### 3.1. Le cas générique.
Lance une console irb et dedans, tape :
```ruby
require 'rspec/mocks/standalone'
```
Cela doit te renvoyer `true` pour te confirmer que tu pourras lancer toutes les commandes de ce cours.

Lorsqu'on écrit
```ruby
mario = double
#<Double (anonymous)>
```
on ne précise pas grand chose si ce n'est qu'on utilise un double.
Et comme tous les objets Ruby, le double va accepter qu'on lui passe certaines méthodes, et va t'insulter dans d'autres cas. La différence, c'est que lorsqu'il t'insulte, il le fait avec plus d'info.

Essaye de passer la commande :
```ruby
mario.jump(on: :ennemi)
```
Le terminal te renverra qu'un `#<Double (anonymous)>` a reçu un message `:jump` avec `({on: :ennemi})`.

### 3.2. Deuxième sous partie


## 4. Points importants à retenir

## 5. Pour aller plus loin