# 18 — Calibrage du 26/10/2026 : ce que le code de `grappes` dit que ces documents ne disaient pas

Sednal a demandé : « lit tout et calibre. » Ce document rend compte de la lecture du dépôt de code
**`NALSED/grappes`** (lecture seule, commit `eed84f5b7bcf048c63b8b055e8870883f04ba079`) et dit, point par
point, ce qui recale `test.md` et les documents voisins. Rien n'a été deviné : chaque point cite le
fichier lu.

## 1. Bonne nouvelle d'abord : « suite/pilote », c'est probablement nous

`pilote/README.md` et `pilote/README.en.md` décrivent une **nouvelle interface** servie sur `/suite` :
« Réglages (sources et outils), Recherche (simple, plan de travail libre appliqué par le moteur,
recherches sauvegardées), Tri (rapprochements proposés par Splink, à confirmer ou séparer) et l'Aide. »
C'est, mot pour mot, la coquille et le vocabulaire de `00-MAIN.md` à `17-sources-a-implementer.md` —
**Confirmer/Séparer** sur des **rapprochements proposés par Splink**, exactement la section 16.

Aucun fichier nommé « spécification suite/pilote » n'existe dans le dépôt : il n'y a nulle part où
chercher un texte séparé. La lecture la plus simple est que **ce dossier (`retour-grappes/pilote/`) est
cette spécification** — momo code `/suite` à partir de ces documents. Le code le confirme déjà en partie :
`pilote/src/pilote/rapprochement_splink.py`, `rapprochements.py`, `static/suite/js/tri.js` et
`tests/test_suite_tri.py` existent et parlent de Splink, Confirmer, Séparer.

**Ancienne interface, à ne pas confondre.** Le dépôt contient aussi une interface plus ancienne (pages
F01 à F23, `pilote/tests/test_f01_*.py` à `test_f23_*.py`) avec son **propre onglet « Tri »**
(`test_f13_tri.py`) qui n'a rien à voir avec le rapprochement : c'est un tri de **candidats d'une seule
recherche** par facettes (ville, genre, source, « solide »/« fragile », « se ressemble »,
`pilote/classements/*.yaml`), pas une validation de doublons entre fiches. Si momo ou Sednal disent
« Tri », vérifier systématiquement lequel des deux ils veulent — **doc 16 ne décrit que le Tri de `/suite`**,
pas l'ancien F13. Ne pas toucher à l'ancien F13 sans consigne séparée : il n'est couvert par aucun
document de ce dossier.

## 2. Le plus important : un système de certification existe déjà, et **son jeu test est brûlé depuis le 12/10/2026**

`grappes/src/grappes/autocal/` est un système de mesure complet, bien plus strict que ce que `test.md`
proposait en 2.1-2.2, et **il a déjà tourné une fois** :

- Fichier `grappes/autocal/test_ouvert.json` (présent dans le dépôt, donc réel, pas une hypothèse) :
  ouvert le **2026-10-12T16:54:55 UTC**, commit `51a4ae1`, statut **« terminé »** le même jour à 19h05,
  **zéro réouverture** consignée.
- `grappes/src/grappes/autocal/test_final.py` l'explique en tête : « Ouverture UNIQUE du jeu de test
  (D62/D66) ». Le fichier `test_ouvert.json` est créé en **création exclusive** : une deuxième ouverture
  est **refusée** (code de sortie 2) et consignée dans `reouvertures` — le code dit littéralement
  « le test est brûlé » et exige que le verdict le dise aussi.
- Trois jeux disjoints, figés le 12/10/2026 (`grappes/autocal/jeux.json`) : **dev** (mise au point,
  rejouable à volonté — `--essai dev`), **validation** (rejouable, sert à mesurer le sur-ajustement —
  `--essai validation`), **test** (le jeu brûlé, 7 sujets, dont un témoin négatif).
- Ce que ça mesure (les « critères 1 à 8 » cités dans le code, numérotés comme dans `docs/CERTIFICATION.md`
  — absent du dépôt, voir §4) : R-précision ≥ 0,90 sur les sujets positifs (et une deuxième ligne, plus
  stricte, sur les sujets à liste fermée) ; taux de remplissage correct quand le nombre de résultats
  attendus est connu ; des témoins négatifs qui ne doivent **jamais** atteindre le seuil de confiance
  (τ = 0,90) ; un test de permutation (le classement réel doit battre un classement aléatoire) ; une
  calibration de la probabilité affichée (erreur de calibration ≤ 0,10, meilleure qu'un pronostic
  constant) ; une reproductibilité **octet pour octet** en rejouant deux fois la même mesure ; `pytest`
  entièrement vert ; et un écart mesuré entre le jeu de validation et le jeu test (détecter le
  sur-ajustement). C'est du Cranfield/TREC fait sérieusement (Rprec, bpref, infAP, MAP — les mêmes sigles
  que `test.md` cite en bas de page), avec en plus de la calibration et de la reproductibilité.
- Le juge (`grappes/src/grappes/autocal/juger.py`) utilise Wikidata comme vérité-terrain, avec **trois**
  verdicts (juste / faux / non vérifiable, jamais une approximation) — « non vérifiable » ne compte
  jamais comme juste, ce qui est exactement l'esprit de la règle 2 de `00-MAIN.md` (contenu IA jamais
  compté comme fait vérifié) appliquée à la mesure elle-même.

**Conséquence directe pour `test.md` §2.1** : la section demandait de reprendre « la méthode de contrôle
qualité de Splink » sur Fusion/doublons avec un jeu FEBRL/`recordlinkage`. Ça reste valable **en soi**,
mais il faut que momo sache, avant de commencer, que :

1. **Ne jamais lancer `python -m grappes.autocal.test_final` sans `--essai dev` ou `--essai validation`.**
   Le jeu test est déjà consommé ; une tentative de l'ouvrir à nouveau est refusée par le code et
   **consignée comme une tentative de brûlage** — ce n'est pas une mesure à refaire « pour voir ».
2. Le système autocal mesure l'algorithme de classement du moteur **sur des données déjà archivées, zéro
   réseau** (rejoue une collecte passée). Il ne dit rien sur la **vérité des sources en direct** — est-ce
   qu'Exa, Serper, The Guardian etc. répondent *aujourd'hui* avec des résultats corrects sur un cas connu.
   C'est exactement le trou que `test.md` §2.2 (CHERCHER/VÉRIFIER/ENRICHIR sur des requêtes connues,
   en direct) comble, et que l'autocal ne couvre pas. **Les deux ne se recouvrent pas, ils se
   complètent** — mais je corrige quand même §2.1 ci-dessous pour éviter toute confusion chez momo entre
   « refaire la mesure Fusion/doublons avec un jeu externe » (ce que je demandais, qui ne touche jamais
   au jeu test de grappes) et « rouvrir le jeu test de grappes » (interdit).

## 3. Correction apportée à `test.md`

Section 2.1 reformulée pour dire explicitement : ce jeu FEBRL/`recordlinkage` est un jeu **externe**,
sans rapport avec `autocal/jeux.json` ; interdiction explicite de lancer `test_final.py` sans
`--essai` ; et mention que si momo veut *aussi* restituer la mesure déjà faite le 12/10 (critères 1 à 8,
jeux dev/validation/test), il peut la rapporter **en plus**, jamais la refaire.

## 4. `CERTIFICATION.md`, `DECISION_SOURCES.md`, `DECISION_FIABILITE.md` : toujours introuvables, mais mieux cernés

Confirmé à nouveau : ces trois fichiers sont cités partout dans le code
(`grappes/src/grappes/autocal/test_final.py`, `pilote/README.en.md`, etc.) mais **absents du dépôt**, non
exclus par aucun `.gitignore` — jamais commités. Ce qu'on sait *indirectement* de `CERTIFICATION.md` par
le code qui le cite (ci-dessus, §2) suffit pour comprendre le principe, mais pas pour en vérifier le
texte exact (seuils précis au-delà de ceux lus dans `test_final.py`, justification de chaque seuil,
exceptions). **Toujours en attente que Sednal les fournisse** (copie dans ce dépôt, ou chemin sur la VM)
si on veut recaler `test.md` dessus mot à mot plutôt que par déduction du code.

## 5. Autres recoupements utiles trouvés en lisant le dépôt

- **`pilote/tests/test_f20_injection.py`** existe : un test de résistance à l'injection de contenu (données
  non fiables d'une source traitées comme texte, jamais comme instruction) — exactement ce que
  `phase8.md §Sécurité` décrit (`search/safe.py`) et ce que `test.md` §3.2 redemande en exploratoire. Les
  deux sont donc complémentaires : f20 couvre l'automatisé, §3.2 couvre l'exploration libre.
- **`pilote/tests/test_f21_ftm.py`** : export FollowTheMoney existe déjà en test — en lien direct avec
  `source.md` (briefing Aleph/FollowTheMoney de Sednal). Pas d'action requise, juste une confirmation que
  ce sujet a déjà une prise dans le code.
- **`pilote/tests/test_f19_retours_reglages.py`** : à vérifier par momo si ça recoupe des points déjà
  couverts par `05-reglages-sources.md`/`06-reglages-outils.md` — pas lu en détail, pas de conflit trouvé,
  mais pas garanti sans régler non plus.
- **`grappes/docs/conception/phase8.md`** confirme toute l'architecture « multiprise » décrite
  implicitement dans `17-sources-a-implementer.md` (liste blanche de fournisseurs, interrupteur unique
  `autonome`, secrets uniquement par variable d'environnement, jamais dans le dépôt ni le journal, arrêt
  sans nouvelle tentative sur 403/429) — cohérent avec ce que momo a rapporté (quotas Exa/Linkup/DILA déjà
  atteints) : c'est le comportement **prévu**, pas un bug.
- **`grappes/docs/conception/cycle.md`** décrit une boucle automatique (`grappes cycle`) avec ses propres
  garde-fous d'apprentissage (D37 à D49) — jamais mentionnée dans aucun document de ce dossier jusqu'ici.
  Statut « proposée », aucune collecte réelle encore lancée. À garder à l'œil mais rien à trancher
  maintenant : Sednal n'a jamais été consulté sur ce sujet, donc rien ici ne doit en parler comme d'une
  décision prise.

## 6. Ce qui reste ouvert après ce calibrage

- Obtenir `CERTIFICATION.md`/`DECISION_SOURCES.md`/`DECISION_FIABILITE.md` eux-mêmes, si Sednal veut un
  recalage mot à mot plutôt que par déduction.
- Le conflit Serper (§ déjà signalé à Sednal avant ce document) : toujours pas résolu.
- La liste de sources non vérifiées venue de la capture d'écran de momo (Brave, GNews, Apify, Meetup,
  Google Places, BrightData, Marginalia, Mojeek, NewsData.io, NewsAPI.ai, Pappers, OpenCorporates,
  Companies House, CourtListener, Charity Commission, OpenSanctions hébergé) : toujours pas résolue.
- Vérifier avec momo que « Tri » dans ses prochains rapports désigne bien `/suite` (doc 16) et pas
  l'ancien F13 — source de confusion possible sinon.
