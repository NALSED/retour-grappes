# 05 — Réglages › Sources

## Ce qu'on implémente

La liste des sources, groupée par catégorie, et la fiche d'une source.

## La liste

Des **cartes repliables**, une par catégorie : Entreprises, Associations, Organisations officielles, Web,
Réseaux sociaux, Lieux, Presse, Personnalisé. L'en-tête de carte porte le nom, un compteur
« n sur m allumées », et le chevron `deplier`.

Une seule carte ouverte à la fois. La carte ouverte prend un **liseré de 3 px en `accent` à gauche** —
c'est le seul endroit de grappes où un bord coloré à gauche est autorisé.

### Les six colonnes, dans cet ordre

| Colonne | Contenu |
| :-- | :-- |
| **ÉTAT** | Pastille + mot : *opérationnelle*, *à vérifier*, *éteinte* |
| **NOM** | Nom de la source en `corps-fort`, et le pays dessous en `legende` |
| **COÛT** | *gratuit* ou le tarif |
| **RÔLE** | Étiquette de rôle, couleur du rôle |
| **QUOTA** | Jauge + chiffres, ou *illimité* |
| **ACTIONS** | *Modifier* et *Retirer*, chacun avec son pictogramme |

En-têtes en `etiquette` (11 px, capitales) — le seul style en capitales.

Le pays est **dans la colonne NOM**, pas dans une colonne à lui : il qualifie la source, il ne mérite pas
une colonne. C'était une colonne dans une maquette antérieure, elle a été supprimée.

## La fiche source

Un **panneau latéral** qui glisse depuis la droite, **480 px**, avec `ombre-panneau` et un voile derrière.
Panneau latéral pour *Modifier*, fenêtre centrée pour *Ajouter* — voir `charte/composants/Panneau/`.

### Les champs

| Champ | Type | Note |
| :-- | :-- | :-- |
| Nom | texte | — |
| Catégorie | puces | une seule |
| Rôle | puces | une seule parmi les six |
| Coût | puces | *gratuit* / *payant* |
| Origine de l'information | puces | *première main* / *seconde main* |
| Pays | texte | `FR`, `UK`, `Mondial`… |
| **Langue** | texte | `fr`, `en`, `de`… — **nouveau, voir plus bas** |
| État | puces | les trois états |
| Quota | nombre + unité | vide = illimité |
| Description | texte long | — |
| Clé d'API | **masqué** | voir plus bas |

### Le champ Langue

**Décidé le 26/10/2026.** C'est la langue dans laquelle la source attend les mots cherchés — pas la
langue de son interface.

Il sert à la fenêtre d'Aide : son contrôle de plan compare la langue de chaque source posée à la langue
de la saisie, et signale qu'une tuile Traduction manque. Sans ce champ, ce contrôle est impossible. Voir
`11-fenetre-aide.md`, contrôle n° 6.

Aide sous le champ : « La langue dans laquelle cette source attend les mots cherchés. L'Aide s'en sert
pour signaler qu'une Traduction manque. »

### La clé d'API

**Jamais affichée en clair.** Champ masqué à la saisie. Une fois enregistrée, l'interface affiche
**« clé enregistrée »** et rien d'autre : pas de valeur partielle, pas de bouton « voir ».

Pour changer une clé, on en saisit une nouvelle. On ne relit jamais l'ancienne depuis l'interface.

### Le pied du panneau

*Enregistrer* en bouton principal, *Annuler* en secondaire, et **un seul bouton principal** par écran —
c'est la règle du GOV.UK Design System.

Si la source existe déjà, *Retirer* est placé **à droite, séparé**, en bouton danger. Jamais à côté
d'*Enregistrer* : on ne met pas une action destructrice sous le doigt qui valide.

## Une source nouvelle démarre « à vérifier »

Jamais « opérationnelle » tant qu'un test n'a pas réussi. C'est le sens même de l'état.

## Où regarder

- `charte/composants/LigneSource/` — la ligne et ses six colonnes
- `charte/composants/Carte/` — la carte repliable
- `charte/composants/Panneau/` — panneau latéral contre fenêtre centrée
- `charte/composants/Pastille/`, `Role/`, `Quota/`, `Champ/`, `Bouton/`, `Puce/`
- `maquettes/Main.dc.html`
