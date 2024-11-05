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

```bash
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
| gclAuthReponse.errType | ETypeErreur (errAucune, errFirebase, errWindev)|
| gclAuthReponse.Données  | Buffer |
| gclAuthReponse.errEmailExistant | Booleen |
| gclAuthReponse.errEmailIntrouvable | Booleen |
| gclAuthReponse.errMotDePasseInvalide | Booleen |
| gclAuthReponse.errUtilisateurDesactivé | Booleen |
| gclAuthReponse.errConnexionParMotDePasseDesactive | Booleen |
| gclAuthReponse.errConnexionAnonymeDesactive | Booleen |
| gclAuthReponse.errTokenInvalide  | Booleen |


### Créer un utilisateur
```bash
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
```bash
gclAuthReponse  = gclInstanceFirebase.Auth.ConnexionAnonyme()
```
### Connexion par e-mail et mot de passe
```bash
gclAuthReponse  = gclInstanceFirebase.Auth.SeConnecter("wx@firebase.com", "test1234")
```
### Demande de réinitialisation de mot de passe
```bash
gclAuthReponse  = gclInstanceFirebase.Auth.RéinitialiserMotDePasse("wx@firebase.com")
```
### Supprimer un utilisateur
```bash
gclAuthReponse  = gclInstanceFirebase.Auth.SupprimerUtilisateur()
```
### Connexion via providers
Les `Providers` sont des fournisseurs d'authentification autres que Firebase, par exemple Facebook, Github, Google ou Twitter. Vous pouvez trouver les fournisseurs d'authentification actuellement pris en charge dans la [documentation officielle de Firebase](https://firebase.google.com/docs/projects/provisioning/configure-oauth?hl=fr#add-idp). A l'heure actuelle le composant prends en compte les providres suivant : 

- Facebook
- Github
- Google

```bash
gclAuthReponse  = gclInstanceFirebase.Auth.SeConnecterProvider(CAuth.Facebook)
```

## II - Firestore

Firestore est la base de données NoSQL de Firebase, permettant de stocker et de synchroniser des données entre l'application et Firebase. Les opérations incluent l'ajout, la récupération, la mise à jour et la suppression de documents.

## III - Storage

Le service Storage permet de gérer le stockage et la récupération de fichiers. Cette fonctionnalité est idéale pour stocker des fichiers multimédias (images, vidéos, etc.) associés aux utilisateurs ou à des fonctionnalités spécifiques.

## Contributions

Les contributions sont les bienvenues.