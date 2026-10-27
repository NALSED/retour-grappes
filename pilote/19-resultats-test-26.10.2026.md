# 19 — Résultats du grand test (`test.md`), 26-27/10/2026

Rapporté par momo : 470 tests verts, commits `fb29ca4` (`/opt/pilote`) et `09037b3` (GitHub). Rapport
complet sur la VM : `/opt/pilote/docs/rapport_test_2026-10-26.md`. **Pas encore recopié** dans
`QUESTIONS.md`/`ETAT.md` ni dans `DECISIONS.md` de `/opt/grappes` — à faire par momo.

**Verdict de momo, repris ici pour mémoire** : utilisable pour de vraies recherches à condition de lire
les messages d'alerte (sources écartées, vérification incomplète) ; pas un produit fini.

## Review totale (8 règles)

| Règle | État | Détail |
| :-- | :-- | :-- |
| 1 — original gardé | partielle | les noms retirés par un filtre n'existent que dans le journal |
| 2 — marquage IA | **pas remplie** | l'Assistant IA ne s'exécute pas ; sans production IA, pas de marquage à tester |
| 3 — rien en dur | remplie | — |
| 4 — Retirer ≠ Supprimer | remplie | — |
| 5 — vocabulaire fermé | partielle | écarts relevés : « Ouvrir », « Chargement… » |
| 6 — l'utilisateur agit | remplie | — |
| 7 — rien d'essentiel ne dépend d'une IA | remplie | — |
| 8 — personnes morales seulement | partielle | `config/personnes.yaml` de `grappes` laissé tel quel, à vérifier |

Clés d'API : aucune fuite trouvée (25 319 fichiers inspectés, historique git compris). Fiabilité A–F et
Langue : présentes sur les 64 sources.

## Sources P1 (`17-sources-a-implementer.md`)

Plus de vingt répondent réellement. En échec ou non branchées, au moins : **Exa** (HTTP 402, crédits
épuisés), **data.gouv** (MCP), **Charity Commission**, **GeoNames**, et une vingtaine d'autres non
détaillées dans le résumé reçu. **À reporter dans `17-sources-a-implementer.md` dès que la liste complète
est disponible** — ne pas réécrire ce document sur la base d'un résumé partiel.

## Partie 1 — sorties connues

- **Fusion/doublons (Splink), jeu FEBRL1 réel** : précision **1,000 à tous les seuils** (aucun faux
  positif), rappel 0,942 (seuil 0,2) à 0,900 (seuil 0,5). Les deux cas piégés demandés en 2.1 (« Club
  Alpin » Lyon/Paris à 13 %, deux boulangeries Martin à numéros officiels différents à 1 %) sont
  correctement écartés. Une régression introduite par momo pendant le test (un nom compté deux fois) a
  été trouvée et corrigée — le test a donc rempli son rôle.
- **35 requêtes à réponse connue** (2.2) : ~30 conformes, 3 partielles. Le CEA échoue, les radios
  (RFI, France Inter, FIP) échouent par intermittence.
- **Faux « oui » sur un cas réel connu : la Fondation Abbé Pierre est rapprochée à tort d'une entité
  Emmaüs.** C'est le seul faux positif constaté sur des données réelles (le jeu FEBRL, synthétique, n'en
  montre aucun) — à surveiller : un seuil qui tient parfaitement sur un jeu synthétique peut encore
  laisser passer un vrai cas.
- **ENRICHIR n'existe pas côté moteur** — à distinguer d'un rôle mal implémenté : il n'y a rien à corriger,
  il reste à construire.
- **Traduction** : aucun moteur réglé (voir `14-questions-ouvertes.md` §3, toujours ouverte) ; une source
  dans une langue non couverte n'est pas traduite.

## Partie 2 — grandeur réelle (5 sessions réelles, pannes injectées)

Comportement correct dans tous les cas testés : source en panne signalée et les autres continuent, pas de
boucle au-delà du quota, mauvaise clé signalée en français sans jamais afficher la clé.

**Vérification sur gros volumes** : seulement 3 à 4 noms vérifiés sur plus de 100 (budget d'appels
BODACC/OpenSanctions partagé entre tous les noms). Dit clairement dans le journal, **pas résolu**.

## Corrigé par momo pendant le test (pour mémoire)

Arrêt sur demande (58 s → 14 s, **sans test de non-régression**), une clé refusée (Serper 403) comptée à
tort comme une réponse, les clés de `secrets.env` jamais chargées pour les sources web (sérieux : rendait
toutes les sources payantes silencieusement inactives), vérification BODACC qui plantait, langue « en »
par défaut sur 49 sources sur 64, absence de gestion du 429, OpenSanctions annonçant « sanctions » pour
toute liste, écran Tri refusé à l'ouverture. Transparence notée : une panne d'abord attribuée au réseau
était en fait un conteneur Overpass local dégradé — momo le dit lui-même plutôt que de laisser l'erreur
d'attribution dans le rapport.

**Publication GitHub** : un instantané choisi du projet, pas un miroir complet de la VM — à garder en
tête pour `CERTIFICATION.md`/`DECISION_SOURCES.md`/`DECISION_FIABILITE.md` (`18-calibrage-26.10.2026.md`
§4) : leur absence peut être un choix de publication, pas un oubli.

## Décisions demandées par momo — voir `14-questions-ouvertes.md` §11
