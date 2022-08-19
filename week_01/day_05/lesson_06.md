# Les Mocks
Comprendre comment fonctionnent les mocks sous RSpec.

## 1. Introduction
Même si [BetterSpecs](https://www.betterspecs.org/) recommande de se tenir éloigner le plus possible des mocks, il est évident que tu as quand même besoin de savoir comment cela fonctionne.

## 2.Double ? Ou Mock ?
En effet, je parle de mocks depuis le début, car c'est le terme générique consacré dans beaucoup de frameworks de tests. Et pas seulement RSpec.
Néanmoins RSpec parle plutôt de `double`. Et un `double` peut se comporter de différentes manières... notamment se comporter comme un mock.

Tu vas voir c'est assez simple.

### 2.1 Une doublure.
Je ne parle pas ici de ton blouson favori, mais bien d'une doublure de cinéma.
Au ciné, un "stunt double" (une doublure) est une personne qui se fait passer pour l'acteur ou l'actrice et qui reçoit un coup de poing, tombe d'une falaise, saute sur un train en marche depuis un hélicoptère, bref fait des cascades.

En Rspec, un "test double" c'est pareil. C'est un objet qui va remplacer l'objet que tu veux tester, et qui va prendre ~cher~ toutes les instructions qu'on souhaite.

Ce faisant, cela permet d'isoler des parties entières de ton app :
- qui seraient autrement difficiles à atteindre : comme la gestion d'erreur d'un code tiers par exemple.
- pour une couche de l'app qui n'est pas encore créée, mais dont tu as besoin.
Et bien d'autres raisons encore.

Pour initialiser un `double` il suffit dans un fichier spec d'écrire :
```ruby
mario = double('Mario')
```

### 2.2 Les différents types de double
Un double peut avoir plusieurs usages et chacun de ces usages porte un nom différent :
- stub : Retourne une réponse prédéfinie
- mock : S'attend à ce qu'on lui appelle une méthode et lancera une erreur si ce n'est pas le cas.
- Null Object : Retourne systématiquement lui-même, quel que soit ce qu'on lui fait.
- Spy : Retiens tout ce qu'on lui demande afin de mieux cafter plus tard.

Ce qui nous amène obligatoirement à la classe que possède le `double`. Car, de manière cachée et en fonction de comment tu définis ton double, RSpec a plusieurs class d'objets adaptés :
- Pure double : c'est à toi de définir totalement le comportement de cet objet.
- Partial double : c'est un objet qui existe déjà dans ton app, à qui tu rajoutes des comportements custom pour tester.
- Verifying double : c'est un objet de test qui va vérifier que l'objet réel que tu veux tester se comporte bien comme tu le décris.
- Stubbed constant : c'est une constante qui prend la place d'un module ou d'une classe le temps d'un seul test.

Et tous ces types d'objets peuvent avoir tous les types de comportements décrits précédemment (mock, stub, spy...).

Tu comprends maintenant ma réticence à te l'expliquer trop tôt dans ce cursus.

## 3. Techniquement et concrètement

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
On ne précise pas grand-chose si ce n'est qu'on utilise un double.
Et comme tous les objets Ruby, le double va accepter qu'on lui passe certaines méthodes, et va t'insulter dans d'autres cas. La différence, c'est que lorsqu'il t'insulte, il le fait avec plus d'info.

Essaye de passer la commande :
```ruby
mario.jump(on: :ennemi)
```
Le terminal te renverra qu'un `#<Double (anonymous)>` a reçu un message `:jump` avec `({on: :ennemi})`.

### 3.2. Stub
Ce qu'il faut retenir de ce paragraphe c'est qu'un stub est programmé pour répondre toujours de la même manière à une sollicitation.
Cela s'emploie bien quand on veut simuler une requête (HTTP par exemple) ou une méthode qui doit renvoyer une valeur sans avoir d'effets de bords.
Pour faire un stub, deux méthodes :

```ruby
mario = double('Character', jump: 'Ennemi killed', crouch: 'Dodge bullets') # renverra <Double (Character)> au lieu de #<Double (anonymous)>
mario.jump(on: :ennemi) # renverra 'Ennemi killed' quelque soit les arguments passés à .jump
mario.crouch # renverra 'Dodge bullets'
```

Et comme tu l'auras pressenti, on peut aussi le faire en deux temps, avec la commande `allow` :

```ruby
mario = double('Character')
allow(mario).to receive_messages(jump: 'Ennemi killed', crouch: 'Dodge bullets')
```
Cela aura exactement le même effet.
Dans la vie sauvage, tu pourras le trouver également sous la forme :
```ruby
allow(mario).to receive(:jump).and_return('Ennemi killed')
allow(mario).to receive(:crouch).and_return('Dodge bullets')
```
Mais c'est pareil.

### 3.3 Mocks
Ici, ce n'est pas la valeur de retour que l'on souhaite simuler, mais au contraire l'effet de bords.
Le mock va juste vérifier que la méthode `jump` a bien été appelée.
À la fin du test, RSpec va vérifier que les mocks ont bien été appelés, et renvoyer une erreur si ce n'est pas le cas, faisant ainsi planter votre test.

```ruby
# Imagine que mario est bien un objet existant
it 'makes mario jump' do
  expect(mario).to receive(:jump)
  bouton.press('A')
end
```
Ici on teste simplement que si on appuie sur le bouton A, cela appelle la méthode `:jump` de l'objet `mario`. Mais on se fout complètement de la valeur de retour de la méthode `jump`.

Pour le vérifier en console sans polluer ton stub de Mario tu peux simplement faire un :
```ruby
luigi = double('Luigi')
expect(luigi).to receive(:jump)
```
Cela ne fera rien. RSpec attend que quelque chose appelle la méthode `jump` sur `luigi`. Pour provoquer cette vérification à la main tu peux faire un `RSpec::Mocks.verify`.
RSpec te dira alors qu'il s'attendait à recevoir une fois la méthode, mais qu'il l'a reçu zéro fois.

Bien sûr tu n'auras pas à lancer la vérification `RSpec::Mocks.verify` dans tes tests. Ce sera lancé automatiquement à la fin de chaque exemple par RSpec.

### 3.4 Null Object
Les doubles que l'on a vus jusqu'ici sont dits "strictes". C'est-à-dire qu'il est nécessaire de déclarer à l'avance les méthodes autorisées ou non. Mais quelques fois on a besoin que le double puisse accepter n'importe quelle méthode sans broncher.
C'est le cas des null objects qui renverront systématiquement eux-mêmes quand on leur passe n'importe quelle méthode.

```ruby
yoshi = double('Yoshi').as_null_object # renverra <Double (Yoshi)> dans irb.
yoshi.eat(:apple) # renverra <Double (Yoshi)> dans irb.
yoshi.do_smth # renverra <Double (Yoshi)> dans irb.
yoshi.whatever(argument: 'bidon') # renverra <Double (Yoshi)> dans irb.
```

Je ne peux faire mieux pour expliquer les nulls objects que de traduire littéralement [Effective Testing with RSpec 3](https://pragprog.com/titles/rspec3/effective-testing-with-rspec-3/).

Les null objects ne font rien, peuvent remplacer n'importe quoi, et peuvent satisfaire absolument n'importe quelle interface.

### 3.5 Spies
C'est l'objet le plus subtil de la liste.
Commence par copier-coller ces quelques lignes dans ton irb :
```ruby
class Game
  def self.play(character)
    character.jump
  end
end
```

ensuite, crée un personnage :
```ruby
peach = double('Princess Peach')
```
et regarde si elle reçoit la méthode `:jump` :
```ruby
expect(peach).to receive(:jump)
```

Et maintenant tu peux lancer le jeu :
```ruby
Game.play(peach)
```

Cela aura pour effet de lancer la méthode `:jump` sur `peach` et donc de satisfaire le expect.

Alors c'est effectivement un peu bizarre de d'abord `expect` quelque chose, puis d'exécuter les commandes. Mais en réalité ce que tu viens de dire à RSpec c'est :
- peach sera un double
- je m'attends à ce qu'elle reçoive la méthode `:jump`, donc espionne-la.
- Je fais un truc qui fait qu'elle reçoit jump (ou pas).
- RSpec me fait un rapport d'espionnage (le test passe, ou bien il fail).

Pour rendre ça plus lisible, tu peux utiliser les deux autres manières :

```ruby
peach = double('Princess Peach').as_null_object
Game.play(peach)
expect(peach).to have_received(:jump)
```
Note qu'ici on doit dire que `peach` est un null_object.
Une autre manière de faire est encore la suivante :

```ruby
peach = double('Princess Peach') # un double classique
allow(peach).to receive(:jump) # mais on déclare explicitement ce qu'on s'attend à recevoir.
Game.play(peach)
expect(peach).to have_received(:jump)
```
Dans ce dernier exemple, on perd l'intérêt d'utiliser un spy, puisqu'on déclare explicitement ce qu'on s'attend à recevoir.
Il vaut mieux utiliser la manière avec `as_null_object`.

Mais dans ce cas, RSpec a pensé à toi, et t'offre la méthode `spy` directement :

```ruby
peach = spy('Princess Peach')
Game.play(peach)
expect(peach).to have_received(:jump)
```

Qui finalement est encore la plus élégante je trouve.

## 4. Pure, Partial, Verifying ou stubbed ?
Cette section est un peu plus avancée et tu n'en as pas besoin pour faire le projet du jour.

Mais puisque tu es encore en train de lire ces lignes, voici les différences entre les différents types de `double` en RSpec.

### 4.1 Pure doubles
Tout ce qu'on a vu jusqu'ici était basé sur des `pure double`. C'est facile, ça se manipule facilement et on peut en faire ce que l'on veut. C'est parfait pour simuler des objets qui n'existent pas encore.

### 4.2 Partial doubles
À l'inverse, souvent, les objets ont déjà été codés et il ne leur manque plus qu'une fonctionnalité ou deux. Ou encore, les objets existent, mais on ne peut contrôler leur valeur de retour au sein du test : `Time.now`, ou `Random.new` par exemple.
Dans ce cas, on se sert de partial doubles :

```ruby
random = Random.new
allow(random).to receive(:rand).and_return(0.1234)
random.rand # affichera 0.1234 comme demandé. Pourtant random est bien un objet de la class Random qui existe bel et bien en ruby.
# toutes les autres méthodes de l'objet random fonctionneront comme indiqué dans la documentation de Ruby.
```

### 4.3 Verifying doubles
L'avantage jusqu'alors c'est que nos doubles pouvaient prendre la forme que l'on voulait. Le problème avec ça, c'est que souvent, comme ton code va évoluer, tes tests pourraient ne plus refléter la réalité.
Les Verifying doubles peuvent se prémunir de cela en "vérifiant" littéralement si l'objet en question en est bien capable.

Pour donner un exemple, tout à l'heure on a utilisé `peach` et on a cherché à vérifier qu'elle recevait bien la méthode `:jump`. Mais imaginons  maintenant qu'on a codé la class Character, ce n'est plus, `:jump` mais `:bounce` qu'on utilise.

Eh bien, tes tests continueront de passer. Puisque tu simules le comportement de peach.
Pour éviter cela, il faut appeler `instance_double` à la place de `double` soit `peach = instance_double('Princess Peach')`. Le reste est identique.
Et RSpec vérifiera que peach peut bien recevoir la méthode `:jump` si tant est que sa class existe.

Note que, dans un tel cas, avec un double classique, ton test unitaire continuait à passer, mais normalement ton test d'acceptance aurait planté. Donc ce n'est pas si grave que cela.

Pour que cela fonctionne correctement, il existe plusieurs Verifying doubles :
- `instance_double('SomeClass')` : qui reprendra l'interface de `SomeClass` et permettra d'accéder aux méthodes d'instances.
- `class_double('SomeClass')` : qui reprendra toutes les méthodes de class.
- `object_double(some_object)` : qui reprendra toutes les méthodes de l'objet. Pratique en cas de structures bizarres avec des Struct, ou de la méta programmation.

### 4.4 Stubbed constants
Ce dernier est le plus facile à comprendre. On gère ici les constantes. Et donc même les variables d'environnement. Pratique quand on veut tester un environnement de prod, dans un environnement de dev par exemple.

Je ne vais pas y aller par 4 chemins, un petit snippet te montrera comment ça marche.
```ruby
class Maths
 CONSTANT_PI = 3.14
end

stub_const('Maths::CONSTANT_PI', 123456)

puts Maths::CONSTANT_PI # Renverra 123456 et non 3.14
```


## 4. Points importants à retenir
Dans cette ressource, on a appris à utiliser les différents types d'objets offerts par RSpec et on a appris non seulement à les différencier, mais également à les utiliser.
Il faut retenir que
- double est la méthode qui permet de définir ces objets.
- Stub : renvoie une valeur prédéfinie à une méthode prédéfinie.
- mock : vérifie qu'une méthode a bien été appelée s'assurant ainsi de l'effet de bord plutôt que de la valeur de retour.
- null_object : renvoie lui-même quoiqu'on lui fasse.
- spy : vérifie les appels passer ou non à un objet réel après coup.

## 5. Pour aller plus loin
Pas de mystères :
- la documentation de RSpec,
- et [Effective Testing with RSpec 3](https://pragprog.com/titles/rspec3/