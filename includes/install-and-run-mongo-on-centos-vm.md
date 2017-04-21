Suivez ces étapes pour installer et exécuter MongoDB sur une machine virtuelle Linux CentOS en cours d’exécution.

> [AZURE.WARNING] Fonctionnalités de sécurité MongoDB, telles que l’authentification et lien d’adresse IP, ne sont pas activées par défaut. Fonctionnalités de sécurité doivent être activées avant de déployer MongoDB dans un environnement de production.  Pour plus d’informations, voir [sécurité et authentification](http://www.mongodb.org/display/DOCS/Security+and+Authentication) .

1. Configurer le système de gestion de Package (YUM) afin que vous pouvez installer MongoDB. Créer un fichier */etc/yum.repos.d/10gen.repo* pour conserver les informations concernant votre référentiel et ajoutez les éléments suivants :

        [10gen]
        name=10gen Repository
        baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64
        gpgcheck=0
        enabled=1

2. Enregistrer le fichier mis en pension, puis exécutez la commande suivante pour mettre à jour de la base de données du package local :

        $ sudo yum update

3. Pour installer le package, exécutez la commande suivante pour installer la dernière version disponible de MongoDB et les outils associés :

        $ sudo yum install mongo-10gen mongo-10gen-server

    Patientez pendant le MongoDB télécharge et installe.

4. Créer un répertoire de données. Par défaut MongoDB stocke les données dans le répertoire */data/db* , mais vous devez créer ce répertoire. Pour créer, exécuter :

        $ sudo mkdir -p /srv/datadrive/data
        $ sudo chown `id -u` /srv/datadrive/data

    Pour plus d’informations sur l’installation MongoDB sur Linux, voir [Démarrage rapide Unix][QuickstartUnix].

5. Pour démarrer la base de données, exécutez :

        $ mongod --dbpath /srv/datadrive/data --logpath /srv/datadrive/data/mongod.log

    Tous les messages de journal seront dirigés vers le fichier */srv/datadrive/data/mongod.log* comme serveur MongoDB démarre et pré-alloue fichiers journaux. Il peut prendre plusieurs minutes MongoDB allouer les fichiers journaux et commencer à l’écoute de connexions.

6. Pour démarrer l’environnement d’administration MongoDB, ouvrez une fenêtre séparée SSH ou PuTTY et exécutez :

        $ mongo
        > db.foo.save ( { a:1 } )
        > db.foo.find()
        { _id : ..., a : 1 }
        > show dbs  
        ...
        > show collections  
        ...  
        > help  

    La base de données est créée à l’insertion.

7. Une fois MongoDB est installé, vous devez configurer un point de terminaison afin que MongoDB accessible à distance. Dans le portail de gestion, cliquez sur **Machines virtuelles**, puis cliquez sur le nom de votre nouvelle machine virtuelle, puis cliquez sur les **points de terminaison**.
    
    ![Points de terminaison][Image7]

8. Cliquez sur **Ajouter un point de terminaison** en bas de la page.
    
    ![Points de terminaison][Image8]

9. Ajouter un point de terminaison avec les paramètres suivants :

 - **Nom**: Mongo
 - **Protocole**: TCP
 - **Port Public**: 27017
 - **Port privé**: 27017
 
 Cela permettra MongoDB soit accessible à distance.



[QuickStartUnix]: http://www.mongodb.org/display/DOCS/Quickstart+Unix


[Image7]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint2.png
