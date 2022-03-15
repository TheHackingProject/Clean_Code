# Legacy Code From Hell (with love)
Dans ce projet, tu vas utiliser tout le "vocabulaire" des matchers fournis dans les ressources du jour pour te sortir du pétrin dans lequel tu viens de te fourrer.

## 1. Introduction
Tu as passé un entretien avec une super boîte qui fait des sandwichs méga bueno (mais sans kinder).
Tout se passe bien, et ton dernier entretien a eu lieu avec le CEO, un certains Bürgerroy, Forbs 30 under 30 🤘️ hell yeah !
Pendant cet entretien il t'annonce fièrement que son équipe tech met un point d'honneur à avoir 100% de coverage sur son appli...

Confiant dans tes capacités : il te recrute ! Félicitations. A toi les pépètes et la gloire  💰️💸️🌟️ !!  Mais maintenant il va falloir assurer.

Aujourd'hui c'est donc ton premier jour de taff. Et là... tu découvres l'horreur. Un code aussi bien présenté qu'un chien qui pose sa mer**. Et le coverage ?
Ouais... en fait il est à 0.
C'est du [pending test](https://relishapp.com/rspec/rspec-core/v/2-0/docs/pending/pending-examples)... Les dév de la boîte ont bien marqué ce qu'il fallait tester. Mais n'ont pas écrit les tests.

Pour ta première PR dans la boîte, on te demande de reprendre les tests, parce que là, sinon, ça fait un peu crade.

Mais le scénario catastrophe ne s'arrête pas là, la personne à l'origine du code, s'est barrée pour burn-out. tu apprends qu'en fait on t'a recruté pour la remplacer.
Ce qui veut dire qu'il va falloir que tu comprennes ce code cryptique par toi-même !!
Ô joie.
(Bonne chance avec Google sur ce coup)

Félicitations on se disait ?..

## 2. Le projet
Ta mission si tu toutefois tu l'acceptes, est de tester ce code.
tu trouveras un vieux fichier de specs dans ce [repo](un_repo_github_de_THP).
### 2.1. Vos contraintes

tu y trouves des tests en pending. Apparemment la description des tests est plutôt précise et bien foutue. tu peux faire confiance à cela.
En revanche : ton lead tech tu interdit formellement de toucher au code pour ton premier jour. tu ne peux modifier QUE le contenu du dossier `spec/`.
Bien sûr : tu as le droit de toucher au Gemfile. Mais pas de charger un nouveau framework de test. (Si )

### 2.2. L'art et la manière
En gros ce que vos lead tech attend de tu c'est de passer de ça :

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

## 3. Rendu attendu
On attend donc de toi le même repo github que ce qui t'a été donné, mais avec des tests remaniés.

Attention, si tu t'amuses à changer le code legacy pour faciliter tes tests... saches que tu risques gros si ton lead tech ou ton CTO s'en aperçoivent...

Ding dong... justement, en parlant d'eux, t'as une nouvelle notif sur ton slack. C'est ton lead qui t'écrit. Le CTO et ton Lead veulent faire une review de ton code demain matin 9h.

Heureusement en tant que mouss/flibustier ou quelque marin que ce soit : la pression tu ne la subit pas, tu la bois pendant les apéros THP.
Pas de panique donc, ce cours t'as suffisemment préparé.
Tu vas t'en sortir haut la main et avec le temps qu'il te reste tu organise l'apéro de mercredi easy peasy.

