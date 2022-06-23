# Mais on teste quoi au juste ??
Comme la grande question sur l'univers, la vie et le reste, la réponse c'est 42.

## 1. Introduction
En fait, on répondrait plutôt `ça dépend`. Oui malheureusement ce que je m'apprête à raconter ici pourrait faire débat pendant des années au sein de la communauté.
Mais a minima, cette ressource te fera passer de "J'ai no clue de ce que je dois faire" à "Ouais... mais là en fait, dans ce cas précis, je ne suis pas tout à fait d'accord".

En gros cette ressource va t'apprendre à formater ton cerveau pour réfléchir `test`.

## 2.Historique et contexte
Déjà, parlons un peu de BDD.
Oui BDD et pas TDD. TDD c'est `Test` driven development. On écrit un test, puis le code qui permet de faire passer le test.
Le BDD c'est pour `Behavior` driven development. Je décris un comportement. Je décris comment mon bout de code est censé se comporter face à une telle situation. Puis alors j'écris le code en question.

### 2.1 Pourquoi BDD ?
Dans le monde du développement (et pas que) c'est monnaie courante d'avoir un client, un lead, un cahier des charges, qui formule une demande.... pas assez détaillée.

Genre c'est dur, personne ne comprend rien, la demande a été formulée à l'arrache en fin d'une réunion ou personne ne voulait être.

Il manque généralement des infos qui amènent des questions du genre : "oui mais dans ce cas-là on fait quoi ?".

Voilà comment est né [le BDD](https://fr.wikipedia.org/wiki/Programmation_pilot%C3%A9e_par_le_comportement)
>Le BDD combine les techniques et principes du développement piloté par les tests avec les principes de la conception pilotée par le domaine et de la conception orientée objet pour partager une méthode et des outils communs entre les équipes de développement et les autres parties prenantes.

En gros l'idée c'est d'écrire le cahier des charges sous forme de tests. Et, avec des tests orientés sur le comportement de l'appli, c'est uniquement si le comportement souhaité change que l'on va changer les tests ! Pas lorsqu'on a oublié un cas tordu de test d'un type de variable inattendu etc...

Ainsi, tes donneurs d'ordres qui ne panent rien au code ne pourront pas dire "Ah mais non mais c'est pas ça que je voulais en fait".

Avec une telle description tu comprends la raison pour laquelle RSpec a choisi de s'orienter sur du BDD.

### 2.2 La manière RSpec
Oui, RSpec à été créé pour rendre la vie des dèv plus simple. Pas juste pour casser les pieds de moussaillons en PLS sur un projet chaton.

Parce que oui de l'aveu même de gros contributeurs d'RSpec, les fonctionnalités d'RSPec permettent:
- de décrire facilement le comportement d'une appli
- de pouvoir séparer facilement le use case du vrai test.
- de pouvoir te concentrer uniquement sur ce dont tu as besoin pour faire passer le prochain test.

En échange, les créateurs ont bien conscience que :
- Ecrire des tests prend du temps.
- Lancer de longues suites de tests prend du temps (ou du temps d'apprentissage pour pouvoir réduire la suite en question)
- Lire des suites de test ultra optimisé c'est souvent imbuvable et nécessite de basculer d'un fichier de config à l'autre pour comprendre.

#### 2.2.1 Ce que font vraiment les spec (tests)
Non, écrire des tests n'est pas le but d'RSpec en fait.
Le but d'RSpec c'est d'obtenir le bénéfice de cette écriture. Et contrairement à ce que pense Jean-michel Obvious... ce n'est pas juste pour "éviter les bugs".

Ecrire des tests c'est pour :
- Créer de la confiance en votre code et l'avancée de votre projet.
- Eliminer la peur de tout péter au moindre changement.
- Mieux, ça permet de faire un gros refacto, car si les comportements (behavior) sont les mêmes, alors les tests aussi.
- Guide le design, en décrivant le comportement on devient son propre client face à ce que le code qu'on écrit doit faire, et ça change tout.
- Permet de maintenir le code dans le temps.
- Documente le code !! Oui, ça à l'air idiot mais ça fait du bien de ne pas avoir à le faire à la main.

Personnellement j'ai même vu RSpec utilisé pour générer les rapports de preuve de sécurité par design pour une appli médicale.


Alors ça donne envie de savoir comment faire non ?


## 3. Quoi tester.
Décider quoi tester est un art.
Un art qui commence par savoir ce qu'on ne va PAS tester en fait.

### 3.1. Le projet trop testé
Maintenant tu le sais, le TDD est un peu mainstream et ça ne te fera pas briller en soirée dansante (laisse tomber j'ai essayé).
Une des raisons à cela, est qu'en TDD, les gens ont tendance à trop tester.

```ruby
def is_3?(number)
  return false unless number.is_a? Integer
  return true if number == 3
end
```

Dans ces quelques lignes de code qui ferait pleurer du sang a n'importe quel dèv sénior (je vais l'appeler Guillaume par la suite, mais donne lui un petit nom à toi 😉️), on pourrait imaginer tester ce que fait la fonction quand elle reçoit un string. A priori elle renvoie `false`.
Mais doit-on le tester ? Dans ce cas est-ce qu'on teste aussi ce qu'il se passe quand l'argument est un `Object`, `Float` ou `Array` ?

On pourrait.

Imaginons qu'un utilisateur utilise `is_3?('3')`... on aurait légitimement envie de dire que la méthode devrait renvoyer `true`...

Et là, sans faire gaffe tu viens de tomber dans le rabbit hole du "Je teste tout et beaucoup trop, ce qui m'amène à refactor ma méthode 15 fois sans jamais pouvoir la livrer".

Imagine également que la personne avant toi, réelle Stakhanoviste distinguée du testing, ai prévue absolument TOUS les cas de figure...
Tu as donc une jolie centaine de tests. Chouette.
Maintenant tu dois modifier cette méthode... elle s'appellera `is_3_or_4_?`... tu la sens comment la centaine de test à modifier ?

La conclusion c'est que tous les tests ne méritent pas l'effort d'être écrit. Encore moins maintenus.

S'il te fallait une preuve supplémentaire, voici [une réponse de Kent Beck](https://stackoverflow.com/questions/153234/how-deep-are-your-unit-tests/153565#153565) sur ce sujet.

Et Kent Beck... [il pèse](https://fr.wikipedia.org/wiki/Kent_Beck) dans le game.

### 3.2. Savoir quoi NE PAS tester
Un test c'est un point de couplage entre ton fichier de test et ton code. Et donc chaque changement d'un côté implique d'autant plus de changement de l'autre.
Donc ce que tu veux en fait c'est la quantité minimale de tests pour permettre de sécuriser le comportement.

Maintenant que Guillaume à fini de saigner, reprenons cette méthode:
```ruby
def is_3?(number)
  return false unless number.is_a? Integer
  return true if number == 3
end
```
Ce qu'elle fait exactement, c'est renvoyer true ou false. C'est tout. C'est ça son comportement.
Et elle le fait sur la base de ce qu'elle reçoit.

Simple.

On est donc d'accord qu'on va tester qu'elle renvoie bien `true` lorsqu'elle reçoit `3`. C'est ça son comportement canonique.

Le reste, c'est pas forcément obligatoire à tester.

#### 3.2.1 le cas non integer
Cela fait aussi partie de son comportement que de renvoyer `false` lorsqu'on lui donne autre chose qu'un `Integer`.
Doit-on le tester ?

En fait ça dépend. Imagine que cette méthode soit directement une partie de controller, et que c'est l'utilisateur qui décide de l'objet reçu en argument.
Bon, dans ce cas, tu as effectivement envie de vérifier que la méthode renvoie bien false pour tous les cas hors `Integer`.

Mais imagine maintenant que cette méthode soit uniquement utilisée en interne du code par d'autres développeurs. C'est peut-être pas la peine d'aller écrire cent tests différents pour tous les cas.
On prend une classe au hasard... celle qui te fait marrer, mettons `String` (oui moi j'ai ce niveau d'humour désolé). Et puis tu tests que la méthode renvoie bien `false` si on lui donne la chaine de caractère `'3'`.

Basta.

La vraie version qui serait en prod de manière professionnelle, genre si t'étais chez Google, serait de créer une collection de classes différentes, genre `[String, Object, Float, User]` et d'écrire un test qui prend aléatoirement une de ces classes, créé un objet dedans complètement random, et test que la méthode renvoie bien `false`.
Ce serait plus dur... mais en cas de changement, il n'y aura qu'à modifier cette collection. Bonus point si cette collection s'instancie toute seule en allant chercher toutes les class d'objets possible. Comme ça si ton code amène de nouvelles classes, pas besoin de penser à mettre à jour cette collection à la main.

Encore une fois, ce que je dis se discute sincèrement : est-ce bien la peine pour une méthode pareille ?

La version réelle que tu risques de rencontrer : c'est que ce n'est pas testé. C'est bête mais c'est ça de moins à faire, à maintenir, et comme le code est utilisé en interne, on suppose (hum) que tu as documenté cette méthode quelque part. Donc le développeur suivant saura comment s'en servir.

Je ferais quoi moi ?
Perso je testerai avec une classe Random. Mais surtout, j'irais refactor cette méthode fissa avant que Guillaume n'attrape ce cutter.

#### 3.2.2 Le cas que tu n'as pas vu...
En revanche, il y a un cas que seuls les gens aux yeux de lynx auront vu...

CETTE METHODE RENOIVE AUSSI NIL.

Et c'est un comportement. Il faudrait donc le tester.

#### 3.2.3 Conclusion
En conclusion, voici ce qu'il faudrait tester sur cette méthode :
```markdown
On s'attend à ce que la méthode `is_3(3)` renvoie true
On s'attend à ce que la méthode `is_3(x)` renvoie nil pour `x.between?(0..2) || x.between?(4..)`
On s'attend à ce que la méthode `is_3(object)` renvoie `false` pour `object = 'toto'`.
```

Et sur ce dernier test, on pourra franchement en discuter longuement avec son CTO/Lead/ ou quiconque fera une review de ton code.
Spoiler ce sera pas Guillaume, il est parti apparemment pour élever des chèvres dans le Larzac.




### 3.3 Les méthodes un peu plus complexes (et mieux écrites)
On est d'accord, Guillaume rentré dans les ordres dans un monastère caché nous le confirme, la méthode précédente était dégueux.
Mais surtout sa particularité c'est de n'avoir qu'une valeur de retour.
Il existe d'autres méthodes qui ont d'autres comportements.
Les méthodes qui changent une variable au passage, de ce genre-là :
```ruby
@core_variable = 'tata'

def initialize
  @core_variable = 'toto'
end
```
Ici la variable `@core_variable` appartient à la classe, pas à la méthode initialize. La valeur de retour de la méthode est 'toto', et la méthode change la variable `@core_variable` de `"tata"` en `"toto"`.


Celles qui provoquent aussi un effet de bord comme celle-là :
```ruby
def side_effect(my_variable = nil)
  raise 'Must be used with not nil variable' unless my_variable

  my_variable
end
```
Cette méthode return directement son argument d'entrée. Pour ainsi dire, elle ne fait donc "rien".
A ceci près que si le paramètre d'entrée est `nil` alors la méthode provoque une erreur.
C'est un effet de bord. Celui-là est clairement visible. D'autres seront plus discrets. Il faudra être attentif à cela.

Bien sûr une méthode peut faire tout ça en même temps. Mais c'est peu recommandé, si tu en trouves une parle-en à ton ou ta lead dèv.

Quoiqu'il en soit il faut nécessairement tester tout ça :

Tester les différentes valeurs de retours suivant les cas. N'oublie pas d'être critique par rapport à ces tests : sont-ils nécessaires ?
Tester les effets de bord : se mettre dans le cas où ils apparaissent et vérifier que la méthode les provoque bien.
Tester que les variables qui sont modifiées par la méthode l'ont bien été selon les cas.

Normalement tu saurais faire déjà tout ça avec RSpec. Mais je comprends que ça fasse un peu peur. Pas de panique : on va y aller petit à petit.


### 3.4 Un peu de boole pour finir (non pas celles-là)
C'est un cas particulier, et assez tordu, du cas où la méthode n'a qu'une valeur de retour.
Sauf que cette valeur de retour est à décider entre plusieurs cas possibles.

Observons cette méthode directement issue du code en production de THP à l'heure où j'écris ces lignes :
```ruby
class Project
# le code classique d'un model rails, relation has_many user as member, et attributs locked, et creator.

# C'est le code qui permet à un user de quitter un project.
  has_and_belongs_to_many :members, class_name: "User"
  belongs_to :creator, class_name: "User"

  def remove_member(user)
    (errors[:base] << "Ce projet est verrouillé !") and return false if locked
    (errors[:base] << "Le créateur ne peut pas quitter son projet !") and return false if user == creator
    # D'autres lignes du genre... mais je simplifie un peu la méthode.

    members.delete(user) if members.include?(user)

    true
  end

  # Un petit ajout juste pour toi pour que tu comprennes
  # locked est un attribut d'un project.
  # Imagines que quand tu créé un project : Project.create(locked: true)
  # ou idem Project.create(locked: false).

  # Bien sûr il faut que tu imagines qu'il existe un controller quelque part, ou un model, qui vient modifier la valeur de cet attribut.
end
```
Oui cette méthode aussi est dégueux. Je sais. Il faut bien comprendre que tous les codes legacy contiennent ce genre de monstruosités malheureusement.

Et pendant que Guillaume commence à arroser d'essence l'intérieur du monastère, regardons de plus près cette méthode.
Elle modifie deux variables existantes : `errors` et `members` et renvoie true ou false suivant les cas.

La principale difficulté pour tester cette méthode, c'est encore de trouver les différents cas possibles.

On pourrait le faire de tête, certains y arrivent, mais je préfère te donner un outil pour y parvenir quelque soit la situation dantesque : l'[algèbre de bool](https://fr.wikipedia.org/wiki/Alg%C3%A8bre_de_Boole_(logique)).
Alors on ne va pas rentrer dans la théorie mathématique rassure-toi. Juste dans le principe simple des ensembles.

Pour cela un schéma vaudra mieux que mille mots et donc on va dessiner.

Je ne peux pas tout te montrer du code, mais en gros, l'attribut `locked` renvoient true ou false, tandis que la méthode `creator` comme tu l'imagines renvoie un user qui `belongs_to` le `group`

On fait du test unitaire et on teste, nous, la méthode `remove_member`. Cela veut dire qu'on ne se préocupe pas de savoir si les attributs et méthodes `locked`, et `creator` fonctionnent correctement. On va juste partir des cas différents qu'elles peuvent renvoyer.

Comptons les :
- Le cas ou locked = true
- Le cas ou locked = false.
- Le cas ou user == creator
- Le cas ou user != creator
- Le cas ou user est inclus dans member
- Le cas ou user n'est pas inclus dans member.

Ca en fait pas mal des cas...

La question donc c'est : doit-on le tester ?
Car on pourrait très bien tester tous les cas possibles ci-dessus de manière brutale. Mais ça ferait 2 puissance 3 cas différents soit 16 tests avec un cas de figure différent à écrire pour chaque test (revérifie, mais normalement je ne me suis pas trompé).

Or si on regarde bien, le cas `user == creator` n'a pas lieu d'exister si `locked == true`, car normalement la méthode n'y passe jamais.
Je te laisse réfléchir là dessus : c'est important que tu fasses l'effort de comprendre pourquoi. Demande aux autres de ta promo si besoin.

Commençons donc à tester par le début, la première ligne.
On se place simplement dans le cas où `locked == true` (je t'expliquerai comment faire en RSpec bien sûr, mais ce n'est pas le point ici) et on va tester que la méthode `remove_member` renvoie bien `false` d'une part, mais aussi que la variable `errors` contient bien le message d'erreur associé.

Bon ça c'est fait, c'était facile.

Maintenant on va juste écrire 1 test qui va vérifier que si `user == creator` ou `members.include(user)` rien ne change dans ce cas-là, la méthode se comporte de la même manière.

C'est la que la théorie de Boole, et les diverses théories ensemblistes interviennent. Ne panique pas, je suis juste en train de faire mon malin. Avec un schéma tout le monde sait faire ça.
Regarde, ça c'est l'ensemble des projects possibles:
![project](https://i.imgur.com/XmdIJpd.jpg)
et un project peut être, soit `locked == true` (en bleu), soit `locked == false` en vert.

Donc si tu es dans la zone bleu : peu importe le reste.

Si tu es dans la zone verte maintenant on a le cas où le user est creator, et celui où il n'est pas creator.
![projects_not_locked](https://i.imgur.com/DVF0cUx.png).
Dans ce cas, il n'y a pas besoin de tester ce qu'il se passe quand on est locked ou pas, c'est déjà traité depuis tout à l'heure !

Pour bien comprendre je vais écrire mes tests en Markdown. Pour l'instant ils ressemblent à ça :
```markdown
# Class Project
## Méthode remove_member
### Dans le cas où le project est locked
on s'attend à ce que la méthode renvoie `false` et que `errors` contienne la chaine `"Ce projet est verrouillé !"`.

### Dans le cas où le project n'est pas locked
  #### Dans le cas où le user est le creator
  on s'attend à ce que la méthode renvoie `false` et que `errors` contienne la chaine `"Le createur ne peut pas quitter son projet !"`.

  #### Dans le où le user n'est pas le creator
  on s'attend à ce que la méthode renvoie `true`
```

Dans ce dernier cas de figure il suffit de tester les deux cas :
- le user fait partie des members : il faut vérifier qu'il est bien enlevé.
- le user ne fait pas partie des members : il faut vérifier que members n'a pas bougé.


Voilà... Si on compte bien, tu as donc fait 5 tests avec autant de cas de figures différents. C'est long, c'est lourd, mais nous sommes loin des 16 tests qu'on imaginait au début.
Au passage : félicitations, tu viens de manipuler les théories ensemblistes (t'as vu c'est fastoche hein ?).

Pour info les dessins ont été faits avec [Excalidraw.io](https://excalidraw.com/) que je te recommande si tu ne connaissais pas.



### 3.5 Les autres types de tests
Juste pour que tu ne paniques pas, si tu te poses la question, je vais aborder les autres types de tests.
Si tu ne te poses pas la question, ce n'est pas grave nous l'aborderons plus en détail dans la semaine, tu peux donc sauter cette section.

L'idée ici est juste de savoir qu'ils existent pour le moment, mais on les abordera quand on fera du rails, ce sera beaucoup plus parlant.

Jusque là on vient de voir ce qu'on appelle les tests unitaires.
On teste chaque méthode séparément, en admettant à chaque fois que les autres méthodes fonctionnent parfaitement.
Cela permet de vérifier exactement à chaque micro étape du projet, que tout est bien conforme à ce qui est attendu.

Mais même si tout fonctionne séparément, cela ne garantit pas que tout fonctionne ensemble.
[La preuve en images](https://www.youtube.com/watch?v=Oj8bfBlwHAg).
On parle dans ce cas-là d'acceptance tests, ou de tests end to end (e2e pour les intimes).

Enfin il y a les tests d'intégrations.
Cette fois-ci tu testes une autre partie du code que généralement tu ne possèdes pas. Comme par exemple une API. Tu vas écrire des tests qui permettent de t'assurer par exemple que l'API tierce fonctionne bien comme la documentation l'indique.
L'idée derrière ça c'est surtout :
- De documenter comment fonctionne l'API au sein de ton propre code.
- De vérifier que l'API n'a pas changé suite à une mise à jour de la mort qui t'aurair échappée.

Et en plus c'est recommandé par [Bob](https://fr.wikipedia.org/wiki/Robert_C._Martin) dans son livre [Clean Code](https://www.google.com/search?q=clean+code&sxsrf=APq-WBt84WaPCL3KDLRvTa5dBjr5eSIOQQ:1650634941439&source=lnms&tbm=bks&sa=X&ved=2ahUKEwiaidqq5qf3AhVUtKQKHW5ND6kQ_AUoBHoECAEQBg&biw=1745&bih=812&dpr=1.1).

A la fin de ce cursus d'une semaine je t'en recommande chaudement la lecture, c'est le doudou que sert fort Guillaume, la larme à l'oeil, en regardant les braises de son monastère.


## 4. Points importants à retenir
On a vu ce que c'était que les tests unitaires : des tests qui ciblent de manière très fine une et une seule méthode à la fois.
On a vu aussi les autres types de tests qu'on laisse de côté pour le moment : les tests d'acceptance (ou e2e) pour vérifier que tout fonctionne sur toute la chaine de code et les tests d'intégrations, plutôt réservés à du code qui ne nous appartient pas (API ou même des services par exemple).

On a vu qu'il faut réfléchir à ce que fait réellement une méthode : quelles valeurs elle retourne, a-t-elle un effet de bord, modifie-t-elle la valeur d'une variable, mais surtout dans quels cas.
On a vu également qu'il faut surtout réfléchir à ce qu'il n'est PAS nécessaire de tester. Soit parce que le scope de la méthode est à peu près certains, où que les cas seraient trop nombreux au quel cas il faut essayer de rationnaliser les efforts par rapport au risque pris et aux personnes concernées par ce bout de code (utilisateur final, ou développeur en interne).

Et enfin, la plus grosse partie : comment bien discerner tous les cas possibles en se servant de ~théorie ensemblistes~ dessins.
