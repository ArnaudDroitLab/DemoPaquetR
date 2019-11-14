# Intégration continue avec Travis

## Qu'est-ce que l'intégration continue?

Selon wikipédia, "L'intégration continue est un ensemble de pratiques utilisées 
en génie logiciel consistant à vérifier à chaque modification de code source que
le résultat des modifications ne produit pas de régression dans l'application
développée."

Dans la pratique, l'intégration continue se manifeste par l'utilisation de
processus de tests et de déploiement automatiques. Travis-CI est un des outils
permettant d'ajouté un tel processus à un projet hébergé sur github.

Une fois l'intégration à Travis-CI activé, chaque nouveau `push` d'un `commit`
sur github cause le démarrage d'une machine virtuelle pouvant exécuter une suite
de scripts arbitraires dont les résultats sont publiés sur 
[https://travis-ci.org]. Dans le cas d'un paquet R, de tels scripts sont 
généralement utilisés pour effectuer les commande `R CMD build`, `R CMD check`,
et `R CMD BiocCheck`.

github, Travis-CI et R sont hautement intégrés et automatisés. Activer 
l'intégration continue est l'affaire de quelques minutes seulement.

## Activer l'intégration continue sur Travis-CI
1. Allez à https://travis-ci.org/
2. Authentifiez-vous avec votre accompte github.
3. Allez dans la page Paramètres/Settings.
4. Activez l'intégration pour le projet contenant votre paquet.
   Si votre paquet existe sous ArnaudDroitLab, vous devrez d'abord cliquer sur 
   l'organization dans la barre latérale.
5. Ajoutez un fichier de configuration travis à votre paquet en utilisant 
   `usethis::use_travis()`. Le fichier généré ressemblera à ceci:
```     
# R for travis: see documentation at https://docs.travis-ci.com/user/languages/r

language: R
cache: packages
```
6. Utilisez le markdown fourni par `usethis` pour ajouter le badge de "build"
   à votre README.md sur votre page github.
7. Ajoutez les lignes suivantes à .travis.yml:

```
# Tell Travis that we're using bioconductor packages.
# Could also use bioc-release  
r: bioc-devel

# Tell Travis to run BiocCheck.
bioc_check: true

# Tell Travis to send us an email notification if the build fails.
notifications:
  email:
    on_success: change
    on_failure: change
```

## Activer le profilage de couverture

[https://codecov.io] fournit un outil de visualisation de la couverture du code
d'un projet par ces tests unitaires, et peut être activé en quelques étapes 
simples.

1. Allez à  https://codecov.io/
2. Authentifiez-vous avec votre accompte github.
3. Activez l'intégration pour le repositoire contenant votre paquet.
   Si votre paquet existe sous ArnaudDroitLab, vous devrez d'abord cliquez
   sur le bouton de l'organisation.
4. Cliquez sur votre projet, puis sur "Settings", puis sur "Badge" pour obtenir 
   le Markdown de votre badge de couverture. Ajoutez le à votre README.md.
5. Ajoutez les lignes suivantes à .travis.yml:

```  
# Install the covr dependancy
r_packages:
  - covr

# Run the coverage tests after a successful build.  
after_success:
  - Rscript -e 'covr::codecov(type="all")'      
```  

6. Faites un `commit` de `.travis.yml`, `.Rbuildignore`, `README.md` et poussez
   vos changements. Allez sur [https://travis-ci.org], et fixez la page
   intensément jusqu'à ce que le nom de votre projet passe du jaune au vert.
   
   Félicitations! Vous avez miantenant de l'intégration continue pour votre 
   paquet!

## Notes utiles
 - Vous pouvez faire une analyse de couverture de votre code locale en appelant 
   `covr::codecov(type="all")`. Pour téléverser les résultats sur 
   [https://codecov.io], vous devrez toutefois utiliser votre jeton d'accès:
   `covr::codecov(type="all", token="my-token")`. Votre jeton est disponible
   sur [https://codecov.io], dans les paramètres de votre projet.
 - Vos premiers builds sur Travis-CI sont susceptibles d'échouer, puisque toutes
   vos dépendances sont bâties à partir de leur code source, ce qui prends
   beaucoup de temps et risque de vous faire atteindre la limite de 50 minutes.
   Toutefois, la ligne `cache: packages` permet aux dépendances d'êtres mises en
   cache, ce qui accélère considérablement les builds subséquent.
   