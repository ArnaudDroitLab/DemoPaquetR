# Soumission d'un paquet R à un repositoire publique

## Repositoires

Il existe deux repositoires publiques acceptant des paquets R: 
[CRAN](https://cran.r-project.org/) et 
[Bioconductor](https://www.bioconductor.org/). CRAN accepte les paquets 
d'intérêt général (par exemple, tous les paquets du [tidyverse](https://www.tidyverse.org/).
Bioconductor se spécialise dans les paquets du
domaine bioinformatique.

Une bonne règle de pouce pour savoir où votre paquet devrait aller: si votre 
paquet dépend d'au moins un paquet Bioconductor, il devrait aller dans 
Bioconductor.

[Lien pour la soumission de paquets CRAN](https://cran.r-project.org/submit.html)

[Lien pour la soumission de paquets Bioconductor](https://www.bioconductor.org/developers/package-submission/)

Nous nous concentrerons sur la soumission à Bioconductor, qui sera celle choisie
pour la plupart de vos projets.

## Soumission à Bioconductor

Bioconductor fournit des [instructions détaillées](https://www.bioconductor.org/developers/package-submission/)
pour la soumission 
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
- Le numéro de version de votre paquet est 0.99.0. 
- Votre paquet doit être bien documenté. Une vignette est quasi-obligatoire.
  
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

### Bioconductor devel vs Bioconductor release

Tous les nouveaux développement de Bioconductor se font sur la branche devel
de celui-ci.

La branche *release* de Bioconductor est celle accessible à tous les usagés, et 
est "barrée" entre chaque release, sauf pour la correction de bugs critiques.

La branche *devel* de Bioconductor contient tous les plus récents développements, 
ce qui implique que plusieurs paquets y sont brisés.

Pour utiliser Bioconductor-devel, vous devez suivre les 
[instructions fournies par Bioconductor](https://bioconductor.org/developers/how-to/useDevel/).

R lance généralement une nouvelle révision vers le mois de mai. Ainsi, 
au mois d'Avril, Bioconductor-devel utilise également la version développement de
R en alpha/bêta. Vous devrez l'installer pour pouvoir tester votre paquet.

### Obligations liées à la soumission de paquets

Soumettre un paquet à Bioconductor implique diverses obligations. Notamment,
le le soumissionaire s'engage à maintenir le paquet, c'est à dire que celui-ci
doit répondre aux rapports de bugs et s'assurer que le paquet continue de 
passer BUILD, CHECK et BiocCheck pour toutes les nouvelles sorties de 
Bioconductor, qui arrivent deux fois par ans (fin avril et fin octobre).

Le mainteneur du paquet doit également s'abonner à la liste 
[bioc-devel](https://stat.ethz.ch/mailman/listinfo/bioc-devel) et s'inscire au 
[site de support de Bioconductor](https://support.bioconductor.org/).

### Processus de soumission

Avant de soumettre le paquet à Bioconductor, vous devez ajouter un webhook au 
git de celui-ci. Un webhook est une fonctionalité de github qui fait qu'un site
externe peut être notifié chaque fois que vous poussez de nouvelles modifications.
Bioconductor utilisera ces notifications pour déterminer quand il doit lancer un 
nouveau BUILD de votre paquet.

[Procédure d'ajout du WebHook](https://github.com/Bioconductor/Contributions#adding-a-web-hook)

Lorsque le paquet est prêt pour la soumission, il faut 
[créer une issue](https://github.com/Bioconductor/Contributions/issues/new) sur le
github de [Bioconductor/Contributions](https://github.com/Bioconductor/Contributions).

Un des modérateurs de Biocondcutor prendra état de votre soumission et, si
celle-ci semble pertinente, lui assignera un reviewer.

Lorsqu'un reviewer est assigné, Bioconductor lancera une build de votre paquet.
Votre git sera cloné sur les machines de build de Bioconductor, et celui-ci
sera BUILD-é, CHECK-é et BiocCheck-é sur les trois plateformes supportés par 
Bioconductor: Windows, Unix et MacOS.

Peu importe à quel point vous avez été soucieux et minutieux et que vous avez
refait BUILD, CHECK et BiocCheck sur votre propre machine avant de soumettre, 
votre premier BUILD sur Bioconductor échouera de façon humiliante et 
catastrophique. bioc-issue-bot ajoutera alors un commentaire à votre issue
avec un lien vers le 
[build report](http://bioconductor.org/spb_reports/metagene2_buildreport_20190405175625.html).
Vous pouvez alors effectuer et pousser des 
correctifs sur votre git. Lorsque vous êtes prêts à retenter le processus de 
BUILD, vous devez incrémenter le 'z' de votre numéro de version et poussez la 
modification. Seuls les push où le numéro de version est incrémenté relance le
BUILD de Bioconductor.

Une fois que le paquets passera l'étape de BUILD, votre reviewer vous fera ses
demandes et commentaires. Vous devrez y répondre avant de pouvoir soumettre
votre paquet. Même si les NOTES de BiocCheck n'empêchent pas un paquet d'être 
publié, celles-ci font généralement l'objet de commentaires des reviewers.

[Exemple de soumission](https://github.com/Bioconductor/Contributions/issues/1081)

Lorsque votre paquet est accepté par le reviewer, celui-ci est ajouté à la branche
devel de Bioconductor.

### Tenir à jour un paquet sur Bioconductor-devel

Une fois la soumission à Bioconductor terminée, le webhook de votre paquet sera 
désactivé, et les changements effectués sur votre git ne se réflecteront plus sur
Bioconductor, et les BUILD automatique ne seront plus lancées lorsque vous
augmenterez votre numéro de version.

Vous recevrez plutôt accès au système git de Bioconductor, qui vous donnera 
accès à plusieurs git où vous devrez pousser vos modifications en fonction
de la release que vous visez:

- *upstream master* est la branche de développement. Les modifications dans ce git
  seront incorporés à Bioconductor-devel pendant les *nightly builds*.
- *upstream RELEASE_X_Y* sont les branches de maintenance. Vous ne devriez
  jamais rien poussez sur une branche de maintenance sauf un fix pour un bug 
  critique qui rend votre paquet inutilisable.

Le git de bioconductor n'est pas accessible avec des noms d'usagers/mots de passe.
Vous devrez avoir une pair de clés SSH publique/privée associée à votre compte
github pour pouvoir y pousser des modifications.

[Ajouter une clé SSH à votre compte git](https://help.github.com/en/articles/connecting-to-github-with-ssh)
[Utiliser git pour maintenir un paquet accepté](https://bioconductor.org/developers/how-to/git/maintain-bioc-only/)
