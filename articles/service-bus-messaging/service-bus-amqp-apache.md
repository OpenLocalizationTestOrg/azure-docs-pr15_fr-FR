<properties 
    pageTitle="Comment installer Apache Qpid PROTONS-C sur un Linux VM | Microsoft Azure"
    description="Comment créer une machine virtuelle Linux de CentOS grâce aux Machines virtuelles Azure et comment créer et installer la bibliothèque Apache Qpid PROTONS-C."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="install-apache-qpid-proton-c-on-an-azure-linux-vm"></a>Installer Apache Qpid PROTONS-C sur une machine virtuelle Linux Azure

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Cette section montre comment créer une machine virtuelle Linux de CentOS grâce aux Machines virtuelles Azure et comment télécharger, créer et installer la bibliothèque Apache Qpid PROTONS-C ainsi que les liaisons de langage Python et PHP. Après avoir effectué ces étapes, vous ne pourrez pas exécuter les exemples Python et PHP inclus dans ce guide.

La première étape est effectuée à l’aide du [portail classique Azure][]. La capture d’écran suivante montre la création d’une machine virtuelle CentOS nommé « scott centos » :

![PROTONS sur un ordinateur virtuel Linux Azure][0]

Après la mise en service, le portail affiche les éléments suivants :

![PROTONS sur un ordinateur virtuel Linux Azure][1]

Pour ouvrir une session sur l’ordinateur, vous devez connaître le port de point de terminaison de SSH. Vous pouvez obtenir cette valeur à partir du [portail classique Azure][] en sélectionnant la machine virtuelle nouvellement créée et en cliquant sur l’onglet **points de terminaison** . La capture d’écran suivante montre que le port SSH public pour cet ordinateur est 57146.

![PROTONS sur un ordinateur virtuel Linux Azure][2]

Vous pouvez désormais être connecté à l’ordinateur à l’aide de SSH. Cet exemple utilise l’outil mastic, comme dans la capture d’écran suivante :

![PROTONS sur un ordinateur virtuel Linux Azure][3]

Pour les applications Python et PHP, cet exemple utilise les bibliothèques de client PROTONS auprès d’Apache. Ces bibliothèques peuvent être téléchargés à partir de [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). Le fichier Lisez-moi dans le package de distribution décrit les étapes requises pour installer les dépendances et générer PROTONS. Voici un résumé des étapes :

1.  Modifier le fichier de configuration de l’yum (/ etc/yum.conf) et commentez l’exclusion les mises à jour aux en-têtes noyau (\# exclure = noyau\*). Cela est nécessaire pour installer le compilateur gcc.

2.  Installer les packages requis :

    ```
    # required dependencies 
    yum install gcc cmake libuuid-devel
    
    # dependencies needed for ssl support
    yum install openssl-devel
    
    # dependencies needed for bindings
    yum install swig python-devel ruby-devel php-devel java-1.6.0-openjdk
    
    # dependencies needed for python docs
    yum install epydoc
    ```

1.  Télécharger la bibliothèque PROTONS :

    ```
    [azureuser@this-user ~]$ wget http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
    --2016-04-17 14:45:03--  http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
    Resolving apache.panu.it (apache.panu.it)... 81.208.22.71
    Connecting to apache.panu.it (apache.panu.it)|81.208.22.71|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 868226 (848K) [application/x-gzip]
    Saving to: ‘qpid-proton-0.9.tar.gz’
    
    qpid-proton-0.9.tar.gz                               
    
    100%[====================================================================================================================>] 847.88K   102KB/s    in 8.4s    
    
    2016-04-17 14:45:12 (101 KB/s) - ‘qpid-proton-0.9.tar.gz’ saved [868226/868226]
    ```

1.  Extraire le code PROTONS l’archive de distribution :

    ```
    tar xvfz qpid-proton-0.9.tar.gz
    ```

1.  Créer et installez le code à l’aide de la procédure suivante, extraite du fichier Lisez-moi :

    ```
    From the directory where you found this README file:    
    
    mkdir build cd build
            
    # Set the install prefix. You may need to adjust depending on your      
    # system.       
    cmake -DCMAKE\_INSTALL\_PREFIX=/usr ..
            
    # Omit the docs target if you do not wish to build or install       
    # documentation.        
    make all docs
            
    # Note that this step will require root privileges.     
    make install
    ```

Après avoir effectué ces étapes, PROTONS est installé sur l’ordinateur et prête à être utilisée.

## <a name="next-steps"></a>Étapes suivantes

Prêt pour en savoir plus ? Visitez le lien suivant :

- [Vue d’ensemble du service Bus AMQP][]

[Vue d’ensemble du service Bus AMQP]: service-bus-amqp-overview.md
[0]: ./media/service-bus-amqp-apache/amqp-apache-1.png
[1]: ./media/service-bus-amqp-apache/amqp-apache-2.png
[2]: ./media/service-bus-amqp-apache/amqp-apache-3.png
[3]: ./media/service-bus-amqp-apache/amqp-apache-4.png

[Portail classique Azure]: http://manage.windowsazure.com


