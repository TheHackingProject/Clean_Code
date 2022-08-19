# Nouvelle Startup, nouvelle vie !
A ton tour de décrire QUOI tester.

## 1. Introduction
Fort de ton expérience accumulée chez Bürgerroy, tu t'es dit que tu ne pouvais pas rester dans une équipe aussi mal structurée.
Tu es donc partie dans une startup super prometteuse mais encore très early stage, qui propose un jeu pour mobile dernier cri en pay-to-win : **"TTT Corp"**.

Tes exploits chez Bürgerroy en termes de testing ont fait de toi une super star en RSpec. Ton job, c'est d'accompagner la CTO de TTT Corp dans la limitation de dettes technique.
Le jeux n'en est qu'au MVP, mais il est validé par les stakeholders, et tout le monde mise dessus. On veut que TTT Corp devienne la nouvelle Licorne française 🦄️🤘️!
Avant d'aller plus loin : il faut avoir une suite de tests afin de pouvoir itérer sereinement sans la crainte de tout péter.

Bref. Gros challenge, mais après Bürgerroy, plus rien ne te fait peur.

Surprise, ta CTO à fait THP en 2018.
Et tu te souviens de ce bon vieux TicTacToe des familles qui t'avait fait suer autant que si t'avais fait soulever des haltères dans un sauna ?
Eh ben c'est celui que ta CTO à fait pendant son cursus.

Bon courage.

## 2. Le projet
Ok. Ce n'est pas le jeu de tes rêves... mais il y a de l'argent (et de la gloire) en vue.

Pour que le jeu passe à l'étape supérieure, il est important de définir ce qui fait que le MVP fonctionne.
Il faut donc le spécifier.

Ta CTO attend donc de toi de décrire ce qu'il faudrait tester si on voulait passer le code de ce TicTacToe sous RSpec.

Pour cela, comme tu fais désormais partie de la société : tu peux cloner ce [repo](https://github.com/TheHackingProject/TicTacToe) et observer comment il fonctionne.

Ne sois pas perturbé. Ce n'est pas le meilleur code du monde pour faire un TicTacToe. On le sait. C'est le principe du MVP.

L'idée est d'aller parcourir chaque fichier `.rb` et d'écrire les tests unitaires pour chacune des méthodes que tu trouveras dans ces fichiers.


## 3. Rendu attendu
Non non non. Tu ne feras pas de code aujourd'hui. C'est bien trop important. Dans le monde professionnel on prend le temps. On veut faire les choses bien.

(Pour le coup ce n'est pas une blague, je suis sérieux).

Donc on va faire une réunion. On attend de toi que tu décrives d'abord ta stratégie de tests.
Le jeu tu le connais.
RSpec : on t'as recruté pour.

Maintenant on veut savoir CE QUI DEVRA ÊTRE TESTÉ : comment et pourquoi.

Pour préparer cette réunion avec ta CTO, tu vas créer un repo contenant un simple fichier markdown avec ce format

```md
# Spec TicTacToe

## bidule.rb
### Méthode truc_muche
#### Use case X
tu décris un environnement qui permet de faire un test
#### Test 1
en se plaçant dans le Use Case X, je m'attends à ce que la méthode truc muche me réponde la chaine de caractère 'plop'.


## chose.rb
### Méthode xxxx
etc...


```

De cette manière ta CTO verra que tu es clairement geek. Ça l'emplira de joie.

Pour le moment, on ne sait pas comment le jeu sera encapsulé pour une appli mobile : donc on s'attend uniquement à avoir des tests dits unitaires, c'est-à-dire qu'on teste chaque méthode séparément. Si une méthode en appelle une autre, on peut écrire : `on s'attend à ce que la méthode truc_mumche appelle la méthode 'chose' du fichier xxx.rb`
On te laisse le soin de voir et de décrire les cas qu'il est nécessaire de tester.

Pour cette réunion on t'attend particulièrement sur les points suivants :
- les points critiques, c'est-à-dire ce qu'il faut impérativement tester.
- les points importants mais moins graves, i.e les choses qu'il faudrait tester si on était parano.
- les points qu'il ne fallait pas tester. En effet, il y a des méthodes, ou parties de méthodes ou des use cases qu'il n'est pas nécessaire de tester. On vérifiera demain que tu ne tombes pas non plus dans une frénésie de tests inutiles ou redondants.

N'oublie pas, dans une startup : Cash is King. Le temps jouera contre toi. Donc ne sois pas trop gourmand en quantité de tests. Privilégie la qualité. Ou alors aies de bons arguments.

La réunion est demain matin.

Good luck.
