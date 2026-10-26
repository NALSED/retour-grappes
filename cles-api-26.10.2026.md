## Clés et comptes à créer — liste établie le 26/10/2026

Pour les sources classées **à ajouter** dans `sources-candidates-21.10.2026.md`.
Liens vérifiés le 26/10/2026 sauf mention contraire.
Une clé posée dans grappes reste **« à vérifier »** jusqu'à son premier test réussi (décision du 20/10).

---

### 1. Comptes à créer maintenant (sources « à ajouter »)

| Source | Catégorie | Où prendre la clé | Ce qu'on a gratuitement |
| :-- | :-- | :-- | :-- |
| Exa | Web | https://dashboard.exa.ai/api-keys | 10 $ à l'inscription, puis 10 $/mois sans carte |
| Linkup | Web | https://app.linkup.so | 4 000 requêtes offertes |
| Firecrawl | Web | https://www.firecrawl.dev/app/api-keys | 1 000 crédits/mois, sans carte |
| Serper | Web | https://serper.dev → « Sign up » | 2 500 requêtes offertes, sans carte |
| Media Cloud | Presse | compte sur https://search.mediacloud.org/sign-in, puis le jeton dans le profil du compte | 4 000 requêtes/semaine |
| API Radio France | Presse | https://developers.radiofrance.fr | 1 000 requêtes/jour, accès sur demande |
| The Guardian | Presse | https://open-platform.theguardian.com/access/ | clé développeur gratuite |
| New York Times | Presse | https://developer.nytimes.com → compte, puis « Apps » et activer les API voulues | clé gratuite |
| GeoNames | Lieux | https://www.geonames.org/login (compte gratuit) | pas de clé : le **nom d'utilisateur** sert de paramètre sur chaque appel |

Les pages de The Guardian et du New York Times refusent la lecture automatique depuis l'environnement de travail :
leurs adresses sont données de mémoire et confirmées par ailleurs, à ouvrir une fois à la main pour contrôle.

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
