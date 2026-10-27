# 20 — Recalibrer Fusion/doublons sur des cas français réels

Proposition pour la 3ᵉ décision de `14-questions-ouvertes.md` §11. **À valider par Sednal avant de la
donner à momo.**

## Le problème

Le jeu FEBRL (anglophone, synthétique) donne une précision de 1,000 à tous les seuils testés. Sur un cas
réel français, grappes a quand même produit un faux « oui » (Fondation Abbé Pierre rapprochée d'une
entité Emmaüs). Un jeu parfait sur FEBRL ne garantit donc rien sur des noms français réels — logique :
FEBRL n'a jamais vu une fondation et une association qui se ressemblent parce que l'une finance ou héberge
l'autre, un cas typique du monde associatif français, absent d'un jeu généraliste anglophone.

**Ne pas faire** : remonter le seuil au doigt mouillé pour écarter ce cas précis. Un seuil ajusté sur un
seul exemple connu se règle à l'aveugle sur tout le reste.

## La méthode, déjà établie ailleurs : le réglage sur « cas difficiles »

C'est une pratique courante en rapprochement d'entités (record linkage / entity resolution), pas une
invention pour l'occasion : chaque faux positif ou faux négatif découvert en usage réel rejoint un petit
**jeu de cas difficiles**, propre au domaine, qui grossit avec le temps et sert de test de non-régression
à chaque changement de seuil ou de réglage — c'est la pratique dite de *clerical review* /
*hard-negative mining*, documentée dans la littérature du modèle de Fellegi-Sunter (celui que Splink
implémente) et dans les propres recommandations de Splink sur l'examen des paires en désaccord
(`splink.datasets`, revue des `clerical_match_score`). On ne réinvente rien : on applique la même méthode
de contrôle déjà utilisée sur FEBRL (`test.md` §2.1), mais sur un deuxième jeu, français et réel.

### Ce que momo doit construire

1. **`autocal_rapprochement_fr/` (nom à sa main)** — un jeu, séparé du jeu FEBRL et surtout séparé et
   sans aucun rapport avec `autocal/jeux.json` de `grappes` (celui-là reste intouché, voir
   `18-calibrage-26.10.2026.md` §2). Format libre, deux colonnes essentielles : la paire de fiches, et le
   verdict correct (« même entité » / « entités différentes »), avec une courte raison en commentaire
   (« la Fondation héberge l'association, mais ce sont deux personnes morales distinctes »).
2. **Premier cas, déjà connu** : Fondation Abbé Pierre / entité Emmaüs rapprochée à tort → verdict
   « entités différentes ».
3. **Compléter avec une dizaine de cas construits par Sednal ou momo**, sur le même modèle que les cas
   piégés déjà demandés en `test.md` §2.1 (« Club Alpin » Lyon/Paris, deux boulangeries Martin) : des
   paires qui se ressemblent par le nom mais sont des personnes morales différentes (fondation/association
   liées, fédération/club affilié, franchise/enseigne nationale et ses structures locales — un cas très
   français, quasi absent d'un jeu anglophone).
4. **Chaque nouveau faux positif ou faux négatif trouvé en usage réel (Partie 2, sessions, ou usage normal
   après livraison) rejoint ce jeu** plutôt que d'être traité isolément — c'est ce qui le rend utile sur
   la durée, pas une mesure ponctuelle.
5. **Mesure** : précision/rappel par seuil sur ce jeu, exactement comme pour FEBRL — même méthode, même
   tableau. Si le seuil qui marche sur FEBRL échoue sur ce jeu, c'est le signal qu'il faut un réglage
   différent pour le contexte français, pas qu'il faille changer FEBRL.

**Important** : ce jeu est **répétable à volonté**, à l'opposé du jeu test de `autocal/` (brûlé le
12/10/2026, voir `18-calibrage-26.10.2026.md` §2). Rien n'interdit de le rejouer après chaque correction.

## Ce que momo rend

Le jeu lui-même (fichier texte, versionné comme le reste), le tableau précision/rappel par seuil, et la
liste des faux positifs/négatifs restants avec leur score — même format que demandé pour FEBRL dans
`test.md` §2.1.
