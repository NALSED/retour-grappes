# 26 — Enrichir Web et Réseaux sociaux, prise base extérieure, recherche par emoji

Consigne pour momo, demandée par Sednal le 27/10/2026. Trois sujets :

1. enrichir en priorité les catégories **Web** et **Réseaux sociaux** ;
2. une prise **base de données extérieure** dans les sources ;
3. dans les réseaux sociaux, la possibilité de **chercher un emoji**.

Tout ce qui est proposé ici a été **vérifié avant d'être écrit** : soit en exécutant le code du dépôt,
soit en interrogeant le service concerné. Ce qui n'a pas pu l'être est marqué comme tel, et deux pistes
sont écartées avec leur raison.

---

## 0. Un point d'ordre, à lire d'abord

Les douze sources de Réseaux sociaux rendent toutes des **comptes**. Or le § 1 de
`24-corrections-mesurees-27.10.2026.md` montre qu'un compte dont le nom est celui d'une personne physique
n'est **pas** écarté du rapprochement côté pilote aujourd'hui : `mcp_generique.py` l. 56 lui donne le
genre « compte », et le filtre de `rapprochements.py` l. 81 n'écarte que le genre « personne ».

**Chaque source de réseau social ajoutée multiplie donc cette exposition** — la règle n° 8 est la seule
qui relève du droit. La correction est petite (la règle existe déjà dans le moteur, il lui manque d'être
appliquée à la couture). Fais-la avant d'allumer de nouvelles sources sociales ; ensuite, enrichis
librement.

---

## 1. Web : le manque n'est pas là où on le croirait

La catégorie compte déjà **dix** sources, dont huit moteurs de recherche généraux (brave, exa, firecrawl,
linkup, marginalia, mojeek, searxng, serper, tavily). Ajouter un onzième moteur général n'apporterait
presque rien : ils renvoient les mêmes pages.

Ce qui manque dans cette catégorie, c'est une **autre nature d'information sur le web** : non pas des
pages, mais ce que l'infrastructure d'une organisation dit d'elle.

### RDAP — à brancher, c'est le meilleur rapport valeur/effort du document

**RDAP** (*Registration Data Access Protocol*) est le successeur normalisé de WHOIS : une réponse **JSON
structurée** au lieu d'un texte libre, pour un nom de domaine, une plage d'adresses IP ou un numéro d'AS.
Il se découvre par un registre d'amorçage de l'IANA (**RFC 9224**), et un serveur d'amorçage public,
[rdap.org](https://about.rdap.org/), redirige une requête vers le registre compétent **sans clé d'API**.
Tous les domaines génériques sont conformes au profil gTLD.

| Point | Valeur |
| :-- | :-- |
| Clé | **aucune** |
| Rôle | **ENRICHIR** — sur un domaine déjà présent sur une fiche, jamais une recherche par mot |
| Limite d'appels | 10 requêtes par 10 secondes sur rdap.org (Cloudflare). Pour un usage plus lourd, la RFC 9224 recommande de lire directement les registres d'amorçage de l'IANA et d'appeler le registre compétent |
| Fiabilité | **B** — registre officiel du domaine, mais second rang par rapport au registre d'entreprise |
| Famille | `api` ; adaptateur `api_json` suffit |

**Conséquence directe : Intelligence X devient inutile.** La seule fonction qu'on lui avait laissée le
27/10 (`17-sources-a-implementer.md` § 5) est l'historique WHOIS d'un domaine. RDAP fait la même chose,
**sans clé, sans compte, en données structurées, et par une norme IETF** — là où Intelligence X est un
relais commercial, noté D, dont la réponse n'a jamais pu être contrôlée faute de clé
(`config/sources/intelligencex.yaml`, note). Branche RDAP ; laisse Intelligence X éteinte, et propose à
Sednal de la retirer.

Règle n° 8 à respecter dans la correspondance des champs : garde le **bureau d'enregistrement, les dates,
le statut du domaine et l'organisation titulaire quand elle est déclarée**. Ne garde **aucun champ de
contact de personne** (nom, adresse, courriel, téléphone du titulaire), même quand le registre le rend.
Les registres européens masquent déjà les titulaires personnes physiques, mais la règle ne doit pas
dépendre de leur politique.

### Transparence des certificats (crt.sh) — écarté pour l'instant, avec sa raison

Retrouver les autres domaines d'une organisation par ses certificats TLS serait utile, et
`crt.sh` est le service de référence. **Mais son `robots.txt` interdit l'accès automatisé** — j'ai essayé
de lire la page et l'accès a été refusé pour cette raison.

Or le moteur **respecte strictement robots.txt** : `grappes/src/grappes/fetch/robots.py`, première ligne,
« robots.txt : lu une fois par hôte et par exécution, **respecté strictement** ». Brancher crt.sh
contredirait la discipline que le programme s'impose partout ailleurs.

Classement : **P3** au sens de `17-sources-a-implementer.md` — ne code pas le branchement. Deux issues
possibles, l'une ou l'autre à vérifier avant quoi que ce soit : une autorisation explicite du service pour
son interface JSON, ou un autre fournisseur de recherche dans les journaux de transparence dont les
conditions l'autorisent.

### Index Common Crawl — faible priorité, et pourquoi

L'index CDX de Common Crawl se consulte librement, mais il répond à « quelles pages existent sous ce
domaine », pas à « qui parle de ce sujet ». Il ne donne donc **aucune portée de recherche
supplémentaire** : c'est un outil de vérification, qui recoupe largement ce que Wayback fait déjà. À
garder en réserve, après le reste.

---

## 2. Réseaux sociaux : une plateforme française manque, et elle compte

### OpenAgenda — à brancher en premier dans cette catégorie

Les groupes locaux français publient leurs événements sur **OpenAgenda**, utilisé par des communes, des
lieux culturels et des collectifs. C'est précisément la cible du projet (organisations et groupes
informels, France), et aucune source du catalogue ne la couvre.

L'API v2 est documentée sur [developers.openagenda.com](https://developers.openagenda.com/) :
**l'usage est gratuit, mais une authentification est requise même en lecture** — un compte, puis une clé
d'API dans les paramètres.

| Point | Valeur |
| :-- | :-- |
| Clé | **à créer** (gratuite) → source **P2** tant qu'elle n'est pas obtenue |
| Rôles | **CHERCHER** (`/v2/agendas/{uid}/events`) **et CATALOGUE** (`/v2/agendas` liste les agendas, donc les organisations qui publient) |
| Pays | FR |
| Fiabilité | **C** — déclaratif : l'organisateur écrit lui-même sa fiche |
| À confirmer au premier essai réel | la page des développeurs ne documente pas les filtres : **vérifie qu'on peut filtrer par mot-clé et par géographie** avant de figer la déclaration. Si le filtre géographique n'existe pas, la source reste utile par mot-clé |

Le rôle CATALOGUE est à traiter comme les quatre autres sources qui le portent : il dépend de la
question n° 1 de `14-questions-ouvertes.md`, **toujours pas tranchée** — ne le fige pas dans le code.

### HelloAsso — écarté, et c'est définitif en l'état

C'était le candidat le plus évident pour les associations françaises. Il ne marche pas :
[dev.helloasso.com](https://dev.helloasso.com/) réserve les clés au **programme de partenariat**, et
l'API donne à une association l'accès à **ses propres** données (dons, adhésions, billetterie) — aucun
point d'entrée public pour chercher *d'autres* organisations.

C'est exactement la limite de l'API Graph d'Instagram : un compte y voit son propre compte, pas ceux des
autres. Ne perds pas de temps dessus. Ce qui reste accessible, c'est la **page publique** d'une
association sur HelloAsso, trouvable par les moteurs de recherche déjà branchés — donc rien de neuf à
brancher.

### YouTube Data API — à brancher, à la place du détour par Apify

Une organisation a souvent une chaîne. L'API officielle (`search.list`, `type=channel`, avec `q` et
`regionCode`) est **gratuite dans un quota journalier**, et c'est une source de **première main** — mieux
que de payer Apify pour la même information de seconde main. Le quota exact et le coût en unités par
appel sont à relever dans la documentation **au moment du branchement** : ils changent, et je ne les
donne pas de mémoire.

### Deux sources déjà spécifiées, jamais branchées

Rien à spécifier, juste à finir — toutes deux listées **P1** dans `17-sources-a-implementer.md` § 5 et
absentes de `config/sources/` :

- **MatrixRooms.info** — annuaire des salons Matrix, s'intègre comme moteur SearXNG ;
- **Bluesky Jetstream** — flux temps réel, rôle OUTIL, le filtrage par mot-clé restant à notre charge.

---

## 3. La prise « base de données extérieure »

### Ce qui existe déjà, et qui est bien fait

`pilote/src/pilote/adaptateurs/base_sql.py` est **déjà** une prise base de données, et ses garde-fous
sont sérieux : fichier ouvert en lecture seule par SQLite lui-même (`mode=ro` en URI), `PRAGMA
query_only = ON`, une **seule** instruction, obligatoirement `SELECT` ou `WITH`, liste de mots interdits
(`attach`, `pragma`, `insert`, `drop`, `load_extension`…), et les mots cherchés passés **par paramètre
lié** (`:q`), jamais collés dans le texte de la requête.

Mais elle ne lit qu'un **fichier SQLite**. Une « base extérieure » au sens courant — PostgreSQL,
MariaDB/MySQL sur une machine du réseau — n'est pas couverte.

### Ce qu'il faut ajouter, et les garde-fous qu'une base en réseau exige en plus

Garde tout ce qui précède, et ajoute ce qu'un fichier ne demandait pas :

1. **La lecture seule imposée par la base, pas par un filtre de mots.** Sur un serveur, une liste de mots
   interdits est contournable. Impose-la du côté du moteur de base :
   - PostgreSQL : `SET TRANSACTION READ ONLY` (ou, mieux, un rôle dédié sans droit d'écriture) ;
   - MySQL/MariaDB : `SET SESSION TRANSACTION READ ONLY`.
   Garde le filtre de mots **en plus**, comme première barrière, jamais comme seule barrière.
2. **Un délai maximal d'exécution**, sinon une requête lourde bloque la recherche :
   `statement_timeout` (PostgreSQL), `max_execution_time` (MySQL).
3. **Les identifiants dans `secrets.env`**, jamais dans la déclaration de la source, qui est versionnée.
   Même traitement qu'une clé d'API : masqués, « clé enregistrée », jamais réaffichés
   (`05-reglages-sources.md`).
4. **Une exception explicite et étroite à la règle des adresses privées.** `grappes/src/grappes/fetch/rules.py`
   refuse toute adresse privée ou locale (`is_private_host`) — c'est juste pour une page web, et c'est
   précisément là qu'est la base de Sednal. L'exception doit porter **sur cette prise seulement**, sur
   l'hôte déclaré dans la source, et nulle part ailleurs : jamais un assouplissement général de la règle.
5. **TLS quand la base n'est pas sur la machine** (`sslmode=require` côté PostgreSQL).
6. **Accès = « Copie locale » dans la fiche source ?** Non : une base extérieure fait un appel réseau mais
   n'a pas de quota, et n'a pas de date de fraîcheur. C'est un **troisième cas**, à ajouter au champ
   **Accès** de `05-reglages-sources.md` (aujourd'hui *API* / *Copie locale*) : **« Base extérieure »** —
   pas de clé d'API, pas de quota, pas de date de copie, mais une adresse de machine et des identifiants.
   Ajoute le mot à `charte/nommage.md` avant de l'écrire à l'écran (règle n° 5).

### La limite de la règle n° 8 sur cette prise, dite franchement

Le test `tests/test_regle8_personnes_morales.py` vérifie qu'aucune copie locale ne verse une colonne de
personne, en lisant la correspondance de colonnes de sa déclaration. **Il ne peut pas faire ça ici** : la
requête est écrite par l'utilisateur, et rien n'empêche un `SELECT prenom AS nom`.

Aucun code ne corrigera ça, et il ne faut pas faire semblant. Ce qui est faisable, et qui suffit :

- la prise ne garde que les colonnes connues (`nom`, `ville`, `genre`, `numero`, `site`, `pays`) —
  `convertir()` le fait déjà ;
- un **avertissement à l'allumage**, le même composant que pour l'Assistant IA et les prestataires de
  collecte (`charte/composants/Avertissement/`) : cette base est la vôtre, son contenu est votre
  responsabilité, et la règle n° 8 du programme ne peut pas la contrôler à votre place ;
- la source démarre **éteinte**, comme toute source nouvelle.

C'est une limite assumée et écrite, pas un trou silencieux.

---

## 4. La recherche par emoji

### Ce que le code fait aujourd'hui : il détruit l'emoji, en silence

Vérifié en exécutant les deux expressions régulières du dépôt sur une requête réelle :

| Fonction | Fichier | Sur `"collectif ♻️ Isère"` |
| :-- | :-- | :-- |
| `mots_significatifs` | `pilote/src/pilote/adaptateurs/__init__.py` l. 145-154 | rend `['collectif', 'Isère']` — l'emoji n'est **jamais** extrait (le motif exige 3 caractères de mot ou un sigle de 2 majuscules) |
| `nom_normalise` | même fichier, l. 156-163 | rend `'collectif isère'` — `[^a-z0-9]+` **efface** l'emoji |

Donc : un emoji tapé dans la recherche est **supprimé avant qu'aucune source ne le voie**, et un emoji
présent dans le nom d'un résultat est effacé à la comparaison. Rien ne le dit à l'utilisateur — ce qui
est aussi un manquement à la règle n° 1 (« un retrait se voit »).

### La norme à suivre : UTS #51, et deux bibliothèques qui la portent

Un emoji **n'est pas un caractère**. Vérifié en exécutant les bibliothèques :

| Emoji | Points de code |
| :-- | :-- |
| ✊🏽 | `U+270A U+1F3FD` (poing + teinte de peau) |
| 🏳️‍🌈 | `U+1F3F3 U+FE0F U+200D U+1F308` (drapeau + sélecteur + liant + arc-en-ciel) |

La norme est **UTS #51, *Unicode Emoji*** (version 18.0, révision 31, 2026-08-28,
[unicode.org/reports/tr51](https://www.unicode.org/reports/tr51/)). Ce qu'elle donne et qu'il ne faut pas
réinventer :

- **U+200D (ZWJ)** joint les éléments d'une séquence en un seul emoji ;
- **U+FE0F (VS16)** demande la présentation « emoji » d'un caractère (ED-9) ;
- les formes **entièrement qualifiée** (ED-18), **minimalement qualifiée** (ED-18a) et **non qualifiée**
  (ED-19) : la même image a plusieurs écritures légales ;
- les **teintes de peau** vont de **U+1F3FB à U+1F3FF** ;
- les fichiers de données publiés : `emoji-test.txt`, `emoji-sequences.txt`, `emoji-zwj-sequences.txt`.

Deux bibliothèques du dépôt public portent déjà cette norme, installées et essayées ici :

| Bibliothèque | Ce qu'elle fait | Essai réel |
| :-- | :-- | :-- |
| `regex` (2026.9.29) | `\X` découpe en **grappes de graphèmes** | `regex.findall(r'\X', …)` rend bien `'🏳️‍🌈'` **en un seul morceau** |
| `emoji` (2.16.0) | porte les données UTS #51 | `emoji.emoji_list(q)` rend chaque emoji avec sa position |

### Les quatre règles de traitement

1. **Ne jamais jeter l'emoji.** `mots_significatifs` doit le rendre comme un mot cherchable à part
   entière. Un emoji seul est une requête valable.
2. **Envoyer à la source la forme entièrement qualifiée** — celle que l'utilisateur a tapée, VS16 compris.
   C'est elle que les services indexent (les formes non qualifiées ne sont pas toutes dans l'ensemble
   recommandé de la norme).
3. **Ne normaliser que pour comparer, en interne** : retirer VS16 et la teinte de peau, garder la
   séquence ZWJ entière. Essayé : `✊🏽` → `✊` (`U+270A`), ce qui fait que `✊🏽` et `✊🏿` se comparent égaux.
   Cette forme réduite ne sort **jamais** vers une source.
4. **Encoder pour l'adresse** : `✊` → `%E2%9C%8A`, `🏳️‍🌈` →
   `%F0%9F%8F%B3%EF%B8%8F%E2%80%8D%F0%9F%8C%88`. `urllib.parse.quote` suffit ; aucune source ne doit
   recevoir l'emoji brut dans une URL.

### Quelles sources cherchent vraiment un emoji : à déclarer, jamais à supposer

C'est le point où ce sujet peut mal tourner. Les services **ne se valent pas du tout** : une recherche
plein texte qui découpe en mots (PostgreSQL `tsvector`, la plupart des moteurs d'articles) **perd**
l'emoji à l'indexation, et rendra zéro résultat sans jamais dire pourquoi. D'autres l'indexent. Ça ne se
devine pas : **ça se mesure, service par service**.

La fiche source a déjà exactement le bon mécanisme : le champ **`consomme`**, qui dit ce qu'une source
accepte en entrée (`sujet`, `mots_cles`, `lieu`, `nom`). Ajoute une valeur **`emoji`**, à ne porter que
sur une source **essayée contre le service réel**, avec sa date dans `verifie_le` — la même discipline que
partout ailleurs dans ce catalogue.

Candidats à essayer en premier, par ordre de chance de marcher : **Bluesky** (`searchPosts`, recherche
plein texte), les **prestataires de collecte** (Apify, Bright Data : ils pilotent le champ de recherche de
la plateforme elle-même), **Arctic Shift** (archive Reddit avec sa propre recherche), les **moteurs
généraux** (serper, brave). Peu probables : **Lemmy** (recherche Postgres), **Mastodon** (la recherche
plein texte est facultative par instance et restreinte depuis la 4.2), **GDELT** (texte d'articles
tokenisé).

### Ce que l'utilisateur doit voir

Reprends le mécanisme déjà en place pour le champ **Langue** — le contrôle n° 6 de la fenêtre d'Aide
(`11-fenetre-aide.md`), qui compare la langue de la saisie à celle des sources posées et signale qu'une
Traduction manque. Même forme, même endroit :

> **la saisie contient un emoji, et aucune source allumée ne déclare le chercher.**

L'Aide **signale**, elle n'éteint rien et n'allume rien (règle n° 6). Et si la recherche part quand même
avec des sources qui ne prennent pas l'emoji, le bilan de recherche doit le **dire** — « emoji non
transmis à n sources » — plutôt que de rendre zéro résultat sans explication. C'est la règle n° 1
appliquée à la requête et non seulement aux résultats.

### Le mot entre dans le vocabulaire fermé

Règle n° 5 : on modifie la liste **d'abord**. Le mot **emoji** (invariable, sans accent, cette forme et
pas une autre) est ajouté à `charte/nommage.md` dans le même envoi que ce document.

### Les tests qui le prouvent

1. `mots_significatifs("collectif ♻️ Isère")` rend les trois éléments, emoji compris ;
2. `🏳️‍🌈` traverse la chaîne **en un seul morceau**, sans être coupé entre le drapeau et l'arc-en-ciel ;
3. `✊🏽` et `✊🏿` se comparent **égaux** en interne, et la forme envoyée à la source reste celle tapée ;
4. une source déclarée sans `emoji` dans `consomme` déclenche le signalement de l'Aide, et le bilan dit
   que l'emoji ne lui a pas été transmis ;
5. un emoji dans le nom d'un résultat n'est plus effacé par `nom_normalise` au point de rendre deux noms
   différents identiques.

---

## Dans quel ordre

| # | Travail | Pourquoi là |
| :-- | :-- | :-- |
| 0 | **Le § 1 du doc 24** (un compte sans indice institutionnel est une personne) | prérequis : toute source sociale ajoutée avant élargit l'exposition à la règle n° 8 |
| 1 | **RDAP** | aucune clé, norme IETF, et rend Intelligence X inutile |
| 2 | **L'emoji dans `mots_significatifs` et `nom_normalise`** + le signalement de l'Aide | corrige une perte **silencieuse** de la saisie ; indépendant de toute nouvelle source |
| 3 | **OpenAgenda** (clé à créer) | la plateforme française des groupes locaux, absente du catalogue |
| 4 | **`consomme: emoji`** essayé source par source, en commençant par Bluesky | sans la mesure, la fonction promet ce qu'elle ne tient pas |
| 5 | **La prise base extérieure** (PostgreSQL, MySQL) | demande de Sednal ; la base SQLite existe déjà et sert de modèle |
| 6 | **YouTube Data API**, **MatrixRooms.info**, **Bluesky Jetstream** | de la portée en plus, sans difficulté particulière |
| 7 | Transparence des certificats | **P3** : robots.txt à régler d'abord, ne code rien |

## Où regarder

- `24-corrections-mesurees-27.10.2026.md` § 1 — le prérequis de la ligne 0
- `17-sources-a-implementer.md` § 1 et § 5 — où s'inscrivent RDAP, OpenAgenda et les deux P1 non branchées
- `05-reglages-sources.md` — le champ **Accès**, à qui il faut ajouter « Base extérieure »
- `11-fenetre-aide.md` contrôle n° 6 — le mécanisme que reprend le signalement de l'emoji
- `charte/nommage.md` — « emoji » et « base extérieure »
- `14-questions-ouvertes.md` § 1 — le rôle CATALOGUE d'OpenAgenda en dépend
