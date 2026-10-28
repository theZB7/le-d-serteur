# « Dis Siri, Jarvis » : relier Jarvis à ton iPhone, iPad et Mac

Une application web (comme Jarvis dans Safari) ne peut pas lire le contenu de ton téléphone : Apple l'interdit. Le seul moyen autorisé est l'app **Raccourcis** (installée d'office). Un raccourci peut lire ton agenda, tes rappels, ta position, ta batterie, le presse-papiers, puis tout envoyer à Claude, qui répond à voix haute.

Tu le crées **une fois sur l'iPhone** : grâce à iCloud, il apparaît aussi sur ton iPad et ton Mac.

Il te faut ta **clé API Anthropic** (la même que dans Jarvis).

## Créer le raccourci (10 minutes)

Ouvre **Raccourcis** → **+** en haut à droite. Renomme-le **Jarvis** (touche le nom en haut). Puis ajoute ces actions dans l'ordre, avec la barre de recherche en bas (« Rechercher des actions ») :

1. **Dicter le texte** — langue : Français. *(Ce que tu dis à Jarvis.)*
2. **Rechercher des évènements de calendrier** — touche « Ajouter un filtre » → *Date de début* « est aujourd'hui ». Limite : 15.
3. **Rechercher des rappels** — filtre : *Est terminé* « est faux ». Limite : 20.
4. **Obtenir le niveau de la batterie**.
5. **Obtenir la position actuelle**.
6. **Date actuelle**.
7. **Texte** — écris le modèle ci-dessous, et à chaque mot en *italique* touche « Variables » pour insérer le résultat de l'action correspondante :

   > Tu es JARVIS, l'assistant personnel de l'utilisateur, en français, bref et précis (réponse lue à voix haute : pas de tableaux ni de listes longues). Utilise ces informations de son appareil si elles servent la question.
   > Date : *Date actuelle*
   > Agenda du jour : *Évènements de calendrier*
   > Rappels en cours : *Rappels*
   > Batterie : *Niveau de la batterie* %
   > Position : *Position actuelle*
   > Question : *Texte dicté*

8. **Obtenir le contenu de l'URL**
   - URL : `https://api.anthropic.com/v1/messages`
   - Touche « Afficher plus » → Méthode : **POST**
   - En-têtes (Ajouter un nouvel en-tête, 3 fois) :
     - `x-api-key` → ta clé API
     - `anthropic-version` → `2023-06-01`
     - `content-type` → `application/json`
   - Corps de la requête : **JSON**, puis ajoute :
     - `model` (Texte) → `claude-opus-5-5`
     - `max_tokens` (Nombre) → `2000`
     - `messages` (Tableau) → un élément de type **Dictionnaire** contenant :
       - `role` (Texte) → `user`
       - `content` (Texte) → la variable *Texte* (étape 7)
     - `tools` (Tableau) → un élément **Dictionnaire** contenant :
       - `type` (Texte) → `web_search_20260209`
       - `name` (Texte) → `web_search`

     *(Ce dernier bloc permet à Jarvis de chercher sur internet : actualité, prix, horaires, météo.)*
9. **Obtenir la valeur du dictionnaire** — Obtenir la *Valeur* pour la clé `content` dans *Contenu de l'URL*.
10. **Répéter avec chaque élément** de *Valeur du dictionnaire*, et à l'intérieur de la boucle :
    - **Obtenir la valeur du dictionnaire** — clé `type` dans *Élément répété*.
    - **Si** *Valeur du dictionnaire* **est** `text` :
      - **Obtenir la valeur du dictionnaire** — clé `text` dans *Élément répété*.
    - **Fin si**
11. *(après « Fin de la répétition »)* **Combiner le texte** — *Résultats de la répétition*, séparateur : Espace.
12. **Énoncer le texte** — *Texte combiné* (choisis une voix française).
13. **Afficher le résultat** — *Texte combiné* (pour pouvoir le relire).

Touche **OK**. La première fois, iOS demande l'accès au calendrier, aux rappels et à la position : accepte.

## L'utiliser

- **Siri** : « Dis Siri, Jarvis », puis pose ta question.
- **Écran d'accueil** : dans Raccourcis, appui long sur Jarvis → Partager → *Sur l'écran d'accueil*.
- **Bouton Action** (iPhone 15 Pro et plus récents) : Réglages → Bouton Action → Raccourci → Jarvis.
- **Mac** : dans la barre des menus, ou « Dis Siri, Jarvis ».

Exemples : « Qu'est-ce que j'ai aujourd'hui ? », « Il me reste combien de batterie, je tiens jusqu'à ce soir ? », « Quel temps fait-il là où je suis ? », « Combien font 18 % de 2 340 ? ».

## Aller plus loin

Tu peux ajouter d'autres actions avant l'étape 7 et les glisser dans le texte : **Obtenir le presse-papiers**, **Rechercher des notes**, **Rechercher des contacts**, **Rechercher des photos**, **Obtenir des échantillons de santé** (pas, sommeil), **Obtenir la météo actuelle**. Et après la réponse, des actions qui agissent : **Ajouter un rappel**, **Ajouter un nouvel évènement**, **Envoyer un message**.

## Limites

- Apple ne laisse **aucun** raccourci ni aucune application lire le contenu des autres apps (WhatsApp, Instagram, Messages reçus, mails d'autres apps…). Seul ce que Raccourcis propose est accessible.
- Chaque question consomme un peu de crédit sur ta clé API Anthropic.
- Ce raccourci ne se souvient pas des conversations précédentes : pour le carnet de notes, utilise l'app Jarvis.
