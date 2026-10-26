## Clés et comptes à créer — liste établie le 26/10/2026

Pour les sources classées **à ajouter** dans `sources-candidates-21.10.2026.md`.
Liens vérifiés le 26/10/2026 sauf mention contraire.
Une clé posée dans grappes reste **« à vérifier »** jusqu'à son premier test réussi (décision du 20/10).

---

### 1. Comptes à créer maintenant (sources « à ajouter »)

État au 26/10/2026, 14 h 52.

| Source | Catégorie | Où prendre la clé | Ce qu'on a gratuitement | État |
| :-- | :-- | :-- | :-- | :-- |
| Exa | Web | https://dashboard.exa.ai/api-keys | 10 $ à l'inscription, puis 10 $/mois sans carte | **à faire** |
| Linkup | Web | https://app.linkup.so | 4 000 requêtes offertes | fait |
| Firecrawl | Web | https://www.firecrawl.dev/app/api-keys | 1 000 crédits/mois, sans carte | fait |
| Serper | Web | https://serper.dev → « Sign up » | 2 500 requêtes offertes, sans carte | fait |
| Media Cloud | Presse | compte sur https://search.mediacloud.org/sign-in, puis le jeton dans le profil | 4 000 requêtes/semaine | fait |
| API Radio France | Presse | https://developers.radiofrance.fr | 1 000 requêtes/jour, accès sur demande | **à faire** |
| The Guardian | Presse | https://open-platform.theguardian.com/access/ | clé développeur gratuite | fait |
| New York Times | Presse | https://developer.nytimes.com/get-started | 4 000 appels/jour, 10/minute sur l'API Article Search | **à faire** |
| GeoNames | Lieux | https://www.geonames.org/login (compte gratuit) | pas de clé : le **nom d'utilisateur** sert de paramètre | **à faire** |

### Marche à suivre pour les trois qui restent

**Exa.** Ouvrir https://dashboard.exa.ai/api-keys, créer le compte, puis créer une clé depuis cette page.
La clé se passe en en-tête, `Authorization: Bearer <clé>` ou `x-api-key: <clé>`. Les bibliothèques
officielles `exa-py` et `exa-js` lisent la variable d'environnement `EXA_API_KEY`.

**New York Times.** Quatre étapes, dans cet ordre :
1. créer le compte sur https://developer.nytimes.com/get-started ;
2. se connecter sur cette même page ;
3. déclarer une application, par exemple « grappes » ;
4. **cocher les API voulues avant de générer la clé** — au minimum *Article Search*, et *Archive* si on
   veut remonter dans le temps.

Le point qui piège : une clé n'interroge que les API cochées **au moment où elle a été générée**. Une API
oubliée demande une nouvelle clé. Limites de l'API Article Search : 4 000 appels par jour, 10 par minute.

**API Radio France.** Le compte et la clé sont deux étapes distinctes :
1. créer le compte sur https://developers.radiofrance.fr ;
2. demander la clé depuis le portail. Le formulaire demande le nom de l'application, une description du
   projet, et un numéro RCS — à voir ce qu'il accepte pour un projet personnel sans société ;
3. attendre la validation manuelle des équipes de Radio France. La clé arrive par courriel.

Questions : support.openapi@radiofrance.com

**Deux réserves à lever avant de s'appuyer sur Radio France.** Les conditions générales interdiraient de
stocker les données renvoyées et de passer par un serveur intermédiaire qui les conserve — ce que grappes
fait par construction. Cette information vient de notes de réunion de 2020 relayées sur un forum, pas des
CGU elles-mêmes : **à vérifier dans les CGU avant d'en faire une source du projet.** L'interdiction visant
les applications d'agrégation de flux live et de podcasts, elle, est confirmée et ne nous concerne pas.

### 2. Aucune clé, aucun compte

Rien à faire : on branche directement.

- **Web** : Parallel Search MCP, Fetch (MCP officiel), Memento TimeTravel
- **Organisations officielles** : OCDS Data Registry, TED Search API, data.europa.eu (SPARQL), org-id.guide, Govdirectory (passe par le SPARQL Wikidata déjà prévu)
- **Associations** : DILA — JOAFE, IRS (BMF / 990), ProPublica Nonprofit, Charities Register NZ, ACNC, CRA Canada, Charities Regulator IE, 360Giving GrantNav
- **Entreprises** : Finlande PRH, Suisse Zefix, BODACC, info-financière (AMF — 10 000 appels/jour par IP), CompanyScope MCP
- **Réseaux sociaux** : Mobilizon, Mastodon (RSS et API timelines), Bluesky (recherche et Jetstream), Lemmy, PeerTube, MatrixRooms.info (moteur dans SearXNG)
- **Lieux** : OpenAddresses, geoBoundaries, Photon, university-domains-list
- **Presse** : radio-browser.info
- **Outillage** : serveurs MCP officiels, trafilatura, news-please, RSSHub / RSS-Bridge, changedetection.io

### 3. À confirmer sur place avant de brancher

| Source | Point à lever | Où regarder |
| :-- | :-- | :-- |
| The Gazette (UK) | conditions d'API non confirmées | https://www.thegazette.co.uk/data-service |
| Danemark — CVR / Virk | chemin d'accès Datafordeler pas clair | vérification directe de 10 min |
| Suisse — Zefix | licence à vérifier | https://opendata.swiss/en/dataset/zefix-zentraler-firmenindex |

### 4. Pas maintenant — comptes à ne créer que si on tranche dans ce sens

| Source | Statut | Où prendre la clé le jour venu |
| :-- | :-- | :-- |
| Wikimedia Enterprise | à surveiller | https://dashboard.enterprise.wikimedia.com/signup/ — 50 000 requêtes/mois, remise à zéro le 1er |
| DATAtourisme | à surveiller, France seule | https://www.datatourisme.fr/utiliser-les-donnees |
| Geoapify | géocodeur de secours | https://myprojects.geoapify.com — 3 000/jour |
| LocationIQ | géocodeur de secours | https://locationiq.com/register — 5 000/jour |
| OpenCage | géocodeur de secours | https://opencagedata.com/users/sign_up — 2 500/jour, non commercial seulement |

### 5. Rappel de stockage des clés

Décision en attente (proposition du 26/10, non tranchée) : la clé est saisie sur la VM, le champ de la WebUI
reste masqué et n'affiche jamais que « clé enregistrée ». Aucune clé ne transite dans la page ni dans le dépôt.
