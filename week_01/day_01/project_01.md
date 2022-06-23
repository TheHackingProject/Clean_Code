# Legacy Code From Hell (with love) 🚀️ (validant)
Dans ce projet, tu vas utiliser tout le "vocabulaire" des matchers fournis dans les ressources du jour pour te sortir du pétrin dans lequel tu viens de te fourrer.

## 1. Introduction
Tu as passé un entretien avec une super boîte qui fait des sandwichs méga bueno (mais sans kinder).
Tout se passe bien, et ton dernier entretien a eu lieu avec le CEO, un certain Bürgerroy, Forbs 30 under 30 🤘️ hell yeah !
Pendant cet entretien il t'annonce fièrement que son équipe tech met un point d'honneur à avoir 100% de coverage sur son appli...

Confiant dans tes capacités : il te recrute ! Félicitations. A toi les pépètes et la gloire  💰️💸️🌟️ !!  Mais maintenant il va falloir assurer.

Aujourd'hui c'est donc ton premier jour de taff. Et là... tu découvres l'horreur. Un code franchement pas pro, voir limite hors jeu. Et le coverage ?
Ouais... en fait il est à 0.
C'est du [pending test](https://relishapp.com/rspec/rspec-core/v/2-0/docs/pending/pending-examples)... Les dév de la boîte ont bien marqué ce qu'il fallait tester. Mais n'ont pas écrit les tests.

Pour ta première PR dans la boîte, on te demande de reprendre les tests, parce que là, sinon, ça fait un peu crade.

Mais le scénario catastrophe ne s'arrête pas là, la personne à l'origine du code, s'est barrée pour burn-out. Tu apprends qu'en fait on t'a recruté pour la remplacer.
Ce qui veut dire qu'il va falloir que tu comprennes ce code cryptique par toi-même !!
Ô joie.
(Bonne chance avec Google sur ce coup)

Félicitations on se disait ?..

## 2. Le projet
Ta mission si toutefois tu l'acceptes, est de tester ce code.
Tu trouveras un vieux fichier de specs dans ce [repo](https://github.com/TheHackingProject/Legacy_Code_From_Hell).
### 2.1. Tes contraintes

Dans ce repo tu y trouves des tests en pending. Apparemment la description des tests est plutôt précise et bien foutue. Tu peux faire confiance à cela.
En revanche : ton lead tech t'interdit formellement de toucher au code pour ton premier jour. Tu ne peux modifier QUE le contenu du dossier `spec/`.
Bien sûr : tu as le droit de toucher au Gemfile. Mais pas de charger un nouveau framework de test. Chez Bürgerroy, on est pas des caramels mous : on veut du RSpec natif.
Mais tu peux très bien te dl de superbes gem supplémentaires pour agrémenter tes outils de débugs, ou les couleurs de sortie dans ton terminal si ça te chante.


### 2.2. L'art et la manière
En gros ce que ton lead tech attend de toi c'est de passer de ça :

```ruby
# # Nom de la classe testée
# ## Nom de la méthode
it 'does something'
```

... à quelque chose de ce genre :
```ruby
describe TestedClass do
 describe '#tested_method' do
   it 'does something' do
     expect(described_class.tested_method).to do_something
   end
 end
end
```

### 2.3. Mais ça marche sur ma machine
Oui tous les dèvs de la boîte vous le diront : ça marche très bien chez eux...
Peut-être que cela aussi fonctionnera chez toi... mais en tout cas, cela ne fonctionne jamais chez les clients.

C'est sûrement que les clients n'ont pas RSpec d'installé chez eux...
Il faut donc t'occuper également du Gemfile et faire en sorte que les tests tournent avec la commande `bundle exec rspec`.

Tips: oui... c'est pour ça que t'as le droit de toucher au Gemfile...

## 3. Rendu attendu
On attend donc de toi un repo github identique à ce qui t'a été donné, mais avec des tests remaniés et qui passent.

Attention, si tu t'amuses à changer le code legacy pour faciliter tes tests... sache que tu risques gros si ton lead tech ou ton CTO s'en aperçoivent...

Ding dong... justement, en parlant d'eux, t'as une nouvelle notif sur ton slack. C'est ton lead qui t'écrit. Le CTO et ton Lead veulent faire une review de ton code demain matin 9h.

Heureusement en tant que mouss/flibustier ou quelque marin que ce soit : la pression tu ne la subis pas, tu la bois pendant les apéros THP.
Pas de panique donc, ce cours t'a suffisamment préparé.
Tu vas t'en sortir haut la main et avec le temps qu'il te reste tu organises l'apéro de mercredi easy peasy.

## 4. Points bonus:
Si tu arrives à organiser les tests avec des describes et des contexts qui vont bien et qui rendent le code lisible et compréhensible, c'est sûr qu'on te file directement une promotion !
