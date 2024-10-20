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

Utilisation de l'action actions/checkout@v3 pour récupérer le code sur la branche en cours.

```
  - uses: actions/checkout@v3
```

Installation de Composer : Utilisation de l'action php-actions/composer@v6 pour installer Composer.

```
  - name: Installation de Composer
      uses: php-actions/composer@v6
```

Exécution des tests avec PHPUnit : Utilisation de l'action php-actions/phpunit@v3 pour exécuter les tests unitaires PHP.

On renseigne certains paramètres : 
- le fichier de configuration pour phpunit
- Fichier bootstrap pour éviter les failures (qui ne marche pas)
- La version de phpunit
- La version de php
- Les extensions utilisés dans le projet

On définie aussi l'environement XDEBUG pour définir par la suite le coverage.
```
- name: Essaie des tests
        uses: php-actions/phpunit@v3
        env:
          XDEBUG_MODE: coverage
        with:
          php_extensions: gd xdebug
          configuration: ./phpunit.xml
          bootstrap: ./tst/Bootstrap.php
          version: 9
          php_version: 8.1
```
On s'occupe ensuite du rapport de Code Coverage. On utilise l'action ci dessous.
On renseigne différents paramètres : 
- le format
- l'emplacement du rapport

On a du modifier la configuration de phpunit pour modifier le type du rapport pour le passer en cobertura étant donné que l'action github ne supporte que ce format là.

```
- name: Code Coverage Report
        uses: irongut/CodeCoverageSummary@v1.3.0
        with:
          filename: log/coverage-cobertura.xml
          badge: true
          fail_below_min: true
          format: markdown
          hide_branch_rate: false
          hide_complexity: true
          indicators: true
          output: both
        continue-on-error: true
```

Ensuite on lance l'action code sniffer afin de vérifier que le code conçu respect bien toute les normes de codages.
On renseigne :
- la version de php
- le path des fichiers qu'on veut vérifier
- la norme qu'on souhaite utiliser

```
 - name: PHP Code Sniffer
        uses: php-actions/phpcs@v1
        with:
          php_version: 8.1
          path: lib/
          standard: PSR12
        continue-on-error: true
```

Puis on utilise l'action Mess Detector qui vérifie que le code soit "comprehensible" et qu'il soit propre.
On renseigne :
- la version de php
- le path des fichiers à vérifier
- comment on veut voir le rapport

```
 - name: PHP Mess Detector
        uses: php-actions/phpmd@v1
        with:
          php_version: 8.1
          path: lib/
          output: text
          ruleset: cleancode
        continue-on-error: true
```

Enfin on utilise l'action PHP Stan pour vérifier qu'il n'y ait pas de potentiels bugs ou d'incohérence. 
On renseigne : 
- le path des fichiers qu'on souhaite analyser
- la sensibilité de vérification des fichiers.

```
 - uses: php-actions/phpstan@v3
        with:
          path: lib/
          level: 9
        continue-on-error: true
```

Ensuite nous avons créé ensuite une 2ème action github. Elle permet de déployer les fichiers sur un serveur FTP. On l'a créé indépendement pour ne pas à avoir à utiliser le composer étant donné que le fichier vendor est déjà présent sur le serveur FTP.
Pour cela,  il faut :
- On fait tout de même un checkout pour être sur la bonne branche. 
- Créer des variables secrètes sur Github afin de ne pas dévoiler le mot de passe, l'url et le login au serveur FTP.
- On indique où on souhaite stocker sur le serveur FTP les fichiers
- On renseigne les paramètres de connexion au serveur.
- Et on exclue le fichier vendor dans le cas où on utilise composer ou si le fichier vendor est aussi pousser sur le dépôt. 

```
- uses: actions/checkout@v3

- name: 📂 Deploiement sur le FTP
  uses: SamKirkland/FTP-Deploy-Action@v4.3.5
  with:
    server-dir: 'www/'
    server: ${{ secrets.URL }}
    username: ${{ secrets.LOGIN }}
    password: ${{ secrets.MDP_FTP }}
    exclude: | 
      **/vendor/**
    continue-on-error: true
```

On utilise sur l'ensemble de nos actions des ```continue-on-error: true ``` afin que l'ensemble des actions puissent se dérouler même si il y a une interruption.