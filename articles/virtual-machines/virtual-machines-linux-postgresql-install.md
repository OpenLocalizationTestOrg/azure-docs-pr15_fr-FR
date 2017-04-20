<properties
    pageTitle="Configurer la PostgreSQL sur un Linux VM | Microsoft Azure"
    description="Découvrez comment installer et configurer PostgreSQL sur une machine virtuelle Linux dans Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="02/01/2016"
    ms.author="mingzhan"/>


# <a name="install-and-configure-postgresql-on-azure"></a>Installer et configurer PostgreSQL sur Azure

PostgreSQL est une base de données open source avancée similaire aux Oracle et DB2. Il inclut des fonctionnalités adaptées à l’entreprise tels que le contrôle de multi-version concurrence conformité complète acide et traitement des transactions fiable. Il prend également en charge les normes telles que SQL ANSI et SQL/MED (y compris wrappers données étrangère pour Oracle, MySQL, MongoDB et bien d’autres). Il est très extensible avec prise en charge de plus de 12 langues procédurales, index GIN et idée, prise en charge des données spatiales et plusieurs fonctionnalités NoSQL JSON ou applications basées sur une valeur-clé.

Dans cet article, vous allez apprendre à installer et configurer PostgreSQL sur une machine virtuelle Azure exécutant Linux.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="install-postgresql"></a>Installer PostgreSQL

> [AZURE.NOTE] Vous devez déjà une machine virtuelle Azure exécutant Linux afin de pouvoir suivre ce didacticiel. Pour créer et configurer un VM Linux avant de poursuivre, consultez le [didacticiel machine virtuelle Linux Azure](virtual-machines-linux-quick-create-cli.md).

Dans ce cas, utilisez le port 1999 comme port PostgreSQL.  

Se connecter à la machine virtuelle que vous avez créé via PuTTY Linux. Si c’est la première fois que vous utilisez une machine virtuelle Linux Azure, Découvrez [comment utiliser SSH avec Linux sur Azure](virtual-machines-linux-mac-create-ssh-keys.md) pour apprendre à utiliser PuTTY pour vous connecter à une VM Linux.

1. Exécutez la commande suivante pour passer à la racine (administrateur) :

        # sudo su -

2. Certains répartitions ont des dépendances que vous devez installer avant d’installer PostgreSQL. Rechercher votre distro dans cette liste et exécutez la commande appropriée :

    - Linux base chapeau rouge :

            # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

    - Debian Linux de base :

            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  

    - SUSE Linux :

            # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

3. Télécharger PostgreSQL dans le répertoire racine et puis décompressez le package :

        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

        # tar jxvf  postgresql-9.3.5.tar.bz2

    Ce qui précède est un exemple. Vous pouvez trouver l’adresse de téléchargement plus détaillée dans l' [Index de/pub/source /](https://ftp.postgresql.org/pub/source/).

4. Pour démarrer la génération, exécutez ces commandes :

        # cd postgresql-9.3.5

        # ./configure --prefix=/opt/postgresql-9.3.5

5. Si vous souhaitez générer tout ce qui peut être créée, y compris la documentation (HTML et les pages man) et les modules complémentaires (cotisation), exécutez la commande suivante à la place :

        # gmake install-world

    Vous devez recevoir le message de confirmation suivant :

        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Configurer PostgreSQL

1. (Facultatif) Créer un lien symbolic pour raccourcir la référence PostgreSQL pour ne pas inclure le numéro de version :

        # ln -s /opt/pgsql9.3.5 /opt/pgsql

2. Créez un répertoire pour la base de données :

        # mkdir -p /opt/pgsql_data

3. Créez un utilisateur non racine et modifier le profil de cet utilisateur. Ensuite, basculez vers ce nouvel utilisateur (appelé *postgres* dans notre exemple) :

        # useradd postgres

        # chown -R postgres.postgres /opt/pgsql_data

        # su - postgres

   > [AZURE.NOTE] Pour des raisons de sécurité, PostgreSQL utilise un utilisateur non racine pour initialisation, démarrer ou arrêter la base de données.


4. Modifier le fichier *bash_profile* en entrant les commandes ci-dessous. Ces lignes sont ajoutés à la fin du fichier *bash_profile* :

        cat >> ~/.bash_profile <<EOF
        export PGPORT=1999
        export PGDATA=/opt/pgsql_data
        export LANG=en_US.utf8
        export PGHOME=/opt/pgsql
        export PATH=\$PATH:\$PGHOME/bin
        export MANPATH=\$MANPATH:\$PGHOME/share/man
        export DATA=`date +"%Y%m%d%H%M"`
        export PGUSER=postgres
        alias rm='rm -i'
        alias ll='ls -lh'
        EOF

5. Exécutez le fichier *bash_profile* :

        $ source .bash_profile

6. Valider votre installation à l’aide de la commande suivante :

        $ which psql

    Si votre installation est terminée, vous verrez la réponse suivante :

        /opt/pgsql/bin/psql

7. Vous pouvez également vérifier la version PostgreSQL :

        $ psql -V

8. Initialisation de la base de données :

        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W

    Vous devez recevoir le résultat suivant :

![image](./media/virtual-machines-linux-postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Configurer la PostgreSQL

<!--    [postgres@ test ~]$ exit -->

Exécutez les commandes suivantes :

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Modifier les deux variables dans le fichier /etc/init.d/postgresql. Le préfixe est défini sur le chemin d’accès de l’installation de PostgreSQL : **/opt/pgsql**. PGDATA est défini sur le chemin d’accès de stockage de données de PostgreSQL : **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/virtual-machines-linux-postgresql-install/no2.png)

Modifier le fichier pour le rendre exécutable :

    # chmod +x /etc/init.d/postgresql

Démarrez PostgreSQL :

    # /etc/init.d/postgresql start

Vérifiez si le point de terminaison de PostgreSQL est activé :

    # netstat -tunlp|grep 1999

Vous devriez voir le résultat suivant :

![image](./media/virtual-machines-linux-postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Se connecter à la base de données Postgres

Basculer vers l’utilisateur postgres une nouvelle fois :

    # su - postgres

Créer une base de données Postgres :

    $ createdb events

Se connecter à la base de données événements que vous venez de créer :

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Créer et supprimer un tableau Postgres

À présent que vous êtes connecté à la base de données, vous pouvez créer des tables qu’il contient.

Par exemple, créer une nouvelle table Postgres exemple à l’aide de la commande suivante :

    CREATE TABLE potluck (name VARCHAR(20), food VARCHAR(30),   confirmed CHAR(1), signup_date DATE);

Vous avez maintenant configuré un tableau à quatre colonnes avec les restrictions de noms de colonne suivants :

1. La colonne « nom » a été limitée par la commande VARCHAR pour contenir 20 caractères.
2. La colonne « alimentation » indique aliment chaque personne s’affiche. VARCHAR les limites de ce texte pour être sous 30 caractères.
3. La colonne « confirmée » enregistrements si la personne a RSVP'd à la buffet. Les valeurs possibles sont « Y » et « N ».
4. La colonne « date » affiche lors de leur inscription de l’événement. Postgres nécessite que les dates soient écrits en tant qu’aaaa-mm-jj.

Si votre tableau a été créé, vous devriez voir les éléments suivants :

![image](./media/virtual-machines-linux-postgresql-install/no4.png)

Vous pouvez également vérifier la structure des tables à l’aide de la commande suivante :

![image](./media/virtual-machines-linux-postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Ajouter des données à une table

Tout d’abord, insérer des informations dans une ligne :

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Vous devez voir ce résultat :

![image](./media/virtual-machines-linux-postgresql-install/no6.png)

Vous pouvez ajouter quelques autres personnes à la table. Voici certaines options, ou vous pouvez créer vos propres :

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Afficher les tables

Utiliser la commande suivante pour afficher une table :

    select * from potluck;

Le résultat est :

![image](./media/virtual-machines-linux-postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Supprimer des données dans un tableau

Pour supprimer des données dans un tableau, utilisez la commande suivante :

    delete from potluck where name=’John’;

Cela supprime toutes les informations dans la ligne « John ». Le résultat est :

![image](./media/virtual-machines-linux-postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Mettre à jour les données dans un tableau

Utilisez la commande suivante pour mettre à jour les données dans un tableau. Dans cet exemple, Sandy a confirmé qu’elle participe, et nous changeront son RSVP à partir de « N » à « Y » :

    UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


##<a name="get-more-information-about-postgresql"></a>Obtenir plus d’informations sur PostgreSQL
Maintenant que vous avez terminé l’installation de PostgreSQL dans une machine virtuelle Linux Azure, vous pouvez l’apprécierez dans Azure. Pour en savoir plus sur PostgreSQL, visitez le [site Web PostgreSQL](http://www.postgresql.org/).
