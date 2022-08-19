# RSpec et Rails
Savoir utiliser RSpec avec Rails et comprendre certaines subtilités.

## 1. Introduction
Tout au long de la semaine, tu as dû le voir, nous n'avons quasiment pas abordé Rails. La raison est que si tu les comprends et que les fondamentaux du testing sont solides hors Rails, alors faire du testing en Rails n'est qu'une simple formalité.
L'inverse est faux : savoir tester une app Rails ne t'apprendra pas à tester convenablement une gem, un scrapping pour une équipe growth, ou autre.

Sachant cela, nous verrons les avantages qu'offre RSpec aux applications Rails, mais surtout : comment l'installer et s'y retrouver dans la configuration ?

## 2. La gem [rspec-rails](https://relishapp.com/rspec/rspec-rails/docs)

### 2.1. Installation
Pour utiliser RSpec dans son app rails, rien de plus simple que de mettre :
```ruby
gem 'rspec-rails', '~> 5.0.0'
```
Dans son Gemfile.
Bien sûr on prendra soin de glisser cette gem au bon endroit :
```ruby
group :development, :test do
  # ici
end

group :production do
  # Mais pas là
end
```

Un petit `bundle install` des familles et on est bon. Cette gem à la particularité d'installer les dépendances suivantes :
- rspec
- rspec-core
- rspec-expectations
- rspec-mocks
- rspec-rails

Ensuite, comme pour tout projet, il faut initialiser RSpec.
Pour cela :
```console
rails generate rspec:install
```

### 2.2. Unpacking et explications
Une fois cette dernière commande exécutée, on observe la création de 3 fichiers :
- .rspec
- spec (le dossier)
- spec/spec_helper.rb
- spec/rails_helper.rb

On va voir ensemble à quoi servent ces 3 fichiers de config.

### 2.2.1 .rspec
Ce fichier configure la commande `rspec`.
Cela veut dire que lorsque tu vas exécuter la commande `rspec`, en fait tu passeras implicitement en argument les options qui se trouvent dans le fichier `.rspec`.

Ce qu'on peut voir c'est que pour le moment ce fichier `.rspec` contient `--require spec_helper`.
cela veut dire que quand tu exécutes la commande `rspec` dans ton terminal, en fait la commande lancée est :
```console
rspec --require spec_helper
```

Pratique. Tu peux voir ce fichier comme un générateur d'alias.
Tu peux rajouter d'autres options en rajoutant d'autres lignes dans ce fichier. Par exemple, sur mon projet perso mon fichier `.rspec` ressemble à ça :

```console
--order rand
--no-fail-fast
--format documentation
```

Typiquement, tu peux rajouter et jouer avec tout ce qui t'est expliqué en résultat de la commande `rspec --help`.

Ce fichier est bien sûr versionné, et donc partagé avec toute ton équipe.

**Alerte bonne astuce** : dans ta prochaine boîte, tu pourras créer un fichier `.rspec-local` sans oublier de l'ajouter au `.gitignore`.
Ce fichier permet d'override le fichier `.rspec`, mais donc uniquement sur ta machine. Utile quand tes collègues veulent impérativement une config que toi tu détestes personnellement (genre `--format NyanCatFormatter`).

### 2.2.2 spec_helper.rb
De la même manière que le `.rspec` le fichier `spec_helper.rb` permet quelques configurations par défaut. C'est là notamment qu'on pourra configurer RSpec pour utiliser l'option `--only-failures`.

Par défaut, il n'y a pas beaucoup de configuration. On respecte le mantra de rails : "Convention over Configuration". Mais tu vois bien qu'il n'y a vraiment presque rien, et que finalement on s'éloigne de la philosophie de testing.

Il y a même toute une section qui est commentée :

```ruby
  # The settings below are suggested to provide a good initial experience
  # with RSpec, but feel free to customize to your heart's content.
=begin
  Beaucoup de ligne de configurations leurs explications en commentaires
=end
```

En fait, clairement, tu peux venir faire ton marché dans ces lignes de configurations. Notamment j'attire ton attention sur les fabuleusement pratiques lignes 57 à 60 de ce fichier.

### 2.2.3 rails_helper.rb
Ce fichier est encore un fichier de config. Mais celui-ci est à part, car il ne sera pas automatiquement require. Il faudra le require manuellement partout où tu en auras besoin.

Dans certains projets, on le trouve carrément dans le fichier `.rspec` (mais je ne le recommande pas sans en parler à quelqu'un de sénior dans l'équipe).

Ce fichier, `rails_helper.rb`, charge à la fois les gems rails et rspec-rails. Cela à l'air stupide dit comme ça, mais jusque là, RSpec aurait exécuté tes tests uniquement "The Ruby Way"... sans Rails. La raison ? C'est que de cette manière, il y a beaucoup moins de choses à charger, et tes tests vont plus vite à s'exécuter.

Et, bien qu'à notre niveau 99.99% des fichiers `_spec.rb` auront besoin d'utiliser rails, il faut bien comprendre que si tu te retrouves à tester une librairie tierce dont tu souhaites qu'elle ne soit SURTOUT pas couplée avec rails, et bien pour t'en assurer, il faut que tu t'assures que Rails ne soit pas chargé. Et donc, que le rails_helper.rb ne soit pas require dans les fichiers de tests correspondants.

Souviens-toi bien de tout ça pour ton futur job, ou test technique en posant la question : quelle est votre utilisation et convention en interne sur le `rails_helper.rb` ?
Tu sors une phrase comme ça : direct tu passes en mode God-Like en testing pendant un recrutement.
(Véridique, je l'ai récemment testé pour toi).

## 2.3 Et maintenant ?

Maintenant, chaque fois que tu feras un `rails generate`, RSpec créera pour toi les fichiers qui vont bien, et dans les bons dossiers. Pas besoin de se prendre la tête.

Mieux : je t'encourage à le tester dès maintenant en faisant un scaffold :

```console
rails generate scaffold Post name:string title:string content:text
```

Maintenant regarde le contenu des fichiers :
```shell
      invoke    rspec
      create      spec/models/post_spec.rb
      create      spec/requests/posts_spec.rb
      create      spec/views/posts/edit.html.erb_spec.rb
      create      spec/views/posts/index.html.erb_spec.rb
      create      spec/views/posts/new.html.erb_spec.rb
      create      spec/views/posts/show.html.erb_spec.rb
      create      spec/routing/posts_routing_spec.rb
      create      spec/helpers/posts_helper_spec.rb
```

On décortique tous ces fichiers dans la ressource qui suit.

## 3. Points importants à retenir
Installer RSpec dans une app rails c'est ajouter la gem `'rspec-rails'` au gemfile et faire un `rails generate rspec:install`.
Le reste c'est de la config.
