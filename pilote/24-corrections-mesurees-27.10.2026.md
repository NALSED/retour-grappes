# 24 — Sept corrections, chacune avec sa preuve dans le code

Consigne pour momo, écrite après une **lecture du dépôt réel** (clone à jour du 27/10/2026, après la
livraison v1), pas après une lecture des documents. Chaque point dit : ce que le code fait aujourd'hui,
avec le fichier et la ligne ; pourquoi c'est un problème ; la correction ; et le test qui prouve qu'elle
est faite.

Rien ici n'invente de méthode : chaque correction réemploie un mécanisme **déjà présent dans le dépôt**,
ou une norme nommée. Quand la correction consiste à rebrancher quelque chose que grappes possède déjà,
c'est dit.

---

## Ce qui est déjà fait — ne le refais pas

La lecture du code annule trois points du document 23, faits depuis :

| Point du doc 23 | État réel au 27/10 |
| :-- | :-- |
| « Construire ENRICHIR » | **Fait.** `pilote/src/pilote/pont/verification.py` l. 57-61 : les champs en plus vivent dans `faits.enrichissements`, avec leur source, sans jamais écraser (règle 1). Trois sources le portent. |
| « Recalibrer sur des cas français » | **Fait** dans sa mécanique : `pilote/autocal_rapprochement_fr/cas.jsonl`, 25 cas, 13 « différentes » / 12 « même », rejouable par `outils/mesurer_rapprochement_fr.py`. Il manque une classe de cas : voir le point 3. |
| « Désambiguïser les noms communs » | **Fait** pour les copies locales : `pilote/src/pilote/fichier_local.py` l. 314 (`homonymes`) et l'adaptateur l. 64-67, qui dit « n entités portent exactement ce nom : précisez la ville ». |

Et une correction d'une erreur que j'ai écrite dans le document 23 : j'ai dit que le recoupement à deux
écosystèmes bloquait les résultats montrés à l'utilisateur. **C'est faux.**
`grappes/src/grappes/classement/production.py` l. 96 part de `fusion.fusionner(ctx, ctx.decouverts, …)`,
qui recopie **tous** les candidats sans filtrer sur leur statut (`fusion.py` l. 50). Le recoupement à deux
écosystèmes ne commande que deux choses : l'export `resultat.csv` du moteur seul, et la boucle
d'apprentissage. Voir le point 7, qui est une question pour Sednal, pas un travail pour toi.

---

## 1. Un compte est une personne dans le moteur, et ne l'est pas dans pilote

**Le plus important de ce document. Il touche la règle n° 8, la seule non négociable qui relève du droit.**

### Ce que le code fait aujourd'hui

Le moteur a une règle explicite, écrite en tête de `grappes/src/grappes/personnes.py` :

> « Un compte (X, Instagram) est une personne par défaut. Il devient « institutionnel » (type `group`)
> seulement si son nom lisible contient un indice explicite (association, journal, radio, mairie...).
> **En cas de doute, ou sans nom, on le traite comme une personne.** »

Elle est appliquée dans le moteur, à `grappes/src/grappes/clustering/assign.py` l. 247
(`if etype is EntityType.PERSON and is_institution(…)`).

Côté pilote, la chaîne d'un compte trouvé par une source est celle-ci :

| Étape | Fichier | Ce qui se passe |
| :-- | :-- | :-- |
| 1 | `pilote/src/pilote/adaptateurs/mcp_generique.py` l. 56 | `genre = str(p.get("genre", "compte"))` — le genre par défaut d'un résultat MCP est **« compte »** |
| 2 | `pilote/src/pilote/pont/executer.py` l. 167-173 | `vers_decouvert` place ce genre dans `Decouvert.type_candidat` |
| 3 | `pilote/src/pilote/pont/faits.py` l. 108 et 118 | il devient `genre_de_chose` de la fiche |
| 4 | `pilote/src/pilote/taches.py` l. 52 | il est enregistré tel quel sur le `Candidat` |
| 5 | `pilote/src/pilote/rapprochements.py` l. 81 | le filtre écarte les candidats dont `genre_de_chose` vaut **exactement « personne »** |

« compte » n'est pas « personne ». **Le compte passe le filtre, et son nom devient une clé de
rapprochement dans Splink.** Dix sources déclarent `genres: [compte]` : apify-facebook, apify-instagram,
arctic-shift, bluesky, brightdata-recherche, lemmy, mastodon, reddit, sepiasearch, telegram-annuaires.

### Pourquoi c'est un problème

Les deux moitiés du programme tiennent le **défaut inverse** sur le même objet. Le moteur dit « sans
indice institutionnel, c'est une personne » ; pilote dit « c'est un compte, donc ce n'est pas une
personne ». Un compte Instagram nommé d'après une personne physique devient donc une fiche normale, et
son nom sert de clé de liaison entre enregistrements — exactement ce que la règle n° 8 interdit, et
exactement ce que **NIST SP 800-188** (*De-Identifying Government Datasets*, déjà cité en tête de
`tests/test_regle8_personnes_morales.py`) nomme comme l'interdit central : un quasi-identifiant ne sert
jamais de clé de liaison.

Ce n'est pas une faute de raisonnement de ta part : le filtre de `rapprochements.py` l. 81 est juste, il
lui manque seulement de recevoir la bonne valeur. Et ce point commande directement le branchement des
prestataires de collecte (Apify, Bright Data) cadré le 27/10 dans
`17-sources-a-implementer.md` § 5 : sans lui, ce cadrage repose sur la discipline de l'utilisateur ; avec
lui, il repose sur le code.

### La correction

Appliquer la règle que le moteur possède déjà, au moment où un résultat de genre « compte » devient un
candidat. `grappes.personnes.is_institution` est importable par le pont (`executer.py` tourne dans
l'interpréteur de grappes et importe déjà `grappes.personnes` indirectement) :

- un résultat dont le genre est « compte » **et** dont le nom ne porte aucun indice institutionnel
  (`is_institution(nom)` faux) reçoit `genre_de_chose = "personne"` ;
- le filtre existant de `rapprochements.py` l. 81 l'écarte alors de lui-même, sans le modifier ;
- la fiche **reste affichée** — on ne supprime rien (règle 1) : elle n'est simplement jamais candidate à
  un rapprochement, et le libellé de son genre le dit à l'écran.

Ne duplique pas la liste d'indices : `DEFAULT_INSTITUTION_HINTS` est dans
`grappes/src/grappes/personnes.py` l. 18-26, c'est elle qui sert.

### Le test qui le prouve

Dans `tests/test_regle8_personnes_morales.py` :

1. un résultat de genre « compte » nommé « Marie Dupont » n'apparaît dans **aucune** paire de
   rapprochement ;
2. un résultat de genre « compte » nommé « Association des Amis du Vercors » y apparaît normalement ;
3. le même contrôle sur un compte **sans nom lisible** (identifiant seul) : écarté, comme le dit la règle
   du moteur (« en cas de doute, ou sans nom »).

---

## 2. Les acronymes sont jetés par les sources en ligne, gardés par les copies locales

### Ce que le code fait aujourd'hui

Les copies locales portent toutes les autres noms d'une entité, et les interrogent :

| Copie locale | Colonne d'alias versée |
| :-- | :-- |
| `sirene-local.yaml` | `sigleUniteLegale`, `denominationUsuelle1/2/3UniteLegale` |
| `rna-local.yaml` | `titre_court` |
| `wikidata-fr.yaml` | `alias` |
| `geonames-fr.yaml` | `asciiname`, `alternatenames` |
| `opensanctions-local.yaml` | `aliases` |

`pilote/src/pilote/fichier_local.py` l. 293-301 (`exact`) cherche dans une table `noms` qui contient le
nom **et** ses alias. Une copie locale trouve donc « CEA ».

Les sources **en ligne** n'ont nulle part où mettre ces noms :

- `pilote/src/pilote/adaptateurs/ror.py` l. 17-22 : `_nom()` ne garde que le nom de type `ror_display`
  et jette tout le reste. Or ROR v2 documente quatre types de noms — `acronym`, `alias`, `label`,
  `ror_display` ([ROR, structure des données](https://ror.readme.io/v2/docs/data-structure)) : « CEA »
  est dans un nom de type `acronym`, et il est jeté.
- `grappes/src/grappes/decouverte/registres.py` l. 37-47 : le `Decouvert` **n'a pas de champ d'alias**.
  La convention de sortie des adaptateurs (lue par `vers_decouvert`, `executer.py` l. 167-173) ne prévoit
  que `nom, numero, ville, site, origine, genre, relations, coordonnees, fonction`.
- la requête Wikidata du moteur (`registres.py` l. 71-75) sélectionne `?itemLabel` seulement, jamais
  `skos:altLabel` : les alias de Wikidata (où « CEA » figure) ne sont même pas demandés.

### Pourquoi c'est un problème

C'est la cause de l'unique échec franc du test réel, écrit dans `pilote/docs/v1.md` : « 1 échec (CEA,
acronyme dans ROR) ». Et c'est un problème général, pas un cas isolé : une même entité trouvée par une
copie locale et par une source en ligne ressort en **deux fiches** qui ne se rejoignent pas, parce que
`fusion._groupes` (`grappes/src/grappes/classement/fusion.py` l. 27-45) regroupe par SIREN sinon par nom
normalisé — et « CEA » n'est pas « Commissariat à l'énergie atomique et aux énergies alternatives ».

Le seul mécanisme qui pourrait les réunir, le regroupement par variantes de nomenklatura, est **éteint**
(`grappes/config/classement.yaml`, `variantes.actif: false`, avec sa raison : « le regroupement enchaîne
des noms sans rapport »). Cette décision est bonne : elle écarte une ressemblance floue. L'alias est la
version sûre de la même chose — un rapprochement par **égalité exacte** sur un nom qu'un registre déclare
lui-même, jamais par ressemblance.

### La correction

1. Ajouter un champ `alias: list[str]` à la convention de sortie des adaptateurs et à `Decouvert`
   (`registres.py` l. 37), et le faire traverser `vers_decouvert`.
2. Le remplir là où le service le donne : ROR (noms de type `acronym` et `alias`), Wikidata
   (`skos:altLabel`, à ajouter à la requête SPARQL).
3. Comparer sur l'**ensemble** des noms, pas sur le seul nom principal. Le motif existe déjà dans le
   dépôt, bien écrit : `pilote/src/pilote/adaptateurs/opensanctions.py` l. 31-32 compare le nom voulu à
   `{caption} ∪ properties.name`. C'est exactement ce qu'il faut généraliser.
4. Faire entrer les alias dans le regroupement de `fusion._groupes` : même SIREN, sinon **un nom en
   commun** (nom ou alias), sinon deux entités distinctes.

Pour GLEIF, la réponse d'exemple enregistrée (`config/sources/exemples/gleif.json`) est une réponse
factice qui ne porte que `legalName` : vérifie le nom exact du champ des autres noms sur une réponse
réelle avant de le brancher, ne le devine pas.

### Le test qui le prouve

1. ROR interrogé sur « CEA » rend une fiche dont les alias contiennent « CEA » et dont le nom est le nom
   complet ;
2. la même entité trouvée par `sirene-local` (par son sigle) et par ROR (par son nom complet) ne fait
   **qu'une** ligne dans le résultat, pas deux ;
3. deux entités qui partagent un alias mais pas de SIREN et aucun nom commun ne sont **pas** réunies
   (contrôle négatif : l'alias ne doit pas devenir une ressemblance).

---

## 3. Le jeu de cas français ne contient pas la classe de cas qui a échoué

### Ce que le jeu contient aujourd'hui

`pilote/autocal_rapprochement_fr/cas.jsonl`, 25 cas, bien équilibré en nombre (13 « différentes », 12
« même »). Mais les deux moitiés ne mesurent pas la même difficulté :

- les 13 « **différentes** » sont presque toutes des relations **mère-fille** : fédération contre club
  affilié, structure nationale contre fédération départementale, fondation contre mouvement. C'est la
  bonne classe, c'est celle du faux positif réel (Fondation Abbé Pierre / Emmaüs).
- les 12 « **même** » sont presque toutes des variantes **typographiques** du même nom : casse, accents,
  forme juridique en tête, sigle entre parenthèses, article, faute de frappe. Une seule est un vrai cas
  sémantique (« Ligue contre le cancer » / « La Ligue nationale contre le cancer »).

**La classe qui manque est précisément celle qui a échoué au test réel** : le sigle et le nom développé
écrits comme deux chaînes sans rapport — CEA / Commissariat à l'énergie atomique et aux énergies
alternatives, CNRS / Centre national de la recherche scientifique, AP-HP / Assistance publique –
Hôpitaux de Paris, INRAE, INSERM, ONF. Normaliser la casse et les accents ne rapproche jamais ces deux
chaînes : c'est un autre problème que celui que le jeu mesure.

### La correction

Ajouter cette classe de cas positifs au jeu, puis le rejouer. **Et ne pas la faire passer en baissant le
seuil de Splink** : ces cas doivent passer grâce aux alias (point 2), c'est-à-dire parce qu'un registre
déclare que « CEA » est un nom de cette entité — pas parce qu'on a rendu le rapprochement plus tolérant.
Un seuil baissé ferait remonter les faux positifs mère-fille que les 13 autres cas sont là pour retenir.

La règle du `LISEZMOI.md` du jeu est la bonne et reste : tout faux positif ou négatif rencontré en usage
réel rejoint le jeu, on n'ajuste jamais un seuil sur un seul exemple.

### Le test qui le prouve

`outils/mesurer_rapprochement_fr.py` rend, après ajout : la précision **ne baisse pas** sur les 13 cas
« différentes », et les nouveaux cas sigle/nom développé passent. Si l'un des deux ne tient pas, c'est le
point 2 qui n'est pas fini — pas le seuil qu'il faut bouger.

---

## 4. Un trou d'une ligne : `universites.yaml`

### Ce que le code fait aujourd'hui

`api_json` possède un garde-fou, avec sa raison écrite à `pilote/src/pilote/adaptateurs/api_json.py`
l. 111 : « Sans cela, la première réponse approchante serait prise pour une confirmation. » Il s'active
par le paramètre `correspondance_exacte`.

Une seule source du catalogue l'active : `bodacc.yaml`. Les autres sources qui travaillent sur le nom
sont protégées **dans leur propre adaptateur** (opensanctions l. 31-32, courtlistener l. 27-30,
fichier_local par `fl.exact`) — sauf une :

`config/sources/universites.yaml` porte `roles: [chercher, enrichir]`, `verifie_sur: nom`,
`adaptateur: api_json`, et **pas** `correspondance_exacte`. Son service cherche par sous-chaîne
(`params: {name: '{quoi}'}`), et `verification.py` l. 57-61 retient `trouves[0]`. Le domaine web du
premier résultat approchant est donc attaché à la fiche.

### La correction

Ajouter `correspondance_exacte: true` aux paramètres de `universites.yaml`. Une ligne.

Puis, pour que le trou ne se rouvre pas ailleurs : un test qui parcourt `config/sources/*.yaml` et exige
que **toute** source portant un rôle `verifier` ou `enrichir` avec `verifie_sur: nom` et l'adaptateur
`api_json` porte `correspondance_exacte: true`. Le garde-fou existe, il doit être obligatoire là où il
s'applique, pas optionnel.

### Le test qui le prouve

Le test de catalogue ci-dessus, plus : l'enrichissement d'une fiche nommée « Université de Grenoble » ne
reçoit jamais le domaine d'une autre université dont le nom contient la même sous-chaîne.

---

## 5. Le seul chiffre qui dirait si le classement est bon n'est pas dans le dépôt

### Ce que le dépôt dit aujourd'hui

`grappes/config/calibration.json` est présent et honnête :

> « Pas de calibration valide : l'indice n'a pas de pouvoir discriminant mesurable contre la référence
> (Brier validation 0,0234 contre 0,0229 pour le prédicteur constant). Le score affiché est
> p = LR/(1+LR) […], une estimation étendue, jamais une mesure. »

J'ai refait le calcul sur les chiffres de l'ajustement enregistré dans ce même fichier (5 215 points,
87 positifs) : le taux de base est 0,016683, et **p(1−p) = 0,01640434 — exactement le `brier_constant`
enregistré**, au dernier chiffre. Avec une ECE de 0,0008 (calibration quasi parfaite après Platt), la
décomposition de Murphy du score de Brier, `BRS = fiabilité − résolution + incertitude`
([Murphy, reprise moderne](https://arxiv.org/pdf/2106.14345)), donne une **résolution d'environ
0,00006 pour une incertitude de 0,0164 : l'indice explique 0,36 % de la variance**.

Donc : **la mesure est juste, et la conclusion du fichier est juste** pour ce qu'elle mesure. Comme
*probabilité qu'un candidat soit un acteur réel du sujet*, l'indice n'apporte à peu près rien. Et le
programme en tire la bonne conséquence, deux fois : le score est étiqueté « estimation étendue »
(`production.py` l. 111), et surtout **il n'est pas montré à l'utilisateur** — `pont/faits.py` l. 113-125
ne rend aucun score, seulement les preuves (origine, sources, nombre d'endroits). C'est le bon choix de
conception et il faut le garder.

### Pourquoi il manque quand même quelque chose

L'indice a un **second** emploi, qui n'est pas celui-là : il **ordonne la liste de résultats**.
`production.classer` l. 106 trie par log-LR décroissant, et `executer.py` l. 296-303 parcourt la liste
dans cet ordre et coupe à `combien`. **Ce qui apparaît à l'écran, et ce qui est coupé, dépend donc de
cet ordre.**

Or ordonner et estimer une probabilité sont deux questions différentes. Une résolution de Brier quasi
nulle sur une tâche où 98,3 % des points sont négatifs ne dit **pas** que l'ordre est mauvais : le Brier
est dominé par la masse des négatifs, alors que l'ordre ne se juge que sur les premiers rangs. La mesure
qui répond à cette question-là existe, elle est déjà branchée, et c'est le critère n° 1 de la
certification : la **R-précision**, calculée par `pytrec_eval` (binding de `trec_eval`, NIST) dans
`grappes/src/grappes/autocal/metriques.py`.

Cette valeur n'est **pas dans le dépôt**. `autocal/test_ouvert.json` prouve que la mesure a eu lieu
(ouverte le 2026-10-12T16:54:55Z, finie à 19:05:27Z, empreinte de rejeu enregistrée), mais
`autocal/resultats/`, `donnees/autocal/test_rejeu1.json`, `docs/CERTIFICATION.md` et
`docs/conception/calibration.md` sont tous absents. Et `.gitignore` (6 lignes : `.venv/`,
`__pycache__/`, `.pytest_cache/`, `.ruff_cache/`, `*.egg-info/`, `secrets.env`) n'en exclut **aucun** :
ils n'ont pas été écartés, ils n'ont jamais été ajoutés.

### La correction

1. **Publier la R-précision et le bpref du test du 12/10** — tu les as sur la VM, dans
   `donnees/autocal/test_rejeu1.json`. C'est la seule façon de savoir si l'ordre des résultats vaut
   quelque chose. Un seul chiffre, et la question est tranchée dans un sens ou dans l'autre.
2. Rendre cet ordre **remesurable à volonté**, sur un jeu rejouable, **sans toucher au jeu test de
   l'autocal** (ouvert et consommé le 12/10, jamais à rouvrir). Le modèle à copier est celui que tu as
   écrit hier pour Fusion/doublons : `autocal_rapprochement_fr/` + `outils/mesurer_rapprochement_fr.py`,
   un jeu qui grossit à chaque erreur rencontrée. Même forme, pour le classement, avec les mesures de
   `metriques.py` qui sont déjà là.
3. Si la R-précision est bonne : le dire dans `v1.md`, à côté de la ligne sur la calibration, pour qu'on
   ne lise plus « l'indice n'a pas de pouvoir discriminant » comme un verdict sur l'ordre des résultats,
   alors que ça n'en est pas un. Si elle est mauvaise : c'est un vrai sujet, et il remonte à Sednal.

### Le test qui le prouve

Un `outils/mesurer_classement.py` qui rend la R-précision sur un jeu français rejouable, et une ligne
dans `v1.md` qui porte le chiffre.

---

## 6. Une recherche de tests verte ne dit pas combien de tests ont tourné

### Ce que le code fait aujourd'hui

470 fonctions de test dans `pilote/tests/`. **30 points de saut** (`pytest.skip` / `skipif`), dont **29
dépendent du réseau ou d'un service extérieur** : « pas de réseau », « portails injoignables », « aucun
serveur Overpass ne répond », « SearXNG ne tourne pas sur cette machine », « node absent », « playwright
absent »…

Un cas précis mérite d'être nommé, parce que c'est le plus mauvais endroit possible pour un saut
silencieux. `tests/test_regle8_personnes_morales.py` l. 35 :

````python
@pytest.mark.skipif(not Path("/opt/grappes/config/personnes.yaml").exists(), reason="le dépôt grappes n'est pas à côté")
````

C'est le test qui vérifie que la collecte de personnes physiques reste éteinte dans le moteur. Il ne
demande aucun réseau — il lit un fichier local — mais il est attaché à un chemin **écrit en dur**. Hors
de la VM (ailleurs, en CI, sur un clone frais), il se saute sans bruit, et la suite reste verte. Au même
moment, `tests/test_f01_reglages.py` l. 196 interdit précisément les chemins en dur `"/opt/` et `"/home/`
ailleurs dans le projet.

### Pourquoi c'est un problème

« 500 tests passés » ne dit pas combien ont été **exécutés**, et le nombre change d'un jour à l'autre
selon la disponibilité du réseau. Pour une livraison, c'est le genre de chiffre qui donne une confiance
qu'il ne mesure pas.

### La correction

1. Le test de la règle n° 8 lit la racine de grappes **par la configuration**, comme le fait déjà
   `pilote/src/pilote/grappes_lib.py` (`cfg.grappes_racine`), et **n'a plus de `skipif`** : il doit
   toujours tourner. S'il ne trouve pas la configuration, il échoue — il ne se saute pas.
2. Séparer la suite en deux : ce qui doit **toujours** tourner (aucun réseau), et ce qui dépend d'un
   service extérieur (une marque pytest, `-m "not reseau"`). Un verrou de forme :
   `--strict-markers` dans `pyproject.toml`, pour qu'une marque mal écrite échoue au lieu d'être ignorée.
3. Faire porter au verdict de livraison le compte des **deux** nombres : passés **et** sautés. Dans
   `v1.md`, « 500 passés » devient « N passés, M sautés (réseau) ».

### Le test qui le prouve

Le test de règle 8 tourne sur un clone sans `/opt/grappes` ; `pytest -m "not reseau"` passe entièrement,
sans un seul saut.

---

## 7. Question pour Sednal, pas un travail pour toi : la boucle d'apprentissage ne peut pas se déclencher

Pour apprendre un mot, `grappes/src/grappes/cycle/learn.py` exige **trois** entités « confirmées »
(`autonomy.py` l. 67, `min_confirmees: 3`), chacune recoupée par des citants de **deux écosystèmes
distincts** (l. 68, `min_ecosystemes: 2`), chacune rattachée à une racine de confiance en deux pas au
plus (l. 75).

Le moteur constate lui-même que la condition ne se remplit pas, dans `docs/conception/cycle.md` l. 61 :

> « le recoupement exige des citants d'au moins 2 écosystèmes ; un élément RSS ne cite personne et peu de
> pages web sont ingérées, **donc aucune entité n'est encore confirmée** ».

Conséquence exacte, maintenant que le point est vérifié : les résultats de pilote ne sont **pas**
affectés (voir la correction en tête de ce document), mais le mécanisme par lequel le programme devait
s'améliorer tout seul **ne s'est jamais déclenché et ne se déclenchera pas** dans les conditions
actuelles.

Ce n'est pas un défaut à corriger seul : les garde-fous de cette boucle (une seule voix par éditeur, la
racine de confiance, le témoin piégé décrit à `cycle.md` l. 29) sont bien conçus, et les baisser est un
choix de fiabilité qui appartient à Sednal. Les deux voies, à lui de trancher :

- **la laisser éteinte** et assumer que l'amélioration passe par le Tri (une personne valide), ce qui est
  cohérent avec la règle n° 6 ;
- **ou** élargir ce qui compte comme écosystème citant, en branchant des sources qui **citent** vraiment
  (un flux RSS ne cite personne), et mesurer si des entités se confirment alors.

Remonte la question, ne la tranche pas.

---

## Dans quel ordre

| # | Correction | Ce que ça coûte | Ce que ça achète |
| :-- | :-- | :-- | :-- |
| 1 | Un compte est une personne sans indice institutionnel | petit (la règle existe déjà dans le moteur) | la règle n° 8 tenue par le code et non par l'utilisateur ; les prestataires de collecte branchables sans risque |
| 4 | `correspondance_exacte` sur `universites.yaml` + test de catalogue | une ligne + un test | plus aucune confirmation sur un nom approchant |
| 2 | Les alias dans la convention des adaptateurs | moyen | l'échec CEA corrigé ; plus de fiches en double entre copie locale et source en ligne |
| 3 | La classe sigle/nom développé dans le jeu français | petit, après le 2 | la correction du 2 est prouvée et le reste |
| 5 | Publier la R-précision, rendre l'ordre remesurable | petit pour publier, moyen pour le jeu | on sait enfin si l'ordre des résultats vaut quelque chose |
| 6 | Le règle 8 sans `skipif`, deux nombres au verdict | petit | un vert qui veut dire quelque chose |
| 7 | — | — | question à Sednal |

Les points 1 et 4 d'abord : ce sont les deux seuls où le code laisse passer aujourd'hui quelque chose
qu'il est censé retenir.

## Et les documents absents du dépôt

Onze documents sont cités par le code et la configuration, et ne sont pas dans le dépôt publié :
`DECISIONS.md`, `DECISION_AUTOCAL.md`, `DECISION_SOURCES.md`, `ECONOMIES.md`, `FINAL.md`, `QUESTIONS.md`,
`SORTIE_FINALE.md`, `docs/CERTIFICATION.md`, `docs/conception/calibration.md`,
`docs/conception/personnes.md` (et `dernier_cycle.md`, qui est une sortie, pas un document).

`.gitignore` n'en exclut aucun. Conséquence : chaque « D64 », « D70 », « FINAL.md § 9 », « MISSION
§ 10.2 » écrit dans le code renvoie à un texte introuvable, et le verdict de certification du 12/10 n'est
pas vérifiable depuis le dépôt. Pousse-les, ou remplace la référence par ce qu'elle dit.

## Où regarder

- `17-sources-a-implementer.md` § 5 — le cadrage des prestataires de collecte, que le point 1 rend sûr
- `20-recalibrage-rapprochement-fr-27.10.2026.md` — le jeu de cas français, que le point 3 complète
- `23-vers-le-produit-fini-27.10.2026.md` — ses points 2 et 4 sont faits, voir la table en tête d'ici
- `pilote/docs/v1.md` — la livraison, à amender par les points 5 et 6
