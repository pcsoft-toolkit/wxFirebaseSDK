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

- **Windev version 25 ou supérieure** ([lien de téléchargeent officiel](https://pcsoft.fr/st/telec/index.html))
- **Un projet Firebase** : vous pouvez en créer un nouveau via la [console Firebase](https://console.firebase.google.com/u/0/) si vous n'en avez pas déjà un.

## Installation

1. Téléchargez la dernière version de `wxFirebase` dans la [page de Releases de ce projet](https://github.com/fabnguess/wxFirebaseSDK/releases).
2. Ajoutez le composant à votre projet WINDEV® en suivant la [documentation officielle](https://doc.pcsoft.fr/?2014006).

## Configuration projet

Pour que le composant puisse accéder à un projet Firebase, vous devez au préalable le configurer en générant un fichier de clé privée dans la console Firebase.

### Étapes pour générer le fichier de clé privée :

1. Ouvrez [cette page de la console Firebase](https://console.firebase.google.com/project/_/settings/serviceaccounts/adminsdk) et sélectionnez le projet pour lequel vous souhaitez générer un fichier de clé privée.
2. Cliquez sur **Générer une nouvelle clé privée**, puis confirmez en cliquant sur **Générer la clé**.

Une fois le fichier de clé privée obtenu, initialisez la configuration Firebase dans votre projet avec le code suivant :

```WLangage
sAPIKey			est une chaîne		= INILit("FIREBASECONFIG", "API_KEY"	   , "", fRepExe() + fSep() + "firebaseConfig.INI")
sStorageBucket	est une chaîne	= INILit("FIREBASECONFIG", "STORAGE_BUCKET", "", fRepExe() + fSep() + "firebaseConfig.INI")
sPojetID		est une chaîne		= INILit("FIREBASECONFIG", "PROJET_ID"	   , "", fRepExe() + fSep() + "firebaseConfig.INI")

// Approche 1 : Initialisation par tableau
gstFirebaseConfig est STFirebaseConfig	= [sAPIKey, sPojetID, sStorageBucket]

// Approche 2 : Initialisation explicite par membres 
gstFirebaseConfig.sApiKey        = sAPIKey
gstFirebaseConfig.sProjetId      = sPojetID
gstFirebaseConfig.sStorageBucket = sStorageBucket

// Création de l'instance Firebase
gclInstanceFirebase est CFireBase(gstFirebaseConfig)
```
> [!NOTE]
>  Dans cette documentation, nous partirons du principe que tous vos paramètres de configuration sont stockés dans un fichier `.INI`.

#### Paramètres :
- `sApiKey` : La clé API Web de votre projet Firebase (`chaine`).
- `sProjetId` : L'dentifiant du projet Firebase (`chaine`).
- `sStorageBucket` : L'URL du bucket de stockage du projet Firebase (`chaine`).

## I - Authentification

L’authentification permet aux utilisateurs de s'authentifier via les API REST de Firebase. Cette fonctionnalité peut inclure la connexion par email et mot de passe, l'inscription de nouveaux utilisateurs.

### Initialisation du service d'authentification
```WLangage
	gclAuth	est CAuth = gclInstanceFirebase.Auth()
```

Chacune des méthodes du service d'authentification documentées ci-dessous renverra une instance de `CAuthReponse` avec les accesseurs suivants :

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

gclAuthReponse  = gclAuth.CréerUtilisateur(gstInfoUtilisateur)
```
#### Paramètres :
- `sEmail` : L'adresse e-mail de l'utilisateur (`chaine`).
- `sMdp` : Le mot de passe de l'utilisateur (`chaine`).
- `sNomAffichage` : Le nom d'affichage de l'utilisateur (`chaine`).
- `sNuméroTéléphone` : Le numéro de téléphone de l'utilisateur (`chaine`).
- `sPhotoURL` : L'URL de la photo de profil de l'utilisateur (`chaine`).
- `bVerifieEmail` : Indique si l'adresse e-mail de l'utilisateur doit être vérifiée (`booléen`).

### Connexion anonyme
Cette méthode créera un nouvel utilisateur dans la base de données du service d'authentification Firebase à chaque fois qu'elle est invoquée
```WLangage
gclAuthReponse  = gclAuth.ConnexionAnonyme()
```
### Connexion par e-mail et mot de passe
```WLangage
gclAuthReponse  = gclAuth.SeConnecter("wx@firebase.com", "test1234")
```
#### Paramètres :
- `sEmail` : L'adresse e-mail de l'utilisateur (`chaine`).
- `sMdp` : Le mot de passe de l'utilisateur (`chaine`).

### Demande de réinitialisation de mot de passe
```WLangage
gclAuthReponse  = gclAuth.RéinitialiserMotDePasse("wx@firebase.com")
```
#### Paramètres :
- `sEmail` : L'adresse e-mail de l'utilisateur (`chaine`).

### Supprimer un utilisateur
```WLangage
gclAuthReponse  = gclAuth.SupprimerUtilisateur()
```
### Connexion via providers
Les `Providers` sont des fournisseurs d'authentification autres que Firebase, par exemple Facebook, Github, Google ou Twitter. Vous pouvez trouver les fournisseurs d'authentification actuellement pris en charge par `Firebase` dans la [documentation officielle de Firebase](https://firebase.google.com/docs/projects/provisioning/configure-oauth?hl=fr#add-idp).
A l'heure actuelle le composant prends en compte les fournisseurs suivants : 

- Facebook
- Github
- Google

#### 1. Connexion via Facebook
```WLangage
stOptionProvider est STProviderOauthOptions

stOptionProvider.sClientID		=  CONST_CLIENT_ID
stOptionProvider.sClientSecret	=  CONST_CLIENT_SECRET
stOptionProvider.sScope			= "email"
stOptionProvider.sURLRedirection= "http://localhost:5000/auth/facebook/callback"

gclProvider est CFacebookProvider(stOptionProvider)

gclAuthReponse  est CAuthReponse = gclAuth.SeConnecterProvider(gclProvider)
```
##### Paramètres :
- `sClientID` : L'identifiant client de l'application Facebook (`chaine`).
- `sClientSecret` : Le secret client de l'application Facebook (`chaine`).
- `sScope` : La portée des autorisations demandées (`chaine`).
- `sURLRedirection` : L'URL de redirection après l'authentification (`chaine`).
- `gclProvider` : Une instance de `CGoogleProvider` initialisée avec les options d'authentification (`STProviderOauthOptions`).

#### 2. Connexion via Github
```WLangage
stOptionProvider est STProviderOauthOptions

stOptionProvider.sClientID		=  CONST_CLIENT_ID
stOptionProvider.sClientSecret	=  CONST_CLIENT_SECRET
stOptionProvider.sScope			= "user"
stOptionProvider.sURLRedirection= "http://localhost:5000/auth/github/callback"

gclProvider est CGithubProvider(stOptionProvider)

gclAuthReponse  est CAuthReponse = gclAuth.SeConnecterProvider(gclProvider)
```
##### Paramètres :
- `sClientID` : L'identifiant client de l'application Github (`chaine`).
- `sClientSecret` : Le secret client de l'application Github (`chaine`).
- `sScope` : La portée des autorisations demandées (`chaine`).
- `sURLRedirection` : L'URL de redirection après l'authentification (`chaine`).

#### 3. Connexion via Google
```WLangage
stOptionProvider est STProviderOauthOptions

stOptionProvider.sClientID		=  CONST_CLIENT_ID
stOptionProvider.sClientSecret	=  CONST_CLIENT_SECRET
stOptionProvider.sScope			= "email"
stOptionProvider.sURLRedirection= "http://localhost:5000/auth/github/callback"

gclProvider est CGoogleProvider(stOptionProvider)

gclAuthReponse  est CAuthReponse = gclAuth.SeConnecterProvider(gclProvider)
```
##### Paramètres :
- `sClientID` : L'identifiant client de l'application Google (`chaine`).
- `sClientSecret` : Le secret client de l'application Google (`chaine`).
- `sScope` : La portée des autorisations demandées (`chaine`).
- `sURLRedirection` : L'URL de redirection après l'authentification (`chaine`).

### Exemple d'utilisation de CAuthReponse
```WLangage
gclAuthReponse est CAuthReponse = gclAuth.SeConnecter("wx@firebase.com", "test1234")

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

Le **service Firebase Storage** permet de stocker et de récupérer des fichiers de manière sécurisée et évolutive. Ce service est idéal pour gérer des fichiers tels que des images, des vidéos, des documents, et bien plus encore.

### Initialisation du service de stockage
```WLangage
	gclStorage	est CStorage = gclInstanceFirebase.Storage()
```

### Téléchargement d'un fichier
```WLangage
sCheminFichier est chaine = "C:/chemin_vers_le_fichier/image.png"

gclStorageReponse est CStorageReponse  = gclStorage.TéléchargerFichier(sCheminFichier)

// Gestion de CStorageReponse 
 SELON gclStorageReponse.errType
	CAS errAucune
		Info(gclStorageReponse.RécupérerUrlFichier)		
	CAS errFirebase
		SI gclAuthReponse.errAccèsRefusé ALORS Info("L'accès au fichier a été refusé en raison des règles de sécurité.")
			
	CAS errWindev
		Info(gclStorageReponse.errWindevMessage)
FIN
```
#### Paramètres :
- `sCheminFichier` : Le chemin du fichier à télécharger (`chaine`).

### Activation de la gestion des rôles
Pour garantir la sécurité de vos fichiers stockés dans Firebase Storage, il est essentiel d'activer et de configurer les règles de sécurité. Voici un exemple de règles de sécurité pour Firebase Storage :
```WLangage
service firebase.storage {
  match /b/{bucket}/o {
    match /{allPaths=**} {
        allow read, write: if request.auth !=null;
    }
  }
}
```
Ces règles permettent uniquement aux utilisateurs authentifiés de lire et d'écrire des fichiers dans Firebase Storage. Vous pouvez ajuster ces règles en fonction de vos besoins spécifiques.

## Contributions

Les contributions sont les bienvenues ! Pour signaler un bug ou proposer des fonctionnalités, veuillez soumettre une issue ou une pull request. [Plus sur comment contributer](./CONTRIBUTING.md).

## Contributeurs

<table>
  <tbody>
    <tr>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/fabnguess"><img src="https://avatars.githubusercontent.com/u/72697416?v=4?s=100" width="100px;" alt="Kouadio Fabrice Nguessan"/><br /><sub><b>Kouadio Fabrice Nguessan</b></sub></a><br /><a href="https://github.com/NodeSecure/i18n/commits?author=fabnguess" title="Code">💻</a> <a href="https://github.com/fabnguess/wxFirebaseSDK/commits?author=fabnguess" title="Documentation">📖</a> </td>
    </tr>
  
  </tbody>
</table>

## License
MIT