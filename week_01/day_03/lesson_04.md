# 100% coverage
On verra ensemble ici ce qu'est le coverage et comment en installer un.

## 1. Introduction
Cette ressource va se baser sur [SimpleCov](https://github.com/simplecov-ruby/simplecov). Un outil de coverage largement utilisé dans le monde de Ruby.
On va apprendre à l'installer, à s'en servir sur un projet simple, et surtout à comprendre ce qu'il te raconte.

## 2.Historique et contexte
Le coverage c'est quoi ?
Le coverage c'est le pourcentage de lignes de ton code qui sont couvertes (covered) par un test.
Ainsi dans le code suivant :
```ruby
def great_method(argument)
  if argument.is_a? Integer
    return true
  else
    return "l'argument est un #{argument.class}"
  end
end
```

Si on écrit le test suivant :
```ruby
describe '#great_method' do
  context 'when argument is an integer' do
    let(:argument) { rand(0..10) }
    it 'returns true' do
      expect(great_method(argument)).to be(true)
    end
  end
end
```

Un bon code coverage te dirais que la ligne 5 de ton code :
```ruby
return "l'argument est un #{argument.class}"
```
n'est pas testée.
Il te dirait donc probablement quelque chose comme 50% de code coverage. Un cas est testé, pas l'autre.

Les ligne
```ruby
def great_method(argument)
```
ou encore
```ruby
end
```
n'ont pas à être testé par nature. Cela parait évident mais il faut le dire.

Voilà comment fonctionne grosso merdo un outil de coverage.

Il en existe plein : SonarQube, Track Ruby, SonarCloud, Codecov. Il existe même des [extensions VS Code](https://marketplace.visualstudio.com/items?itemName=markis.code-coverage) qui te souligne les parties du code qui ne sont pas couvertes par des test.
C'est un peu comme les IDE, chacun sa paroisse... Pour ce cursus on utilisera SimpleCov.

Pourquoi ?
Parce que c'est le premier que j'ai installé de ma vie.

## 3. SimpleCov

### 3.1. Installation
Grosso merdo ici on va simplement suivre [la doc](https://github.com/simplecov-ruby/simplecov) de SimpleCov.
On va commencer par l'ajouter à notre Gemfile. On le mettra dans le groupe de test dans le cas d'une app rails.
```ruby
gem 'simplecov', require: false
```

Ensuite, dans le `spec_helper.rb` on va ajouter ces quelques lignes directement tout en haut du fichier. Attention, tout ce qui sera au dessus de ces deux lignes, ne sera pas pris en compte par SimpleCov. Donc si tu require un autre fichier, tous les tests associés ne seront pas pris en compte.
```ruby
require 'simplecov'
SimpleCov.start
```

A partir de là, SimpleCov est opérationnel. Si tu lance la commande `bundle exec rspec` ou même simplement `rspec`, SimpleCov va créer un dossier `./coverage` dans lequel il y aura plein de fichiers.
Bien sûr on ne va pas versionner ces fichiers, cela n'a pas de sens.
Tu peux donc exécuter la commande suivante pour rajouter tout ce merdier au gitignore.

```ruby
echo coverage >> .gitignore
```

### 3.2. Comprendre ce que ça raconte
#### 3.2.1 Résultat du coverage
Déjà quand tu lances RSpec, tu constate une chose : à la fin de l'éxécution, RSpec te rajoute l'indication `(xx%) covered`. C'est le coverage global de ton projet.

D'une manière générale, tu peux aussi ouvrir le fichier `./coverage/index.html`.
Dedans, tu y trouvera les infos suivantes :
Le nombre de fichiers total concernés, le nombre de lignes pertinentes pour le test coverage (un commentaire n'est pas une ligne pertinente par exemple).
Puis, dans un tableau, la liste des fichiers et leurs chemin d'accès et les ratio de testing.
Pour chaque ligne tu peux cliquer sur le nom d'un fichier pour en savoir plus.

Lorsque tu fais ça, SimpleCov te montre directement les lignes couvertes (en vert) ou non couvertes (en rouge) par un test. C'est simple.

#### 3.2.x Ca veut dire quoi en vrai un coverage.
Alors voilà une vraie question qu'il faut se poser.

Reprenons notre méthode d'hier :
```ruby
def is_3?(number)
  return false unless number.is_a? Integer
  return true if number == 3
end
```

Je te propose en exercice de m'écrire deux test :
- On s'attend à ce que `is_3('toto')` soit false
- On s'attend à ce que `is_3(3)` soit true

Je te conseille de bien prendre le temps de te créé un mini repo avec RSpec dedans et tout, et de vraiment faire l'exo. Cela t'entrainera pour le projet.

C'est bon ?

maintenant fait fonctionner RSpec sur ce mini repo et regarde quel est ton coverage.
Si tu as écrit uniquement les tests précédents tu dois avoir quelque chose de ce genre :
```ruby
context 'covers it all' do
  it 'returns false' do
    expect(is_3?('toto')).to be(false)
  end
  it 'returns true' do
    expect(is_3?(3)).to be(true)
  end
end
```
et un coverage de 100%...


Sauf qu'il te manque un cas.
Celui où la méthode renvoie `nil`.

Ainsi c'est la conclusion de cette ressource. Attention : 100% de Coverage ne veut pas dire que tes tests couvrent tous les cas.

## 4. Points importants à retenir
On a vu comment installer SimpleCov, un outil de coverage parmis tant d'autres.
On a vu comment s'en servir et comment lire ses résultats.
On a vu surtout que 100% de coverage ne veut pas dire que tu as bien pensé tous les cas. A l'inverse, à quoi bon faire 100% de coverage si c'est pour tester une méthode qui fait juste un print dans un log serveur ?

Tu verras que cela dépendra des boîtes avec lesquelles tu travaille.

En ce moment, la mode est à l'augementation du coverage à chaque PR, en visant le 70/80% in fine.

## 5. Pour aller plus loin
Maintenant, tu as assez de compétences pour rentrer dans le fameux débat : 100% de coverage or not 100% de coverage.
Tu peux donc lire et réfléchir sur ce genre de [réponses SO](https://stackoverflow.com/questions/3123777/is-100-code-coverage-a-really-good-thing-when-doing-unit-tests).

Je t'invite à rentrer dans le débat avec des séniors et des alumnis sur le Discord, voir ce qu'ils en pensent. Tu te forgeras ton opinion au gré de ton expérience. Et tu verras aussi qu'elle changera avec le temps.
