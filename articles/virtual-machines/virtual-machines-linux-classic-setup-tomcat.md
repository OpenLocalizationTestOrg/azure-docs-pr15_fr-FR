<properties
    pageTitle="Configurer la Apache Tomcat sur un ordinateur virtuel Linux | Microsoft Azure"
    description="Découvrez comment configurer Apache Tomcat7 à l’aide d’une Azure machine virtuelle () exécutant Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="NingKuang"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="ningk"/>

#<a name="how-to-set-up-tomcat7-on-a-linux-virtual-machine-with-microsoft-azure"></a>Comment configurer Tomcat7 sur une Machine virtuelle Linux avec Microsoft Azure

Apache Tomcat (ou simplement Tomcat, anciennement également Jakarta Tomcat) est un serveur web source ouvert et le conteneur de servlet développé par la Apache logiciel Foundation ASF (). Tomcat mettent en œuvre, le Servlet Java ainsi que les spécifications Java Server Pages (JSP) à partir de SunMicrosystems et fournit un environnement de serveur web HTTP Java intégral dans lesquelles vous pouvez exécuter du code Java. Dans la configuration la plus simple, Tomcat s’exécute dans un processus de système d’exploitation unique. Ce processus s’exécute une machine virtuelle (machine virtuelle Java). Chaque demande HTTP depuis un navigateur pour Tomcat est traité comme un thread séparé dans le processus de Tomcat.  

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Dans ce guide, vous installerez tomcat7 sur une image Linux et déployer dans Microsoft Azure.  

Vous allez apprendre :  

-   Comment créer une machine virtuelle dans Azure.
-   Découvrez comment préparer la machine virtuelle tomcat7.
-   Comment installer tomcat7.

Il est supposé que le lecteur a déjà un abonnement Azure.  Si ce n’est pas le cas, vous pouvez vous inscrire à une version d’évaluation gratuite en [http://azure.microsoft.com](https://azure.microsoft.com/). Si vous avez un abonnement MSDN, voir [des tarifs spéciaux Microsoft Azure : MSDN Code produit du fabricant et des avantages Bizspark](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Pour en savoir plus sur Azure, voir [Nouveautés Azure ?](https://azure.microsoft.com/overview/what-is-azure/).

Cette rubrique part du principe que vous avez base connaissance de tomcat et Linux.  

##<a name="phase-1-create-an-image"></a>Phase 1 : Créer une image
Dans cette phase, vous allez créer une machine virtuelle à l’aide d’une image Linux dans Azure.  

###<a name="step-1-generate-an-ssh-authentication-key"></a>Étape 1 : Générer un code d’authentification SSH
SSH est un outil important pour les administrateurs système. Toutefois, la configuration de sécurité d’accès basée sur un mot de passe déterminé humaines n’est pas une meilleure pratique. Les utilisateurs malveillants peuvent pénétrer dans votre système basé sur un nom d’utilisateur et mot de passe faible.

La bonne nouvelle est qu’il existe un moyen pour avoir ne pas à vous soucier des mots de passe et laissez d’accès à distance ouverte. La méthode se compose de l’authentification avec chiffrement asymétrique. Clé privée de l’utilisateur est celle qui accorde l’authentification. Vous pouvez même le verrouiller le compte d’utilisateur pour ne pas autoriser l’authentification de mot de passe complètement.

Un autre avantage de cette méthode est que vous n’avez pas besoin de différents mots de passe pour vous connecter à différents serveurs. Vous pouvez vous authentifier à l’aide de la clé privée personnelle sur tous les serveurs, ce qui vous évite d’avoir à retenir plusieurs mots de passe.

Il est également possible de se connecter par exiger un mot de passe avec cette méthode.

Suivez ces étapes pour générer la clé d’authentification SSH.

1.  Téléchargez et installez puttygen à l’emplacement suivant : [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.  Exécutez PUTTYGEN. EXE.
3.  Cliquez sur **Générer** pour générer les clés. Dans le processus, vous pouvez augmenter caractère aléatoire en déplaçant la souris sur la zone vide dans la fenêtre.  
![][1]
4.  Après le processus de générer, Puttygen.exe vous montrent votre clé générée. Par exemple :  
![][2]
5.  Sélectionnez et copiez la clé publique sous **clé** et enregistrez-le dans un fichier nommé publicKey.pem. Ne cliquez sur **Enregistrer la clé publique**, car le format de fichier de la clé publique enregistré est la différence entre la clé publique nous voulons.
6.  Cliquez sur **Enregistrer la clé privée** et enregistrez-le dans un fichier nommé privateKey.ppk.

###<a name="step-2-create-the-image-in-the-azure-portal"></a>Étape 2 : Créer l’image dans le portail Azure.
Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Nouveau** dans la barre des tâches pour créer une image en choisissant l’image Linux selon vos besoins. L’exemple suivant utilise l’image 14.04 Ubuntu.
![][3]

Pour **Nom d’hôte** spécifiez le nom de l’URL que les clients Internet et vous utiliserez pour accéder à cette machine virtuelle. Définir la dernière partie du nom DNS, par exemple tomcatdemo, et Azure générera l’URL en tant que tomcatdemo.cloudapp.net.  

Pour **Clé SSH d’authentification**, copiez la valeur de clé à partir du fichier **publicKey.pem** , qui contient la clé publique générée par puttygen.  
![][4]

Configurer d’autres paramètres selon vos besoins, puis cliquez sur Créer.  

##<a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>Phase 2 : Préparer votre machine virtuelle Tomcat7
Dans cette phase, vous configurez un point de terminaison pour le trafic tomcat et puis se connecter à votre nouvelle machine virtuelle.
###<a name="step-1-open-the-http-port-to-allow-web-access"></a>Étape 1 : Ouvrir le port HTTP pour autoriser l’accès web
Points de terminaison dans Azure se composent d’un protocole (TCP ou UDP), ainsi qu’un port publique et privé. Le port privé est le port que le service écoute sur l’ordinateur virtuel. Le port public est le port que le service en nuage Azure écoute avec l’extérieur pour le trafic entrant, basées sur Internet.  

Port TCP 8080 est le numéro de port par défaut sur qui écoute tomcat. Ouvrir ce port avec un point de terminaison Azure permettra de vous et autres clients Internet à accéder à tomcat pages.  

1.  Dans le portail Azure, cliquez sur **Parcourir** -> **Machine virtuelle**, puis cliquez sur l’ordinateur virtuel que vous avez créé.  
![][5]
2.  Pour ajouter un point de terminaison à votre machine virtuelle, cliquez sur la zone de **points de terminaison** .
![][6]
3.  Cliquez sur **Ajouter**.  
    1.  Pour que le **point de terminaison**, tapez un nom pour le point de terminaison de point de terminaison et tapez 80 dans la zone **Port Public**.  

        Si vous affectez 80, n’avez pas besoin d’inclure le numéro de port dans l’URL qui permet d’accéder aux tomcat. Par exemple, http://tomcatdemo.cloudapp.net.    

        Si vous la définissez à une autre valeur, comme 81, vous devez ajouter le numéro de port à l’URL pour accéder aux tomcat. Par exemple, http://tomcatdemo.cloudapp.net:81 /.
    2.  Tapez 8080 Port privé. Par défaut, tomcat écoute sur le port 8080. Si vous avez modifié la valeur par défaut écouter port tomcat, vous devez mettre à jour Port privé pour être qu'identique à la tomcat port d’écoute.  
    ![][7]

4.  Cliquez sur **OK** pour ajouter le point de terminaison à votre machine virtuelle.



###<a name="step-2-connect-to-the-image-you-created"></a>Étape 2 : Se connecter à l’image que vous avez créé.
Vous pouvez choisir n’importe quel outil SSH pour vous connecter à votre machine virtuelle. Dans cet exemple, nous utilisons Putty.  

Tout d’abord, obtenir le nom DNS de votre machine virtuelle à partir du portail Azure. **Cliquez sur Parcourir** -> **machines virtuelles** -> le nom de votre machine virtuelle -> **Propriétés**et consultez ensuite le champ **Nom de domaine** de la vignette de **Propriétés** .  

Obtenir le numéro de port pour identifier les connexions SSH à partir du champ **SSH** . Voici un exemple.  
![][8]

Téléchargez Putty [ici](http://www.putty.org/) .  

Après avoir téléchargé, cliquez sur le fichier exécutable PUTTY. EXE. Configurer les options de base avec le nom d’hôte et le port numéro obtenu à partir des propriétés de votre machine virtuelle. Voici un exemple :  
![][9]

Dans le volet gauche, cliquez sur **la connexion** -> **SSH** -> **Auth** , puis cliquez sur **Parcourir** pour spécifier l’emplacement du fichier **privateKey.ppk** , qui contient la clé privée générée par puttygen à la Phase 1 : créer une Image. Voici un exemple :  
![][10]

Cliquez sur **Ouvrir**. Vous pouvez être prévenu par une zone de message. Si vous avez configuré le nom DNS et numéro de port correctement, cliquez sur **Oui**.
![][11]  


Vous devez voir les rubriques suivantes :  
![][12]

Entrez le nom d’utilisateur spécifié lorsque vous avez créé la machine virtuelle à la Phase 1 : créer une Image. Vous verrez ce qui suit :  
![][13]





##<a name="phase-3-install-software"></a>Phase 3 : Installer le logiciel
Dans cette phase, vous installez l’environnement d’exécution Java, tomcat et d’autres composants tomcat.  

###<a name="java-runtime-environment"></a>Environnement d’exécution Java
Tomcat est écrit en Java. Il existe deux types de Kits de développement Java (kits JDK) (OpenJDK et Oracle JDK), et vous pouvez choisir celui de que votre choix.  

>AZURE. Remarque : Les deux kits JDK apparaître presque le même code pour les classes dans l’API Java, mais le code de la machine virtuelle est en réalité différent. Lorsqu’il s’agit des bibliothèques, OpenJDK a tendance à utiliser des bibliothèques ouvertes tandis que Oracle a tendance à utiliser sont ceux qui fermé. Mais Oracle JDK n’a plus cours et certains fixe bogues et Oracle JDK est plus stable que OpenJDK.

Les commandes suivantes téléchargement les kits JDK différents.  

Ouvrir-jdk   

    sudo apt-get update  
    sudo apt-get install openjdk-7-jre  

Oracle jdk  

-   Pour télécharger le JDK depuis le site Web Oracle :  

        wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  

-   Pour créer un répertoire contenant les fichiers JDK :  

        sudo mkdir /usr/lib/jvm  

-   Pour extraire les fichiers JDK dans le répertoire/usr/bibliothèque/machine virtuelle Java / :  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  

-   Pour définir JDK Oracle par défaut machine virtuelle Java :  

        sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  
        sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

####<a name="test"></a>Test :
Vous pouvez utiliser une commande comme suit pour tester si l’environnement d’exécution Java est correctement installée :  

    java -version  

Si vous avez installé ouvrir jdk, vous devez voir un message comme suit :![][14]

Si vous avez installé oracle jdk, vous devez voir un message comme suit :![][15]

###<a name="tomcat7"></a>Tomcat7
Utilisation de la commande suivante pour installer tomcat7 :  

    sudo apt-get install tomcat7  

Si vous n’utilisez pas tomcat7, utilisez la variation de cette commande appropriée.  

####<a name="test"></a>Test :

Pour vérifier si tomcat7 est installée avec succès, naviguez jusqu’au nom DNS de votre serveur tomcat (http://tomcatexample.cloudapp.net/ est l’URL d’exemple dans cet article). Si vous voyez une page à ce qui suit, vous avez tomcat7 installé correct.
![][16]


###<a name="install-other-tomcat-components"></a>Installer d’autres composants Tomcat
Il existe d’autres composants tomcat facultatif que vous pouvez également installer.  

Utilisez la commande **sudo recherche susceptibles de cache tomcat7** pour afficher tous les composants disponibles. Les commandes suivantes sont des exemples d’installer certaines parties utiles.  

    sudo apt-get install tomcat7-admin      #admin web applications
    sudo apt-get install tomcat7-user         #tools to create user instances  

##<a name="phase-4-configure-tomcat"></a>Phase 4 : Configurer Tomcat
Dans cette phase, vous gérez tomcat.
###<a name="start-and-stop-tomcat7"></a>Démarrer et arrêter tomcat7
Le serveur tomcat7 démarre automatiquement lorsque vous l’installer. Vous pouvez également le démarrer vous-même avec la commande suivante :   

    sudo /etc/init.d/tomcat7 start

Pour arrêter tomcat7 :  

    sudo /etc/init.d/tomcat7 stop

Pour afficher l’état de tomcat7 :  

    sudo /etc/init.d/tomcat7 status

Pour redémarrer tomcat services :  

    sudo /etc/init.d/tomcat7 restart

###<a name="tomcat-administration"></a>Administration Tomcat
Vous pouvez modifier le fichier de configuration Tomcat utilisateur pour configurer vos informations d’identification d’administration avec la commande suivante :  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Voici un exemple :  
![][17]  

>AZURE. Remarque : Créer un mot de passe pour le nom d’utilisateur d’administration.  

Après avoir modifié ce fichier, vous devez redémarrer services tomcat7 avec la commande suivante pour vous assurer que les modifications prennent effet :  

    sudo /etc/init.d/tomcat7 restart  

Ouvrez votre navigateur, puis entrez l’URL **http://<your tomcat server DNS name>/Gestionnaire/html**. Dans l’exemple de cet article, l’URL est http://tomcatexample.cloudapp.net/manager/html.  

Une fois connecté, vous devriez voir doit ressembler à ce qui suit :  
![][18]

##<a name="common-issues"></a>Problèmes courants

###<a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>Ne peut pas accéder à la machine virtuelle avec Tomcat et Moodle à partir d’Internet

-   **Manifestation**  
Tomcat est en cours d’exécution, mais vous ne voyez pas la page par défaut Tomcat avec votre navigateur.
-   **Cas racine possibles**   
    1.  Le port d’écoute tomcat n’est pas identique au Port privé du point de terminaison de votre machine virtuelle pour le trafic tomcat.  

        Vérifiez vos paramètres de point de terminaison ports Public et privé et vérifiez que le Port privé est qu'identique à la tomcat port d’écoute. Voir Phase 1 : Créer une Image pour obtenir des instructions sur la configuration des points de terminaison pour votre machine virtuelle.  

        Pour déterminer le port d’écoute tomcat, ouvrez /etc/httpd/conf/httpd.conf (version finale chapeau rouge) ou /etc/tomcat7/server.xml (Debian version finale). Par défaut, le port d’écoute tomcat est 8080. Voici un exemple :  

            <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"  URIEncoding="UTF-8"            redirectPort="8443" />  

        Si vous utilisez une machine virtuelle tel que Debian ou Ubuntu et que vous souhaitez modifier la valeur par défaut port de Tomcat écouter (par exemple 8081), vous devez également ouvrir le port pour le système d’exploitation. Tout d’abord, ouvrez le profil :  

            sudo vi /etc/default/tomcat7  

        Supprimez les commentaires de la dernière ligne, puis modifier « non » pour « Oui ».  

            AUTHBIND=yes

    2.  Le pare-feu a désactivé le port d’écoute de tomcat.

        Si vous ne voyez que la page par défaut Tomcat à partir de l’hôte local, le problème est très probablement que le port qui est pris en compte les par tomcat est bloqué par le pare-feu. Vous pouvez utiliser l’outil w3m pour rechercher la page web. Les commandes suivantes installer w3m et accédez à la page par défaut Tomcat :  

            sudo yum  install w3m w3m-img
            w3m http://localhost:8080  

-   **Solution**
    1. Si le service tomcat écoute port n’est pas identique au Port privé du point de terminaison pour le trafic vers la machine virtuelle, vous devez modifier le Port privé pour être qu'identique à la tomcat port d’écoute.   

    2.  Si le problème est causé par le pare-feu/iptables, ajoutez les lignes suivantes à /etc/sysconfig/iptables :  

            -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
            -A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

        Notez que la deuxième ligne est nécessaire uniquement pour le trafic https.  

        Vérifiez que cela se trouve au-dessus de toutes les lignes qui seraient globalement limiter l’accès, comme suit :  

            -A INPUT -j REJECT --reject-with icmp-host-prohibited  

        Pour recharger l’iptables, exécutez la commande suivante :  

            service iptables restart  

        Cela a été testé sur CentOS 6.3.

###<a name="permission-denied-when-upload-you-project-files-to-varlibtomcat7webapps"></a>Autorisation refusée quand télécharger project de fichiers à /var/lib/tomcat7/webapps /  

-   **Symptôme**  
Lorsque vous utilisez n’importe quel client SFTP (par exemple, FileZilla) pour vous connecter à votre machine virtuelle et accédez à /var/lib/tomcat7/webapps/pour publier votre site, vous obtenez un message d’erreur similaire à ce qui suit :  

        status: Listing directory /var/lib/tomcat7/webapps
        Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
        Error:  /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
        Error:  File transfer failed

-   **Cas racine possibles** Vous êtes pas autorisé à accéder au dossier /var/lib/tomcat7/webapps.  
-   **Solution**  
Vous avez besoin d’obtenir l’autorisation de compte racine. Vous pouvez remplacer l’appartenance de ce dossier racine au nom d’utilisateur que vous avez utilisé lors de la configuration de l’ordinateur. Voici un exemple avec le nom du compte azureuser :  

        sudo chown azureuser -R /var/lib/tomcat7/webapps

    Utilisez l’option-R pour appliquer les autorisations pour tous les fichiers à l’intérieur d’un répertoire trop.  

    Notez que cette commande fonctionne également pour les répertoires. L’option-R modifie les autorisations pour tous les fichiers et des répertoires à l’intérieur de l’annuaire. Voici un exemple :  

        sudo chown -R username:group directory  

    Cette commande permet de modifier la propriété (utilisateur et groupe) pour tous les fichiers et des répertoires à l’intérieur de répertoire et lui-même.  

    La commande suivante ne modifie l’autorisation d’accès du répertoire dossier qu’et pas les fichiers et dossiers à l’intérieur de l’annuaire seul.  

        sudo chown username:group directory



[1]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png
