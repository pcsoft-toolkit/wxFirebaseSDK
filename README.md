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
gstFirebaseConfig est ST_wxFirebaseConfig = [CONST_API_KEY, CONST_PROJET_ID, CONST_STORAGE_BUCKET]
gclInstanceFirebase est CFireBase(gstFirebaseConfig)
```
> [!NOTE]
> Initialisez la configuration Firebase en remplaçant CONST_API_KEY, CONST_PROJET_ID, et CONST_STORAGE_BUCKET par vos propres identifiants, puis instanciez CFireBase pour commencer à utiliser les services Firebase.

## Contributions

Les contributions sont les bienvenues.