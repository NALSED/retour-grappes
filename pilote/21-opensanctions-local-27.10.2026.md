# 21 — OpenSanctions en copie locale, au lieu de l'API

Consigne pour momo. Tranché par Sednal le 27/10/2026 : la place disque de `/mnt/carte` (68 Go libres sur
100) est disponible, **mais on n'installe pas yente**. On charge les données OpenSanctions en copie locale
et VÉRIFIER interroge cette copie.

## Pourquoi pas yente

yente (l'API OpenSanctions auto-hébergée) demande **60 Go de disque et 8 Go de RAM** (16 recommandés,
index sur SSD) d'après la documentation d'OpenSanctions. La RAM de la VM suffirait (12 Go disponibles),
le disque passerait de justesse — mais trois raisons l'écartent :

1. **yente apporte son propre moteur de rapprochement**, avec son propre score. Le projet aurait alors
   deux moteurs à calibrer et à certifier au lieu d'un, alors que Fusion/doublons est déjà mesuré
   (précision 1,000 sur FEBRL, voir `19-resultats-test-26.10.2026.md`). **Le rapprochement reste dans
   Fusion/doublons.** OpenSanctions est une source de données, pas un deuxième juge.
2. **La marge disque** : 60 des 68 Go, sur le point de montage où grossissent aussi les archives autocal,
   les rejeux, les journaux et le jeu de cas français du document 20.
3. **Un service de plus à surveiller** (Elasticsearch) sur une VM où un conteneur Overpass s'est déjà
   dégradé en plein test.

À garder en réserve si la copie locale s'avère trop lente — **mesurée, pas supposée**.

## Ce qu'il faut télécharger

OpenSanctions publie ses exports en téléchargement direct, sans clé ni compte. Pour la collection
`default` (3,9 M d'entités, 1,24 M de cibles, 478 sources, mise à jour quotidienne) :

| Fichier | Contenu | Taille |
| :-- | :-- | :-- |
| `targets.simple.csv` | les cibles, format CSV simplifié | **450 Mo** |
| `names.txt` | les noms des cibles seulement | 105 Mo |
| `entities.ftm.json` | les entités au format FollowTheMoney | 2,4 Go |
| `statements.csv` | granularité par affirmation | 8,76 Go |

**Prendre `targets.simple.csv`** (450 Mo) : il porte les noms, les alias, les pays, les dates et le motif
d'inscription — tout ce dont VÉRIFIER a besoin. Chargé en table SQLite avec ses index, compter 1 à 2 Go
sur `/mnt/carte` : à comparer aux 60 Go de yente.

`entities.ftm.json` est l'option si tu veux la structure FollowTheMoney complète — cohérent avec l'export
FtM qui existe déjà (`test_f21_ftm.py`) — mais 2,4 Go pour une information que le CSV simplifié porte
déjà sous une forme plus directe à interroger. **Ne prends `statements.csv` dans aucun cas** : 8,76 Go
pour une granularité dont rien dans grappes ne se sert.

## Ce que ça règle

La limitation constatée au test du 26/10 : sur plus de 100 noms, 3 à 4 seulement étaient vérifiés, parce
que le budget d'appels de l'API était partagé entre tous les noms. **Une copie locale n'a pas de quota** :
les 100+ noms sont tous vérifiables contre OpenSanctions.

**Attention, ça ne règle que la partie OpenSanctions.** BODACC reste une API avec son quota, donc la
mention « X sur Y noms vérifiés » demandée dans `13-quotas-couts-budget.md` reste nécessaire — elle
portera sur BODACC, plus sur OpenSanctions.

## Licence : ce qui est permis, et à quelle condition

Les données OpenSanctions sont sous **CC-BY-NC 4.0**. Leur page sur l'usage non commercial autorise
explicitement « un projet étudiant, une analyse de loisir, de la recherche personnelle » sans aucune
démarche ni permission.

**Déclaré par Sednal le 27/10/2026 : usage personnel, les données ne sortent pas de la VM.** On est donc
dans le cadre gratuit.

**La condition à retenir, parce qu'elle n'est pas définitive** : la même page exclut explicitement du
cadre gratuit « la recherche OSINT faite comme activité professionnelle ou pour des clients », et tout
usage à l'intérieur d'une entreprise à but lucratif — indépendamment du volume de données utilisé. Le
jour où grappes sert pour un client ou dans un cadre professionnel, cette source doit être soit payée
(licence de données OpenSanctions), soit retirée. **Remonte la question à Sednal ce jour-là, ne la tranche
pas.**

**Attribution obligatoire** (le BY de CC-BY-NC) : OpenSanctions se déclare avec son champ `attribution`
dans son fichier de `config/sources/`, exactement comme GDELT le fait déjà (voir `pilote/README.md`
« Sources et mentions obligatoires »). La mention doit donc apparaître dans le `SOURCES.txt` de toute
recherche qui s'en sert. **Mécanisme existant, rien à inventer.**

## Fiabilité de la source

OpenSanctions est un agrégateur : il republie 478 sources officielles. Ce n'est donc **pas de la première
main** au sens du code Admiralty (`05-reglages-sources.md`). La copie locale ne change pas ça — la
fiabilité se note sur l'origine de l'information, pas sur le moyen d'y accéder. À noter en **B** (source
fiable mais qui relaie), jamais en A.

## Où regarder

- `13-quotas-couts-budget.md` — la mention « X sur Y noms vérifiés », qui reste due pour BODACC
- `14-questions-ouvertes.md` §5 — la question du disque, tranchée par ce document
- `19-resultats-test-26.10.2026.md` — la limitation constatée au test
- `pilote/README.md` du dépôt de code — le mécanisme `attribution` → `SOURCES.txt`
