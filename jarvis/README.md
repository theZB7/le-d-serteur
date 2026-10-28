# Jarvis

Assistant personnel inspiré de l'IA d'Iron Man. Il répond à toutes les questions (calcul, information, explication, détails), et il garde dans un **carnet** tout ce que tu lui demandes de retenir. Il relit ce carnet à chaque question pour mieux comprendre ce que tu cherches.

## Ce qu'il sait faire

- **Répondre à tout** : questions, explications, définitions, conseils, rédaction, traduction.
- **Calculer juste** : les calculs passent par une vraie calculatrice (`+ - * / % ^ !`, `sqrt`, `sin`, `ln`, `log`, `pi`…), donc pas d'erreur de calcul mental.
- **Prendre des notes** : dis « note que… », « retiens que… » ou « rappelle-moi… ». Les notes sont classées par catégorie dans l'onglet **Carnet**, où tu peux aussi chercher, en ajouter ou en supprimer à la main.
- **Parler** : le bouton haut-parleur lit les réponses à voix haute. Le micro permet de dicter, sur les navigateurs qui le permettent (Safari, Chrome).
- **Chercher sur le web** (mode autonome seulement) : actualité, prix, horaires.

## Deux façons de l'utiliser

### 1. Dans l'app Claude (le plus simple)

Ouvre le lien de l'artifact Jarvis dans l'app Claude, sur iPhone, iPad, Mac, Android ou PC. Il utilise ton compte Claude, donc aucune clé n'est nécessaire, et **ton carnet est le même sur tous tes appareils**.

### 2. Comme une vraie application installée

Le dossier `jarvis/` est une application web installable (PWA). Il faut la mettre en ligne une fois, puis l'installer sur chaque appareil.

**Mise en ligne avec GitHub Pages (gratuit)**
1. Sur GitHub, ouvre le dépôt, puis **Settings → Pages**.
2. Dans *Source*, choisis la branche qui contient `jarvis/`, dossier `/ (root)`, puis **Save**.
3. Après une minute, l'app est disponible à `https://<ton-pseudo>.github.io/le-d-serteur/jarvis/`.

**Installation sur chaque appareil**
- **iPhone / iPad** : ouvre l'adresse dans Safari → bouton Partager → **Sur l'écran d'accueil**.
- **Mac** : Safari → menu Fichier → **Ajouter au Dock**. Avec Chrome : icône d'installation dans la barre d'adresse.
- **Android** : Chrome → menu ⋮ → **Installer l'application**.
- **Windows / PC** : Chrome ou Edge → icône d'installation dans la barre d'adresse.

Au premier lancement, ouvre les **Réglages** (roue dentée) et colle ta clé API Anthropic (à créer sur [console.anthropic.com](https://console.anthropic.com)). La clé reste dans l'appareil. Dans ce mode, le carnet est enregistré **sur chaque appareil séparément**.

## Fichiers

| Fichier | Rôle |
|---|---|
| `index.html` | L'application complète (interface, mémoire, calculatrice, connexion à Claude) |
| `manifest.webmanifest` | Nom, couleurs et icônes pour l'installation |
| `sw.js` | Garde l'interface disponible hors ligne |
| `icon.svg`, `icon-*.png` | Icônes |
