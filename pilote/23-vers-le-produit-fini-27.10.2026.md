# 23 — Vers le produit fini : l'ordre de travail

Consigne pour momo. grappes est aujourd'hui « opérationnel pour un usage encadré », pas terminé (rapport
du 26/10). Ce document liste ce qu'il reste, **dans l'ordre où le faire**, et pourquoi cet ordre : la
fiabilité du résultat d'abord, les pièces manquantes ensuite, les réglages de confort en dernier.

Trois catégories, à ne pas mélanger : des **pièces qui n'existent pas**, des **règles à moitié tenues**
(réglages, pas des trous), et des **choix encore ouverts**. Rien ici n'invente une méthode nouvelle —
chaque point reprend un mécanisme déjà nommé et documenté.

---

## 1. Priorité : recalibrer Fusion/doublons sur des cas français réels

**Déjà écrit en détail dans `20-recalibrage-rapprochement-fr-27.10.2026.md`.** À faire en premier parce
que c'est la seule chose de cette liste qui touche directement à la **fiabilité du résultat** — la
priorité n°1 du projet. Le jeu FEBRL (synthétique, anglophone) donne une précision parfaite ; un cas
réel connu (Fondation Abbé Pierre / entité Emmaüs) est pourtant passé à travers. Résumé : constituer un
petit jeu de cas difficiles français, qui grossit à chaque faux positif ou négatif rencontré, mesuré avec
la même méthode que FEBRL (précision/rappel par seuil). Détail complet dans le doc 20.

## 2. Construire ENRICHIR

**Une des trois fonctions de base (CHERCHER/VÉRIFIER/ENRICHIR) n'existe pas côté moteur.** Ce n'est pas
un rôle mal implémenté, c'est une pièce manquante.

Le travail n'est pas de l'inventer : **sept sources portent déjà le rôle ENRICHIR** dans
`17-sources-a-implementer.md` — Memento TimeTravel, ProPublica, 360Giving GrantNav, info-financière (AMF),
CompanyScope, OpenAddresses, university-domains-list, GeoNames. Elles définissent déjà ce qu'ENRICHIR doit
faire : **prendre une fiche déjà trouvée par CHERCHER, et lui ajouter des informations correctes et
traçables jusqu'à la source qui les a données** (test.md §2.2) — jamais remplacer, jamais écraser (règle
n°1).

Construire ENRICHIR, concrètement : un pas de pipeline qui s'exécute **après** qu'une entité a une fiche
(donc après CHERCHER, et idéalement après VÉRIFIER), qui prend les sources listées ENRICHIR comme entrée
et ajoute leurs champs à la fiche existante, chacun marqué de sa source d'origine — même mécanisme de
traçabilité que CHERCHER, juste un moment différent du pipeline. Commencer par GeoNames et
university-domains-list (aucune clé, P1, déjà obtenues) pour avoir un ENRICHIR qui tourne vite, puis
ajouter les autres.

## 3. Les sources P1 encore non branchées

Reprendre `17-sources-a-implementer.md` et finir la colonne P1 (une vingtaine de sources listées non
branchées au 26/10 : TED, OCDS, data.europa.eu, org-id.guide, IRS, CRA, Charities Regulator, GrantNav,
CompanyScope, Zefix, The Gazette, GeoNames, OpenAddresses, geoBoundaries, outillage). Certaines sont
maintenant couvertes par le travail en cours sur `22-bases-locales-27.10.2026.md` (GeoNames, geoBoundaries
déjà en copie locale) — vérifier avant de rebrancher en double.

## 4. Désambiguïser les noms communs (cas « Renault »)

Constaté au test réel : un nom très répandu (une entreprise et un patronyme courant) noie le bon résultat
dans ses homonymes sur SIRENE. **Ne pas corriger ça en changeant le seuil de Fusion/doublons** — ce n'est
pas un problème de rapprochement, c'est un problème de **recherche**.

La méthode existante pour ce cas précis : le **blocage multi-champs** (*blocking*), pratique standard en
recherche d'entités — ne jamais chercher sur le nom seul quand plusieurs résultats le partagent, mais
proposer d'office les champs qui désambiguïsent (SIREN, code d'activité, ville, département) plutôt que
de renvoyer une liste plate triée par nom. SIRENE porte déjà ces champs ; Splink lui-même recommande le
blocage multi-champs dans sa propre documentation (déjà cité en `test.md` pour Fusion/doublons) — le même
principe s'applique ici, en amont, sur CHERCHER.

## 5. Les trois règles à moitié tenues

Pas bloquant pour un usage encadré, mais nécessaire pour dire le produit fini. Méthode pour chacune,
déjà nommée, pas inventée pour l'occasion :

- **Règle 1 (original gardé) — rendre un retrait visible.** Aujourd'hui, un nom écarté par un filtre
  n'existe plus que dans le journal technique. Motif à reprendre : la **divulgation d'éléments filtrés**
  (« N éléments écartés, les montrer »), déjà documenté par Algolia dans son guide *Filtering Patterns*
  et par les pratiques de filtrage à facettes en général. À appliquer sur l'écran de résultat de grappes :
  un retrait reste un clic pour le consulter, jamais une disparition silencieuse.
- **Règle 5 (vocabulaire fermé) — empêcher la dérive.** Deux mots hors liste trouvés au test (« Ouvrir »,
  « Chargement… »). Outil nommé : **Vale** (vale.sh), un linter de texte avec une fonction *Vocabularies*
  faite pour ça — transformer `charte/nommage.md` en liste Vale et vérifier toutes les chaînes de
  l'interface en CI, pour que l'écart devienne une erreur de build plutôt qu'une relecture manuelle.
- **Règle 8 (personnes morales seulement) — vérifier `config/personnes.yaml`.** Revue ligne par ligne :
  aucun champ qui en découle ne doit entrer dans le calcul du rapprochement. Référence à citer :
  **NIST SP 800-188**, *De-Identifying Government Datasets* — la norme qui documente qu'un
  quasi-identifiant ne doit jamais servir de clé de liaison entre enregistrements, exactement le principe
  de la règle n°8.

## 6. Dette de test

- **L'arrêt prompt** (58 s → 14 s, corrigé le 26/10) n'a pas de test de non-régression. En ajouter un
  avant d'y retoucher à nouveau.
- Les conflits de sources signalés par momo le 26/10 (Brave, GNews, Apify, Meetup, Google Places,
  BrightData, Marginalia, Mojeek, NewsData.io, NewsAPI.ai, Pappers, OpenCorporates, Companies House,
  CourtListener, Charity Commission, OpenSanctions hébergé) : toujours pas clarifiés avec Sednal. À
  remonter avant de les brancher, pas à trancher seul.

---

## Ce qui reste volontairement hors de cette liste

- **Tableau de bord** : aucune spécification, reporté par Sednal — ne pas improviser.
- **Assistant IA / règle 2** : laissé de côté le 27/10 (`14-questions-ouvertes.md` §11) — n'y revenir que
  si Sednal relance ce sujet.
- **`CERTIFICATION.md` / `DECISION_SOURCES.md` / `DECISION_FIABILITE.md`** : toujours absents du dépôt
  publié (`18-calibrage-26.10.2026.md`). Sans eux, le calibrage reste partiel sur ce point précis — à
  signaler à Sednal, pas à reconstituer en les devinant.

## Où regarder

- `20-recalibrage-rapprochement-fr-27.10.2026.md` — section 1, détaillée
- `17-sources-a-implementer.md` — sections 2 et 3
- `19-resultats-test-26.10.2026.md` — l'origine de chaque point de cette liste
- `22-bases-locales-27.10.2026.md` — ce qui recoupe déjà la section 3
