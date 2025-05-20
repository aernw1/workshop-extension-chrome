# 🚀 Workshop Extensions Chrome

## Introduction

Bonjour et bienvenue à ce workshop sur la création d'extensions Chrome ! Aujourd’hui, nous allons développer deux extensions Chrome de A à Z en suivant une approche progressive. Vous apprendrez les bases des extensions Chrome tout en construisant des fonctionnalités concrètes pour GitHub : **GitHub File Icons** et **GitHub Background Themes**.

### Objectifs du workshop :
- Comprendre les concepts fondamentaux des extensions Chrome
- Créer deux extensions complètes, étape par étape
- Apprendre les bonnes pratiques de développement

### Prérequis :
- Connaissances de base en **HTML**, **CSS** et **JavaScript**
- **Google Chrome** installé
- Un éditeur de code (par exemple, **VS Code**)

---

## Partie 1 : Découverte et premiers pas avec les extensions Chrome

### Étape 1.1 : Qu’est-ce qu’une extension Chrome ?
Une extension Chrome est un petit programme qui ajoute ou modifie des fonctionnalités dans le navigateur Chrome. Elles sont construites avec des technologies web standards : **HTML**, **CSS** et **JavaScript**.

#### Action : Vérifions que tout est prêt
1. Ouvrez **Google Chrome**.
2. Allez à `chrome://extensions/` dans la barre d’adresse.
3. Activez le **Mode développeur** (interrupteur en haut à droite).
   - Vous verrez une interface pour gérer les extensions.

### Étape 1.2 : Créer la structure de base d’un projet
Chaque extension a besoin d’un dossier avec des fichiers spécifiques. Commençons par une structure minimale.

#### Action : Créez votre premier dossier
1. Créez un dossier nommé `mon-extension` sur votre ordinateur.
2. À l’intérieur, ajoutez un fichier nommé `manifest.json` avec ce contenu :

```json
{
  "manifest_version": 3,
  "name": "Ma Première Extension",
  "version": "1.0",
  "description": "Une extension simple pour apprendre les bases"
}
```

3. Chargez cette extension dans Chrome :
   - Retournez à `chrome://extensions/`.
   - Cliquez sur **Charger l’extension non empaquetée**.
   - Sélectionnez le dossier `mon-extension`.
   - Votre extension devrait apparaître dans la liste !

#### Vérification
- Si tout fonctionne, vous verrez "Ma Première Extension" dans `chrome://extensions/`. Pas de fonctionnalité pour l’instant, mais c’est un début !

---

## Partie 2 : Projet 1 - GitHub File Icons

Nous allons créer une extension qui ajoute des icônes aux fichiers dans l’explorateur de dépôts GitHub pour une meilleure identification visuelle. Construisons-la étape par étape.

### Étape 2.1 : Configurer le manifest.json
Le fichier `manifest.json` est le cœur de l’extension. Il indique à Chrome où et comment exécuter notre code.

#### Action : Mettez à jour manifest.json
Remplacez le contenu de `manifest.json` par ceci :

```json
{
  "manifest_version": 3,
  "name": "GitHub File Icons",
  "version": "1.0",
  "description": "Ajoute des icônes visuelles pour les types de fichiers dans GitHub",
  "content_scripts": [
    {
      "matches": ["*://github.com/*"],
      "js": ["content.js"]
    }
  ]
}
```

- **`content_scripts`** : Indique que `content.js` sera exécuté sur les pages GitHub.

#### Vérification
- Rechargez l’extension dans `chrome://extensions/` (cliquez sur l’icône ↻).
- L’extension est maintenant prête à interagir avec GitHub.

### Étape 2.2 : Ajouter un content script simple
Les **content scripts** sont des fichiers JavaScript qui s’exécutent dans les pages web pour interagir avec leur contenu.

#### Action : Créez content.js
1. Dans le dossier `mon-extension`, créez un fichier nommé `content.js`.
2. Ajoutez ce code :

```javascript
console.log("Content script chargé sur GitHub !");
```

3. Rechargez l’extension dans `chrome://extensions/` (bouton ↻).
4. Allez sur une page GitHub (par exemple, un dépôt comme `https://github.com/torvalds/linux`).
5. Ouvrez la console (clic droit → **Inspecter** → onglet **Console**).

#### Vérification
- Vous devriez voir "Content script chargé sur GitHub !" dans la console. Cela confirme que le script fonctionne sur GitHub.

### Étape 2.3 : Modifier le DOM
Modifions quelque chose sur la page pour tester notre contrôle sur le DOM.

#### Action : Changez la couleur de fond
Mettez à jour `content.js` avec ceci :

```javascript
console.log("Content script chargé sur GitHub !");
document.body.style.backgroundColor = "lightblue";
```

- Rechargez l’extension et la page GitHub.

#### Vérification
- Le fond de la page GitHub devrait être bleu clair. Si oui, vous pouvez modifier le DOM !

### Étape 2.4 : Ajouter des icônes aux fichiers
Ajoutons des icônes à côté des noms de fichiers dans les dépôts GitHub.

#### Action : Préparez les icônes et le code
1. Créez un sous-dossier `icons` dans `mon-extension`.
2. Téléchargez quelques icônes (par exemple, `js.png`, `css.png`, `py.png`) de 20x20 pixels et placez-les dans `icons/`.
3. Mettez à jour `manifest.json` pour rendre les icônes accessibles :

```json
{
  "manifest_version": 3,
  "name": "GitHub File Icons",
  "version": "1.0",
  "description": "Ajoute des icônes visuelles pour les types de fichiers dans GitHub",
  "content_scripts": [
    {
      "matches": ["*://github.com/*"],
      "js": ["content.js"]
    }
  ],
  "web_accessible_resources": [
    {
      "resources": ["icons/*"],
      "matches": ["https://github.com/*"]
    }
  ]
}
```

4. Remplacez le contenu de `content.js` par :

```javascript
const fileIcons = {
  '.js': 'js.png',
  '.css': 'css.png',
  '.py': 'python.png'
};

function addFileIcons() {
  const fileRows = document.querySelectorAll('a.Link--primary[title][aria-label]');
  fileRows.forEach(element => {
    const fileName = element.textContent.trim();
    const extension = fileName.match(/\.[0-9a-z]+$/i)?.[0]?.toLowerCase();
    if (extension && fileIcons[extension] && !element.querySelector('img')) {
      const icon = document.createElement('img');
      icon.src = chrome.runtime.getURL(`icons/${fileIcons[extension]}`);
      icon.style.width = '20px';
      icon.style.height = '20px';
      icon.style.marginRight = '5px';
      element.prepend(icon);
    }
  });
}

addFileIcons();
```

- Rechargez l’extension et la page GitHub.

#### Vérification
- Dans un dépôt GitHub, les fichiers comme `script.js` ou `style.css` devraient avoir une icône devant leur nom.

### Étape 2.5 : Gérer le contenu dynamique
GitHub charge souvent des fichiers via AJAX. Utilisons **MutationObserver** pour détecter ces changements.

#### Action : Ajoutez MutationObserver
Mettez à jour `content.js` avec :

```javascript
const fileIcons = {
  '.js': 'js.png',
  '.css': 'css.png',
  '.py': 'python.png'
};

function addFileIcons() {
  const fileRows = document.querySelectorAll('a.Link--primary[title][aria-label]');
  fileRows.forEach(element => {
    const fileName = element.textContent.trim();
    const extension = fileName.match(/\.[0-9a-z]+$/i)?.[0]?.toLowerCase();
    if (extension && fileIcons[extension] && !element.querySelector('img')) {
      const icon = document.createElement('img');
      icon.src = chrome.runtime.getURL(`icons/${fileIcons[extension]}`);
      icon.style.width = '20px';
      icon.style.height = '20px';
      icon.style.marginRight = '5px';
      element.prepend(icon);
    }
  });
}

addFileIcons();

const observer = new MutationObserver(() => {
  addFileIcons();
});

observer.observe(document.body, { childList: true, subtree: true });
```

- Rechargez tout et naviguez dans un dépôt.

#### Vérification
- Les icônes devraient apparaître même lorsque vous changez de dossier ou chargez de nouveaux fichiers.

---

## Partie 3 : Projet 2 - GitHub Background Themes

Maintenant, créons une extension qui permet de personnaliser l’arrière-plan de GitHub avec des couleurs ou des dégradés.

### Étape 3.1 : Ajouter un popup
Un **popup** est une interface qui apparaît quand on clique sur l’icône de l’extension.

#### Action : Créez le popup
1. Mettez à jour `manifest.json` :

```json
{
  "manifest_version": 3,
  "name": "GitHub Background Themes",
  "version": "1.0",
  "description": "Personnalise l’arrière-plan de GitHub",
  "action": {
    "default_popup": "popup.html"
  },
  "content_scripts": [
    {
      "matches": ["*://github.com/*"],
      "js": ["content.js"]
    }
  ],
  "permissions": ["activeTab"]
}
```

2. Créez `popup.html` dans `mon-extension` :

```html
<!DOCTYPE html>
<html>
<head>
  <style>
    body { width: 200px; padding: 10px; }
    button { width: 100%; margin-top: 10px; }
  </style>
</head>
<body>
  <h3>Changer le fond</h3>
  <input type="color" id="colorPicker">
  <script src="popup.js"></script>
</body>
</html>
```

3. Créez `popup.js` :

```javascript
document.getElementById('colorPicker').addEventListener('change', function(e) {
  const color = e.target.value;
  chrome.tabs.query({active: true, currentWindow: true}, function(tabs) {
    chrome.tabs.sendMessage(tabs[0].id, {action: 'setColor', color: color});
  });
});
```

- Rechargez l’extension.

#### Vérification
- Cliquez sur l’icône de l’extension dans la barre d’outils Chrome. Un popup avec un sélecteur de couleur devrait apparaître.

### Étape 3.2 : Appliquer la couleur
Relions le popup au content script pour changer l’arrière-plan.

#### Action : Mettez à jour content.js
Remplacez `content.js` par :

```javascript
chrome.runtime.onMessage.addListener((message) => {
  if (message.action === 'setColor') {
    document.body.style.backgroundColor = message.color;
  }
});
```

- Rechargez tout, ouvrez le popup, et choisissez une couleur.

#### Vérification
- La couleur de fond de GitHub change selon votre sélection.

### Étape 3.3 : Ajouter un dégradé animé
Ajoutons une option pour un fond animé.

#### Action : Étendez le popup et le content script
1. Mettez à jour `popup.html` :

```html
<!DOCTYPE html>
<html>
<head>
  <style>
    body { width: 200px; padding: 10px; }
    button { width: 100%; margin-top: 10px; }
  </style>
</head>
<body>
  <h3>Changer le fond</h3>
  <input type="color" id="colorPicker">
  <button id="dynamicButton">Dégradé animé</button>
  <script src="popup.js"></script>
</body>
</html>
```

2. Mettez à jour `popup.js` :

```javascript
document.getElementById('colorPicker').addEventListener('change', function(e) {
  const color = e.target.value;
  chrome.tabs.query({active: true, currentWindow: true}, function(tabs) {
    chrome.tabs.sendMessage(tabs[0].id, {action: 'setColor', color: color});
  });
});

document.getElementById('dynamicButton').addEventListener('click', function() {
  chrome.tabs.query({active: true, currentWindow: true}, function(tabs) {
    chrome.tabs.sendMessage(tabs[0].id, {action: 'setDynamicBackground'});
  });
});
```

3. Mettez à jour `content.js` :

```javascript
function setBackground(type, value) {
  document.body.style.backgroundImage = 'none';
  document.body.style.backgroundColor = 'transparent';
  document.body.style.animation = 'none';

  if (type === 'color') {
    document.body.style.backgroundColor = value;
  } else if (type === 'dynamic') {
    document.body.style.background = 'linear-gradient(-45deg, #ee7752, #e73c7e, #23a6d5, #23d5ab)';
    document.body.style.backgroundSize = '400% 400%';
    document.body.style.animation = 'gradient 15s ease infinite';
  }
}

const style = document.createElement('style');
style.textContent = `
  @keyframes gradient {
    0% { background-position: 0% 50%; }
    50% { background-position: 100% 50%; }
    100% { background-position: 0% 50%; }
  }
`;
document.head.appendChild(style);

chrome.runtime.onMessage.addListener((message) => {
  if (message.action === 'setColor') {
    setBackground('color', message.color);
  } else if (message.action === 'setDynamicBackground') {
    setBackground('dynamic');
  }
});
```

- Rechargez tout et testez le bouton.

#### Vérification
- Cliquez sur "Dégradé animé". Le fond de GitHub devrait devenir un dégradé animé.

### Étape 3.4 : Sauvegarder les préférences
Utilisons `chrome.storage` pour que les choix persistent.

#### Action : Ajoutez le stockage
1. Mettez à jour `manifest.json` :

```json
{
  "manifest_version": 3,
  "name": "GitHub Background Themes",
  "version": "1.0",
  "description": "Personnalise l’arrière-plan de GitHub",
  "action": {
    "default_popup": "popup.html"
  },
  "content_scripts": [
    {
      "matches": ["*://github.com/*"],
      "js": ["content.js"]
    }
  ],
  "permissions": ["activeTab", "storage"]
}
```

2. Mettez à jour `content.js` :

```javascript
function setBackground(type, value) {
  document.body.style.backgroundImage = 'none';
  document.body.style.backgroundColor = 'transparent';
  document.body.style.animation = 'none';

  if (type === 'color') {
    document.body.style.backgroundColor = value;
  } else if (type === 'dynamic') {
    document.body.style.background = 'linear-gradient(-45deg, #ee7752, #e73c7e, #23a6d5, #23d5ab)';
    document.body.style.backgroundSize = '400% 400%';
    document.body.style.animation = 'gradient 15s ease infinite';
  }
}

const style = document.createElement('style');
style.textContent = `
  @keyframes gradient {
    0% { background-position: 0% 50%; }
    50% { background-position: 100% 50%; }
    100% { background-position: 0% 50%; }
  }
`;
document.head.appendChild(style);

chrome.storage.local.get(['savedType', 'savedColor'], (result) => {
  if (result.savedType === 'color') {
    setBackground('color', result.savedColor);
  } else if (result.savedType === 'dynamic') {
    setBackground('dynamic');
  }
});

chrome.runtime.onMessage.addListener((message) => {
  if (message.action === 'setColor') {
    setBackground('color', message.color);
    chrome.storage.local.set({ savedType: 'color', savedColor: message.color });
  } else if (message.action === 'setDynamicBackground') {
    setBackground('dynamic');
    chrome.storage.local.set({ savedType: 'dynamic' });
  }
});
```

- Rechargez tout, changez le fond, puis rechargez la page GitHub.

#### Vérification
- Le fond reste le même après un rechargement.

---

## Partie 4 : Bonnes pratiques et débogage

### Étape 4.1 : Tester et déboguer
#### Action : Apprenez à déboguer
- **Console** : Ouvrez la console (F12) sur GitHub pour voir les logs ou erreurs.
- **Popup** : Clic droit sur l’icône de l’extension → **Inspecter** pour déboguer le popup.
- **Rechargement** : Après chaque modification, rechargez l’extension dans `chrome://extensions/` (↻).

### Étape 4.2 : Appliquer des bonnes pratiques
- **Code clair** : Utilisez des noms explicites (ex. `addFileIcons`).
- **Erreurs** : Ajoutez des `console.log` pour suivre l’exécution.
- **Performance** : Évitez les boucles inutiles dans `addFileIcons`.

---

## Partie 5 : Créez votre propre extension !

### Étape 5.1 : Personnalisez vos projets
- Ajoutez plus d’icônes à "GitHub File Icons".
- Incluez une option d’image dans "GitHub Background Themes".

### Ressources
- [Documentation Chrome Extensions](https://developer.chrome.com/docs/extensions/)
- [Exemples d’extensions](https://github.com/GoogleChrome/chrome-extensions-samples)

---

Merci d’avoir participé ! Continuez à expérimenter et partagez vos créations.

**Workshop réalisé par :**
- Dryss Margueritte (dryss.margueritte@epitech.eu)
- Erwan Seytor (erwan.seytor@epitech.eu)
