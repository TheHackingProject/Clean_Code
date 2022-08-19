# Variables et use cases
On va voir ici comment DRY son code en RSpec

## 1. Introduction
En RSpec, comme en Ruby, on peut créer des variables. On va voir pourquoi RSpec a réinventé la roue, et surtout pourquoi ça va te sauver la vie.

## 2.Historique et contexte
On est d'accord que j'aurais beau écrire le meilleur cursus de la planète, le testing c'est quand même pas le truc le plus drôle. Quoique.

Mais, l'idée c'est que ce soit le moins pénible possible, car à la moindre friction, tes collègues, ou toi même allez avoir envie de ne pas écrire ni modifier les tests.

Et ça, ce n’est pas ce que tu souhaites. Tu souhaites que ce soit fastoche à écrire, mais aussi fastoche à relire, corriger et modifier.

Et donc, imagine la situation suivante en rails. T'as un model User avec plein de méthodes différentes. Chaque méthode modifie les attributs du User.

Tu pourrais très bien te dire que tu vas créer un User. Et à chaque test, vérifier que la modif est bien faite...
Tu vas donc écrire un truc du genre :
```ruby
user =  User.create(...) # avec les bons attributs

describe '#method_1' do
  it 'changes something' do
    user.method_1
    expect(user).to have_attributes(...) # les attributs dont tu espères le changement avec leurs nouvelles valeurs.
  end
end
```
Mais dans ce cas tu fais quoi pour la méthode 2 ? Tu recrées un nouveau User ? Tu remodifies à la main le `user` précédent pour pouvoir refaire un test dessus ?

### 2.1 Les pain points
#### 2.1.1 Orga et ordre des tests
Utiliser des variables "the Ruby way" a deux problèmes.
Le premier c'est que comme tu t'en doutes, il faut gérer "À la main"™ les changements effectués, au risque d'avoir des tests qui pètent et de se retrouver à débuguer ses tests...
On ne va quand même pas écrire des tests de tests, non ?

Autre problématique que cela engendre, en admettant que tu sois cador de l'organisation, cela impliquerait que tes tests ne passent plus QUE dans un certain ordre.

Et ça : c'est NO GO.
On n'en parlera pas à Guillaume, mais en vrai les tests, c'est mieux s'ils passent dans un ordre aléatoire. Si tu ouvres un `spec_helper.rb` tu verras qu'il y a, commenté, la configuration suivante :
```ruby
# Run specs in random order to surface order dependencies. If you find an
  # order dependency and want to debug it, you can fix the order by providing
  # the seed, which is printed after each run.
  #     --seed 1234
  config.order = :random
```
Cela permet de trouver les bugs provoqués par une suite de commande (requêtes HTTP par exemple) qui provoquerait un bug.
Cette config fait que RSpec lancera donc les tests dans un ordre aléatoire, et te rendra à l'issue, qu'il y ait une failure ou non, un code d'ordre, nommé `--seed`. Si tu le veux tu pourras relancer ta suite de test exactement dans le même ordre en faisant :
```console
bundle exec rspec --seed xxx
```
Où xxx est le numéro du seed que RSpec t'avait donné.

Ce n’est pas évident à comprendre ni à voir. Si comme moi tu es de nature visuelle, voici un exemple qui devrait te plaire :

```ruby
class Article
  # La table Article :
  # Id      Integer
  # Name    String
  # Content Text_Area
  # -----------------

  belongs_to :category
end

RSpec.describe Article do
  it 'can create a new article' do
    Article.create(name: 'un article', content: '')
    expect(Article.count).to eq(1) # On a créé un article en faisant ça.
  end

  it 'can create an article with a category' do
    category = Category.create(name: 'sport')
    article = Article.create(name: 'autre article', content: '', category: category)
    expect(Article.last).to have_attributes(category: category)
  end
end
```

Dans le sens de lecture : tout se passe bien. Dans le sens inverse, l'example `'can create a new article'` va fail car il y aura à cet instant 2 articles en base et non un seul.

#### 2.1.2 Les hooks.
T'es super smart et tu te dis que pour faire des tests DRY, comme le martini, tu vas utiliser les hooks.
```ruby
before(:example) do
  user =  User.create(...) # avec les bons attributs
end

describe '#method_1' do
  it 'changes something' do
    user.method_1
    expect(user).to have_attributes(...) # les attributs dont tu espère le changement avec leurs nouvelles valeurs.
  end
end
```
Hop !! Plus de problèmes, à chaque test on crée un User tout neuf et hop on le met dans la variable `user` !
Génial : job done on part au bistro commander un martini au shaker (pas à la cuillère).

Sauf qu'avant de te prendre pour James Bond, je ne vais t'enseigner un truc.

Chaque fois que ton hook sera exécuté (donc à chaque test de ton fichier spec), un User est créé... et persistera en base de données.

On s'en fout, c'est la base de test en local me diras-tu... oui, sauf qu'au bout de 3 ans ta base elle fait 500Go si tu ne penses pas à la nettoyer, et tes requêtes deviennent de plus en plus longues...
Et finalement ta suite de test sur toute l'appli mettra 2h à se lancer et s'exécuter.

Ça non plus : ce n'est pas ce que tu souhaites. Ni pour toi ni pour tes collègues.

Bien sûr tu peux englober chaque test dans une [transaction](https://api.rubyonrails.org/v7.0.2.3/classes/ActiveRecord/Transactions/ClassMethods.html), clean ta base à l'issue de toute la suite de tests via une config utiliser une gem, etc.

Mais franchement je ne vais pas te mentir, y'a sacrément plus élégant et pratique.

## 3. Let, Let! et Subject
Pour pallier ces précédentes problématiques, chez RSpec, ont été inventés des helpers : `let` et `let!`.

On verra également comment se servir d'un truc génial : les subjects.

### 3.1. Let
Le `let` c'est un peu la variable en RSpec. En fait c'est un helper qui va prendre en argument un symbol, et un block.
L'argument va servir à nommer la "variable", et le block va servir à stocker le "contenu" de cette "variable".
Note bien que je mets des guillemets partout.

Ce n’est pas pour rien.

En fait `let` permet de mettre en cache le contenu du block pour le réutiliser à travers le même example.
C'est une phrase violente pour dire que le contenu du block sera appelé à chaque example.

Démonstration :
```ruby
let(:start_value) { 0 }

it 'calls the correct variable' do
  expect(start_value).to eq(0)
end
```

Dans cet exemple, on crée une variable que l'on nomme `:start_value`. Pour l'utiliser, on peut simplement écrire `start_value` sans les ":".
Sa valeur c'est `0`.

Super.

Maintenant une petite subtilité :

```ruby
let(:start_value) { 0 }
let(:count) { start_value += 1 }

it 'calls the correct variable' do
  expect(count).to eq(1)
end
```

Jusque là, ça se comprend assez bien. On comprend que quand on appelle `count` on rajoute 1 à `start_value`.

Sauf que :

```ruby
let(:start_value) { 0 }
let(:count) { start_value += 1 }

it 'calls the correct variable' do
  expect(count).to eq(1)
  expect(count).to eq(1)
end
```

Ici, il faut bien remarquer une chose : on a appelé `count` une première fois. Donc `start_value == 1` puis, on rappelle une deuxième fois la variable `count` juste après. Donc logiquement `count` devrait valoir `start_value + 1` soit `2`. Sauf que non.

Les valeurs de `start_value` mais surtout de `count` ont été mises en cache lors du premier appel aux variables respectives. Ensuite, leurs valeurs respectives ne changent plus.

Relisons ces quelques lignes tranquillement :
```ruby
let(:start_value) { 0 }
let(:count) { start_value += 1 }
```
Ici RSpec ne fait rien... mais sait qu'il existe potentiellement deux variables portant les noms de `start_value`, `count`.

```ruby
it 'calls the correct variable' do
  expect(count).to eq(1)
```
Ici RSpec constate qu'on utilise `count`, il va donc chercher la "valeur" de count : `start_value += 1`.
Damned, pour ça il faut instancier la valeur de `start_value` puisque c'est la première fois qu'on l'utilise.
Donc RSpec exécute `start_value = 0` (première ligne), puis `count` qui vaut `start_value += 1` soit 1.

À ce stade donc, RSpec a lu 1 fois chacune des variables. Les a mis en cache pour le reste de l'exemple. Une des variables exécutant une modif sur l'autre variable, RSpec l'a fait aussi.

au deuxième appel de
```ruby
expect(count).to #...
```
RSpec ne rejoue pas le contenu de `count`. RSpec ne rejoue donc pas `start_value += 1`.

Donc on a toujours `start_value = count = 1`.


En revanche, RSpec remet rigoureusement à zéro les "variable" à chaque example.
Ainsi :

```ruby
let(:start_value) { 0 }
let(:count) { start_value += 1 }

it 'calls the correct variable' do
  expect(count).to eq(1)
  expect(count).to eq(1)
end

it 'does not cache through mutliple example' do
  expect(start_value).to eq(0)
end
```

Ce qui fait que c'est super pratique pour réinitialiser le contenu de tes variables entre chaque test, ou chaque example si tu préfères.

Malin, non ?

C'est également cette remise à zéro qui permet de ne pas persister en base de données tes objets rails que tu pourrais créer. Ainsi, entre ça et la mise en cache, tes tests continueront d'être optimisés, et rapide à exécuter. À toi les remerciements de tes collègues !

### 3.2. Let!
C'est la même chose que `let`. À l'exception près que `let`, sans le "!", est ce qu'on appelle Lazy-Loaded.
En gros RSpec ne s'en occupera QUE s'il le rencontre.

C'est donc super pratique, car par exemple :
```ruby
let(:never_used) { User.new }

it 'does not call the correct variable' do
  # Random other code.
end
```
Dans ce cas, la "variable" `never_used` n'est jamais lue... donc elle n'est en fait jamais créée non plus... donc la base de données n'est jamais affectée par un `User.new`.
C'est donc juste une ligne de code en trop : sans aucun impact pour tes perfos ! Stylay !!!


En revanche :
```ruby
let!(:never_used) { User.new }

it 'does not call the correct variable' do
  # Random other code.
end
```

Là cette fois, quoiqu'il arrive, que tu t'en serves ou non : `never_used` est belle et bien utilisée, instanciée et mise en cache.

C'est la différence entre `let` et `let!`

Bon il y a bien sûr d'autres subtilités. Mais pour être franc, je les ai découvertes en écrivant ces lignes, et ce n'est vraiment pas primordial.


### 3.3 Félicitations : c'est un très beau block !
Petit rappel si tu t'es endormi : on fait du ruby.

Donc
```ruby
let(:block_of_hell) do
  # Plein de code de la mort !!
end
```
ça marche aussi 😉️


### 3.3. Subject
Un autre outil super cool c'est le `subject`.
On en a parlé dès le premier jour, le subject c'est exactement comme le sujet de la phrase `expect(subject).to be(something)`.
"Le chien doit être poilu" => `expect(dog).to be_furry`

OK... mais RSpec a poussé le bouchon jusqu'à en faire un objet à part entière.

### 3.3.1 Il y a toujours un subject....
En vrai, quand on écrit un `expect` il y a un sujet qui est implicite.
C'est celui que l'on renseigne à la toute première ligne de son fichier `_spec.rb` :
```ruby
RSpec.describe "Ici c'est le sujet" do
```
Ici donc le sujet : c'est une chaîne de caractères.

OK, mais ça t'avance à quoi ? à écrire un test de cette manière :
```ruby
it 'has a String class' do
  is_expected.to be_a(String)
end
```
C'est plus simple, mais ce n'est pas là le vrai avantage.

La vraie classe c'est d'écrire son fichier de Spec de cette manière :
```ruby
# Imagine que tu as une classe comme ça :
class MaClassQueJeVeuxTester
  def initialize
    puts "Je suis initialisé"
  end

  def another_cool_method
    'Yeaaaah'
  end
end


RSpec.describe MaClassQueJeVeuxTester do
  it 'Yell at initialization' do
    expect(subject).to be_a(Class) # Oui : subject == MaClassQueJeVeuxTester
  end
end
```

Pas besoin d'expliquer à RSpec ce que c'est que le `subject` il sait que c'est ce qu'il y a dans le `RSpec.describe` en première ligne

Et tu as même un autre helper super stylay :
`described_class`

```ruby
expect(described_class.new.another_cool_method).to eq('Yeaaah')
```

Ça, c'était le sujet implicite.


### 3.3.2 ... mais des fois tu veux le changer.
Et dans ces cas-là, il y a le sujet explicite.

Parce que oui bien sûr RSpec te donne la possibilité de changer le subject comme tu veux.
Démonstration :

```ruby
RSpec.describe 'On ne se sert pas du sujet implicite' do
  subject { [1, 2, 3] }

  it 'returns a array of three integers' do
    expect(subject).to start_with(1).and end_with(3).and include(2)
  end
end
```

Le truc ultime c'est que tu peux lui donner un petit nom en prime :

```ruby
RSpec.describe 'On ne se sert pas du sujet implicite' do
  subject(:array_of_three) { [1, 2, 3] }

  it 'returns a array of three integers' do
    expect(array_of_three).to start_with(1).and end_with(3).and include(2)
  end
end
```

Ainsi tu peux en créer plusieurs.


### 3.3.3 Attention au bug de l'espace.
Il y a tout de même un piège à abuser de Subject. En fait le subject explicite est un helper qui va exécuter la commande située entre les accolades `{ du code ruby }`.

Par voie de fait tu pourrais très bien tomber dans ce piège :

```ruby
RSpec.describe 'Piège en haut subject' do
  element_list = [1, 2, 3] # On créé un array. Chouette.

  subject { element_list.pop } # Le subject c'est de virer le dernier élément du tableau : 3

  it "is done only once in a given example" do
    expect { 3.times { subject } }.to change{ element_list }.from([1, 2, 3]).to([1, 2])
    # On voit que le tableau reste à [1, 2] même si on exécute plusieurs fois le subject.
    expect(subject).to eq(3)
    # On voit que le subject reste égal à 3 (le résultat de element_list.pop)
  end

  it "is saved acrossed example" do
    expect{ subject }.to change{ element_list }.from([1, 2]).to([1])
    # En revanche, RSpec à retenu l'exécution du subject d'avant... et du coup comme element_list à changé, et RSpec
    # exécute à nouveau le subject....
    expect(subject).to eq(2)
  end
end
```

Ce n’est pas bien méchant, mais si tu ne le sais pas tu peux chercher longtemps...


### 3.3.3 En vrai à quoi ça sert ?
À DRY ton code pardi !

L'idée est toujours la même : il faut que ce soit le plus facile à lire, et le plus facile à maintenir.

Des fois, il vaudra mieux se passer d'un subject pour être clair.
Des fois il vaudra mieux utiliser le subject afin de libérer de l'espace de lecture pour les autres variables ou use case...

Ce sera à toi de voir.

Mais je ne pouvais pas te laisser sans cette ressource.


## 4. Points importants à retenir
On a vu que RSpec offre des helpers qui permettent d'éviter de créer des variables à la main, qui seraient galères à maintenir au bout de plusieurs tests.
 `let(:nom_du_helper) { 'Code à exécuter lors d'un appel du helper' }`

On a vu qu'il fallait faire attention au caching de ces helpers, mais que bien utiliser cela permet de vraiment séparer les responsabilités de chacun de tes tests.


Ensuite, on a vu que, comme pour n'importe quelle phrase, RSpec considère qu'il y a toujours un `subject` pour accompagner le verbe `expect`.
Ce `subject` est directement la valeur de ce qu'on place dans le describe précédent la ligne `expect(...)`. Cela peut donc être nil ou une simple chaîne de caractères.

Si on place une class en `describe` on peut profiter de helper stylay du style : `described_class` afin d'alléger la lecture de son code.

On peut aussi créer explicitement le subject en lui donnant un nom avec la commande `subject(:le_nom_du_sujet) { Code Ruby à exécuter }`, et on fait attention que ce code ne sera exécuté qu'une seule fois maximum par exemple.

## 5. Pour aller plus loin
[La doc de `let` et `let!`](https://relishapp.com/rspec/rspec-core/v/2-11/docs/helper-methods/let-and-let)
Comparaison before vs let vs let!
[La doc des `Subject`] (https://relishapp.com/rspec/rspec-core/v/3-10/docs/subject). Tu verras, il y a certaines subtilités intéressantes.
