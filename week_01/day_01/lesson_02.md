# Décryptage d'un fichier RSpec.
Dans cette ressource on va partir d'un fichier RSpec très simple et on va le disséquer pour mieux comprendre de quoi on parle au juste.


## 1. Introduction
En entrée, l'idée c'est qu'on soit d'accord sur le vocabulaire et les possibilités offertes par RSpec.

Pas le temps de digérer qu'en guise de plat, tu vas manger du "matcher" à tire la rigo. Et attention, c'est sans sauce. Ce sera donc un peu sec à avaler, mais franchement c'est comme apprendre une langue étrangère. À un moment donné il faut accepter de se farcir du vocabulaire sinon comment pourrais-tu commander ta bière dans un bar de [Bischkek](https://g.page/metropubkg?share)(bar que, par ailleurs, je te recommande) ?
Avec RSpec c'est pareil il faut bien un minimum de vocabulaire.

Enfin, en dessert, on aura une partie bonus qui te révèlera d'où vient la magie de RSpec.


## 2.Historique et contexte
Cette ressource aura le fabuleux avantage que tu sauras quoi chercher dans Google, et arrêtera de te gratter la tête pour comprendre les réponses de stackoverflow.
RSpec a son propre vocabulaire. Il est un peu déroutant. Mais il faut s'y faire.

## 3. Nomenclatures
Attention, ça va peut-être te piquer un peu les yeux. Je vais te balancer un fichier RSpec bidon, mais avec l'intégralité des features possibles. L'idée n'est pas de comprendre ce qu'on teste dans ce fichier. Mais plutôt de s'arrêter sur chaque partie pour savoir :
- Comment on appelle ce machin.
- À quoi ça sert.
- Est-ce que ça se mange (nan je déconne... n'essaie pas de manger RSpec).

#### 3.1 Le corps

Regardons le fichier : `ta_class_que_tu_veux_test_spec.rb`
```ruby
require 'fabulous gems or helpers, modules and class you need'

RSpec.describe TaClassQueTuVeuxTest do
# Ici les tests RSpec.
end
```

Quand on veut tester un fichier `ta_class_que_tu_veux_test.rb`, l'idéal c'est de le faire dans le fichier `./spec/ta_class_que_tu_veux_test_spec.rb`. Ce n'est pas obligé, mais si tu nommes correctement les deux fichiers en symbiose, RSpec saura quel fichier Ruby il faut tester. Malin.

Un fichier `*_spec.rb` comme tu l'auras remarqué c'est un fichier Ruby. Ainsi, en début de fichier n'hésite pas à y mettre tous les require dont tu pourrais avoir besoin. Tu peux aussi y mettre du code, même si on ne le recommande pas dans les best practices 🙃.

Ensuite commence véritablement le corps du fichier `RSPec.describe ...`.
`RSpec` appelle en fait la classe de RSpec contenu dans la ou les gems que tu as installé.
`describe` est donc une méthode de la class qui prend un bloc.
On pourrait tout à fait écrire le fichier comme suit :
```ruby
require 'fabulous gems or helpers, modules and class you need'

RSpec.describe TaClassQueTuVeuxTest {'# Ici les tests RSpec.'}
```
Mais reconnais qu'à moins d'arriver à faire tenir tes tests sur une ligne c'est peu pratique.
Quoiqu'il en soit, ce qu'il va se passer, c'est que la gem RSpec va lire ce qu'il y a dans ce bloc et l'exécuter.

Arrêtons-nous deux secondes aussi sur les arguments de la méthode `describe`.
```ruby
RSpec.describe TaClassQueTuVeuxTest {'# Ici les tests RSpec.'}
```
`TaClassQueTuVeuxTest` est ici un argument de la méthode `describe`. Mais tu peux également mettre une chaîne de caractères.
```ruby
RSpec.describe 'je suis trop ouf dans ma tête' {'# Ici les tests RSpec.'}
```
Cela fonctionnera tout aussi bien. Mais sâche que l'avantage de donner la classe, c'est que si ton fichier `ta_class_que_tu_veux_test.rb` possède plusieurs classes, et bien RSpec saura exactement ce qu'on est en train de tester, alors qu'en donnant une chaîne de caractères à la méthode `describe` tu perdras cet avantage (et d'autres subtilités avancées qu'on verra plus tard cette semaine).

Note : la méthode `describe` accepte après le premier argument, un nombre infini d'autres arguments sous forme de hash.
Exemple
```ruby
RSpec.describe TaClassQueTuVeuxTest, un_autre_argument: 'sa valeur', encore_un_autre: true {'# Ici les tests RSpec.'}
```
C'est un peu technique à expliquer et pour l'instant peu pertinent donc ne t'embête pas avec ça pour le moment.

Maintenant que tu as vu la coquille d'un fichier spec, regardons l'intérieur :

#### 3.2 Examples
```ruby
it 'does something' do
  # ton test, genre expect(Féfé).to be('funny')
end
```
Ceci est ce que l'on appelle un "example".
Ce nom n'est pas négociable. Il prendra tout son sens à la fin de la ressource, mais j'ai besoin que tu l'apprennes par cœur pour le moment. Fais-moi confiance.


#### 3.3 Example Groups
Comme il existe des examples, il existe également des groupes d'examples (oui je continuerais de l'écrire sous sa forme Anglaise 😉️).
```ruby
describe "Un groupe d'example" do
  it 'un premier example' do
    # le corps de l'example
  end

  it 'un deuxième example' do
    # le corps de l'example
  end

  # autant d'autres examples que tu veux.
end

context "Une autre manière d'avoir un groupe d'example." do
  # autant d'example que tu veux.
end
```

la méthode `context` est rigoureusement la même que la méthode `describe`. En fait elles sont alias l'une de l'autre et sont donc parfaitement interchangeable.
Mais en pratique elles ont des usages séparés.
Tu te serviras de `describe` pour décrire la méthode ou le scope que tu veux tester. Et tu te serviras de `context` pour décrire la condition pour que ce comportement apparaisse.

Regarde bien :
```ruby
describe 'mon controller' do
  context 'quand je suis login' do
    it 'renvoie une HTTP 200' do
      expect(response.status).to eq(200)
    end
  end

  context 'quand je suis logout' do
    it 'renvoie une HTTP 403 Forbbidden' do
      expect(response.status).to eq(403)
    end
  end
end
```

Mais on peut intervertir les deux pourtant ?

Nan. Effectivement, cela fonctionnera, mais je ne laisserai pas passer en correction.

En fait c'est primordial en RSpec de bien respecter cette convention. Les tests, même si tu finiras la semaine en aimant cela, ça reste lourd à écrire. Donc autant faciliter la lecture de la personne qui devra reprendre ou modifier tes tests. Spoiler ce sera sûrement toi. Donc respecte-toi. Respecte cette convention 🙏️.

Au passage as-tu noté qu'on peut imbriquer les `describe` et `context` l'une dans l'autre ? Et bien sache que c'est possible quasi à l'infini. Donc n'hésite surtout pas à le faire si cela peut rendre ton code plus lisible.

#### 3.4 Les Hooks

```ruby
require 'fabulous gems or helpers, modules and class you need'

RSpec.describe TaClassQueTuVeuxTest do
  before(:something) do
    # Ici du code que tu veux exécuter 'avant' (before) something.
    #   J'ai mis 'something' mais il n'y a que quelques valeurs autorisées
  end

  after(:something_else) do
    # T'as pigé le truc
  end
end
```
Voici ce qu'on appelle des `hooks`. Retiens bien ce nom.

Tu peux les mettre où tu veux dans le fichier, et ils ne seront pris en compte qu'une fois lus par l'interpréteur ruby. Encore une fois je te demanderai cependant de respecter la convention, c'est-à-dire de les mettre au début. La personne qui devra te relire t'en remerciera. Et crois-moi, toi aussi.

`before(:something)` Bon tu te doutes bien que ce n'est pas something mais autre chose qu'il faut mettre.
Voici les symboles autorisés par RSpec :
- `:example` : avant (ou après) chaque example
- `:context` : avant (ou après) chaque groupe d'example. Cela inclut bien sûr les `describe`
- `:suite` : avant (ou après) toute la suite de test, c'est à dire l'intégralité de tous les fichiers `*_spec.rb` exécutés par la commande `rspec`... mais ce hook n'est pris en compte uniquement que dans un seul cas bien particulier. Donc je te conseille de malheureusement l'oublier pour le moment.

Attention aux anciennes versions de RSpec pour lesquelles on trouve :
- `:each` : remplacé aujourd'hui par`:example`.
- `:all` : remplacé aujourd'hui par `:context`.
Ces hooks fonctionnent encore juste pour permettre une rétrocompatibilité avec les anciennes versions de RSpec. Comme tu le vois c'est assez trompeur et a été changé pour cette raison.
S'il te plait : ne t'en sers pas. Oublie-les.

#### 3.5 What else ?
Un café peut-être. Et oui, car en substance, c'est tout ce qu'il y a à voir en termes de vocabulaire et autres dingueries. Note tout de même que si besoin, tu peux y ajouter du code ruby autant que tu veux et à n'importe quel endroit du fichier. Il sera exécuté par RSpec de manière logique :

```ruby
RSpec.describe TaClassQueTuVeuxTest do
  def george
    puts 'What else ?'
  end

  describe 'ce que tu veux' do
    it 'un example' do
      george
      # tes tests
    end

    george
  end

  george
end

# renverra 3x 'What else ?' dans ton terminal
```

Pratique quand tu as besoin de DRY un peu tes tests avec un ensemble de code dont tu as besoin plusieurs fois.

## 4. Points importants à retenir
```ruby
require 'tes gems si besoin'

# Éventuellement du code, mais c'est rare.

RSpec.describe TaClasse, un_tag_optionnel: 'la valeur du tag, pas forcément en string d\'ailleurs' do
  before(:example) do # sera exécuté avant chaque example
    # Du code, un example, un group d'example, ou même un puts pour débuguer
  end


  after(:context) do # Sera exécuté après chaque group d'example
    # ce que tu veux.
  end

  # Éventuellement du code pour t'aider à DRY tes tests (à mettre où tu veux)

  describe 'la méthode que tu veux test' do # Un groupe d'examples
    context 'dans un cas particulier' do  # Un autre groupe d'examples imbriqué
      it 'décris ce que ça fait' do
        # ton test.
      end
    end
  end
end
```
