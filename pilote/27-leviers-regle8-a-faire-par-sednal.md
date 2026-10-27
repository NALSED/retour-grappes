# 27 — Leviers sur la règle n° 8 : implémentés

Discuté avec Sednal le 27/10/2026, au sujet du point 1 du document `24-corrections-mesurees-27.10.2026.md`
(l'écart de la règle n° 8 entre le moteur et pilote). **Décidé pendant la discussion : pas une consigne
pour momo — c'est Claude qui a écrit et poussé le code lui-même**, directement sur `NALSED/GRAPPES`
(commit après `3a3f324`, celui qui a livré les documents 24/25/26).

## Ce qui ne change pas

Le mécanisme d'exclusion lui-même (`is_institution()` au moment où un résultat de genre « compte » devient
un candidat, doc 24 §1 — déjà livré par momo dans `pilote/regle8.py` avant cette session) reste la règle
par défaut. Le moteur n'est pas débridé. Rien n'ouvre, par défaut, la recherche ou le rapprochement sur
des personnes physiques nommées.

## Les trois leviers, tels qu'implémentés

1. **Indices d'institution configurables.** Pas besoin de sortir `DEFAULT_INSTITUTION_HINTS` vers
   `config/personnes.yaml` comme envisagé en discussion : le moteur avait déjà cette liste rendue
   configurable, ailleurs — `config/scoring.yaml`, clé `resultat.indices_institution`, lue par
   `clustering/assign.py` à l'affectation des entités. `pilote/regle8.py::genre_retenu()` accepte
   maintenant un paramètre `hints`, et `pont/executer.py` lui passe `cfg.scoring.resultat_indices_institution`
   — la même liste que le moteur, lue une seule fois, modifiable par Sednal dans ce seul fichier.

2. **Levée au cas par cas, sur une fiche précise.** `tri.marquer_personne_morale()` (nouvelle fonction,
   `pilote/tri.py`) : reclasse une seule fiche de « personne » à « compte », trace la décision dans
   `faits.regle8_leve` (qui, quand) et le journal, relance aussitôt la recherche de rapprochements pour
   cette recherche. Nouvel point d'entrée `POST /api/recherches/{rid}/noms/{cid}/personne_morale`. Dans
   l'écran, le bouton « marquez-la comme personne morale » apparaît sous une fiche dont le genre est
   « personne », à côté de l'avertissement qui dit pourquoi elle est écartée (`static/tri.js`).

3. **Levée au moment de lancer une recherche précise.** Nouvelle case dans les options avancées de
   Recherche : « Afficher les comptes écartés par la règle n° 8 dans cette recherche »
   (`static/index.html`, `static/lancement.js`). Le paramètre `regle8_afficher_comptes` (booléen, validé
   dans `lancement.valider_options`) traverse `executeur_grappes.preparer()` jusqu'à `lancement.json`, et
   `pont/executer.py` saute la reclassification pour cette recherche-là seulement : les comptes concernés
   entrent dans le lot normal des candidats, passent par Fusion/doublons comme les autres, et tout
   rapprochement qui en sort atterrit dans l'onglet Tri comme n'importe quel autre — *Confirmer*/*Séparer*,
   décision humaine, comme toujours.

## Pourquoi ces trois formes et pas un interrupteur global

Écarté en discussion : un interrupteur global donnerait, le temps qu'il est activé, la capacité de
rechercher ou rapprocher sur des personnes physiques nommées, ce que la règle n° 8 interdit par principe
(base CNIL/RGPD, pas une question d'ergonomie). Les trois leviers restent chacun scopés — une fiche, une
recherche, ou une liste de mots que Sednal contrôle — et chacun passe par une confirmation humaine avant
tout rapprochement effectif.

## Vocabulaire ajouté

« Marquer comme personne morale » et « Afficher les comptes écartés par la règle n° 8 dans cette
recherche » — ajoutés à `charte/nommage.md` (règle n° 5) dans ce même commit.

## Tests

`tests/test_regle8_personnes_morales.py`, quatre nouveaux : `genre_retenu` avec indices supplémentaires ;
`marquer_personne_morale` qui lève la règle sur une fiche sans toucher aux autres ; `marquer_personne_morale`
refusé sur une fiche que la règle n'écarte pas ; `regle8_afficher_comptes` validé comme un booléen. Suite
`pilote` complète rejouée (hors tests réseau/navigateur, marqués `reseau`) : aucune régression introduite
par ces changements — voir le reste du journal de commit pour le détail de ce qui a été vérifié.

## Reste ouvert

Rien côté code. Reste à voir à l'usage : si la case « afficher les comptes » de la recherche simple
(preset) doit aussi exister, pas seulement dans Recherche avancée — pas demandé pour l'instant.
