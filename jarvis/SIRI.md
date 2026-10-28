# Jarvis dans ton iPhone, ton iPad et ton Mac

Ce guide crée un assistant **Jarvis** intégré à l'appareil, sans ouvrir d'application :

- tu l'appelles par **« Dis Siri, Jarvis »**, le **bouton Action**, un **double tapotement au dos** de l'iPhone, ou un raccourci clavier sur Mac ;
- tu lui **parles ou tu lui écris** ;
- il **se souvient** de ce que tu lui demandes de retenir (dans une note Apple, synchronisée sur tous tes appareils) ;
- il connaît ton **agenda**, tes **rappels**, ta **batterie**, ta **position**, et **cherche sur internet** ;
- il peut **agir** : créer un rappel, appeler, envoyer un SMS ou un WhatsApp, lancer un itinéraire, de la musique, ouvrir une application ou une page.

Tout se fait avec l'app **Raccourcis** d'Apple, déjà installée. Tu le crées **une fois sur l'iPhone** ; avec iCloud, il apparaît aussi sur ton iPad et ton Mac.

Il te faut ta **clé API Anthropic** (la même que dans l'app Jarvis).

---

## Étape 0 : la note mémoire

Ouvre l'app **Notes**, crée une note dans un dossier **iCloud**, et écris sur la première ligne : `Mémoire Jarvis`. Laisse-la ainsi. C'est là que Jarvis écrira ce qu'il doit retenir. Tu pourras la relire ou la corriger quand tu veux.

## Étape 1 : créer le raccourci

Ouvre **Raccourcis** → **+** en haut à droite → touche le nom en haut et appelle-le **Jarvis**. Ajoute les actions suivantes dans l'ordre, avec la barre « Rechercher des actions » en bas. Les mots en *italique* sont des **variables** : touche le champ, puis « Variables » ou la suggestion au-dessus du clavier.

### A. Ta demande

1. **Demander une entrée** — Question : `Oui ?` — Type : Texte.
   *Avec Siri, il te pose la question à voix haute et t'écoute. Depuis l'écran d'accueil, un champ s'ouvre : écris, ou touche le micro du clavier pour dicter.*

### B. Ce qu'il sait de toi et de ton appareil

2. **Rechercher des notes** — Ajouter un filtre : *Nom* « est » `Mémoire Jarvis`. Limite : 1.
3. **Rechercher des évènements de calendrier** — filtre : *Date de début* « est aujourd'hui ». Limite : 15.
4. **Rechercher des rappels** — filtre : *Est terminé* « est faux ». Limite : 20.
5. **Obtenir le niveau de la batterie**.
6. **Obtenir la position actuelle**.
7. **Date actuelle** — touche *Date* → Format : Long, Heure : Courte.
8. **Texte** — écris ce qui suit en insérant les variables :

   > Date : *Date actuelle*
   > Ce que tu dois savoir sur moi (ta mémoire) : *Notes*
   > Mon agenda du jour : *Évènements de calendrier*
   > Mes rappels en cours : *Rappels*
   > Batterie : *Niveau de la batterie* %
   > Position : *Position actuelle*
   >
   > Ma demande : *Entrée fournie*

### C. Les consignes de Jarvis

9. **Texte** — copie-colle exactement ce bloc :

```
Tu es JARVIS, l'assistant personnel de l'utilisateur, intégré à son iPhone, iPad et Mac. Calme, précis, une pointe d'humour. Réponds en français, en phrases courtes : ta réponse est lue à voix haute (pas de tableaux, pas de listes longues, pas de markdown).

Tu reçois sa mémoire, son agenda, ses rappels, sa batterie et sa position : sers-t'en quand c'est utile. Pour l'actualité, la météo, les prix ou les horaires, fais une recherche web. Pour un calcul, vérifie-le.

Tu peux agir en ajoutant à la FIN de ta réponse une ou plusieurs lignes, chacune seule sur sa ligne, exactement sous cette forme :
MÉMO: <fait à retenir> — quand il dit « retiens », « note », « souviens-toi », ou donne une info durable sur lui (prénom, goûts, projets, proches, habitudes).
RAPPEL: <texte du rappel avec la date et l'heure>
OUVRIR: <adresse> — pour agir dans une application ou sur un site. Exemples :
  appel : tel:0612345678
  SMS : sms:0612345678&body=texte%20encodé
  WhatsApp : whatsapp://send?phone=33612345678&text=texte%20encodé
  e-mail : mailto:adresse@exemple.fr?subject=...&body=...
  itinéraire : maps://?daddr=adresse%20encodée (ou comgooglemaps://?daddr=... , waze://?q=...)
  musique : spotify:search:artiste%20titre (ou music://)
  vidéo : youtube://results?search_query=...
  un autre raccourci : shortcuts://run-shortcut?name=Nom
  ouvrir une app : son adresse (instagram://, snapchat://, tiktok://, fb://, photos-redirect://, mobilenotes://, calshow://, x-apple-reminderkit://, prefs:) ou un site https://
Encode les espaces et accents des adresses (%20 etc.). N'invente jamais un numéro : s'il manque, demande-le ou cherche-le dans sa mémoire. N'écris une ligne OUVRIR que s'il demande clairement l'action.
```

### D. Demander la réponse à Claude

10. **Obtenir le contenu de l'URL**
    - URL : `https://api.anthropic.com/v1/messages`
    - « Afficher plus » → Méthode : **POST**
    - En-têtes (3) :
      - `x-api-key` → ta clé API
      - `anthropic-version` → `2023-06-01`
      - `content-type` → `application/json`
    - Corps de la requête : **JSON** :
      - `model` (Texte) → `claude-opus-5-5`
      - `max_tokens` (Nombre) → `3000`
      - `system` (Texte) → *Texte* de l'étape 9
      - `messages` (Tableau) → 1 élément **Dictionnaire** :
        - `role` (Texte) → `user`
        - `content` (Texte) → *Texte* de l'étape 8
      - `tools` (Tableau) → 1 élément **Dictionnaire** :
        - `type` (Texte) → `web_search_20260209`
        - `name` (Texte) → `web_search`
11. **Obtenir la valeur du dictionnaire** — clé `content` dans *Contenu de l'URL*.
12. **Répéter avec chaque élément** de *Valeur du dictionnaire* :
    - **Obtenir la valeur du dictionnaire** — clé `type` dans *Élément répété*.
    - **Si** *Valeur du dictionnaire* **est** `text` :
      - **Obtenir la valeur du dictionnaire** — clé `text` dans *Élément répété*.
    - **Fin si**
13. *(après « Fin de la répétition »)* **Combiner le texte** — *Résultats de la répétition*, séparateur : Espace.

### E. Se souvenir et agir

14. **Faire correspondre le texte** — Motif : `MÉMO:\s*(.+)` — dans *Texte combiné*.
15. **Répéter avec chaque élément** de *Correspondances* :
    - **Obtenir le groupe du texte correspondant** — Groupe à l'index 1.
    - **Ajouter à la note** — *Groupe* → note : *Notes* (étape 2).
    - **Fin de la répétition**
16. **Faire correspondre le texte** — Motif : `RAPPEL:\s*(.+)` — dans *Texte combiné*.
17. **Répéter avec chaque élément** de *Correspondances* :
    - **Obtenir le groupe du texte correspondant** — index 1.
    - **Obtenir les dates de l'entrée** — *Groupe*.
    - **Ajouter un rappel** — titre : *Groupe* ; touche « Afficher plus » → Alerte : *Dates*.
    - **Fin de la répétition**
18. **Faire correspondre le texte** — Motif : `OUVRIR:\s*(\S+)` — dans *Texte combiné*.
19. **Répéter avec chaque élément** de *Correspondances* :
    - **Obtenir le groupe du texte correspondant** — index 1.
    - **Ouvrir les URL** — *Groupe*.
    - **Fin de la répétition**

### F. Répondre

20. **Remplacer le texte** — Rechercher : `(?m)^\s*(MÉMO|RAPPEL|OUVRIR):.*$` — Remplacer par : *(vide)* — dans *Texte combiné* — touche « Afficher plus » et active **Expression régulière**.
21. **Afficher le résultat** — *Texte modifié*.
    *Avec Siri, la réponse est lue à voix haute ; sinon elle s'affiche à l'écran. Pour qu'il parle aussi hors Siri, ajoute avant **Énoncer le texte**.*

Touche **OK**. Au premier lancement, accepte les accès (Notes, Calendrier, Rappels, Position, api.anthropic.com).

---

## Étape 2 : l'appeler sans ouvrir d'application

- **Siri (iPhone, iPad, Mac)** : « Dis Siri, Jarvis ».
- **Bouton Action** (iPhone 15 Pro et plus) : Réglages → Bouton Action → Raccourci → Jarvis.
- **Toucher le dos de l'iPhone** : Réglages → Accessibilité → Toucher → Toucher le dos → Toucher deux fois → Jarvis.
- **Mac** : ouvre Raccourcis → double-clic sur Jarvis → onglet ⓘ → *Ajouter un raccourci clavier* (par exemple ⌥ Espace), et coche *Épingler dans la barre des menus*.
- **Écran d'accueil / widget** : appui long sur Jarvis dans Raccourcis → Partager → *Sur l'écran d'accueil*.

## Exemples

- « Retiens que ma mère s'appelle Sophie et que son numéro est le 06 12 34 56 78. »
- « Envoie un WhatsApp à ma mère pour lui dire que j'arrive à 19 h. »
- « Qu'est-ce que j'ai aujourd'hui ? »
- « Rappelle-moi demain à 9 h d'appeler le garage. »
- « Emmène-moi à la gare la plus proche. »
- « Mets du Jul sur Spotify. »
- « Quel temps fera-t-il cet après-midi ici ? »
- « Qu'est-ce que tu sais sur moi ? »

## Ce qui est possible, et ce qui ne l'est pas

**Possible** : tout ce qu'une application accepte de recevoir de l'extérieur : appeler, écrire un SMS / WhatsApp / e-mail (tu confirmes l'envoi), lancer un itinéraire, de la musique, une vidéo, ouvrir une app ou une page, créer des rappels, et lancer **n'importe quel autre raccourci** (beaucoup d'apps en proposent : domotique, minuteur, mode avion, etc. — crée-les dans Raccourcis et dis à Jarvis leur nom).

**Impossible**, sur iPhone comme sur iPad : lire l'intérieur des autres apps (messages WhatsApp reçus, Instagram, tes mails dans Gmail…), appuyer sur des boutons à ta place dans une app, ou envoyer un message sans que tu le valides. Apple bloque ça pour tous les assistants, Siri compris.

Chaque question consomme un peu de crédit sur ta clé API Anthropic.
