## Paquets nécessaires

Les outils `devtools` et `usethis` ont été créés et sont maintenus par
Rstudio. Ils permettent d'automatiser la création et l'initialisation des
fichiers et des répertoires en plus de faciliter la documentation et les tests
des fonctions lors de la mise en place de notre paquet.

```r
library(devtools)
library(usethis)
```

## Mise en place

Dans cette section, nous verrons comment mettre en place rapidement un
squelette de paquet qui contient tous les fichiers obligatoires pour un paquet
R.

### Créer le répertoire

Pour cette démonstation, nous allons créer un paquet R nomme `paquet`. Pour se
simplifier la vie, nous allons utiliser la fonction `create_package()` du
paquet `usethis`.

```r
usethis::create_package("paquet")
```

Les fichiers/dossiers suivant seront créés:

* `DESCRIPTION`: Metadonnées du paquet (nom, but, auteur, dépendances ...)
* `NAMESPACE`: Fichier qui décrit les paquets/fonctions importés et exportés
* `R/`: Là où on va retrouver tout le code

On doit modifier manuellement le fichier `DESCRIPTION`:

```
Package: paquet
Title: Paquet démo pour formation
Version: 0.0.0.9000
Authors@R:
    person(given = "Charles",
           family = "Joly",
           role = c("aut", "cre"),
           email = "selarch@gmail.com",
           comment = c(ORCID = "YOUR-ORCID-ID"))
Description: Montrer comment bâtir un paquet.
License: What license it uses
Encoding: UTF-8
LazyData: true
Depends:
    R (>= 4.0.0)
```

Il faut donc remplir les sections suivantes:

* `Title`: Un titre bref, mais informatif.
* `Authors@R`: La liste des auteurs.
* `Description`: Un court survol des caractéristiques du paquet.

De plus, pour éviter des problèmes lors des validations du paquet, on ajoutera
une dépendance à une version de R (la partie `R (>= 3.5)` dans la section
`Depends:`).

Le fichier `NAMESPACE` ne devrait pas être directement modifier. Il faut plutôt
passer par la documentation avec ROxygen et `devtools::document()`.

### License

Le paquet `usethis` permet de créer les fichiers de licenses. Plusieurs types
de licenses sont disponibles. Dans cet exemple, nous utiliserons la licence
MIT:

```r
usethis::use_mit_license("Charles Joly")
```

### Tests unitaires

Pour créer les fichiers et répertoires nécessaires aux tests unitaires, on peut
utiliser la commande suivante:

```r
usethis::use_testthat()
✔ Adding 'testthat' to Suggests field in DESCRIPTION
✔ Creating 'tests/testthat/'
✔ Writing 'tests/testthat.R'
● Call `use_test()` to initialize a basic test file and open it for editing.
```

Les tests unitaires seront expliqués en plus grand détail dans une section
subséquente.

### Vignette

C'est dans la vignette qu'on retrouvera la documentation moins formelle de
notre paquet.

Pour créer un squelette de vignette, on peut utiliser la fonction suivante:

```r
usethis::use_vignette("ma-vignette")
```

Ce code permettra d'obtenir le fichier `vignettes/ma-vignette.Rmd`

## Code

### Les manuels

Créons une fonction R de base dans le fichier `R/add.R`:

```r
#' Add together two numbers.
#'
#' A more detailed description.
#'
#' @param x A number.
#' @param y A number.
#'
#' @return The sum of \code{x} and \code{y}.
#'
#' @examples
#' add(1, 1)
#' add(10, 1)
add <- function(x, y) {
  x + y
}
```

Les balises ROxygen commencent par `#'`. On retrouve généralement les sections
suivantes:

* Titre: La première phrase de la documentation. Un résumé de la fonction en
  quelques mots.
* Description: Le deuxième paragraphe de la documentation. Une brève
  description de la fonction.
* Détails: Les paragraphes suivants. Une description détaillée de la fonction.
* `@param`: La description des paramètres de la fonction.
* `@return`: La description de l'objet retourné par la fonction.
* `@examples`: Un exemple d'utilisation de la fonction. À noter que cet exemple
  doit être fonctionnel car il sera exécuté lors de la validation du paquet.
  Pour cette même raison, on doit éviter que le code soit trop long à exécuter.
  Si l'exemple ne peut être raccourci et prend trop de temps à exécuter, on
  peut utiliser la balise `\dontrun{}`.

Pour créer la documentation officielle de la fonction, nous allons tout d'abord
ouvrir une session R à partir du répertoire correspondand à notre paquet.

Le paquet `devtools` offre une fonction `document()` qui permet de créer le
manuel à partir de la documentation ROxygen.

```r
devtools::document()
```

On peut ensuite tester notre fonction en chargeant le paquet:

```r
devtools::load_all()
add(2,1)
[1] 3
```

### Exporter et importer

Comme presque tous les langages de programmation, une des principales tâches de
R est d'associer des noms (`x`, `nom_usager`, `add`) à des valeurs (`1`, 
`"John"`, `function(x,y) { x+y }`, etc). Chaque paquet contenant des centaines
de variables et de fonctions, ceux-ci se retrouvent rapidement à réutiliser
des noms de variables déjà en usage dans d'autres paquets.

Afin de gérer cette complexité, R encapsule les paquets dans un NAMESPACE.
Chaque paquet a donc une liste de noms de variables qu'il rend visible à 
l'usager (les noms **exportés**) et une liste de noms de variable d'autres
paquet qu'il veut pouvoir utiliser (les noms **importés**).

#### Exporter

Pour que la fonction soit accessible aux utilisateurs, il faut l'exporter. Pour
ce faire, il faut ajouter `@export` dans la documentation de la fonction:

```r
#' Add together two numbers.
#'
#' @param x A number.
#' @param y A number.
#'
#' @return The sum of \code{x} and \code{y}.
#'
#' @examples
#' add(1, 1)
#' add(10, 1)
#'
#' @export
add <- function(x, y) {
  x + y
}
```

Puis recréer la documentation:

```r
devtools::document()
```

Finalement, on recharge le paquet pour valider que tout a bien fonctionné:

```r
devtools::load_all()
```

En regardant dans le fichier `NAMESPACE`, on peut voir que la fonction est bien
exportée:

```
# Generated by roxygen2: do not edit by hand

export(add)
```

Pour l'utilisateur, la fonction pourra désormais être utilisée après le
chargement du paquet.

```r
> add(3,4)
Error: object 'add' not found

> library(paquet)
> add(3,4)
[1] 7
```

#### Importer

Un des buts des paquets R est de s'assurer que tous les paquets nécessaires
pour l'exécution des fonctionnalités soient présents lors de leur appel.

Au niveau de la structure du paquet, cela implique de modifier les fichiers
`DESCRIPTION` et `NAMESPACE` de manière à réfléter les besoins de notre paquet.
L'on reviendra sur le fichier `DESCRIPTION` dans la section suivante.

Par exemple, supposons que l'on veut des fonctionalités de `dplyr`:

```r
add <- function(x, y) {
    data.frame(res = x + y) %>%
        as_tibble
}
```

Lors de l'appel de la fonction, on obtient l'erreur suivante:

```r
> add(3,4)
Error in data.frame(res = x + y) %>% as_tibble : 
  could not find function "%>%"
```

%>% est un opérateur **exporté** par `dplyr`. Puisque nous n'avons pas indiqué à 
R que nous voulions utiliser les fonctionalités de `dplyr`, celui-ci ne sait pas
où trouver cette fonction. Nous devons lui indiquer en l'ajoutant dans le 
fichier NAMESPACE.

Il est recommandé de ne jamais modifié directement le fichier `NAMESPACE`. On
passe plutôt par les documentations de chaque fonction pour ajouter les paquets
complémentaires. De cette manière, si on retire une fonction, toutes ses
dépendances pourront être retirées automatiquement lorsque la documentation
sera regénérée. La commande pour importer un paquet est `@import`:

```r
#' Add together two numbers.
#'
#' @param x A number.
#' @param y A number.
#'
#' @return The sum of \code{x} and \code{y}.
#'
#' @examples
#' add(1, 1)
#' add(10, 1)
#'
#' @import dplyr
#'
#' @export
add <- function(x, y) {
    data.frame(res = x + y) %>%
        as_tibble
}
```

On peut ensuite créer la documentation et tester:

```r
devtools::document()
devtools::load_all()
add(3,4)
# A tibble: 1 x 1
    res
  <dbl>
1     7
```

On peut confirmer que le fichier que le fichier `NAMESPACE` est à jour:

```
# Generated by roxygen2: do not edit by hand

export(add)
import(dplyr)
```

### `Import`, `Depends` et `Suggests`

Les directives `Import`, `Depends` et `Suggests` indiquent les dépendances d'un
paquet et influent sur trois actions effectuées par R:

 - Lorsque le paquet est **installé**, la dépendance est-elle aussi installée?
 - Lorsque le paquet est **chargé**, la dépendance est-elle aussi chargée?
 - Lorsque le paquet est **attaché**, la dépendance est-elle aussi attachée?

Chaque type de dépendance a son propre comportement vis-a-vis ces trois 
scénarios:

|                    | Depends            | Imports            | Suggests |
|-------------------:|:------------------:|:------------------:|:--------:|
| Installe le paquet | :heavy_check_mark: | :heavy_check_mark: | :x:      |
| Charge le paquet   | :heavy_check_mark: | :heavy_check_mark: | :x:      |
| Attache le paquet  | :heavy_check_mark: | :x:                | :x:      |

 
R fait la distinction entre charger (*Loading*) et attacher (*Attaching*) un 
paquet.

Lorsqu'on charge un paquet, toutes les fonctions seront disponibles mais ne
pourront pas être accédées directement (il faudra utiliser l'opérateur `::`).

Lorsqu'on attache un paquet, on ajoute ses fonctions exportés dans le chemin de
recherche que R va utiliser pour résoudre les espaces de noms.

On ne peut pas attacher un paquet sans l'avoir chargé préalablement. Lorsqu'on
appelle un paquet en utilisant la fonction `library()`, on va le charger **et**
l'attacher.

Dans la majorité des cas, lorsqu'on veut ajouter une dépendance à un paquet
externe, on va utiliser `Imports`. De cette manière, les fonctions du paquets
seront chargés, mais ce dernier ne sera pas attaché.

Lorsqu'on utilise `Depends`, le paquet importé sera également attaché.
L'utilisateur aura donc accès à toutes les fonctions exportées par ce paquet
lorsqu'il sera utilisé. On va utiliser `Depends` lorsque le paquet
complémentaire est indissociable de notre paquet et que l'utilisateur devra
nécessairement utiliser les deux paquets conjointement lors d'une analyse.

Dans ce cas, la documentation restera la même (`@import`), mais il faudra
modifier le fichier `DESCRIPTION`:

```r
usethis::use_package("dplyr", type = "Depends")
✔ Setting active project to '/Users/charles/Tests/paquet'
✔ Moving 'dplyr' from Imports to Dependsfield in DESCRIPTION
● Are you sure you want Depends? Imports is almost always the better choice.
```

Le fichier DESCRIPTION est automatiquement mis-à-jour:

```
Package: paquet
Title: Paquet démo pour formation
Version: 0.0.0.9000
Authors@R:
    person(given = "Charles",
           family = "Joly",
           role = c("aut", "cre"),
           email = "selarch@gmail.com",
           comment = c(ORCID = "YOUR-ORCID-ID"))
Description: Montrer comment bâtir un paquet.
License: MIT + file LICENSE
Encoding: UTF-8
LazyData: true
Depends:
    R (>= 3.5),
    dplyr
Suggests:
    testthat,
    knitr,
    rmarkdown
VignetteBuilder: knitr
RoxygenNote: 6.1.1
```

Pour le tester, on peut utiliser `devtools::load_all()` en spécifiant
d'utiliser uniquement l'environnement exporter par le `NAMESPACE`:

```r
devtools::load_all(".", export_all = FALSE)
```

On peut alors utiliser les fonctions de `dplyr` à travers les fonctions du
paquet:

```r
add(3,4)
# A tibble: 1 x 1
    res
  <dbl>
1     7
```

Mais également à l'extérieur:

```r
data.frame(res = 7) %>% mutate(x = res + 2)
  res x
1   7 9
```

Pour retourner à `Imports`, il faut changer manuellement le fichier
`DESCRIPTION`:

```
Package: paquet
Title: Paquet démo pour formation
Version: 0.0.0.9000
Authors@R:
    person(given = "Charles",
           family = "Joly",
           role = c("aut", "cre"),
           email = "selarch@gmail.com",
           comment = c(ORCID = "YOUR-ORCID-ID"))
Description: Montrer comment bâtir un paquet.
License: MIT + file LICENSE
Encoding: UTF-8
LazyData: true
Depends:
    R (>= 3.5)
Imports:
    dplyr
Suggests:
    testthat,
    knitr,
    rmarkdown
VignetteBuilder: knitr
RoxygenNote: 6.1.1
```

On peut voir ensuite qu'il n'est plus possible d'utiliser les fonctions de
`dplyr` à l'extérieur des fonctions du paquet:

```r
devtools::load_all(".", export_all = FALSE)
add(3,4)
# A tibble: 1 x 1
    res
  <dbl>
1     7
data.frame(res = 7) %>% mutate(x = res + 2)
Error in data.frame(res = 7) %>% mutate(x = res + 2) :
  could not find function "%>%"
```

#### `Suggests`

Finalement, les paquets dans la section `Suggests` ne sont pas essentiels au
fonctionnement du paquet, mais peuvent le complémenter. On y retrouve
généralement les paquets nécessaires pour effectuer les tests unitaires. Ce
pourrait également être un jeu de données externe utilisé uniquement dans la
vignette.

## Tests unitaires

Le paquet `usethis` offre une fonction pour créer automatiquement les fichiers
de tests unitaires:

```r
usethis::use_test(name = "add")
```

On peut alors modifier le fichier `tests/testthat/test-add.R`:

```r
test_that("add works", {
    expect_equal(add(2,3)$res, 2+3)
    expect_equal(add(-2,3)$res, -2+3)
})
```

Pour réaliser les tests, on peut utiliser `devtools::test()`:

```r
devtools::test()
Loading paquet
Testing paquet
✔ | OK F W S | Context
✔ |  2       | test-add

══ Results ═════════════════════════════════════════════════════════════════════
OK:       2
Failed:   0
Warnings: 0
Skipped:  0
```

Les tests sont égalements lancés lors de l'appel de `R CMD check` qui sera
décrite plus en détails dans une section subséquente.

## Les données

On peut vouloir stocker les données d'un paquet sous 2 formats:

* Données binaires (`data/`)
* Données brutes (`inst/extdata`)

### `data/`

Dans le répertoire `data/`, on sauvegarde les données en format binaire. On
parle donc de données ayant généralement subient un traitement.

#### Données accessibles aux utilisateurs

La sauvegarde des données qui sont accessibles aux utilisateurs peut se faire
en utilisant la fonction `usethis::use_data()`:

```r
x <- sample(1000)
usethis::use_data(x)
✔ Saving 'x' to 'data/x.rda'
```

Les utilisateurs pourrons alors accéder à la variable `x` lorsque le paquet
sera chargé.

Il faut également documenter les jeux de données. La documentation doit se
trouver dans un fichier `.R` qui se trouvera dans le dossier `R/`. Par exemple,
on pourrait sauvegarder la documentation de l'objet dans un fichier `R/data.R`:

```r
#' 1000 chiffres.
#'
#' Les 1000 valeurs ont été générées aléatoirement
#'
#' @format A data frame with 53940 rows and 10 variables:
#'
#' @source \url{http://www.asdf.com}
"x"
```

On peut ensuite mettre à jour la documentation avec `devtools::document()`.

À noter que `LazyData: true`, retrouvé dans le fichier `DESCRIPTION`, évite que
l'objet soit chargé en mémoire avant qu'il soit directement utilisé. Celà
permet de sauver de l'espace en mémoire lorsque les données ne sont pas
utilisées.

#### Données internes

On peut également avoir besoin de sauvegarder des données qui seront utilisées
uniquement à l'interne et qui ne doivent donc pas être accessibles aux
utilisateurs. Ces données devront se retrouvées dans le fichier
`R/sysdata.rda`.

On peut utiliser la fonction `usethis::use_data` avec l'option `internal =
TRUE` pour sauvegarder ce type de données.

```r
y <- sample(1000)
z <- sample(1000)
usethis::use_data(y, z, internal = TRUE)
```

### Données brutes

On sauvegarde les données brutes dans le répertoire `inst/extdata`. Il est
possible d'accéder à ces données à l'aide de la fonction `system.file`:

```r
library(readr)
write_csv(data.frame(a = letters[1:3], b = 1:3), "inst/extdata/data.csv")
system.file("extdata", "data.csv", package = "paquet", mustWork = TRUE)
```

## La vignette

La vignette est généralement le premier contact qu'un utilisateur aura avec
notre paquet. Il faut donc s'assurer d'y intégrer toutes les informations
pertinentes pour bien comprendre le but de l'outil.

La vignette ne sera donc pas une documentation détaillée de l'ensemble des
fonctions de notre paquet, contrairement aux manuels. On devra plutôt commencer
par expliquer clairement quel est le but de notre paquet et dans quel contexte
il devrait être utilisé.

On expliquera ensuite les cas d'usage principaux de manière à ce que
l'utilisateur soit en mesure d'adapter facilement les exemples à sa
problématique.

On utilisera donc un style d'écriture plus convivial que pour la documentation
de référence des fonctions. Le tout devrait se lire comme un chapitre d'un
livre ou comme un article de blog.

## Compiler et tester notre paquet

Une fois toutes les sections du paquets complétées, on va ensuite s'assurer que
le paquet puisse être compilé correctement (`R CMD build`) et qu'il passe les
tests de CRAN (`R CMD check`) et de Bioconductor (`R CMD BiocCheck`).

Toutes ces étapes peuvent être assez longue. Il est donc recommandé de générer
une première version minimale du paquet et de la tester rapidement. Il sera
ainsi possible d'éviter de se retrouver devant des dizaines de problèmes à
régler d'un seul coup. Une fois qu'on a une version qui passe tous les tests,
on pourra ajouter progressivement les autres fonctionnalités du paquet et les
tester au fur et à mesure.

### `R CMD build`

La commande `R CMD build` lancée à partir du Shell permettra de créer l'archive
de notre paquet:

```bash
R CMD build paquet/
* checking for file ‘../paquet/DESCRIPTION’ ... OK
* preparing ‘paquet’:
* checking DESCRIPTION meta-information ... OK
* installing the package to build vignettes
* creating vignettes ... OK
* checking for LF line-endings in source and make files and shell scripts
* checking for empty or unneeded directories
* looking to see if a ‘data/datalist’ file should be added
* building ‘paquet_0.0.0.9000.tar.gz’
```

On retrouvera donc une archive (dans ce cas, ce sera le fichier
paquet_0.0.0.9000.tar.gz`). On pourrait installer ce paquet dans R avec la
commande `R CMD INSTALL paquet_0.0.0.9000.tar.gz`.

À cette étape, seulement quelques tests de base auront été effectués sur notre
paquet. C'est plutôt lors des étapes suivantes qu'on validera tous les détails
de notre projet.

### `R CMD check`

Les validations de CRAN sont un ensemble de critères que tout paquet doit
répondre pour pouvoir être publié sur CRAN ou sur Bioconductor. Selon la taille
de notre paquet, cette étape peut être très longue.

On lancera le check sur le paquet compilé:

```bash
R CMD check paquet_0.0.0.9000.tar.gz
* using log directory ‘/Users/charles/Tests/paquet.Rcheck’
* using R version 3.6.0 beta (2019-04-16 r76403)
* using platform: x86_64-apple-darwin15.6.0 (64-bit)
* using session charset: UTF-8
* checking for file ‘paquet/DESCRIPTION’ ... OK
* this is package ‘paquet’ version ‘0.0.0.9000’
* package encoding: UTF-8
* checking package namespace information ... OK
* checking package dependencies ... OK
[...]
* checking tests ...
  Running ‘testthat.R’
 OK
* checking for unstated dependencies in vignettes ... OK
* checking package vignettes in ‘inst/doc’ ... OK
* checking running R code from vignettes ...
   ‘ma-vignette.Rmd’ using ‘UTF-8’ ... OK
 NONE
* checking re-building of vignette outputs ... OK
* checking PDF version of manual ... OK
* DONE

Status: OK
```

Pour être accepté, un paquet ne doit pas avoir ni d'`ERRORS`, ni de `WARNINGS`.
Si on en retrouve après cette étape, il faudra les analyser puis corriger le
code en conséquence. Finalement, on relancera le build et le check jusqu'à ce
qu'ils passent sans erreurs.

Les `NOTES` devraient être corrigés lorsque c'est possible, mais elles
n'empêchera pas la soumission du paquet sur CRAN ou Bioconductor.

### `R CMD BiocCheck`

Le groupe de Bioconductor a mis en place une autre série de tests pour valider
certaines contraintes propres aux paquets à soumettre sous cette plateforme.
Une fois le check complété avec succès, on lancera donc les BiocCheck.

Tout d'abord, il faut s'assurer d'avoir installer le paquet `BiocCheck`, puis
de l'avoir chargé au moins une fois pour l'installer sur notre système.

On peut ensuite lancer le BiocCheck de la manière suivante:

```bash
R CMD BiocCheck paquet_0.0.0.9000.tar.gz
This is BiocCheck version 1.19.33. BiocCheck is a work in progress.
Output and severity of issues may change. Installing package...
* Checking Package Dependencies...
* Checking if other packages can import this one...
* Checking to see if we understand object initialization...
* Checking for deprecated package usage...
* Checking for remote package usage...
* Checking version number...
[...]
* Checking for support site registration...
    Maintainer is registered at support site.


Summary:
ERROR count: 2
WARNING count: 1
NOTE count: 3
For detailed information about these checks, see the BiocCheck
vignette, available at
https://bioconductor.org/packages/3.9/bioc/vignettes/BiocCheck/inst/doc/BiocCheck.html#interpreting-bioccheck-output
BiocCheck FAILED.
```

Encore une fois, il est obligatoire de régler toutes les erreurs et les
avertissements. Il faut tenter de régler également toutes les notes dans la
mesure du possible. Les gens de Bioconductor pourraient exiger de les régler
lors de la soumission du paquet.

Dans notre cas, on peut voir qu'il y a des erreurs et des avertissements. C'est
parce que nous avons mis en place un paquet qui respecte les recommandation
pour un paquet standard sans tenir compte des critères spécifiques à
Bioconductor.

## Références

Le livre [R Packages](http://r-pkgs.had.co.nz/) par Hadley Wickam explique les bonnes pratiques de développement R.
[Le manuel de référence de développement d'extension R](https://cran.r-project.org/doc/manuals/r-release/R-exts.html), une source plus hermétique mais plus complète.
