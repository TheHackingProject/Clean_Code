# RSpec : rappels et installation.
Installer RSpec est aussi simple qu'un `gem install`. Mais il y a plusieurs gems formant le framework. Comprendre ce qu'il y a dedans, c'est comprendre quoi utiliser pour tous tes futurs projets.

## 1. Introduction
Faisons un point rapide histoire qu'on soit tous au diapason. RSpec est déjà certainement installé sur ta machine, mais on va faire comme si ce n'était pas le cas. On va aborder rapidement les différentes gems qui existent, et on verra quand et pourquoi les mettre dans ton projet.

Enfin on apprendra à lancer RSpec correctement. Oui il y a deux ou trois trucs que tu dois savoir pour briller en société. Hein ? Non pas le roquefort... (Si tu n'as pas pigé la blague, ce n'est pas bien grave. Pour les autres, j'espère qu'elle passe crème.)

## 2.Historique et contexte
On ne va pas faire un long exposé sur RSpecs, mais si ça t'intéresse et que tu as du temps libre, tu peux aller voir directement sur leur site, où il y a une [page d'info](https://rspec.info/about/) qui raconte l'histoire de RSpec.

## 3. La ressource
### 3.1. Installer RSpec
#### 3.1.1 En global
##### 3.1.1.1 Le contenu d'RSpec
En fait RSpec est composé de trois gems indépendantes. Et ouais :

- ***rspec-core :*** c'est le moteur d'rspec. En gros c'est cette gem qui répond à la commande `rspec` dans ton terminal.
- ***rspec-expectations :*** qui te fournit toute la syntaxe qui te rebute tant jusqu'à maintenant. Tu sais les fameux :

```ruby
describe 'une méthode' do
  it 'fait quelque chose ' do
    expect(some_code).to match(something)
  end
end
```
Ben voilà c'est cette gem.

- ***rspec-mocks :*** cette gem est responsable de ma perte de cheveux. On ne l'abordera qu'à la toute fin de ce parcours. Mais pour faire simple, cela permet de tester du code qui n'existe pas encore. "To mock" signifie "imiter" en anglais. Donc de manière très basique cela permet de créer des objets qui vont pouvoir faire semblant de se comporter comme tu en aurais besoin. Sauf qu'il n'y a pas de code derrière.... Bref. Pour le moment c'est obscur. Ne fais pas comme moi : ne commence pas par t'attarder sur ce point.

Avec tout ça vient également une 4e gem :
- ***rspec-support :*** celle-là regroupe tout un tas de choses dont les 3 autres peuvent avoir besoin. Mais cette gem n'est pas faite pour fonctionner directement toute seule. Tu peux donc l'oublier pour le moment.

##### 3.1.1.2 Et rails ?
Bien vu ! Il existe une gem dédiée à rails : ***rspec-rails***. Elle t'offre tout un tas de helpers super utile dans rails. On abordera ça spécifiquement un peu plus tard dans la semaine.

##### 3.1.1.3 Installation
Bien sûr tu peux tout à fait installer toutes ces gems séparément. Tu peux donc très bien taper dans ton terminal :
```shell
gem install rspec-core
```
Cela va fonctionner et te donner accès à la commande rspec. Cela est très pratique à savoir si jamais tu as besoin uniquement de `rspec-expectation` dans un projet qui utilise mini-test par exemple.

Mais le mieux, pour nous et pour apprendre, reste encore de tout installer d'un seul coup avec cette commande que je te demande de faire :
```shell
gem install rspec
```

Normalement cela te donne accès aux 3 gems utiles ainsi qu'à celle que je t'ai demandé d'oublier.

Pour être sûr que tout est rentré dans l'ordre, tu peux exécuter la commande :
```shell
rspec --version
```
ou
```shell
rspec -v
```
qui doit normalement te renvoyer les lignes suivantes :
```shell
RSpec 3.11
  - rspec-core 3.11.0
  - rspec-expectations 3.11.0
  - rspec-mocks 3.11.0
  - rspec-rails 5.0.2
  - rspec-support 3.11.0
```

Ainsi tu as tout, et à jour ! 🎉️ 💪️ !

#### 3.1.2 En local dans un projet
Bien sûr il existe des projets ou pour une raison ou une autre tu ne voudras pas la version installée sur ta machine. Tu pourras bien évidemment te servir du fameux Gemfile pour spécifier la version que tu veux dans le projet.

Ce n’est pas compliqué, mais cela va modifier la manière de lancer RSpec. On voit ça dès maintenant.


### 3.2. Lancer RSpec (non pas si loin)
#### 3.2.1 Celui que tu viens d'installer
Lancer RSpec quand tu es sur ta propre machine et que tu ne t'es pas pris la tête avec un Gemfile, tu peux simplement exécuter la commande :
```shell
rspec
```
Dans le dossier où tu veux qu'RSpec se lance. RSpec va alors aller dans le dossier `spec/` et lancer tous les tests trouvés dans les fichiers terminant par `*_spec.rb`.

#### 3.2.2 Une autre version.
C'est un peu différent lorsque tu veux lancer une version bien spécifique uniquement dans un projet en cours.

Pour bien comprendre, il nous faut un peu d'imagination : tu viens de récupérer le repository de quelqu'un. En bon corsaire/pirate/moussaillon que tu es (rayer les mentions inutiles), ton réflexe est donc de faire un petit `bundle install` des familles histoire d'installer localement toutes les gems.

Cette personne, dans son Gemfile, avait précisé : `gem 'rspec', '~> 3.8'`. Non pas que ça te dérange, mais si tu exécutes la commande `rspec -v` tu verras que c'est bien la version `3.8` du projet qui est lancée... pas la `3.11` de ta machine.

Mais tu vas également sûrement te taper un petit warning. Et puis... comment en être véritablement sûr dans le feu de l'action ?

Et bien c'est simple :
```shell
bundle exec rspec
```
Est la commande qu'il te faut.

Attention, ceci n'est pas un cours sur Bundler, et il est probable que si quelqu'un de sénior en dév lit ces lignes, ma tête soit mise à prix.

Mais je vais te faire un aveu. C'est tout de même ce que conseillent deux des contributeurs principaux d'RSpec. J'ai décidé de te transmettre ce savoir tel quel.


⚠️⚠️ Tu noteras que dans le reste de ce parcours je vais utiliser la commande `rspec` et non `bundle exec rspec` par simplicité. ⚠️⚠️
À toi d'adapter la commande en fonction de ce que tu fais.
Note tout de même que pour les projets récents, le risque n'est pas bien lourd.


#### 3.2.3 `rspec` c'est bien... mais `rspec` avec option c'est mieux.
lance un petit
```shell
rspec --help
```
Pour voir.

Tu devrais voir pléthore d'options différentes.
On ne va pas toutes les parcourir, car cela pourrait faire l'objet de ressources entières.
Mais juste je vais te présenter quelques petits tips qui te simplifieront la vie.

##### 3.2.3.1 rspec [files or directories]
Quand on écrit des tests, même quand on est maso et qu'on aime ça, on n’a pas envie de se farcir de lancer l'intégralité de la suite de tests. Surtout que dans les projets classiques en entreprise, on parle de plusieurs minutes... voir des heures pour certains projets.
Pour info l'application Airfrance à une suite de tests qui mets plus de 45 minutes à tourner (non pour le coup, ce n'est pas une blague).

Donc évidemment quand t'as corrigé une typo dans un fichier et que tu veux juste t'assurer que tu n’as rien pété, tu vas simplement exécuter :
```shell
rspec spec/chemin/vers/ton/fichier_spec.rb
```

Et cela ne lancera rspec que sur le fichier en question.

On peut même aller encore plus loin en faisant :
```shell
rspec spec/chemin/vers/ton/fichier_spec.rb:15
```

Ceci ne lancera que le premier groupe de tests qui se trouvera à partir de la ligne 15 du fichier. Une fois ce groupe de tests passé, RSpec n'ira pas plus loin dans le fichier.

##### 3.2.3.2 rspec --only-failures
Ahhh... alors celui-là c'est mon favori. Il m'a fait briller de nombreuses fois en startup.
En fait c'est tout con.
Tu lances rspec une première fois... certains tests sont au vert. D'autres au rouge.
Flemme de lancer la commande :
```shell
rspec fichier1_spec.rb:4 fichier2_spec.rb:12 ... fichierN_spec.rb:42
```
?

Pas de panique :
```shell
rspec --only-failures
```
Ne lancera que les tests qui ont précédemment échoués. Une fois corrigé, le test sortira en vert, et sera retiré de cette liste et ne sera plus exécuté la prochaine fois que relancera la commande. ![Magique](https://c.tenor.com/SWKI18Zs_H8AAAAd/magic-meme.gif)
Pour que cela fonctionne, il faut juste que RSpec enregistre cette liste quelque part.
Je t'invite donc à configurer cela dans un fichier spec. Rajoute les lignes suivantes dans un fichier et hop magic :
```ruby
RSpec.configure do |config|
  config.example_status_persistence_file_path = 'spec/example.txt'
end
```
Cela créera un fichier `example.txt` dans le dossier `spec/`. Mais tu peux aussi le nommer comme tu veux.

Tu as tout de même encore trop de tests qui sont au rouge ?
Pas de panique, tu as dans ce cas, avec la même config :
```shell
rspec --next-failure
```
Qui ne lancera qu'un seul test précédemment échoué à la fois (toujours dans le même ordre). Donc tu corriges ton code pour faire passer ton test au vert et HOP rpsec ira directement au test suivant jusqu'à ce qu'il n'y en ait plus.


## 4. Points importants à retenir
Cela fait déjà beaucoup à retenir d'entrée de jeux. J'ai bien conscience de cela. Mais ne t'inquiète pas, l'essentiel à retenir tiens en quelques lignes :
installer la dernière version de RSpec c'est con comme `gem install rspec`.
Pour l'utiliser dans un projet récupéré chez quelqu'un d'autre c'est mieux de faire un `bundle exec rspec`.
On peut exécuter RSpec uniquement sur certains fichiers avec `rspec spec/chemin/vers/ton/fichier_spec.rb`. En ajoutant `:15` où 15 est le numéro de la ligne que tu veux, tu peux même carrément ne lancer qu'un groupe de tests à la fois.
Tu peux aussi ne lancer que les tests qui ont précédemment foiré via `rspec --only-failures` ou `rspec --next-failure`, moyennant un peu de config.

## 5. Pour aller plus loin
Si t'as du temps, je t'invite à essayer de découvrir les autres fonctionnalités cachées en lisant attentivement le résultat de la commande `rspec --help`.
Tu ne comprendras probablement pas tout, mais il y a certaines choses qui pourraient te plaire. N'oublie pas d'y revenir de temps en temps cette semaine. Tu y découvriras des pépites au gré de ta montée en compétence.

Également si t'as le feu sacré en toi, tu peux regarder la [gem guard](https://github.com/guard/guard-rspec) qui relancera automatiquement les tests que tu viens de modifier.
Tu peux aussi te renseigner un peu plus sur la commande `rspec --tag TAG[:value]` indiquée dans l'aide. Cela pourra te permettre plus tard dans la semaine de ne lancer que les tests controllers par exemple avec un `rspec -t type:request` qui va bien 😉️. Attention : uniquement si tu te sens super super en forme, que t'as fini d'aider les autres, et que t'as fini de prendre de l'avance sur demain.