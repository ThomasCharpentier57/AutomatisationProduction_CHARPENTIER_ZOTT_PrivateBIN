# AutomatisationProduction_CHARPENTIER_ZOTT_PrivateBIN

Thomas CHARPENTIER
Alexis ZOTT

L'action ci-dessous marche de cette façon :

Le nom de notre action github
```
name: GitHub Actions Test
```

On lance on l'action quand il y a un pull ou un push
```
on: 
  push:
  pull_request:
```

Le **job** c'est à dire le déroulement d'action que github vas effectuer a chaque push ou pull
L'action se lance sur ubuntu
```
jobs:
  Explore-GitHub-Actions:
    runs-on: ubuntu-latest
    steps:
```

Utilisation de l'action actions/checkout@v3 pour récupérer le code source sur la branche en cours.
Installation de Composer : Utilisation de l'action php-actions/composer@v6 pour installer Composer.
Exécution des tests avec PHPUnit : Utilisation de l'action php-actions/phpunit@v3 pour exécuter les tests unitaires PHP.
```
steps:
  - uses: actions/checkout@v3
  - name: Installation de Composer
      uses: php-actions/composer@v6
  - name: Essaie des tests
      uses: php-actions/phpunit@v3
```

Les paramètres pour phpunit.
On utilises certaines extensions pour que les tests fonctionnent.
Fichier de configuration pour phpunit
Fichier bootstrap pour éviter les failures (qui ne marche pas)
```
with:
  php_extensions: gd mbstring sqlite3 simplexml xdebug
  configuration: ./phpunit.xml
  bootstrap: ./tst/Bootstrap.php
```

L'action complète : 
```
name: GitHub Actions Test

run-name: ${{ github.actor }} a lancé les tests sur PrivateBin 🚀

on: 
  push:
  pull_request:

jobs:
  Explore-GitHub-Actions:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Installation de Composer
        uses: php-actions/composer@v6
      - name: Essaie des tests
        uses: php-actions/phpunit@v3
        with:
          php_extensions: gd mbstring sqlite3 simplexml xdebug
          configuration: ./phpunit.xml
          bootstrap: ./tst/Bootstrap.php
```
