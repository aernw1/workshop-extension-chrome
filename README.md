Voici une version remaniée du workshop sur la création d'extensions Chrome, divisée en sections plus petites et plus progressives :

# 🚀 Workshop Extensions Chrome - Version Progressive

## Introduction (15 minutes)

Bonjour et bienvenue à ce workshop sur la création d'extensions Chrome ! Au cours des prochaines heures, nous allons explorer ensemble comment développer vos propres extensions pour le navigateur Chrome.

### Objectifs du workshop :
- Comprendre les concepts fondamentaux des extensions Chrome
- Créer deux extensions complètes de A à Z
- Apprendre les bonnes pratiques de développement

### Prérequis :
- Connaissances de base en HTML, CSS et JavaScript
- Google Chrome installé
- Un éditeur de code (VS Code recommandé)

## Partie 1 : Découverte des Extensions Chrome (30 minutes)

### 1.1 Qu'est-ce qu'une extension Chrome ?
Une extension Chrome est un petit programme qui étend ou modifie les fonctionnalités du navigateur Chrome. Elles sont construites avec des technologies web standards que vous connaissez déjà : HTML, CSS et JavaScript.

### 1.2 À quoi servent les extensions ?
Les extensions peuvent :
- Modifier l'apparence ou le comportement des sites web
- Ajouter de nouvelles fonctionnalités au navigateur
- Interagir avec le contenu des pages web
- Fournir des informations ou des outils supplémentaires

### 1.3 Exemples d'extensions populaires
- Bloqueurs de publicité (uBlock Origin)
- Gestionnaires de mots de passe (LastPass)
- Outils de productivité (Grammarly, Evernote Web Clipper)
- Extensions de personnalisation (Dark Reader)

## Partie 2 : Anatomie d'une Extension Chrome (45 minutes)

### 2.1 Le fichier manifest.json
Le fichier manifest.json est le cœur de votre extension. C'est un fichier de configuration qui décrit votre extension à Chrome.

Voici un exemple de base :

```json
{
  "manifest_version": 3,
  "name": "Mon Extension",
  "version": "1.0",
  "description": "Ma première extension Chrome"
}
```

Explications :
- `manifest_version` : Toujours 3 pour les nouvelles extensions
- `name` : Le nom de votre extension
- `version` : La version actuelle
- `description` : Une brève description

### 2.2 Les composants principaux

#### a) Content Scripts
Les content scripts sont des scripts JavaScript qui s'exécutent dans le contexte des pages web. Ils peuvent lire et modifier le DOM de la page.

Exemple de content script simple :

```javascript
// content.js
console.log("Content script chargé !");
document.body.style.backgroundColor = "lightblue";
```

#### b) Background Scripts
Les background scripts (ou service workers dans Manifest V3) s'exécutent en arrière-plan et gèrent la logique principale de votre extension.

Exemple de background script :

```javascript
// background.js
chrome.runtime.onInstalled.addListener(() => {
  console.log("Extension installée !");
});
```

#### c) Popup
La popup est l'interface utilisateur qui apparaît lorsque vous cliquez sur l'icône de l'extension dans la barre d'outils.

Exemple de popup.html :

```html
<!DOCTYPE html>
<html>
<head>
  <style>
    body { width: 200px; padding: 10px; }
    button { width: 100%; padding: 5px; }
  </style>
</head>
<body>
  <h3>Mon Extension</h3>
  <button id="myButton">Cliquez-moi !</button>
  <script src="popup.js"></script>
</body>
</html>
```

#### d) Options Page
La page d'options permet aux utilisateurs de configurer votre extension.

Exemple d'options.html :

```html
<!DOCTYPE html>
<html>
<head>
  <title>Options</title>
</head>
<body>
  <h1>Options de l'extension</h1>
  <form>
    <label>
      <input type="checkbox" id="enableFeature"> Activer la fonctionnalité
    </label>
  </form>
  <script src="options.js"></script>
</body>
</html>
```

## Partie 3 : Configuration de l'Environnement de Développement (20 minutes)

### 3.1 Création de la structure du projet
Créez un nouveau dossier pour votre projet avec cette structure de base :

```
mon-extension/
├── manifest.json
├── icons/
│   ├── icon16.png
│   ├── icon48.png
│   └── icon128.png
├── popup/
│   ├── popup.html
│   ├── popup.js
│   └── popup.css
├── content.js
└── background.js
```

### 3.2 Chargement de l'extension en développement
1. Ouvrez Chrome et accédez à `chrome://extensions/`
2. Activez le "Mode développeur" (interrupteur en haut à droite)
3. Cliquez sur "Charger l'extension non empaquetée"
4. Sélectionnez le dossier de votre extension

### 3.3 Rechargement après modifications
Après avoir modifié des fichiers, revenez à `chrome://extensions/` et cliquez sur l'icône de rechargement (↻) de votre extension pour appliquer les changements.

## Partie 4 : Projet 1 - GitHub File Icons (1 heure)

### 4.1 Introduction au projet
Nous allons créer une extension qui ajoute des icônes aux fichiers sur GitHub pour une meilleure identification visuelle.

### 4.2 Configuration du manifest.json
```json
{
  "manifest_version": 3,
  "name": "GitHub File Icons",
  "version": "1.0",
  "description": "Ajoute des icônes aux fichiers sur GitHub",
  "icons": {
    "16": "icons/icon16.png",
    "48": "icons/icon48.png",
    "128": "icons/icon128.png"
  },
  "content_scripts": [
    {
      "matches": ["https://github.com/*"],
      "js": ["content.js"]
    }
  ],
  "web_accessible_resources": [{
    "resources": ["icons/*"],
    "matches": ["https://github.com/*"]
  }]
}
```

### 4.3 Création du content script
Voici comment nous allons procéder dans content.js :

```javascript
// 1. Définir un dictionnaire des extensions et leurs icônes
const fileIcons = {
  'js': 'icons/js.png',
  'css': 'icons/css.png',
  'html': 'icons/html.png',
  'py': 'icons/python.png',
  'java': 'icons/java.png',
  'md': 'icons/markdown.png',
  'json': 'icons/json.png',
  'png': 'icons/image.png',
  'jpg': 'icons/image.png',
  'jpeg': 'icons/image.png',
  'gif': 'icons/image.png',
  'svg': 'icons/image.png'
};

// 2. Fonction pour ajouter des icônes aux fichiers
function addIconsToFiles() {
  // Trouver tous les éléments représentant des fichiers
  const fileElements = document.querySelectorAll('.js-navigation-item');

  fileElements.forEach(file => {
    // Extraire le nom du fichier
    const fileName = file.querySelector('.js-navigation-open').textContent;

    // Obtenir l'extension (après le dernier point)
    const extension = fileName.split('.').pop();

    if (fileIcons[extension]) {
      // Créer et ajouter l'icône
      const icon = document.createElement('img');
      icon.src = chrome.runtime.getURL(fileIcons[extension]);
      icon.className = 'file-icon';
      icon.style.width = '16px';
      icon.style.marginRight = '5px';

      // Insérer avant le nom du fichier
      file.querySelector('.js-navigation-open').prepend(icon);
    }
  });
}

// 3. Observer les changements de page (navigation GitHub en AJAX)
const observer = new MutationObserver(mutations => {
  for (let mutation of mutations) {
    if (mutation.addedNodes.length) {
      addIconsToFiles();
    }
  }
});

// 4. Démarrer l'observation et ajouter les icônes initiales
observer.observe(document.body, { childList: true, subtree: true });
addIconsToFiles();
```

### 4.4 Explications du code
1. **Dictionnaire des icônes** : Nous définissons quelles icônes utiliser pour quelles extensions de fichiers.
2. **Fonction addIconsToFiles** :
   - Trouve tous les éléments de fichier sur la page
   - Extrait le nom du fichier et son extension
   - Ajoute une icône appropriée si l'extension est reconnue
3. **MutationObserver** :
   - Surveille les changements dans le DOM
   - Réapplique les icônes lorsque de nouveaux éléments sont chargés (navigation AJAX)

### 4.5 Test de l'extension
1. Allez sur un dépôt GitHub
2. Vérifiez que les icônes apparaissent à côté des noms de fichiers
3. Naviguez dans le dépôt pour tester le rechargement AJAX

## Pause (15 minutes)

## Partie 5 : Projet 2 - GitHub Background Themes (1 heure)

### 5.1 Introduction au projet
Nous allons créer une extension qui permet de personnaliser l'apparence de GitHub avec des couleurs ou images d'arrière-plan.

### 5.2 Configuration du manifest.json
```json
{
  "manifest_version": 3,
  "name": "GitHub Themes",
  "version": "1.0",
  "description": "Personnalise l'arrière-plan de GitHub",
  "icons": {
    "16": "icons/icon16.png",
    "48": "icons/icon48.png",
    "128": "icons/icon128.png"
  },
  "permissions": ["storage"],
  "host_permissions": ["https://github.com/*"],
  "action": {
    "default_popup": "popup.html",
    "default_icon": {
      "16": "icons/icon16.png",
      "48": "icons/icon48.png"
    }
  },
  "content_scripts": [
    {
      "matches": ["https://github.com/*"],
      "js": ["content.js"]
    }
  ]
}
```

### 5.3 Création de la popup
popup.html :
```html
<!DOCTYPE html>
<html>
<head>
  <style>
    body {
      width: 250px;
      padding: 10px;
      font-family: Arial, sans-serif;
    }
    .option {
      margin-bottom: 10px;
    }
    #colorPicker {
      width: 100%;
      height: 30px;
    }
    #imagePreview {
      max-width: 100%;
      margin-top: 5px;
      border: 1px solid #ddd;
    }
    button {
      width: 100%;
      padding: 8px;
      background-color: #4CAF50;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    button:hover {
      background-color: #45a049;
    }
  </style>
</head>
<body>
  <h3>GitHub Themes</h3>

  <div class="option">
    <label><input type="radio" name="themeType" value="color" checked> Couleur</label>
    <input type="color" id="colorPicker" value="#f0f0f0">
  </div>

  <div class="option">
    <label><input type="radio" name="themeType" value="image"> Image</label>
    <input type="file" id="imageUpload" accept="image/*">
    <img id="imagePreview" style="display: none;">
  </div>

  <div class="option">
    <label><input type="radio" name="themeType" value="dynamic"> Dégradé dynamique</label>
  </div>

  <button id="saveButton">Appliquer</button>

  <script src="popup.js"></script>
</body>
</html>
```

### 5.4 Création du script popup.js
```javascript
// Récupérer les éléments du DOM
const radioButtons = document.querySelectorAll('input[name="themeType"]');
const colorPicker = document.getElementById('colorPicker');
const imageUpload = document.getElementById('imageUpload');
const imagePreview = document.getElementById('imagePreview');
const saveButton = document.getElementById('saveButton');

// Charger les préférences sauvegardées
chrome.storage.local.get(['themeType', 'themeColor', 'themeImage'], (result) => {
  if (result.themeType) {
    // Sélectionner le bon radio button
    document.querySelector(`input[value="${result.themeType}"]`).checked = true;

    // Restaurer les valeurs
    if (result.themeColor) colorPicker.value = result.themeColor;
    if (result.themeImage) {
      imagePreview.src = result.themeImage;
      imagePreview.style.display = 'block';
    }
  }
});

// Aperçu d'image téléchargée
imageUpload.addEventListener('change', () => {
  const file = imageUpload.files[0];
  if (file) {
    const reader = new FileReader();
    reader.onload = (e) => {
      imagePreview.src = e.target.result;
      imagePreview.style.display = 'block';
    };
    reader.readAsDataURL(file);
  }
});

// Sauvegarder et appliquer le thème
saveButton.addEventListener('click', () => {
  // Déterminer quel type de thème est sélectionné
  const selectedType = document.querySelector('input[name="themeType"]:checked').value;

  // Préparer les données à sauvegarder
  const themeData = { themeType: selectedType };

  if (selectedType === 'color') {
    themeData.themeColor = colorPicker.value;
  } else if (selectedType === 'image' && imagePreview.src) {
    themeData.themeImage = imagePreview.src;
  }

  // Sauvegarder dans le stockage local
  chrome.storage.local.set(themeData, () => {
    // Envoyer un message au content script
    chrome.tabs.query({active: true, currentWindow: true}, (tabs) => {
      chrome.tabs.sendMessage(tabs[0].id, {
        action: 'applyTheme',
        theme: themeData
      });
    });
  });
});
```

### 5.5 Création du content script
```javascript
// Appliquer le thème sauvegardé au chargement de la page
chrome.storage.local.get(['themeType', 'themeColor', 'themeImage'], (result) => {
  if (result.themeType) {
    applyTheme(result);
  }
});

// Écouter les messages de le popup
chrome.runtime.onMessage.addListener((message, sender, sendResponse) => {
  if (message.action === 'applyTheme') {
    applyTheme(message.theme);
  }
});

// Fonction pour appliquer le thème
function applyTheme(theme) {
  // Réinitialiser d'abord
  document.body.style.backgroundColor = '';
  document.body.style.backgroundImage = '';

  // Appliquer selon le type
  switch (theme.themeType) {
    case 'color':
      document.body.style.backgroundColor = theme.themeColor;
      break;

    case 'image':
      if (theme.themeImage) {
        document.body.style.backgroundImage = `url(${theme.themeImage})`;
        document.body.style.backgroundSize = 'cover';
        document.body.style.backgroundAttachment = 'fixed';
      }
      break;

    case 'dynamic':
      // Ajouter une classe CSS pour l'animation
      document.body.classList.add('dynamic-bg');
      // Injecter la règle CSS d'animation
      const style = document.createElement('style');
      style.textContent = `
        .dynamic-bg {
          background: linear-gradient(-45deg, #ee7752, #e73c7e, #23a6d5, #23d5ab);
          background-size: 400% 400%;
          animation: gradient 15s ease infinite;
        }
        @keyframes gradient {
          0% { background-position: 0% 50%; }
          50% { background-position: 100% 50%; }
          100% { background-position: 0% 50%; }
        }
      `;
      document.head.appendChild(style);
      break;
  }

  // Assurer la lisibilité du texte
  adjustTextContrast();
}

// Ajuster la couleur du texte pour assurer la lisibilité
function adjustTextContrast() {
  // Logique pour ajuster la couleur du texte selon l'arrière-plan
  // (implémentation simplifiée)
  const isDark = document.body.style.backgroundColor &&
    isColorDark(document.body.style.backgroundColor);

  if (isDark) {
    document.body.classList.add('light-text');
  } else {
    document.body.classList.remove('light-text');
  }
}

// Déterminer si une couleur est sombre
function isColorDark(color) {
  // Logique simplifiée pour déterminer si une couleur est sombre
  // Implémentation réelle nécessiterait une conversion RGB et calcul de luminosité
  return color.match(/^#(?:[0-9a-f]{3}){1,2}$/i) &&
         parseInt(color.substr(1), 16) < 8388608; // < 50% de luminosité
}
```

### 5.6 Explications du code
1. **Popup** :
   - Interface pour sélectionner le type de thème
   - Gestion des événements utilisateur
   - Sauvegarde des préférences

2. **Content Script** :
   - Application du thème au chargement de la page
   - Écoute des messages de la popup
   - Fonction d'application du thème
   - Ajustement du contraste du texte

3. **Communication** :
   - Utilisation de chrome.storage pour sauvegarder les préférences
   - Envoi de messages entre la popup et le content script

### 5.7 Test de l'extension
1. Cliquez sur l'icône de l'extension pour ouvrir la popup
2. Sélectionnez un type de thème et configurez-le
3. Cliquez sur "Appliquer" et vérifiez que le thème est appliqué
4. Rechargez la page pour vérifier que le thème persiste

## Partie 6 : Techniques Avancées et Bonnes Pratiques (30 minutes)

### 6.1 Bonnes pratiques de développement
1. **Organisation du code** :
   - Séparer la logique en modules
   - Utiliser des noms de variables et fonctions clairs
   - Commenter le code complexe

2. **Gestion des erreurs** :
   - Toujours vérifier les permissions
   - Gérer les cas d'erreur dans les callbacks
   - Utiliser try/catch pour les opérations risquées

3. **Optimisation des performances** :
   - Limiter l'utilisation de MutationObserver
   - Éviter les opérations DOM coûteuses
   - Utiliser le lazy loading pour les ressources

### 6.2 Débogage d'extensions
1. **Console du navigateur** :
   - Clic droit → Inspecter → Console
   - Voir les logs et erreurs

2. **Inspecter le popup** :
   - Clic droit sur l'icône de l'extension → Inspecter

3. **Recharger l'extension** :
   - Après modifications : chrome://extensions/ → ↻

### 6.3 Publication sur le Chrome Web Store
1. **Préparation du package** :
   - Vérifier le manifest.json
   - Tester sur différentes pages
   - Préparer les captures d'écran

2. **Processus de soumission** :
   - Créer un compte développeur ($5)
   - Remplir les informations de l'extension
   - Soumettre pour examen

3. **Conseils pour le succès** :
   - Description claire et concise
   - Captures d'écran de qualité
   - Vidéo de démonstration
   - Support utilisateur réactif

## Partie 7 : Session Pratique Libre (30 minutes)

### 7.1 Personnalisation des projets
- Ajoutez des fonctionnalités supplémentaires à vos extensions
- Expérimentez avec les concepts appris
- Essayez de combiner des éléments des deux projets

### 7.2 Idées d'amélioration
Pour GitHub File Icons :
- Ajouter plus d'icônes de fichiers
- Permettre la personnalisation des icônes
- Ajouter des options de taille et position

Pour GitHub Background Themes :
- Ajouter plus d'options de thème
- Permettre l'import/export de thèmes
- Ajouter un système de thèmes prédéfinis

### 7.3 Questions/Réponses
- Réponses aux questions spécifiques
- Discussion des cas d'usage personnels
- Partage d'expériences entre participants

## Conclusion (15 minutes)

### Récapitulatif
- Nous avons couvert les concepts fondamentaux des extensions Chrome
- Créé deux extensions complètes de A à Z
- Appris les bonnes pratiques de développement

### Ressources pour aller plus loin
- [Documentation officielle Chrome Extensions](https://developer.chrome.com/docs/extensions/)
- [Exemples d'extensions](https://github.com/GoogleChrome/chrome-extensions-samples)
- [Forum des développeurs Chrome](https://groups.google.com/a/chromium.org/g/chromium-extensions)

### Feedback
- Vos impressions sur le workshop
- Ce que vous avez préféré
- Ce que nous pourrions améliorer

Merci d'avoir participé à ce workshop ! N'hésitez pas à continuer à expérimenter avec les extensions Chrome et à partager vos créations.
