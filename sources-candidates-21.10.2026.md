## Sources candidates pour grappes — recherche du 21/10/2026

Notes de travail. Rangement par les 8 catégories de l'arborescence (voir `retour-reglages-20.10.2026.md`, section 2).
Chaque source porte un verdict : **à ajouter** / **à surveiller** / **écarté**.
Les sources déjà en place ou déjà tranchées avant le 21/10 ne sont pas répétées ici.

---

### 1. Web

**À ajouter**

| Source | Apport | Coût / clé |
| :-- | :-- | :-- |
| [Parallel Search MCP](https://docs.parallel.ai/integrations/mcp/quickstart) | Serveur MCP hébergé de recherche web | Gratuit, **sans clé ni compte** |
| [Exa](https://exa.ai/pricing) | Recherche web par le sens | $10 à l'inscription **puis $10/mois renouvelés, sans carte** |
| [Linkup](https://www.linkup.so/pricing) | Recherche web + récupération de page | 4 000 requêtes offertes, puis ~$5/1000 |
| [Firecrawl](https://www.firecrawl.dev/) | Recherche **et** livraison de page — couvre aussi le rôle de ZenRows | 1 000 crédits/mois sans carte |
| [Fetch (MCP officiel)](https://github.com/modelcontextprotocol/servers) | Récupère une page et la rend exploitable | Gratuit, auto-hébergé |
| [Memento TimeTravel](https://timetravel.mementoweb.org/about) | Interroge plusieurs archives web d'un coup, pas seulement Wayback | Gratuit, sans clé |
| [Serper](https://serper.dev/) | Résultats Google, recours payant le moins cher | 2 500 offertes puis $0,30/1000 |

**À surveiller**

- [Wikimedia Enterprise — palier gratuit](https://enterprise.wikimedia.com/blog/enhanced-free-api) : 50 000 requêtes/mois, articles en JSON structuré, remise à zéro le 1er du mois. Complète Wikidata (identifiants) par le texte des fiches.
- [Open Web Index — openwebsearch.eu](https://openwebsearch.eu/) : index web ouvert européen, travaux terminés avec de bons résultats mais pas encore interrogeable clé en main. L'idéal pour ce projet — à re-regarder dans quelques mois.

**Écarté**

- **Brave Search API** : palier gratuit **supprimé en février 2026**. Le crédit restant n'est conservé qu'à condition d'afficher publiquement un crédit « Brave » — incompatible avec un projet libre.
- **Google Custom Search JSON API** : fermé aux nouveaux clients, **arrêt annoncé au 1er janvier 2027**.
- **Bing Web Search API** : arrêté le 11 août 2025.

---

### 2. Organisations officielles

**À ajouter**

| Source | Apport | Coût / clé |
| :-- | :-- | :-- |
| [OCDS Data Registry](https://data.open-contracting.org/) | Marchés publics de **100+ organismes publieurs** dans le monde, tous au même format. Qui achète quoi à qui. | Gratuit, sans clé |
| [TED — Search API](https://docs.ted.europa.eu/api/latest/index.html) | Avis de marchés publics de toute l'UE | Gratuit, **aucune clé requise** |
| [data.europa.eu](https://data.europa.eu/en/about/sparql) | Catalogue officiel de l'open data européen, interrogeable. Alimente la fonctionnalité « catalogue de bases officielles par pays » : grappes découvre les jeux de données disponibles au lieu qu'on les cherche à la main. | Gratuit |
| [org-id.guide](https://org-id.guide/) ([données](https://github.com/OpenDataServices/org-ids)) | Catalogue des registres officiels d'organisations pays par pays. La liste de départ du catalogue par pays, prête à lire. | Gratuit, AGPL |
| [Govdirectory](https://www.govdirectory.org/) | Comptes et services officiels des administrations, ~60 pays, **CC0**. Données dans Wikidata → accessible par le SPARQL Wikidata déjà prévu, rien de neuf à brancher. | Gratuit |

---

### 3. Associations

**À ajouter**

| Source | Apport | Coût / clé | Pays |
| :-- | :-- | :-- | :-- |
| [DILA — JOAFE](https://echanges.dila.gouv.fr/OPENDATA/ASSOCIATIONS/) + [API annonces et comptes](https://www.data.gouv.fr/dataservices/api-annonces-officielles-et-comptes-annuels-des-associations) | Créations, modifications, dissolutions au Journal officiel, en continu, plus les comptes annuels. L'équivalent du « flux » côté associations. | Gratuit | FR |
| [IRS — Exempt Organizations BMF + 990](https://www.irs.gov/charities-non-profits/tax-exempt-organization-search-bulk-data-downloads) | Fichiers officiels de toutes les organisations exonérées d'impôt | Gratuit, sans clé | US |
| [ProPublica Nonprofit Explorer API](https://projects.propublica.org/nonprofits/api) | Recherche par nom ou EIN + finances par année. 3 M de déclarations, 14 M de documents depuis 2001. Appels sans authentification. | Gratuit | US |
| [Charities Register NZ (OData)](http://www.odata.charities.govt.nz) | Registre officiel, JSON/CSV/ATOM, filtres côté serveur, « no authentication required » | Gratuit, sans clé | NZ |
| [ACNC Charity Register](https://data.gov.au/data/dataset/acnc-register) | Registre officiel des organismes à but non lucratif | Gratuit, sans clé | AU |
| [CRA — organismes de bienfaisance](https://ised-isde.canada.ca/site/corporations-canada/en/node/269) | Registre officiel, déclarations T3010 en CSV | Gratuit, sans clé | CA |
| [Charities Regulator](https://datacatalogue.gov.ie/dataset/register-of-charities) | Registre officiel en CSV, publié par le régulateur | Gratuit, sans clé | IE |
| [360Giving GrantNav](https://grantnav.threesixtygiving.org/) | Subventions versées aux associations. Angle utile : trouver une association **par l'argent qu'elle reçoit**, pas par sa déclaration. | Gratuit | UK |

**À surveiller**

- [OSCR — Scottish Charity Register](https://www.oscr.org.uk/) : registre public, mais ni fichier ni API confirmés. À vérifier sur place.
- [Registre de transparence de l'UE](https://transparency-register.europa.eu/index_en) : ONG et associations qui font du plaidoyer à Bruxelles — pertinent pour des acteurs de mobilisations, mais aucune API ni export officiel confirmé.
- [LittleSis](https://littlesis.org/database) : liens entre organisations et responsables, libre et exportable, mais surtout américain et alimenté par des bénévoles → seconde main, **jamais pour confirmer un nom**.

**Écarté**

- **API DJEPVA / RNA** ([via API Entreprise](https://entreprise.api.gouv.fr/catalogue/djepva/associations_open_data)) : la plus riche sur les associations françaises (statuts, agréments, bénévoles, salariés, comptes), **mais** accès sur habilitation liée à un cas d'usage déclaré, interdiction de commercialiser, affichage limité aux agents autorisés, journalisation de leurs accès. **Incompatible avec un outil publié en MIT que n'importe qui installe.** Demande d'accès possible à titre personnel, mais jamais comme source par défaut du projet.
- **Connecteurs Apify** autour de l'ACNC, de ProPublica et des 990 : relais payants de données officielles déjà gratuites et sans clé.
- **Base permanente des équipements (INSEE)** : France seule, comptage d'équipements par commune plutôt que des entités nommées.

---

### 4. Entreprises

**À ajouter**

| Source | Apport | Coût / clé | Pays |
| :-- | :-- | :-- | :-- |
| [Finlande — PRH](https://avoindata.prh.fi/en/info/swagger-ui) | Registre du commerce : entreprises, notifications depuis 2014, comptes annuels. Mis à jour quotidiennement, CC-BY. | Gratuit | FI |
| [Suisse — Zefix](https://opendata.swiss/en/dataset/zefix-zentraler-firmenindex) | Registre du commerce, API REST officielle, quotidien, + publications FOSC. Licence à vérifier. | Gratuit | CH |
| [BODACC](https://www.data.gouv.fr/datasets/bodacc) | Annonces civiles et commerciales : créations, radiations, liquidations, ventes. Les **événements**, pas la photo statique du registre. | Gratuit | FR |
| [info-financière (AMF)](https://www.data.gouv.fr/dataservices/api-info-financiere) | Communiqués et information réglementée des sociétés cotées | **Libre, sans clé, 10 000 appels/jour par IP** | FR |
| [The Gazette](https://www.thegazette.co.uk/) | Journal officiel : liquidations, faillites, avis d'entreprises. Officiel et première main, mais **conditions d'API non confirmées** — à vérifier sur place. | À confirmer | UK |
| [CompanyScope MCP](https://github.com/Stewyboy1990/companyscope-mcp) | Fiche agrégée (Wikidata, GitHub, SEC EDGAR, RDAP, DNS, brevets, pages carrières). **Enrichissement d'un nom déjà trouvé, pas découverte.** Seconde main par construction — à marquer comme tel pour ne pas compter deux fois la même info. | Gratuit, sans clé | Mondial |

**Écarté**

- **Les « actors » Apify de registres** (EU Company Registry, TED Tenders, gBizINFO Japan, Ireland CRO, Finland PRH…) : revendent des données obtenues gratuitement en direct.
- **Opendatabot (Ukraine)** : techniquement solide mais commercial sans tarif public, passage obligé par un service commercial.
- **[world-company-database](https://github.com/Alessandro114/world-company-database)** : pousse le service payant de son auteur dans presque chaque section, maintenance invérifiable, rien d'inédit.
- **Serveurs MCP SEC EDGAR** (8+ dépôts concurrents) : SEC EDGAR est déjà classé « valeur limitée, US-only » ; un emballage MCP ne change pas la valeur de la source.
- **Checko** (Russie) : clé payante.
- **Allemagne, Autriche, Italie, Portugal, Espagne, Inde** : reconfirmé, registres payants ou sans API officielle gratuite.
- **Danemark (CVR / Virk)** : gratuit en principe, mais chemin d'accès (Datafordeler) pas clair. Vérification directe de 10 min à faire.

---

### 5. Réseaux sociaux

Tout ce bloc est **gratuit, sans clé, première main** — ce sont les API officielles des plateformes elles-mêmes, pas des revendeurs.

**À ajouter**

| Source | Apport | Détail |
| :-- | :-- | :-- |
| [Mobilizon](https://docs.mobilizon.org/5.%20Interoperability/3.graphql_api/) | Recherche d'**événements** par terme, tags, fenêtre de dates et **rayon en km autour d'un point**. L'équivalent libre de Meetup (toujours en attente d'abonnement payant), et la plateforme des mobilisations françaises. | GraphQL sur `/api` de n'importe quelle instance, sans authentification. Réserve : recherche de *groupes* non documentée, seulement les événements. |
| [Mastodon — flux RSS](https://fedi.tips/?p=1271) | Tout compte et **tout hashtag** expose un flux RSS nativement | `/@compte.rss`, `/tags/motcle.rss`. Zéro clé, zéro quota, et grappes lit déjà du RSS → branchement quasi gratuit. |
| [Mastodon — API timelines](https://docs.joinmastodon.org/methods/timelines/) | Timeline publique et par hashtag | `GET /api/v1/timelines/tag/:hashtag`. Chaque instance peut couper l'accès anonyme (401) : à traiter comme un échec de source normal, pas un bug. |
| [Bluesky — recherche](https://docs.bsky.app/docs/advanced-guides/rate-limits) | Recherche plein texte sur tout le réseau | Via `public.api.bsky.app`, qui **ne gère pas l'authentification** par conception. Limites « généreuses », non chiffrées. |
| [Bluesky — Jetstream](https://atproto.com/blog/jetstream) | **Flux temps réel** de tout le réseau, en JSON. Utile pour suivre une mobilisation en direct. | Websocket, 4 instances publiques, auto-hébergeable (Go). Le filtrage par mot-clé se fait de notre côté. |
| [Lemmy — `/search`](https://apis.io/apis/lemmy/lemmy-search-api/) | Recherche sur le réseau fédéré : posts, commentaires, **communautés**, comptes. Le remplaçant de Reddit, bloqué. | `GET /search` avec `type_=Communities` pour ne sortir que des groupes. Sans authentification. |
| PeerTube | Recherche de vidéos, chaînes et comptes | API publique sans clé sur chaque instance. Priorité moyenne : moins de volume, mais même coût de branchement qu'une API JSON. |
| [MatrixRooms.info](https://matrixrooms.info/integrations) | Recherche sur les salons publics Matrix | **S'intègre comme moteur dans SearXNG** ([doc](https://docs.searxng.org/dev/engines/online/mrs.html)), déjà auto-hébergé → une ligne de config, pas un adaptateur. |

**À vérifier (test de 10 min chacun)**

- **Discourse** : API de recherche sur les forums, accès anonyme courant mais non confirmé.
- **Open Collective** : API GraphQL publique sur les collectifs **et leur financement** — intéressant, relie une organisation à son argent.

**Écarté**

- **Change.org / Avaaz** : aucune API officielle, uniquement des jeux de données universitaires et des scrapers non officiels.
- **Maigret MCP, Xquik, Clearfront, AnySite** : fonction principale = fouiller des comptes et identifiants d'individus. Contraire à la décision du 20/10.
- **OpenGraph Intel, Flowsint** : redondants avec le choix déjà acté d'exporter en GEXF/GraphML pour Gephi. Flowsint embarque Maigret.

---

### 6. Lieux

**À ajouter**

| Source | Apport | Coût / clé |
| :-- | :-- | :-- |
| [OpenAddresses](https://results.openaddresses.io/) | Adresses, bâtiments et parcelles issus de sources officielles. Complète OSM là où OSM est pauvre en adresses. | Gratuit, sans clé |
| [GeoNames](https://www.geonames.org/) | Millions de lieux nommés, hiérarchie administrative, **variantes de noms**, codes pays. Dumps libres. | Gratuit (nom d'utilisateur pour l'API). Licence dépendante du producteur amont si redistribution — à vérifier par pays. |
| [geoBoundaries](https://www.geoboundaries.org/) | Limites administratives mondiales. Alimente directement les champs pays/région/département/ville de la page Recherches. | Gratuit, sans clé |
| [Photon](https://photon.komoot.io/) ([dépôt](https://github.com/komoot/photon)) | Géocodage et autocomplétion sur OSM, **tolérant aux fautes de frappe** là où Nominatim est strict. Auto-hébergeable comme Overpass. | Gratuit. Politique d'usage de l'instance publique à vérifier avant d'y envoyer du volume. |
| [university-domains-list](https://github.com/Hipo/university-domains-list) | Universités mondiales **avec leur nom de domaine web**. Le seul qui fait le pont nom d'organisation → site web, donc alimente trouver-sites en aval sans étape intermédiaire. | Gratuit, sans clé. Communautaire → seconde main, mais vérifiable. |

**À surveiller**

- **Géocodeurs de secours sur clé**, compatibles avec le mécanisme de plafond de dépense : [Geoapify](https://apidocs.geoapify.com/docs/geocoding/forward-geocoding/) 3 000/jour, [LocationIQ](https://locationiq.com/) 5 000/jour, [OpenCage](https://opencagedata.com/) 2 500/jour (non commercial seulement), Positionstack 25 000/mois.
- [Pelias](https://github.com/pelias/pelias) : géocodeur auto-hébergeable mélangeant OSM + OpenAddresses + WhosOnFirst + GeoNames en une recherche. Plus complet que Nominatim, nettement plus lourd — seulement si Nominatim/Photon montrent leurs limites.
- [API DATAtourisme](https://api.datatourisme.fr/v1/docs) : 530 000+ points d'intérêt en France, officiel, gratuit sur clé. France seule et orienté tourisme.

**Écarté**

- **Tous les serveurs MCP OpenStreetMap / géocodage** (cyanheads, jagan-shanmugam, NERVsystems/osmmcp, geocode-mcp) : ils enveloppent Nominatim ou Overpass, déjà interrogés en direct sur notre instance. Dépendance tierce, zéro donnée nouvelle.
- **Tout l'axe imagerie et géolocalisation par photo** (Copernicus, NASA Worldview, USGS, OpenAerialMap, Maxar, Mapillary, KartaView, GeoSpy, Picarta, ShadowFinder, SunCalc, Shadowmap) : demande de regarder des images (F07-4 en pause), et grappes cherche des **noms**, pas l'endroit où une photo a été prise.
- **[Bellingcat osm-search](https://github.com/bellingcat/osm-search)** : même motif, et redondant avec Overpass.
- **Wikimapia** : à l'abandon relatif, qualité inégale.
- **Mapbox, HERE, Google Geocoding** : payants et propriétaires, aucun avantage sur l'ensemble gratuit ci-dessus.

---

### 7. Presse

**À ajouter**

| Source | Apport | Coût / clé |
| :-- | :-- | :-- |
| [Media Cloud](https://www.mediacloud.org/documentation/search-api-guide) | 200 M+ articles, ~20 langues, **+ un annuaire de sources et de flux par pays interrogeable par API**, client Python officiel. Deux outils en un. | Gratuit : **4 000 requêtes/semaine** |
| [API Radio France](https://www.data.gouv.fr/dataservices/api-radio-france) | Grilles, émissions, podcasts, **invités**, résumés. Première main, francophone. | 1 000/jour, accès sur demande. Interdit pour une appli d'agrégation de flux live ou de podcasts — notre usage (recherche) est hors de cette interdiction. |
| [The Guardian Open Platform](https://open-platform.theguardian.com/) | Archive complète, texte intégral | Clé gratuite |
| [New York Times APIs](https://developer.nytimes.com/) | Archive, recherche d'articles | Clé gratuite |
| [radio-browser.info](https://publicapis.io/radio-browser-api) | Annuaire mondial des radios : nom, pays, langue, site web. Pas pour les articles — pour **construire la liste des médias par pays**. | Libre, sans clé |

**À surveiller**

- [Europe Media Monitor / NewsBrief (JRC)](https://data.europa.eu/en/publications/use-cases/europe-media-monitor-newsbrief) : surveillance média multilingue officielle avec flux RSS publics. Très pertinent sur le papier, **mais documentation datée de 2016 et aucune page d'API à jour trouvée**. Ne pas documenter comme fonctionnel sans l'avoir appelé.
- [NewsData.io](https://newsdata.io/pricing) : 200 crédits/jour, mais **48 h d'historique seulement** — fenêtre trop courte pour recouper des noms dans le temps. Veille temps réel uniquement.

**Écarté**

- **[GNews](https://gnews.io/)** : palier gratuit **explicitement non commercial** → risque de licence pour un projet publié en libre.
- **NewsAPI.org** : palier gratuit limité au développement + **délai de 24 h** sur les articles.
- **AFP, Reuters, AP, Belga** : tarification entreprise, hors plafond de dépense.

---

### 8. Personnalisé / outillage

**À ajouter**

| Source | Apport | Coût / clé |
| :-- | :-- | :-- |
| [Serveurs MCP officiels](https://github.com/modelcontextprotocol/servers) | `Fetch`, `Filesystem` (fichiers locaux avec contrôle d'accès), `Git`, `Time`. Correspondent aux types de source déjà déclarés « fichier local » et « programme local ». | Gratuit |
| [trafilatura](https://trafilatura.readthedocs.io/) | Extraction du texte et des métadonnées d'un article, le mieux classé des comparatifs. La brique qui transforme une page récupérée en article propre. | Gratuit |
| [news-please](https://github.com/fhamborg/news-please) | Crawler + extracteur de presse intégré, fonctionne sans réglage | Gratuit |
| [RSSHub](https://docs.rsshub.app/) / [RSS-Bridge](https://github.com/RSS-Bridge/rss-bridge) | Fabriquent un flux RSS pour les sites qui n'en ont pas. Le chaînon manquant : transforme n'importe quel site en source RSS consommable par la brique OPML déjà en place. | Gratuit, auto-hébergés |
| [changedetection.io](https://github.com/dgtlmoon/changedetection.io) | Surveille les changements d'une page et **en sort du RSS**. Permet de surveiller un registre ou un média sans API et de le brancher comme source RSS. | Gratuit, auto-hébergé |

**Retenus en secours uniquement** (décision du 21/10)

- **OpenRegistry** (27 registres nationaux, palier gratuit puis 29 $/mois) et **Registry Lookup / Veridion** (521 M d'entités, 5 000 appels/mois gratuits) : relais tiers non identifiés qui republient des registres officiels. À utiliser **seulement quand aucun registre direct n'existe pour un pays**, et toujours marqués seconde main.

**En attente de la décision disque**

La VM de momo a 100 Go, Overpass en occupe déjà 19. Les deux candidats ci-dessous se disputent les 81 Go restants :

- [yente](https://www.opensanctions.org/docs/self-hosted/) — OpenSanctions auto-hébergé, **libre et gratuit** (« we won't charge you to run it »). Fin de la consommation de crédits, vérification illimitée. Besoins : 8 Go de RAM (16 recommandés), 1 vCPU, **60 Go de disque**. Données en CC-BY-NC (non commercial) — compatible avec un projet MIT non commercial, à confirmer sur leur page de licence.
- [Aleph (OCCRP)](https://docs.aleph.occrp.org/) — plateforme libre auto-hébergeable, conçue pour croiser registres d'entreprises + fuites + sanctions, au format `FollowTheMoney` déjà prévu comme export. Ressources à chiffrer.

**À surveiller**

- [ArchiveBox](https://archivebox.io/) : archive localement ce qui a été collecté, pour prouver l'état d'une page à une date.
- [newspaper4k](https://pypi.org/project/newspaper4k) : fork maintenu de newspaper3k, redondant avec trafilatura.
- **DataNexus MCP** ([smithery](https://smithery.ai/servers/dev-7bd0/mcp-server/)) : déclarations 990, marchés publics fédéraux, brevets. US seulement, gratuité non annoncée. Relais tiers.
- [Claudii Exploratores](https://github.com/SOsintOps/claudii-exploratores) : catalogue de 898 outils OSINT exposé en MCP. **Construit des URL de recherche sans aller chercher les pages** → c'est un annuaire, pas une source, et son README le dit non validé pour un usage opérationnel. Utile comme catalogue de veille, pas à brancher.
- [Not Human Search](https://nothumansearch.ai) : moteur de découverte de serveurs MCP. Outil de veille, pas une source.

**Non retenus pour le moment** (décision du 21/10)

- **[SpiderFoot](https://github.com/smicallef/spiderfoot)** (MIT, auto-hébergeable, 200+ modules) : bon candidat pour le type « programme local », **mais** une partie de ses modules visent des personnes et des emails — le brancher sans désactiver explicitement ces modules ferait rentrer par la porte de derrière ce qui a été retiré le 20/10. Reporté.
- **[Fylings MCP](https://github.com/HeyZod/fylings-mcp)** (18 registres officiels africains, sans clé, chaque réponse portant le registre d'origine, la date de vérification et un indice de confiance) : reporté jusqu'à confirmation du tarif, non annoncé. **Conséquence : l'Afrique reste sans couverture** — aucune autre source trouvée ne la couvre.

---

### Point important : un type de source déclaré n'a plus de serveur officiel

Les serveurs MCP officiels **PostgreSQL et SQLite sont archivés**. Le type de source déjà déclaré « base perso en lecture seule » n'a donc plus d'implémentation officielle : il faudra un tiers ou la nôtre. À savoir avant de s'appuyer dessus.

### Deux conclusions de méthode

**Sur l'OPML, le résultat est négatif — et c'est une bonne nouvelle.** Il n'existe aucune liste OPML de médias francophones ou multi-pays correctement maintenue. La réponse n'est donc pas un fichier OPML à entretenir soi-même (qui pourrit), mais l'annuaire de sources de **Media Cloud** (par pays, tenu à jour par eux) + **radio-browser** + **RSSHub** pour les sites sans flux. Côté associations, aucun registre officiel ne publie en RSS : ils publient des fichiers rafraîchis (DILA, data.gov.au, data.gov.ie, CRA). Les agendas d'événements locaux n'ont pas de source mondiale — portail open data par ville, donc à traiter via le type « source personnalisée ».

**Sur l'axe MCP, méfiance.** Il n'existe aucun serveur MCP officiel pour la presse, le RSS ou les registres. Ceux des annuaires (mcp.so, glama.ai, lobehub, smithery) sont des projets individuels non vérifiés — même réserve que OpenRegistry : pratiques pour tester, pas des sources de confiance. Les seuls dignes de la liste sont **Fetch** (officiel) et **Parallel Search** (opérateur identifié, sans clé).

### Veille à reconsulter plutôt que de refaire cette recherche

- [soxoj/awesome-osint-mcp-servers](https://github.com/soxoj/awesome-osint-mcp-servers) — 34 serveurs MCP OSINT, MIT, tenu par l'auteur de Maigret.
- [osintshifu/awesome-osint-repos](https://github.com/osintshifu/awesome-osint-repos) — suit en continu les outils OSINT et les serveurs MCP.
