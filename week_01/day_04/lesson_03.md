# To be signed in... or not to be
On va voir ici brièvement comment "simuler" le "sign_in" d'un utilisateur.

## 1. Ya pas un helper ?
Comme tu t'en doutes c'est le point faible de la gem rspec-rails. Aucun outil de sign_in par défaut n'est présent dans cette gem.

On va donc voir comment en faire un... A la main... et oui.

## 2. Retour aux bases
Reprenons la base de ce qu'on fait. On teste le comportement de l'application.
En soi : pouvoir se logger est une fonctionnalité, un comportement.

Mais en somme, cela ne va pas plus loin qu'un test controller :
- On vérifie qu'avec les bons paramètres le controller renvoie une 200.
- On vérifie qu'à l'issue, `current_user`, par exemple, est bien défini.

Mais comment on fait maintenant pour tous les autres controllers qui requièrent un User Sign_in ?

## 3. Inclure un Sign_in
## 3.1 La Version DIY
En fait dans nos mécanismes d'authentifications, si on regarde, on fait généralement un `if current_user` pour savoir si un user est connecté ou non.
Et en fonction, on renvoie une 401, ou alors on passe dans le controller.

Et bien on va brancher ça.

Reprends ton scaffold de tout à l'heure, et créé le fichier `/spec/support/sign_in_helper.rb`.

Dedans on va définir un module dédié à cette opération :

```ruby
module SignInHelper

end

```

Dans ce module on va déjà créer la méthode qui permet de définir le `current_user` en rajoutant ces quelques lignes :

```ruby
module SignInHelper
  def current_user(id = nil)
    @current_user ||= User.find_by(id: id) if id
  end
end
```

Voilà, donc si jamais tu include ce module dans un fichier spec, tu auras accès à la méthode `current_user` qui renverra le User correspondant à l'`id` si current_user n'est pas déjà défini.

Ensuite on va juste définir une méthode de `sign_in` factice et on sera bon :

```ruby
module SignInHelper
  def current_user(id = nil)
    @current_user ||= User.find_by(id: id) if id
  end

  def sign_in(user_id = nil)
    current_user(user_id)
  end
end
```

Maintenant, dans tes spec, tu n'as plus qu'à `include SignInHelper` pour avoir accès à une méthode `sign_in` qui mettra en place le `current_user`.


**Astuce Démoniaque :**
Tu ne vas quand même pas include ce module dans tous tes fichiers de specs de `type: :request` à la main non ?
Non.
A la place tu vas simplement ajouter ces quelques lignes à la fin de ton fichier `rails_helper.rb` :
```ruby
config.include SignInHelper, type: :request
```

Voilà, comme ça ton module sera inclus systématiquement dès que tu mettras la metadata `type: :request` à ton fichier de specs.

La classe non ?

En tout cas crois-moi : ça pourra te faire briller en test technique cette affaire.

### 3.2. La version Devise
Et oui, tu t'en doutais, ce n'est pas vers RSpec qu'il fallait se tourner, mais bien vers Devise. Car ce sont eux qui ont pensé à nous offrir les méthodes de `sign_in`.

Et bien ce n'est pas plus difficile qu'un :

```ruby
config.include Devise::Test::IntegrationHelpers, type: :request
```

dans ton `rails_helper.rb`.

Voilà c'est fini, merci qui ?
Merci Devise.

Tu as désormais accès à la méthode `sign_in(user)` de devise. Lorsque cette méthode est appelée, Devise va automatiquement connecté le User comme s'il était passé par l'interface graphique. Attention, il faut bien penser à l'utiliser avant chaque example qui en a besoin, car Devise fera la déconnexion automatiquement après chaque example.

Pratique pour ne pas oublier qu'un User est déjà connecté !

## 4. Points importants à retenir
Pour tester le login d'un user, c'est un test d'acceptance sur ton controller ad hoc qu'il faut faire.
Pour simuler le login d'un user pour d'autres tests il te faut :
- Soit un mécanisme d'authentification en DIY dont tu as un exemple ici.
- Soit inclure les helpers de Devise si tu as Devise de branché.
