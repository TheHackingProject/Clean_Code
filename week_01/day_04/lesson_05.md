# FactoryBot
Oui j'ai bien cherché un titre sympa pour cette ultime ressource. Mais faudra que tu te débrouilles avec le nom de la gem.

## 1. Introduction
On va voir ici pourquoi c'est quasi indispensable pour les tests d'applis rails.

## 2. FactoryBot Kezako ?
Avant cette gem s'appelait FactoryGirl. Pour des raisons évidente, les créateurs ont renommé cette gem. Ne sois donc pas étonné si tu vois mentionner ce nom dans les posts Stackoverflow qui datent.

Concretement, FactoryBot est une gem qui va te permettre de créer des objets de ta base de données, comme un `user` par exemple, avec tous ses attributs nécessaires, simplement avec un pauvre `create(:user)`. Et ce, où tu veux dans tes tests.

Cette gem est véritablement puissante, et il y a deux trois choses que tu dois connaitre et comprendre avant de pouvoir te plonger dans la doc.

## 3. Les Fixtures
RSpec prévoit l'utilisation de ce qui s'appelle des fixtures. Les fixtures sont des fichiers qui permettent de créer les objets usuels de votre base de données.
Il se trouve que FactoryBot est une gem qui permet de créer automatiquement les fixtures pour chacun de vos objets en base.

### 3.1. Installation
En fait il existe deux gems. Une gem pour les codes rubys en tout genre : factory : `gem install factory_bot`.

Nous on va se préoccuper d'apprendre à se servir de FactoryBot directement dans une app rails, comme c'est le plus souvent le cas dans la vie sauvage.

On va donc placer la gem dans les group `:development` et `:test` de notre Gemfile :

```ruby
group :development, :test do
  gem 'factory_bot_rails'
end
```
Un petit `bundle install` bien sûr.

Ensuite on a besoin de dire à RSpec que lorsqu'il créé un objet il pourra le faire en se servant de FactoryBot.
Pour cela, [la doc de configuration](https://github.com/thoughtbot/factory_bot/blob/master/GETTING_STARTED.md#configure-your-test-suite) de FactoryBot nous indique qu'il faut placer ces lignes quelque part dans la configuration de RSpec :
```ruby
RSpec.configure do |config|
  config.include FactoryBot::Syntax::Methods
end
```

Rien de plus simple tu vas donc juste rajouter la ligne `config.include #...` dans ton `rails_helper.rb`, et HOP ! Le tour est joué.

### 3.2. Mise en place
Dès cet instant, chaque fois que tu changeras quelque chose dans tes models par la commande `rails generate` (model ou migration), une factorie sera créée où modifiée dans le dossier `spec/factories/`.

Tu peux par exemple tester en faisant un `rails generate scaffold Article name:string title:string content:text`.
Tu verras qu'un fichier `/spec/factories/articles.rb` (note l'utilisation du pluriel dans le nom du fichier) est créé avec comme contenu :
```ruby
FactoryBot.define do
  factory :article do
    name { "MyString" }
    title { "MyString" }
    content { "MyText" }
  end
end
```

Autrement, tu peux aussi demander à rails de générer pour toi les factories de models déjà existant en exécutant la commande :

```console
rails g factory_bot:model Post name title content
```

et hop voilà une factory toute propre :

```ruby
FactoryBot.define do
  factory :post do
    name { "MyString" }
    title { "MyString" }
    content { "MyString" }
  end
end
```

### 3.3 Utilisation
#### 3.3.1 Dans RSpec
Grâce à la ligne de configuration que tu as rajoutée dans ton `rails_helper.rb`, FactoryBot est directement inclu dans tes specs. Et partout où tu écriras `create(:article)` ou `create(:post)` tu auras un objet Article ou Post avec les attributs de ta factory.

**Explication :**
`create(:post)` va aller chercher la factory du model `Post`. A savoir le fichier `spec/factories/posts.rb` contenant
```ruby
FactoryBot.define do
  factory :post do
    name { "MyString" }
    title { "MyString" }
    content { "MyString" }
  end
end
```
Puis, automatiquement, la factory va exécuter la commande `Post.create(name: 'MyString', title: 'MyString', content: 'MyString')`.
Voilà, c'est tout.

Mais cela évite de le faire "à la main".

Alors évidemment tu me feras le plaisir d'écrire ton `create(:post)` dans un `let` :
```ruby
let(:mon_post_trop_cool) { create(:post) }
```

#### 3.3.2 En dehors de RSpec
L'astuce ultime, c'est que cela fonctionne également en console rails !!!
Et là ça envoie du lourd.
```console
rails c
```
Et maintenant tu peux directement faire un petit
```console
FactoryBot.create(:post)
```
pour avoir un post tout neuf avec les attributs tous initialisés à `"MyString"`.

Avoue : t'as passé combien de temps ces derniers mois à créer des objets bien relous à la main ? Genre ton projet final où tu avais un model qui avait 15 attributs de l'enfer, tous obligatoires. Que quand tu voulais tester tu étais obligé de TOUS LES RENSEIGNER....

Ben là en une commande c'est réglé.

(Je suis dispo sur le discord si tu souhaites m'envoyer des fleurs).

#### 3.3.3 Astuces communes
##### 3.3.3.1 Remplacer un attribut de manière spécifique
Alors bien sûr tu t'en doutes, cela ne s'arrête pas là. FactoryBot te permet bien sûr de remplacer n'importe quelle valeur d'attribut par la tienne.
**Démonstration :**
```ruby
create(:post, content: "C'est ce contenu que je veux que mon post ai")
# Créer le post avec les attributs :
# name: 'MyString'
# title: 'MyString'
# content: "C'est ce contenu que je veux que mon post ai"
```
Et tu peux le faire pour n'importe quel attribut. Cela remplacera automatiquement la valeur renseignée dans ton fichier factory par celle que tu veux.

##### 3.3.3.2 create list
On est d'accord, quand tu as besoin de 15 posts différents tu ne vas pas encore t'embêter avec une boucle hein ?
Non
A la place tu feras ça :

```ruby
create_list(:post, 15)
```
Et voilà, 15 posts tout neufs, tous avec les même valeurs dedans.

Tu préfères d'autres valeurs ? Pas de soucis :
```ruby
create_list(:post, 15, title: Faker::Book.title, content: Faker::Lorem.sentence)
```

Et voilà 15 posts avec un titre et un contenu générés par Faker... (Attention : ils auront tous le même titre et le même contenu en revanche).

##### 3.3.3.3 Factory de deux models liés entre eux.
Ton model post est en `belong_to` user ?

S'il te plait : ne rajoute pas à la main un user à chaque post de ta liste de 15 posts :

```ruby
create_list(:post, user: create(:user))
```
suffira.

Une fois encore ce sera le même user qui sera relié aux 15 posts.

## 4 Factory Advanced
Il n'est pas nécessaire de lire les lignes qui suivent pour s'en sortir.
Néanmoins, en cas de base de données un peu bizarres, ou de projets complexes ou que sais-je encore, ce qui suit pourra t'être utile.

### 4.1 Les limitations précédentes.
Tu le reconnais, un content qui a toujours 'MyString' en valeure c'est pas sexy.
Et on ne va pas réécrire systématiquement le Faker dans la commande `create` non plus.

Alors on peut directement modifier le fichier factory.

Modifie le `spec/factories/posts.rb` pour qu'il ressemble à cela :
```ruby
FactoryBot.define do
  factory :post do
    name { Faker::Name.first_name }
    title { Faker::Book.fitle }
    content { Faker::Lorem.sentence }
  end
end
```
Et hop comme par magie, pour chaque post créé par la factorie, le post aura des attributs différents.
Et oui même en cas de `create_list` cette fois ci tes posts seront tous différents.

### 4.2 La philo FactoryBot.
Si tu parcours la documentation de FactoryBot, tu verras que la philo est assez simple et si tu y réfléchis, elle rejoint celle de RSpec.
Ce que l'on veut c'est tester. Et donc pour ça on doit pouvoir créer l'objet minimal.
Par exemple, si pour le model `Post` les attributs `name`, `title` et `content` ne sont pas obligatoires, alors l'objet `Post` minimal c'est juste `id: 1, name: nil, title: nil, content: nil`.
Et donc il faudrait que ta factory ressemble plutôt à
```ruby
FactoryBot.define do
  factory :post do
    # rien puisque qu'aucun des attributs n'est obligatoire.
  end
end
```

Ensuite tu auras sûrement, dans tes tests unitaires ou d'acceptance, besoin de tester ce qu'il se passe avec des valeurs dans les autres attributs. Dans ce cas tu pourras créer ton objet minimal avec `create(:post, name: 'le nom pour tester')` pour tester un post avec un `name` par exemple.

La philo c'est ça : n'avoir QUE l'objet MINIMAL lorsqu'on utilise la factory. Tout ce qui n'est pas nécessaire à la création de l'objet, ne doit pas être tester. Sinon tu pourrais induire certains tests à passer au vert alors qu'ils ne devraient pas.

En revanche dès qu'un attribut est obligatoire il faut que ta factory le créé au passage.

Par exemple fais un petit scaffold User rapidement :
```console
rails g scaffold User name:string email:string
```

Puis un petit
```console
rails g migration AddUserToPosts user:references
```

Puis rajoute `has_many :posts` dans ton model User, et `belongs_to :user` dans ton model Post.
Exécute la ou les migrations si besoin et ouvre la console de rails.

Normalement la commande `FactoryBot.create(:post)` doit t'insulter, soi-disant que
```console
validations.rb:80:in `raise_validation_error': Validation failed: User must exist (ActiveRecord::RecordInvalid)
```
(même si tu relances la dernière migration en enlevant le `null: false`)

C'est normal, un post doit avoir un user.

Ainsi, si tu changes ta factory `spec/factories/posts.rb` par
```ruby
FactoryBot.define do
  factory :post do
    user
  end
end
```
Cette fois ci : le post est bien créé, et son user avec.

**L'oeil de Link 🗡️🛡️🏹️** aura sûrement remarqué que nous n'avons pas écrit `user: create(:user)` dans la factory.
Et c'est bien vu.
Car en fait factory est conscient de ses capacités. Et donc sait que la factory nommée `user` existe. Et donc pas besoin de l'appeler par un `create`. La méthode `user` suffit.

Il y a d'autres manières de gérer ses associations. [Tu trouveras plus d'info ici](https://github.com/thoughtbot/factory_bot/blob/master/GETTING_STARTED.md#associations).

Dans tous les cas, chaque post créé aura son user obligatoire. Tu auras donc ton objet minimal.


"Oui mais si je veux très régulièrement plus que l'objet minimal ? Je vais me taper de le préciser à la main à chaque `create(...)` ??

Non.

### 4.3 factory de factory
Pour cela il y a les [nested factories](https://github.com/thoughtbot/factory_bot/blob/master/GETTING_STARTED.md#inheritance).

Tu peux très bien créer une factory dans une factory.

Exemple :

```ruby
FactoryBot.define do
  factory :post do
    user

    factory :complete_post do
      name { Faker::Name.first_name }
      title { Faker::Book.fitle }
      content { Faker::Lorem.sentence }
    end
  end
end
```

Il ne te reste plus qu'à écrire un `create(:complete_post)` et tu auras dans ce cas :
- Un post avec son user
- Contenant un titre, un nom et un content...

Mais `create(:post)` continuera de fonctionner et t'offrira l'objet minimal pour tes tests.

### 4.4 Traits
"Trait" signifie, en anglais, un trait de caractère.

Et bien Factory y a pensé et a appelé ça des traits. Cela permet de modifier les valeurs des attributs de manière sensée. Et c'est très utile donc dans RSpec car on peut alors écrire :
`create(:post, published)` au lieu de `create(:post, published: true)`. Ça n'a pas l'air comme ça. Mais c'est très utile et beaucoup plus lisible dans des cas plus complexe

Admettons que nous ayons un model Post ayant les attributs supplémentaires suivants :

```ruby
published: Boolean
publication_date: DateTime
```

Et bien on peut définir la factory de la manière suivante par exemple :
```ruby
FactoryBot.define do
  factory :post do
    user

    factory :complete_post do
      name { Faker::Name.first_name }
      title { Faker::Book.fitle }
      content { Faker::Lorem.sentence }
    end

    trait :published do
      published { true }
    end

    trait :unpublished do
      published { false }
    end

    trait :old do
      published_date { 1.year.ago }
    end

    trait :recent do
      published_date { 1.day.ago }
    end
  end
end
```

Et ainsi, que l'on créé un `:post` ou un `:complete_post`, on pourra toujours faire varier en faisant par exemple :

```ruby
create(:post, :old, :unpublished)
```

Et si vraiment on s'en sert souvent, on peut même modifier encore la factory de cette manière :
```ruby
FactoryBot.define do
  factory :post do
    # ...

    trait :unpublished do
      published { false }
    end

    trait :old do
      published_date { 1.year.ago }
    end

    factory :old_and_forgotten_post, traits: [:unpublished, :old]
  end
end
```
Et donc on aura plus qu'à écrire : `create(:old_and_forgotten_post)` et le tour est joué.

Quelle différence avec les nested fatories ?
Et bien les nested factories permettent de séparer les attributs à remplir (objet minimal versus objet complet), alors que les traits sont plutôt réservés pour séparer les cas de valeurs possibles (published à true versus false par exemple).


### 4.5 sequence versus Faker
Un petit point important et qui m'a fait suer, ainsi que de nombreuses personnes passées aspirants.

Il arrive que tu veuilles l'unicité d'une valeure dans un model. Typiquement le model User prend un attribut `email` et on se doute bien qu'il sera unique.

ET là, beaucoup de gens utilisent FactoryBot et [sa method `sequence`](https://github.com/thoughtbot/factory_bot/blob/master/GETTING_STARTED.md#sequences).


```ruby
FactoryBot.define do
  factory :user do
    name { "MyString" }
    sequence(:email) { |n| "person#{n}@example.com" }
  end
end
```

Cela aura pour effet d'incrémenter la valeur "n" à chaque fois qu'un user est créé par FactoryBot, et donc de le rendre l'email unique, au même titre qu'un id par exemple.

Super ! non ? non....

Parce qu'à l'instant où quelqu'un d'autre va devoir faire un `create(:user, email: 'toto@gmail.com')`, ta sequence est foutue, FactoryBot ne saura pas s'en sortir.

Auquel cas il vaut mieux utiliser la génération unique de la gem Faker.
Démonstration :
```ruby
FactoryBot.define do
  factory :user do
    name { "MyString" }
    email { "#{Faker::Lorem.word}@#{Faker::Number.unique.number(4)}.com" }
  end
end
```
Ainsi tu auras une adresse mail du type `'unmotrandom@3468.com'` avec la suite `3468` qui sera garantie unique par Faker.

Bien sûr, si tu as peur que 10000 ne soit pas assez, tu peux augmenter la quantité en changeant le `number(4)` par `number(8)` par exemple.

Voilà. Ça n'a l'air de rien comme ça. Mais crois moi, cela va te sauver la vie et celles de tes collègues un jour.
Et en plus ça sauve des bébé pandas.

### 4.6 Quelques graines pour finir.
Avant d'aller énerver l'[autre](https://tenor.com/boA2Q.gif), je voulais juste te parler du seed...
Car oui : FactoryBot peut fonctionner avec le seed.

Un peu plus pratique non ?

## 5. Points importants à retenir
La ressource en quelques points importants.

## 6. Pour aller plus loin
- Si ce n'est pas déjà fait, tu peux t'attaquer au paragraphe sur les fonctionnalités poussées de FactoryBot.
- Et si tu en veux d'autres, tu peux en fait simplement [lire la doc](https://github.com/thoughtbot/factory_bot/blob/master/GETTING_STARTED.md). Elle est vraiment bien faite et pleine de fonctionnalités très avancées notamment l'utilisation des callbacks du type `after_create` par exemple. Ceux-ci seront pratiques si tu te retrouves à tester un jour ce genre de choses.