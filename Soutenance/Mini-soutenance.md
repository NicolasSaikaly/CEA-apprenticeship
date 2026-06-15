# Discours de soutenance – Bilan d'apprentissage année 1
# Soutenance du 17 juin
**Nicolas SAIKALY – CEA SGLS/LESIM – SALOME platforme**
**Durée cible : 20 minutes**

## SLIDE 1 — Page de titre
**⏱ ~30 secondes**

Bonjour à tous. Je suis Nicolas SAIKALY,apprenti en première année du cycle ingénieur à Polytech Paris Saclay, en spécialité informatique et ingénierie mathématique. On se réunit aujourd'hui pour faire le bilan de ma première année d'apprentissage qui a débuté en Septembre dernier dans le service de génie logiciel au CEA Saclay. Je vais vous présenter les travaux accomplis pendant cette période là, qui représente exactement 16 semaines de travail effectif. Mon sujet porte sur le développement et la modernisation de la plate
forme SALOME et je suis encadré par Christophe Bourcier et Mohd Afeef Badri présent ici avec nous.

---

## SLIDE 2 — Agenda
**⏱ ~30 secondes**

Pour structurer cette présentation, je vais commencer par vous donner le contexte de mon apprentissage en présentant le CEA et la plateforme SALOME. Puis je vais parler en détail des travaux accomplis pendant le premier trimestre notamment sur le meshbooleanplugin, puis je présenterai plus brièvement ceux du second trimestre avec le polymeshplugin pour respecter le temps, et je terminerai par une bilan et les perspectives pour la suite.

---

## SLIDE 3 — Section 1 : Contexte
*(slide de transition)*

---

## SLIDE 4 — Le CEA
**⏱ ~1 minute**

Donc petite présentation du CEA qui est mon entreprise d'accueil. C'est un organisme de recherche public majeur fondé en 1945 par le général de Gaulle pour développer les applications civiles et militaires de l'énergie nucléaire. Il intervient dans différents domaines comme l'énergie bas carbone, l'énergie nucléaire, la défense et les technologies numériques. Le CEA compte aujourd'hui plus de 21 000 salariés répartis sur différents sites en France, moi je suis sur le site de Saclay. Il fait partie également du top 5 national des organismes déposant des brevets, et est membre fondateur et partenaire de l'Université Paris-Saclay.

Mon laboratoire d'accueil est le LESIM, le Laboratoire Environnement de Simulation, dans le service de génie logiciel. C'est un laboratoire orienté développement logiciel scientifique, on fait principalement de l'ingénierie logicielle avec plusieurs projets européens, et notre principal projet open source est la plateforme SALOME. Notre but est donc de fournir à nos ingénieurs chercheurs et physiciens un environnement de travail complet.

---

## SLIDE 5 — Simulations numériques au CEA
**⏱ ~50 secondes**

Donc, comme je viens de le dire, un grand travail d'ingénierie logiciel se fait au CEA, la simulation numérique est centrale. Elle permet de remplacer des expériences physiques coûteuses ou dangereuses ou meme parfois impossible à réaliser — en nucléaire, en mécanique des fluides, en thermique... Pour illustrer un peu ces simulations,j'ai mis des exemples de simulations numérique qui se font au CEA, la visualisation de ligne de courant, champ de pression, simulation d'ecoulement de goutte d'eau,ou meme une simulation HPC sur un réacteur nucléaire et plein d'autres.

Pour rendre ces simulations possibles, on a besoin d'outil pour faire le **pré-traitement** — pour préparer la géométrie et le maillage — et de **post-traitement** — pour analyser les résultats. Donc ici au CEA on utilise un outil qui s'appelle SALOME et ça en vient au coeur de mon sujet.

---

## SLIDE 6 — SALOME – Vue d'ensemble
**⏱ ~1 min 20**

La plateforme SALOME c'est quoi? SALOME est une plateforme de simulation numérique co-développée depuis l'année 2000, par le CEA et EDF et rendu open source depuis 2004. Elle se base sur une architecture en module qui permet de faire le pre et post traitement, avec des modules pour faire la geometrie CAO, le maillage et la visualisation des résultats. En terme de chiffre, elle dépasse les 5000 téléchargements mensuels et compte deux sorties de versions majeures par an. On estime environ 1000 utilisateurs en interne au CEA et EDF. Une étape à ne pas sous estimer ici est l'étape du maillage qui consiste à discrétiser une géométrie en un ensemble fini d'éléments sur lesquels les solveurs vont calculer. C'est une étape fondamentale dans ce workflow, une erreur ici compromet l'intégralité de la simulation en aval. 

Elle est utilisée dans de nombreux contextes comme la formation en ingénierie numérique,la recherche fondamentale en mécanique des fluides et des solides, et dans des secteurs industriels comme l'énergie, la marine, l'aéronautique ou l'automobile.

Ma mission dans cette équipe : je suis apprenti ingénieur au LESIM, et mon travail jusqu'à présent c'est principalement basé dans le module **SMESH** — le module de maillage de SALOME ou j'ai développé et modernisé deux plugin, le meshbooleanplugin et le polymeshplugin.

---

## SLIDE 7 — Section 2 : Premier trimestre
*(slide de transition)*
---

## SLIDE 8 — MeshBooleanPlugin – Vue d'ensemble
**⏱ ~1 min 30**

Donc on va passer au travaux accompli pendant le premier trimestre en commençant par une présentation du meshbooleanplugin
Le MeshBooleanPlugin permet d'effectuer des opérations booléennes entre deux maillages — c'est-à-dire calculer leur union, leur intersection ou leur différence — et d'importer le résultat directement au format natif de maillage .med dans SALOME. Il intègre 6 algorithmes de calcul offrant ainsi une multiplicité de choix à l'utilisateur.

Techniquement, il repose sur du Python pour la logique et l'interface graphique en PyQt, et des algorithmes de calcul en C++ . Il est hébergé sur GitHub donc déja sorti dans les anciennes version de SALOME et doit fonctionner aussi bien sur Linux que sur Windows.

Quand j'ai pris le plugin en charge :
- On avait aucun moyen d'arreter un calcul déja lancé.
- La logique algorithmique et l'interface graphique étaient mélangées dans le même code.
- Il n'y avait ni d'API Python, ni de script de test, ni d'intégration avec le "dump study" de SALOME.

Mon travail a consisté à corriger tout ça.

---

## SLIDE 9 — Types d'opérations booléennes
**⏱ ~45 secondes**

Avant d'entrer dans les détails techniques, je vais vous montrer une illustration concrète de ce que fait le plugin. On part dans cet exemple de deux maillage tetra simple, deux cubes partiellement superposés. On peut alors calculer leur **union** — les deux réunis en un seul maillage —, leur **intersection** — uniquement la partie commune —, ou leur **différence** — l'un soustrait de l'autre qui peut donner un différent résultat en fonction de comment les deux maillages sont choisis. En l'occurence ici, si on inversait le choix des maillage, on aurait eu ça.

---

## SLIDE 10 — Bouton Annuler – Premier défi technique
**⏱ ~2 min 30**

Maintenant passons à la première tache réelle que j'ai accompli, le bouton cancel.
J'ai fait deux essaies avant d'arriver à une solution optimale.
Le problème qu'on avait était que, une fois l'utilisateur lance un calcul, il n'a aucun moyen de l'arreter, devait attendre la fin du calcul ou tuer SALOME entièrement. Ce qui n'était pas pratique sur les longs calculs.

**Première tentative** : j'ai utilisé le module `multiprocessing` de Python. Alors que ça marchait bien sur mon poste en linux, les tests ont montré différemment sur Windows. SALOME devant etre entierement cross platform, j'ai du trouver une méthode plus standard.

**Deuxième tentative** : j'ai cherché une méthode dans la bibliothèque standard Python, trouvant une méthode qui marche bien  et qui est de combiner `subprocess`, qui lance l'algorithme comme un processus externe, avec `QThread` de Qt, qui surveille ce processus de manière asynchrone sans geler l'interface GUI. Quand l'utilisateur clique sur "Annuler", un signal Qt est envoyé au thread de surveillance, qui termine le processus proprement. Après tests, ça marchait aussi bien sur linux que sur windows.

Et par la suite mes changements ont pu etre intégré dans la branche master de SALOME, comme on le voit sur cette pull request, une expérience qui m'a aussi permis de maitriser le rebase interactif de git.

---

## SLIDE 11 — API Python & Indépendance de la GUI
**⏱ ~2 minutes**

Directement après j'ai enchainé dans le meme plugin sur la création d'une API et le couplage entre l'interface graphique et la logique algorithmique.
Il faut savoir que la plateforme SALOME peut etre utilisée dans l'interface graphique, ou via des scripts python. Donc faire une API pour ce plugin permettrai de l'utiliser dans ces scripts là.

J'ai extrait les algorithmes d'operations booléennes dans une API python, ce qui a permis à l'interface PyQt devient alors un simple wrapper qui appelle cette API qui gère déja tout de a à z.

Une fois l'API faite, on peut maintenant lancer directement une **exécution en ligne de commande** sans ouvrir SALOME.
J'ai aussi directement implémenté un script de tests de validation des algos indépendamment de l'interface, avec une marge d'erreur de 5.10-4. Les tests on été fait avec le module subtest de python, qui permet donc de lancer un grand test de chaque opération booléenne (Union, intersection, différence) avec les différents algos présents.

J'en ai aussi profité pour faire du nettoyage de code avec Pylint, une habitude donnée par mes tuteurs de nettoyé chaque fois le code, bien le commenter et y ajouter des loggers pour des potentiels futurs debug. 

Un manque aussi de ce plugin était la non gestion des fichiers temporaires. Faire une opération booléenne générait plusieurs fichiers intermédiaires .off .obj .stl qui restait stocker dans le dossier /tmp de l'environnement sans jamais y etre nettoyé. Pour gérer cela j'ai utilisé les **context managers** Python with try qui créé un répertoire temporaire au début de chaque opération, garantissant la suppression de ces fichiers même en cas de crash.

---

## SLIDE 12 — Exemples
**⏱ ~30 secondes**

Donc on peut voir que le script de test peut etre lancé en terminal en initialisant le SALOME context, dans mon cas j'ai un algo qui n'est présent, donc j'avais 5 algos présent dans mon environnement, mais on remarque que le script ne renvoie pas un échec, parce que les tests sont exécuté uniquement pour les algos disponibles. Donc 5 algo et 3 opérations booléennes, 15 sub test exécuté.

Et c'est aussi le cas pour l'API ou on peut faire une opération booléenne directement en terminal et on remarque à la fin que l'Union a bien réussi avec le fichier .med en sortie et le dossier temporaire effacé.

---

## SLIDE 13 — Intégration Dump Study
**⏱ ~1 min 30**

J'ai parlé tout à l'heure du fait que SALOME peut etre utilisé avec des scripts python. L'integration de ce plugin dans le dump study renforce encore plus cette capacité
Le **dump study** est une fonctionnalité de SALOME qui enregistre automatiquement toutes les actions de l'utilisateur dans l'interface graphique sous forme de script Python — ce qui permet de rejouer des opérations sans interaction manuelle.

On avait un petit problème dans le dump, il enregistrait les importations des fichiers temporaires. Mais vu que à présent ces fichiers était nettoyé après l'opération, le script produit était inutilisable.

Donc j'ai mis en pause l'enregistrement pendant ces étapes intermédiaires et j'injecte directement des appels API propres dans le dump pour chaque opération booléenne réalisée. Résultat : les actions de l'interface sont maintenant enregistrées comme des commandes API lisibles et rejouables. Cela **augmente l'independance entre le GUI et python API** et permet donc à l'utilisateur de jouer avec le script et le relancer comme il le souhaite.
Comme on peut le voir ici un extrait d'un dump study que j'ai fait ou on remarque 3 opérations booléennes que j'ai fait dans l'interface bien enregistrés comme commandes python, et on remarque le .GetMesh fait automatiquement sur des objets SALOME, et sur le intersection_1 on le voit pas car c'est déja un objet python.

---

## SLIDE 14 — Cas d'usage scientifiques
**⏱ ~1 minute**

Passons aux cas d'usage scientifiques, pourquoi fait on des opérations booléennes sur maillage? C'est quoi l'interet? C'est très important de toujours comprendre l'interet du travail qu'on fait.

Les opérations booléennes sur les maillages répondent à un besoin réel en simulation numérique. Elles sont particulièrement utiles quand il n'existe pas de modèle CAO disponible, par ce que en temps normal pour faire une simulation on part d'un modèle CAO - une représentation géométrique précise de l'objet - et on génère le maillage à partir de là. Mais dans certain cas ce modèle n'existe pas, avec des données tomographiques par exemple. Dans ce cas le maillage est la seule représentation disponible, et les opérations booléennes deviennent le seul moyen d'assembler ou de combiner ces géométries.

Deux exemples concrets de ce genre d'opération:
L'intersection entre un maillage surcafique cylindrique et des agrégats de bétons de formes irrégulières. En haut on remarque les deux maillages d'entrée séparés, et puis une vue en coupe du maillage obtenu après intersection. C'est utilisé pour simuler le comportement mécanique des matériaux composites. Un autre exemple serait la modélisation de cellules de batteries lithium-ion. Ici on voit une miscrostructure poreuse d'electrode, on pourrait assembler les composants des cellules pour des simulations de stockage d'energie.

---

## SLIDE 15 — Section 3 : Deuxième trimestre
*(slide de transition)*
Passons maintenant au travaux accomplis pendant le deuxième trimestre avec le polymeshplugin, ici je vais parler plus brièvement pour respecter le temps.

---

## SLIDE 16 — PolyMeshPlugin – Vue d'ensemble
**⏱ ~1 minute**
Le polymeshplugin permet de transformer n'importe quel maillage en **maillage polyédrique**, donc un maillage avec des cellules en forme de polygones. Il intègre trois algorithmes C++ : Polydual, Geogram et cfMesh, chacun produisant un type différent de maillage polyédrique. Les deux algos polydual et Cfmesh se basent sur la librairie OpenFoam, geogram sur la librairie geogram avec l'api vorpalite.

Techniquement, l'architecture est similaire : Python, PyQt, C++, GitHub en interne pour le moment, les changements seront inclus avec la prochaine sortie de SALOME master, et bien evidemment compatible Linux et Windows.

Donc c'était un travail initié par un stagiaire de 5 mois :
La méthode wexpect /pexpect utilisait avait des complications d'utilisations sur windows
L'interface se figeait après un calcul.
Il n'y avait pas de gestion d'erreur propre.

---

## SLIDE 17 — Avantage du maillage polyédrique
**⏱ ~1 minute**


Avant d'entrer des le travail technique, j'aimerai rapidement aborder l'interet des maillages polyhédriques.
Pourquoi s'intéresser aux maillages polyédriques ?Car ils permettent un calcul plus rapide avec un nombre de cellule plus bas. Je vais vous montrer ici des statistiques que j'ai tiré d'une étude qui compare différents types de maillage.
Le maillage polyhédrique est dérivé du maillage tetra directement en formant des polygones autour de chaque noeuds du maillage tetra.
Donc pour ces deux maillages, on remarque le nombre d'éléments volumiques à peu près 5 fois plus petit dans un maillage poly que dans un maillage tetra. Ce qui revient à ce que je viens de dire, un calcul plus rapide.

Autre point, on a une meilleur orthogonalité, donc une marge d'erreur plus petite. Ils prouvent aussi etre meilleurs sur des geométrie complexes.

Donc on peut dire qu'ils représentent **un bon compromis** entre les maillages tétraédriques — faciles à générer mais moins précis en CFD — et les maillages hexaédriques — plus précis mais très difficiles à générer sur des géométries complexes.

Concrètement : pour un même niveau de convergence en simulation CFD, le maillage polyédrique nécessite environ **deux fois moins d'itérations** que le tétraédrique, tout en ayant un nombre de cellules bien inférieur. 

---

## SLIDE 18 — Refactoring cross-platform
**⏱ ~1 min 30**

Maintenant, ce que j'ai fait techniquement dans ce plugin c'est premièrement rendre l'utilisation sur windows encore plus simples. En remplaçant la méthode wexpect/pexpect qui nécessitait un script post install sur windows pour marcher par la méthode que subprocess que j'avais utilisé pour le meshbooleanplugin. Une méthode déja testé et que je connais. Donc résultat meme comportement sur linux et windows sans script d'installation supplémentaire.

Deuxième amélioration : avec l'ancienne méthode on avait une **barre de progression en temps réel**. En passant à subprocess, reproduire cela n'était pas aussi simple. J'ai mis en place une lecture caractère par caractère de la sortie du processus que je stock dans un buffer, le buffer est vidé à chaque espace ou retour à la ligne, analysé pour extraire le pourcentage d'avancement, et chaque étape de l'algorithme déclenche une mise à jour visuelle.

J'ai également ajouté : une boîte de chargement après le calcul pour que l'utilisateur sache que l'opération est finie et le fichier .med est en train de se charger dans l'object browser de SALOME. j'ai aussi implémenté une gestion explicite des erreurs de cfMesh avec un message clair si le maillage est vide. En lisant le fichier point généré par l'opération, et des avertissements en cas de transfert partiel de groupes — au lieu d'un échec silencieux.

---

## SLIDE 19 — Geogram – Refonte architecturale
**⏱ ~2 minutes**

Maintenant la tache dont je suis le plus fièr dans ce second trimestre. L'algorithme geogram du polymeshplugin passait deux étapes. Le fichier produit par l'algorithme était d'abord converti en `.ovm`, puis parsé par des centaines de lignes de Python fragile, avant d'être importé dans SALOME. C'était lent, instable et difficile à maintenir. Donc on m'a demandé d'améliorer cela en abandonnant le format .ovm pour le format .geogram binaire natif.

Donc j'ai développé un **exécutable C++ dédié** appelé `geogram2med`, compilé avec CMake. Il repose sur deux bibliothèques principales : Geogram pour lire le fichier de maillage, et MEDCoupling — une bibliothèque C++ du CEA — pour construire et exporter le maillage au format .med que SALOME comprend nativement. Alors comment ça fonctionne concrètement : premierement on charge le fichier .geogram produit par vorpalite, l'algo de Voronoi avec l'option generate_ids = true. Ensuite il extrait les coordonnées de tous les noeuds dans un tableau de MEDCoupling. Une partie clé est que Geogram stock les facettes 2D avec un attribut cell_id qui indique à quelle cellule polyédrique appartient chaque facette. Puis on reconstruit les polyèdre séparé par des -1 qui est la convention MEDCoupling pour la norme NORM_POLYHED qui représente un polyhèdre quelconque.
Après le programme calcule automatiquement la surface 2D du volume et export le tout dans un fichier .med avec les groupes associés.

Le layer Python n'a plus qu'à importer ce fichier `.med` nativement. Résultat : des centaines de lignes de Python fragile supprimées, un pipeline plus rapide, stable et maintenable.

---

## SLIDE 20 — Cas test avec Geogram
**⏱ ~30 secondes**

Voici un exemple concret sur une géométrie type pipe. Geogram contient une option export seeds qui permet de garder le meme rafinement dans le maillage poly que dans le maillage tetra en faisant de chaque point seed le centre d'une nouvelle cellule générée. À gauche, le maillage tétraédrique de départ. À droite,le maillage obtenu après application de Geogram avec export des seeds, moi j'ai affiché les deux maillage de manière superposée pour voir comment chaque point seed est le centre des nouvelles cellules.

---

## SLIDE 21 — Section 4 : Conclusion & Perspectives
*(slide de transition)*

---

## SLIDE 22 — Bilan & Perspectives
**⏱ ~2 minutes**

Pour conclure, ce que je retiens de cette année, qui n'est pas encore fini et pour moi le plus grand travail reste à venir, mais je suis très satisfait de la progression qui a été faite.

**Sur le plan personnel** : j'ai gagné en autonomie sur Linux, arrivant ici ne sachant rien faire, j'ai également profité d'une formation C++ scientifique moderne de 21 heures dispensée par le CNRS, et j'ai appris en continu tout au long de l'année.

**Compétences techniques acquises** : développement d'interfaces graphiques avec PyQt, architecture logicielle et conception d'API, intégration Python/C++, qualité de code avec les tests unitaires, Pylint et le rebase Git.


**Statut des pull requests** :
- MeshBooleanPlugin : **mergé dans master** mes changement seront intégré dans la prochaine version de SALOME.
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
