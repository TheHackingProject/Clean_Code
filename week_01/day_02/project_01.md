# Titre du projet
<!-- Description du projet au format texte et en 1 paragraphe max (pas plus) -->
A ton tour de décrire QUOI tester.

## 1. Introduction
Tu te souviens de ce bon vieux TicTacToe des familles qui t'avait fait suer autant que si t'avais fait de la muscu dans un sauna ?
Eh ben on va le tester.

## 2. Le projet
Ce projet est vraiment pas compliqué.
Il faut décrire qu'est ce qu'il faudrait tester si on voulait passer le code de ce TicTacToe sous RSpec.

Clone ce repos et observe comment il fonctionne.

Ne sois pas perturbé. Ce n'est pas le meilleur code du monde pour faire un TicTacToe. Pour être franc c'est celui que j'avais fait lors de la session 5 de THP il y a quelques années maintenant.

L'idée est d'aller parcourir chaque fichier `.rb` et d'écrire les tests unitaires pour chacune des méthodes que tu trouveras dans ces fichiers.


## 3. Rendu attendu
Ce qu'on attend de toi c'est un repo contenant un simple fichier markdown avec ce format

```markdown
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

On s'attend uniquement à avoir des tests dit unitaires, c'est à dire qu'on test chaque méthode séparément. Si une méthode en appelle une autre, on peut écrire : `on s'attend à ce que la méthode truc_mumche appelle la méthode 'chose' du fichier xxx.rb`
On te laisse le soin de voir et de décrire les cas qu'il est nécessaire de tester.

Tu seras évalué à la fois sur :
- les points critiques, c'est-à-dire ce qu'il faut impérativement tester
- les points importants mais moins grave, i.e les choses qu'il faudrait tester si on est parano.
- les points qu'il ne fallait pas tester. En effet, il y a des méthodes, ou parties de méthodes ou des use cases qu'il n'est pas nécessaire de tester. On vérifiera demain que tu ne tombe pas non plus dans une frénésie de tests inutiles ou redondant.

Good luck.