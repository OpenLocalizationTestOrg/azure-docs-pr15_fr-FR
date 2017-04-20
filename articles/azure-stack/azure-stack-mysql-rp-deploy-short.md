<properties
    pageTitle="Utiliser les bases de données MySQL comme PaaS sur pile Azure | Microsoft Azure"
    description="Comprendre les étapes rapides pour déployer le fournisseur de ressources MySQL et fournir MySQL en tant que service sur Azure pile."
    services="azure-stack"
    documentationCenter=""
    authors="Dumagar"
    manager="bradleyb"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dumagar"/>

# <a name="use-mysql-databases-as-paas-on-azure-stack"></a>Utiliser les bases de données MySQL comme PaaS sur Azure pile

> [AZURE.NOTE] Les informations suivantes s’applique uniquement aux déploiements Azure pile TP1.

Vous pouvez déployer un fournisseur de ressources MySQL sur Azure pile. Une fois que vous déployez le fournisseur de ressources, vous pouvez créer des serveurs MySQL et bases de données via des modèles de déploiement d’Azure le Gestionnaire de ressources et fournir des bases de données MySQL en tant que service. Bases de données MySQL, qui sont communes sur les sites web, prend en charge plusieurs plateformes de site Web. Une fois que vous déployez le fournisseur de ressources, vous pouvez créer des sites Web WordPress à partir de la plateforme Azure Web Apps comme un module complémentaire du service (PaaS) pour Azure pile.

## <a name="quick-steps-to-deploy-the-resource-provider"></a>Étapes à suivre pour déployer le fournisseur de ressources
Utilisez ces étapes si vous connaissez déjà pile Azure. Si vous souhaitez obtenir plus d’informations, suivez les liens de chaque section ou accéder directement à [déployer la carte de fournisseur de ressources de base de données MySQL sur Azure pile du contact](azure-stack-mysql-rp-deploy-long.md).

1.  Vérifiez que [toutes les étapes de configuration effectuées](azure-stack-mysql-rp-deploy-long.md#set-up-steps-before-you-deploy) avant de déployer le fournisseur de ressources :

    - .NET framework 3.5 est déjà configuré dans l’image de Windows Server base. (Si vous avez téléchargé les bits Azure pile après février 23, 2016, vous pouvez ignorer cette étape.)
    - [Une version de PowerShell Azure qui est compatible avec Azure pile est installée](http://aka.ms/azStackPsh).
    - Dans paramètres de sécurité Internet Explorer sur ClientVM, [Internet Explorer sécurité renforcée est désactivée et les cookies sont activés](azure-stack-mysql-rp-deploy-long.md#Turn-off-IE-enhanced-security-and-enable-cookies).

2. [Téléchargez le fichier MySQL ressource fournisseur binaires](http://aka.ms/masmysqlrp) et extraire sur la ClientVM dans votre pile Azure preuve de concept (du contact).

3. [Exécuter bootstrap.cmd et les scripts](azure-stack-mysql-rp-deploy-long.md#Bootstrap-the-resource-provider-deployment-PowerShell-and-Prepare-for-deployment).

    Un ensemble de scripts est regroupé par deux onglets principaux s’ouvre dans l’environnement de l’écriture de script intégré PowerShell (ISE). Exécuter tous les scripts chargés dans l’ordre de gauche à droite dans chaque onglet.

    1. Exécuter des scripts dans l’onglet **préparer** de gauche à droite pour :

        - Créer un certificat générique pour sécuriser les communications entre le fournisseur de ressources et Azure le Gestionnaire de ressources.
        - Accepter les termes du contrat CLUF MySQL et télécharger les fichiers binaires MySQL.
        - Télécharger les certificats et tous les autres objets à un compte de stockage Azure pile.
        - Publier des packages galerie afin que vous pouvez déployer les ressources MySQL via la galerie.

        > [AZURE.IMPORTANT] Si un des scripts se bloque sans raison apparente après avoir effectué votre client Azure Active Directory, vos paramètres de sécurité empêche peut-être une DLL requis pour le déploiement à exécuter. Pour résoudre ce problème, recherchez la Microsoft.AzureStack.Deployment.Telemetry.Dll dans votre dossier de fournisseur de ressources avec le bouton droit dessus et cliquez sur **Propriétés**, puis cochez **Débloquer** dans l’onglet **Général** .

    2. Exécuter des scripts dans l’onglet **déployer** de gauche à droite pour :

        - [Déployer une machine virtuelle ()](azure-stack-mysql-rp-deploy-long.md#Deploy-the-MySQLResource-Provider-VM) hébergeant à la fois votre fournisseur de ressources, les serveurs MySQL et bases de données qui vous serez instanciation. Ce script fait référence à un fichier de paramètres JSON, vous devez mettre à jour avec certaines valeurs avant d’exécuter le script.
        - [Enregistrer un enregistrement DNS local](azure-stack-mysql-rp-deploy-long.md#Update-the-local-DNS) qui seront associées à votre fournisseur de ressources machine virtuelle.
        - [Enregistrer votre fournisseur de ressources](azure-stack-mysql-rp-deploy-long.md#Register-the-MySQL-RP-Resource-Provider) avec le Gestionnaire de ressources Azure local.

        > [AZURE.IMPORTANT] Tous les scripts part du principe que l’image du système d’exploitation base remplit les conditions préalables (.NET 3.5 est installé, JavaScript et des cookies sur le ClientVM et la dernière version d’Azure PowerShell installé). Si vous obtenez des erreurs lors de l’exécution de scripts, vérifiez que vous rempli les conditions préalables.

5. Pour [tester votre nouveau fournisseur de ressources MySQL](/azure-stack-MySql-rp-deploy-long.md#create-your-first-mysql-database-to=test-your-deployment), déployez une base de données MySQL à partir du portail de pile Azure. Cliquez sur **créer** &gt; **personnalisé** &gt; **serveur MySQL et base de données**.

Cela doit obtenir votre fournisseur de ressources MySQL vers le haut et en exécutant dans environ 25 minutes.
