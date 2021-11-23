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
(reproductibilite-controverses-chapitre)=
# Reproductibilité et controverses
<table>
  <tr>
    <td align="center">
      <a href="https://github.com/elisabethloranger">
        <img src="https://avatars.githubusercontent.com/u/90270981?v=4?s=100" width="100px;" alt=""/>
        <br /><sub><b>Élisabeth Loranger</b></sub>
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

Durant ce cours, on a passé en revue diverses techniques de neuroimagerie qui ouvrent une fenêtre fascinante sur la structure et la fonction du cerveau. Mais ces techniques sont régulièrement impliquées dans des articles scientifiques qui semblent peu crédibles. Dans ce cours nous allons discuter des controverses autour de la neuroimagerie, et plus généralement de la crise de reproducibilité en sciences.

Les objectifs de ce cours sont les suivants :
- Comprendre la crise de reproductibilité en sciences.
- Comprendre certaines pratiques scientifiques douteuses qui participent
au manque de reproductibilité en neurosciences cognitives.
- Connaître certains outils qui peuvent améliorer la reproductibilité en neurosciences cognitives.

## La crise de reproductibilité

### Une crise? Quelle crise?
```{figure} ./reproductibilite/significant.png
---
width: 600px
name: significant-fig
---
Cette figure illustre le processus qui amène à un résultat scientifique controversé (et le problème de comparaisons multiples). Cette figure est tirée de [xkcd webcomic](https://xkcd.com/882/), sous licence [CC-BY-NC 2.5](https://creativecommons.org/licenses/by-nc/2.5/).
```
En 2016, un sondage auprès de 1576 chercheurs a été mené dans le but de voir si, dans la
perception des professionnels dans la recherche, il y a une crise de
reproductibilité et si oui, laquelle ([Baker, 2016](https://doi.org/10.1038/533452a)). En tout, 90% des chercheurs dans ce sondage pensent
qu’il y a effectivement une crise de reproductibilité (52% pour une crise significative et 38% pour une crise modérée).

La reproductibilité, c’est quoi ? Si on avait accès
aux données derrière ce papier, est-ce qu’on serait capable de refaire les
analyses et arriver aux mêmes conclusions ? Un autre concept proche est la réplication: en recrutant de nouveaux sujets et en faisant exactement ce que les autres chercheurs ont fait au niveau des outils utilisés et des analyses effectuées, est-ce qu'on va trouver les mêmes résultats ? Dans le sondage, 70% des personnes sondées rapportent avoir échoué à reproduire les résultats d'une autre équipe de recherche, et plus de 50% rapportent avoir échoué à reproduire leurs propres résultats.

Les personnes sondées ont aussi évalué les causes probables de cette crise de
reproductibilité. Parmi les raisons les plus fréquemment mentionnées,
on retrouve la _pression à publier_ et la _publication sélective_ (les gens publient
seulement ce qui fonctionne bien) ainsi que la _puissance statistique limitée_.
Ce chapitre va expliquer certaines de ces notions plus en détails, en démarrant par formaliser le processus de génération de connaissances scientifiques.

### La méthode scientifique
```{figure} ./reproductibilite/researchcycle_original.png
---
width: 800px
name: researchcycle-original-fig
---
Cette figure illustre le cycle des découvertes scientifiques, selon l'approche de la méthode scientifique décrite par [Karl Popper](https://fr.wikipedia.org/wiki/Karl_Popper#Philosophie_des_sciences). Figure adaptée d'un travail original par [scriberia](https://info.scriberia.com/contact-us) dans le cadre du livre [The Turing way](https://the-turing-way.netlify.app) sous licence [CC-BY 4.0](https://creativecommons.org/licenses/by/4.0/), DOI: [10.5281/zenodo.3332807](https://doi.org/10.5281/zenodo.3332807). La figure adaptée par P. Bellec est elle-même disponible sous licence [CC-BY 4.0](https://creativecommons.org/licenses/by/4.0/).
```
La figure {numref}`researchcycle-original` présente une version simplifiée de la méthode scientifique pour la découverte de connaissances, inspirée par la théorie de [Karl Popper](https://fr.wikipedia.org/wiki/Karl_Popper#Philosophie_des_sciences), telle qu'elle est généralement implémentée dans la communauté de recherche.
 * On commence avec les publications, qui représentent les connaissances qui ont été accumulées par d’autres.
 * En lisant cette litérature, les chercheuses/chercheurs peuvent apprendre ce qui a déjà été découvert, et faire des hypothèses sur des choses qu’on ne connait pas encore.
 * Les chercheuses/chercheurs vont alors formuler un devis de recherche : nombre de participants, groupes, tests statistiques, etc. Elles/ils vont aussi faire des prédicitions concernant les résultats qu’elles/ils pensent obtenir.
 * Une fois le devis de recherche élaboré, il est temps de recueillir les données.
 * Ensuite, on analyse les données en suivant le protocole qui avait été établi dans le devis de recherche.
 * Il faut alors interpréter les résultats, et notamment les comparer à nos prédictions pour valider ou invalider nos hypothèses.
 * Les résultats de la recherche sont alors publiés pour permettre au reste de la communauté de recherche de continuer à formuler de nouvelles hypothèses.

 Comme on utilise des statistiques rigoureuses dans cette approche, on ne génère qu'une quantité limité de faux positifs, et donc on fait des découvertes scientifiques sans faire trop d'erreurs. En pratique, cette approche peut être adaptée de nombreuses manières avec _des pratiques de recherche douteuses_ qui vont compromettre l'intégrité et la rigueur des conclusions de l'étude.

### La méthode scientifique: hacked
```{figure} ./reproductibilite/researchcycle_hacked.png
---
width: 800px
name: researchcycle-hacked-fig
---
Cette figure illustre les pratiques douteuses qui peuvent affecter négativement l'intégrité du cycle des découvertes scientifiques. Figure adaptée d'un travail original par [scriberia](https://info.scriberia.com/contact-us) dans le cadre du livre [The Turing way](https://the-turing-way.netlify.app) sous licence [CC-BY 4.0](https://creativecommons.org/licenses/by/4.0/), DOI: [10.5281/zenodo.3332807](https://doi.org/10.5281/zenodo.3332807). La figure intègre aussi une image [shutterstock](https://www.shutterstock.com/image-vector/computer-hacker-laptop-icon-787273936), utilisée sous licence shutterstock standard.
```
#### Biais de publication
La publication sélective est un de problèmes les plus importants identifiés dans le sondage vu plus tôt. Cela signifie que les résultats d'une étude ne sont publiés que lorsqu’ils ne sont positifs, c'est à dire uniquement s'ils confirment les hypothèses de l'équipe de recherche. Si ce type de pratique est systématique dans une communauté de recherche, il se peut que plusieurs groupes rapporte un résultat, qui semble alors  robuste, alors qu'en fait un nombre plus important de groupes de recherche n'ont pas pu répliquer cet effet, mais sans publier. Cela vient déformer complètement les connaissances accumulées par la communauté scientifique, qui est à l'origine des hypothèses des études futures.

##### p-hacking
Si on voit que nos résultats ne correspondent pas à nos attentes, on pourrait se demander si on n'a pas commis une erreur ou peut être qu'on n'a pas choisi la technique d'analyse la plus optimale. On va alors revisiter la manière
dont analyse les données jusqu'à ce que les résultats deviennent significatifs. Ce type d'approche a été baptisé _p-hacking_. Le p-hacking peut prendre de nombreuses formes: exclusion arbitraire de "valeurs aberrantes", sélection d'un sous-groupe qui montre l'effet attendu, changement des paramètres de prétraitements.

#### HARKing
La dernière pratique douteuse est baptisée le « HARKing ». Le terme HARK est un acronyme en anglais pour les termes « Hypothesis after results are known », ou bien "définition des hypothèses après que les résultats soient connus". On va effectuer de nombreux tests à partir des données recueillies, et on formule a posteriori des hypothèses correspondant aux résultats significatifs dans l'échantillon. Ce processus n'est pas nécessairement malicieux, mais peut émerger d'une volonté d'interpréter les données. Cette démarche n'est pas nécessairement problématique, du moment que les hypothèses sont (correctement) présentées comme exploratoires, guidées par les données, plutôt que comme une hypothèse a priori rigoureuse.

## Reproducibilité et neuroimagerie
Nous allons maintenant voir comment la neuroimagerie représente un domaine particulièrement propice au p-hacking, et d'autres facteurs qui contribuent au manque de reproductibilité. Ces facteurs sont tous liés à la complexité des chaines de traitement en neuroimagerie.
 * Tout d'abord, il est possible de faire varier beaucoup les conclusions d'une étude juste en modifiant les choix analytiques que l'on fait dans la chaine de traitement (ce que l'on appelle les **degrés de liberté en recherche**).
 * Ensuite, il est possible d'ajuster nos modèles de manière excessive à l'échantillon de données que l'on a acquis, sans que cela généralise à un jeu de données indépendant (un mécanisme appelé **overfitting**).
 * Enfin, à cause de la complexité des méthodes utilisées, il est souvent difficile voir impossible de vraiment comprendre les méthodes utilisées dans un article à partir du texte de cet article (**méthodes incomplètes**).

### Degrés de liberté en recherche
```{figure} ./reproductibilite/multi_analyses_fmri.png
---
width: 800px
name: multi-analyses-fmri-fig
---
Cette figure résume les cartes d'activations IRMf générées par 64 équipes indépendantes, à partir des mêmes données et pour tester la même hypothèse. Les équipes ont été séparées en trois sous groupes, sur la base de la similarité spatiale de leurs cartes d'activation à l'aide d'un algorithme automatique. Le premier groupe (cluster 1) est le plus gros, avec 50 équipes, alors que les deux autres sous-groupes incluent 7 équipes chaque. Notez les variations importantes entre les trois sous-groupes. Figure tirée de [Botvinik-Nezer et al., 2020](https://doi.org/10.1101/843193) sous licence [CC-BY-NC-ND 4.0](http://creativecommons.org/licenses/by-nc-nd/4.0/).
```
Pour chacune des techniques étudiées dans ces notes de cours, il est nécessaire d'implémenter une série d'étapes d'analyses, et chaque étape demande de choisir certains paramètres. Dans la mesure où l'on n'a pas de vérité de terrain auquelle se référer en neuroimagerie, il n'existe pas de consensus sur le choix optimal pour ces paramètres, et ce choix est probablement dépendant de la population d'intérêt et des questions de recherche dans une large mesure. Pour quantifier cette variabilité, une étude récente a invité 70 équipes de recherche à analyser le même jeu de données, et tester les mêmes hypothèses, sur un jeu de données par activation en IRMf [Botvinik-Nezer et al., 2020](https://doi.org/10.1101/843193). Un premier résultat frappant est que chaque équipe a utilisé une approche unique pour analyser les données, illustrant le manque criant de standardisation dans le domaine. Un autre résultat frappant est, pour une hypothèse donnée, certaines équipes ont produit des cartes très différentes, voir {numref}`multi-analyses-fmri-fig`. Bien que certains sous-groupes d'équipes ont identifié des cartes très similaires, certains choix ont amené à des différences importantes. Par ailleurs, même pour les équipes générant des cartes similaires, leur interprétation de la carte pour répondre à l'hypothèse variait substantiellement!

```{admonition} Degrés de liberté en recherche et p-hacking
:class: tip
:name: researcher-degrees-freedom-tip

Le nombre de paramètres qu'un chercheur peut manipuler est appelé _degrés de liberté en recherche_. Comme la neuroimagerie a un très grand nombre de degrés de liberté, cela augmente le risque de p-hacking, car il est toujours possible de comparer plusieurs approches pour sélectionner la "meilleure", c'est à dire celle qui amène les résultats les plus conformes aux hypothèses de l'équipe de recherche.
```

```{admonition} Impact des logiciels d'analyse et de l'environnement
:class: caution attention
:name: softwark-warning
Au delà des paramètres utilisés dans une analyse, des différences substantielles peuvent venir du choix du logiciel, ou de la version du logiciel utilisée ([Bowring et al., 2019](https://doi.org/10.1002/hbm.24603)). Même des changements mineurs peuvent avoir un impact sur les résultats. Et cela n'est pas limité au logiciel de neuroimagerie en tant que tel. Un changement de système d'opération peut lui aussi créer des différences, par exemple dans une analyse de morphométrie ([Gronenschild et al., 2012](https://doi.org/10.1371/journal.pone.0038234)).
```

### Tailles d'effet
Une autre erreur qu’on voit particulièrement en neuroimagerie c’est
d’interpréter une différence significative comme une différence importante.
Une différence significative signifierait que les populations étudiées sont
complètement différentes. Par exemple, on trouve que les amygdales des
personnes sur le spectre de l’autisme est plus petite que pour les gens
neurotypiques. Cela signifie que la différence de la moyenne des
distributions est différente, pas que tous les individus du groupe des gens
ayant un TSA ont une amygdale plus petite que les gens neurotypiques.
Malheureusement, beaucoup de chercheurs et de professionnels ont
tendance à faire ce genre de conclusion. Une manière d’aller voir la taille de
la différence est en regardant le d de Cohen. Un d de Cohen de 0.1 serait ce
qu’on va trouver si on remarque une différence significative entre nos
moyennes de groupe, tel qu’illustrer avec l’exemple tes TSA plus tôt. Un d de
Cohen de 0.79 décrirait un effet de groupe immense, ou le score de tous les
membres d’un groupe est inférieur au score de tous les membres de l’autre
groupe. Or, si on a un N très grand, même avec un d de Cohen de 0.1, on va
avoir un p de 0.000001. Il est important de comprendre que malgré la
significativité de la différence de groupe, le p ne nous dit rien quant à
l’importance de cette différence. Le vocabulaire est très important a
comprendre, car beaucoup de gens vont dire qu’il y a une forte différence en
raison du p, alors que cette valeur ne nous dit rien au sujet de la taille
d’effet. En fait, ce qu’on doit retenir, c’est que si on score significatif n’a pas
de taille d’effet, elle n’a pas de raison d’être répliqué. Pour répliquer un effet,
il doit être significatif et fort. En neuroimagerie surtout, on fait beaucoup de
tests statistiques alors on se retrouve souvent avec des p qui sont très
faibles. La majorité des papiers ne rapportent même pas les tailles d’effet,
alors au final, personne ne regarde si l’effet est fort. Or, cette question est
très importante car la taille de la différence a un impact sur la
reproductibilité

### Méthodes incomplètes
```{figure} ./reproductibilite/machine_learning.png
---
width: 600px
name: machine-learning-fig
---
Cette figure illustre le processus parfois chaotique de développement d'une méthode optimale, et la difficulté de communiquer ce processus de manière claire et complète dans une section de méthodes d'un article. Cette figure est tirée de [xkcd webcomic](https://xkcd.com/1838/), sous licence [CC-BY-NC 2.5](https://creativecommons.org/licenses/by-nc/2.5/).
```
Le manque de détails dans la section "Méthodes" d'un article peut être nn autre obstacle majeur à la reproduction des résultats. Comme les techniques d'analyse utilisées en neuroimagerie sont souvent complexes, il est très rare d'avoir une description complète des méthodes. Il est aussi courant d'omettre les étapes qui ont amené à la sélection des méthodes utilisées dans l'article. Le texte d'un article scientifique est généralement écrit de manière à raconter une histoire claire. Le matériel supplémentaire de l'article contient parfois (mais pas toujours) plus de détails méthodologiques ainsi que des expériences supplémentaires, non essentielles au narratif principal de l'article. Il se peut tout à fait que d'autres analyses soient omises entièrement de l'article, et que les membres de l'équipe de recherche soient eux même incapables de retracer le processus qui a amené à la sélection des analyses finales publiées dans l'article.

## Des solutions

### Études pré-enregistrées
Solutions permettant de répondre à la crise de la reproductibilité
La première idée, serait de modifier la manière dont on publie les articles. Un
des problèmes dans le cercle présenté au départ, c’est qu’il y a un temps
phénoménal entre l’élaboration des hypothèses et la publication en plus de
ne pas avoir de publication des résultats négatifs. Une manière d’éliminer ça,
c’est la publication des hypothèses et des plans d’analyse. Cela permet aux
reviewers de critiquer la conception de l’étude avant qu’elle soit terminée,
donc permettrait les modifications à priori si nécessaire. De plus, si les
résultats ne collent pas aux hypothèses, ce ne serait pas grave car l’article
serait déjà accepté. Il existe des articles préenregistrés, ou des registered
recall. C’est comme un article normal, il manque seulement les résultats et
la discussion. Une fois que les gens se sont mis d’accord quant a la méthode,
l’article est accepté peu importe les résultats. Cela ne veut pas dire qu’on ne
peut pas présenter des nouvelles analyses auxquelles on n’avait pas pensé
avant. Celles-ci seront alors présentées comme exploratoires comme il se
devrait, plutôt que confirmatoires comme c’est souvent le cas dans les
articles dont la méthode n’est pas acceptée et publiée d’avance., ce qu’on
appelle le HARKing. Ce type d’approche ne colle pas avec tout ce qu’on
souhaiterait publier. Ce ne fonctionne pas vraiment bien avec le travail
méthodologique, non plus avec les analyses plus exploratoires. Par contre,
pour les articles qui suivent la démarche traditionnelle, cela fonctionnerait
bien et répondrait à beaucoup des problèmes vus au début de ce cours.

### Code
Une autre solution serait d’apprendre à coder. Automatiser les analyses
permet de les rendre plus facile pour quiconque de les reproduire. Il peut y
avoir des erreurs dans le code, mais elles peuvent être vues et réparées
avec des traces. Les analyses qui ne reposent pas sur du code représente un
obstacle majeur à la reproductibilité.

### Partage de code
Ensuite, partager ce code est un peu anxiogène. Souvent, les gens sont
réticents a rendre public le code utilisé pour générer un article. Comme c’est
une partie critique du travail de recherche, ça vaut la peine d’apprendre a le
faire comme il faut et de le partager pour aider à réduire le problème de
reproductibilité. Beaucoup de gens qui utilisent Github, une plateforme qui
permet de partager le code et aussi de partager les modifications qui y sont
faites avec le temps. En fait, la principale personne qui bénéficie de la
publicisation de son code est la personne qui le publie, car si le projet évolue
dans le temps il y a traces de ce qui a été fait sur cette plateforme. Le
monstre du 2e sous-sol est alors un peu moins caché et un peu moins
inconnu pour vous et pour les personnes qui vous lisent à la suite de la
publication. De plus en plus, on s’attend que les scripts d’analyse soient
rendus publics lors de la publication des papiers.

### Partage de données
Une autre solution est de partager les données. Cela facilite la vie des
laboratoires car 1 an et 2 ans après avoir publié un papier, il est possible
qu’on ne se souvienne même plus ou se trouvent les données et quelle
version des données qui a été utilisée. Le partage de celles-ci rend plus facile
de se relire et de retrouver nos traces. Malheureusement, ce n’est pas facile
de rendre nos données publiques. De plus en plus de gens poussent pour
rendre le partage de données plus commun et plus facile, mais ce n’est pas
encore fait.
Partager ses données c’est comme un spectre qui est représenté par ce
graphique. Sur l’axe des y on a à quel point c’est utilisable et sur l’axe des x
c’est a quel point c’est beaucoup de données et ça prend du temps a
préparer. En bas, on a les données ADNI ou HCP, des projets ou les gens
publient leurs données brutes et les données prétraitées. Ensuite, on a
d’autres personnes qui partagent uniquement leurs données brutes. Par la
suite, on a des gens qui partagent leurs cartes statistiques. Enfin, les
coordonnées des types d’activation est quelque chose que beaucoup de
gens partagent dans les articles et qui est très utile, bien que beaucoup
moins riche que les cartes elles-mêmes. Le jour ou on arrivera a partager nos
données systématiquement avec nos articles on va avoir de grandes
améliorations au niveau de la reproductibilité.

### Partage d'environnement
Ensuite, on a des outils qui permettent de partager notre environnement de
travail. Une initiative qui est bien appréciée est neurodebian, qui est une
version de Linux qui vient préinstaller avec un appstore pour la
neuroimagerie. On peut installer directement les logiciels et les systèmes
d’opération. Ainsi, quelqu’un qui veut reproduire votre environnement
pourrait le faire. Il y a aussi les containers qui permettent de garder tout
l’environnement de travail sur un seul fichier, cela fonctionne sur Linux et il y
a des manières de le faire fonctionner sur Mac et sur Windows. C’est
beaucoup utilisé pour la programmation Web, et la communauté scientifique
a commencé à l’utiliser pour améliorer la reproductibilité.

### Bonnes pratiques
Certains articles se concentrent sur la formulation de « guides » de bonnes
pratiques pour différentes techniques et méthodes de recherche. Cela
permet de voir ce qui est le plus utile pour contrer la crise de reproductibilité
en fonction des méthodes les plus convoitées en neuroscience cognitive.
Une autre chose qui peut être faite est d’étudier la puissance statistique en
élaborant un projet pour savoir si on doit modifier la taille de notre
échantillon. Cela peut être fait avec différents logiciels et avec le site web
suivant : https://rpsychologist.com/d3/nhst/

### La méthode scientifique revisitée

### Vers une science généralisable

## Conclusions

## Exercices
