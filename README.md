<h1 align="center">wxFirebaseSDK</h1>

<div align="center">

[![WinDev](https://img.shields.io/badge/WinDev-25+-blue.svg)](https://pcsoft.fr/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Firebase](https://img.shields.io/badge/Firebase-REST_API-orange.svg)](https://firebase.google.com/docs/reference/rest)

</div>

<p align="center">
    Un composant destiné aux développeurs utilisant les produits 
    <a href="https://pcsoft.fr/" target="_blank">PCSoft</a>, facilitant l'intégration des services Firebase dans vos projets Windev, WebDev, et Windev Mobile.
</p>

<p align="center">
    <strong>Authentification</strong> | <strong>Firestore</strong> | <strong>Storage</strong>
</p>

## Prérequis

- **Windev version 25 ou supérieure** ([lien de téléchargement officiel](https://pcsoft.fr/st/telec/index.html))
- **Un projet Firebase** : vous pouvez en créer un nouveau via la [console Firebase](https://console.firebase.google.com/u/0/) si vous n'en avez pas déjà un.

## Installation

1. Téléchargez la dernière version de `wxFirebase` dans la [page de Releases de ce projet](https://github.com/pcsoft-toolkit/wxFirebaseSDK/releases).
2. Ajoutez le composant à votre projet WINDEV® en suivant la [documentation officielle](https://doc.pcsoft.fr/?2014006).

## Configuration Firebase

Pour que le composant puisse accéder à un projet Firebase, vous devez au préalable le configurer en générant un fichier de clé privée dans la console Firebase.

### Étape 1 : Créer un projet Firebase

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
- `PROJET_ID` : L'identifiant du projet Firebase (`chaine`).
- `STORAGE_BUCKET` : L'URL du bucket de stockage du projet Firebase (`chaine`).

### Étape 3 : Initialisation dans votre code

```WLangage
// Initialisation avec configuration par défaut
gclConfigResponse est CConfigResponse = CFirebaseApp.initializeApp()

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