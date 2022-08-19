# Doc, my old friend
La doc d'RSpec, de l'aveu des contributeurs eux-mêmes, c'est franchement mal organisé et pas beginner friendly. Je t'explique comment t'y retrouver.

## 1. Introduction
Effectivement, la documentation d'RSpec, tout le monde le dit : c'est le foutoir, c'est pas compliqué on dirait ma piaule.

Toutefois, avec un peu d'aide et un chien d'aveugle on peut s'y retrouver et même prendre gout à la manière dont elle est rédigée.
(Si je te jure, j'ai même appris des trucs en écrivant ce cours !)

## 2.Historique et contexte
Si tu te souviens bien ce que je t'ai raconté il y a quelques jours, RSpec est divisé en plusieurs gems :
- rspec/rspec-rails
- rspec/rspec-expectations
- rspec/rspec-mocks
- rspec/rspec-core

Et donc... la doc aussi.

De plus, le site officiel ne porte absolument pas le bon nom : [relishapp](https://relishapp.com/). Oui la doc est bien là.

## 3. La doc
Clairement il n'y a pas grand chose sur la page d'accueil : [relishapp](https://relishapp.com/).
Mais en fonction de ce que tu cherches, tu trouveras ta réponse dans une des parties de la doc...
Sur la page d'accueil si tu regardes en bas à droite, tu trouveras les liens vers les différentes parties :
- [rspec/rspec-rails](https://relishapp.com/rspec/rspec-rails/docs)
- [rspec/rspec-expectations](https://relishapp.com/rspec/rspec-expectations/docs)
- [rspec/rspec-mocks](https://relishapp.com/rspec/rspec-mocks/docs)
- [rspec/rspec-core](https://relishapp.com/rspec/rspec-core/docs)


### 3.1. rspec/rspec-expectations
Il faut bien prendre un exemple, donc je te demande de cliquer sur ce lien : [rspec/rspec-expectations](https://relishapp.com/rspec/rspec-expectations/docs).

rspec-expectation, c'est là où sont rangés la liste de matchers que je t'avais donné l'autre jour.

Bref.

Sur cette page d'accueil tu trouves de quoi changer la version d'RSpec dans un menu déroulant en haut à droite. En fonction de ce que tu choisis, le contenu de la doc pourra changer.

Il faut que tu prennes la doc un peu comme un dossier, possédant des sous-dossiers.
Les sous-dossiers : ils te sont présentés dans le bandeau à gauche de la page.

Tu retrouves les sous-dossiers du dossier en cours tout en bas de chaque page s'il y en a.

Voilà comment on se promène dans une des docs des gems d'RSpec.

### 3.2. Comment ça se lit
Toujours à partir d'[rspec/rspec-expectations](https://relishapp.com/rspec/rspec-expectations/docs), va dans [Built in matchers](https://relishapp.com/rspec/rspec-expectations/v/3-11/docs/built-in-matchers).

Prends une grande inspiration : ça va bien se passer.

En gros tu viens d'aller au "Sommaire" du sous-dossier "Built in matchers" de la doc d'rspec/rspec-expectations.

Et comme tout sommaire un peu académique, on te présente un résumé de ce que tu vas trouver dans les sous-parties.
Sous-parties pour lesquelles tu peux avoir plus de détail dans le bandeau à gauche, mais aussi tout en bas de la page.

Soyons fous : allons dans [Equality Matchers](https://relishapp.com/rspec/rspec-expectations/v/3-11/docs/built-in-matchers/equality-matchers).

A chaque fois la doc d'RSpec se compose de la même manière :
- Une partie générale où on t'explique la notion de manière un peu abstraite
- Des tests avec juste un commentaire : "le test passe" ou "le test ne passe pas".

C'est en général ça qui rebute tout le monde.

C'est qu'il faut lire les tests pour comprendre comment fonctionne la notion abordée sur la page.

En gros : pour lire la doc d'RSpec il faut comprendre comment lire du... RSpec... et c'est pour ça que c'est difficile d'aborder la doc.

Maintenant que je t'ai expliqué un peu et que tu es capable de lire du RSpec regardons de plus près juste le [premier scenario](https://relishapp.com/rspec/rspec-expectations/v/3-11/docs/built-in-matchers/equality-matchers#compare-using-eq-(==))

### 3.2.1 compare using eq (==)
Ce titre, c'est l'objet de ce sur quoi vont s'orienter les prochains tests.

### 3.2.2 Given a file named "compare_using_eq.rb" with:
Cette partie est vachement intéressante. Elle explique que si tu copies le contenu qui suit dans un fichier nommé `compare_using_eq.rb` (ou comme tu veux), et bien tu pourras l'exécuter sans erreur.

### 3.2.3 Le block qui suit
Dans ce bloc tu y trouveras (si tu n'as pas changé la version d'RSpec dans le menu déroulant) les lignes suivantes :
```ruby
RSpec.describe "a string" do
  it "is equal to another string of the same value" do
    expect("this string").to eq("this string")
  end

  it "is not equal to another string of a different value" do
    expect("this string").not_to eq("a different string")
  end
end

RSpec.describe "an integer" do
  it "is equal to a float of the same value" do
    expect(5).to eq(5.0)
  end
end
```
En gros ce sont tous les cas de figure possibles.
Les descriptions des `RSpec.describe` te donnent le contexte, celui des `it` des précisions quant au use case.

A toi ensuite de lire les lignes expect et de les comprendre.

### 3.2.4 Le mot de la fin
Juste en dessous de ce bloc tu trouveras pour finir
```markdown
When I run rspec `compare_using_eq.rb`
Then the output should contain `"3 examples, 0 failures"`
```

En gros cela te dit le résultat auquel t'attendre si tu lances la commande RSpec sur le bloc qui précède.

Faites-y bien attention. Si tu n'as pas le même résultat c'est que c'est un signe d'une mauvaise config, ou d'un parasite quelque part dans votre repo.

De temps en temps, la documentation indique par exemple que certains tests fails. C'est dans ce cas en général abordé dans les descriptions du bloc, et cela donne une indication sur ce qu'il faut comprendre du fonctionnement.

## 4. Points importants à retenir
La doc est divisée en plusieurs parties distinctes correspondant aux diverses gem qu'il est possible d'installer séparément.
Le rangement d'un point particulier (les matchers par exemple) dans une des parties de la doc n'est pas forcément intuitif. Une recherche Google ou l'expérience t'en dira plus.

La doc explique RSpec en utilisant RSpec : d'où son caractère franchement rebutant de prime abord.