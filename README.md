<h1 align="center">wxFirebaseSDK</h1>

<div align="center">

[![WinDev](https://img.shields.io/badge/WinDev-25+-blue.svg?style=for-the-badge)](https://pcsoft.fr/)
[![License](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)](LICENSE)
[![Firebase](https://img.shields.io/badge/Firebase-REST_API-orange.svg?style=for-the-badge)](https://firebase.google.com/docs/reference/rest)

</div>

<p align="center">
    Un composant destiné aux développeurs utilisant les produits 
    <a href="https://pcsoft.fr/" target="_blank">PCSoft</a>, facilitant l'intégration des services Firebase dans vos projets WinDev, WebDev, et WinDev Mobile.
</p>

<p align="center">
    <strong>Authentification</strong> | <strong>Firestore</strong> | <strong>Storage</strong>
</p>

<p align="center">
    <a href="https://github.com/pcsoft-toolkit/wxFirebaseSDK/tree/main/Ressources/Exemple"><strong>Télécharger la démo</strong></a>
</p>

## Prérequis

- **WinDev version 25 ou supérieure** ([lien de téléchargement officiel](https://pcsoft.fr/st/telec/index.html))
- **Un projet Firebase** : vous pouvez en créer un nouveau via la [console Firebase](https://console.firebase.google.com/u/0/) si vous n'en avez pas déjà un.

## Installation

1. Téléchargez la dernière version de `wxFirebase` dans la [page de Releases de ce projet](https://github.com/pcsoft-toolkit/wxFirebaseSDK/releases).
2. Ajoutez le composant à votre projet WinDev en suivant la [documentation officielle](https://doc.pcsoft.fr/?2014006).

## Configuration Firebase

Pour que le composant puisse accéder à un projet Firebase, vous devez au préalable le configurer en récupérant la configuration Web de votre projet dans la console Firebase.

### Étape 1 : Créer un projet Firebase

- Allez sur [console.firebase.google.com](https://console.firebase.google.com).
- Cliquez sur `Créer un projet`
- Donnez un nom à votre projet (ex : `wxFirebaseWrapper`)
- **Google Analytics** : Vous pouvez le désactiver
- Cliquez sur `Créer un projet` et attendez la finalisation

### Étape 2 : Ajouter une application Web
- Dans votre console de projet, cliquez sur l'icône **Web** `</>`
- **Pseudo de l'application** : Donnez un nom descriptif (ex : `wxFirebaseWrapper`)
- Cliquez sur **Enregistrer l'application**
> [!CAUTION]
>  NE COCHEZ PAS `Configurer Firebase Hosting`

### Étape 3 : Récupérer la configuration
Firebase vous affiche maintenant un code JavaScript ressemblant à ceci :
```js
const firebaseConfig = {
  apiKey: "AIzaSyBxxxxxxxxxxxxxxxxxxxxxxxx",
  authDomain: "mon-projet.firebaseapp.com", 
  projectId: "mon-projet",
  storageBucket: "mon-projet.appspot.com",
  messagingSenderId: "123456789012",
  appId: "1:123456789012:web:abcdef123456789"
};
```
> [!NOTE]
> Pour `wxFirebaseSDK`, copiez uniquement ces 3 valeurs (`apiKey`, `projectId`, `storageBucket`)

## Configuration du projet
### Étape 1 : Configuration du fichier INI
Créez un fichier `firebaseConfig.INI` dans le répertoire de votre exécutable avec la structure suivante :
```ini
[FIREBASECONFIG]
API_KEY=votre_api_key_web_ici
PROJET_ID=votre_projet_id_ici
STORAGE_BUCKET=votre_storage_bucket_ici
```
#### Paramètres :
| Clé | Description | Type |
| --- | --- | --- |
| `API_KEY` | La clé API Web de votre projet Firebase | `chaîne`
| `PROJET_ID`  | L'identifiant du projet Firebase | `chaîne`
| `STORAGE_BUCKET` | L'URL du bucket de stockage du projet Firebase | `chaîne`

### Étape 2 : Initialisation dans votre code
#### Initialisation standard
```WLangage
// Initialisation avec configuration par défaut
gclConfigResponse est CConfigResponse = CFirebaseApp.initializeApp()
```
#### Initialisation avec chemin personnalisé
```WLangage
// Initialisation avec un chemin personnalisé vers le fichier `INI`
sCheminConfig est chaîne = "C:\MonApp\config\firebase-prod.ini"
gclConfigResponse est CConfigResponse = CFirebaseApp.initializeApp(sCheminConfig)
```
> [!NOTE]
> Si aucun chemin n’est précisé, `wxFirebaseSDK` recherche par défaut le fichier `firebaseConfig.ini` dans le répertoire de l’exécutable.

#### Gestion de la réponse
`CConfigResponse` est une classe de réponse qui hérite de **CFirebaseResponse**.
Elle est retournée par la méthode `CFirebaseApp.initializeApp()` et permet de savoir si l’initialisation a réussi ou non, ainsi que de récupérer l’instance Firebase prête à l’emploi.

| Méthode | Description |
| --- | --- |
| `hasError()` | Retourne `Vrai` si une erreur est survenue lors de l’initialisation |
| `getMessage()`  | Retourne le message d’erreur |
| `getInstance()` | Retourne l’instance de `CFirebase` initialisée |

##### Exemple d’utilisation
```WLangage
gclConfigResponse est CConfigResponse = CFirebaseApp.initializeApp()

SI gclConfigResponse.hasError() ALORS
	Erreur(gclConfigResponse.getMessage())
    RETOUR
FIN

gclFirebase est CFirebase = gclConfigResponse.getInstance()
```

## I - Authentification
L’authentification permet aux utilisateurs de s'authentifier via les API REST de Firebase. Cette fonctionnalité peut inclure la connexion par email et mot de passe, l'inscription de nouveaux utilisateurs.

### Initialisation du service d'authentification
```WLangage
gclAuth	est CAuth = gclFirebase.Auth()
```
#### Créer un utilisateur
```WLangage
stUserInfo est CAuth.STAuthPayload

stUserInfo.sEmail		  = "wx@firebase.com"
stUserInfo.sPassword	  = "test1234"
stUserInfo.sDisplayName	  = "wxFirebase"
stUserInfo.sPhoneNumber	  = "+2250000000000"
stUserInfo.sPhotoURL	  = "https://lorempicture.point-sys.com/400/300/"
stUserInfo.bEmailVerified = Faux

gclAuthReponse est CAuthReponse = gclAuth.signUpWithEmailPassword(stUserInfo)
```
##### Paramètres :
| Clé | Description | Type |
| --- | --- | --- |
| `sEmail` | L'adresse e-mail de l'utilisateur | `chaîne`
| `sPassword`  | Le mot de passe de l'utilisateur | `chaîne`
| `sDisplayName` | Le nom d'affichage de l'utilisateur | `chaîne`
| `sPhoneNumber` | Le numéro de téléphone de l'utilisateur | `chaîne`
| `sPhotoURL` | L'URL de la photo de profil de l'utilisateur | `chaîne`
| `bEmailVerified` | Indique si l'adresse e-mail de l'utilisateur doit être vérifiée | `booléen`

#### Connexion anonyme
Cette méthode créera un nouvel utilisateur dans la base de données du service d'authentification Firebase à chaque fois qu'elle est invoquée
```WLangage
gclAuthReponse est CAuthReponse = gclAuth.signInAnonymously()
```
#### Connexion par e-mail et mot de passe
```WLangage
gclAuthReponse est CAuthReponse = gclAuth.signInWithEmailAndPassword("wx@firebase.com", "test1234")
```
##### Paramètres :
| Clé | Description | Type |
| --- | --- | --- |
| `sEmail` | L'adresse e-mail de l'utilisateur | `chaîne`
| `sMdp`  | Le mot de passe de l'utilisateur | `chaîne`

### Demande de réinitialisation de mot de passe
```WLangage
gclAuthReponse est CAuthReponse = gclAuth.sendPasswordResetWithEmail("wx@firebase.com")
```
##### Paramètres :
| Clé | Description | Type |
| --- | --- | --- |
| `sEmail` | L'adresse e-mail de l'utilisateur | `chaîne`

#### Supprimer un utilisateur
```WLangage
gclAuthReponse est CAuthReponse = gclAuth.deleteAccount()
```
#### Connexion via providers
Les `Providers` sont des fournisseurs d'authentification autres que Firebase, par exemple Facebook, Github, Google ou Twitter. Vous pouvez trouver les fournisseurs d'authentification actuellement pris en charge par `Firebase` dans la [documentation officielle de Firebase](https://firebase.google.com/docs/projects/provisioning/configure-oauth?hl=fr#add-idp).
A l'heure actuelle le composant prends en compte les fournisseurs suivants : (Facebook, Github, Google)

##### 1. Connexion via Facebook
```WLangage
stOptionProvider est STProviderOauthOptions

stOptionProvider.sClientID			=  CONST_CLIENT_ID
stOptionProvider.sClientSecret		=  CONST_CLIENT_SECRET
stOptionProvider.sScope				= "email"
stOptionProvider.sURLRedirection	= "http://localhost:5000/auth/facebook/callback"

gclProvider	est CFacebookProvider(stOptionProvider)

gclAuthReponse est CAuthReponse = gclAuth.signInWithProvider(gclProvider)
```
###### Paramètres :
| Clé | Description | Type |
| --- | --- | --- |
| `sClientID` | L'identifiant client de l'application Facebook | `chaîne`
| `sClientSecret`  | Le secret client de l'application Facebook| `chaîne`
| `sScope` | La portée des autorisations demandées | `chaîne`
| `sURLRedirection` | L'URL de redirection après l'authentification | `chaîne`
| `gclProvider` | Une instance de `CGoogleProvider` initialisée avec les options d'authentification | `STProviderOauthOptions`

##### 2. Connexion via Github
```WLangage
stOptionProvider est STProviderOauthOptions

stOptionProvider.sClientID			=  CONST_CLIENT_ID
stOptionProvider.sClientSecret		=  CONST_CLIENT_SECRET
stOptionProvider.sScope				= "user"
stOptionProvider.sURLRedirection	= "http://localhost:5000/auth/github/callback"

gclProvider est CGitHubProvider(stOptionProvider)

gclAuthReponse est CAuthReponse = gclAuth.signInWithProvider(gclProvider)
```
###### Paramètres :
| Clé | Description | Type |
| --- | --- | --- |
| `sClientID` | L'identifiant client de l'application Github | `chaîne`
| `sClientSecret`  | Le secret client de l'application Github | `chaîne`
| `sScope` | La portée des autorisations demandées | `chaîne`
| `sURLRedirection` | L'URL de redirection après l'authentification | `chaîne`

##### 3. Connexion via Google
```WLangage
stOptionProvider est STProviderOauthOptions

stOptionProvider.sClientID			= CONST_CLIENT_ID
stOptionProvider.sClientSecret		= CONST_CLIENT_SECRET
stOptionProvider.sScope				= "email"
stOptionProvider.sURLRedirection	= "http://localhost:5000/auth/github/callback"

gclProvider est CGoogleProvider(stOptionProvider)

gclAuthReponse est CAuthReponse = gclAuth.signInWithProvider(gclProvider)
```
###### Paramètres :
| Clé | Description | Type |
| --- | --- | --- |
| `sClientID` | L'identifiant client de l'application Google | `chaîne`
| `sClientSecret`  | Le secret client de l'application Google | `chaîne`
| `sScope` | La portée des autorisations demandées | `chaîne`
| `sURLRedirection` | L'URL de redirection après l'authentification | `chaîne`

#### Gestion de la réponse
Chacune des méthodes du service d'authentification documentées ci-dessus renverra une instance de `CAuthReponse` avec les accesseurs suivants :

| Méthode | Description |
| --- | --- |
| `hasError()` | Retourne `Vrai` si erreur |
| `getMessage()`  | Retourne le message d’erreur |
| `getUser()` | Données de l’utilisateur connecté |

##### Exemple d'utilisation de CAuthReponse
```WLangage
gclAuthReponse est CAuthReponse = gclAuth.signInWithEmailAndPassword("wx@firebase.com", "test1234")

SI gclAuthReponse.hasError() ALORS
	Info(gclAuthReponse.getMessage())
SINON
	Info(gclAuthReponse.getUser())
FIN
```

## Contributions

Les contributions sont les bienvenues ! Pour signaler un bug ou proposer des fonctionnalités, veuillez soumettre une issue ou une pull request. [Plus sur comment contributer](./CONTRIBUTING.md).

## Contributeurs

<table>
  <tbody>
    <tr>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/fabnguess"><img src="https://avatars.githubusercontent.com/u/72697416?v=4?s=100" width="100px;" alt="Kouadio Fabrice Nguessan"/><br /><sub><b>Kouadio Fabrice Nguessan</b></sub></a><br /><a href="https://github.com/fabnguess/wxFirebaseSDK/commits?author=fabnguess" title="Code">💻</a> <a href="https://github.com/fabnguess/wxFirebaseSDK/commits?author=fabnguess" title="Documentation">📖</a> </td>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/mrani24"><img src="https://avatars.githubusercontent.com/u/163332562?v=4" width="100px;" alt="Madara"/><br /><sub><b>Madara</b></sub></a><br /><a href="https://github.com/fabnguess/wxFirebaseSDK/commits?author=mrani24" title="Code">💻</a> </td>
    </tr>
  
  </tbody>
</table>

## License
MIT
