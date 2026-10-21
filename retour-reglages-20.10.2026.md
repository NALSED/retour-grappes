## Retours Sednal sur la page Réglages (20/10/2026)

### 0. Intelligence X : retrait complet, prioritaire

On revient à l'état d'avant l'intégration. Critère : **quelqu'un qui inspecte le code ou la doc ne doit en trouver aucune trace**. À supprimer :
- `src/pilote/adaptateurs/intelligencex.py`
- `config/sources/intelligence-x.yaml` et `config/sources/exemples/intelligence-x.json`
- la clé `INTELIGENCEX_API_KEY` dans `config/secrets.env`
- les tests qui la visent (`test_f17_revue.py` et autres)
- **toutes les mentions dans la doc** : ETAT.md, RECETTE.md, BLOCAGE.md, QUESTIONS.md (F16-1, F18-1, F18-2, F18-3), `docs/`

Plus largement : plus aucune trace d'outil de recherche de personne dans la doc (GHunt, holehe, linkedin2username, MailAccess, Intelligence X). F16-1 reste en vigueur, définitivement.

**On ne touche pas à l'historique git** : les commits restent tels quels, aucune réécriture. Noter le retrait dans DECISIONS.md.

### 1. Défaut d'affichage systémique (explique 5 remarques d'un coup)

Les champs sont concaténés sans séparateur partout dans la page : « ajout**é**test », « Web**W**eb », « copie locale complète, EE**t**éléchargée le… », « seconde main**c**ommunautés… », « gratuit**P**as encore testée ». Un seul correctif, à passer sur toute la page.

### 2. Menus repliables et arborescence des sources (modèle TAF)

La page est un empilement de grands blocs : il faut que chaque bloc s'ouvre et se ferme, comme la section Recherches de TAF. Replié : intitulé + état + un rappel court de ce qui est réglé (équivalent du « 41 offre(s) ») + chevron à droite. Déplié : le contenu actuel.

**Complément du 21/10/2026 — arborescence des sources.** Aujourd'hui le menu Recherche/Sources liste toutes les API en vrac, c'est ingérable dès qu'on ajoute des sources. Il faut une arborescence par grande catégorie, reprenant les 8 presets déjà validés le 13/10 :

- Web
- Organisations officielles
- Associations
- Entreprises
- Réseaux sociaux
- Lieux
- Presse
- Personnalisé

Chaque catégorie est un bloc repliable/dépliable. Replié : nom de la catégorie + rappel court (ex. « 6 sources actives »). Déplié : la liste détaillée des sources de cette catégorie, avec leur voyant et leur libellé court (voir section 6).

### 3. Nommage : « Sources » partout

L'assistant dit « ① Sources », la section de Réglages dit « Recherche ». Garder **Sources** partout.

### 4. Preset nouvellement créé

Un preset créé n'est pas éditable : seul « Choisir les sources » est proposé. Il faut pouvoir éditer son nom et sa description.

### 5. Bases officielles / Copies locales

Les deux blocs sont collés et se confondent. Les séparer nettement : « Bases officielles de mon pays » = le catalogue de ce qui existe (9 pays, rien de téléchargé) ; « Copies locales » = ce qui est réellement sur le disque (EE, FR).

### 6. Ligne de source : voyant et texte

- Le rond à gauche doit être **vert** quand la source est disponible et utilisable. (Ceci tranche P1 : le vert et le rouge ne sont plus réservés aux seules décisions utilisateur.)
- Le texte descriptif est trop long et hors sujet. Ne garder que le nom et l'indication **gratuite / payante**. L'état est porté par le voyant.
- Vaut pour tout l'onglet, pas seulement les API (MCP, RSS, etc.).

### 7. Supprimer et ajouter des sources depuis le webui

- **Supprimer** : un bouton sur chaque source affichée. La suppression est visuelle (retire de l'interface), elle ne touche pas aux fichiers.
- **Ajouter** : deux voies.
  - *Ajouter une source existante* : un tableau simple des sources déjà enregistrées **et valides** (prêtes à l'emploi — pas celles sans clé ni celles non implémentées).
  - *Ajouter une nouvelle source* : choix du type (API, MCP, flux…), puis un encart s'ouvre dessous (ou un onglet) pour saisir la clé ou ce que demande le type. Le programme place les identifiants au bon endroit lui-même, sans passer par le fichier de configuration à la main.

**Complément du 21/10/2026 — rangement obligatoire à l'ajout.** Le formulaire d'ajout comporte un champ **catégorie obligatoire** : la nouvelle source doit être rangée dans une des 8 catégories de la section 2. « Personnalisé » sert de catégorie libre pour ce qui ne rentre dans aucune des 7 autres. Une source ne doit jamais pouvoir être enregistrée sans catégorie — c'est ce qui alimente l'arborescence.

### 8. Partie Vérification : mêmes règles

Texte raccourci, voyant vert si la source est opérationnelle, suppression obligatoire, ajout comme ci-dessus.

Sur ce qui est ajoutable en déclaratif depuis le webui : les API JSON ou CSV (le flux « coller l'adresse → désigner les champs » existe déjà), les serveurs MCP, les flux RSS/OPML, les fichiers locaux, une base SQLite en lecture seule, un programme local. Ne le sont pas : l'authentification en plusieurs étapes (OAuth), la pagination à curseur, le GraphQL, les réponses très imbriquées — elles demandent un adaptateur. Dans ce cas, **enregistrer quand même la source et afficher en clair « cette source demande un adaptateur »**, jamais un refus sans explication.

### 9. RSS mal placé

Le bloc RSS est sous Recherche/Sources alors que tout le reste de l'ajout est dans l'onglet Ajout. Le déplacer dans Ajout.

### 10. Interrupteur « Vérifier aussi… » (F17-2)

Le défaut reste **éteint**, décision de Sednal — il le cochera quand il en aura besoin. Deux corrections seulement :
- le texte est périmé (il parle de « points de confirmation », remplacés par un nombre) ;
- après le retrait d'Intelligence X, la liste devient CourtListener, OpenSanctions, Wayback Machine.

### 11. Sortie

Rien à signaler.
