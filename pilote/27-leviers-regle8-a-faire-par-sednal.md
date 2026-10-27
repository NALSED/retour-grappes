# 27 — Leviers sur la règle n° 8 : ce qui a été dit, pas une consigne pour momo

Ce document note ce qui a été discuté et décidé avec Sednal le 27/10/2026, au sujet du point 1 du
document `24-corrections-mesurees-27.10.2026.md` (l'écart de la règle n° 8 entre le moteur et pilote).

**Ce n'est pas une spécification à donner à momo.** Sednal implémentera lui-même ces leviers dans le code,
une fois que momo aura terminé le travail en cours. Ce document existe pour qu'on ne reparte pas de zéro
ce moment-là.

## Ce qui ne change pas

Le mécanisme d'exclusion lui-même (appliquer `is_institution()` au moment où un résultat de genre
« compte » devient un candidat, comme décrit dans le doc 24 §1) reste la règle par défaut. Le moteur n'est
pas débridé. Rien n'ouvre, par défaut, la recherche ou le rapprochement sur des personnes physiques
nommées.

## Les trois leviers demandés par Sednal

1. **Indices d'institution configurables.** `DEFAULT_INSTITUTION_HINTS`
   (`grappes/src/grappes/personnes.py` l. 18-26) est aujourd'hui une liste figée dans le code. La sortir
   vers `config/personnes.yaml` (déjà le fichier où un commentaire dit explicitement « décision de
   Sednal, jamais de l'agent ») sous forme de liste modifiable, pour que Sednal puisse ajouter un indice
   sans toucher au code.

2. **Levée au cas par cas, sur une fiche précise.** Une action sur une fiche déjà écartée du
   rapprochement (elle reste affichée, règle n° 1) : « Marquer comme personne morale ». Elle reclasse
   cette seule fiche, avec trace (quand, laquelle), réversible comme un *Retirer*. Ne touche à rien
   d'autre.

3. **Levée au moment de lancer une recherche précise.** Une case sur la recherche elle-même (plan de
   travail ou recherche simple), du type « Afficher les comptes écartés par la règle n° 8 dans cette
   recherche ». Scopée à cette recherche seule. Elle ne fusionne rien automatiquement : les comptes
   concernés entrent simplement dans le lot normal des candidats, passent par Fusion/doublons comme les
   autres, et tout rapprochement qui en sort atterrit dans l'onglet Tri comme n'importe quel autre
   rapprochement — *Confirmer*/*Séparer*, décision humaine, comme toujours.

## Pourquoi ces trois formes et pas un interrupteur global

Un interrupteur global sur/off pour toute la règle a été écarté en discussion : il donnerait, le temps
qu'il est sur « activé », la capacité de rechercher ou rapprocher sur des personnes physiques nommées, ce
que la règle n° 8 interdit par principe (base CNIL/RGPD, pas une question d'ergonomie). Les trois leviers
ci-dessus restent chacun scopés — une fiche, une recherche, ou une liste de mots que Sednal contrôle — et
chacun passe par une confirmation humaine avant tout rapprochement effectif.

## Vocabulaire à ajouter avant tout écran

« Marquer comme personne morale » (verbe d'action) et le libellé de la case de recherche doivent passer
par `charte/nommage.md` (règle n° 5) avant d'apparaître où que ce soit.

## À faire, plus tard

Rien pour l'instant. Quand Sednal reprend ce point : relire `24-corrections-mesurees-27.10.2026.md` §1
d'abord, puis ce document.
