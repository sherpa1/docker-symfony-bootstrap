# Symfony Docker Bootstrap

## Initialisation

`docker compose up`

A la première exécution : création de l'image à partir du fichier Dockerfile et téléchargement des images utilisées par le projet (soit, MySQL, PhpMyAdmin, PHP 7.4 FPM Alpine).

## Lancement des services Docker

`docker-compose up -d`

## Fermeture des services Docker

`docker-compose stop`

## Génération d'un nouveau projet Symfony à l'intérieur du container symfony_php-fpm

Si aucun projet Symfony n'est encore présent. Il est nécessaire d'en générer un à l'aide du CLI de Symfony. Ensuite, lorsque le projet a été généré, il est possible d'adapter le fichier docker-compose.yml pour que le service "symfony_php-fpm" démarre directement sur ce projet.

### Génération d'un nouveau projet Symfony

- Entrer dans le bash du container symfony_php-fpm :

`docker exec -ti symfony_php-fpm bash`

- puis

```
git config --global user.email "john@doe.com"
git config --global user.name "johndoe"
symfony new nom-du-projet --full
```

Pour sortir du bash du container Docker : `exit`.

Cette commande (à adapter) va créer un projet Symfony à la racine du répertoire "/var/www/html" du container "symfony_php-fpm" répliqué à la racine du répertoire local courant.

Les modifications effectuées dans les fichiers locaux sont répliqués dans le container.

### Adaptations après génération d'un nouveau projet Symfony

Le nom du répertoire (ici "projet") doit également être utilisé dans la commande du service "symfony_php-fpm" du fichier "docker-compose.yml" afin de démarrer le serveur et pointer vers ce répertoire.

Il faut modifier le fichier .env du projet Symfony afin de communiquer avec la base de données MySQL du service "symfony_mysql" (ici, /projet/.env).

```
DATABASE_URL="mysql://root:root@mysql:3306/test?serverVersion=5.7"
```

Dans cet exemple "root" est le nom de l'utilisateur root et "test" est son mot de passe. Ces valeurs sont à adapter.

### Adaptation du fichier docker-compose.yml

Lorsque le projet Symfony a été créé et que son fichier .env a été adapté

## Exécution de commandes de Symfony CLI

- Entrer dans le bash du container symfony_php-fpm :

`docker exec -ti symfony_php-fpm bash`

- puis exécuter la commande souhaitée, ex :

- `php bin/console make:entity`
- `php bin/console make:migration`
- `php bin/console doctrine:migrations:migrate`
- ...

### Exécution d'une commande dans le container sans entrer dans le bash du container

`docker run <nom-du-container> bin/console make:entity`

## Architecture

- Le projet Symfony se situe dans le dossier projet

- Le fichier Dockerfile permet de générer une image Docker sur-mesure prête à l'emploi pour Symfony avec PDO, CLI Symfony et Git. Il est possible d'ajouter d'autres dépendances.

- Le fichier docker-compose.yml permet de faire collaborer les 3 services suivants :

  - base de données MySQL
  - serveur PHP-FPM
  - PhpMyAdmin

- La commande du service symfony_php `symfony server:start --dir=projet` permet de démarrer le serveur symfony en pointant dans le dossier racine du projet symfony (nommé "projet")

- Les variables d'environnement sont définies dans le fichier /.env (copier, adapter et renommer le fichier .env_model)

- Le projet Symfony est accessible à l'URL : http://localhost:5000

- PhpMyAdmin est accessible à l'URL : http://localhost:8080

---

**Alexandre Leroux**

- _Mail_ : alex@sherpa.one
- _Github_ : sherpa1
- _Twitter_ : @_sherpa_
- _Discord_ : sherpa#3890

_Enseignant vacataire à l'Université de Lorraine_

- IUT Nancy-Charlemagne (LP Ciasie)

- Institut des Sciences du Digital, Management & Cognition (Masters Sciences Cognitives)
