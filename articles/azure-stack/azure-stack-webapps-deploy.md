<properties
    pageTitle="Ajouter un fournisseur de ressources des applications Web à pile Azure | Microsoft Azure"
    description="Instructions détaillées pour le déploiement des applications Web dans la pile d’Azure"
    services="azure-stack"
    documentationCenter=""
    authors="ccompy, apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>

# <a name="add-a-web-apps-resource-provider-to-azure-stack"></a>Ajouter un fournisseur de ressources des applications Web à pile d’Azure

> [AZURE.NOTE] Les informations suivantes s’applique uniquement aux déploiements Azure pile TP1.

Ajout d’un fournisseur de ressources d’applications Web à Azure pile comporte sept étapes :

1.  Télécharger les composants requis.
2.  Créer des certificats devant être utilisé par Azure pile Web Apps.
3.  Utiliser le programme d’installation pour télécharger, partage et installer les applications Web Azure pile. 
4.  Valider l’Installation des applications Web.
5.  Créer des enregistrements DNS pour les programmes d’équilibrage de charge Management Server frontal.
6.  Enregistrer le fournisseur de ressource Web Apps nouvellement déployé avec processeur.
7.  Testez le fournisseur de ressources des applications Web.

## <a name="download-required-components"></a>Télécharger les composants requis

1.  Télécharger [le programme d’installation de Service d’application Azure empilement preview](http://aka.ms/azasinstaller). 
2.  Télécharger les [scripts de Service d’application Azure empilement déploiement d’assistance](http://aka.ms/azashelper). 
3.  Extraire les fichiers à partir du fichier zip de scripts d’assistance, il convient de trois scripts :
    - AppServiceCerts.ps1 créer
    - AppServiceDnsRecords.ps1 créer
    - Registre AppServiceResourceProvider.ps1 

## <a name="create-certificates-to-be-used-by-azure-stack-web-apps"></a>Créer des certificats devant être utilisé par Azure pile Web Apps

Ce script premier fonctionne avec l’autorité de certification pile Azure pour créer 3 certificats sont requis par les applications Web. Exécuter le script dans le ClientVM s’assurer que vous exécutez PowerShell en tant qu’azurestack\administrator :
1.  Dans une session PowerShell exécutés en tant que **azurestack\administrator**, exécutez le script **AppServiceCerts.ps1 de créer** .  Cela crée trois certificats, dans le même dossier que le script requis par les applications Web.
2.  Entrez un mot de passe pour sécuriser les fichiers pfx et prenez note de ce que vous devrez entrer le programme d’installation d’applications Web Azure pile.

## <a name="use-the-installer-to-download-and-install-azure-stack-web-apps"></a>Utiliser le programme d’installation pour télécharger et installer les applications Web Azure pile

Le programme d’installation appservice.exe est :
1.  Inviter l’utilisateur à accepter les tiers CLUF Microsoft.
2.  Collecter les informations de déploiement d’Azure pile.
3.  Créer un conteneur blob sur le compte de stockage Azure pile spécifié.
4.  Télécharger les fichiers nécessaires pour installer le fournisseur de ressources Azure pile Web App.
5.  Préparer l’installation à déployer le fournisseur de ressources Web App dans l’environnement pile Azure.
6.  Téléchargez les fichiers sur le compte de stockage de Service d’application.
7.  Présenter les informations nécessaires pour démarrer le Gestionnaire de ressources Azure modèle.

La procédure suivante vous guide dans les étapes de l’installation :

>[AZURE.NOTE] Vous devez utiliser un compte avec élévation de privilèges (administrateur local ou de domaine) pour exécuter le programme d’installation. Si vous êtes connecté en tant qu’azurestack\azurestackuser, le système vous demandera informations d’identification élevées. 

1.  Exécuter appservice.exe en tant que **azurestack\administrator**. 
2.  Cliquez sur **déployer en utilisant le Gestionnaire de ressources Azure**.

![Le programme d’installation de pile Azure Application Service version d’évaluation technique 1][1]

3.  Passez en revue et acceptez le contrat de licence en version précommerciale du logiciel Microsoft, puis sur **suivant**.
4.  Passez en revue et acceptez les termes partylicense troisième et puis cliquez sur **suivant**.
5.  Passez en revue les informations de configuration de Service Cloud application et cliquez sur **suivant**.

![Configuration de pile Azure Application Service version d’évaluation technique 1 application Service Cloud][2]

6. Cliquez sur **se connecter** (à côté de la zone Azure pile abonnements).

![Pile Azure Application Service version d’évaluation technique 1 application Service Cloud Configuration écran deux][3]

7.  Dans la fenêtre d’authentification Azure pile fournir votre **compte d’administrateur de Service Azure Active Directory** et le **mot de passe**, puis cliquez sur **Se connecter**.
**Remarque :** Il s’agit du compte Azure Active Directory que vous avez fourni lorsque vous avez déployé pile Azure.
    - Cliquez sur la **Flèche vers le bas** sur le côté droit de la case en regard **d’Abonnements de pile Azure** , puis sélectionnez votre abonnement.

![Sélection d’abonnement pile Azure Application Service version d’évaluation technique 1][5]

8.  Cliquez sur la **Flèche vers le bas** sur le côté droit de la case en regard des **Emplacements de pile Azure**.
    - Sélectionnez **Local**.
9. Entrez le **nom** de votre administrateur.
10. Entrez un **mot de passe** pour l’administrateur.
11. Passez en revue les **Détails de SQL Server** et apporter des modifications si nécessaire.
12. Examinez le **Compte de connexion SysAdmin** et apporter des modifications si nécessaire.
13. Entrez le **Mot de passe SysAdmin**.
14. Cliquez sur **suivant**.  À ce stade, le programme d’installation vérifie maintenant les détails de connexion pour SQL Server fourni.

![Sélection d’abonnement pile Azure Application Service version d’évaluation technique 1][4]    

15. Cliquez sur **Parcourir** en regard du **Fichier de certificat SSL par défaut applications Web** et accédez à la WebApp **. AzureStack.Local** certificat [créé précédemment](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
16. Entrez le **mot de passe de certificat** que vous avez défini lorsque vous avez créé les certificats.
17. Cliquez sur **Parcourir** en regard du **Fichier de certificat SSL ressource fournisseur** et accédez à la gestion de **. AzureStack.Local** certificat [créé précédemment](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
18. Entrez le **mot de passe de certificat** que vous avez défini lorsque vous avez créé les certificats.
19. Cliquez sur **Parcourir** en regard du **Fichier de certificat racine ressource fournisseur** et accédez à la gestion de **. AzureStack.Local** certificat [créé précédemment](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
20. Cliquez sur **suivant** le programme d’installation vérifie le mot de passe de certificat fourni.

![Détails du certificat pile Azure Application Service version d’évaluation technique 1][6]

Le déploiement prendra environ 45-60 minutes.

![Progression de l’Installation pile Azure Application Service version d’évaluation technique 1][7]

21. Une fois le programme d’installation terminée, cliquez sur **Quitter**.

## <a name="validate-web-apps-installation"></a>Valider l’Installation des applications Web

1.  Sur votre **Ordinateur hôte de pile Azure** ouvrez **Gestionnaire Hyper-V**.
2.  Recherchez la **Machine virtuelle CN0** et **se connecter** à la machine virtuelle.
![Gestionnaire de pile Azure Application Service version d’évaluation technique 1 Hyper-V][8]
3.  Sur le bureau de cet ordinateur virtuel, double-cliquez sur le **Web Cloud Management Console**.
![Console de gestion de version d’évaluation technique 1 pile Azure Application Service][9]
4.  Accédez à **serveurs gérés**.
5.  Lorsque tous les ordinateurs sont **prêts** passez à l’étape suivante. 
![État de serveurs gérés 1 pile Azure Application Service version d’évaluation technique][10]

## <a name="create-dns-records-for-the-management-server-and-front-end-load-balancers"></a>Créer des enregistrements DNS pour le serveur de gestion et les programmes d’équilibrage de charge frontal
1.  Ouvrir une instance de PowerShell en tant que **azurestack\administrator**.
2.  Accédez à l’emplacement des scripts téléchargée et extraite de l' [étape prérequis](#Download-Required-Components).
3.  Exécuter le script **Créer AppServiceDnsRecords.ps1** , cette action crée des entrées DNS pour activer les demandes d’application portail et web routés vers les serveurs frontaux.  Au cours du déploiement processeur des applications Web, équilibrage de charge deux logiciels (SLBs) ont été créés dans le fournisseur de ressources réseau. Ils pointent vers les serveurs de gestion et les serveurs frontal. Les requêtes de processeur au fournisseur de ressources Azure pile application Service et portail accédez au serveur d’administration.

## <a name="register-the-newly-deployed-azure-stack-web-apps-provider-with-arm"></a>Enregistrer le fournisseur de Azure pile Web Apps nouvellement déployé avec processeur
1.  Ouvrir une instance de PowerShell en tant que **azurestack\administrator**.
2.  Accédez à l’emplacement des scripts téléchargée et extraite de l' [étape prérequis](#Download-Required-Components).
3.  Exécuter le script de **Registre AppServiceResourceProvider.ps1** . 

>[AZURE.NOTE] Tapez le nom d’utilisateur et mot de passe **exactement (y compris la casse inférieure et supérieure)** qu’elle a été entrée pour les champs de **mot de passe** dans l’installation et **l’Ou les ordinateurs virtuels administrateur** ou votre fournisseur de ressources l’inscription échoue.

## <a name="test-drive-azure-stack-web-apps"></a>Applications Web de test lecteur pile Azure

À présent que vous avez déployée et enregistré le fournisseur de ressources des applications Web, vous pouvez tester pour vous assurer que les clients peuvent déployer des applications web.

1.  Dans le portail pile Azure, cliquez sur Nouveau, cliquez sur Web + Mobile et cliquez sur application Web.
2.  Dans la carte Web App, tapez un nom dans la zone de l’application Web.
3.  Sous groupe de ressources, cliquez sur Nouveau et tapez un nom dans la zone de groupe de ressources. 
4.  Cliquez sur plan/emplacement du Service d’application et cliquez sur Créer un nouveau.
5.  Dans la carte de plan de Service d’application, tapez un nom dans la zone de plan de Service d’application.
6.  Cliquez sur couche de tarification et cliquez sur Free Shared ou non partagé partagé, cliquez sur Sélectionner, cliquez sur OK, puis cliquez sur Créer.
7.  En moins d’une minute, une vignette pour la nouvelle application web s’affichent dans le tableau de bord. Cliquez sur la vignette.
8.  Dans la carte de l’application web, cliquez sur Parcourir pour afficher le site Web par défaut pour cette application.


**Déploiement d’un site Web WordPress, DNN ou Django (facultatif)**

1. Dans le portail pile Azure, cliquez sur « + », accédez à la place de marché Azure, déployer un site Web Django et attendez la réussite. La plateforme web Django utilise un fichier système-de base de données et ne nécessite pas les fournisseurs de ressources supplémentaires, tels que SQL ou MySQL.  

2. Si vous avez également déployé un fournisseur de ressources MySQL, vous pouvez déployer un site Web WordPress à la place de marché. Lorsque vous êtes invité pour les paramètres de base de données, entrez le nom d’utilisateur en tant que *User1@Server1* (avec le nom d’utilisateur et le nom du serveur de votre choix).

3. Si vous avez également déployé un fournisseur de ressources de SQL Server, vous pouvez déployer un site Web DNN à la place de marché. Lorsque vous êtes invité pour les paramètres de base de données, choisissez une base de données dans l’ordinateur qui exécute SQL Server est connecté à votre fournisseur de ressources.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également essayer autre [plateforme en tant que service (PaaS) services](azure-stack-tools-paas-services.md), tels que le [fournisseur de ressources de SQL Server](azure-stack-sql-rp-deploy-short.md) et le [fournisseur de ressources MySQL](azure-stack-mysql-rp-deploy-short.md).

<!--Image references-->
[1]: ./media/azure-stack-webapps-deploy/AppService_exe_Start.png
[2]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep1.png
[3]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2.png
[4]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2_populated.png
[5]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2_SubscriptionSelection.png
[6]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep3_Certificates.png
[7]: ./media/azure-stack-webapps-deploy/AppService_exe_InstallationProgress.png
[8]: ./media/azure-stack-webapps-deploy/HyperV.png
[9]: ./media/azure-stack-webapps-deploy/MMC.png
[10]: ./media/azure-stack-webapps-deploy/ManagedServers.png


<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[WebAppsDeployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
