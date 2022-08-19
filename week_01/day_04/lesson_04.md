# Faut-il tester les gems qu'on utilise ?
Forcément, la réponse c'est "ça dépend". Sauf que ça dépend, ça dépasse. Alors on va voir comment arbitrer sur la question dans cette ressource.

## 1. Introduction
Je t'ai parlé des tests d'intégrations. Je te les ai décrits comme étant les tests pour s'assurer que des services, API, mais aussi code tiers se comportent bien de la même manière.

Donc il est légitime de se poser la question de tester, au moins par intégration, les différentes gems qu'on pourrait utiliser.

## 2. Le rabbit hole :
On vient d'utiliser Devise, et ses helpers de tests.
Et si t'es toujours à balle, tu dois forcément te poser la question : mais est-ce qu'on ne devrait pas tester Devise ? Et puis tant qu'à faire : est-ce qu'on teste les autres gems dont on se sert ? Kaminari par exemple ?
Mais quid des méthodes de rails ? Devons-nous tester les associations de modèle par exemple ? Et si j'utilise un `scope`, je dois le tester aussi ?
Et ma fonction `to_a` ? mon `to_json` aussi ?
Mince est-ce que je ne devrais pas tester Ruby en fait ?

Bref... tu vois bien que cette dernière question est ridicule. Mais la première pas du tout. Alors : elle est où la limite ?

## 3. La criticité de tes tests.
En fait, l'arbitrage à faire n'est pas anodin. Encore une fois il dépendra grandement de l'entreprise, ou le projet pour lequel tu travailles.

Il faut se poser deux questions :
- Est-ce que si ce bout de code plante, mon application perd de la valeur ?
- Est-ce que je peux avoir confiance que ce code est bien robuste ?

### 3.1. Perte de valeur de l'app
Si ton app perd de la valeur lors de l'échec d'un bout de code, c'est que ce bout de code est critique.

Un exemple concernant l'app qui gère les pokémons de tout à l'heure : si l'API plante, ton app ne sert à rien, ce code est donc critique.
En revanche sur cette même app, peut-être que c'est pas bien grave que tes pokémons soient correctement paginés. Donc la gem Kaminari, si elle plante, c'est pas bien grave : ce code n'est pas critique.

Attention, je ne dis pas qu'un code qui plante est une bonne chose.
Simplement je dis que si tu n'as du temps que pour tester une seule partie de ton code, alors il vaut mieux se concentrer sur la partie critique.

### 3.2. Est-ce que je peux avoir confiance ?
Clairement, c'est toujours le trade off de l'ingénierie logicielle. D'autres gens ont travaillé dur avant toi pour produire un code de qualité.
Il est tout à fait possible que cela plante... mais si c'est le cas, tu n'y pourras pas grand-chose. Il faut accepter de prendre le risque.

C'est le cas de Devise, des gems de [Thoughbot](https://thoughtbot.com/), mais aussi de rails, sans oublier Ruby (qui est écrit en C je te rappelle).

Donc il est clair qu'on ne va pas tester ces gems ou frameworks. On va leur faire confiance dans le fonctionnement. On va aussi faire confiance dans la stabilité de leur fonctionnement.

Devise ne remplacera pas du jour au lendemain son fameux `current_user` par `current_human` par exemple. Donc on est tranquille.

En revanche, si tu utilises la gem [Holidays](https://github.com/holidays/holidays) qui sert à te dire si un jour est férié ou non, dans n'importe quel pays du monde, et bien c'est peut être préférable.

Alors si c'est pour gérer ton propre calendrier, ce serait surement overkill. Mais si tu bosses chez [Swile](https://www.swile.co/fr-fr/), et que pendant les jours fériés l'app doit renvoyer un refus de paiement en titre restaurant, ET QUE cela engage ta responsabilité pénale si tu te foires, ben il vaut peut-être mieux consolider par des tests d'intégrations. Cela t'évitera des frais d'avocats.

Remarque juste une chose.
Chez Swile : ils utilisent véritablement la gem Holidays, ils ont véritablement cette responsabilité juridique...
...
...mais ils n'ont aucun test d'intégrations sur cette gem...

## 4. Points importants à retenir
Ça dépend.

Mais dans le doute, pour ce cursus : détends-toi. Et considère que les dévs des gems que tu utilises ont bien fait leur boulot.
Dans ton prochain taf en revanche : pose la question à tes collègues.
