#Discours de soutenance visite en entreprise du 13 avril
# Discours de soutenance – Bilan d'apprentissage Année 1
**Nicolas SAIKALY – CEA SGLS/LESIM – SALOME Platform**
**Durée cible : 20 minutes**

---

> 💡 **Conseil général** : parle lentement, regarde le jury, et ne lis pas tes slides — utilise ce discours comme guide, pas comme script mot pour mot.

---

## SLIDE 1 — Page de titre
**⏱ ~30 secondes**

Bonjour à tous. Je m'appelle Nicolas Saikaly, je suis en première année d'apprentissage à Polytech Paris-Saclay, au sein du laboratoire LESIM du CEA. Cette année, j'ai travaillé sur la plateforme SALOME, et c'est ce travail que je vais vous présenter aujourd'hui.

---

## SLIDE 2 — Agenda
**⏱ ~30 secondes**

Pour structurer cette présentation, je vais commencer par vous donner le contexte de mon apprentissage et de la plateforme SALOME. Ensuite, je vous présenterai mes contributions du premier trimestre sur le MeshBooleanPlugin, puis celles du second trimestre sur le PolyMeshPlugin. Je terminerai par un bilan et les perspectives pour la suite.

---

## SLIDE 3 — Section 1 : Contexte
*(slide de transition)*

---

## SLIDE 4 — Le CEA
**⏱ ~1 minute**

Mon apprentissage se déroule au CEA, le Commissariat à l'Énergie Atomique et aux Énergies Alternatives. C'est un organisme de recherche public fondé en 1945, qui compte environ 20 000 employés répartis sur plusieurs sites en France. Il intervient dans des domaines comme l'énergie nucléaire, la défense et les technologies numériques.

Mon laboratoire d'accueil s'inscrit dans une chaîne hiérarchique assez longue : je suis rattaché à la Direction des Énergies, plus précisément à l'ISAS — l'Institut des sciences appliquées et de la simulation pour les énergies bas carbone — au sein du Service de Génie Logiciel, dans le Laboratoire Environnement de Simulation, le LESIM.

C'est un laboratoire orienté développement logiciel scientifique, qui développe et maintient des plateformes open source majeures comme SALOME, mais aussi des projets classifiés.

---

## SLIDE 5 — Simulations numériques au CEA
**⏱ ~1 minute**

Au CEA, la simulation numérique est centrale. Elle permet de remplacer des expériences physiques coûteuses ou dangereuses — en nucléaire, en mécanique des fluides, en thermique... Les images que vous voyez à l'écran illustrent concrètement ces simulations : lignes de courant, champs de pression, structures complexes en 3D.

Pour que ces simulations soient possibles, il faut des outils de **pré-traitement** — pour préparer la géométrie et le maillage — et de **post-traitement** — pour analyser les résultats. C'est précisément le rôle de SALOME.

---

## SLIDE 6 — SALOME – Vue d'ensemble
**⏱ ~1 min 30**

SALOME est une plateforme de simulation numérique développée depuis 2000, co-maintenue par le CEA et EDF, et disponible en open source depuis 2016. Elle offre des fonctionnalités de création de géométrie, de maillage et de visualisation. Son architecture modulaire permet de l'étendre via des plugins.

Elle est utilisée dans de nombreux contextes : formation en ingénierie numérique, recherche fondamentale en mécanique des fluides et des solides, et dans des secteurs industriels comme l'énergie, la marine, l'aéronautique ou l'automobile.

Ma mission dans cette équipe : je suis apprenti ingénieur au LESIM, et je travaille principalement sur le module **SMESH** — le module de maillage de SALOME. Mon travail consiste à développer et moderniser deux plugins : le **MeshBooleanPlugin** et le **PolyMeshPlugin**.

---

## SLIDE 7 — Section 2 : Premier trimestre
*(slide de transition)*

---

## SLIDE 8 — MeshBooleanPlugin – Vue d'ensemble
**⏱ ~1 min 30**

Le MeshBooleanPlugin permet d'effectuer des opérations booléennes entre deux maillages — c'est-à-dire calculer leur union, leur intersection ou leur différence — et d'importer le résultat directement dans SALOME.

Techniquement, il repose sur du Python pour la logique et l'interface graphique en PyQt, et des algorithmes en C++ pour le calcul. Il est hébergé sur GitHub et doit fonctionner aussi bien sur Linux que sur Windows.

Quand j'ai pris le plugin en charge, voici l'état dans lequel il se trouvait :
- Il était caché dans le menu, sans icône visible.
- Si on lançait un calcul, il était impossible de l'arrêter — il fallait tuer SALOME entièrement ou attendre la fin.
- La logique algorithmique et l'interface graphique étaient mélangées dans le même code.
- Il n'y avait ni API Python exposée, ni intégration avec le "dump study" de SALOME.

Mon travail a consisté à corriger tout ça.

---

## SLIDE 9 — Types d'opérations booléennes
**⏱ ~45 secondes**

Avant d'entrer dans les détails techniques, voici une illustration concrète de ce que fait le plugin. On part de deux maillages qui se chevauchent — ici deux cubes partiellement superposés. On peut alors calculer leur **union** — les deux réunis en un seul maillage —, leur **intersection** — uniquement la partie commune —, ou leur **différence** — l'un soustrait de l'autre. Ces opérations sont fondamentales dans les workflows de simulation numérique.

---

## SLIDE 10 — Bouton Annuler – Premier défi technique
**⏱ ~2 minutes**

Le premier problème majeur : une fois un calcul lancé, l'utilisateur n'avait aucun moyen de l'arrêter.

**Première tentative** : j'ai utilisé le module `multiprocessing` de Python, qui permet de lancer l'algorithme dans un processus séparé et de le tuer sur clic d'annulation. Ça fonctionnait parfaitement sur Linux, mais pas sur Windows — les processus y sont gérés différemment. C'est ma **première leçon de l'année** : SALOME doit être entièrement cross-platform, et cette contrainte ne peut pas être ignorée.

**Deuxième tentative** : j'ai cherché une méthode dans la bibliothèque standard Python. La solution : combiner `subprocess`, qui lance l'algorithme comme un processus externe, avec `QThread` de Qt, qui surveille ce processus de manière asynchrone. Quand l'utilisateur clique sur "Annuler", un signal Qt est envoyé au thread de surveillance, qui termine le processus proprement.

Résultat : comportement identique sur Linux et Windows. La pull request a été soumise, revue par l'équipe, les tests ont été validés, et elle a été **mergée avec succès** dans la branche principale.

---

## SLIDE 11 — API Python & Indépendance de la GUI
**⏱ ~1 min 30**

Deuxième chantier majeur : le couplage entre l'interface graphique et la logique algorithmique.

J'ai extrait les algorithmes dans une **API Python autonome**. L'interface PyQt devient alors un simple wrapper léger qui appelle cette API, sans contenir de logique métier.

Ça permet notamment :
- Une **exécution en ligne de commande** sans lancer SALOME du tout.
- La **validation des algorithmes** indépendamment de l'interface, avec une précision vérifiée à un seuil de 5×10⁻⁴.

J'en ai aussi profité pour faire du nettoyage de code avec Pylint, et pour gérer les fichiers temporaires avec des **context managers** Python — ce qui garantit leur suppression même en cas de crash.

---

## SLIDE 12 — Exemples
**⏱ ~30 secondes**

Voici deux illustrations concrètes : à gauche, les tests unitaires qui tournent en terminal en configurant simplement le contexte SALOME — 15 sous-tests, exécutés en 13 secondes, résultat OK. À droite, l'API appelée directement en Python interactif, sans interface graphique.

---

## SLIDE 13 — Intégration Dump Study
**⏱ ~1 min 30**

Le **dump study** est une fonctionnalité de SALOME qui enregistre automatiquement toutes les actions de l'utilisateur dans l'interface graphique sous forme de script Python — ce qui permet de rejouer des opérations sans interaction manuelle.

Le problème : les exports de fichiers intermédiaires que faisait le plugin parasitaient ce script généré, le rendant inutilisable tel quel.

Ma contribution : j'ai mis en pause l'enregistrement pendant ces étapes intermédiaires, et j'injecte directement des appels API propres dans le dump. Résultat : les actions de l'interface sont maintenant enregistrées comme des commandes API lisibles et rejouables. Cela **comble le fossé entre la GUI et l'API Python**, et rend le plugin utilisable dans des workflows d'automatisation.

---

## SLIDE 14 — Cas d'usage scientifiques
**⏱ ~1 minute**

Les opérations booléennes sur les maillages répondent à un besoin réel en simulation numérique. Elles sont particulièrement utiles quand il n'existe pas de modèle CAO disponible — par exemple avec des données tomographiques. L'idée est qu'elles remplacent partiellement ce que fait l'outil commercial MG-Cleaner.

Deux exemples concrets développés cette année :
- **Batteries lithium-ion** : opérations booléennes entre les composants d'une cellule, chacun coloré et identifié individuellement pour des simulations de stockage d'énergie.
- **Béton et agrégats** : intersection d'un maillage surfacique cylindrique avec des agrégats de béton, pour modéliser le comportement mécanique de matériaux composites — la coupe permet de visualiser le volume intérieur.

---

## SLIDE 15 — Section 3 : Deuxième trimestre
*(slide de transition)*

---

## SLIDE 16 — PolyMeshPlugin – Vue d'ensemble
**⏱ ~1 minute**

Le PolyMeshPlugin est le second plugin sur lequel j'ai travaillé. Son rôle : transformer n'importe quel maillage en **maillage polyédrique**. Il intègre trois algorithmes C++ : Polydual, Geogram et cfMesh, chacun produisant un type différent de maillage polyédrique.

Techniquement, l'architecture est similaire : Python, PyQt, C++, GitHub, Linux et Windows.

Quand j'ai pris la suite d'un stage de 5 mois :
- La méthode `wexpect/pexpect` utilisée ne fonctionnait pas correctement sur Windows.
- L'interface se figeait après un calcul.
- Il n'y avait pas de gestion d'erreur propre.

---

## SLIDE 17 — Avantage du maillage polyédrique
**⏱ ~1 minute**

Pourquoi s'intéresser aux maillages polyédriques ? Parce qu'ils représentent **un bon compromis** entre les maillages tétraédriques — faciles à générer mais moins précis en CFD — et les maillages hexaédriques — plus précis mais très difficiles à générer sur des géométries complexes.

Concrètement : pour un même niveau de convergence en simulation CFD, le maillage polyédrique nécessite environ **deux fois moins d'itérations** que le tétraédrique, tout en ayant un nombre de cellules bien inférieur. Il offre une meilleure orthogonalité et s'adapte mieux aux géométries complexes.

---

## SLIDE 18 — Refactoring cross-platform
**⏱ ~1 min 30**

Premier chantier : remplacer `wexpect/pexpect` par `subprocess`, la bibliothèque standard Python. Même comportement sur Linux et Windows, sans script d'installation supplémentaire.

Deuxième amélioration : une **barre de progression en temps réel**. J'ai mis en place une lecture caractère par caractère de la sortie du processus — le buffer est vidé à chaque espace ou retour à la ligne, analysé pour extraire le pourcentage d'avancement, et chaque étape de l'algorithme déclenche une mise à jour visuelle.

J'ai également ajouté : une boîte de chargement post-calcul pour que l'utilisateur sache toujours ce qu'il se passe, une gestion explicite des erreurs de cfMesh avec un message clair si le maillage est vide, et des avertissements en cas de transfert partiel de groupes — au lieu d'un échec silencieux.

---

## SLIDE 19 — Geogram – Refonte architecturale
**⏱ ~2 minutes**

Le chantier le plus ambitieux du semestre : repenser complètement le pipeline de conversion pour l'algorithme Geogram.

**Avant** : le fichier `.geogram` produit par l'algorithme était d'abord converti en `.ovm`, puis parsé par des centaines de lignes de Python fragile, avant d'être importé dans SALOME. C'était lent, instable et difficile à maintenir.

**Après** : j'ai développé un **exécutable C++ dédié** appelé `geogram2med`. Il lit le fichier `.geogram` directement via la bibliothèque Geogram, extrait les coordonnées des nœuds dans un tableau MEDCoupling, regroupe les facettes 2D par attribut `cell_id`, reconstruit les polyèdres 3D avec la norme `NORM_POLYHED`, calcule la surface 2D du volume, et exporte directement en `.med`.

Le layer Python n'a plus qu'à importer ce fichier `.med` nativement. Résultat : des centaines de lignes de Python fragile supprimées, un pipeline plus rapide, stable et maintenable.

---

## SLIDE 20 — Cas test avec Geogram
**⏱ ~30 secondes**

Voici un exemple concret sur une géométrie type "coude de tuyau". À gauche, le maillage tétraédrique de départ. À droite, après application de Geogram avec export des seeds, le maillage polyédrique obtenu — où chaque point seed devient le centre d'une cellule, ce qui permet de conserver le même niveau de raffinement que dans le maillage tétraédrique d'origine.

---

## SLIDE 21 — Section 4 : Conclusion & Perspectives
*(slide de transition)*

---

## SLIDE 22 — Bilan & Perspectives
**⏱ ~2 minutes**

Pour conclure, cette première année m'a permis de progresser sur plusieurs plans.

**Compétences techniques acquises** : développement d'interfaces graphiques avec PyQt, architecture logicielle et conception d'API, intégration Python/C++, qualité de code avec les tests unitaires, Pylint et le rebase Git.

**Croissance personnelle** : j'ai gagné en autonomie sur Linux, j'ai suivi une formation C++ scientifique moderne de 21 heures au CNRS, et j'ai appris en continu tout au long de l'année.

**Statut des pull requests** :
- MeshBooleanPlugin : **mergé dans master**.
- PolyMeshPlugin : **en attente de merge**, sans conflits.

**Perspectives pour l'année 2** :
- Intégration d'algorithmes de maillage hexaédrique dans un workflow multi-outils.
- Algorithme de couche prismatique issu de code_saturne, le solveur CFD open source.

**Objectif global sur 3 ans** : monter progressivement en compétence, prendre en charge des problèmes d'ingénierie de plus en plus complexes, et contribuer à faire de SALOME une plateforme de simulation prête pour la production.

---

## SLIDE 23 — Merci / Questions

Merci pour votre attention. Je suis maintenant disponible pour répondre à vos questions.

---

## 🕐 Minutage indicatif

| Section | Durée estimée |
|---|---|
| Titre + Agenda | ~1 min |
| Contexte (CEA, simulations, SALOME) | ~3 min |
| MeshBooleanPlugin (slides 8 à 14) | ~8 min |
| PolyMeshPlugin (slides 16 à 20) | ~6 min |
| Bilan & Perspectives | ~2 min |
| **Total** | **~20 min** |

---

## 💬 Questions fréquentes à anticiper

**Q : Pourquoi avoir choisi subprocess plutôt qu'une autre approche ?**
> Parce que c'est une bibliothèque standard Python, donc sans dépendance externe, et elle a un comportement identique sur Linux et Windows — ce qui est une contrainte non négociable pour SALOME.

**Q : Quelle est la différence entre Polydual, Geogram et cfMesh ?**
> Ce sont trois algorithmes C++ différents intégrés dans le plugin, qui produisent des types de maillages polyédriques différents. Polydual est basé sur la dualisation du maillage tétraédrique, Geogram utilise des diagrammes de Voronoï, et cfMesh est un générateur de maillage polyédrique plus général.

**Q : Quel est l'état de maturité des plugins à l'issue de cette année ?**
> Le MeshBooleanPlugin est mergé en production. Le PolyMeshPlugin est fonctionnel, les tests passent, il attend simplement la revue finale pour être mergé.

**Q : Qu'est-ce que le dump study exactement ?**
> C'est une fonctionnalité native de SALOME qui enregistre toutes les actions de l'utilisateur dans l'interface graphique sous forme de script Python rejouable. Mon travail a permis que les opérations du MeshBooleanPlugin soient correctement enregistrées dans ce script.
