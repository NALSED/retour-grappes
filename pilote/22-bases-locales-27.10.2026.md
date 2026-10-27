# 22 — Bases à rapatrier en local, par catégorie

Consigne pour momo. Demande de Sednal le 27/10/2026 : partout où une source existe aussi en **dump
téléchargeable**, on prend la copie locale et grappes l'interroge sans appel réseau — pas de jetons
brûlés, pas de quota d'API, pas d'appel croisé qui coince. On veut pouvoir **brancher n'importe quelle
base comme on branche une API**.

Ce document ne remplace pas `17-sources-a-implementer.md` (les API qui n'ont pas de dump y restent). Il
dit, pour chaque catégorie, ce qui existe en téléchargement, sa taille, et comment grappes s'en sert en
local.

## Périmètre : la France, pour l'instant

**Tranché par Sednal le 27/10/2026 : on se concentre sur la France pour toutes les sources.** Partout où
une base existe en version mondiale et en sous-ensemble français, on prend **le sous-ensemble français**
(GeoNames FR, Wikidata organisations situées en France, OSM France, geoBoundaries France). Les versions
mondiales et l'étage « monde entier » décrit plus bas sont **reportés** — n'y touche pas sans remonter la
question. Conséquence directe : les tailles s'effondrent, et tout tient très largement dans le disque
actuel.

Deux exceptions qui restent mondiales parce qu'elles sont petites et n'ont pas de sens « franco-français » :
**OpenSanctions** (les sanctions ne sont pas un sujet national, 450 Mo) et **GLEIF** (filtrable sur les
LEI français, mais le fichier entier ne fait que 523 Mo — le filtre n'en vaut probablement pas la peine).

## Trois règles qui valent pour tout ce document

1. **Une base locale est une source comme une autre.** Elle s'inscrit dans `config/sources/` avec sa
   fiabilité (A–F), sa langue, son rôle. La seule différence avec une API : le moteur lit un fichier au
   lieu d'appeler le réseau.
2. **Jamais un deuxième moteur de rapprochement.** Comme pour OpenSanctions (`21`), une base locale
   apporte des *données*, pas un juge. Le rapprochement reste dans Fusion/doublons, mesuré une seule fois.
3. **La fiabilité se note sur l'origine, pas sur le moyen d'accès.** Télécharger SIRENE ne le rend ni plus
   ni moins fiable que l'API SIRENE : c'est la même donnée officielle (A ou B selon le cas). Un
   agrégateur reste un agrégateur même en local (OpenSanctions = B).

## Le mécanisme générique : un type de source « fichier local »

Pour « brancher n'importe quelle db », il faut **un seul type de source réutilisable**, pas un
branchement sur mesure par base. Déclaré dans `config/sources/`, type `fichier_local`, trois formats
d'entrée suffisent à couvrir tout ce document :

- **CSV / TSV** (SIRENE, RNA, OpenSanctions, GLEIF, GeoNames…)
- **JSONL** (un objet par ligne — Wikidata filtré, exports FollowTheMoney…)
- **SQLite déjà constitué** (quand on a préchargé la base une fois)

Le chargement se fait une fois (import → table SQLite indexée), puis grappes interroge la table. Un
nouveau dump = un nouveau fichier dans le dossier + une ligne de config. C'est ça, « brancher n'importe
quelle db ». **C'est la vraie demande de Sednal — la construire d'abord, le reste n'est que des fichiers
à y verser.**

---

## 1. Entreprises

| Base | Ce qu'elle apporte | Format | Taille (téléchargement) | Maj |
| :-- | :-- | :-- | :-- | :-- |
| **SIRENE** (stock entreprises + établissements) | toutes les entreprises et établissements français, SIREN/SIRET, adresses, activité, état | CSV (zip) | **≈ 4–5 Go** zip, **≈ 10–15 Go** en base indexée | mensuel + quotidien |
| **GLEIF LEI** (concatenated full file) | **3,47 M** d'identifiants légaux mondiaux (LEI), raison sociale, pays, maison mère | XML / CSV | **523 Mo** (vérifié 26/10/2026) | quotidien |
| Registres étrangers (PRH Finlande, Zefix CH…) | selon le pays | variable | petit à moyen | variable |

SIRENE est déjà dans le projet via API ; **le passer en local supprime son quota** et le rend interrogeable
sur n'importe quel volume. C'est la plus grosse pièce française et la plus utile. Fiabilité **A**
(registre officiel, première main).

## 2. Associations

| Base | Ce qu'elle apporte | Format | Taille | Maj |
| :-- | :-- | :-- | :-- | :-- |
| **RNA** (Répertoire National des Associations) | toutes les associations déclarées en France | CSV (waldec) | **≈ 1 Go** | mensuel |
| IRS BMF / 990 (US), ACNC (AU), CRA (CA)… | associations/charities par pays | CSV/JSON | moyen par pays | variable |

RNA en local = toutes les assos françaises interrogeables sans quota. Fiabilité **A**.

## 3. Organisations officielles et identifiants

| Base | Ce qu'elle apporte | Format | Taille | Maj |
| :-- | :-- | :-- | :-- | :-- |
| **Wikidata** (extrait « organisations » uniquement) | entités, noms multilingues, alias, identifiants croisés (SIREN, LEI, domaine…) | JSONL filtré | voir avertissement ci-dessous | continu |
| **org-id.guide** | annuaire des registres d'organisations du monde | CSV/JSON | **quelques Mo** | périodique |
| data.europa.eu | catalogue de jeux de données publics UE | catalogue | petit | — |

**Avertissement Wikidata — à ne pas prendre en entier.** Le dump complet fait **1,4 To décompressé**
(vérifié) : hors de question de le charger tel quel. On en prend un **extrait filtré sur les personnes
morales** (classes organisation, entreprise, association et sous-classes) au moment de l'import — l'outil
`wdumper` ou un passage en flux du dump `truthy` permet de ne garder que ça. Résultat attendu : de l'ordre
de **5 à 15 Go** selon la largeur du filtre, pas 1,4 To. **À mesurer à l'import, pas à supposer.** C'est
cohérent avec la règle n°8 : on ne rapatrie même pas les personnes physiques. Fiabilité **C** (collaboratif,
piège de circularité déjà noté dans `grappes/docs/conception/sources.md`).

## 4. Lieux (géocodage et périmètres)

| Base | Ce qu'elle apporte | Format | Taille | Maj |
| :-- | :-- | :-- | :-- | :-- |
| **GeoNames** (allCountries) | noms de lieux du monde, variantes, coordonnées, pays/région | TSV (zip) | **≈ 400 Mo** zip, **≈ 1,5 Go** décompressé | quotidien |
| **OpenStreetMap France** (extrait Geofabrik) | géocodage adresses FR complet | PBF | **≈ 4–4,5 Go** | quotidien |
| **geoBoundaries** (simplifié) | limites administratives (pays/région) | GeoJSON | **≈ 1–2 Go** simplifié | périodique |
| OpenAddresses (France/Europe) | adresses précises | CSV | **France seule : moyen ; monde : > 40 Go** | périodique |
| university-domains-list | université → domaine web | JSON | **quelques Mo** | périodique |

**Prendre les extraits France/Europe, pas le monde**, pour OSM et OpenAddresses : c'est là que les
centaines de Go que tu proposes deviennent vraiment utiles si un jour tu veux le monde entier — mais pour
commencer, la France suffit et tient dans quelques Go. Géocodage local = plus aucun appel à un service
externe (Photon, Nominatim). Fiabilité **B** (OSM collaboratif mais sérieux), **A** pour geoBoundaries
(sources officielles).

## 5. Presse

| Base | Ce qu'elle apporte | Format | Taille | Maj |
| :-- | :-- | :-- | :-- | :-- |
| **GDELT** | événements et articles de presse mondiaux | CSV | **flux continu, To cumulés** | 15 min |
| radio-browser (dump) | annuaire des radios par pays | CSV/SQL | **quelques dizaines de Mo** | quotidien |
| Media Cloud (annuaire de sources) | annuaire de médias par pays | CSV | petit | périodique |

**GDELT ne se prend pas en entier** (c'est un flux, pas un fichier, et ça se compte en téraoctets
cumulés). On en prend une **fenêtre glissante** (les N derniers jours) ou un filtre pays/thème à l'import.
À cadrer avec toi avant de brancher : c'est le seul de la liste où « on prend tout » n'a pas de sens.
GDELT impose sa mention (déjà géré, `SOURCES.txt`). Fiabilité **C/D** (agrégat automatique de presse).

## 6. Sanctions / organisations signalées

| Base | Ce qu'elle apporte | Format | Taille | Maj |
| :-- | :-- | :-- | :-- | :-- |
| **OpenSanctions** (targets.simple.csv) | 1,24 M de cibles (sanctions, PPE, signalements) | CSV | **450 Mo** (déjà tranché, voir `21`) | quotidien |

Déjà décidé. Fiabilité **B** (agrégateur de 478 sources officielles). Rappel : CC-BY-NC, cadre gratuit
tant que l'usage reste personnel (`21`).

## 7. Traduction (modèles locaux)

| Base | Ce qu'elle apporte | Format | Taille | Maj |
| :-- | :-- | :-- | :-- | :-- |
| **Argos Translate / LibreTranslate** (modèles) | traduction hors-ligne, par paire de langues | modèles | **≈ 100–200 Mo par paire** ; jeu ciblé (fr↔en, es, de, it, ar, ru, zh) **≈ 2–3 Go** | stable |

C'est la réponse à la question n°3 (traduction, toujours ouverte) **côté matériel** : LibreTranslate
auto-hébergé ne sort rien sur le réseau, tout est local. Le choix LibreTranslate vs DeepL reste ton
arbitrage (`14-questions-ouvertes.md` §3). Prendre d'abord les paires utiles, pas toutes les langues.

## 8. Web (listes de référence)

| Base | Ce qu'elle apporte | Format | Taille | Maj |
| :-- | :-- | :-- | :-- | :-- |
| **Tranco** | classement de popularité des domaines (anti-bruit) | CSV | **≈ 50 Mo** | quotidien |

Déjà utilisé par grappes au premier lancement (`README.md` du dépôt de code). Fiabilité : outil, pas une
source de résultats.

---

## Ce qui NE peut PAS être pris en local (honnête)

- **OpenCorporates** : pas de dump libre, données sous licence payante pour le téléchargement en masse.
  Reste une API ou rien.
- **Les API des réseaux sociaux** (Mastodon, Bluesky, Mobilizon, Lemmy…) : par nature du temps réel,
  aucun dump. Restent des appels, comme dans `17`.
- **The Guardian, New York Times, Media Cloud (articles), Radio France** : API seulement, pas de dump
  d'articles libre.
- **BODACC** : API avec quota (c'est elle qui portera encore la mention « X sur Y vérifiés » du doc 13).

Pour ceux-là, le local ne change rien : on garde les API de `17`.

---

## Estimation de place — périmètre France (tailles vérifiées le 27/10/2026)

Deux colonnes : le **téléchargement** (ce qui transite par le réseau) et le **sur disque** une fois
chargé en base indexée (1,5 à 3× le CSV décompressé).

| Poste | Téléchargement | Sur disque | Source de la taille |
| :-- | :-- | :-- | :-- |
| SIRENE (stock établissement 2,7 Go + unité légale 1,2 Go) | **≈ 4 Go** zip | ≈ 15–25 Go | vérifié data.gouv |
| RNA (associations FR) | ≈ 0,5–1 Go | ≈ 1–2 Go | estimé |
| GLEIF (entier, ou filtré FR) | 523 Mo | ≈ 1 Go | **vérifié** |
| OpenSanctions (entier) | 450 Mo | ≈ 1–2 Go | **vérifié** |
| GeoNames **FR seul** (FR.zip) | ≈ 20–40 Mo | < 0,5 Go | France ≪ allCountries |
| geoBoundaries **France** | < 0,1 Go | < 0,5 Go | France seule |
| Wikidata **organisations situées en France** | filtré à l'import | ≈ 1–2 Go | France ≪ monde, à mesurer |
| Modèles de traduction (paires utiles) | ≈ 2–3 Go | ≈ 2–3 Go | estimé |
| Tranco, org-id.guide, divers | < 0,1 Go | < 1 Go | — |
| **Sous-total (hors géocodage OSM)** | **≈ 8–10 Go** | **≈ 22–35 Go** | |

En périmètre France, le seul vrai poids reste **SIRENE** (toutes les entreprises françaises). Tout le
reste est devenu petit en se limitant à la France.

**Le seul poste qui demande un arbitrage : le géocodage OSM France.** L'extrait France brut fait
**≈ 4,3 Go** (PBF). S'en servir pour géocoder demande de le charger dans un moteur (Nominatim, Photon),
et là il **gonfle à ≈ 30–40 Go pour la France**. Trois options :

1. Géocodage léger (GeoNames FR seul, pas de moteur OSM) → socle **≈ 22–35 Go**, très à l'aise dans les
   68 Go actuels.
2. Géocodage OSM France complet → **≈ 55–75 Go**, à la limite du disque actuel : c'est là que ta réserve
   de disque sert.
3. Pas de géocodage local pour l'instant → on garde Photon en appel, comme aujourd'hui.

**Socle France réaliste : entre 22 Go (option 1) et 75 Go (option 2).** L'option 1 tient sans rien
ajouter.

## Reporté : l'étage « monde entier »

Hors périmètre pour l'instant (décision France du 27/10/2026). Gardé ici pour mémoire, chiffres vérifiés,
le jour où le périmètre s'élargira — **ne pas brancher sans remonter la question** :

| Poste | Taille | Source |
| :-- | :-- | :-- |
| OSM planet (PBF brut) | **88,5 Go** | vérifié (01/10/2026) |
| OSM planet dans un géocodeur mondial | **≈ 1 To** | ordre de grandeur connu |
| OpenAddresses monde | **≈ 40 Go +** | estimé |
| Wikidata peu filtré / complet | **100 Go +** | dump complet 1,4 To décompressé (vérifié) |
| GeoNames allCountries (monde) | ≈ 1,5 Go | estimé |

C'est pour cet étage que les centaines de Go que tu proposes seraient utiles — pas pour le socle France,
qui tient déjà.

## Ordre proposé

1. **Le type de source `fichier_local`** (le mécanisme générique) — tout le reste en dépend.
2. SIRENE + RNA + OpenSanctions en local — les trois plus utiles, tout de suite sans quota.
3. GeoNames + OSM France — le géocodage local.
4. Wikidata filtré — le plus technique (le filtre à l'import), à faire quand le reste tourne.
5. Modèles de traduction — quand la question n°3 est tranchée.

## Où regarder

- `17-sources-a-implementer.md` — les API sans dump, qui restent des appels
- `21-opensanctions-local-27.10.2026.md` — le premier cas de base locale, déjà tranché
- `05-reglages-sources.md` — la fiche source (où s'inscrit une source `fichier_local`)
- `13-quotas-couts-budget.md` — une source locale affiche *illimité*, pas de quota
