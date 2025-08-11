<h1 align="center">wxFirebaseSDK</h1>

<div align="center">

[![WinDev](https://img.shields.io/badge/WinDev-25+-blue.svg?style=for-the-badge)](https://pcsoft.fr/)
[![License](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)](LICENSE)
[![Firebase](https://img.shields.io/badge/Firebase-REST_API-orange.svg?style=for-the-badge)](https://firebase.google.com/docs/reference/rest)

</div>

<p align="center">
    Un composant destiné aux développeurs utilisant les produits 
    <a href="https://pcsoft.fr/" target="_blank">PCSoft</a>, facilitant l'intégration des services Firebase dans vos projets Windev, WebDev, et Windev Mobile.
</p>

<p align="center">
    <strong>Authentification</strong> | <strong>Firestore</strong> | <strong>Storage</strong>
</p>

<p align="center">
    <a href="/Ressources/Exemple/" target="_blank"><strong>Télécharger la démo </strong></a>
</p>

## Prérequis

- **Windev version 25 ou supérieure** ([lien de téléchargement officiel](https://pcsoft.fr/st/telec/index.html))
- **Un projet Firebase** : vous pouvez en créer un nouveau via la [console Firebase](https://console.firebase.google.com/u/0/) si vous n'en avez pas déjà un.

## Installation

1. Téléchargez la dernière version de `wxFirebase` dans la [page de Releases de ce projet](https://github.com/pcsoft-toolkit/wxFirebaseSDK/releases).
2. Ajoutez le composant à votre projet Windev en suivant la [documentation officielle](https://doc.pcsoft.fr/?2014006).

## Configuration Firebase

Pour que le composant puisse accéder à un projet Firebase, vous devez au préalable le configurer en générant un fichier de clé privée dans la console Firebase.

### Étape 1 : Créer un projet Firebase

- Allez sur [console.firebase.google.com](https://console.firebase.google.com).
- Cliquez sur `Créer un projet`
- Donnez un nom à votre projet (ex: `wxFirebaseWrapper`)
- **Google Analytics** : Vous pouvez le désactiver
- Cliquez sur `Créer un projet` et attendez la finalisation

### Étape 2 : Ajouter une application Web
- Dans votre console de projet, cliquez sur l'icône **Web** `</>`
- **Pseudo de l'application** : Donnez un nom descriptif (ex: `wxFirebaseWrapper`)
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
| --- | :-: | --- |
| `API_KEY` | La clé API Web de votre projet Firebase | `chaîne`.
| `PROJET_ID`  | L'identifiant du projet Firebase | `chaîne`.
| `STORAGE_BUCKET` | L'URL du bucket de stockage du projet Firebase | `chaîne`.

### Étape 2 : Initialisation dans votre code
#### Initialisation standard
```WLangage
// Initialisation avec configuration par défaut
gclConfigResponse est CConfigResponse = CFirebaseApp.initializeApp()

// Vérification des erreurs
SI gclConfigResponse.hasError() ALORS
	Erreur(gclConfigResponse.getMessage())
	RETOUR
FIN

// Récupération de l'instance Firebase
gclFirebase est CFireBase = gclConfigResponse.getInstance()
```

#### Initialisation avec chemin personnalisé
```WLangage
// Initialisation avec un chemin personnalisé vers le fichier INI
sCheminConfig est chaîne = "C:\MonApp\config\firebase-prod.ini"
gclConfigResponse est CConfigResponse = CFirebaseApp.initializeApp(sCheminConfig)

// Vérification des erreurs
SI gclConfigResponse.hasError() ALORS
	Erreur(gclConfigResponse.getMessage())
	RETOUR
FIN

// Récupération de l'instance Firebase
gclFirebase est CFireBase = gclConfigResponse.getInstance()
```
> [!NOTE]
>  Si aucun chemin n'est spécifié, le composant recherche automatiquement le fichier `firebaseConfig.INI` dans le répertoire de l'exécutable.