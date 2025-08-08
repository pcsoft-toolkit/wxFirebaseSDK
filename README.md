<h1 align="center">wxFirebaseSDK</h1>

<div align="center">

[![WinDev](https://img.shields.io/badge/WinDev-25+-blue.svg)](#)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](#)
[![Firebase](https://img.shields.io/badge/Firebase-REST_API-orange.svg)](#)

</div>

<p align="center">
    Un composant destiné aux développeurs utilisant les produits 
    <a href="https://pcsoft.fr/" target="_blank">PCSoft</a>, facilitant l'intégration des services Firebase dans vos projets Windev, WebDev, et Windev Mobile.
</p>

- Authentification
- Firestore
- Storage

## Prérequis

- **Windev version 25 ou supérieure** ([lien de téléchargeent officiel](https://pcsoft.fr/st/telec/index.html))
- **Un projet Firebase** : vous pouvez en créer un nouveau via la [console Firebase](https://console.firebase.google.com/u/0/) si vous n'en avez pas déjà un.

## Installation

1. Téléchargez la dernière version de `wxFirebase` dans la [page de Releases de ce projet](https://github.com/fabnguess/wxFirebaseSDK/releases).
2. Ajoutez le composant à votre projet WINDEV® en suivant la [documentation officielle](https://doc.pcsoft.fr/?2014006).

## Configuration projet

Pour que le composant puisse accéder à un projet Firebase, vous devez au préalable le configurer en générant un fichier de clé privée dans la console Firebase.

### Étape 1 : Générer une clé privée Firebase

- Ouvrez [cette page de la console Firebase](https://console.firebase.google.com/project/_/settings/serviceaccounts/adminsdk) et sélectionnez le projet pour lequel vous souhaitez générer un fichier de clé privée.
- Cliquez sur **Générer une nouvelle clé privée**, puis confirmez en cliquant sur **Générer la clé**.

### Étape 2 : Configuration du fichier INI
Créez un fichier `firebaseConfig.INI` dans le répertoire de votre exécutable avec la structure suivante :
```ini
[FIREBASECONFIG]
API_KEY=votre_api_key_web_ici
PROJET_ID=votre_projet_id_ici
STORAGE_BUCKET=votre_storage_bucket_ici
```
#### Paramètres :
- `API_KEY` : La clé API Web de votre projet Firebase (`chaine`).
- `PROJET_ID` : L'dentifiant du projet Firebase (`chaine`).
- `STORAGE_BUCKET` : L'URL du bucket de stockage du projet Firebase (`chaine`).

### Étape 3 : Initialisation dans votre code

#### Initialisation standard
```WLangage
// Initialisation avec le fichier de configuration par défaut (`firebaseConfig.INI`)
gclInstanceFirebase est CFireBase = CFirebaseApp.initializeApp()
```
> [!NOTE]
>  Si aucun chemin n'est spécifié, le composant recherche automatiquement le fichier `firebaseConfig.INI` dans le répertoire de l'exécutable.

#### Initialisation avec fichier personnalisé
```WLangage
// Initialisation avec un fichier de configuration personnalisé
sCheminConfig est chaîne = "C:\MonApp\config\firebase-prod.ini"
gclInstanceFirebase est CFireBase = CFirebaseApp.initializeApp(sCheminConfig)
```

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

gstInfoUtilisateur.sEmail           = "wx@firebase.com"
gstInfoUtilisateur.sMdp             = "test1234"
gstInfoUtilisateur.sNomAffichage    = "wxFirebase"
gstInfoUtilisateur.sNuméroTéléphone = "+2250000000000"
gstInfoUtilisateur.sPhotoURL        = "https://lorempicture.point-sys.com/400/300/"
gstInfoUtilisateur.bVerifieEmail    = Faux

gclAuthReponse  = gclAuth.signUpWithEmailPassword(gstInfoUtilisateur)
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
gclAuthReponse  = gclAuth.signInAnonymously()
```
### Connexion par e-mail et mot de passe
```WLangage
gclAuthReponse  = gclAuth.signInWithEmailAndPassword("wx@firebase.com", "test1234")
```
#### Paramètres :
- `sEmail` : L'adresse e-mail de l'utilisateur (`chaine`).
- `sMdp` : Le mot de passe de l'utilisateur (`chaine`).

### Demande de réinitialisation de mot de passe
```WLangage
gclAuthReponse  = gclAuth.sendPasswordResetWithEmail("wx@firebase.com")
```
#### Paramètres :
- `sEmail` : L'adresse e-mail de l'utilisateur (`chaine`).

### Supprimer un utilisateur
```WLangage
gclAuthReponse  = gclAuth.deleteAccount()
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

stOptionProvider.sClientID	 =  CONST_CLIENT_ID
stOptionProvider.sClientSecret	 =  CONST_CLIENT_SECRET
stOptionProvider.sScope		 = "email"
stOptionProvider.sURLRedirection = "http://localhost:5000/auth/facebook/callback"

gclProvider est CFacebookProvider(stOptionProvider)

gclAuthReponse  est CAuthReponse = gclAuth.signInWithProvider(gclProvider)
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

stOptionProvider.sClientID	 =  CONST_CLIENT_ID
stOptionProvider.sClientSecret	 =  CONST_CLIENT_SECRET
stOptionProvider.sScope		 = "user"
stOptionProvider.sURLRedirection = "http://localhost:5000/auth/github/callback"

gclProvider est CGithubProvider(stOptionProvider)

gclAuthReponse  est CAuthReponse = gclAuth.signInWithProvider(gclProvider)
```
##### Paramètres :
- `sClientID` : L'identifiant client de l'application Github (`chaine`).
- `sClientSecret` : Le secret client de l'application Github (`chaine`).
- `sScope` : La portée des autorisations demandées (`chaine`).
- `sURLRedirection` : L'URL de redirection après l'authentification (`chaine`).

#### 3. Connexion via Google
```WLangage
stOptionProvider est STProviderOauthOptions

stOptionProvider.sClientID	 =  CONST_CLIENT_ID
stOptionProvider.sClientSecret	 =  CONST_CLIENT_SECRET
stOptionProvider.sScope		 = "email"
stOptionProvider.sURLRedirection = "http://localhost:5000/auth/github/callback"

gclProvider est CGoogleProvider(stOptionProvider)

gclAuthReponse  est CAuthReponse = gclAuth.signInWithProvider(gclProvider)
```
##### Paramètres :
- `sClientID` : L'identifiant client de l'application Google (`chaine`).
- `sClientSecret` : Le secret client de l'application Google (`chaine`).
- `sScope` : La portée des autorisations demandées (`chaine`).
- `sURLRedirection` : L'URL de redirection après l'authentification (`chaine`).

### Exemple d'utilisation de CAuthReponse
```WLangage
gclAuthReponse est CAuthReponse = gclAuth.sendPasswordResetWithEmail("wx@firebase.com", "test1234")

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
Firestore est une base de données NoSQL flexible et évolutive. Elle permet de stocker et de synchroniser des données pour les applications mobiles, web et serveur.

### Configuration de la Sécurité
Avant de commencer, il est fortement conseillé de configurer les règles de sécurité Firestore pour protéger vos données. Voici un exemple de règles de sécurité de base :
```WLangage
rules_version = '2';

service cloud.firestore {
  match /databases/{database}/documents {

   match /{document=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```
> [!NOTE]
>Cette règle permet uniquement aux utilisateurs authentifiés de lire et d'écrire des données dans Firestore.
>Lien vers la documentation https://firebase.google.com/docs/firestore/security/get-started?hl=fr

### Initialisation de Firestore
Pour initialiser Firestore, utilise le code suivant :
```WLangage
gclFireStoreDB est CFireStore = gclInstanceFirebase.FireStore()
```

### Fonctionnalités de **CFirestore**
`CFirestore` expose la méthode `Collection`, qui à son tour expose les méthodes presentes dans le tableau des fonctionnalités générales suivantes :

#### Fonctionnalités générales
| Méthode   | Description                                                                               |
|-----------|-------------------------------------------------------------------------------------------|
| Collection| Accède à une collection Firestore.                                                        |
| Creer     | Ajoute un nouveau document dans une collection Firestore.                                 |
| Modifier  | Modifie un document existant dans Firestore.                                              |
| Afficher  | Récupère les données d'un document Firestore.                                             |
| Supprimer | Supprime un document spécifique de Firestore.                                             |
| Lister    | Initialise une requête pour lister les documents d'une collection avec des options avancées. |

#### Fonctionnalités avancées
L'accès aux fonctionnalités avancées nécessite un appel préalable à la méthode `Lister` :

| Méthode   | Description                                                                 |
|-----------|-----------------------------------------------------------------------------|
| TrierPar  | Trie les résultats retournés selon un champ spécifique.                     |
| Filtrer   | Ajoute un filtre pour restreindre les documents retournés.                  |
| Limiter   | Limite le nombre de documents retournés par la requête.                     |
| Executer  | Exécute la requête construite et retourne les résultats.                    |

> [!IMPORTANT]
> Toutes les methodes precedement cité retourne une intance de `CFireStoreReponse`

### Gestion de **CFireStoreReponse**
La classe `CFireStoreReponse` encapsule les réponses de Firestore et de WinDev afin de fournir aux développeurs un ensemble de méthodes simples et cohérentes.
```WLangage
gclFireStoreReponse est CFireStoreReponse = gclFiretoreDB.Collection("utilisateurs").Creer(gJsonfirestoreDocument)
```
#### Description des Méthodes
Voici un aperçu des méthodes principales de `CFireStoreReponse` :
- `errType` : Retourne une instance de ETypeErreur : errFirebase pour une erreur liée à Firestore, errWindev pour une erreur liée à WinDev, ou errAucune si aucune erreur n'est survenue.
- `Données` : Retourne le résultat d'un document Firestore au format JSON.
- `errArgumentInvalide` : Erreur survenant lorsque les arguments passés à la méthode sont invalides ou mal formatés.
- `errAutorisationRefusee` : Erreur indiquant que l'utilisateur ou le client n'a pas les permissions nécessaires pour effectuer l'action demandée.
- `errIndisponible` : Erreur signalant que le service Firestore est temporairement indisponible.
- `errNonAuthentifie` : Erreur retournée lorsque l'utilisateur n'est pas authentifié ou que l'authentification a expiré.
- `errQuotaEpuisee` : Erreur déclenchée lorsque le quota d'utilisation des services Firestore est atteint.
- `errServeurIndisponible` : Erreur survenant lorsque le serveur Firestore ne répond pas ou est inaccessible.
- `errWindevMessage` : Contient le message d'erreur détaillé lorsqu'une erreur liée à WinDev est rencontrée.

###### Exemple d'utilisation
```WLangage
SELON gclFireStoreReponse.errType
	CAS errAucune : Info(gclFireStoreReponse.Données)

	CAS errFirebase
		SI gclFireStoreReponse.errQuotaEpuisee        ALORS Info("Votre quota est épuisé.")
		SI gclFireStoreReponse.errArgumentInvalide    ALORS Info("Un ou plusieurs arguments fournis sont invalides.")
		SI gclFireStoreReponse.errIndisponible        ALORS Info("Le service Firestore est actuellement indisponible.")
		SI gclFireStoreReponse.errNonAuthentifie      ALORS Info("La requête n'est pas authentifiée.")
		SI gclFireStoreReponse.errServeurIndisponible ALORS Info("Le serveur Cloud Firestore est inaccessible.")
		SI gclFireStoreReponse.errAutorisationRefusee ALORS Info("Vous n'êtes pas autorisé à effectuer cette opération.")
		
	CAS errWindev : Info(gclFireStoreReponse.errWindevMessage)		
FIN
```

### Exemple de JSON pour les Méthodes Creer et Modifier
Le code ci-dessous montre un exemple de document JSON qui peut être utilisé dans les méthodes `Creer` et `Modifier` :

```json
jsonfirestoreDocument est chaîne=
	{
  "nomEnseignant": "Dr. Alice Johnson",
  "anneesExperience": 12,
  "estTitulaire": true,
  "cours": [
    {
      "nomCours": "Introduction à la Programmation",
      "coefficient": 4,
      "etudiants": [
        {
          "nom": "Jean Dupont",
          "age": 20
        },
        {
          "nom": "Marie Curie",
          "age": 22
        }
      ],
      "projets": [
        {
          "titre": "Développement d'une Application Web",
          "description": "Créer une application web simple en utilisant React.",
          "scoreMaximal": 100
        },
        {
          "titre": "Implémentation de Structures de Données",
          "description": "Implémenter des structures de données clés comme la liste chaînée et l'arbre binaire.",
          "scoreMaximal": 100
        }
      ]
    },
    {
      "nomCours": "Systèmes de Gestion de Bases de Données",
      "coefficient": 3,
      "etudiants": [
        {
          "nom": "Alice Martin",
          "age": 21
        },
        {
          "nom": "Robert Blanc",
          "age": 23
        }
      ],
      "projets": [
        {
          "titre": "Optimisation des Requêtes SQL",
          "description": "Optimiser des requêtes SQL complexes pour de meilleures performances.",
          "scoreMaximal": 100
        }
      ]
    }
  ],
  "departements": {
    "informatique": {
      "nombreEnseignants": 20,
      "responsable": "Pr. David King"
    },
    "mathematiques": {
      "nombreEnseignants": 15,
      "responsable": "Pr. Susan Clark"
    }
  }
}
```
### Créer un document
Ajoute un nouveau document dans une collection donnée.
```WLangage
gclDbReponse est CFireStoreReponse = gclFirestoreDB.Collection("utilisateurs").Creer(jsonfirestoreDocument)
```
#### Paramètres :
- `sCollectionID` : Nom de la collection Firestore (`chaine`).
- `jsonDonnees` : Données du document à ajouter (`json`).

> [!NOTE]
>  Le paramètre `sCollectionID` est identique pour toutes les méthodes ci-dessous.

### Modifier un document
Modifie un document existant dans Firestore.
```WLangage
gclDbReponse est CFireStoreReponse = gclFirestoreDB.Collection("utilisateurs").Modifier("6PBA9QuFmM6zEKSgVqRo",jsonfirestoreDocument)
```
#### Paramètres :
- `sDocumentID` : Données du document à ajouter (`chaine`).
-`jsonDonnees` : Identiques à la méthode `Creer`.

### Afficher un document
Récupère les détails d'un document spécifique.
```WLangage
gclDbReponse est CFireStoreReponse = gclFirestoreDB.Collection("utilisateurs").Afficher("6PBA9QuFmM6zEKSgVqRo")
```
#### Paramètres :
- `sDocumentID` : Données du document à ajouter (`chaine`).

### Supprimer un document
Supprime un document existant dans Firestore.
```WLangage
gclDbReponse est CFireStoreReponse = gclFirestoreDB.Collection("utilisateurs").Supprimer("uGemEu6yiMMgAiKu359w")
```
#### Paramètres : Identiques à la méthode `Afficher`.

## III - Storage

Le **service Firebase Storage** permet de stocker et de récupérer des fichiers de manière sécurisée et évolutive. Ce service est idéal pour gérer des fichiers tels que des images, des vidéos, des documents, et bien plus encore.

### Initialisation du service de stockage
```WLangage
gclStorage est CStorage = gclInstanceFirebase.Storage()
```

### Téléchargement d'un fichier
```WLangage
sCheminFichier est chaine = "C:/chemin_vers_le_fichier/image.png"

gclStorageReponse est CStorageReponse  = gclStorage.downloadFile(sCheminFichier)

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
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/fabnguess"><img src="https://avatars.githubusercontent.com/u/72697416?v=4?s=100" width="100px;" alt="Kouadio Fabrice Nguessan"/><br /><sub><b>Kouadio Fabrice Nguessan</b></sub></a><br /><a href="https://github.com/fabnguess/wxFirebaseSDK/commits?author=fabnguess" title="Code">💻</a> <a href="https://github.com/fabnguess/wxFirebaseSDK/commits?author=fabnguess" title="Documentation">📖</a> </td>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/mrani24"><img src="https://avatars.githubusercontent.com/u/163332562?v=4" width="100px;" alt="Madara"/><br /><sub><b>Madara</b></sub></a><br /><a href="https://github.com/fabnguess/wxFirebaseSDK/commits?author=mrani24" title="Code">💻</a> </td>
    </tr>
  
  </tbody>
</table>

## License
MIT
