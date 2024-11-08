<p align="center"> <img src="Ressources/Images/Logo-wdFirebase.webp" alt="Logo wxFirebase" height="120"/> </p> 

<h1 align="center"> wxFirebaseSDK </h1> 

<p align="center"> 
    Une bibliothèque destinée aux développeurs utilisant les produits 
    <a href="https://pcsoft.fr/" target="_blank">PCSoft</a>, facilitant l'intégration des services Firebase dans vos projets Windev, WebDev, et Windev Mobile. Ce SDK se concentre sur l'interaction via les API REST de Firebase, sans nécessiter l'utilisation du SDK Firebase natif. Les services supportés incluent :
</p>

- 🔒 Authentification
- 📂 Firestore
- 🗄️ Storage

## Prérequis

- **Windev version 25 ou supérieure** ([lien vers PCSoft](https://pcsoft.fr/))
- **Un projet Firebase** : vous pouvez en créer un nouveau via la [console Firebase](https://console.firebase.google.com/u/0/) si vous n'en avez pas déjà un.

## Installation

1. Téléchargez la dernière version de `wxFirebase` dans la [page de Releases de ce projet](https://github.com/fabnguess/wxFirebase/releases).
2. Ajoutez le composant à votre projet WINDEV® en suivant la [documentation officielle](https://doc.pcsoft.fr/?2014006).

## Configuration projet

Pour que le composant puisse accéder à un projet Firebase, vous devez au préalable le configurer en générant un fichier de clé privée dans la console Firebase.

### Étapes pour générer le fichier de clé privée :

1. Ouvrez [cette page de la console Firebase](https://console.firebase.google.com/project/_/settings/serviceaccounts/adminsdk) et sélectionnez le projet pour lequel vous souhaitez générer un fichier de clé privée.
2. Cliquez sur **Générer une nouvelle clé privée**, puis confirmez en cliquant sur **Générer la clé**.

Une fois le fichier de clé privée obtenu, initialisez la configuration Firebase dans votre projet avec le code suivant :

```WLangage
gstFirebaseConfig est STFirebaseConfig = [CONST_API_KEY, CONST_PROJET_ID, CONST_STORAGE_BUCKET]
gclInstanceFirebase est CFireBase(gstFirebaseConfig)
```
> [!NOTE]
> Initialisez la configuration Firebase en remplaçant CONST_API_KEY, CONST_PROJET_ID, et CONST_STORAGE_BUCKET par vos propres identifiants, puis instanciez CFireBase pour commencer à utiliser les services Firebase.

## I - Authentification

L’authentification permet aux utilisateurs de s'authentifier via les API REST de Firebase. Cette fonctionnalité peut inclure la connexion par email et mot de passe, l'inscription de nouveaux utilisateurs.

Chacune des méthodes documentées ci-dessous renverra une instance de `CAuthReponse` avec les accesseurs suivants :

| accesseurs | Type | description |
| --- | :-: | --- |
| gclAuthReponse.errType | ETypeErreur | Determine qu'une erreur est presente ou pas.
| gclAuthReponse.Données  | Buffer | Retourne les informations utilisateur
| gclAuthReponse.errEmailExistant | Booleen | L'adresse e-mail fournie est déjà utilisée par un utilisateur.
| gclAuthReponse.errEmailIntrouvable | Booleen | Il n'existe aucun utilisateur correspondant à cet identifiant. L'utilisateur a peut-être été supprimé.
| gclAuthReponse.errMotDePasseInvalide | Booleen | La valeur fournie pour la propriété utilisateur password n'est pas valide. Il doit s'agir d'une chaîne d'au moins six caractères.
| gclAuthReponse.errUtilisateurDesactivé | Booleen | Le compte utilisateur a été désactivé par un administrateur.
| gclAuthReponse.errConnexionParMotDePasseDesactive | Booleen | La connexion par mot de passe est désactivée pour ce projet.
| gclAuthReponse.errConnexionAnonymeDesactive | Booleen | La connexion anonyme est désactivée pour ce projet.
| gclAuthReponse.errWindevMessage | Chaine | Retourne le message d'erreur wwindev correspondant.
| gclAuthReponse.errTokenInvalide  | Booleen | Identifiant de l'utilisateur ne sont plus valides. L'utilisateur doit se reconnecter.
| gclAuthReponse.errTokenProviderExpire  | Booleen | Identifiant d'authentification fournies sont mal formées ou ont expiré
> [!NOTE]
> `ETypeErreur` est une enumeration comprenant trois valeurs : 
> - errAucune   : Aucune erreur n'as ete detectée.
> - errFirebase : Une erreur `Firebase` à été detectée.
> - errWindev   : Une erreur `Windev` à été detectée.

### Créer un utilisateur
```WLangage
gstInfoUtilisateur est STAuthPayload

gstInfoUtilisateur.sEmail = "wx@firebase.com"
gstInfoUtilisateur.sMdp = "test1234"
gstInfoUtilisateur.sNomAffichage = "wxFirebase"
gstInfoUtilisateur.sNuméroTéléphone = "+2250000000000"
gstInfoUtilisateur.sPhotoURL = "https://lorempicture.point-sys.com/400/300/"
gstInfoUtilisateur.bVerifieEmail = Faux

gclAuthReponse  = gclInstanceFirebase.Auth.CréerUtilisateur(gstInfoUtilisateur)
```
### Connexion anonyme
Cette méthode créera un nouvel utilisateur dans la base de données du service d'authentification Firebase à chaque fois qu'elle est invoquée
```WLangage
gclAuthReponse  = gclInstanceFirebase.Auth.ConnexionAnonyme()
```
### Connexion par e-mail et mot de passe
```WLangage
gclAuthReponse  = gclInstanceFirebase.Auth.SeConnecter("wx@firebase.com", "test1234")
```
### Demande de réinitialisation de mot de passe
```WLangage
gclAuthReponse  = gclInstanceFirebase.Auth.RéinitialiserMotDePasse("wx@firebase.com")
```
### Supprimer un utilisateur
```WLangage
gclAuthReponse  = gclInstanceFirebase.Auth.SupprimerUtilisateur()
```
### Connexion via providers
Les `Providers` sont des fournisseurs d'authentification autres que Firebase, par exemple Facebook, Github, Google ou Twitter. Vous pouvez trouver les fournisseurs d'authentification actuellement pris en charge dans la [documentation officielle de Firebase](https://firebase.google.com/docs/projects/provisioning/configure-oauth?hl=fr#add-idp). A l'heure actuelle le composant prends en compte les fournisseurs suivants : 

- Facebook
- Github
- Google

```WLangage
stOptionProvider est STProviderOauthOptions

stOptionProvider.sClientID					=  CONST_CLIENT_ID
stOptionProvider.sClientSecret				=  CONST_CLIENT_SECRET
stOptionProvider.sScope						= "email"
stOptionProvider.sURLRedirection			= "http://localhost:5000/auth/google/callback"

gclProvider est CGoogleProvider(stOptionProvider)

gclAuthReponse  est CAuthReponse = gclInstanceFirebase.Auth.SeConnecterProvider(gclProvider)
```
### Gestion de la réponse
```WLangage
gclAuthReponse est CAuthReponse = gclInstanceFirebase.Auth.SeConnecter("wx@firebase.com", "test1234")

SELON gclAuthReponse.errType
	CAS errAucune
		
		gbufDonnéesUtilisateur est un Buffer = gclAuthReponse.Données
		
	CAS errFirebase
		SI gclAuthReponse.errEmailExistant ALORS Info("Email Existant")
		SI gclAuthReponse.errEmailIntrouvable ALORS Info("Email Introuvable")
		SI gclAuthReponse.errMotDePasseInvalide ALORS Info("Mot De Passe Invalide")
	
	CAS errWindev
		Info(gclAuthReponse.errWindevMessage)
FIN
```

## II - Firestore



## III - Storage



## Contributions

