# Les matchers. Non, ça ne se boit pas.

## 1. Introduction
Les matchers représentent le dernier chaînon qu'il te manque pour te lancer dans le projet du jour. Une fois que tu auras vu ça : à toi la gloire.

## 2.Historique et contexte
Dans le décryptage de RSpec, on a vu beaucoup de nouveaux mots.
Pourtant si tu as l'œil affûté, et bien déjà, fais attention de ne pas te couper, mais surtout tu auras remarqué que je n'ai pas abordé la partie la plus croustillante d'un fichier RSpec : le test en lui-même.

Tu sais cette fameuse partie : `expect(truc).to machin(bidule)`.

Et bien on aborde cela dès maintenant.

## 3. Décryptage d'un test.
Si dans ton parcours précédent tu as essayé malgré tout de faire des tests, tu as dû remarquer que c'était très pénible. La raison principale c'est que si tu sais quoi tester (déjà tu fais partie du haut du panier), tu ne sais pas forcément comment l'écrire en RSpec...

Exemple. Tu veux tester que la méthode suivante fonctionne correctement :
```ruby
def ma_methode(argument = nil)
  return 'toto' if argument.nil?
  'tata'
end
```
Bon on voit bien que suivant s'il y a un argument ou pas, la méthode renvoie soit la chaîne `'toto'` soit la chaîne `'tata'`.

Là tu te demandes : "D'accord, mais comment on écrit ça ?".

Et 3h après t'es toujours en PLS sur stackoverflow a essayé de comprendre ce que raconte les gens...

Ne t'inquiète pas. C'est fini ça maintenant. Fini fini.

### 3.1. Décomposer le mouvement.
Alors déjà faut bien comprendre ce qu'on regarde.

Dans un example, il ne doit en général y avoir qu'un seul test. C'est comme ça. Tu peux en mettre plusieurs, mais la convention c'est : un example = un test.

Mais dis-donc Jamy, c'est quoi la différence entre une spec, un test et un example ?
À première vue il n'y a pas de différence. Tous ces termes réfèrent à la même syntaxe de code. Mais chacun de ces trois termes a une connotation différente dans le monde du testing.

Pour un bout de code donné :
- Un test : valide qu'il fonctionne correctement
- Une spec : (diminutif d'une spécificité), décrit le comportement souhaité de ce bout de code.
- Un example : montre de quelle manière ce bout de code est censé être utilisé.

La philosophie de RSpec devient alors évidente : on spécifie les besoins en écrivant des fichiers de specs. Dans chaque fichier il y a des examples.
Mais pas de tests.

Ne te prends pas trop la tête pour le moment. On verra comment utiliser cette philo demain.

Mais pour l'heure ce qu'il faut comprendre, c'est qu'on écrit des specs (fichiers) qui contiennent des examples. En voici un pour l'exemple (désolé j'étais obligé de la faire à un moment celle-là) :
```ruby
it 'returns toto' do
  expect(ma_methode(un_argument)).to eq('toto')
end
```

Décomposons :

#### 3.1.1 l'Example

```ruby
it 'returns toto' # ceci est un example
```
Oui en soi `it` est une méthode. Cette méthode accepte un objet en premier argument (ici le string `'returns toto'`), puis un hash (on verra ça plus tard si tu as assez de bravoure en toi), et enfin un block.

On peut donc écrire indifféremment :
```ruby
it 'returns toto' do
  expect(ma_methode(un_argument)).to eq('toto')
end
```
ou
```ruby
it 'returns toto' { expect(ma_methode(un_argument)).to eq('toto') }
```
C'est pareil.

#### 3.1.2 l'Expectation
La partie
```ruby
expect(ma_methode(un_argument)).to
```
Est-ce qu'on appelle une `expectation` ?
C'est cet objet qui, conjointement avec `.to`, s'occupe de signaler le succès ou l'échec de l'example.
C'est donc cet objet qui te largue un gros message d'erreur dans la tronche, ou une jolie couleur verte pleine d'espoir quand ça marche.
On ne le manipule pas vraiment. Mais il faudra juste réfléchir à ce qu'on lui donne comme argument : ici `ma_methode(un_argument)`. Cet argument, on l'appelle le **subject**.

La partie bizarre c'est ce `.to`. C'est aussi une méthode, qui prend cette fois-ci l'argument `eq('toto')`. Cela fait partie de l'expectation.
Note qu'il existe la méthode `.not_to` qui vérifiera qu'on n'obtient **PAS** quelque chose.
On peut aussi écrire `.to_not` c'est la même chose, mais selon le cas ça fait plus propre.

#### 3.1.3 le Subject
Comme le sujet d'un verbe dans une phrase, le sujet est bien ce dont on parle.
dans :
```ruby
it 'returns toto' do
  expect(ma_methode(un_argument)).to eq('toto')
end
```
Le sujet est donc `ma_methode(un_argument)`. C'est l'argument (le subject) de la méthode (du verbe) `expect().to` (ou *'to expect'* pour les fans de shakespear).

#### 3.1.3 les Matchers
```ruby
eq('toto')
```
Est un matcher. C'est la partie responsable de comparer la valeur `'toto'` avec le contenu du `expect(...).to`. Cette méthode va simplement renvoyer un booléen true/false selon le cas.

Et c'est cette partie qu'il faut connaître correctement. Il en existe de plein de sortes faisant plein de choses. Ils sont composables, chainables et quand on les maîtrise on devient un peu tout puissant.

Tu ne me crois pas ?
Tiens, regarde :

```ruby
staff = [
  { name: 'Guillaume', job: 'developer', favorite_number: 42 },
  { name: 'Féfé', job: 'founder of THP', favorite_number: 3.14},
  { name: 'Josselin', job: 'community manager', favorite_number: 1 }
]

it 'chains matchers like a boss' do
  expect(staff).to include(an_object_having_attributes(favorite_number: an_integer(be_within(0.1).of(Math::PI))))
end
```
Ici les matchers utilisés sont :
- include
- an_object_having_attributes
- an_integer
- be_within

Le `of` ne peut s'utiliser que greffé à un autre matcher. C'est un cas un peu particulier. Il en existe d'autre assez pratique comme `and`, `or` etc...

Le truc génial c'est qu'on peut même les créer si tu ne trouves pas ton bonheur.
Je déconseille de le faire à ton niveau, mais sache que c'est possible.

### 3.2. La cheat sheet de guedin !!
Alors voilà, c'est simple.

Maintenant tu connais toute la conjugaison et la syntaxe de RSpec. Et j'en suis désolé d'avance, j'ai pourtant repoussé ce moment autant que possible. Mais c'est l'instant relou où il va falloir apprendre des trucs...

Les matchers...

Voici une liste non exhaustive, mais qui pourra te sortir de beaucoup de situations :

| Matchers | Alias | Renvoie true si... |
|---|---|---|
| eq(x) | an_object_eq_to(x) | a == x |
| eql(x) | an_object_eql_to(x) | a.eql?(x) |
| equal(x) | be(x) an_object_equal_to(x) | a.equal?(x) |
| be_truthy | a_truthy_value | a != nil && a != false |
| be(true) |  | a == true |
| be_falsey | be_falsy a_falsey_value a_falsy_value | a == nil \|\| a == false |
| be(false) |  | a ==  false |
| be_nil | a_nil_value | a.nil? |
| be_an_instance_of(klass)  ou klass est par exemple Integer ou encore User, Article etc... | be_instance_of(klass) an_instance_of(klass) | a.class == klass |
| be_a_kind_of(klass) | be_a(klass) be_kind_of(klass) a_kind_of(klass) | a.is_a? |
| be OP x ou OP est un opérateur : ==, ===, <, <=, >, >=, =~  Exemple : be > x | a_value OP x    a_value > x | a OP x    a > x |
| be_between(1,10).inclusive | be_between(1,10) a_value_between(1,10).inclusive a_value(1,10) | a >= 1 && a <= 10 |
| be_between(1,10).exclusive | a_value_between(1,10).inclusive | a > 1 && a < 10 |
| be_within(0.1).of(x) | a_value_within(0.1).of(x) | a > (x - 0.1) && a < (x + 0.1) |
| be_within(5).percent.of(x) | a_value_within(5).percent.of(x) | a > (x*0.95) && a < (x*1.05) |
| contain_exactly(2,1,3) | match_array([2,1,3]) a_collection_containing_exactly(2,1,3) | a.sort == [2,1,3].sort |
| start_with(x, y) | a_collection_starting_with(x,y) a_string_starting_with(x, y) | a[0] == x && a[1] == y |
| end_with(x, y) | a_collection_ending_with(x,y) a_string_ending_with(x, y) | a[-1] == x && a[-2] == y |
| include(x, y)  x et y peuvent aussi être une paire clef valeur.   Exemple include(clef: 'valeur') | a_collection_including(x, y) a_string_including(x, y) an_array_including(x, y) a_hash_including(x, y)   | (a.include?(x) && a.include?(y)) \|\| (a.key?(x) && a.key?(y))     a[:clef] == 'valeur' |
| all(x)  Exemple : est-ce que tous les éléments du tableau a sont égaux à 2 ? |  | a.all? do \|element  x.matches?(element) end |
| match(/regex/) | match_regex(/regex/) a_string_matching(/regex/) | a.match(/regex/) |
| have_attributes(w: x, y: z | an_object_having_attributes(w: x, y: z) | a.w == x && a.y == z |
| respond_to(:x, :y)   Exemple, est-ce que ma class Article  répond à la méthode #category ? | a_object_responding_to(:x, :y) | a.respond_to?(:x) && a.respond_to?(:y)   Article.respond_to?(:category) |
| Si la méthode xyz? existe sur un objet même si c'est toi qui l'a créée et qu'elle renvoie un booléen, alors :   be_xyz |     be_a_xyz |     a.xyz? |
| exist | an_object_existing | a.exist? \|\| a.exists? |

Ensuite il y a quelques autres matchers qui ont une particularité. Les "block matchers".
Ils se comparent au block de code de l'expectation, et non à son argument (ou subject).
On doit dans ce cas écrire cette syntaxe.

```ruby
expect { some_code }.to block_matcher(some_other_code) # cette ligne ne fonctionne pas, mais ça montre la syntaxe
```

Je ne vais pas te les faires bouffer de manière brutale ceux-là, mais plutôt te montrer un exemple venant de Rails parce que c'est super pratique :

```ruby
expect { Article.new }.to change { Article, :count }.by(1)

def create_three_articles
  3.times { Article.create() } # on admet que ça crée 3 articles
end

expect { create_three_articles }.to change { Article.count }.from(1).to(4) # on avait 1 article, maintenant on en a 3 de plus

expect { create_three_articles }.to change { Article, :count }.by_at_least(2)
# Article, :count ou Article.count les deux syntaxes sont correctes.

Expect { create_three_articles }.to not_change { User, :count }
expect { create_three_articles }.not_to change { User, :count } # ces deux syntaxes sont rigoureusement équivalentes.
```

Voilà, bien sûr, il y a d'autres matchers basés sur les block, mais on en a déjà bien assez vu pour faire de toi une brute épaisse en RSpec.

## 4. Points importants à retenir
On a décortiqué la dernière partie d'un fichier de spec : l'example.

On a vu que c'était composé d'un bloc contenant :
- une expectation
- un subject
- un, ou plusieurs matchers chainés entre eux.

On a vu que si tu connais bien tes matchers, en fait il t'en suffit en général d'un seul pour briller dans ton équipe de dév !

Et on a également vu que le combo : `expectation(subject) matcher(valeur)` était en fait une simple comparaison sortant un booléen, interprété par l'expectation pour en faire un message d'erreur.

On a également parcouru une liste, certes non exhaustive, mais quand même badass des matchers qui te permettra de te sortir de quasiment toutes les situations (~80% à vue de nez).

## 5. Pour aller plus loin
[Une liste d'alias](https://gist.github.com/JunichiIto/f603d3fbfcf99b914f86), pour toi, qui aime que ton code fasse de belles phrases lisibles.
[Et enfin, la liste exhaustive des matchers](https://relishapp.com/rspec/rspec-expectations/v/3-10/docs/built-in-matchers)
Là ils y sont tous.
Enjoy si jamais tu veux épater les potos à l'apéro mercredi.
Moi en général je fais ça... et puis je finis mon verre tout seul et je paye l'addition.
