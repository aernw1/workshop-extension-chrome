# 🚀 Workshop Extensions Chrome - Mode Interactif

## Bienvenue au Workshop sur la création d'extensions Chrome !

Dans ce workshop de **3h30**, vous allez **créer deux extensions Chrome** de A à Z : une pour ajouter des icônes aux fichiers sur GitHub et une autre pour personnaliser l'arrière-plan de GitHub. À travers des **exercices pratiques progressifs**, vous apprendrez les bases du développement d'extensions Chrome tout en mettant les mains dans le code.

### 🎯 Objectifs
- Comprendre la structure et les composants d'une extension Chrome
- Manipuler le DOM avec des **content scripts**
- Créer une interface utilisateur avec une **popup**
- Stocker des données avec l'API **chrome.storage**
- Déboguer et tester vos extensions

### ⏰ Programme
- **Introduction** (30 min) : Concepts de base et configuration
- **Projet 1 : GitHub File Icons** (1h) : Ajouter des icônes aux fichiers sur GitHub
- **Pause** (15 min)
- **Projet 2 : GitHub Background Themes** (1h15) : Personnaliser l'arrière-plan de GitHub
- **Exploration libre** (30 min) : Ajouter vos propres fonctionnalités

## 📋 Prérequis

- Connaissances de base en **HTML**, **CSS**, et **JavaScript**
- **Google Chrome** installé
- Un éditeur de code (**VS Code** recommandé)
- **Git** (optionnel, pour cloner ce dépôt)

**🛠️ Exercice 0 : Préparer votre environnement**
1. Créez un dossier `chrome-extension-workshop` sur votre ordinateur.
2. Créez deux sous-dossiers : `github-file-icons` pour le Projet 1 et `github-themes` pour le Projet 2.
3. Ouvrez ces dossiers dans votre éditeur de code.
4. Dans Chrome, allez à `chrome://extensions/` et activez le **Mode développeur** (en haut à droite).
5. **Vérifiez** : Vous devriez voir les boutons "Charger l'extension non empaquetée" et "Mettre à jour".

*Objectif* : S'assurer que votre environnement est prêt pour développer et tester des extensions.

---

## 🎓 Comprendre les extensions Chrome

Les **extensions Chrome** sont des mini-programmes qui modifient ou améliorent le navigateur Chrome en utilisant **HTML**, **CSS**, et **JavaScript**. Elles se composent de plusieurs parties clés :

1. **manifest.json** : Fichier de configuration qui définit l'extension
2. **Content scripts** : Scripts injectés dans les pages web
3. **Background scripts** : Scripts qui fonctionnent en arrière-plan
4. **Popup** : Interface affichée lors du clic sur l'icône de l'extension
5. **Options page** : Page pour configurer l'extension (optionnelle)

**🧠 Exercice 1 : Décrypter un manifest.json**
1. Créez un fichier `manifest.json` dans le dossier `github-file-icons` avec le contenu suivant :

```json
{
  "manifest_version": 3,
  "name": "GitHub File Icons",
  "version": "1.0",
  "description": "Ajoute des icônes aux fichiers sur GitHub"
}
