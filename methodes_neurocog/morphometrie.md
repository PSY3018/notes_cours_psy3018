---
jupytext:
  cell_metadata_filter: -all
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.10.3
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---
(morphometrie-chapitre)=
# Analyses morphométriques

<table>
  <tr>
    <td align="center">
      <a href="https://github.com/eddyfortier">
        <img src="https://avatars.githubusercontent.com/u/72314243?v=4?s=100" width="100px;" alt=""/>
        <br /><sub><b>Eddy Fortier</b></sub>
      </a>
      <br />
        <a title="Contenu">🤔</a>
    </td>
    <td align="center">
      <a href="https://github.com/pbellec">
        <img src="https://avatars.githubusercontent.com/u/1670887?v=4?s=100" width="100px;" alt=""/>
        <br /><sub><b>Pierre bellec</b></sub>
      </a>
      <br />
        <a title="Contenu">🤔</a>
    </td>
  </tr>
</table>

```{warning}
Ce chapitre est en cours de développement. Il se peut que l'information soit incomplète, ou sujette à changement.
```

## Objectifs du cours

Ce cours introduit différentes approches pour quantifier la morphologie du cerveau à l'aide des données d'imagerie par résonance magnétique anatomique. On va tout d'abord présenter trois grandes approches d'analyses:
 * la **volumétrie manuelle**, qui vise a mesurer la taille d'une région cérébrale;
 * la **morphométrie basée voxel (*voxel-based morphometry* ou VBM)**, qui vise à mesurer le volume de matière grise systématiquement partout dans le cerveau;
 * les **analyses de surface**, qui exploitent la structure en ruban de la matière grise.

On parlera également d'étapes d'analyse d'images utiles pour l'ensemble de ces techniques, telles que le **recalage**, la **segmentation**, et le **contrôle de qualité**.

## Morphométrie

```{figure} ./morphometrie/morphometrie_durer.jpg
---
width: 600px
name: morphometrie-durer-fig
---
Étude de Dürer sur les proportions du visage. Image sous domaine public, tirée de [wikimedia](https://commons.wikimedia.org/wiki/File:Morpho_durer.JPG).
```

En neurosciences, la [morphométrie](https://fr.wikipedia.org/wiki/Morphom%C3%A9trie) est l'étude de la forme du cerveau et de ses structures.
Le terme morphométrie vient de deux termes tirés du grec ancien: *morphos* (forme) et *métron* (mesure).
La morphométrie est donc la "mesure" de la "forme".
Cette discipline se concentre sur la caractérisation des dimensions et des formes des différentes structures d'intérêt.
Pour ce faire, il est nécessaire de pouvoir observer clairement les délimitations de ces structures.

```{figure} ./morphometrie/ledig2018.webp
---
width: 600px
name: ledig2018-fig
---
Cette figure illustre les différences morphologiques entre individus qui présentent des profils cliniques différents: cognitivement normal (haut), troubles légers de la cognition (milieu), démence de type Alzheimer (bas). Par ailleurs on peut également observer des différences longitudinales au sein d'un même individu (de gauche à droite, visite initiale, suivi à deux ans, différence des deux images). Figure tirée de {cite:p}`Ledig2018-ai`, sous licence CC-BY.
```
L'utilisation de ce genre de technique permet aussi de faire des comparaisons inter-individuelles.
On pourrait en effet vouloir comparer les variations dans la forme de divers structures à travers les cerveaux de différentes personnes.
De telles comparaisons peuvent être informatrices au niveau du stade développemental d'un sujet, ou même, de la présence de certaines lésions ou pathologies.

## volumétrie

## Segmentation manuelle

La **volumétrie manuelle** constiste à manuellement délimiter une aire cérébrale particulière, comme l'hippocampe. Cette approche nécessite du temps, car chaque coupe d'IRM doit être délimitée manuellement. On commencera d'abord par identifier ce contour sur chaque coupe où la structure est présente dans un premier plan (par exemple, sur une coupe axiale), puis il faudra aller corriger cette délimitation sur chaque coupe prise dans un second plan (comme une coupe sagitale) et finalement, répéter de nouveau cette correction sur le troisième plan (une coupe coronale).

> Pour un rappel concernant termes et les différents types de coupes du cerveau, veuillez vous référer au [Chapitre 1: Cartes cérébrales](https://psy3018.github.io/cartes_cerebrales.html#irm-structurelle).

Ce type d'approche requiert également un protocole de segmentation rigoureux, avec des critères anatomiques claires pour décider où une région cérébrale se trouve. Pour certaines structures, comme les ventricules latéraux, c'est assez clair. Pour d'autres structures, comme l'hippocampe, il existe des protocoles détaillés, voir par exemple {cite:p}`Wisse2017-ff`. Enfin pour d'autres régions, comme les aires visuelles V1, V2, etc, il est nécessaire de réaliser des expériences fonctionnelles pour les délimiter, et les délimitations anatomiques ne sont pas nécessairement disponibles ou bien établies.

Un protocole de segmentation clair est nécessaire pour assurer un bon niveau de reproductibilité des résultats et un accord inter-juge acceptable. Certains protocoles offrent une certification, qui offre un certain niveau de garantie que la chercheuse effectuant la segmentation applique le protocole correctement.

## Segmentation automatique

Afin de faciliter la standardisation de la segmentation, il est possible d'utiliser des cartes préétablies par des équipes de chercheurs.
On appelle ces cartes des **atlas de segmentation**.
Ceux-ci sont développés par des équipes de scientifiques afin de permettre une segmentation robuste de certaines structures d'intérêt.
Comme il existe une variété d'atlas permettant de rencontrer divers besoin en terme de segmentaton, il est important de choisir adéquatement celui qui sera utilisé en fonction des structures particulières que vous voulez étudier.

```{code-cell} ipython 3
:tags: ["hide-input"]

# Téléchargement de l'atlas Harvard-Oxford
from nilearn import datasets

dataset = datasets.fetch_atlas_harvard_oxford('cort-maxprob-thr25-2mm')
atlas_filename = dataset.maps

# Visualisation de la figure
from myst_nb import glue
from nilearn import plotting

fig = plotting.plot_roi(atlas_filename,
                        title="Harvard Oxford atlas",
                        cut_coords=(8, -4, 9),
                        colorbar=True,
                        cmap='Paired')

glue("atlas1-fig", fig, display=False)
```

```{glue:figure} atlas1-fig
:figwidth: 800px
:name: "atlas1-fig"

Un exemple de segmentation utilisant l'atlas Harvard-Oxford sur trois plans de coupes: coronal (gauche), sagital (milieu) et axial (droite).
Voir l'astuce {ref}`Naviguer à travers les coupes du cerveau<coupes-tip>` pour une explication de ces termes.
Cette figure est générée par du code python à l'aide de la librairie [nilearn](https://nilearn.github.io/) à partir d'un jeu de données public appelé fetch_atlas_harvard_oxford ([Nilearn, section 9.2.1: Basic Atlas plotting](https://nilearn.github.io/auto_examples/01_plotting/plot_atlas.html)) {cite:p}`MAKRIS2006155, Frazier2005, DESIKAN2006968, GOLDSTEIN2007935` (cliquer sur + pour voir le code).
```

## Morphométrie basée voxel (*Voxel-based morphometry*)


L'**approche par voxel**, aussi mieux connue sous le nom de ***voxel-based morphometry*** (ou **VBM**), est une autre approche qu'il est possible d'employer afin de segmenter différentes aires d'intérêt du cerveau.
Son objectif est de mesurer la densité de matière grise à l'intérieur et immédiatement autour d'un voxel donné.
Cette approche est donc moins limitée par le besoin d'avoir des frontières préétablies claires entre les différentes structures à l'étude.
Lorsque répétée pour l'entièreté du volume du cerveau, on peut obtenir une carte tridimensionnelle de la densité de matière grise à travers celui-ci.
L'avantage premier de cette approche est son économie au niveau du temps nécessaire à un.e chercheur.e pour l'étape de la segmentation.
En effet, comme cette technique présente une approche de segmentation automatisée, la présence d'une personne externe ne devient nécessaire que lors de l'étape de la vérification de la segmentation.
Par contre, cette approche ayant une quantité importante de points de mesure (liés à chaque voxel étudié), elle pose aussi un sérieux problème de **comparaisons multiples** lorsque vient le temps de faire les analyses statistiques.

> Les particularités des analyses statistiques en neuroimagerie seront vues en détail lors du [Chapitre 6: Régression linéaire](https://psy3018.github.io/regression.html).
>
> Les particularités des corrections à apporter lors de ces analyses statistiques seront vues en détail lors du [Chapitre 10: Cartes statistiques](https://psy3018.github.io/cartes_statistiques.html).

Le traitement des données en VBM suit un processus en quatre étapes:
1. La segmentation
2. Le recalage dans un espace stéréotaxique de référence
3. Le lissage spatial
4. Les analyses statistiques

La **segmentation** est la première étape de la séquence.
Dans le contexte de la VBM, qui est une analyse de la matière grise, l'objectif principal de cette étape est de déterminer s'il y a de la matière grise dans chacun des voxels.
Un des types de segmentation automatisée qu'il est possible d'utiliser pour obtenir cette information est la segmentation probabiliste.
Dans ce genre d'algorithme, on essaie d'attribuer une étiquette concernant le contenu du voxel (air autour de la tête, liquide céphalo-rachidien, matière grise, matière blanche, etc.).
Pour ce faire, on établit la probabilité que la valeur du voxel appartienne à l'une ou l'autre des catégories possibles.
La segmentation retournera donc une carte des voxels contenant probablement de la matière grise.
Il est en effet possible que la segmentation automatique nous retourne certains autres tissus non-désirés, mais dont les valeurs étant similaires à celle de la matière grise, ne sont pas distinguées par l'algorithme de segmentation.
Il est aussi possible que des voxels se trouvant directement sur la jonction entre une zone blanche et une zone noire (par exemple, sur une paroi de matière blanche qui borderait un ventricule) aient comme valeur résultante une valeur s'apparentant plutôt au gris associé à la matière grise (valeur moyenne entre blanc et noir).
On appelle ce genre d'effet de mélange de noir et de blanc les volumes partiels (une partie du volume du voxel est blanche alors que l'autre partie est noire).
- Ce genre d'erreur est une source possible de **faux positifs**.

Il est aussi possible de perdre certaines structures pour lequelles le contraste entre matière blanche et matière grise ne seraient pas assez important pour que l'algorithme réussisse à les classer efficacement.
Pour ce genre de structure, il est important d'ajouter des a priori (des règles/conditions supplémentaires) dans notre algorithme de traitement afin de ne pas les perdre.
Il est aussi envisageable d'effectuer cette partie de la segmentation de façon manuelle.
- Ce genre d'erreur est une source possible de **faux négatifs**.

La seconde étape est l'étape du **recalage dans un espace stéréotaxique de référence** (*coregistration* en anglais).
Celle-ci sert à pouvoir mettre en relation les différents voxels à travers différents sujets (nécessaire pour les analyses statistiques).
Contrairement à la volumétrie manuelle où chaque volume à l'étude est délimité de façon à représenter la même structure d'intérêt, ni plus, ni moins, lorsque l'on fait une analyse en VBM, les unités de volume (voxels) que nous obtenons ne sont pas liées à une structure particulière.
En fonction de différentes sources de variation (par exemple: la position de la tête du sujet durant l'acquisition, la variabilité interindividuelle, etc.), un voxel *x* peut se retrouver dans des structures différentes lorsque l'on compare deux sujets.
Il faut donc que l'on procède à cette étape afin de créer une concordance des différents voxels à travers les sujets à l'étude.
L'espace stéréotaxique de référence que l'on crée ainsi est donc un système de référence sur lequel on réaligne les données de chaque sujet afin de permettre ces comparaisons.
Ainsi, on s'assure que lorsque l'on observe une coupe particulière du cerveau de différents participants, on observe aussi les mêmes structures.

> Les détails concernant l'étape du recalage seront présentés plus en détail plus loin dans le [présent chapitre](https://psy3018.github.io/morphometrie.html#recalage-d-images).

L'étape suivante correspond au **lissage spatial** (aussi appelée convolution spatiale).
Le lissage s'apparente à ajouter un filtre sur l'image la rendant plus floue.
Il est nécessaire de procéder à cette étape afin d'obtenir des valeurs de densité de matière grise pour des zones qui dépasse le voxel unique.
Comme l'objectif de notre carte de densité de matière grise est de pouvoir observer des densités locales contenant des groupes (voisinages) de voxels, il est plus facile de procéder à ce genre d'observation après l'étape du lissage spatial.
Lors de cette opération, on remplace la valeur obtenue pour le voxel *x* par une moyenne pondérée par une distribution gaussienne des valeurs de ce voxel et des voxels avoisinants.
Comme c'est une moyenne pondérée, la valeur originale du voxel est celle qui aura la plus grande pondération, mais les valeurs des voxels situés directement autour vont aussi l'affecter grandement.
Mais plus on s'éloigne du voxel d'intérêt *x*, moins les autres voxels influencent sa valeur.
Afin de savoir jusqu'à quel point on s'éloignera du voxel *x* pour calculer la valeur lissée, il nous faudra un autre paramètre: le FWHM (*full width at half maximum*).
- Il est important de ne pas confondre cette valeur avec l'écart-type.

Plus la valeur de FWHM est grande, plus grand sera le rayon du voisinage de voxels qui auront un impact sur la valeur lissée du voxel *x*.

> Les détails concernant l'étape du lissage spatial seront présentés plus en détail lors du [Chapitre 4: IRM fonctionnelle](https://psy3018.github.io/irm_fonctionnelle.html#pretraitement-des-donnees-d-irmf).

L'ultime étape de ce processus est celle des **analyses statistiques**.
C'est lors de cette étape que l'on parvient à obtenir les cartes finales avec lesquelles il est possible de procéder aux analyses et de tirer les observations et conclusions d'une étude en morphométrie.
On y superpose à l'image de l'espace stéréotaxique de référence les valeurs obtenues pour les régions où des informations d'intérêt statistique pour le protocol de recherche ont été obtenues.
C'est généralement le genre d'image qui sera par la suite utilisé lors de publications scientifiques.

## Recalage d'images
```{code-cell} ipython 3
:tags: ["hide-input"]

from IPython.display import HTML
import warnings
warnings.filterwarnings("ignore")

# Youtube
HTML('<iframe width="560" height="315" src="https://www.youtube.com/embed/VYN4K-K-Fjc" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>')
```
Le **recalage** est une étape cruciale du processus lorsque l'on utilise des approches automatisées basées sur des unités de volumes fixes à travers le cerveau (voxels) et que nous voulons pouvoir procéder à des comparaisons entre plusieurs sujets.
Cette étape peut aussi être nécessaire afin de comparer les données d'un même sujet acquises lors de différentes séances dans le scanneur.
En effet, à chaque fois que les images de la tête d'un sujet présentent des différences:
- À cause d'un possible mouvement de la tête durant une même séance
- À cause d'un positionnement qui peut différer légèrement d'une séance à l'autre
- À cause des différences inter-individuelles
- Etc.

il y aura un besoin de recalage afin de palier à ces différences et permettre les comparaisons.
Sa principale fonction est de réaligner les images du cerveau sur une même référence, permettant ainsi de pouvoir comparer les voxels à leur équivalent (voxels représentant le même lieu physique dans les cerveaux représentés par différentes images).
Différents types de recalage sont disponibles, ont des propriétés plus ou moins complexes et permettent des comparaisons entre des cerveau présentant plus ou moins de variations inter-individuelles.

### Recalage linéaire
Le **recalage linéaire** est la version la plus simple du processus de recalage, mais il est aussi la première étape de la version plus complexe du processus qu'est le recalage non-linéaire.
Cette technique est efficace pour aligner le contour du cerveau et/ou les structures de grande taille s'y trouvant.
En d'autres mots, cette technique peut être utilisé pour ajuster les grosses différences.
Le recalage linéaire est une combinaison plus ou moins complexe, selon les besoins, de trois paramètres de transformation linéaire:
- La rotation (pour corriger si la tête a pivoté)
- La translation (si la tête est décalée latéralement)
- La mise à l'échelle (afin d'ajuster la taille et/ou la forme du cerveau)

Chacun de ces paramètres peut être modifié le long des trois axes (espace tridimensionnel), ce qui nous donne un total de 9 paramètres pouvant être ajustés.
Ces paramètres seront estimés par un algorithme et nous permettront de réaligner grossièrement les images de cerveau étudiées.

### Recalage non-linéaire
Le **recalage non-linéaire** est une étape plus complexe permettant des ajustements localisés.
Il reste tout de même nécessaire de débuter par un racalage linaire avant de procéder au recalage non-linéaire.
Cette seconde étape permettra de tenir compte des différences plus fines pour lesquelles il n'était pas possible d'obtenir de correction à l'aide du recalage linéaire.
Il permettra aussi de positionner les aires cérébrales de différents sujets aux mêmes endroits sur les images afin de faciliter les analyses comparatives.
Afin de compléter ce second recalage, il est nécessaire de construire une norme (*template*) basée sur notre espace stéréotaxique de référence.
L'objectif de la norme est de construire une carte caractérisant l'ensemble des changements non-linéaires locaux qu'il est nécessaire d'effectuer afin de parvenir à la référence.
Il est important que les déformations soient continues.
Autrement dit, des endroits adjacents dans les images non-recalées doivent toujours être adjacents après le recalage.
Il est possible que la distance entre ces points soit plus ou moins grande que celle d'origine, mais il est nécessaire de conserver la continuité dans les lignes du cadrillage de la norme.

### Volumétrie
Maintenant, que l'espace stéréotaxique de référence de notre groupe de participants est généré et que l'ensemble des images individuelles ont été recalées sur cette référence, il est possible de procéder à une analyse de la volumétrie.
Que le choix se porte vers une segmentation manuelle (telle que vu précédement dans ce chapitre) ou vers l'utilisation d'un atlas, le recalage rend la segmentation de la référence généralisable aux images individuelles recalées.
On facilite ainsi grandement le processus en permettant une automatisation du processus de segmentation pour chacun des sujets.

## Contrôle de qualité
Comme pour toute opération automatisée, il reste toujours une possibilité d'erreur au cours du processus de recalage.
Il est donc nécessaire de prévoir une étape de vérification des résultats afin de s'assurer qu'il n'y a pas eu d'aberrations qui se sont introduites dans les données.
Ces aberrations peuvent venir de plusieurs sources différentes:
- Erreurs dans les étapes de recalage linéaire et/ou non-linéaire
- Présence d'artéfacts lors de l'acquisition des données (présence d'objects métalliques, etc.)
- Etc.

Cette vérification de la qualité des images permettra d'éliminer les images inutilisables avant de procéder aux analyses statistiques.
Conserver ces dernières pourrait avoir des impacts importants sur les résultats ainsi que sur les conclusions tirées, c'est pourquoi il est primordial de garder ce risque en tête lors du traitement des données.

## Analyses de surface
```{code-cell} ipython 3
:tags: ["hide-input"]

from IPython.display import HTML
import warnings
warnings.filterwarnings("ignore")

# Youtube
HTML('<iframe width="560" height="315" src="https://www.youtube.com/embed/S-8rk7PlWBI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>')
```

Cette famille d'approches diffère des précédentes en ce qu'elle ne mesure pas la densité de la matière grise, mais plutôt sa répartition, son épaisseur et/ou sa surface.
Cette façon de faire particulière permet de faire des analyses sur l'ensemble de la surface corticale.
Par contre, qui dit surface corticale, sous-entend aussi que les structures sous-corticales sont laissées de côté.
Cette famille de techniques n'est donc pas recommandée pour un protocol de recherche durant lequel on voudrait étudier des structures enfouies dans la boîte crânienne telles que l'hypothalamus, les ganglions de la base, etc.
Plutôt que de procéder à l'analyse du contenu d'unités de volume (voxels), comme c'était le cas pour la VBM, on utilisera ici l'analyse du contenu d'unités de surface: les **vertex**.
Ainsi, on cherche à étudier à l'aide de ceux-ci la forme que prend localement la matière grise.

Le processus permettant d'arriver aux résultats partage certaines similitudes avec les analyses volumétriques, alors que d'autres étapes sont spécifiques aux analyses de surface.
- En effet, la première étape consiste encore à procéder au **prétraitement** (recalage et contrôle de qualité) et à la **segmentation** des images du cerveau.
Par contre, certaines des étapes suivantes diffèrent de celles utilisées lors des protocols de volumétrie.
- De ce fait, la seconde étape vise à **délimiter la frontière** entre la surface de matière grise et les tissus/milieux environnant.
Afin d'y parvenir, on utilisera des modèles permettant d'estimer la **surface piale** (surface extérieure du cortex, à la frontière entre la matière grise et le liquide céphalo-rachidien) et la **surface intérieure** (à la frontière entre la matière grise et la matière blanche).
Il faudra, pour y parvenir, éliminer des images les structures n'appartenant pas au cortex (boîte crânienne, tissus adipeux, méninges, liquide céphalo-rachidien, etc.).
C'est l'étape de la création du **masque** du cerveau.
Il est important de s'assurer, une fois que le masque est généré, qu'il contient bel et bien l'ensemble du volume du cerveau, ni plus, ni moins.
Il y a en effet un aspect de contrôle de qualité qui doit être vérifié à ce stade afin de ne pas mettre en péril l'ensemble des étapes suivantes.
- On procédera ensuite à la **délimitation des surfaces** piale et interne.
Pour ce faire, on modélisera un volume en forme de ballon virtuel au centre de chacun des hémisphères du cerveau.
On définit ensuite des contraintes physiques (délimitation de la "cavité" interne dans laquelle le ballon peut évoluer) afin de marquer la frontière entre la matière blanche et la matière grise (surface interne).
On procède ensuite à "gonfler" ce ballon jusqu'à ce qu'il épouse le mieux possible la frontière de la surface interne (jusqu'à ce que le ballon soit gonflé jusqu'à occuper tout l'espace dans la cavité et suivre l'ensemble des courbes de la paroi).
Il est aussi possible de faire la procédure inverse.
On pourrait en effet générer un ballon virtuel autour de chacun des hémisphères et les "dégonfler" jusqu'à ce qu'ils épousent les contours des frontières délimitées par les contraintes physiques.
Lorsque l'une des frontières (surface interne ou surface piale) est délimitées, il est possible de continuer la procédure de gonflement/dégonflement afin d'obtenir la seconde surface.
On peut ensuite utiliser la distance entre les deux surfaces en un point donné afin d'évaluer l'épaisseur corticale pour ce vertex.
Cette distance est obtenue en prenant la perpendiculaire à l'une des surfaces et en mesurant la distance entre les deux surfaces le long de cette perpendiculaire.
Ce genre de technique permet par la suite de générer des cartes d'épaisseur corticale.

```{admonition} Attention
:class: caution attention
:name: controle-qualite-attention
Malheureusement, ce genre de technique est coûteuse en terme de ressources de calcul et des erreurs peuvent survenir à plusieurs niveaux.
Par exemple, cette technique est particulièrement peu robuste face aux effets des volumes partiels.
On pourrait en effet avoir une surface qui ne se rend pas jusqu'au fond d'un sulcus, ou lorsque les giri sont très rapprochés, qui ne rentre même pas à l'intérieur du sulcus.
Le résultat de ces deux types d'erreurs, qui sont possibles autant sur la surface piale que sur la surface interne, sera une forte surestimation localisée de l'épaisseur corticale.
C'est pourquoi il est souhaitable de procéder à des contrôles de qualité fréquemment.
```

- La dernière étape des analyses de surfaces marque un retour aux similitudes avec les techniques de volumétrie: c'est l'étape des **analyses statistiques**.

## Conclusion
Ce chapitre vous a introduit aux différentes familles de techniques de segmentation qu'il est possible d'utiliser avec des données acquises en imagerie par résonance magnétique anatomique.
Il a en effet été question de **volumétrie manuelle**, d'**approche par voxel** (***voxel-based morphometry*** ou **VBM**) et d'**analyse de surface**.
Les processus de **recalage** et l'importance du **contrôle de qualité** ont aussi été abordés.
Lors du prochain chapitre, il sera question des principes de l'IRM fonctionnelle.


## Références

```{bibliography}
:filter: docname in docnames
```

## Exercices

```{admonition} Exercice 3.1
:class: note

Choisissez la meilleure réponse et expliquez pourquoi.
Des données d’IRM T1 individuelle sont...
 1. Une image 3D d’un cerveau,
 2. Des dizaines d’images 2D sagittales d’un cerveau,
 3. Des centaines d’images 2D axiales, coronales et sagittales d’un cerveau,
 4. Toutes les réponses 1-3.
```

```{admonition} Exercice 3.2
:class: note

On souhaite comparer le volume moyen du putamen droit entre des participants neurotypiques et des participants étant sur le spectre de l’autisme. On considère pour cela deux méthodes alternatives: la volumétrie manuelle et l'analyse VBM.
Citez une force et une faiblesse en lien avec les objectifs de l'étude pour chacune de ces techniques.
```

```{admonition} Exercice 3.3
:class: note

Pour chacun des énoncés suivants, spécifiez si l'affirmation est vraie ou fausse et expliquez votre choix.
 - Les données IRM T1 doivent être réalignées pour étudier la morphologie du cerveau à l’échelle d’une population.
 - Les données d’IRM "brutes" (avant prétraitement) sont inutilisables pour étudier la morphométrie.
 - Le lissage spatial est important en VBM, même pour une analyse individuelle.
```

```{admonition} Exercice 3.4
:class: note

Pour chacun des énoncés suivants, spécifiez si l'affirmation est vraie ou fausse et expliquez votre choix.
 - Les mouvements d’un participant de recherche peuvent créer du bruit dans une carte VBM.
 - La présence de métal peut créer du bruit et des déformations dans une carte VBM.
 - Un trou dans une carte cérébrale VBM signifie qu'il y a un trou dans le cerveau du participant.
```

```{admonition} Exercice 3.5
:class: note

En vérifiant ses données structurelles, une chercheuse réalise qu’un de ses participants de recherche a un volume cérébral de deux fois supérieur à la normale! Pourtant, le crâne de ce participant semblait normal. Proposez une explication.
```

```{admonition} Exercice 3.6
:class: note

On souhaite faire une comparaison entre la quantité de matière grise présente au niveau du sillon post-central et celle contenue dans le sillon précentral, en moyenne, sur une population.
On considère pour cela deux méthodes alternatives: une analyse VBM ou bien une analyse de l’épaisseur corticale (analyse de surface).
Quelle technique choisiriez-vous et pourquoi?
```

```{admonition} Exercice 3.7
:class: note

Les données d’un participant de recherche sont de qualité médiocre et la segmentation de la matière grise est imprécise.
Pour chacune des combinaisons de choix suivantes, quelle technique choisiriez-vous et pourquoi?
 - VBM vs volumétrie manuelle?
 - VBM vs analyse de surface?
```

```{admonition} Exercice 3.8
:class: note
On a vu quelques exemples exemples de structures anatomiques cérébrales en cours. Faisons un peu de révisions... Vous trouverez un visualisateur interactif de cerveau ci dessous, si vous êtes sur la [page web du cours](morphometrie.html#exercices). Donnez les coordonnées (`x`, `y` ou `z`) où l'on voit...
* une coupe sagitale avec le corps calleux.
* une coupe corononale avec le corps calleux.
* une coupe axiale avec des ventricules.
* une coupe axiale avec le sillon central.
```

```{code-cell} ipython 3
:tags: ["hide-input"]
# Ce code récupère des données IRM T1
# et génère une image dans trois plans de coupes

# Enlève les warnings
import warnings
warnings.filterwarnings("ignore")

# Télécharge un scan anatomique (template MNI152)
from nilearn.datasets import fetch_icbm152_2009
mni = fetch_icbm152_2009()

# Visualise le volume cérébral
from nilearn.plotting import view_img

view_img(
    mni.t1,
    bg_img=None,
    black_bg=True,
    cut_coords=[-17, 0, 17],
    title='IRM pondérée en T1',
    cmap = 'gray',
    symmetric_cmap=False
)
```

```{admonition} Exercice 3.9
:class: note
Pour répondre à cette question, lisez l'article de Mensen et collaborateurs, "_Development of cortical thickness and surface area in autism spectrum disorder_", publié dans Neuroimage Clinical (2017, 13: 215-22) et disponible en libre accès à cette [adresse](https://www.sciencedirect.com/science/article/pii/S2213158216302406). Les questions suivantes sont à développement court.
* Quel(s) type(s) de participant a(ont) été recruté(s) dans cette étude?
* Quel est l'objectif principal de l'étude?
* Quels sont les critères d'inclusion et d'exclusion?
* Quelle technique de neuroimagerie est utilisée? S'agit-il d'une technique structurelle ou fonctionnelle?
* Quelle séquence d'image est utilisée? Listez les paramètres.
* Est ce qu'une étape de recalage est appliquée? laquelle?
* Y-a-t-il une procédure de contrôle qualité? Résumez cette procédure.
* Comment les régions d'intérêt sont-elles définies? avec quel atlas? Combien y-a-t-il de régions?
* Quelles mesures morphologiques sont utilisées pour chaque région?
* Quelle figure (ou tableau) répond à l'objectif principal de l'étude?
```
