# RSpec et les inputs / outputs de Terminal
Une petite ressource vite fait histoire que tu cherches pas des plombes comment tester les interactions avec le terminal.

## 1. Introduction
Le projet du jour, c'est pas d'la tarte au citron.
C'est pourquoi il te faut les bonnes armes. Et pour éviter que tu fouilles pendant des heures dans l'armurerie je t'offre cette mini ressource.

## 2. Les mocks
Si tu as vu Hunger Games en VO tu dois te souvenir du [Mockingjay](https://fr.wikipedia.org/wiki/Moqueur#:~:text=Le%20geai%20moqueur%20(Mockingjay%20en,de%20celui%20de%20sa%20famille.), cet oiseau qui imite les sifflements qu'il entend.

Et bien les mocks dans RSpec c'est pareil.

Un mock est un objet qui va se comporter "comme si" c'était [Insert ici ce dont tu as besoin], mais qui n'est pas [Insert ici ce dont tu as besoin].

L'avantage c'est que tu pourras contrôler avec finesse ce qu'il se passe puisque c'est toi qui décides.

### 2.1. Dans la nature
Si tu te retrouve à devoir fouiller les internets, sache que cela peut porter beaucoup de noms différents suivant le framework, mais aussi pour un framework donné (comme RSpec) suivant l'usage.
Pour RSpec voici tous les mots clefs possibles pour cette notion:
- double
  - Pure double
  - Partial double
  - Verifying double
  - Stubbed constant
- stub
- mock
- Null Object
- Spy

En gros le concept reste le même, et je ne rentrerai pas dans les subtilités franchement complexes.

Ici je vais juste t'apprendre à t'en servir dans un cas ultra particulier.

### 2.2. Spy
Dans le cas qui nous intéresse, on va utiliser un spy en fait.
Un spy est un espion. On prend donc un objet qui existe, on lui place un trackeur dessus. Et dès que cet objet reçoit une méthode on l'intercepte et on remplace la valeure de réponse par la notre.

T'as rien pigé ?

Ouais, c'est normal ma phrase c'est un peu le foutoir. Je vais clarifier ça avec un exemple concrêt : l'entrée utilisateur dans le terminal.

Imagine que tu as ce code à tester :

```ruby
class TrucATester
  def terminal_input
    input = $stdin.gets.chomp
    puts "YOUHOU #{input}"
  end
end
```

Ce qu'on veut : c'est tester que pour une entrée "X" par l'utilisateur dans le terminal, la sortie dans le terminal c'est "YOUHOU X"....

Ok

Le matcher qui va bien pour cet exemple c'est : `output('YOUHOU X').to_stdout`.

Super.

Mais du coup on doit bien à un moment "simuler" un utilisateur rentrant la chaine de caractère 'X' quelque part pour pouvoir écrire le fameux :
```ruby
expect(terminal_input).to output('YOUHOU X').to_stdout
```

Et bien pour ça on va utiliser la ligne :
```ruby
allow($stdin).to receive(:gets).and_return('X')
```

Ce que fait cette ligne c'est de dire : on va regarder `$stdin` (basiquement tout ce qui rentre dans le terminal par le clavier), et dès que `$stdin` reçoit la méthode `gets` (qui prend en compte les frappes clavier de l'utilisateur), alors on l'intercèpte, et, ON SE FOUT de l'utilisateur parce qu'on retournera `'X'` à la place.

Ton test s'écrit donc comme ça :

```ruby
it 'tests a terminal input' do
  allow($stdin).to receive(:gets).and_return('X')
  expect(terminal_input).to output('YOUHOU X').to_stdout
end
```

### 2.1. Attention au piège
Avec ce que je viens de te montrer, si jamais la méthode en question n'utilise pas la méthode `gets`, mais que tu utilise quand même le spy, genre comme ça :

```ruby
class TrucATester
  def terminal_input
    puts "YOUHOU X"
  end
end

RSpec.describe 'chéri ? Ca va planter' do
  it 'does not work' do
    allow($stdin).to receive(:gets).and_return('X')
    expect(terminal_input).to eq('YOUHOU X')
  end
end
```

RSpec t'insultera en te disant qu'il s'attendait à ce que `$stdin` recoive la méthode `gets` mais finalement ce ne fut pas le cas....

Et oui.

En gros RSpec considère que tu test aussi ce qu'il se passe sur `$stdin`.

## 3. Points importants à retenir
Pour tester une sortie dans le terminal on peut utiliser le matcher `output('quelque chose').to_stdout`

Pour simuler une entrée dans le terminal, on créé juste avant le `expect`, un spy qui va remplacer l'interaction avec le terminal : `allow($stdin).to receive(:gets).and_return('X')`

## 4. Pour aller plus loin
Si vraiment tu as l'âme masochiste, je ne te jugerai pas, et tu peux te lancer dans [cette doc](https://relishapp.com/rspec/rspec-mocks/docs/basics/test-doubles).

Sinon tu attends vendredi et tu auras une autre ressource plus friendly.
