# 27 — Leviers sur la règle n° 8, et les 16 bugs de la revue du 27/10

Deux choses dans ce document : les trois leviers demandés par Sednal sur la règle n° 8, et la revue de bugs qui a
suivi. Tout a été écrit et poussé par Claude directement sur `NALSED/GRAPPES`, pas donné à momo.

## Les trois leviers, tels qu'ils marchent

Le principe tenu : **le genre d'une fiche n'est jamais réécrit**. Il dit ce que la collecte a constaté ; un levier
s'ajoute à côté (règle n° 1). C'est ce qui a permis de corriger le bug le plus sérieux de la revue — voir le point 8.

1. **Indices d'institution configurables.** Pas besoin de sortir `DEFAULT_INSTITUTION_HINTS` du code comme envisagé :
   le moteur avait déjà cette liste rendue configurable ailleurs, dans `config/scoring.yaml`
   (`resultat.indices_institution`), lue par `clustering/assign.py`. `pilote/regle8.py` lit maintenant la même, que
   `pont/executer.py` lui passe. Une seule liste, tenue par Sednal, dans un seul fichier.
   **Attention** : renseigner cette clé REMPLACE la liste par défaut, elle ne s'y ajoute pas. Le commentaire du fichier
   le dit désormais.

2. **Levée au cas par cas, sur une fiche.** `tri.marquer_personne_morale` écrit `faits.regle8_leve` (qui, quand) et
   ne touche à rien d'autre : le genre reste « personne ». C'est `rapprochements.proposer` qui lit cette levée pour
   laisser la fiche entrer dans le lot des candidats. **Réversible** : `tri.annuler_personne_morale` retire la levée
   et les paires encore en attente ; une paire déjà tranchée reste lisible. Routes `POST` et `DELETE` sur
   `/api/recherches/{rid}/noms/{cid}/personne_morale`, deux boutons dans l'écran selon l'état.

3. **Levée au moment de lancer une recherche.** Case « Afficher les comptes écartés par la règle n° 8 dans cette
   recherche » dans les options avancées. Le paramètre `regle8_afficher_comptes` traverse `lancement.valider_options`
   puis `executeur_grappes.preparer` jusqu'à `lancement.json`. Le pont laisse alors le genre déclaré par la source —
   **mais pose quand même le constat** dans `faits.regle8_personne`, et écrit au journal de la recherche que la règle a
   été levée, pour que ça se lise après coup et pas seulement avant.

Un interrupteur global a été écarté : le temps qu'il serait actif, il donnerait la capacité de rechercher et de
rapprocher sur des personnes physiques nommées, ce que la règle n° 8 interdit (base CNIL/RGPD). Les trois leviers
restent scopés — une liste de mots, une fiche, une recherche — et chacun passe par une décision humaine avant tout
rapprochement effectif.

## Les 16 bugs de la revue, et ce qui a été fait

Revue demandée le 27/10 au soir, sur tout le code, bugs seulement. Cinq étaient reproduits par exécution.
**Les 16 sont corrigés**, chacun avec le test qui le prouve quand il est testable.

### Bloquants à l'installation sur une machine neuve

| # | Où | Le bug | Le correctif |
| :-- | :-- | :-- | :-- |
| 1 | `installer.sh` | `pip install -e grappes` échouait sur **pyicu** (dépendance de followthemoney) avec un message illisible. Reproduit 3 fois. | Contrôle des paquets système AVANT pip (`pkg-config`, `libicu-dev`, `python3-dev`, compilateur, `venv`), avec la commande `apt`/`dnf` à copier. `installer_grappes.sh` les installe lui-même. |
| 2 | `installer.sh` l. 11 et 19 | `|| true` masquait l'échec de torch-CPU : pip reprenait ensuite le **torch GPU** (plusieurs Go de CUDA) ; idem pour `pg8000`/`pymysql`, dont l'absence ne se voyait qu'à l'usage. | Les deux échouent franchement, avec la commande de rattrapage. |
| 3 | `suite_script.py` l. 57 | Repli `cmd[3:]` au lieu de `cmd[2:]` : **l'interpréteur était perdu**. Là où `unshare` existe mais est refusé (noyau sans espaces de noms non privilégiés, conteneur sans droits), « Tester le script » levait `FileNotFoundError: '-I'` → 500. Reproduit. | `cmd[2:]`, et `OSError` rattrapée pour que rien ne sorte en pile d'appels. Reproduction rejouée : le script s'exécute. |
| 4 | `outils/installer_service.sh` | Service systemd **utilisateur** sans `loginctl enable-linger` : pilote mourait à la déconnexion SSH. | `enable-linger` demandé, et dit clairement s'il est refusé. |
| 5 | `tls.py` l. 20 | `openssl` appelé sans vérifier sa présence (`FileNotFoundError` au démarrage sur une image minimale), et certificat limité à `localhost`/`127.0.0.1` : inutilisable depuis un autre poste. | Absence d'`openssl` dite en français (`TlsIndisponible`, rattrapée dans `__main__`), et le certificat porte le nom de la machine, son adresse, l'adresse d'écoute et les `hotes_autorises`. Vérifié : `DNS:localhost, IP:127.0.0.1, DNS:<machine>`. |

### Garde-fous qui ne gardaient rien

| # | Où | Le bug | Le correctif |
| :-- | :-- | :-- | :-- |
| 6 | `importer_base_locale.py` l. 119-125 | Sans `Content-Length`, la taille valait 0 : le contrôle de place disque **ne se déclenchait jamais** et la taille reçue n'était plus vérifiée — une page d'erreur HTTP pouvait être enregistrée à la place de l'archive. Reproduit. | Marge fixe exigée quand la taille est inconnue, et **signature d'archive vérifiée** (`PK\x03\x04`, gzip, bzip2) : une page HTML est refusée avec sa cause. |
| 7 | `gdelt_local.py` l. 108 | `pool.map` sur toute la fenêtre soumettait les ~8 600 quarts d'heure d'un coup ; les résultats terminés s'empilaient en mémoire. Pic non borné sur 90 jours. | Traitement par tranches (`fils × 4`), `executemany` au lieu d'un `INSERT` par ligne, validation en fin de tranche. |
| 8 | `regle8` + `formats/ftm.py` l. 82 | **Le plus sérieux.** Les leviers basculaient `genre_de_chose` ; or la sortie FollowTheMoney filtre sur le même champ. Un levier prévu pour le seul rapprochement ouvrait donc un export qui garantit le contraire. Reproduit : `ignorees_personnes_physiques` passait de 1 à 0 et le nom était écrit. | Le genre n'est plus jamais réécrit. Le **constat** (`faits.regle8_personne`) suit la fiche et tient la porte de FtM ; seule une **décision humaine fiche par fiche** (`regle8_leve`) la rouvre. Un interrupteur de recherche ne suffit plus. Test : les quatre cas. |
| 9 | `tri.py` | La levée était annoncée réversible et ne l'était pas : valeur d'origine perdue, aucune action inverse. | Rien n'est écrasé, `annuler_personne_morale` ajouté, testé dans les deux sens. |
| 10 | `tri.py` | La levée supposait que « personne » venait toujours de la règle n° 8 — vrai aujourd'hui, faux le jour où une source déclare ce genre. | Le constat est posé à la collecte et c'est lui qui autorise la levée, plus une déduction sur le genre. |
| 11 | `tri.py` | La levée relançait Splink (sous-processus, délai 120 s) **dans la requête du clic**, sans indicateur d'attente. | La levée rend la main tout de suite ; l'écran enchaîne sur la route « proposer » qui existait déjà, bouton désactivé et message pendant l'attente. |
| 12 | `tri.py` | `Indisponible` avalée et rendue comme `{"proposes": 0}` : indistinguable d'un succès sans résultat. | L'indisponibilité remonte et s'affiche : « Levée faite, mais le moteur de rapprochement n'a pas tourné : … ». |
| 13 | `executeur_grappes.py` | Une recherche menée règle n° 8 levée ne le disait **nulle part après coup**. | Événement `regle8_levee_pour_la_recherche` au journal de la recherche, avec sa portée et son effet. |
| 14 | `pont/executer.py` l. 246 | `except BaseException` par source : avalait aussi `KeyboardInterrupt` et `SystemExit`. | `except Exception`. |

### Tests qui ne disaient pas la vérité

| # | Où | Le bug | Le correctif |
| :-- | :-- | :-- | :-- |
| 15 | `tests/test_squelette.py` l. 46 | Le test affirmait `grappes_racine == "/opt/grappes"` : vrai seulement sur la machine de momo, faux partout ailleurs alors que la configuration est juste. | On vérifie que la racine se résout sur le dossier voisin du dépôt et qu'elle porte la configuration du moteur, plus un chemin de déploiement. |
| 16 | `tests/test_regle8_personnes_morales.py` l. 42 | Le test **le plus sensible de la règle n° 8** lisait la copie temporaire de la fixture, où `../grappes` ne mène nulle part : il échouait sur son propre montage. | Il lit la configuration livrée (`charger()`), donc il dit quelque chose partout. |

## Installation complète

`installer_grappes.sh`, à la racine du dépôt, fait tout depuis une machine neuve et **propose chaque prérequis sans
l'imposer** : paquets système, environnements Python, les six bases locales (toutes, ou une par une), la fenêtre GDELT,
SearXNG, LibreTranslate, Overpass, le service systemd, puis le lancement. `--tout` pour ne rien demander, `--minimal`
pour le strict nécessaire, une option par étape pour rejouer une installation à l'identique. Reprenable ; un prérequis
qui échoue n'arrête pas les autres et la liste de ce qui reste à faire est donnée à la fin.

Il résout aussi les adresses SIRENE auprès de l'API de data.gouv.fr avant de télécharger : celles de la déclaration
sont horodatées (`…/20261001-072119/…`) et finissent par disparaître.

## Dimensionnement de la VM

Processeur **« host »** sur Proxmox, pas `kvm64` : `duckdb` est épinglé `<2` parce que la version 2.0 plante en
« instruction illégale » sur un processeur virtuel qui masque AVX2, et `torch` est sensible de la même façon.

| | Minimum | Confortable |
| :-- | :-- | :-- |
| RAM | 8 Go | **16 Go** |
| vCPU | 4 | **8** |
| Disque | 60 Go | **100 Go** (+100 Go à part pour Overpass) |

Tailles mesurées le 27/10 : SIRENE 0,98 + 2,88 Go d'archives (≈ 20 Go indexés, import de plusieurs heures,
mono-cœur), RNA 0,10 + 0,41 Go. Debian 12/13 ou Ubuntu 24.04, Python ≥ 3.11.
