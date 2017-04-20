
<properties 
   pageTitle="Notes de mise à jour de SDK Azure pour .NET 2,8" 
   description="Notes de mise à jour de SDK Azure pour .NET 2,8" 
   services="app-service\web" 
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
 
# <a name="azure-sdk-for-net-28-281-and-282"></a>Kit de développement Azure pour .NET 2,8, 2.8.1 et.2.8.2

##<a name="overview"></a>Vue d’ensemble
 
Cet article contient les notes de publication (qui inclut des problèmes connus et des modifications avec rupture) pour le Kit de développement Azure pour les versions .NET 2,8, 2.8.1 et.2.8.2. 

Pour obtenir une liste complète des nouvelles fonctionnalités et les mises à jour effectuées dans cette version, voir l’annonce [Azure SDK 2,8 pour Visual Studio 2013 et Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) . 

##  <a name="azure-sdk-for-net-28"></a>Kit de développement Azure pour .NET 2,8

### <a name="download-azure-sdk-for-net-28"></a>Téléchargez le Kit de développement logiciel Azure pour .NET 2,8

[Kit de développement Azure pour .NET 2,8 Visual Studio 2015](http://go.microsoft.com/fwlink/?LinkId=699285) 

[Kit de développement Azure pour .NET 2,8 pour Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=699287)
 
### <a name="net-452-support"></a>.NET 4.5.2 prend en charge 

####<a name="known-issues"></a>Problèmes connus

Azure .NET SDK 2,8 vous permet de créer .NET 4.5.2 packages Service Cloud. Toutefois 4.5.2 .NET framework ne sera pas installé sur la valeur par défaut des images de système d’exploitation invité jusqu'à janvier 2016 système d’exploitation invité relâchez. Avant que, 4.5.2 .NET framework sera disponible via une version finale du système d’exploitation invité distincte – novembre 2015-02. Consultez la page [Azure invité OS versions et matrice de compatibilité SDK](../cloud-services/cloud-services-guestos-update-matrix.md) pour déterminer quand est mise à l’image.  Une fois l’image 2015-02 novembre est lancé, vous pouvez choisir d’utiliser cette image en mettant à jour votre fichier de configuration du Service Cloud (.cscfg). Dans la configuration du service fichier définie l’attribut osVersion de l’élément ServiceConfiguration avec la chaîne « WA-invité-OS-4.26_201511-02 ». Si vous choisissez de participer à utiliser cette image, vous obtiendrez ne sont plus mises à jour automatiques pour le système d’exploitation invité. Pour obtenir les mises à jour automatiques osVersion doit être définie sur « * » et .NET 4.5.2 n’est disponible par le biais des mises à jour automatiques dans janvier 2016.

###<a name="azure-data-factory"></a>Données Azure usine

####<a name="known-issues"></a>Problèmes connus 

Lors de la création de projet de **Données usine modèle** impliquant des exemples de données, script shell power azure peut échouer si version shell power azure installée sur l’ordinateur se trouve après 0.9.8.

Pour créer ce type de projet, vous devez installer la [version shell power azure 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).


### <a name="azure-resource-manager-tools"></a>Outils du Gestionnaire de ressources Azure 

####<a name="breaking-changes"></a>Annulation de modifications

Le script PowerShell fourni par le projet de groupe de ressources Azure a été mis à jour dans cette version pour travailler avec les applets de commande PowerShell Azure nouvelle version 1.0.  Ce nouveau script ne fonctionne pas à partir d’avec Visual Studio, lorsque vous utilisez une version du Kit de développement avant 2,8.  

Scripts des projets créés dans les versions antérieures du Kit de développement ne seront exécute pas à partir de Visual Studio lors de l’utilisation du Kit de développement 2,8.  Tous les scripts continueront à fonctionner en dehors de Visual Studio avec la version appropriée des applets de commande PowerShell Azure.  

Le Kit de développement 2,8 requiert la version 1.0 des applets de commande PowerShell Azure.  Toutes les autres versions du Kit de développement requièrent version 0.9.8 des applets de commande PowerShell Azure.  Pour plus d’informations, voir [ce](http://go.microsoft.com/fwlink/?LinkID=623011) billet de blog.

###<a name="web-tools-extensions"></a>Extensions d’outils Web

####<a name="known-issues"></a>Problèmes connus

Problèmes connus suivants seront résolus dans la version suivante.

- Application Service lié Cloud et l’Explorateur de serveurs mouvement pour les environnements de production non (par exemple, Chine Azure ou clients pile Azure) ne fonctionnent pas. Pour les clients dans ces domaines concernés, télécharger le profil de publication à partir du portail Azure permettra possibilité de publication. Une version ultérieure sera corrigée mouvements tels que « Débogueur joindre » et « Afficher les journaux en continu » pour les clients de pile et Azure Chine. 
- Clients peuvent voir erreurs au cours du Service d’application création lors de l’analyse de l’application instance vers lequel ils sont déploiement se trouve dans une région autre que les États-Unis Extrême-Orient. Dans ces scénarios, création d’un Service d’application dans le portail et le téléchargement du profil de publication permettront scénarios de publication. 

###<a name="azure-hdinsight-tools"></a>Outils Azure HDInsight

####<a name="new-updates"></a>Nouvelles mises à jour

- Vous pouvez exécuter votre requête Hive du cluster via HiveServer2 avec presque pas de surcharge et voir que la tâche se connecte en temps réel.
- À l’aide de la nouvelle ruche l’exécution affichage des tâches que vous pouvez attentivement votre travail plus approfondie, recherchez plus d’informations, puis identifier les problèmes potentiels.

Pour plus d’informations, voir [Azure SDK 2,8 pour Visual Studio 2013 et Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/). 

## <a name="azure-sdk-for-net-281"></a>Kit de développement Azure pour .NET 2.8.1

### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>Problèmes connus pour Visual Studio 2013 et Visual Studio 2015
 
1. WebJob déclenchée publie vers les emplacements s’afficher et erreur et ne définir une planification, mais il sera pousser la WebJob vers Azure. Les clients qui ont besoin d’une tâche planifiée peuvent ensuite utiliser le portail Azure pour configurer la planification de la WebJob. 
2. Clients Python peuvent rencontrer des problèmes de débogueur. L’équipe du service développe un correctif pour ce mais s’ils sont affectés, n’hésitez pas faire Microsoft connaître dans les forums ou sur le blog de l’annonce ou libérer la section commentaires de notes. 
3. Clients dans certaines régions (par exemple, Inde Sud) conséquences d’une application Service mise en service des erreurs. Ceci est cohérent avec le portail et les clients qui rencontrent ce problème peuvent utiliser le portail Azure pour demander l’accès à publier sur ces régions geo. Une fois qu’ils demandent l’accès à ces régions à l’aide de la configuration du portail Azure devrait fonctionner. 

##<a name="azure-sdk-for-net-282"></a>Kit de développement Azure pour .NET point 2.8.2

Après l’installation de la point 2.8.2 des outils clients peuvent rencontrer le problème suivant.         

- Si vous utilisez Windows 10 et que vous n’avez pas installé Internet Explorer, vous pouvez obtenir un message d’erreur « Internet Explorer ne peut pas être trouvé ».
Pour résoudre le problème, installez Internet Explorer à l’aide de la boîte de dialogue Ajouter/supprimer des composants Windows.

Si vous observez ce problème, utilisez la fonctionnalité Envoyer un sourire à signaler.

Pour plus d’informations, reportez-vous à [ce](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) billet.
##<a name="other-updates"></a>Autres mises à jour

Les autres mises à jour, voir [annonce Azure SDK 2,8 publier](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

##<a name="also-see"></a>Voir aussi

[Billet d’annonce SDK 2,8 Azure](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Prise en charge et déclassement des informations pour le Kit de développement Azure pour .NET et API](https://msdn.microsoft.com/library/azure/dn479282.aspx)

