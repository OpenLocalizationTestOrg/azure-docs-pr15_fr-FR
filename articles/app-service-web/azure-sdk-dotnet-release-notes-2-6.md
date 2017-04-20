<properties 
   pageTitle="Notes de mise à jour de SDK Azure pour .NET 2.6" 
   description="Notes de mise à jour de SDK Azure pour .NET 2.6" 
   services="app-service/web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/17/2016"
   ms.author="juliako"/>

 
# <a name="azure-sdk-for-net-26-release-notes"></a>Notes de mise à jour de SDK Azure pour .NET 2.6

Ce document contient les notes de publication pour le Kit de développement Azure pour .NET 2.6 mise à jour. 

Avec Azure SDK 2.6 vous pouvez développer des applications de service cloud (PaaS) ciblage .NET 4.5.2 ou .NET 4.6 autant que vous installez manuellement la cible du .NET Framework sur le rôle de Service Cloud. Voir [installer .NET sur un rôle de Service Cloud](http://go.microsoft.com/fwlink/?LinkID=309796).


##<a name="service-bus-updates"></a>Mises à jour Bus des services

- Événement Hubs : 

    - Permet à présent le contrôle d’accès ciblée lors de l’envoi des événements en exposant point de terminaison publisher supplémentaires pour Hubs événement.
    - Stabilité et l’amélioration ajouté à la fonctionnalité Hubs événement.
    - Ajout de prise en charge du protocole Amqp sur WebSocket pour la messagerie et les événements Hubs.

##<a name="hdinsight-tools-for-visual-studio-updates"></a>HDInsight Tools pour Visual Studio met à jour

- **Amélioration d’IntelliSense**: suggestion de métadonnées à distance

    Maintenant HDInsight Tools pour Visual Studio prend en charge lors de l’obtention de métadonnées à distance lorsque vous modifiez votre script Hive. Par exemple, vous pouvez taper *FROM *Sélectionnez* ** et tous les noms de table seront affichés. En outre, les noms de colonne seront affichés après la spécification d’une table.

- **Prise en charge des émulateur HDInsight**

    Maintenant HDInsight Tools pour Visual Studio prise en charge la connexion à Microsoft emulator HDInsight, afin que vous puissiez développer vos scripts Hive localement sans introduire de tous les coûts, puis exécuter les scripts sur vos clusters HDInsight. 

    Pour plus d’informations, reportez-vous à [ce manuel](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

- **HDInsight Tools pour Visual Studio prend en charge pour les clusters Hadoop génériques** (Preview)

    Maintenant HDInsight Tools pour Visual Studio prennent en charge génériques clusters Hadoop, afin d’utiliser HDInsight Tools pour Visual Studio pour effectuer les opérations suivantes :

    - se connecter à votre cluster, 
    - Écrire requête Hive avec prise en charge IntelliSense/la saisie semi-automatique améliorée, 
    - afficher toutes les tâches dans votre cluster avec une interface utilisateur intuitive. 

    Pour plus d’informations, reportez-vous à [ce manuel](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

##<a name="in-role-cache-updates"></a>Mises à jour dans le rôle Cache

- **Dans le rôle Cache** a été mis à jour pour utiliser **Microsoft Azure stockage SDK** version 4.3. Jusqu'à présent, le **Cache de rôle** utilisait Azure stockage SDK version 1.7.

    Clients à l’aide d’Azure SDK 2.5 ou ci-dessous doit mettre à jour vers Azure SDK 2.6 et déplacer vers la nouvelle version du Kit de développement de stockage Azure. 

    Pour le moment stockage Azure version 2011-08-18 est programmé pour être supprimé 1 août 2016. Les migrations du Cache dans le rôle à partir d’Azure SDK 2.5 ou en dessous de vers 2.6 doivent être terminées à ce stade. Pour plus d’informations sur la retraite de stockage Azure version 2011-08-18, voir [mise à jour Microsoft Azure stockage Service Version suppression : Extension 2016](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

>[AZURE.IMPORTANT]Nous améliorons annonce la retraite 30 novembre 2016, de Service de Cache géré Azure et Azure dans le rôle Cache. Nous vous recommandons de migrer vers Azure Redis Cache en vue de ce déclassement. Pour plus d’informations sur les dates et Guide de migration, voir [offre de Cache Azure qui correspond à mes besoins ?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)

##<a name="azure-app-service-tools"></a>Outils de Service application Azure

Les éléments suivants ont été mis à jour dans la version Azure SDK 2.6.

- Publication Azure améliorée pour inclure des applications de l’API Azure comme cible de déploiement.
- Applications de l’API des fonctionnalités de mise en service pour permettre aux utilisateurs avec la fonctionnalité de création et la mise en service des API application.
- Explorateur de serveurs modifié pour refléter le nouveau nœud de Service d’application, avec les applications Web, Mobile et API regroupées par groupe de ressources.
- Ajouter un mouvement de Client de l’application API Azure ajouté à la plupart des projets c# qui permettra à génération automatique des applications API compatibles avec les Swagger en cours d’exécution dans l’abonnement Azure d’un utilisateur.
- Applications API outils et des nœuds de Service d’application dans l’Explorateur de serveur sont uniquement disponibles dans Visual Studio 2013. 

##<a name="azure-resource-manager-tools-updates"></a>Mises à jour des outils du Gestionnaire de ressources Azure

Les outils du Gestionnaire de ressources Azure ont été mis à jour pour inclure les modèles de Machines virtuelles, de réseau et de stockage. Le JSON expérience d’édition a été mis à jour pour inclure un nouveau mode plan pour les modèles et la possibilité de modifier les modèles de l’utilisation d’extraits JSON. Modèles déployés à partir de Visual Studio utilisent un script PowerShell fourni avec le projet, afin que les modifications apportées au script seront utilisées par Visual Studio.

##<a name="diagnostics-improvements-for-cloud-services"></a>Améliorations des diagnostics pour les Services en nuage

Azure 2.6 SDK ramène prise en charge pour collecter les journaux de diagnostics dans l’émulateur cluster Azure et les transférer vers stockage de développement. Les diagnostics se connecte (y compris la trace ouvre, suivi de journaux Windows (ETW), compteurs de performance, infrastructure journaux et windows les journaux d’événements des événements de l’application) générée lorsque l’application est en cours d’exécution dans l’émulateur peuvent être transférés vers le stockage de développement pour vérifier que la journalisation des diagnostics fonctionne sur votre ordinateur local. 

Le compte de stockage Diagnostics peut désormais être indiqué dans le fichier de configuration (.cscfg) service rend plus facile à utiliser des comptes de stockage diagnostics différentes pour les différents environnements. Il existe certaines différences importantes entre comment l’application de la chaîne de connexion dans Azure SDK 2.4 et Azure SDK 2.6. Pour plus d’informations sur l’utilisation de la connexion de stockage Diagnostics chaîne et leur impact sur vos projets voir [Configuration des Diagnostics pour les Services en nuage Azure](http://go.microsoft.com/fwlink/?LinkID=532784).

##<a name="breaking-changes"></a>Annulation de modifications

###<a name="azure-resource-manager-tools"></a>Outils du Gestionnaire de ressources Azure 

- Le type de projet **Projets de déploiement de Cloud** disponible dans le SDK Azure 2.5 a été renommé au **Groupe de ressources Azure**.
- Type de **Projets de déploiement de cloud** de projets créés dans le Azure SDK 2.5 peut être utilisé dans 2.6 mais déploiement du modèle à partir de Visual Studio échouera. Toutefois, le déploiement avec le script PowerShell fonctionnera toujours comme précédemment.  Pour plus d’informations sur l’utilisation de **Projets de déploiement de Cloud** dans 2.6 Lisez ce [billet](http://go.microsoft.com/fwlink/?LinkID=534086).
 
##<a name="known-issues"></a>Problèmes connus

- Collecter les journaux de diagnostics dans l’émulateur nécessite un système d’exploitation 64 bits. Les journaux de Diagnostics ne seront pas recueillies lors de l’exécution sur un système d’exploitation 32 bits. Cela n’affecte pas les autres fonctionnalités émulateur. 

- Azure 2.6 SDK publié sur 4/29/2015 avait deux problèmes : 

    - Application universelle n’a pas pu être chargée dans Visual Studio 2015 Azure SDK 2.6 a été installé sur l’ordinateur.
    - Débogage d’un projet de Service Cloud échoue dans Visual Studio 2013 et Visual Studio 2015 où Visual Studio cesse de répondre et se bloque lors de l’affichage d’une boîte de dialogue avec le message « Configuration diagnostics pour émulateur ».
    
    Une mise à jour vers Azure SDK 2.6 a été publié 18/5/2015. La version mise à jour est 2.6.30508.1601 ; Il comprend les correctifs de deux problèmes décrits ci-dessus. Vous pouvez identifier la version du Kit de développement le panneau de configuration -> Programmes et fonctionnalités -> Microsoft Azure Tools pour Microsoft Visual Studio 2013 – v 2.6. La colonne Version affiche le numéro de version.

    Si vous êtes toujours confronté aux problèmes ci-dessus, installer la dernière version du Kit de développement 2.6 Azure pour [VS 2012](http://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [2013 VS](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) ou [VS 2015](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).
 
##<a name="see-also"></a>Voir aussi

[Prise en charge et déclassement des informations pour le Kit de développement Azure pour .NET et API](https://msdn.microsoft.com/library/azure/dn479282.aspx/)
