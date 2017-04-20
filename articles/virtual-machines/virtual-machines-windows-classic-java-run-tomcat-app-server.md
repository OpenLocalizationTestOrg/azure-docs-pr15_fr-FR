<properties
    pageTitle="Tomcat sur une machine virtuelle | Microsoft Azure"
    description="Ce didacticiel utilise les ressources créées avec le modèle de déploiement classique et montre comment créer une machine virtuelle Windows et configurez-le pour lancer Apache Tomcat application server."
    services="virtual-machines-windows"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.workload="web"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>Comment faire pour exécuter un serveur d’applications Java sur une machine virtuelle créée avec le modèle de déploiement classique

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Avec Azure, vous pouvez utiliser une machine virtuelle pour fournir des fonctionnalités du serveur. Par exemple, une machine virtuelle s’exécutant sur Azure peut être configurée pour héberger un serveur d’applications Java, tels que Apache Tomcat. Après avoir terminé ce guide, vous devez comprendre comment créer une machine virtuelle s’exécutant sur Azure et configurez-le pour exécuter un serveur d’applications Java.

Vous allez apprendre :

* Comment créer un ordinateur virtuel qui contient un Kit de développement Java (JDK) déjà installé.
* Comment se connecter à distance à votre machine virtuelle.
* Comment installer un serveur d’applications Java sur votre ordinateur virtuel.
* Comment créer un point de terminaison de votre machine virtuelle.
* Comment ouvrir un port dans le pare-feu pour votre serveur d’applications.

Aux fins de ce didacticiel, un serveur d’applications Apache Tomcat sera installé sur une machine virtuelle. L’installation terminée provoquera une installation Tomcat comme suit.

![Machine virtuelle s’exécutant Apache Tomcat][virtual_machine_tomcat]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Pour créer une machine virtuelle

1. Connectez-vous au [portail classique Azure](https://manage.windowsazure.com).
2. Cliquez sur **Nouveau**et cliquez sur **Calculer**, cliquez sur **machine virtuelle**, puis cliquez sur **à partir de la galerie**.
3. Dans la boîte de dialogue **Sélectionnez image machine virtuelle** , sélectionnez **JDK 7 Windows Server 2012**.
Notez que **JDK 6 Windows Server 2012** est disponible que si vous avez des applications héritées qui ne sont pas prêtes à être exécuté dans JDK 7.
4. Cliquez sur **suivant**.
5. Dans la boîte de dialogue **configuration de machine virtuelle** :
    1. Spécifiez un nom pour la machine virtuelle.
    2. Spécifiez la taille à utiliser pour la machine virtuelle.
    3. Entrez un nom pour l’administrateur dans le champ **Nom d’utilisateur** . N’oubliez pas ce nom et le mot de passe que vous entrez ensuite, vous les utiliserez lorsque vous vous connectez à distance à la machine virtuelle.
    4. Entrez un mot de passe dans le champ **nouveau mot de passe** , puis entrez-le dans le champ **Confirmer** . Il s’agit du mot de passe de compte d’administrateur.
    5. Cliquez sur **suivant**.
6. Dans la boîte de dialogue **configuration de machine virtuelle** suivante :
    1. Pour le **service Cloud**, utilisez la valeur par défaut **créer un nouveau service cloud**.
    2. La valeur nom du **Cloud service DNS** doit être unique au sein de cloudapp.net. Si nécessaire, modifier cette valeur pour ce Azure indique qu’il est unique.
    2. Spécifier une région, groupe affinité ou réseau virtuel. Aux fins de ce didacticiel, spécifiez une région, par exemple **US ouest**.
    2. Pour le **Compte de stockage**, sélectionnez **utiliser un compte de stockage générée automatiquement**.
    3. Pour **Définir la disponibilité**, sélectionnez **(aucun)**.
    4. Cliquez sur **suivant**.
7. Dans la dernière boîte de dialogue **configuration de machine virtuelle** :
    1. Accepter les entrées de point de terminaison par défaut.
    2. Cliquez sur **terminé**.

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>Pour vous connecter à distance à votre machine virtuelle

1. Connectez-vous au [portail classique Azure](https://manage.windowsazure.com).
2. Cliquez sur **machines virtuelles**.
3. Cliquez sur le nom de la machine virtuelle à se connecter à.
4. Une fois que la machine virtuelle a commencé, un menu contextuel dans la partie inférieure de la page autorise les connexions.
5. Cliquez sur **se connecter**.
6. Répondez aux invites nécessaire pour se connecter à la machine virtuelle. Cela doit entraîner l’enregistrement ou l’ouverture du fichier .rdp qui contient des informations de connexion. Vous devrez peut-être copier l’url : port en tant que la dernière partie de la première ligne du fichier .rdp et collez-le dans une application de se connecter à distance.

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>Installer un serveur d’applications Java sur votre machine virtuelle

Vous pouvez copier un serveur d’applications Java à votre machine virtuelle, ou vous pouvez installer un serveur d’applications Java via un programme d’installation.

Aux fins de ce didacticiel, Tomcat sera installé.

1. Lorsque vous êtes connecté à votre machine virtuelle, ouvrez une session de navigateur à [Apache Tomcat](http://tomcat.apache.org/download-70.cgi).
2. Double-cliquez sur le lien pour **32 bits/64 bits Windows Installer Service**. À l’aide de cette technique, Tomcat sera installé comme un service Windows.
3. Lorsque vous y êtes invité, choisissez d’exécuter le programme d’installation.
4. Dans l’Assistant **Configuration de Tomcat Apache** , suivez les invites pour installer Tomcat. Aux fins de ce didacticiel, accepter les paramètres par défaut est prêt. Lorsque vous atteignez la boîte de dialogue **fin de l’Assistant de configuration Apache Tomcat** , vous pouvez éventuellement vérifier **Exécuter Apache Tomcat** pour que Tomcat Démarrer maintenant. Cliquez sur **Terminer** pour terminer le processus de configuration Tomcat.

## <a name="to-start-tomcat"></a>Pour démarrer Tomcat
Si vous n’avez pas choisi d’exécuter Tomcat dans la boîte de dialogue **fin de l’Assistant de configuration Apache Tomcat** , commencez en ouvrant une invite de commande sur votre ordinateur virtuel et en exécutant **Tomcat7 démarre**.

Vous devez maintenant voir Tomcat en cours d’exécution si vous exécutez l’Explorateur de la machine virtuelle et ouvrez <http://localhost : 8080>.

Pour voir Tomcat en cours d’exécution à partir d’ordinateurs externe, vous devez créer un point de terminaison et ouvrir un port.

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>Pour créer un point de terminaison de votre machine virtuelle
1. Connectez-vous au [portail classique Azure](https://manage.windowsazure.com).
2. Cliquez sur **machines virtuelles**.
3. Cliquez sur le nom de la machine virtuelle exécutant votre serveur d’applications Java.
4. Cliquez sur **points de terminaison**.
5. Cliquez sur **Ajouter**.
6. Dans la boîte de dialogue **Ajout de point de terminaison** Vérifiez **point de terminaison ajouter autonome** est sélectionnée, puis cliquez sur **suivant**.
7. Dans la boîte de dialogue **nouveaux détails de point de terminaison** :
    1. Spécifiez un nom pour le point de terminaison ; par exemple, **HttpIn**.
    2. Spécifiez **TCP** pour le protocole.
    3. Spécifiez **80** pour le port public.
    4. Spécifiez **8080** pour le port privé.
    5. Cliquez sur le bouton **Terminer** pour fermer la boîte de dialogue. Votre point de terminaison maintenant est créée.

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>Pour ouvrir un port dans le pare-feu de votre machine virtuelle
1. Se connecter à votre machine virtuelle.
2. Cliquez sur **Démarrer de Windows**.
3. Cliquez sur **le panneau de configuration**.
4. Cliquez sur **système et sécurité**, cliquez sur **Pare-feu Windows**, puis cliquez sur **Paramètres avancés**.
5. Cliquez sur **Règles de trafic entrant**, puis cliquez sur **Nouvelle règle**.
 ![Nouvelle règle de trafic entrant][NewIBRule]
6. Pour le **Type de règle**, sélectionnez **Port**, puis cliquez sur **suivant**.
 ![Nouveau port de règle de trafic entrant][NewRulePort]
7. Dans l’écran **Ports et protocoles** , sélectionnez **TCP**, spécifiez le **port local spécifique** **8080** , puis sur **suivant**.
 ![Nouvelle règle de trafic entrant][NewRuleProtocol]
8. Dans l’écran **Action** , sélectionnez **Autoriser la connexion**, puis cliquez sur **suivant**.
 ![Nouvelle action de règle de trafic entrant][NewRuleAction]
9. Dans l’écran **profil** , vérifiez que **domaine** **privé**et **Public** sont sélectionnés, puis cliquez sur **suivant**.
 ![Nouveau profil de règle de trafic entrant][NewRuleProfile]
10. Dans l’écran **nom** , attribuez un nom à la règle, par exemple **HttpIn** (le nom de la règle n’est pas nécessaire pour faire correspondre le nom du point de terminaison, cependant), puis sur **Terminer**.  
 ![Nouveau nom de la règle de trafic entrant][NewRuleName]

À ce stade, votre site Web Tomcat doit être visible à partir d’un navigateur externe en utilisant une URL du formulaire * *http://*votre\_DNS\_nom*. cloudapp.net**où ** *votre\_DNS\_nom*** est le nom DNS que vous avez spécifié lors de la création de la machine virtuelle.

## <a name="application-lifecycle-considerations"></a>Considérations sur l’application du cycle de vie
* Faire créer votre propre archive d’application web (WAR) et ajouter au dossier **applications Web** . Par exemple, créez un projet web dynamique Java Service Page (JSP) base et exporter sous la forme d’un fichier WAR, copiez la guerre dans le dossier de **WebApp** Apache Tomcat sur l’ordinateur virtuel, puis exécutez-le dans un navigateur.
* Par défaut lorsque le service Tomcat est installé, il est configuré pour démarrer manuellement. Vous pouvez basculer qu’il démarre automatiquement à l’aide du composant logiciel enfichable Services. Démarrez le composant logiciel enfichable Services en cliquant sur **Démarrer de Windows**, sur **Outils d’administration**, puis sur **Services**. Double-cliquez sur le service **Apache Tomcat** et définir le **type de démarrage** sur **automatique**.

    ![Définition d’un service pour un démarrage automatique][service_automatic_startup]

    L’avantage de disposer automatiquement démarrer Tomcat est qu’il va démarrer si la machine virtuelle est redémarrée (par exemple, après installent des mises à jour logicielles nécessitant un redémarrage).

## <a name="next-steps"></a>Étapes suivantes
Découvrez d’autres services (par exemple, le stockage Azure, un service bus et base de données SQL) que vous souhaitez inclure avec vos applications Java en affichant les informations disponibles à partir du [Centre de développement Java](https://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProfile.png
