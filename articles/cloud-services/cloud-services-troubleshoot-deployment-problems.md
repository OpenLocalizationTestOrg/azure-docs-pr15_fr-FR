<properties
 pageTitle="Résoudre les problèmes de déploiement de service cloud | Microsoft Azure"
 description="Il existe quelques problèmes courants que vous pouvez rencontrer lors du déploiement d’un service cloud sur Azure. Cet article fournit des solutions à certains d'entre eux."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="troubleshoot-cloud-service-deployment-problems"></a>Résoudre les problèmes de déploiement de service cloud

Lorsque vous déployez un package d’application de service cloud Azure, vous pouvez obtenir des informations sur le déploiement à partir du volet de **Propriétés** dans le portail Azure. Vous pouvez utiliser les détails dans ce volet pour vous aider à résoudre les problèmes avec le service cloud, et vous pouvez fournir ces informations pour prendre en charge Azure lors de l’ouverture d’une nouvelle demande de support.

Vous pouvez trouver le volet **Propriétés** comme suit :

* Dans le portail Azure, cliquez sur le déploiement de votre service cloud et cliquez sur **tous les paramètres**, puis cliquez sur **Propriétés**.
* Dans le portail Azure classique, cliquez sur le déploiement de votre service cloud, cliquez sur **tableau de bord**, situé dans le coin inférieur droit de la page (sous **coup de œil**). N’oubliez pas qu’il n’y a aucune étiquette « Propriétés » sur ce volet.

> [AZURE.NOTE] Vous pouvez copier le contenu du volet **Propriétés** dans le Presse-papiers en cliquant sur l’icône dans le coin supérieur droit du volet.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problème : je ne parviens pas à accéder à mon site Web, mais mon déploiement est démarré et toutes les instances de rôle sont prêts

Le lien URL de site Web affiché dans le portail n’inclut pas le port. Le port par défaut pour les sites Web est 80. Si votre application est configurée pour s’exécuter dans un autre port, vous devez ajouter le numéro de port à l’URL s’ils accèdent au site Web.

1. Dans le portail Azure, cliquez sur le déploiement de votre service cloud.
2. Dans le volet **Propriétés** du portail Azure, vérifiez les ports pour les instances de rôle (sous **Points de terminaison d’entrée**).
3. Si le port n’est pas 80, ajoutez la valeur de port approprié à l’URL lorsque vous accédez à l’application. Pour spécifier un port ceux par défaut, tapez l’URL, suivi de deux-points ( :)), suivi du numéro de port, sans espace.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problème : Mon rôle instances recyclés sans me rien faire

Service correcteur s’effectue automatiquement lorsque Azure détecte les nœuds problème et par conséquent déplace instances de rôles pour les nouveaux nœuds. Dans ce cas, vous pouvez voir vos instances de rôles recyclage automatiquement. Pour déterminer si service correcteur s’est produite :

1. Dans le portail Azure, cliquez sur le déploiement de votre service cloud.
2. Dans le volet **Propriétés** du portail Azure, vérifiez les informations et déterminez si service correcteur s’est produite lors de l’heure à laquelle vous avez observé les rôles recyclage.

Rôles seront également Corbeille environ une fois par mois pendant le système d’exploitation hôte et mises à jour du système d’exploitation invité.  
Pour plus d’informations, consultez le blog de publication [Rôle Instance redémarre en raison de mises à niveau du système d’exploitation](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problème : je ne parviens pas à effectuer une permutation VIP et recevoir un message d’erreur

Une permutation VIP n’est pas autorisée si une mise à jour de déploiement est en cours. Mises à jour de déploiement peuvent se produire automatiquement lorsque :

* Un nouveau système d’exploitation invité est disponible et vous sont configurés pour les mises à jour automatiques.
* Service correcteur se produit.

Pour déterminer si une mise à jour automatique vous empêche d’effectuer une permutation VIP :

1. Dans le portail Azure, cliquez sur le déploiement de votre service cloud.
2. Dans le volet **Propriétés** du portail Azure, examinez la valeur de **statut**. S’il est **prêt**, puis vérifiez **dernière opération** pour voir si une récemment s’est produit qui peuvent empêcher la permutation VIP.
3. Répétez les étapes 1 et 2 pour le déploiement en production.
4. Si une mise à jour automatique est en cours, attendez qu’elle se termine avant d’essayer d’effectuer le remplacement VIP.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problème : Une instance de rôle est une boucle entre mise en route, initialisation, occupé (e) et arrêt

Cette condition peut indiquer un problème avec votre fichier de code, package ou la configuration d’application. Dans ce cas, vous devez être en mesure d’afficher le statut de modification de quelques minutes et le portail Azure peut dire quelque chose comme **recyclage**, **occupé (e)**ou **initialisation**. Cela indique qu’il ne répond pas l’application qui empêche l’instance de rôle en cours d’exécution.

Pour plus d’informations sur la façon de résoudre les problèmes pour résoudre ce problème, consultez le blog [PaaS Azure calculer des données de diagnostic](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) et les [problèmes courants qui provoquent des rôles vers la Corbeille](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problème : Mon application a cessé de fonctionner

1. Dans le portail Azure, cliquez sur l’instance de rôle.
2. Dans le volet **Propriétés** du portail Azure, considérez les conditions suivantes pour résoudre votre problème :
   * Si l’instance de rôle a cessé de récemment (vous pouvez vérifier la valeur de **abandonner count**), le déploiement peut être mise à jour. Patientez pour voir si l’instance de rôle reprend fonctionne sur sa propre.
   * Si l’instance de rôle est **occupé (e)**, vérifiez votre code d’application pour voir si l’événement [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck) est géré. Vous devrez peut-être ajouter ou corriger du code qui gère cet événement.
   * Parcourez les données de diagnostic et les scénarios résolution des problèmes dans le billet de blog [PaaS Azure calculer des données de diagnostic](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

>[AZURE.WARNING] Si vous Corbeille votre service cloud, vous rétablir les propriétés pour le déploiement, effacement efficacement les informations pour le problème d’origine.

## <a name="next-steps"></a>Étapes suivantes

Afficher d’autres [articles de résolution des problèmes](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) pour les services en nuage.

Pour savoir comment résoudre les problèmes de rôle de service cloud à l’aide de données de diagnostic Azure PaaS ordinateur, voir [la série de Kevin Williamson blog](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
