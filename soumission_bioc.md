# Soumission d'un paquet R à un repositoire publique

## Repositoires

Il existe deux repositoires publiques acceptant des paquets R: 
[CRAN](https://cran.r-project.org/) et 
[Bioconductor](https://www.bioconductor.org/). CRAN accepte les paquets 
d'intérêt général (par exemple, tous les paquets du [tidyverse]
(https://www.tidyverse.org/). Bioconductor se spécialise dans les paquets du
domaine bioinformatique.

Une bonne règle de pouce pour savoir où votre paquet devrait aller: si votre 
paquet dépend d'au moins un paquet Bioconductor, il devrait aller dans 
Bioconductor.

[Lien pour la soumission de paquets CRAN](https://cran.r-project.org/submit.html)

[Lien pour la soumission de paquets Bioconductor]
(https://www.bioconductor.org/developers/package-submission/)

Nous nous concentrerons sur la soumission à Bioconductor, qui sera celle choisie
pour la plupart de vos projets.

## Soumission à Bioconductor

Bioconductor fournit des [instructions détaillées]
(https://www.bioconductor.org/developers/package-submission/) pour la soumission 
de paquets. Toutefois, avant de s'attaquer aux pré-requis spécifique de 
Bioconductor, assurez-vous des choses suivantes:

- Votre paquet a son propre git sur github. Le répertoire a racine du paquet
  doit être le répertoire racine du git. Si vous produisez le paquet dans le 
  cadre de votre travail au labo, je vous suggère d'utiliser le groupe
  [ArnaudDroitLab](https://github.com/ArnaudDroitLab).
- Votre paquet doit avoir un champs BugReports dans son fichier DESCRIPTION.
  Je vous suggère fortemnt d'utiliser la page *issues* du répertoire git
  du projet.
- Votre paquet doit passer R CMD CHECK et R CMD BiocCheck sans avertissements
  et sans erreurs.
  
### Types de paquet Bioconductor

Bioconductor sépare ses paquets en deux grands groupes: les paquets logiciels,
et les paquets de données. Les paquets de données sont eux même séparés en 
paquets d'`ExperimentHub` et d'`AnnotationHub`.

Les paquets logiciels doivent, une fois BUILDé, peser moins de 5 Mo. Un paquet
logiciel peut contenir des données, mais celles-ci doivent être peu 
volumineuses et doivent servir de complément aux fonctionalités offertes par le
paquet.
  
Un paquet de donnée est un paquet où un jeu de donnée joue un rôle central. Le
paquet peut également contenir des fonctions pour explorer ou regénérer le jeu
de donnée. Les paquets de donnée sont généralement plutôt minces: leurs données
ne vivent pas directement dans le paquet, mais sur un serveur web externe.
Bioconductor fournit de l'espace sur Amazon S3 pour les paquets de données
n'ayant pas leur propre serveurs.

La distinction entre un paquet AnnotationHub et un paquet ExperimentHub est floue.
Généralement, AnnotationHub contient des données plus brutes, ExperimentHub,
des données un peu plus retravaillées.

[Créer un paquet ExperimentHub](http://bioconductor.org/packages/release/bioc/vignettes/ExperimentHub/inst/doc/CreateAnExperimentHubPackage.html)
[Créer un paquet AnnotationHub](http://bioconductor.org/packages/release/bioc/vignettes/AnnotationHub/inst/doc/CreateAnAnnotationPackage.html)

### Obligations liées à la soumission de paquets

Soumettre un paquet à Bioconductor implique diverses obligations. Notamment,
le le soumissionaire s'engage à maintenir le paquet, c'est à dire que celui-ci
doit répondre aux rapports de bugs et s'assurer que le paquet continue de 
passer BUILD, CHECK et BiocCheck pour toutes les nouvelles sorties de 
Bioconductor, qui arrivent deux fois par ans (fin avril et fin octobre).

Le mainteneur du paquet doit également s'abonner à la liste [bioc-devel]
(https://stat.ethz.ch/mailman/listinfo/bioc-devel) et s'inscire au [site de 
support de Bioconductor](https://support.bioconductor.org/).