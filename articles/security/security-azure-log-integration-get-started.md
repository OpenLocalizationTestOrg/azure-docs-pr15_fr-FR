<properties
   pageTitle="Prise en main intégration du journal Azure | Microsoft Azure"
   description="Découvrez comment installer le service d’intégration journal Azure, intégrer les journaux de stockage Azure, les journaux d’Audit Azure et les alertes du centre de sécurité Azure."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# <a name="get-started-with-azure-log-integration-preview"></a>Prise en main intégration du journal Azure (Preview)

Journal Azure intégration vous permet d’intégrer des journaux brutes à partir de vos ressources Azure dans vos systèmes locaux les informations de sécurité et de gestion des événements (SIEM). Cette intégration fournit un tableau de bord unifiée pour tous vos actifs, en local ou dans le cloud, afin que vous pouvez agréger, corrélation, analyser et d’alerte pour les événements de sécurité associées à vos applications.

Ce didacticiel décrit comment installer intégration du journal Azure et intégrer les journaux de stockage Azure Azure les journaux d’Audit et alertes du centre de sécurité Azure. Durée estimée pour compléter ce didacticiel est une heure.

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer ce didacticiel, vous devez disposer des éléments suivants :

- Un ordinateur (en local ou sur le cloud) pour installer le service d’intégration journal Azure. Cet ordinateur doit exécuter un système d’exploitation Windows 64 bits avec .net 4.5.1 installé. Cet ordinateur est appelé l' **Intégration Azlog**.
- Abonnement Azure. Si vous n’avez pas une, vous pouvez vous inscrire pour un [compte gratuit](https://azure.microsoft.com/free/).
- Diagnostics de Windows Azure activés pour vos machines virtuelles Azure (machines virtuelles). Pour activer des diagnostics pour les Services en nuage, voir [Activation des Diagnostics Azure dans Azure Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics.md). Pour activer des diagnostics pour une machine virtuelle Azure exécutant Windows, voir [Utiliser PowerShell pour activer les Diagnostics Azure dans une Machine virtuelle en cours d’exécution](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md).
- Connectivité à partir de l’intégration Azlog stockage Azure et de s’authentifier et autoriser l’accès aux abonnement Azure.
- Pour les journaux machine virtuelle Azure, l’agent SIEM (par exemple, redirecteurs universel Splunk, agent collecteur d’événements HP ArcSight Windows ou IBM QRadar WinCollect) doit être installé sur l’intégration Azlog.

## <a name="deployment-considerations"></a>Considérations relatives au déploiement

Vous pouvez exécuter plusieurs instances de l’intégration Azlog si volume des événements est élevé. Équilibrage de charge des comptes de stockage Azure Diagnostics pour Windows *(WAD)* et le nombre d’abonnements pour fournir aux instances doit être basée sur votre capacité.

Sur un ordinateur 8 processeurs (cœur), une seule instance d’intégration Azlog peut traiter environ 24 millions d’événements par jour (~1M/hour).

Sur un ordinateur 4 processeurs (cœur), une seule instance d’intégration Azlog peut traiter environ 1,5 millions d’événements par jour (~62.5K/hour).

## <a name="install-azure-log-integration"></a>Installer l’intégration journal Azure

Téléchargez [Azure s’intégration](https://www.microsoft.com/download/details.aspx?id=53324).

Le service d’intégration journal Azure collecte des données de télémétrie à partir de l’ordinateur sur lequel il est installé.  Données de télémétrie recueillies sont la suivante :

- Exceptions qui se produisent pendant l’exécution de l’intégration de journal Azure
- Indicateurs sur le nombre de requêtes et événements traités
- Statistiques sur les Azlog.exe Utilisation des options de ligne de commande

> [AZURE.NOTE] Vous pouvez désactiver la collecte de données de télémétrie en désactivant cette option.

## <a name="integrate-azure-vm-logs-from-your-azure-diagnostics-storage-accounts"></a>Intégrer des journaux Azure machine virtuelle à partir de vos comptes de stockage Azure Diagnostics

1. Vérifier les conditions préalables répertoriées ci-dessus pour vous assurer que votre compte de stockage WAD collecte des journaux avant de poursuivre l’intégration de journal Azure. N’effectuez pas les étapes suivantes si votre compte de stockage WAD pas collecte des journaux.

2. Ouvrez l’invite de commandes et le **cd** dans **c:\Program Files\Microsoft Azure journal d’intégration**.

3. Exécutez la commande

        azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>

      Remplacez StorageAccountName par le nom du compte de stockage Azure configuré pour recevoir des événements de diagnostic de votre ordinateur virtuel.

        azlog source add azlogtest WAD azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==

      Si vous souhaitez que l’id de l’abonnement s’affiche dans l’événement XML, ajoutez l’ID de l’abonnement au nom convivial :

        azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>

4. Patientez 30 à 60 minutes (Cela peut prendre jusqu'à une heure), puis affichez les événements qui sont extraites du compte de stockage. Pour afficher, ouvrez **Observateur d’événements > journaux Windows > événements transmis** sur l’intégration Azlog.

5. Assurez-vous que votre lien SIEM standard installé sur l’ordinateur est configuré pour sélectionner des événements à partir du dossier **Événements transmis** et les canaux à votre instance SIEM. Passez en revue la configuration spécifique SIEM pour configurer et consultez les journaux d’intégration.

## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>Que se passe-t-il si données n’apparaît pas dans le dossier événements transférés ?

Si après une heure données n’apparaît pas dans le dossier **Événements transmis** , puis :

1. Vérifier l’ordinateur et vérifiez qu’il peut accéder Azure. Pour tester la connectivité, essayez d’ouvrir le [portail Azure](http://portal.azure.com) à partir du navigateur.

2. Vérifiez que de compte d’utilisateur **azlog** a l’autorisation d’écriture sur dossier **users\azlog**.

3. Vérifiez que le compte de stockage ajouté dans la commande **Ajouter azlog source** apparaît lorsque vous exécutez la commande **liste source azlog**.
4. Accédez à **Observateur d’événements > journaux Windows > Application** pour déterminer s’il existe des erreurs signalées à partir de l’intégration de journal Azure.

Si vous ne voyez toujours les événements, puis :

1. Télécharger [Microsoft Azure stockage Explorer](http://storageexplorer.com/).

2. Se connecter à ajouter dans la commande **azlog source ajouter**le compte de stockage.

3. Dans l’Explorateur de stockage Microsoft Azure, accédez au tableau **WADWindowsEventLogsTable** pour savoir s’il existe des données. Dans le cas contraire, puis diagnostics dans la machine virtuelle n’est pas configuré correctement.

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Intégrer les journaux d’audit Azure et les alertes du centre de sécurité

1. Ouvrez l’invite de commandes et le **cd** dans **c:\Program Files\Microsoft Azure journal d’intégration**.

2. Exécutez la commande

        azlog createazureid

      Cette commande vous demande votre nom d’utilisateur Azure. La commande puis crée un [Principal du Service Azure Active Directory](../active-directory/active-directory-application-objects.md) dans les clients AD Azure qui hébergent les abonnements Azure dans lequel l’utilisateur connecté est un administrateur, un administrateur de co-création ou un propriétaire. La commande échouera si l’utilisateur connecté n'est qu’un utilisateur invité dans le client AD Azure. Authentification à Azure est effectuée par le biais Azure Active Directory (AD).  Création d’un principal de service d’intégration Azlog crée l’identité Azure AD qui sera accordée l’accès en lecture à partir des abonnements Azure.

3. Exécutez la commande

        azlog authorize <SubscriptionID>

      Il attribue accès en lecture de l’abonnement au service principal créé à l’étape 2. Si vous ne spécifiez pas une SubscriptionID, puis il tente d’affecter le rôle de lecteur principal service tous les abonnements à laquelle vous avez un accès.

        azlog authorize 0ee9d577-9bc4-4a32-a4e8-c29981025328

      > [AZURE.NOTE] Vous pouvez afficher les alertes si vous exécutez la commande **Autoriser** immédiatement après la commande **createazureid** . Il existe une latence entre lorsque le compte Azure AD est créé et lorsque le compte est disponible pour une utilisation. Si vous attendez environ 10 secondes après l’exécution de la commande **createazureid** pour exécuter la commande **Autoriser** , vous ne devriez pas voir ces avertissements.

4. Vérifier les dossiers suivants pour confirmer que les fichiers JSON du journal d’Audit sont :

  - **c:\Users\azlog\AzureResourceManagerJson**
  - **c:\Users\azlog\AzureResourceManagerJsonLD**

5. Vérifier pour confirmer que les alertes du centre de sécurité existent dans les dossiers suivants :

  - **c:\Users\azlog\ AzureSecurityCenterJson**
  - **c:\Users\azlog\AzureSecurityCenterJsonLD**

6. Pointez sur le lien de redirecteurs standard SIEM fichier vers le dossier approprié pour copier les données à l’instance SIEM. Vous devrez peut-être certains mappages de champ en fonction du produit SIEM que vous utilisez.

Si vous avez des questions sur l’intégration de journal Azure, envoyez un message électronique à [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment installer intégration du journal Azure et intégrer les journaux à partir du stockage Azure. Pour plus d’informations, voir les rubriques suivantes :

- [Intégration du journal Microsoft Azure Azure journaux (Preview)](https://www.microsoft.com/download/details.aspx?id=53324) – centre de téléchargement pour plus d’informations, configuration système requise et les instructions d’installation sur l’intégration de journal Azure.
- [Présentation de l’intégration de journal Azure](security-azure-log-integration-overview.md) – ce document vous présente l’intégration de journal Azure, ses fonctionnalités clées et son fonctionnement.
- [Étapes de configuration partenaire](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – ce billet de blog vous montre comment configurer l’intégration de journal Azure pour fonctionner avec les solutions des partenaires Splunk ArcSight HP et IBM QRadar.
- [Journal azure intégration Forum aux questions (FAQ)](security-azure-log-integration-faq.md) - ce forum aux questions répond aux questions sur l’intégration de journal Azure.
- [Alertes du centre de sécurité intégration avec Azure s’intégration](../security-center/security-center-integrating-alerts-with-log-integration.md) – ce document vous montre comment synchroniser les alertes du centre de sécurité, ainsi que des événements de sécurité machine virtuelle collectées par Diagnostics Azure et Azure les journaux d’Audit, avec le journal analytique ou votre solution SIEM.
- [Nouvelles fonctionnalités pour les diagnostics de Windows Azure et Azure les journaux d’Audit](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – ce billet de blog vous présente les journaux d’Audit Azure et d’autres fonctionnalités qui vous aident Familiarisez-vous dans les opérations de vos ressources Azure.
