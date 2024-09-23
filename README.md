# AutomatisationProduction_CHARPENTIER_ZOTT_PrivateBIN

Thomas CHARPENTIER
Alexis ZOTT

//Le nom de notre action github
name: GitHub Actions Test


run-name: ${{ github.actor }} a lancé les tests sur PrivateBin 🚀

//On lance on l'action quand on pull ou qu'on push
on: 
  push:
  pull_request:

jobs:                                                               //Le "job" c'est à dire le déroulement d'action que github vas effectuer a chaque push ou pull
  Explore-GitHub-Actions:                                           //L'action se lance sur ubuntu
    runs-on: ubuntu-latest
    steps:                                                          //Les différentes étapes
      - uses: actions/checkout@v3                                   //On se dirigie sur la branch action car l'action n'est pas sur la branch main
      - name: Installation de Composer                              //Nom de l'etape
        uses: php-actions/composer@v6                               //On installe et on lance composer pour pouvoir utiliser php
      - name: Essaie des tests                                      
        uses: php-actions/phpunit@v3                                //On installe et on lance phpunit pour pouvoir lancer les tests
        with:                                                       //Paramètres de phpunit
          php_extensions: gd mbstring sqlite3 simplexml xdebug      //Les extentions pour php pour que les tests fonctionne
          configuration: ./phpunit.xml                              //Fichier de configuration pour phpunit
          bootstrap: ./tst/Bootstrap.php                            //Fichier bootstrap pour éviter les failures (qui ne marche pas)
