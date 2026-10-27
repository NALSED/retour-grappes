# 25 — Treize cas de sigles français, prêts à verser dans le jeu

Matériau pour le point 3 de `24-corrections-mesurees-27.10.2026.md` : la classe de cas qui manque au jeu
`pilote/autocal_rapprochement_fr/cas.jsonl`, celle du **sigle contre le nom développé** — la classe qui a
produit l'unique échec franc du test réel (« CEA, acronyme dans ROR », `pilote/docs/v1.md`).

Tous les cas ci-dessous viennent du **registre officiel**, pas de ma mémoire : chacun porte son SIREN
réel, relevé dans l'annuaire des entreprises et des associations
([API recherche-entreprises](https://recherche-entreprises.api.gouv.fr/search?q=CEA)), qui est la source
que pilote interroge déjà (`config/sources/recherche-entreprises.yaml`). Ils sont donc vérifiables, un
par un, en rejouant la même requête.

---

## 1. D'abord : le format du jeu doit gagner un champ

Une ligne de `cas.jsonl` porte aujourd'hui `{nom, ville, numero}` par fiche. **Ce format ne peut pas
exprimer un cas de sigle** : il n'a pas d'endroit pour dire « cette entité est aussi connue sous ce
nom-là ». Or c'est exactement l'information dont le rapprochement a besoin, et exactement celle que le
point 2 du document 24 fait remonter des sources.

Ajout proposé : un champ **`alias`**, liste de chaînes, absent ou vide par défaut.

````json
{"id": "…", "verdict": "meme", "a": {"nom": "…", "alias": ["…"], "ville": "…", "numero": "…"}, "b": {…}, "raison": "…"}
````

Les 25 cas déjà écrits restent valides sans modification : `alias` absent se lit comme `[]`.

`outils/mesurer_rapprochement_fr.py` doit alors passer les alias au rapprochement, comme une source les
lui passerait. Sans cette étape, les six premiers cas ci-dessous sont **par construction** impossibles à
réussir, et les mesurer n'apprendrait rien.

## 2. Six cas « même » : le sigle contre le nom développé

Le côté qui porte le sigle **n'a pas de numéro officiel** : c'est le cas réel (ROR, Wikidata, une page
web donnent un sigle, jamais un SIREN). C'est là toute la difficulté — le rapprochement doit tenir sans
l'identifiant qui le rendrait trivial.

````json
{"id": "sigle-cea", "verdict": "meme", "a": {"nom": "CEA", "alias": [], "ville": "Paris", "numero": ""}, "b": {"nom": "Commissariat à l'énergie atomique et aux énergies alternatives", "alias": ["CEA"], "ville": "Paris", "numero": "775685019"}, "raison": "Sigle contre nom développé, sigle déclaré au registre (sigleUniteLegale = CEA). Échec franc du test réel du 26/10 via ROR."}
{"id": "sigle-cnrs", "verdict": "meme", "a": {"nom": "CNRS", "alias": [], "ville": "Paris", "numero": ""}, "b": {"nom": "Centre national de la recherche scientifique", "alias": ["CNRS"], "ville": "Paris", "numero": "180089013"}, "raison": "Sigle déclaré au registre. Résultat partiel du test réel du 26/10."}
{"id": "sigle-inserm", "verdict": "meme", "a": {"nom": "INSERM", "alias": [], "ville": "Paris", "numero": ""}, "b": {"nom": "Institut national de la santé et de la recherche médicale", "alias": ["INSERM"], "ville": "Paris", "numero": "180036048"}, "raison": "Sigle déclaré au registre ; aucun mot commun entre les deux chaînes."}
{"id": "sigle-aphp", "verdict": "meme", "a": {"nom": "AP-HP", "alias": [], "ville": "Paris", "numero": ""}, "b": {"nom": "Assistance publique - Hôpitaux de Paris", "alias": ["AP-HP"], "ville": "Paris", "numero": "267500452"}, "raison": "Sigle à trait d'union, déclaré au registre : la mise à plat de la ponctuation ne doit pas le casser."}
{"id": "sigle-ens", "verdict": "meme", "a": {"nom": "ENS", "alias": [], "ville": "Paris", "numero": ""}, "b": {"nom": "École normale supérieure", "alias": ["ENS"], "ville": "Paris", "numero": "197534597"}, "raison": "Sigle de trois lettres déclaré au registre. Voir le cas homonyme-ens-paris-saint-cloud : ce sigle ne suffit pas seul."}
{"id": "sigle-iram", "verdict": "meme", "a": {"nom": "IRAM", "alias": [], "ville": "Saint-Martin-d'Hères", "numero": ""}, "b": {"nom": "Institut de radioastronomie millimétrique", "alias": ["IRAM"], "ville": "Saint-Martin-d'Hères", "numero": "316501238"}, "raison": "Sigle déclaré au registre, hors Paris : le cas ne doit pas dépendre d'une ville particulière."}
````

## 3. Sept cas « différentes » : ce que le sigle ne doit PAS réunir

### Le cas décisif — un sigle qui est le nom d'une autre entité

````json
{"id": "cea-contre-societe-cea", "verdict": "differentes", "a": {"nom": "Commissariat à l'énergie atomique et aux énergies alternatives", "alias": ["CEA"], "ville": "Paris", "numero": "775685019"}, "b": {"nom": "CEA", "alias": [], "ville": "Paris", "numero": "515361517"}, "raison": "Une société se nomme littéralement CEA et son siège est à Paris, comme celui du Commissariat (activité 68.20B, location immobilière). Même chaîne, même ville : seuls le numéro officiel et l'activité les séparent. Un alias qui vaudrait identité les fusionnerait."}
````

**C'est le cas à ne jamais perdre.** Il dit la limite exacte de la correction du point 2 : un alias est
une preuve, pas une identité. Si ce cas passe en « même », la correction a été faite trop largement.

### Filiale, fondation, service rattaché : un nom contient l'autre

Ces quatre cas sont décidables sans numéro : « Fondation de X » n'est pas X, et le nom le dit. Ils sont
donc écrits **sans numéro**, comme le serait une fiche venue d'une page web.

````json
{"id": "inserm-contre-inserm-transfert", "verdict": "differentes", "a": {"nom": "Inserm", "alias": ["INSERM"], "ville": "Paris", "numero": ""}, "b": {"nom": "Inserm Transfert", "alias": [], "ville": "Paris", "numero": ""}, "raison": "Filiale de valorisation (SIREN 434033619) contre l'institut (180036048), même ville. Un nom est le préfixe de l'autre : c'est précisément le motif que fusion.variantes() signale comme doublon probable, et ici ce serait faux."}
{"id": "aphp-contre-fondation-aphp", "verdict": "differentes", "a": {"nom": "Assistance publique - Hôpitaux de Paris", "alias": ["AP-HP"], "ville": "Paris", "numero": ""}, "b": {"nom": "Fondation de l'Assistance publique - Hôpitaux de Paris", "alias": [], "ville": "Paris", "numero": ""}, "raison": "La fondation (SIREN 815144571) et l'établissement (267500452) sont deux personnes morales, même ville, le nom de l'une contenant entièrement celui de l'autre."}
{"id": "aphp-contre-dsfip-aphp", "verdict": "differentes", "a": {"nom": "Assistance publique - Hôpitaux de Paris", "alias": ["AP-HP"], "ville": "Paris", "numero": ""}, "b": {"nom": "Direction spécialisée des finances publiques pour l'Assistance publique - Hôpitaux de Paris", "alias": ["DSFIP"], "ville": "Paris", "numero": ""}, "raison": "Service de l'État rattaché (SIREN 177502143), dont le nom contient le nom complet de l'établissement. Deux entités, deux sigles."}
{"id": "ens-contre-fondation-ens", "verdict": "differentes", "a": {"nom": "École normale supérieure", "alias": ["ENS"], "ville": "Paris", "numero": ""}, "b": {"nom": "Fondation de l'École normale supérieure", "alias": ["FENS"], "ville": "Paris", "numero": ""}, "raison": "Fondation (SIREN 377536735) contre école (197534597), même ville. Les sigles diffèrent (FENS / ENS) : un sigle proche n'est pas un sigle commun."}
````

### Homonymes exacts : le numéro est le seul juge

Trois établissements portent **exactement** le nom « École normale supérieure » au registre, avec trois
SIREN différents, et deux d'entre eux à Paris. Ces cas sont écrits **avec leur numéro**, et c'est
volontaire — voir la mise en garde du § 4.

````json
{"id": "homonyme-ens-paris-saint-cloud", "verdict": "differentes", "a": {"nom": "École normale supérieure", "alias": ["ENS"], "ville": "Paris", "numero": "197534597"}, "b": {"nom": "École normale supérieure", "alias": [], "ville": "Saint-Cloud", "numero": "199208125"}, "raison": "Nom exactement identique au registre, villes différentes, numéros différents. Le numéro officiel doit opposer son veto."}
{"id": "homonyme-ens-paris-paris14", "verdict": "differentes", "a": {"nom": "École normale supérieure", "alias": ["ENS"], "ville": "Paris", "numero": "197534597"}, "b": {"nom": "École normale supérieure", "alias": [], "ville": "Paris 14", "numero": "197534621"}, "raison": "Le cas le plus dur du jeu : nom identique, MÊME ville écrite autrement (« Paris » et « Paris 14 » sont le même lieu), numéros différents. Seul le numéro sépare. Si le rapprochement fusionne ici, il fusionne n'importe quels homonymes d'une même ville."}
````

## 4. Une mise en garde : ne jamais verser un cas indécidable

Les deux cas d'homonymes ci-dessus portent leur numéro officiel **exprès**. Écrits sans numéro, ils
deviendraient indécidables : deux fiches nommées « École normale supérieure », toutes deux à Paris, sans
rien d'autre, **ne peuvent pas** être séparées — aucune information présente ne le permet, ni pour le
programme ni pour une personne.

Un jeu de référence ne doit contenir aucun cas dont la bonne réponse n'est pas déductible de ce qu'on
donne en entrée. Y en mettre un pousserait le réglage vers un comportement faux : pour « réussir », il
faudrait séparer des fiches sur une base qui n'existe pas, donc séparer aussi les vrais doublons.

Le bon traitement d'une paire indécidable est déjà dans le programme, à deux endroits, et il faut le
laisser faire :

- `pilote/src/pilote/fichier_local.py` l. 314 (`homonymes`) et l'adaptateur l. 64-67, qui disent
  « n entités portent exactement ce nom : précisez la ville ou le département, ou donnez le numéro
  officiel » ;
- l'onglet **Tri**, où une personne tranche (règle n° 6).

Si une paire indécidable arrive quand même au rapprochement, le bon résultat n'est ni « même » ni
« différentes » : c'est **proposée au Tri**, avec sa raison écrite.

## 5. Ce que le jeu mesure après l'ajout

| Classe | Avant | Après |
| :-- | :-- | :-- |
| « même », variantes typographiques (casse, accents, forme juridique, article) | 11 | 11 |
| « même », sémantique (nom développé différemment) | 1 | 1 |
| **« même », sigle contre nom développé** | **0** | **6** |
| « différentes », relation mère-fille | 13 | 17 |
| **« différentes », sigle contre dénomination d'un tiers** | **0** | **1** |
| **« différentes », homonyme exact départagé par le numéro** | **0** | **2** |
| **Total** | **25** | **38** |

Après l'ajout, deux chiffres sont à lire ensemble, et **aucun des deux ne doit bouger dans le mauvais
sens** :

- les six cas de sigles passent → la correction du point 2 du document 24 fonctionne ;
- **et** la précision ne baisse pas sur les 20 cas « différentes » → elle n'a pas été obtenue en élargissant
  le rapprochement.

Si les sigles ne passent qu'au prix d'une baisse de précision, **c'est le seuil qui a été touché au lieu
du mécanisme** : il faut revenir au point 2 et faire remonter les alias depuis les sources, au lieu de
rendre le rapprochement plus tolérant.

## Où regarder

- `24-corrections-mesurees-27.10.2026.md` § 2 et § 3 — la correction que ce jeu mesure, et son
  contre-exemple
- `20-recalibrage-rapprochement-fr-27.10.2026.md` — la méthode du jeu de cas et sa règle de croissance
- `pilote/autocal_rapprochement_fr/LISEZMOI.md` — la règle à garder : toute erreur rencontrée en usage
  réel rejoint le jeu, on n'ajuste jamais un seuil sur un seul exemple
