<properties 
    pageTitle="Utiliser ReportViewer dans un Site Web | Microsoft Azure"
    description="Cette rubrique explique comment créer un site Web Microsoft Azure avec ce Visual Studio contrôle qui affiche un rapport stocké sur une Machine virtuelle de Microsoft Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="guyinacube"
    manager="erikre"
    editor="monicar" 
    tags="azure-service-management" />
<tags 
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/04/2016"
    ms.author="asaxton" />

# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Utiliser ReportViewer dans un Site Web hébergé dans Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Vous pouvez créer un site Web Microsoft Azure avec ce Visual Studio contrôle qui affiche un rapport stocké sur une Machine virtuelle de Microsoft Azure. Le contrôle ReportViewer se trouve dans une application Web que vous créez avec le modèle d’application Web ASP.NET.

>[AZURE.IMPORTANT] Les modèles d’Application Web ASP.NET MVC ne prennent pas en charge le contrôle ReportViewer.

Pour intégrer ReportViewer dans votre site Web Microsoft Azure, vous devez effectuer les tâches suivantes.

- **Ajouter** Assemblys pour le Package de déploiement

- **Configurer** Authentification et autorisation

- **Publier** l’application Web ASP.NET vers Azure

## <a name="prerequisites"></a>Conditions préalables

Passez en revue la section « recommandation générale et des pratiques recommandées » dans [SQL Server Business Intelligence dans le Machines virtuelles Azure](virtual-machines-windows-classic-ps-sql-bi.md).

>[AZURE.NOTE] Contrôles ReportViewer sont fournis avec Visual Studio, Standard Edition ou version ultérieure. Si vous utilisez Web Developer Express Edition, vous devez installer [MICROSOFT rapport visionneuse 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) pour utiliser les fonctionnalités de runtime ReportViewer.
>
>ReportViewer configuré en mode de traitement local n’est pas pris en charge dans Microsoft Azure.

Passez en revue le livre blanc [contrôle Visionneuse de rapports Reporting Services et machine virtuelle Microsoft Azure en fonction de serveurs de rapports](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx).

## <a name="adding-assemblies-to-the-deployment-package"></a>Ajout d’assemblys pour le Package de déploiement

Lorsque vous hébergez vos ASP.NET application localement, les assemblys ReportViewer sont généralement installés directement dans le global assembly cache (GAC) du serveur IIS pendant l’installation de Visual Studio et vous peuvent accéder directement par l’application. Toutefois, lorsque vous hébergez votre application ASP.NET dans le cloud, Microsoft Azure n’autorise pas lié à être installés dans le GAC, afin que vous devez vous assurer que les assemblys ReportViewer sont disponibles localement dans votre application. Vous pouvez procéder en ajoutant des références à ceux-ci dans votre projet et les configurer pour être copié localement.

En mode de traitement distant, le contrôle ReportViewer utilise les assemblys suivants :

- **Microsoft.ReportViewer.WebForms.dll**: contient le code de ReportViewer, vous devez utiliser ReportViewer dans votre page. Référence pour cet assembly est ajoutée à votre projet lorsque vous déposez un contrôle ReportViewer sur une page ASP.NET dans votre projet.

- **Microsoft.ReportViewer.Common.dll**: contient des classes utilisées par le contrôle ReportViewer en cours d’exécution. Il n’est pas automatiquement ajouté à votre projet.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Pour ajouter une référence à Microsoft.ReportViewer.Common

- Avec le bouton droit nœud **références** de votre projet et sélectionnez **Ajouter une référence**, sélectionnez l’assembly dans l’onglet .NET et cliquez sur **OK**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Pour rendre les assemblys localement accessible par votre application ASP.NET

1. Dans le dossier **références** , cliquez sur l’assembly Microsoft.ReportViewer.Common afin que ses propriétés s’affichent dans le volet Propriétés.

1. Dans le volet Propriétés, définissez la **Copie locale** sur True.

1. Répétez les étapes 1 et 2 pour Microsoft.ReportViewer.WebForms.

### <a name="to-get-reportviewer-language-pack"></a>Pour obtenir le module linguistique ReportViewer

1. Installer le package redistribuable Microsoft rapport visionneuse 2012 Runtime approprié à partir du [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=317386).

1. Sélectionnez la langue dans la liste déroulante et la page soient redirigée vers la page Centre de téléchargement correspondante.

1. Cliquez sur **Télécharger** pour démarrer le téléchargement de ReportViewerLP.exe.

1. Après avoir téléchargé ReportViewerLP.exe, cliquez sur **exécuter** pour installer immédiatement, ou cliquez sur **Enregistrer** pour enregistrer sur votre ordinateur. Si vous cliquez sur **Enregistrer**, n’oubliez pas le nom du dossier où vous enregistrez le fichier.

1. Recherchez le dossier où vous avez enregistré le fichier. Avec le bouton droit ReportViewerLP.exe et cliquez sur **Exécuter en tant qu’administrateur**, puis cliquez sur **Oui**.

1. Après avoir exécuté ReportViewerLP.exe, vous verrez le c:\windows\assembly a la ressource fichiers **Microsoft.ReportViewer.Webforms.Resources** et **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Pour configurer pour localisées contrôle ReportViewer

1. Téléchargez et installez le package redistribuable Microsoft rapport visionneuse 2012 Runtime en suivant les instructions spécifiées ci-dessus.

1. Créer <language> dossier dans le projet et copiez l’assembly de ressource associée fichiers il. Les fichiers d’assemblage ressource à copier sont : **Microsoft.ReportViewer.Webforms.Resources.dll** et **Microsoft.ReportViewer.Common.Resources.dll**. Sélectionnez les fichiers d’assembly ressource, puis dans le volet Propriétés, affectez à **Copier dans le répertoire de sortie** pour «**toujours**».

1. Définir la Culture et UICulture pour le projet web. Pour plus d’informations sur la configuration de la Culture et la Culture d’interface utilisateur pour une page Web ASP.NET, voir [Comment : définir la Culture et la Culture d’interface utilisateur pour la globalisation des pages Web ASP.NET](http://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Configuration d’authentification et autorisation

ReportViewer a besoin d’utiliser les informations d’identification appropriées pour vous authentifier avec le serveur de rapports et les informations d’identification doivent être autorisées par le serveur de rapports pour accéder aux rapports de que votre choix. Pour plus d’informations sur l’authentification, consultez le livre blanc [contrôle Visionneuse de rapports Reporting Services et machine virtuelle Microsoft Azure en fonction de serveurs de rapports](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Publier l’application Web ASP.NET vers Azure

Pour obtenir des instructions sur la publication d’une application Web ASP.NET vers Azure, voir [Comment : migrer et de publier une Application Web vers Azure à partir de Visual Studio](../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) et [prise en main des applications Web ASP.NET](../app-service-web/web-sites-dotnet-get-started.md).

>[AZURE.IMPORTANT] Si la commande Ajouter un projet de déploiement Azure ou ajouter un projet Azure Cloud Service n’apparaît pas dans le menu contextuel dans l’Explorateur de solutions, vous devrez peut-être modifier le framework cible pour le projet sur .NET Framework 4.
>
>Les deux commandes offrent essentiellement les mêmes fonctionnalités. Un ou l’autre commande s’affichent dans le menu contextuel en fonction de la version de Microsoft Azure SDK que vous avez installé.

## <a name="resources"></a>Ressources

[Rapports de Microsoft](http://go.microsoft.com/fwlink/?LinkId=205399)

[À la décision SQL Server dans des Machines virtuelles Azure](virtual-machines-windows-classic-ps-sql-bi.md)

[Utiliser PowerShell pour créer une machine virtuelle Azure avec un serveur de rapports en Mode natif](virtual-machines-windows-classic-ps-sql-report.md)

[Déclaration de contrôle de visionneuse de rapports de Services et Microsoft Azure machine virtuelle basée serveurs de rapports](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx)
