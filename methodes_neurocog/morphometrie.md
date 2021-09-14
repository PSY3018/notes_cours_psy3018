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
        <a title="Révision du texte">👀</a>
    </td>
    <td align="center">
      <a href="https://github.com/pbellec">
        <img src="https://avatars.githubusercontent.com/u/1670887?v=4?s=100" width="100px;" alt=""/>
        <br /><sub><b>Pierre bellec</b></sub>
      </a>
      <br />
        <a title="Contenu">🤔</a>
        <a title="Quizz">⚠️</a>
        <a title="Révision du texte">👀</a>
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

## Volumétrie

### Segmentation manuelle

```{figure} ./morphometrie/ashempour2019.jpg
---
width: 600px
name: ashempour2019-fig
---
Cette figure illustre un protocole de segmentation manuelle de l'amygdale. Vue coronale d'une segmentation manuelle de l'amygdale gauche (jaune) et droit (bleu) avant (gauche) et après (droite) corrections dans le plan coronal. Figure tirée de {cite:p}`Hashempour2019-jq`, sous licence CC-BY.
```

La **volumétrie manuelle** constiste à manuellement délimiter une aire cérébrale particulière, comme l'hippocampe ou l'amygdale (voir {numref}`ashempour2019-fig`). Cette approche nécessite du temps, car chaque coupe d'IRM doit être délimitée manuellement. On commencera d'abord par identifier ce contour sur chaque coupe où la structure est présente dans un premier plan (par exemple, sur une coupe axiale), puis il faudra aller corriger cette délimitation sur chaque coupe prise dans un second plan (comme une coupe sagitale) et finalement, répéter de nouveau cette correction sur le troisième plan (une coupe coronale).

> Pour un rappel concernant les différents types de coupes du cerveau, veuillez vous référer au {ref}`Chapitre 1: Cartes cérébrales <coupes-tip>`.

Ce type d'approche requiert également un protocole de segmentation rigoureux, avec des critères anatomiques claires pour décider où une région cérébrale se trouve. Pour certaines structures, comme les ventricules latéraux, c'est assez clair. Pour d'autres structures, comme l'hippocampe, il existe des protocoles détaillés, voir par exemple {cite:p}`Wisse2017-ff`. Enfin pour d'autres régions, comme les aires visuelles V1, V2, etc, il est nécessaire de réaliser des expériences fonctionnelles pour les délimiter, et les délimitations anatomiques ne sont pas nécessairement disponibles ou bien établies.

Un protocole de segmentation clair est nécessaire pour assurer un bon niveau de reproductibilité des résultats et un [accord inter-juge](https://en.wikipedia.org/wiki/Inter-rater_reliability) acceptable. Certains protocoles offrent une certification, qui offre un certain niveau de garantie que la chercheuse effectuant la segmentation applique le protocole correctement.

### Segmentation automatique

```{code-cell} ipython 3
:tags: ["hide-input", "remove-output"]

# Téléchargement de l'atlas Harvard-Oxford
from nilearn import datasets

# Enlève les warnings
import warnings
warnings.filterwarnings("ignore")

atlas = datasets.fetch_atlas_harvard_oxford('cort-maxprob-thr25-2mm').maps
mni = datasets.fetch_icbm152_2009()

# Visualisation de la figure
import matplotlib.pyplot as plt
from myst_nb import glue
from nilearn import plotting

fig = plt.figure(figsize=(12, 4))
plotting.plot_roi(atlas,
    bg_img=mni.t1,
    axes=fig.gca(),
    title="Harvard Oxford atlas",
    cut_coords=(8, -4, 9),
    colorbar=True,
    cmap='Paired')

glue("harvard-oxford-fig", fig, display=False)
```

```{glue:figure} harvard-oxford-fig
:figwidth: 800px
:name: "harvard-oxford-fig"

Un exemple d'atlas de régions anatomiques: l'atlas Harvard-Oxford. Cette figure est générée par du code python à l'aide de la librairie [nilearn](https://nilearn.github.io/) à partir d'un jeu de données public appelé fetch_atlas_harvard_oxford ([Nilearn, section 9.2.1: Basic Atlas plotting](https://nilearn.github.io/auto_examples/01_plotting/plot_atlas.html)) {cite:p}`MAKRIS2006155, Frazier2005, DESIKAN2006968, GOLDSTEIN2007935` (cliquer sur + pour voir le code).
```

Afin d'automatiser le travail de segmentation, il est possible d'utiliser une segmentation déjà effectuée par une équipe de chercheurs dans un espace de référence, encore appelé espace stéréotaxique. On appelle ces segmentations de référence des atlas, ou parfois parcellisations. Comme il existe une variété de parcellisations correspondant à différents critères anatomiques ou fonctionnels, il est important de choisir adéquatement l'atlas en fonction des structures particulières que vous voulez étudier. L'IRM structurelle d'un participant est {ref}`recalée <registration-tip>` de manière automatisée vers l'{ref}`espace stéréotaxique <stereotaxique-tip>` de référence, et cette transformation permet d'adapter l'atlas à l'anatomie du sujet.

```{admonition} Recalage
:class: tip
:name: registration-tip

Afin d'appliquer un atlas de régions cérébrales sur une IRM individuelle, il est nécessaire de recaler cette IRM sur l'espace stéréotaxique qui a été utilisé pour établir les régions. Ce processus mathématique va chercher à déformer l'image individuelle vers l'espace stéréotaxique. Cette transformation peut être affine (notamment translation, rotation, mise à l'échelle) ou bien non-linéaire (déplacement dans n'importe quelle direction de l'espace). L'objectif du recalage est de rendre les images plus similaires possible, mais il est important que les déformations soient continues. Autrement dit, des endroits adjacents dans les images non-recalées doivent toujours être adjacents après le recalage. Les images ci dessous illustrent l'effet de différents types de recalage, et sont tirées de la documentation du logiciel [slicer](https://www.slicer.org/wiki/Documentation:Nightly:Registration:RegistrationLibrary:RegLib_C42) sous licence CC-Attributions Share Alike.
```{figure} morphometrie/registration_slicer_raw.gif
:figwidth: 400px
:align: left
:figclass: margin-caption
Images brutes: deux scans du même sujet, prises à des jours différents.

```{figure} morphometrie/registration_slicer_affine.gif
:figwidth: 400px
:align: left
:figclass: margin-caption
Images recalées après une transformation affine.

```{figure} morphometrie/registration_slicer_nonlinear.gif
:figwidth: 400px
:align: left
:figclass: margin-caption
Images recalées après une transformation non-linéaire.

```{figure} morphometrie/registration_slicer_nonlinear_only.gif
:figwidth: 400px
:align: left
:figclass: margin-caption
Effet du recalage non-linéaire seulement
```

```{code-cell} ipython 3
:tags: ["hide-input", "remove-output"]
# Ce code récupère des données IRM T1
# et génère une image dans trois plans de coupes

# Enlève les warnings
import warnings
warnings.filterwarnings("ignore")

# Télécharge un scan anatomique (template MNI152)
from nilearn.datasets import fetch_icbm152_2009
mni = fetch_icbm152_2009()

# Visualise le volume cérébral
import matplotlib.pyplot as plt
from myst_nb import glue
from nilearn.plotting import plot_anat

fig = plt.figure(figsize=(12, 4))
plot_anat(
  mni.t1,
  axes=fig.gca(),
  cut_coords=[-17, 0, 17],
  title='Espace stereotaxique MNI152'
)
glue("mni-template-fig", fig, display=False)
```

```{admonition} Espace stéréotaxique
:class: tip
:name: stereotaxique-tip

Afin de définir une anatomie de référence, les chercheurs utilisent généralement un cerveau "moyen". Le cerveau de plusieurs dizaines d'individus sont recalées les uns avec les autres, puis moyennés pour obtenir une seule image. Si le recalage a bien fonctionné, les détails de la neuroanatomie sont préservés dans la moyenne.
```{glue:figure} mni-template-fig
:figwidth: 600px
:align: left
Espace stéréotaxique de l'Institut Neurologique de Montréal (MNI), moyenne de 152 sujets après recalage non-linéaire itératif {cite:p}`Fonov2011-xr`.

```
### Analyses statistiques
```{figure} ./morphometrie/ledig2018_stats.png
---
width: 400px
name: ledig2018-stats-fig
---
Cette figure illustre les différences de volume de l'hippocampe entre participants cognitivement sains (HC), participants avec troubles cognitifs légers stables (sMCI) ou progressifs (pMCI), ou patients avec une démence de type Alzheimer (AD), dans la cohorte ADNI. Plus les symptômes cliniques sont sévères, plus la probabilité de présenter la maladie d'Alzheimer est grand, et plus le stade de la maladie est sévère. L'atrophie de l'hippocampe est claire chez les patients présentant les symptômes les plus sévères. Figure tirée de {cite:p}`Ledig2018-ai`, sous licence CC-BY.
```
Pour les analyses statistiques, on extrait le volume de chaque structure segmentée (en $mm^3$), et on peut par exemple comparer statistiquement le volume moyen entre deux groupes, ou tester l'association du volume avec une variable comme l'âge. Par exemple, dans la {numref}`ledig2018-stats-fig`, on compare le volume de l'hippocampe entre différents groupes cliniques avec différents risques de la maladie d'Alzheimer.

## Morphométrie basée voxel (VBM)

### Densité de matière grise
La morphométrie basée voxel (VBM) a pour objectif de mesurer le volume de matière grise immédiatement autour d'un voxel donné. Cette approche n'est donc pas limitée par le besoin d'avoir des frontières préétablies claires entre différentes structures cérébrales.
Lorsque l'on génère une mesure de volume pour l'ensemble des voxels du cerveau, on obtient une carte 3D de la densité de matière grise.
L'avantage premier de cette approche est d'être automatisée et systématique. La présence d'une personne ne devient nécessaire que pour vérifier que la procédure a fonctionné correctement, une étape appelée contrôle qualité (ou QC, pour "quality control"). On va aussi tester la morphologie du cerveau au travers de l'ensemble de la matière grise. Mais le grand nombre de mesures différentes générées pose un  problème de _comparaisons multiples_ lorsque vient le temps de faire les analyses statistiques, voir le [Chapitre 10: Cartes statistiques](cartes_statistiques.html).

### Segmentation
```{code-cell} ipython 3
:tags: ["hide-input", "remove-output"]
# Importe les librairies nécessaires
import matplotlib.pyplot as plt
import numpy as np
from myst_nb import glue
import seaborn as sns

import warnings
warnings.filterwarnings("ignore")

# Télécharge un scan anatomique (template MNI152)
from nilearn import datasets
mni = datasets.fetch_icbm152_2009()

# Initialise la figure
fig = plt.figure(figsize=(15, 15))

from nilearn.plotting import plot_stat_map
from nilearn.image import math_img
from nilearn.input_data import NiftiMasker

thresh = 0.8
coords = [-5, 5, -25]

# Full brain
ax_plot = plt.subplot2grid((4, 3), (0, 0), colspan=1)
mask_brain = math_img(f"img>{thresh}", img=mni.mask)
val_brain = NiftiMasker(mask_img=mask_brain).fit_transform(mni.t1)
ax = sns.distplot(val_brain, norm_hist=False)
ax.set_xlim(left=0, right=100)
ax_plot = plt.subplot2grid((4, 3), (0, 1), colspan=2)
plot_stat_map(mni.mask,
              bg_img=mni.t1,
              cut_coords=coords,
              axes=ax_plot,
              black_bg=True,
              title='cerveau'
              )

# Gray matter
ax_plot = plt.subplot2grid((4, 3), (1, 0), colspan=1)
mask_gm = math_img(f"img>{thresh}", img=mni.gm)
val_gm = NiftiMasker(mask_img=mask_gm).fit_transform(mni.t1)
ax = sns.distplot(val_gm, norm_hist=False)
ax.set_xlim(left=0, right=100)
ax_plot = plt.subplot2grid((4, 3), (1, 1), colspan=2)
plot_stat_map(mni.gm,
              bg_img=mni.t1,
              cut_coords=coords,
              axes=ax_plot,
              black_bg=True,
              title='matière grise'
              )

# White matter
ax_plot = plt.subplot2grid((4, 3), (2, 0), colspan=1)
mask_wm = math_img(f"img>{thresh}", img=mni.wm)
val_wm = NiftiMasker(mask_img=mask_wm).fit_transform(mni.t1)
ax = sns.distplot(val_wm, norm_hist=False)
ax.set_xlim(left=0, right=100)
ax_plot = plt.subplot2grid((4, 3), (2, 1), colspan=2)
plot_stat_map(mni.wm,
              bg_img=mni.t1,
              cut_coords=coords,
              axes=ax_plot,
              black_bg=True,
              title='matière blanche'
              )

# CSF
ax_plot = plt.subplot2grid((4, 3), (3, 0), colspan=1)
mask_csf = math_img(f"img>{thresh}", img=mni.csf)
val_csf = NiftiMasker(mask_img=mask_csf).fit_transform(mni.t1)
ax = sns.distplot(val_csf, axlabel="intensité de l'image", norm_hist=False)
ax.set_xlim(left=0, right=100)
ax_plot = plt.subplot2grid((4, 3), (3, 1), colspan=2)
plot_stat_map(mni.csf,
              bg_img=mni.t1,
              cut_coords=coords,
              axes=ax_plot,
              black_bg=True,
              title='liquide cephalo rachidien'
              )

from myst_nb import glue
glue("mni-segmentation-fig", fig, display=False)
```
```{glue:figure} mni-segmentation-fig
:figwidth: 600px
:name: mni-segmentation-fig
Segmentation probabiliste des principaux types de tissus, et distribution des valeurs pondérées en T1 dans les voxels "purs" (probabilité supérieure à 80% pour un type de tissu donné). L'image pondérée en T1 ainsi que les segmentations correspondent à l'espace stéréotaxique MNI152 {cite:p}`Fonov2011-xr`.
```
Une étape importante de la VBM est la segmentation. Cette analyse vise à catégoriser les différents tissus du cerveau en classes, notamment matière grise, matière blanche et liquide céphalo-rachidien. On va aussi généralement extraire un masque du cerveau et exclure les méninges ainsi que le crâne. On va généralement inclure d'autres types de tissus également, comme la graisse. Un algorithme de segmentation va examiner la distribution des niveaux de gris dans l'image, par exemple pondérée en T1, et estimer pour chaque voxel la proportion du voxel qui contient un tissu donné. Cette proportion est souvent appelé {ref}`effet de volume partiel <volume-partiel-tip>`. Un voxel peut par exemple être assigné à 80% de matière grise et 20% de liquide céphalo-rachidien.

```{admonition} Effets de volume partiel
:class: tip
:name: volume-partiel-tip
Il est possible que la segmentation automatique nous retourne certains tissus non-désirés, mais dont les valeurs dont les valeurs dans l'image sont similaires à celle de la matière grise. Il est ainsi possible que des voxels se trouvant directement sur la jonction entre une zone blanche et une zone noire (par exemple, sur une paroi de matière blanche qui borderait un ventricule) aient comme valeur résultante une valeur s'apparentant plutôt au gris associé à la matière grise (valeur moyenne entre blanc et noir). On appelle ce genre d'effet de mélange de noir et de blanc les volumes partiels (une partie du volume du voxel est blanche alors que l'autre partie est noire).
```

```{admonition} Erreurs de segmentation
:class: tip
Il est possible de perdre certaines structures pour lequelles le contraste entre la matière blanche et matière grise n'est pas assez important pour que l'algorithme réussisse à les classifier efficacement. Pour ce genre de structure, il est important d'ajouter des a priori (des règles, ou conditions supplémentaires) afin de ne pas les perdre. Il est aussi envisageable de corriger cette partie de la segmentation de façon manuelle.

```{figure} ./morphometrie/segmentation-error-volume-fig.png
---
width: 600px
name: segmentation-error-volume-fig
---
Image de gauche: IRM individuelle pondérée en T1. Image de droite: classification matière grise et matière blanche générée par le logiciel [ANTS](http://stnava.github.io/ANTs/). Notez comment la matière blanche proche du gyrus est classifié de manière erronnée comme matière grise. Image sous licence CC Attribution, tirée de Klein et al., 2017 {cite:p}`Klein2017-zh`.
```

### Lissage
```{code-cell} ipython 3
:tags: ["hide-input", "remove-output"]
# Improte les librairies nécessaires
import matplotlib.pyplot as plt
import numpy as np
from myst_nb import glue
import seaborn as sns

import warnings
warnings.filterwarnings("ignore")

# Télécharge un scan anatomique (template MNI152)
from nilearn import datasets
mni = datasets.fetch_icbm152_2009()

# Initialise la figure
fig = plt.figure(figsize=(15, 15))

from nilearn.plotting import plot_anat
from nilearn.image import math_img
from nilearn.input_data import NiftiMasker
from nilearn.image import smooth_img

list_fwhm = (0, 5, 8, 10)
n_fwhm = len(list_fwhm)
coords = [-5, 5, -25]

for num, fwhm in enumerate(list_fwhm):
    ax_plot = plt.subplot2grid((n_fwhm, 1), (num, 0), colspan=1)
    vol = smooth_img(mni.gm, fwhm)
    plot_anat(vol,
              cut_coords=coords,
              axes=ax_plot,
              black_bg=True,
              title=f'FWHM={fwhm}',
              vmax=1)

from myst_nb import glue
glue("smoothing-fig", fig, display=False)
```
```{glue:figure} smoothing-fig
:figwidth: 600px
:name: smoothing-fig
Illustration de l'impact du lissage sur une carte de densité de matière grise en VBM. Lorsque le paramère `FWHM` augmente, la mesure de densité représente une région entourant le voxel de plus en plus grande. Cette figure est générée par du code python à l'aide de la librairie [nilearn](https://nilearn.github.io/) à partir d'un jeu de données public appelé template MNI152 2009 {cite:p}`Fonov2011-xr` (cliquer sur + pour voir le code).
```

L'étape suivante correspond au lissage spatial, qui consiste à ajouter un filtre sur l'image qui va la rendre plus  floue. En pratique, le lissage remplace la valeur à chaque voxel par une moyenne pondérée de ses voisins.
Comme c'est une moyenne pondérée, la valeur originale du voxel est celle qui aura la plus grande pondération, mais les valeurs des voxels situés directement autour vont aussi l'affecter grandement. La valeur des poids suit un profil de distribution Gaussienne 3D. Il est nécessaire de procéder à cette étape afin d'obtenir des valeurs de densité de matière grise pour des zones qui dépasse le voxel unique, et analogue du volume d'une petite région, centrée sur le voxel. La taille de la région est contrôlée par un paramètre de _largeur à mi-hauteur_, ou `FWHM` (_full width at half maximum_), qui se mesure en millimètres. Plus la valeur de FWHM est grande, plus grand sera le rayon du voisinage de voxels qui auront un impact sur la valeur lissée du voxel, voir {numref}`smoothing-fig`.

### Analyses statistiques
```{code-cell} ipython 3
:tags: ["hide-input", "remove-output"]
import numpy as np
import matplotlib.pyplot as plt
from nilearn import datasets
from nilearn.input_data import NiftiMasker
from nilearn.image import get_data

n_subjects = 50  # more subjects requires more memory

# Charge les données
oasis_dataset = datasets.fetch_oasis_vbm(n_subjects=n_subjects)
gray_matter_map_filenames = oasis_dataset.gray_matter_maps
age = oasis_dataset.ext_vars['age'].astype(float)

# Prétraitement (mask)
nifti_masker = NiftiMasker(
    standardize=False,
    smoothing_fwhm=2,
    memory='nilearn_cache')  # cache options

# Normalise les données
gm_maps_masked = nifti_masker.fit_transform(gray_matter_map_filenames)

from sklearn.feature_selection import VarianceThreshold
variance_threshold = VarianceThreshold(threshold=.01)

gm_maps_thresholded = variance_threshold.fit_transform(gm_maps_masked)
gm_maps_masked = variance_threshold.inverse_transform(gm_maps_thresholded)
data = variance_threshold.fit_transform(gm_maps_masked)

# Modèle de régression massivement univarié
from nilearn.mass_univariate import permuted_ols
neg_log_pvals, t_scores_original_data, _ = permuted_ols(
    age, data,  # + intercept as a covariate by default
    n_perm=2000,  # 1,000 in the interest of time; 10000 would be better
    verbose=1, # display progress bar
    n_jobs=1)  # can be changed to use more CPUs
signed_neg_log_pvals = neg_log_pvals * np.sign(t_scores_original_data)
signed_neg_log_pvals_unmasked = nifti_masker.inverse_transform(
    variance_threshold.inverse_transform(signed_neg_log_pvals))

# Visualise les résultats
threshold = -np.log10(0.1)  # 10% corrected

fig = plt.figure(figsize=(10, 3), facecolor='k')
bg_filename = gray_matter_map_filenames[0]
cut_coords = [0, 0, 0]
display = plot_stat_map(signed_neg_log_pvals_unmasked, bg_img=bg_filename,
                        threshold=threshold, cmap=plt.cm.RdBu_r,
                        cut_coords=cut_coords,
                        figure=fig)
title = ('Negative $\\log_{10}$ p-values'
         '\n(Non-parametric + max-type correction)')
display.title(title, y=1.2)
plt.show()

from myst_nb import glue
glue("vbm-fig", fig, display=False)
```
```{glue:figure} vbm-fig
:figwidth: 600px
:name: vbm-fig
Régression linéaire en VBM. On teste ici l'effet de l'âge sur un groupe (N=50) de participants de la base de données OASIS. La significativité $-\log_{10}(p)$ de l'effet de l'âge est présentée superposé à une image de densité de matière grise. Cette figure est adapté d'un tutoriel [Nilearn](https://nilearn.github.io/auto_examples/02_decoding/plot_oasis_vbm.html#sphx-glr-auto-examples-02-decoding-plot-oasis-vbm-py).
```
Afin de pouvoir comparer les valeurs de densité de matière grise entre les sujets, on utilise la même procédure de {ref}`recalage <registration-tip>` non-linéaire que pour la volumétrie automatique. Contrairement à la volumétrie manuelle où chaque volume à l'étude est délimité de façon à représenter la même structure d'intérêt, le recalage utilisé en VBM n'est pas lié à une structure particulière. Une fois les cartes de densité recalées dans l'espace stéréotaxique de référence, on peut faire des tests statistiques à chaque voxel. Dans l'exemple ci-dessus, on teste l'effet de l'âge sur la matière grise. C'est généralement le genre d'image qui sera par la suite utilisé lors de publications scientifiques.

> Les détails concernant les modèles stastistiques seront présentés dans le chapitre sur la [régression linéaire](regression.html).

## Analyses de surface

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

- La dernière étape des analyses de surfaces marque un retour aux similitudes avec les techniques de volumétrie: c'est l'étape des **analyses statistiques**.

## Chaine de traitements
Comme pour toute opération automatisée, il reste toujours une possibilité d'erreur au cours du processus de recalage.
Il est donc nécessaire de prévoir une étape de vérification des résultats afin de s'assurer qu'il n'y a pas eu d'aberrations qui se sont introduites dans les données.
Ces aberrations peuvent venir de plusieurs sources différentes:
- Erreurs dans les étapes de recalage linéaire et/ou non-linéaire
- Présence d'artéfacts lors de l'acquisition des données (présence d'objects métalliques, etc.)
- Etc.

Cette vérification de la qualité des images permettra d'éliminer les images inutilisables avant de procéder aux analyses statistiques.
Conserver ces dernières pourrait avoir des impacts importants sur les résultats ainsi que sur les conclusions tirées, c'est pourquoi il est primordial de garder ce risque en tête lors du traitement des données.

```{admonition} Attention
:class: caution attention
:name: controle-qualite-attention
Malheureusement, ce genre de technique est coûteuse en terme de ressources de calcul et des erreurs peuvent survenir à plusieurs niveaux.
Par exemple, cette technique est particulièrement peu robuste face aux effets des volumes partiels.
On pourrait en effet avoir une surface qui ne se rend pas jusqu'au fond d'un sulcus, ou lorsque les giri sont très rapprochés, qui ne rentre même pas à l'intérieur du sulcus.
Le résultat de ces deux types d'erreurs, qui sont possibles autant sur la surface piale que sur la surface interne, sera une forte surestimation localisée de l'épaisseur corticale.
C'est pourquoi il est souhaitable de procéder à des contrôles de qualité fréquemment.
```

## Conclusion
Ce chapitre vous a introduit aux différentes familles de techniques de morphologie computationnelle qu'il est possible d'utiliser avec des données acquises en imagerie par résonance magnétique anatomique. On a discuté de plusieurs techniques clés d'analyse d'image, et l'on a parlé de modèles statistiques.


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

Pour un rappel concernant les différents types de coupes du cerveau, veuillez vous référer au {ref}`Chapitre 1: Cartes cérébrales <coupes-tip>`.
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
