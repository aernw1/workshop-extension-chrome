# 🚀 Workshop Extensions Chrome

## Bienvenue au Workshop sur la création d'extensions Chrome!

Ce workshop vous guidera pas à pas pour créer vos propres extensions Chrome en environ 3h30. Vous apprendrez les concepts fondamentaux du développement d'extensions et mettrez en pratique ces connaissances à travers deux projets concrets.

## 📋 Prérequis

- Connaissances de base en HTML, CSS et JavaScript
- Google Chrome installé sur votre ordinateur
- Un éditeur de code (VS Code recommandé)
- Git installé (optionnel, pour cloner ce repository)

## 🎓 Comprendre les extensions Chrome

Les extensions Chrome sont des programmes qui étendent ou modifient les fonctionnalités du navigateur Chrome. Elles utilisent des technologies web standards (HTML, CSS, JavaScript).

### Les composants d'une extension Chrome

Une extension Chrome comprend plusieurs éléments qui travaillent ensemble :

1. **manifest.json** - Le fichier de configuration obligatoire qui définit votre extension
2. **content scripts** - Scripts qui s'exécutent directement dans les pages web visitées
3. **background scripts** - Scripts qui fonctionnent en arrière-plan du navigateur
4. **popup** - L'interface qui apparaît quand on clique sur l'icône de l'extension
5. **options page** - Page permettant de configurer l'extension

### Le manifest.json expliqué

Ce fichier est le cœur de votre extension. Voici un exemple simplifié avec des explications :

```json
{
  "manifest_version": 3,        // Version du format (toujours 3 pour les nouvelles extensions)
  "name": "Mon Extension",      // Le nom de votre extension
  "version": "1.0",             // La version de votre extension
  "description": "Ma première extension Chrome", // Description qui apparaît dans le store
  
  "icons": {                    // Définit les icônes de votre extension à différentes tailles
    "16": "icons/icon16.png",   // Petit icône pour les favoris
    "48": "icons/icon48.png",   // Icône moyenne pour la page d'extensions
    "128": "icons/icon128.png"  // Grand icône pour le Chrome Web Store
  },
  
  "action": {                   // Configure le comportement quand on clique sur l'icône
    "default_popup": "popup.html", // La page HTML qui s'ouvre en popup
    "default_icon": {           // Les icônes spécifiques à l'action
      "16": "icons/icon16.png",
      "48": "icons/icon48.png"
    }
  },
  
  "content_scripts": [          // Scripts injectés dans les pages web visitées
    {
      "matches": ["https://*.github.com/*"], // Sur quels sites ils s'exécutent
      "js": ["content.js"]      // Quels fichiers JavaScript exécuter
    }
  ],
  
  "permissions": ["storage", "activeTab"], // Autorisations demandées par l'extension
  "host_permissions": ["https://*.github.com/*"] // Sites auxquels l'extension peut accéder
}
```

## 📝 Programme du workshop (3h30)

### Introduction (30 min)
- Qu'est-ce qu'une extension Chrome et comment ça fonctionne?
- Structure d'une extension et rôle de chaque composant
- Configuration avec manifest.json

### Projet 1: GitHub File Icons (1h)
Nous allons créer ensemble une extension qui ajoute des icônes aux fichiers sur GitHub.

**Ce que vous allez apprendre:**
- Comment manipuler le DOM d'une page web avec JavaScript
- Comment détecter les changements de page avec MutationObserver
- Comment accéder aux ressources de votre extension

### Pause (15 min)

### Projet 2: GitHub Background Themes (1h15)
Dans ce projet, nous créerons une extension permettant de personnaliser l'arrière-plan de GitHub.

**Ce que vous allez apprendre:**
- Comment créer une interface popup interactive
- Comment stocker les préférences utilisateur avec chrome.storage
- Comment faire communiquer différentes parties de votre extension

### Exploration et personnalisation (30 min)
- Temps pour personnaliser vos extensions
- Ajout de fonctionnalités supplémentaires selon vos idées

## 🛠️ Installation d'une extension en mode développement

Suivez ces étapes simples pour tester votre extension:

1. Ouvrez Chrome et accédez à `chrome://extensions/`
2. Activez le "Mode développeur" (interrupteur en haut à droite)
3. Cliquez sur "Charger l'extension non empaquetée"
4. Sélectionnez le dossier contenant vos fichiers d'extension

## 📚 Les projets en détail

### Projet 1: GitHub File Icons

#### Objectif: 
Ajouter des icônes visuelles à côté des noms de fichiers dans les repositories GitHub pour identifier facilement leur type.

#### Comment ça marche:
1. L'extension s'active uniquement sur les pages GitHub
2. Elle analyse tous les noms de fichiers affichés
3. Elle ajoute une icône correspondant au type de fichier (.js, .css, .py, etc.)
4. Elle surveille les changements de page pour ajouter des icônes aux nouveaux fichiers chargés

#### Les étapes clés:

**1. Créer le manifest.json**
```json
{
  "manifest_version": 3,
  "name": "GitHub File Icons",
  "version": "1.0",
  "description": "Ajoute des icônes aux fichiers sur GitHub",
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

**2. Créer le content.js**

Voici comment vous pourriez approcher la logique (pseudocode simplifié):

```javascript
// 1. Définir un dictionnaire des extensions et leurs icônes
const fileIcons = {
  'js': 'icons/js.png',
  'css': 'icons/css.png',
  // etc...
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

**Concepts clés à expliquer:**

- **Sélection d'éléments DOM**: Comment trouver des éléments spécifiques sur la page
- **MutationObserver**: Pour détecter quand GitHub charge de nouveaux contenus
- **chrome.runtime.getURL()**: Pour accéder aux ressources de votre extension

### Projet 2: GitHub Background Themes

#### Objectif:
Créer une extension qui permet de personnaliser l'apparence de GitHub avec des couleurs ou images d'arrière-plan.

#### Comment ça marche:
1. Cliquer sur l'icône de l'extension ouvre une popup
2. La popup permet de choisir une couleur, une image ou un thème dynamique
3. Les paramètres sont sauvegardés et appliqués à toutes les pages GitHub

#### Les étapes clés:

**1. Créer le manifest.json**
```json
{
  "manifest_version": 3,
  "name": "GitHub Themes",
  "version": "1.0",
  "description": "Personnalise l'arrière-plan de GitHub",
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

**2. Créer popup.html**

Une interface simple pour la sélection de thème:

```html
<!DOCTYPE html>
<html>
<head>
  <style>
    body { width: 250px; padding: 10px; font-family: Arial; }
    .option { margin-bottom: 10px; }
    #colorPicker { width: 100%; }
    #imagePreview { max-width: 100%; margin-top: 5px; }
  </style>
</head>
<body>
  <h3>GitHub Themes</h3>
  
  <div class="option">
    <label><input type="radio" name="themeType" value="color"> Couleur</label>
    <input type="color" id="colorPicker" value="#f0f0f0">
  </div>
  
  <div class="option">
    <label><input type="radio" name="themeType" value="image"> Image</label>
    <input type="file" id="imageUpload" accept="image/*">
    <img id="imagePreview">
  </div>
  
  <div class="option">
    <label><input type="radio" name="themeType" value="dynamic"> Dégradé dynamique</label>
  </div>
  
  <button id="saveButton">Appliquer</button>
  
  <script src="popup.js"></script>
</body>
</html>
```

**3. Créer popup.js**

La logique de l'interface utilisateur:

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
    if (result.themeImage) imagePreview.src = result.themeImage;
  }
});

// Aperçu d'image téléchargée
imageUpload.addEventListener('change', () => {
  const file = imageUpload.files[0];
  if (file) {
    const reader = new FileReader();
    reader.onload = (e) => {
      imagePreview.src = e.target.result;
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

**4. Créer content.js**

Le script qui applique les thèmes:

```javascript
// Appliquer le thème sauvegardé au chargement de la page
chrome.storage.local.get(['themeType', 'themeColor', 'themeImage'], (result) => {
  if (result.themeType) {
    applyTheme(result);
  }
});

// Écouter les messages de la popup
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

**Concepts clés à expliquer:**

- **chrome.storage API**: Comment stocker des données entre les sessions
- **Communication entre scripts**: Comment faire communiquer popup.js et content.js
- **FileReader API**: Comment gérer les images téléchargées par l'utilisateur
- **Manipulation dynamique des styles**: Comment modifier l'apparence d'une page

## 🔍 Débogage d'extensions

Tout développeur d'extensions doit savoir comment déboguer efficacement:

1. **Console du navigateur**: Pour voir les logs et erreurs
   - Clic droit sur la page → Inspecter → Console

2. **Inspecter la popup**:
   - Clic droit sur l'icône de l'extension → Inspecter

3. **Recharger l'extension**:
   - Après modification des fichiers: chrome://extensions → ↻ (icône de rechargement)

## 🧩 Progression d'exercices pratiques

### Projet 1: GitHub File Icons - Progression

#### Exercice 1.1: Créer la structure de base
Commencez par créer le manifest.json et une version minimale du content.js qui affiche un message dans la console.

```json
// manifest.json
{
  "manifest_version": 3,
  "name": "GitHub File Icons",
  "version": "1.0",
  "description": "Ajoute des icônes aux fichiers sur GitHub",
  "content_scripts": [
    {
      "matches": ["https://github.com/*"],
      "js": ["content.js"]
    }
  ]
}
```

```javascript
// content.js
console.log("Extension GitHub File Icons activée !");
```

**Objectif**: Vérifier que l'extension se charge correctement sur GitHub.

#### Exercice 1.2: Sélectionner les éléments de fichier
Modifiez content.js pour identifier et compter les éléments représentant des fichiers sur GitHub.

```javascript
// content.js
function findFileElements() {
  // Sur GitHub, les fichiers sont dans des éléments avec certaines classes
  const fileElements = document.querySelectorAll('.js-navigation-item');
  console.log(`Nombre de fichiers trouvés: ${fileElements.length}`);
  
  // Afficher les noms des fichiers trouvés
  fileElements.forEach(file => {
    const nameElement = file.querySelector('.js-navigation-open');
    if (nameElement) {
      console.log(`Fichier trouvé: ${nameElement.textContent}`);
    }
  });
}

// Exécuter après chargement de la page
window.addEventListener('load', findFileElements);
```

**Objectif**: Apprendre à sélectionner des éléments spécifiques du DOM.

#### Exercice 1.3: Extraire les extensions de fichiers
Ajoutez une fonction pour extraire et afficher les extensions des fichiers trouvés.

```javascript
// content.js
function getFileExtension(fileName) {
  // Gérer les cas comme .gitignore ou les fichiers sans extension
  if (fileName.startsWith('.') && fileName.indexOf('.', 1) === -1) {
    return fileName; // C'est un fichier caché sans extension
  }
  
  const parts = fileName.split('.');
  // S'il n'y a qu'une partie ou commence par un point, pas d'extension
  if (parts.length === 1 || (fileName.startsWith('.') && parts.length === 2)) {
    return '';
  }
  return parts.pop().toLowerCase(); // Retourne la dernière partie en minuscules
}

function analyzeFiles() {
  const fileElements = document.querySelectorAll('.js-navigation-item');
  
  fileElements.forEach(file => {
    const nameElement = file.querySelector('.js-navigation-open');
    if (nameElement) {
      const fileName = nameElement.textContent;
      const extension = getFileExtension(fileName);
      console.log(`Fichier: ${fileName}, Extension: ${extension || '(aucune)'}`);
    }
  });
}

window.addEventListener('load', analyzeFiles);
```

**Objectif**: Apprendre à extraire des informations pertinentes avec des fonctions utilitaires.

#### Exercice 1.4: Créer le dictionnaire d'icônes
Ajoutez un dictionnaire associant les extensions de fichiers à leurs icônes.

```javascript
// content.js
const fileIcons = {
  // Langages web
  'html': '🌐',
  'css': '🎨',
  'js': '📜',
  'jsx': '⚛️',
  'ts': '📘',
  'json': '📋',
  
  // Langages de programmation
  'py': '🐍',
  'java': '☕',
  'php': '🐘',
  'rb': '💎',
  'c': '©️',
  'cpp': '🔷',
  'go': '🦫',
  'rs': '🦀',
  
  // Documents
  'md': '📝',
  'txt': '📄',
  'pdf': '📕',
  'doc': '📘',
  'docx': '📘',
  
  // Images
  'png': '🖼️',
  'jpg': '🖼️',
  'jpeg': '🖼️',
  'gif': '🖼️',
  'svg': '🖼️',
  
  // Divers
  'gitignore': '👁️‍🗨️',
  'env': '🔒'
};

// Pour l'instant, utilisons des emojis au lieu d'images
// Plus tard, nous pourrons utiliser chrome.runtime.getURL() pour accéder aux vraies icônes
```

**Objectif**: Créer une structure de données pour associer les types de fichiers à leurs représentations visuelles.

#### Exercice 1.5: Ajouter les icônes aux fichiers
Modifiez le script pour ajouter les emojis (puis les icônes) à côté des noms de fichiers.

```javascript
function addIconsToFiles() {
  const fileElements = document.querySelectorAll('.js-navigation-item');
  
  fileElements.forEach(file => {
    const nameElement = file.querySelector('.js-navigation-open');
    if (nameElement) {
      const fileName = nameElement.textContent;
      const extension = getFileExtension(fileName);
      
      // Vérifier si nous avons une icône pour cette extension
      const icon = fileIcons[extension] || '📄'; // Emoji par défaut
      
      // Éviter d'ajouter des icônes en double
      if (!nameElement.querySelector('.file-icon-emoji')) {
        // Créer un élément pour l'icône
        const iconElement = document.createElement('span');
        iconElement.className = 'file-icon-emoji';
        iconElement.textContent = icon + ' '; // Ajouter un espace après l'emoji
        iconElement.style.marginRight = '5px';
        
        // Ajouter l'icône avant le nom du fichier
        nameElement.prepend(iconElement);
      }
    }
  });
}

window.addEventListener('load', addIconsToFiles);
```

**Objectif**: Apprendre à manipuler le DOM en y ajoutant des éléments.

#### Exercice 1.6: Implémenter MutationObserver
Ajoutez un MutationObserver pour détecter les changements de page dans GitHub (qui utilise AJAX).

```javascript
function initObserver() {
  // Créer une instance de MutationObserver
  const observer = new MutationObserver((mutations) => {
    // Vérifier si de nouveaux éléments ont été ajoutés à la page
    for (const mutation of mutations) {
      if (mutation.addedNodes.length > 0) {
        // De nouveaux nœuds ont été ajoutés, vérifier s'il faut ajouter des icônes
        addIconsToFiles();
      }
    }
  });
  
  // Commencer à observer le document
  observer.observe(document.body, {
    childList: true, // Observer les ajouts/suppressions d'enfants directs
    subtree: true    // Observer tous les descendants
  });
  
  console.log("MutationObserver activé pour GitHub File Icons");
}

// Initialiser au chargement de la page
window.addEventListener('load', () => {
  addIconsToFiles();  // Ajouter les icônes initiales
  initObserver();     // Démarrer l'observation des changements
});
```

**Objectif**: Apprendre à utiliser MutationObserver pour réagir aux changements dynamiques de la page.

#### Exercice 1.7: Utiliser de vraies icônes d'image
Remplacez les emojis par de vraies icônes d'image et mettez à jour le manifest.json pour y accéder.

```json
// manifest.json mis à jour
{
  "manifest_version": 3,
  "name": "GitHub File Icons",
  "version": "1.0",
  "description": "Ajoute des icônes aux fichiers sur GitHub",
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

```javascript
// Mise à jour du dictionnaire dans content.js
const fileIcons = {
  // Utilisez maintenant des chemins vers les vraies icônes
  'js': 'icons/js.png',
  'css': 'icons/css.png',
  'html': 'icons/html.png',
  // etc...
};

// Modification de la fonction addIconsToFiles
function addIconsToFiles() {
  const fileElements = document.querySelectorAll('.js-navigation-item');
  
  fileElements.forEach(file => {
    const nameElement = file.querySelector('.js-navigation-open');
    if (nameElement) {
      const fileName = nameElement.textContent;
      const extension = getFileExtension(fileName);
      
      // Vérifier si nous avons une icône pour cette extension
      if (fileIcons[extension] && !nameElement.querySelector('.file-icon')) {
        // Créer l'élément d'image pour l'icône
        const iconElement = document.createElement('img');
        iconElement.className = 'file-icon';
        iconElement.src = chrome.runtime.getURL(fileIcons[extension]);
        iconElement.style.width = '16px';
        iconElement.style.height = '16px';
        iconElement.style.marginRight = '5px';
        iconElement.style.verticalAlign = 'middle';
        
        // Ajouter l'icône avant le nom du fichier
        nameElement.prepend(iconElement);
      }
    }
  });
}
```

**Objectif**: Apprendre à accéder aux ressources de l'extension et à utiliser chrome.runtime.getURL().

### Projet 2: GitHub Background Themes - Progression

#### Exercice 2.1: Créer l'interface popup de base
Commencez par créer le manifest.json et une popup simple qui affiche un message.

```json
// manifest.json
{
  "manifest_version": 3,
  "name": "GitHub Themes",
  "version": "1.0",
  "description": "Personnalise l'arrière-plan de GitHub",
  "permissions": ["storage"],
  "host_permissions": ["https://github.com/*"],
  "action": {
    "default_popup": "popup.html"
  },
  "content_scripts": [
    {
      "matches": ["https://github.com/*"],
      "js": ["content.js"]
    }
  ]
}
```

```html
<!-- popup.html -->
<!DOCTYPE html>
<html>
<head>
  <style>
    body {
      width: 200px;
      padding: 10px;
      font-family: Arial, sans-serif;
    }
  </style>
</head>
<body>
  <h3>GitHub Themes</h3>
  <p>Cette extension vous permet de personnaliser l'apparence de GitHub.</p>
  
  <script src="popup.js"></script>
</body>
</html>
```

```javascript
// popup.js
console.log("Popup GitHub Themes activée!");
```

```javascript
// content.js
console.log("Extension GitHub Themes chargée sur cette page GitHub!");
```

**Objectif**: Créer la structure de base de l'extension avec popup.

#### Exercice 2.2: Ajouter un sélecteur de couleur
Ajoutez un sélecteur de couleur à la popup et affichez la couleur sélectionnée.

```html
<!-- popup.html mis à jour -->
<!DOCTYPE html>
<html>
<head>
  <style>
    body {
      width: 200px;
      padding: 10px;
      font-family: Arial, sans-serif;
    }
    .color-section {
      margin-top: 10px;
    }
    #colorPicker {
      width: 100%;
      height: 30px;
    }
    .color-preview {
      margin-top: 5px;
      text-align: center;
    }
  </style>
</head>
<body>
  <h3>GitHub Themes</h3>
  
  <div class="color-section">
    <label for="colorPicker">Couleur d'arrière-plan:</label>
    <input type="color" id="colorPicker" value="#f0f0f0">
    
    <div class="color-preview">
      <span id="colorValue">#f0f0f0</span>
    </div>
  </div>
  
  <script src="popup.js"></script>
</body>
</html>
```

```javascript
// popup.js mis à jour
document.addEventListener('DOMContentLoaded', function() {
  const colorPicker = document.getElementById('colorPicker');
  const colorValue = document.getElementById('colorValue');
  
  // Afficher la couleur actuelle
  colorPicker.addEventListener('input', function() {
    const selectedColor = colorPicker.value;
    colorValue.textContent = selectedColor;
    colorValue.style.backgroundColor = selectedColor;
    colorValue.style.color = isColorDark(selectedColor) ? 'white' : 'black';
  });
  
  // Fonction pour déterminer si une couleur est sombre
  function isColorDark(hexColor) {
    // Convertir la couleur hexadécimale en RGB
    const r = parseInt(hexColor.substr(1, 2), 16);
    const g = parseInt(hexColor.substr(3, 2), 16);
    const b = parseInt(hexColor.substr(5, 2), 16);
    
    // Calculer la luminosité approximative
    const brightness = (r * 299 + g * 587 + b * 114) / 1000;
    
    // Retourner true si la couleur est sombre (luminosité < 128)
    return brightness < 128;
  }
});
```

**Objectif**: Apprendre à créer une interface utilisateur interactive dans la popup.

#### Exercice 2.3: Ajouter un bouton d'application et communiquer avec content.js
Ajoutez un bouton pour appliquer la couleur et envoyez-la au content script.

```html
<!-- Ajouter à popup.html -->
<button id="applyButton" style="margin-top: 10px; width: 100%;">Appliquer</button>
```

```javascript
document.addEventListener('DOMContentLoaded', function() {
  const colorPicker = document.getElementById('colorPicker');
  const colorValue = document.getElementById('colorValue');
  const applyButton = document.getElementById('applyButton');
  
  // Afficher la couleur actuelle
  colorPicker.addEventListener('input', function() {
    const selectedColor = colorPicker.value;
    colorValue.textContent = selectedColor;
    colorValue.style.backgroundColor = selectedColor;
    colorValue.style.color = isColorDark(selectedColor) ? 'white' : 'black';
  });
  
  // Appliquer la couleur
  applyButton.addEventListener('click', function() {
    const selectedColor = colorPicker.value;
    
    // Envoyer un message au content script
    chrome.tabs.query({active: true, currentWindow: true}, function(tabs) {
      chrome.tabs.sendMessage(tabs[0].id, {
        action: 'changeColor',
        color: selectedColor
      });
    });
    
    // Feedback visuel que la couleur a été appliquée
    applyButton.textContent = 'Appliqué!';
    setTimeout(() => {
      applyButton.textContent = 'Appliquer';
    }, 1000);
  });
  
  // Fonction pour déterminer si une couleur est sombre (inchangée)
  function isColorDark(hexColor) {
  }
});
```

```javascript
// Écouter les messages de la popup
chrome.runtime.onMessage.addListener((message, sender, sendResponse) => {
  console.log("Message reçu:", message);
  
  if (message.action === 'changeColor') {
    // Appliquer la couleur d'arrière-plan
    document.body.style.backgroundColor = message.color;
    
    console.log(`Couleur d'arrière-plan changée en ${message.color}`);
  }
});
```

**Objectif**: Apprendre à établir une communication entre popup.js et content.js.

#### Exercice 2.4: Sauvegarder les préférences avec chrome.storage
Sauvegardez la couleur choisie dans le stockage local et chargez-la au démarrage.

```javascript
document.addEventListener('DOMContentLoaded', function() {
  const colorPicker = document.getElementById('colorPicker');
  const colorValue = document.getElementById('colorValue');
  const applyButton = document.getElementById('applyButton');
  
  // Charger la couleur sauvegardée précédemment
  chrome.storage.local.get(['backgroundColor'], function(result) {
    if (result.backgroundColor) {
      colorPicker.value = result.backgroundColor;
      colorValue.textContent = result.backgroundColor;
      colorValue.style.backgroundColor = result.backgroundColor;
      colorValue.style.color = isColorDark(result.backgroundColor) ? 'white' : 'black';
    }
  });
  
  // Mise à jour de l'affichage (inchangé)
  colorPicker.addEventListener('input', function() {
  });
  
  // Appliquer et sauvegarder la couleur
  applyButton.addEventListener('click', function() {
    const selectedColor = colorPicker.value;
    
    // Sauvegarder dans le stockage local
    chrome.storage.local.set({backgroundColor: selectedColor}, function() {
      console.log('Couleur sauvegardée:', selectedColor);
      
      // Envoyer un message au content script
      chrome.tabs.query({active: true, currentWindow: true}, function(tabs) {
        chrome.tabs.sendMessage(tabs[0].id, {
          action: 'changeColor',
          color: selectedColor
        });
      });
      
      // Feedback visuel
      applyButton.textContent = 'Appliqué!';
      setTimeout(() => {
        applyButton.textContent = 'Appliquer';
      }, 1000);
    });
  });
  
  // Fonction pour déterminer si une couleur est sombre (inchangée)
  function isColorDark(hexColor) {
  }
});
```

```javascript
// Charger et appliquer la couleur sauvegardée au chargement de la page
chrome.storage.local.get(['backgroundColor'], function(result) {
  if (result.backgroundColor) {
    document.body.style.backgroundColor = result.backgroundColor;
    console.log(`Couleur chargée du stockage: ${result.backgroundColor}`);
  }
});

// Écouter les message
chrome.runtime.onMessage.addListener((message, sender, sendResponse) => {
});
```

**Objectif**: Apprendre à utiliser chrome.storage pour sauvegarder les préférences utilisateur.

#### Exercice 2.5: Ajouter l'option d'image d'arrière-plan
Ajoutez une option pour télécharger et utiliser une image comme arrière-plan.

```html
<!-- Ajouter à popup.html -->
<div class="option" style="margin-top: 15px;">
  <label><input type="radio" name="themeType" value="color" checked> Couleur</label>
  <br>
  <label><input type="radio" name="themeType" value="image"> Image</label>
  
  <div id="imageSection" style="margin-top: 10px; display: none;">
    <input type="file" id="imageUpload" accept="image/*">
    <div style="margin-top: 5px;">
      <img id="imagePreview" style="max-width: 100%; max-height: 100px; display: none;" />
    </div>
  </div>
</div>
```

```javascript
// popup.js mis à jour pour gérer les images
document.addEventListener('DOMContentLoaded', function() {
  const colorPicker = document.getElementById('colorPicker');
  const colorValue = document.getElementById('colorValue');
  const applyButton = document.getElementById('applyButton');
  const themeTypeRadios = document.querySelectorAll('input[name="themeType"]');
  const imageSection = document.getElementById('imageSection');
  const imageUpload = document.getElementById('imageUpload');
  const imagePreview = document.getElementById('imagePreview');
  
  // Afficher/masquer les sections en fonction du type de thème sélectionné
  themeTypeRadios.forEach(radio => {
    radio.addEventListener('change', function() {
      if (this.value === 'color') {
        colorPicker.parentElement.style.display = 'block';
        imageSection.style.display = 'none';
      } else if (this.value === 'image') {
        colorPicker.parentElement.style.display = 'none';
        imageSection.style.display = 'block';
      }
    });
  });
  
  // Prévisualiser l'image téléchargée
  imageUpload.addEventListener('change', function() {
    const file = imageUpload.files[0];
    if (file) {
      const reader = new FileReader();
      reader.onload = function(e) {
        imagePreview.src = e.target.result;
        imagePreview.style.display = 'block';
      };
      reader.readAsDataURL(file);
    }
  });
  
  // Charger les paramètres sauvegardés
  chrome.storage.local.get(['themeType', 'backgroundColor', 'backgroundImage'], function(result) {
    if (result.themeType) {
      // Sélectionner le bon type de thème
      document.querySelector(`input[value="${result.themeType}"]`).checked = true;
      
      // Afficher la section appropriée
      if (result.themeType === 'color') {
        colorPicker.parentElement.style.display = 'block';
        imageSection.style.display = 'none';
        
        // Restaurer la couleur
        if (result.backgroundColor) {
          colorPicker.value = result.backgroundColor;
          colorValue.textContent = result.backgroundColor;
          colorValue.style.backgroundColor = result.backgroundColor;
          colorValue.style.color = isColorDark(result.backgroundColor) ? 'white' : 'black';
        }
      } else if (result.themeType === 'image') {
        colorPicker.parentElement.style.display = 'none';
        imageSection.style.display = 'block';
        
        // Restaurer l'image
        if (result.backgroundImage) {
          imagePreview.src = result.backgroundImage;
          imagePreview.style.display = 'block';
        }
      }
    }
  });
  
  // Reste du code pour colorPicker.addEventListener('input') (inchangé)
  
  // Appliquer et sauvegarder le thème
  applyButton.addEventListener('click', function() {
    // Déterminer le type de thème sélectionné
    const selectedType = document.querySelector('input[name="themeType"]:checked').value;
    
    // Préparer les données à sauvegarder
    const themeData = {
      themeType: selectedType
    };
    
    if (selectedType === 'color') {
      themeData.backgroundColor = colorPicker.value;
    } else if (selectedType === 'image' && imagePreview.src) {
      themeData.backgroundImage = imagePreview.src;
    }
    
    // Sauvegarder dans le stockage local
    chrome.storage.local.set(themeData, function() {
      console.log('Thème sauvegardé:', themeData);
      
      // Envoyer un message au content script
      chrome.tabs.query({active: true, currentWindow: true}, function(tabs) {
        chrome.tabs.sendMessage(tabs[0].id, {
          action: 'applyTheme',
          theme: themeData
        });
      });
      
      // Feedback visuel
      applyButton.textContent = 'Appliqué!';
      setTimeout(() => {
        applyButton.textContent = 'Appliquer';
      }, 1000);
    });
  });
  
  // Fonction isColorDark (inchangée)
});
```

```javascript
// content.js mis à jour pour gérer différents types de thèmes
// Charger et appliquer le thème sauvegardé au chargement de la page
chrome.storage.local.get(['themeType', 'backgroundColor', 'backgroundImage'], function(result) {
  if (result.themeType) {
    applyTheme(result);
  }
});

// Écouter les messages de la popup
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
  if (theme.themeType === 'color' && theme.backgroundColor) {
    document.body.style.backgroundColor = theme.backgroundColor;
    console.log(`Couleur d'arrière-plan appliquée: ${theme.backgroundColor}`);
  } 
  else if (theme.themeType === 'image' && theme.backgroundImage) {
    document.body.style.backgroundImage = `url(${theme.backgroundImage})`;
    document.body.style.backgroundSize = 'cover';
    document.body.style.backgroundAttachment = 'fixed';
    console.log('Image d\'arrière-plan appliquée');
  }
}
```

**Objectif**: Apprendre à manipuler différents types de données (images) et à créer une interface utilisateur plus complexe.

#### Exercice 2.6: Ajouter un thème dynamique avec animation CSS
Ajoutez une option pour un thème avec dégradé animé et gérez les styles CSS injectés.

```html
<!-- Ajouter à popup.html dans la section des options de thème -->
<label><input type="radio" name="themeType" value="dynamic"> Dégradé dynamique</label>
```

```javascript
// Mise à jour de la fonction applyTheme dans content.js
function applyTheme(theme) {
  // Réinitialiser d'abord
  document.body.style.backgroundColor = '';
  document.body.style.backgroundImage = '';
  document.body.classList.remove('github-dynamic-theme');
  
  // Supprimer l'ancien style d'animation s'il existe
  const oldStyle = document.getElementById('github-theme-style');
  if (oldStyle) {
    oldStyle.remove();
  }
  
  // Appliquer selon le type
  if (theme.themeType === 'color' && theme.backgroundColor) {
    document.body.style.backgroundColor = theme.backgroundColor;
    console.log(`Couleur d'arrière-plan appliquée: ${theme.backgroundColor}`);
  } 
  else if (theme.themeType === 'image' && theme.backgroundImage) {
    document.body.style.backgroundImage = `url(${theme.backgroundImage})`;
    document.body.style.backgroundSize = 'cover';
    document.body.style.backgroundAttachment = 'fixed';
    console.log('Image d\'arrière-plan appliquée');
  }
  else if (theme.themeType === 'dynamic') {
    // Ajouter la classe pour l'animation
    document.body.classList.add('github-dynamic-theme');
    
    // Créer et injecter le style CSS pour l'animation
    const styleElement = document.createElement('style');
    styleElement.id = 'github-theme-style';
    styleElement.textContent = `
      .github-dynamic-theme {
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
    
    document.head.appendChild(styleElement);
    console.log('Thème dynamique appliqué');
  }
}
```

**Objectif**: Apprendre à injecter et manipuler des styles CSS dynamiquement, y compris des animations.

#### Exercice 2.7: Améliorer la lisibilité du texte
Ajoutez une fonction pour ajuster automatiquement la couleur du texte en fonction de l'arrière-plan.

```javascript
// Ajouter à content.js
function applyTheme(theme) {
  // Code précédent inchangé...
  
  // À la fin de la fonction, après avoir appliqué le thème
  adjustTextContrast(theme.themeType);
}

// Fonction pour ajuster la lisibilité du texte
function adjustTextContrast(themeType) {
  // Supprimer les styles précédents
  const oldStyle = document.getElementById('github-text-contrast');
  if (oldStyle) {
    oldStyle.remove();
  }
  
  // Créer un nouveau style pour améliorer la lisibilité
  const styleElement = document.createElement('style');
  styleElement.id = 'github-text-contrast';
  
  // Différentes règles selon le type de thème
  if (themeType === 'dynamic' || theme

## 📚 Ressources pour aller plus loin

- [Documentation officielle des extensions Chrome](https://developer.chrome.com/docs/extensions/)
- [Chrome Extension Samples](https://github.com/GoogleChrome/chrome-extensions-samples)
- [Mozilla MDN - Extensions de navigateur](https://developer.mozilla.org/fr/docs/Mozilla/Add-ons/WebExtensions)

---

Créé avec ❤️ pour l'apprentissage du développement d'extensions Chrome
