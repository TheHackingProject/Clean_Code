# Clean Code.

Bonjour à toi et bienvenue dans ce cours de Clean Code. Ici tu vas apprendre globalement à tester ton app avec RSpec

## Pourquoi ce cours.
Quand on sort de THP, on est généralement très fort et on sait faire plein de trucs. Mais une chose manque cruellement : les tests. Que ce soit dans les cursus Fullstack ou Développeur, on aborde ces notions. On te fait même faire des projets avec. On vous l'impose même dans la grille de correction des projets finaux. Pourtant, aux projets finaux, peu de groupes ont fait l'effort d'écrire plus d'un test ou deux, histoire de valider la grille de correction.

On le sait. C'est dur, c'est franchement lourd, et ça ne fait pas avancé les feature. Seulement voilà, dans votre premier job, il faudra bien savoir le faire.

## Le mot de l'auteur.
J'étais aspirant pour THP quand j'ai accepté d'écrire ce cours. On m'a proposé de l'écrire car j'ai impressionné l'équipe par mon savoir-faire bien spécifique. Mais crois-moi, ça n'a pas toujours été le cas. Mon histoire est particulière. Elle mérite d'être racontée pour mieux comprendre ce qui t'attend.

J'ai fait THP en 2018. Pour les personnes qui ne me connaissent pas j'ai fait partie de [ce groupe](https://youtu.be/ELGAJjkaICU) et je fais partie actuellement de [ce groupe](https://youtu.be/Bg72vnqUwAc).

En janvier 2019, je rejoins une boîte en tant que dèv Angular junior. Je vous passe les détails, mais cette boîte ne faisait aucun test... jamais. C'était un peu l'enfer, le code était franchement mauvais, et le niveau des dévs en place est resté coincé dans les années 70. Pour preuve, le backend était en [Delphi](https://fr.wikipedia.org/wiki/Delphi_(langage)) et aucun versionnage de code n'était fait. Véridique, ils n'utilisaient pas git et faisaient des zip de dossier à la main 🤯

Autant dire que j'ai perdu mon temps, et que niveau dev je n'ai pas appris grand-chose.

J'ai donc cherché un nouveau job en Ruby cette fois, et ai fini par décrocher le Graal dans une superbe startup Parisienne faisant de l'IA en Cardiologie.

Le recrutement annonce la couleur : "on ne veut que les top talents et rien d'autre". Je réussis le test technique (pourtant très très énervé). La RH m'annonce que cela faisait deux ans que personne n'arrivait jusque là dans le process, que le CTO est tout seul sur l'app en rails, et que je suis attendu comme le messie.

Quand j'arrive, le code est tellement clean que j'aurai pu manger sur mon IDE. Toutes les best practices sont respectées à la lettre, le workflow d'équipe est ouffissime. Je n'avais jamais vu ni cotoyé des gens aussi brillants et carrés de toute ma vie.

Au bout de 2 jours, mon CTO m'assigne une Issue sur Github. C'est une rake task. Elle n'est pas testée. C'est à moi de le faire.

J'étais en PLS.

En 1 mois il a fallu que j'apprenne d'un seul coup Docker, RSpec, FactoryBot, le CI, les best practices, les conventions de la boîte, la business layer de l'app, l'architecture de l'appli, Rails en API, Postman, et je te passe tout une suite de gems et d'outils tellement longue... sans oublier : apprendre la cardiologie car c'est pour ça qu'on est là.

Mais le truc sur lequel j'étais en PLS, c'était RSpec. Ces tests de l'enfer qui me rendait dingue. Je ne comprenais rien, ni à comment il fallait que je les écrive, ni comment ils fonctionnaient ni rien... Et les personnes qui pouvaient m'aider me regardaient bizarrement : tu ne sais pas ça toi ? Tout le monde avait perdu de vue ce temps où c'était inconnu pour eux, et donc, ils ne savaient pas me l'expliquer.

J'ai cravaché comme un âne et mis le paquet. Mais 5 mois plus tard on a mis fin à ma période d'essai. Imagine à quel point mon syndrome de l'imposteur était énorme à la sortie...

Depuis je me suis juré que les tests ce ne serait plus jamais un problème. Et je n'ai fait que ça pendant des mois (et je continue encore pour être honnête).

Mais arrivé en tant qu'aspirant pour THP, Guillaume, le CTO m'a dit : "Mec, en fait, t'es meilleur que moi en RSpec".

Alors quand on m'a proposé d'écrire ce cours, j'ai pensé à moi. 8 mois plus tôt, en PLS.

Ce cours va retracer toutes les étapes d'apprentissage par lesquelles je suis passé dans la douleur. Sauf qu'on va y aller calmement, étape par étape. Sans le stress d'une app en prod et d'une hiérarchie qui te met la pression.

Ce cours, c'est pour toi que je l'ai écrit. Pour que tu ne vives jamais ce que j'ai vécu.

Enjoy.



## Syllabus
Dans l'ordre les notions abordées seront :

- Lundi
  - Rappels RSpec
  - Nomenclature et explications des différents termes
  - Les matchers
  - Projet Utiliser tous les matchers à sa disposition pour tester une app.
- Mardi
  - La philo BDD de RSpec et pourquoi c'est important
  - Comprendre ce qu'est un test unitaire
  - Savoir quoi tester
  - Projet Savoir quoi tester avec son papier et son crayon.
- Mercredi
  - Utilisation du caching RSpec pour rendre ses tests agnostiques
  - Comment est organisée la doc de RSpec et s'y retrouver.
  - Savoir installer un outil de coverage et s'en servir
  - Projet 100% de coverage sur une app en CLI
- Jeudi
  - Particularité de RSpec pour rails
  - Comprendre les tests d'acceptance et d'intégration.
  - Savoir tester correctement son sign_in, ses controllers et ses API.
  - Mettre en place les fixtures.
  - Projet 100% de coverage sur une app rails complète.
- Vendredi
  - Comprendre les bests practices
  - Quelques ressources pour passer en mode God sur RSpec.
  - Projet Appréhension des mocks et découverte d'autre monde.

## Les objectifs
En sortant de ce cours tu sauras :

- Parler RSpec couramment (c'est comme Fourchelang mais le swag en plus).
- Savoir vraiment quoi tester et avoir une vision beaucoup plus Engineering de ton code.
- Tester n'importe quelle app.
- T'y retrouver et utiliser la doc d'RSpec (ultra important).
- Être capable de discuter philosophie de tests avec des seniors dèv de part le monde entier.

## Philo du cursus.

Ce cursus a été conçu pour aborder des notions très complexes en empruntant un chemin de pensée simple. De ce fait, et parce que le monde du testing ne s'arrête pas à quelques lignes de codes, méthodes et classes bien choisies, les ressources seront nombreuses. La quantité d'info sera dense et je ne te prends pas du tout par la main pour les projets.

En conséquence, les projets peuvent devenir de véritables mines d'or pour devenir cador sur RSpec, ou aussi un véritable enfer. Je sais de quoi je parle.

La différence entre les deux c'est la manière dont tu vas lire les ressources. J'ai fait en sorte qu'en lisant les ressources, tu aies tout ce qu'il faut pour faire les projets.

Mon conseil pour en tirer le meilleur parti est d'en faire une lecture active. Chaque fois que tu tombes sur une ligne de commande dans une ressource : essayes-là, joue avec, tente des trucs et seulement après continue de lire.

C'est la clef.

Et c'est de toutes façon ça qu'il faudra faire en entreprise quand quelqu'un de plus senior t'expliquera la lib utilisée depuis 10 ans et qui n'a toujours pas de doc.

En échange les projets, si tu suis mon conseil, devraient se faire rapidement.


### En groupe ou pas en groupe ?

Nous sommes à THP. Ce cursus se fait en échangeant avec les autres faisant le même cursus. D'une manière générale j'ai essayer de dimensionner les projets pour que tout puisse être fait par une seule personne à chaque fois.

Mais je te conseille vivement de te séparer le travail avec quelqu'un d'autre pour le projet de Jeudi. Car pour le coup, qui sait sur quelle app rails tu vas tomber ?

## Enjoy.

Dans tous les cas, je te souhaite une belle montée en compétence en testing tout au long de ce cursus.
