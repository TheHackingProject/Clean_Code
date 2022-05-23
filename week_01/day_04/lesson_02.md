# Acceptances, E2E, Integration et... metadata
Jusqu'à présent nous nous étions concentré sur l'utilisation d'RSpec, avec les tests unitaires comme terrain de jeux.
Cette fois-ci, nous allons voir dans le cas d'une vraie app Rails comment les autres types de tests se construises et comment RSpec a déjà tout prévu pour nous simplifier la vie.

## 1. Introduction
Nous l'avons vu Mardi dans la ressource "Quoi tester", il existe plusieurs formes de tests. Encore une fois je ne souhaite pas rentrer dans les querelles de clochers qui font rages. Il existe beaucoup de philosophie différentes toutes correctes selon moi pour des raisons différentes, toutes plus ou moins complexes, toutes plus où moins applicables suivant le contexte, les ressources en devs, ou la dette technique.

Ici je propose de suivre une philo relativement simple et direct dont j'ai pris la source au près de deux contributeurs de RSpec mais que je simplifierai un peu.
Je t'invite donc aussi à en discuter avec les seniors de ton prochain poste, mais aussi avec les alumnis sur le Discord ou le Slack de la commu.

## 2. Quelle philo
### 2.1 Notre philo
Pour faire simple et parce que cela reste tes premiers pas dans le monde du testing je t'ai proposé mardi dernier l'approche de testing suivante :
- End to end (nommé e2e) : de bout en bout, et donc test l'app du clic sur le bouton "S'inscrire", des dimensions et couleur du bouton, jusqu'au `User` créé en base. On peut dire que cela simule le véritable parcours utilisateur et regarde si les résultats qu'on attend (ou qu'on souhaite éviter) se produisent bien.
- Acceptance ([Tu te souviens ?](https://www.youtube.com/watch?v=Oj8bfBlwHAg)), c'est la même chose que les e2e sauf qu'on ne test pas l'interface graphique, l'UI, l'UX etc... Mais on test par exemple que si on tape dans la route `POST /articles` on se retrouve bien avec un nouvel article créé en base.
- Integration : On test ici que les API, services, ou autres codes plus ou moins externes à l'appli (ou externe à ton équipe) se comporte comme prévu.
- Unit (ou Unitaires en français), mais ceux là tu connais maintenant.

Nous ne ferons pas de tests e2e, car ils sont généralement réservés aux gens qui font du Front. Je considère ici que vous faite du Back.
De plus ils font en général appel à des lib complémentaires qu'il serait trop long d'aborder ici. Rassures-toi, tu as désormais largement le niveau pour aller apprendre un nouveau framework de test à la sorti de ce cursus.

### 2.2 La philo RSpec
Il faut comprendre que la gem `rspec-rails` n'a en vrai pas tout à fait la même approche. La raison c'est que la gem se doit d'être agnostique afin de pouvoir s'adapter à tout style de développement.

La gem, elle, propose donc ce type d'approche :
- Integration : pour tester notre app comme un boîte noire en utilisant seulement les requêtes HTTP (c'est l'équivalent de mon appellation "Acceptance")
- Functionnal : Pour tester comment les controllers répondent à chaque requête sans se préoccuper des effets de bords potentiels.
- Unit : ceux-là tu connais.
- Specific tests : Pour les models, mailers, background jobs etc... Ici RSpec annonce qu'il peut s'agir aussi bien de tests unitaires que de tests d'intégrations.

Bref c'est comme leur documentation, c'est le foutoire et cela peut provoquer des confusions.
Ne retiens donc pas tout ça.

Ce qu'il faut retenir en revanche, c'est comment RSpec fait pour savoir de quel type de test il s'agit.

Pour cela on utilise un truc qui s'appelle metadata.

## 3. Gérer ses différents types de tests avec RSpec et Rails
### 3.1. Les metaquoi ?
Les metadata.

Si tu as bonne mémoire, tu réalises que j'essaye subrepticement de t'en parler depuis le premier jour.
Reprenons :

```ruby
describe 'something' do
  # du code.
end
```
je t'avait expliqué que la méthode `describe` prenait un argument, un hash puis un bloc. Dans le bout de code qui précède on voit bien l'argument `'something'`, le bloc
```ruby
do
 # du code.
end
```
Mais on y voit pas de hash.

Et bien voilà un exemple avec :
```ruby
describe 'something', some_key: 'some value' do
  # Toujours le même code.
end
```

la partie `some_key:` est ce qui s'appelle **metadata** en RSpec.
Basiquement cela ne sert pas à grand chose d'autre que de "nommer" les `describe`, `context` et autres `it`. Sauf que, RSpec est capable d'en interpréter certains.

Par exemple :

```ruby
describe 'something', :focus do
  # du code.
end
```

Si la valeur n'est pas donnée, elle sera instanciée à `true` par défaut. Par ailleurs, cette metadata, sera interprété par RSpec. RSpec ne lancera pas les autres tests, et se focalisera sur celui ci uniquement... quelque soit la manière dont tu essayes de le lancer.
Je te le déconseille donc fortement, car si tu oublie de l'enlever lors de ton push, tu risques de faire passer le CI.... à tort.

Mais il en existe d'autres : `skip` par exemple, mais aussi `type` qui nous sera bien utile dans un instant.

La seule chose à retenir, c'est que tu peux très bien rajouter tout ce que tu veux, RSpec n'y verra aucune objection et t'autorisera même à t'en servir.
Pratique lorsque tu veux tagger certains tests pour une raison particulière : tu pourras ensuite ne lancer que ceux là avec la commande :
```console
rspéc --tag ton_tag:sa_valeur
```

Voici [un peu de doc](https://relishapp.com/rspec/rspec-core/docs/metadata/user-defined-metadata) si tu souhaite aller plus loin sur le sujet.

## 3.2 Les Metadata native de RSpec pour Rails
C'est bien ce que tu penses : RSpec à prévu des metadata spécifique dans la gem rspec-rails, pour pouvoir différentier les types de tests.
voici un exemple

```ruby
RSpec.desccribe ArticleController, type: :controller do
  # Tes tests controller
end
```

RSpec est capable d'interpréter la metadonnée `type` mais surtout sa valeur, et pourra charger des helpers spécifique pour l'occasion.
Comme par exemple, une méthode de `sign_in(user)` ou encore tout simplement de quoi faire tes requêtes HTTP `get root_path` par exemple. Ces helper ne seront pas accessible dans un test qui aurait le `type: :model`.

L'idée derrière ça :
- c'est de pouvoir charger uniquement ce dont tu as besoin, et donc aller plus vite
- Surtout, c'est de pouvoir séparer au maximum les responsabilités, afin d'être sûr que tu n'as pas d'interférence entre tes tests. Sait-on jamais.

## 3.3. Le rapport avec les types de tests.

Et bien c'est très simple. Dans notre philo : acceptance, integration, unitaires, nous placerons des tests unitaires pour les différentes méthodes de tes models, des tests d'intégrations pour tes services ou tes différents autres objets, et enfin, des tests d'acceptances pour les controllers.

Ainsi on aura :
```ruby
RSpec.describe TonModel

RSpec.describe TonMailer
```

Mais, et ce sera important :

```ruby
RSpec.describe TonController, type: :request
```

Oui... `:request`, et pas `:controller` qui existe aussi comme valeur du metadata `type:`.

Dans ces quelques lignes de code, il y a beaucoup de choses. Je te le détaille étape par étape.

### 3.3.1 Pas de metadata systématique
Si tu as bien remarqué, tu as vu que je n'ai pas mis de `type:` aux spec de model ni de mailer. La raison ?

Elle se trouve ligne 58 de ton `rails_helper.rb`
```ruby
config.infer_spec_type_from_file_location!
```
Si tu lis le commentaire situé juste au dessus de cette ligne, tu comprends que RSpec est capable d'assigné un type, rien qu'en se basant sur l'endroit où tu as rangé la classe que tu es en train de tester.

Ainsi, ton model rangé dans le dossier `app/model` sera automatiqement de `type: :model` et ainsi de suite.

### 3.3.2 Type: :request, ou type: :controller ?
Seulement voilà, nous, on ne veut pas tester les controller de la manière dont RSpec avec le `type: :controller`.
Pourquoi ?

Parce qu'on veut qu'un test controller soit en fait un test d'acceptance.

Ce qu'on veut c'est vérifier que lorsqu'on tape une certaine route, mettons `post user_registration_path`, cela :
- Créé bien un user,
- Exécute bien les différentes actions du model le cas échéant
- Demande bien au mailer d'envoyer un mail le cas échéant
- Renvoie bien une 200 à la fin.

Mais on se fout complètement de savoir si les actions du model fonctionnent, ou que le mail arrive à destination avec le bon contenu. C'est le rôle des tests unitaires.
Non, nous ce qu'on veut, c'est juste s'assurer que ces divers éléments ont été déclenchés, et qu'à la fin, le serveur renvoie bien une HTTP 200 à ton navigateur.

Dans un autre cas on pourrait aussi simplement vérifier que le controller te renvoie une `401 Unauthorized` et te redirige vers le sign_in si tu n'es pas login par example.

Et bien pour faire ça, RSpec à déjà prévu tout un tas de helpers bien pratiques dans le `type: :request`. On rangera donc les spec des controllers dans le dossier

`/spec/request/mon_controller_spec.rb`.


Mais pourquoi pas le `type: :controller` ? Ça ne marchait pas ça ?

Si, mais ils sont plus simplistes, et plutôt réservés pour faire du test unitaire sur les controllers. Pour preuve, [le blog de RSpec](http://rspec.info/blog/2016/07/rspec-3-5-has-been-released/#rails-support-for-rails-5) mentionne directement cette philo et explique pourquoi il est préférable d'utiliser les tests de type request :

>Request specs allow you to focus on a single controller action, but unlike controller tests involve the router, the middleware stack, and both rack requests and responses. This adds realism to the test that you are writing, and helps avoid many of the issues that are common in controller specs.

Voilà, tu sais maintenant pourquoi je te propose de faire de cette manière.
Encore une fois, il y a mille façons de faire, et je te recommande chaudement d'en parler avec les alumnis déjà en poste, où à tes collègues.

Et si tu ne vois toujours pas bien comment faire tout ça, pas de panique : je t'explique ça dans un bref instant.


### 3.4 Le résultat du scaffold de la ressource précédente
Je ne pouvais pas terminer cette ressource sans aborder le scaffold de tout à l'heure. Si tu te rappelles, on a scaffold un model Post qui contient les attributs `name`, `title` et `content`.
RSpec, lors du scaffold, a généré les fichiers suivants :

- spec/models/post_spec.rb
- spec/requests/posts_spec.rb
- spec/views/posts/edit.html.erb_spec.rb
- spec/views/posts/index.html.erb_spec.rb
- spec/views/posts/new.html.erb_spec.rb
- spec/views/posts/show.html.erb_spec.rb
- spec/routing/posts_routing_spec.rb
- spec/helpers/posts_helper_spec.rb

On retrouve donc notre fichier de model, jusque là tout va bien.
Le fichier `posts_helper_spec.rb`, lui sert si tu as besoin de dry certaines parties de tes specs. Un commentaire en début de fichier t'explique comment t'en servir. J'avoue ne jamais m'en être servi.

On trouve également un fichier `posts_routing_spec.rb`. Celui-ci sert à tester de manière unitaire les différentes routes. Il m'est d'avis que ce n'est pas nécessaire. La raison ? On va tester chacune des routes déjà à travers le test d'acceptation (request). Donc quelque part ce n'est pas la peine de tester unitairement les routes. Ce serait overkill, car si tes routes sont pétés, tu imagines bien que ton controller aussi, et qu'RSpec te renverra un joyeux `no route matches '/ta/route/pétée'`... Et dans ce cas tu sauras ce qu'il te reste à faire.

Plusieurs fichiers de specs sont également créés afin de tester les views. A ce stade je te sens paniqué : mais comment on test les views ???? Simplement comparant le rendue HTML avec ce que l'on pense obtenir. Je te laisse regarder ces différents fichiers pour voir comment ça fonctionne. Nous on ne va pas se préoccuper de ça. Pourquoi ? Parce que souvent les app rails existent pour servir le backend et on laisse le frontend de plus en plus à un framework en JS (React / Vue etc...). Donc on ira plutôt tester dans ces framework là. Ensuite, parce qu'RSpec, très entre nous, n'est vraiment pas le meilleur framework pour tester du front. Je te laisse donc le soin d'aller plutôt voir du côté de Capybara. Qui, par ailleurs, s'intègre très très bien avec RSpec.

Enfin, on trouve notre fameux fichier `spec/requests/posts_spec.rb`. Remarque bien que RSpec n'a pas créé de fichier `spec/controllers/posts_spec.rb`, mais bien un fichier request.

## 4. Acceptance et Intégrations par l'exemple.
### 4.1 Acceptance
Alors tu dois maintenant te poser la question : mais comment on fait concrètement pour tester une route controller ???

Clairement, la réponse est dans le fichier `spec/requests/posts_spec.rb`. Je te laisse le lire. C'est assez explicite et simple. Le bonus ? C'est que tu as tout un CRUD en guise d'exemple... elle est pas belle la vie ?

Bon maintenant ce n'est pas tout.
Imagine, que le controller Post, envoie un mail à l'auteur avec le contenu du mail. En plus ? A chaque nouveau Post, le modèle rajoute une catégorie par défaut intitulée `'life style'`.

On aurait donc le code suivant :
```ruby
# app/models/post.rb
class Post < ApplicationRecord
  after_create :add_category

  def add_category
    category ||= 'life style'
  end
end

# app/controllers/posts_controller.rb
class PostsController < ApplicationController
  def create
    @post = Post.new(post_params)

    UserMailer.new_post_email(user.name, user.email, @post.title)

    if @post.save
      redirect_to post_url(@post)
    else
      render :new, status: :unprocessable_entity
    end
  end
end
```

Ce qu'on veut pour un tel controller, c'est qu'un user envoie la requête `POST /posts` :
- Un post soit créé en base avec la catégorie `life style` ou celle donnée par les params.
- La méthode `new_post_email` du mailer `UserMailer` soit appellée.
- Qu'avec des bons paramètres on atterisse sur la page show, sinon sur la page new.

Mais on se fiche complètement de savoir si le Mailer plante, ni de quelle manière le model va géré la catégorie. En revanche on vérifie le scénario d'un bout à l'autre de la chaine. On vérifie le parcours utilisateur.

Par rapport aux tests qui ont été décrit par le scaffold, il manque donc :
- la vérification sur la catégorie du post créé
- la vérification que le Mailer a bien été appelé.

On pourra donc rajouter :
```ruby
describe "POST /create" do
    context "with valid parameters" do
      # le code du scaffold

      context 'when parameters have no category' do
        it "creates a new posts with 'life style' category" do
          post posts_url, params: { post: valid_attributes }
          expect(Post.last.reload).to have_attributes(category: 'life style')
        end
      end

      context 'when parameters have a category' do
        it "creates a new posts with the given category" do
          post posts_url, params: { post: valid_attributes.merge(category: 'toto') }
          expect(Post.last.reload).to have_attributes(category: 'toto')
        end
      end

      it 'sends an email' do
        expect(UserMailer).to receive(:new_post_email).with(user.name, user.email, valid_attributes[:title])
        post posts_url, params: { post: valid_attributes }
      end
    end

    context "with invalid parameters" do
      # le code du scaffold
    end
  end
```

Je te laisse le soin de voir comment on peut dry un peu tout ça, mais t'as compris le principe.
Tu auras noté la subtile utilisation d'un spy pour le mailer.
Tu auras également noté l'utilisation du `reload` qui permet d'être sûr que la base de donnée a bien pris en compte ta création de post.

### 4.1 Integration
Concernant les tests d'intégrations, voici un exemple qui va te parler. Imagines que tu créé une app pour gérer tes pokemons. Pour cela, intelligemment, tu utilise l'API [pokeapi](https://pokeapi.co/).

Et forcément c'est intelligent de créé un service pour aller récuperer ce dont tu as besoin sur cette api.
Je te laisse lire la documentation de [Pokeapi](https://pokeapi.co/docs/v2#resource-listspagination-section) pour comprendre les quelques lignes qui suivent.

Mais dans l'ensemble j'ai essayé d'avoir le code le plus "`Clean`™️" possible.


```ruby
class PokeApi
  BASE_URI = "https://pokeapi.co/api/v2/"

  def get_url(path, parameter)
    request = "#{BASE_URI}#{path}#{parameter}"
    HTTP.get(request)
  end

  def get_pokemon(name = nil)
    get_url('pokemon/', name)
  end

  def get_ability(name = nil)
    get_url('ability/', name)
  end

  def create_from_json_pokemon(pokemon = nil)
    Pokemon.create(JSON.parse(pokemon))
  end

  def create_abilities_from_pokemon(pokemon)
    pokemon.abilities.each do |ability|
      Ability.create(JSON.parse(get_ability(ability.name)))
    end
  end
end
```

Bien sûr on imagine que tu as déjà testé unitairement que `get_url` envoie bien la bonne requête, que `get_pokemon` récupère bien un pokemon etc...
Grâce aux tests unitaires on voit bien que séparément tout fonctionne.

(Je t'encourage à faire l'exercice tiens 😉️)

Mais quid ensemble ?


En fait, de manière très simple ce qu'on veut c'est tester, c'est que tous les éléments de la chaîne s'imbriquent bien. Et donc que
```ruby
create_abilities_from_pokemon( create_from_json_pokemon(get_pokemon('pikachu')))
```
- ne plante pas et...
- ... créé bien 2 abilities et 1 pokemon en base et avec les bonnes valeures.

Voilà. C'est aussi simple que ça.

et ça peut se résumer en un seul context par :
```ruby
context 'with correct pokemon name' do
  subject(:whole_service) { create_abilities_from_pokemon( create_from_json_pokemon(get_pokemon('pikachu'))) }
  it 'successfully creates abilities' do
    expect { whole_service }.to change(Ability, :count).from(0).to(2)
  end
  it 'successfully creates pokemon' do
    expect { whole_service }.to change(Pokemon, :count).from(0).to(1)
  end
end

context 'with incorrect pokemon name' do
  it 'returns error...' do
    pending "A ton tour de complèter ce que renvoie l'API dans un tel cas"
  end
end
```

On oublie pas de tester aussi que toute la chaine plante si on renseigne un mauvais paramètre

De même on pourrait imaginer (avec un service plus complexe) qu'il faudrait aussi bien vérifier les différents attributs des JSON renvoyés par l'API. Car si jamais l'API change, il faut que certains tests cassent. De cette manière il n'y aura jamais de surprise en prod !

Globalement, dans le cas présent tu peux imaginer mettre ces tests d'intégration dans le même fichier que tes tests unitaires.
Les séparer fait plus propre... mais là, pour si peu, ce serait overkill. A toi de voir la manière la plus propre.

**Mais je mets quoi du coup comme metadata `type:` ?**
Et bien globalement ce que tu veux.
Si tu as besoin, pour faire des tests d'intégration de ton service, de faire un `get` ou `post`, un `sign_in` etc... tu peux mettre un `type: :request` dans le describe correspondant.
Sinon laisser par défaut ou mettre un `type: :model` suffit largement en général.

## 5. Points importants à retenir
- Model, mailers et autres services devront être testés unitairement.
- Si on place les fichiers spec au bon endroit, il n'y a pas besoin de préciser ce que l'on fait avec une metadata.
- Les controllers eux, seront testés à travers un test d'acceptance qui englobera le routeur, le controller, le model et même d'autres classes qui pourraient être impliqués. On le fera par le biais de la metadata `type: :request` que l'on mettra au début de son fichier spec. Ce fichier on le placera gentillement dans le dossier `/spec/request/` d'ailleurs.

- En fonction de la complexité de l'app, ou de la criticité, on pourra englober également les services et autres API tierces dans des tests d'intégrations. Ceux-ci ne sont pas bien compliqués et il suffit de décrire ce qu'on attend du service dans les différents cas de figure. Sans se préoccuper de son fonctionnement interne.
- Enfin, si tu te sert vraiment beaucoup d'une API tierce, et qu'elle devient critique pour ton appli, il est alors sage de créé un fichier qui test scrupuleusement que son fonctionnement n'a pas changé par rapport à sa documentation.

## 6. Pour aller plus loin
J'ai conscience que cette ressource est déjà bien assez lourde. Mais je sais aussi que tu reviendra plus tard relire cette ressource, lorsque tu seras plus à l'aise avec ces tests.

Alors je te laisse ça là, quand ton mal de tête aura cessé.

Un petit tableau des différents `type:` de tests sous RSpec :

| type: | Pour quels tests ? | Helpers qui viennent avec | Commentaire |
|---|---|---|---|
| :feature | Pour tester un parcours utilisateur entier | - l'alias feature (describe) - l'alias scenario (it) - Accès à l'API de capybara - Les routes au format  `some_route_path` | Il faut installer  la gem Capybara pour ça |
| :request | Acceptance test On prend une route, et  on vérifie le résultat  final.  Si besoin on peut déborder un peu, en faisant un parcours utilisateur impliquant plusieurs routes. Mais je ne recommande pas. | - request helpers comme `get /index` ou `post /create` etc..  - request matchers comme `redirect_to`, `have_http_status`  - Les routes au format  `some_route_path` |  |
| :model | Tests unitaires sur son model | - Permet d'avoir les Database Transactions (pas de persistence des objets en base) |  |
| :controller | Tests unitaires sur les controllers | Idem :feature. Sans le helper `redirect_to`, mais avec le helper `route_to` | Par défaut ne fait de  render des views. Il faut appeler le render explicitement une fois la requête faite en appelant `render_view` |
| :view | Tests unitaires sur les views | - Permet le helper `assign` afin d'utiliser (et de remplir) les variables comme l'aurait fait le controller. |  |
| :helper | Tests unitaires des modules que l'on place dans les dossiers `/helpers`. | - Permet aussi le `assign` afin d'utiliser (et de remplir) les variables. |  |
| :mailer | Tests unitaires des mailers | Permet l'utilisation des routes au format  `some_route_path` |  |
| :routing | Tests unitaires des routes | Permet l'utilisation des matchers :  - `route_to` - `be_routable`  Permet l'utilisation des routes au format  `some_route_path` |  |
| :job | Tests unitaires des background jobs | Idem au type `:model` |  |

- Une excellente vision de Martin Fowler (Oncle Bob) sur les tests d'intégration:  https://martinfowler.com/bliki/IntegrationTest.html

D'une manière générale, et vu qu'il s'agit de philosophies, de points de vues mais aussi d'expériences différentes, je t'invite fortement à générer des discussions avec tous les dèv que tu trouveras.
Garde du recul, car l'immense majorité des dèv (100% ?) te diront avoir raison. La réalité opérationnelle amène souvent à d'autres choix.