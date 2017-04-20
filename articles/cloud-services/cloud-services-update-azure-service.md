<properties
pageTitle="Comment mettre à jour un service cloud | Microsoft Azure"
description="Découvrez comment mettre à jour des services cloud dans Azure. Découvrez comment une mise à jour sur un service cloud commence à assurer la disponibilité."
services="cloud-services"
documentationCenter=""
authors="Thraka"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="08/10/2016"
ms.author="adegeo"/>

# <a name="how-to-update-a-cloud-service"></a>Comment mettre à jour un service cloud

## <a name="overview"></a>Vue d’ensemble
10 000 pieds, mise à jour d’un service cloud, y compris ses rôles et le système d’exploitation, invité inclut un processus en trois étapes. Tout d’abord, les fichiers binaires et configuration pour la version de système d’exploitation ou un service en nuage doivent être téléchargés. Ensuite, Azure réserve ressources informatiques et de réseau pour le service de nuage basé sur la configuration requise de la nouvelle version de service cloud. Pour finir, Azure effectue une mise à niveau pour par incréments mettre à jour le client à la nouvelle version ou invité du système d’exploitation, tout en conservant votre disponibilité. Cet article décrit les détails de cette dernière étape – la mise à niveau propagée.

## <a name="update-an-azure-service"></a>Mettre à jour un Service d’Azure

Azure organise vos instances de rôles en groupes logiques appelés domaines mise à niveau (UD). Mise à niveau domaines (UD) sont des jeux de logique d’instances de rôle sont mis à jour en tant que groupe.  Mises à jour Azure un cloud service une UD à la fois, qui permet aux instances dans d’autres UDs afin de continuer le trafic.

Le nombre de domaines mise à niveau par défaut est 5. Vous pouvez spécifier un nombre différent de domaines mise à niveau en incluant l’attribut upgradeDomainCount du fichier de définition du service (.csdef). Pour plus d’informations sur l’attribut upgradeDomainCount, voir [WebRole schéma](https://msdn.microsoft.com/library/azure/gg557553.aspx) ou [WorkerRole](https://msdn.microsoft.com/library/azure/gg557552.aspx).

Lorsque vous effectuez une mise à jour de la place d’un ou plusieurs rôles dans votre service, Azure met à jour les jeux d’instances de rôle en fonction de la mise à niveau domaine auquel ils appartiennent. Azure mises à jour toutes les instances dans un domaine de mise à niveau donné – les arrêter, les mettre à jour, en bénéficiant les sauvegarder en ligne – se place ensuite sur le domaine suivant. En arrêt uniquement les instances en cours d’exécution dans le domaine de mise à niveau actuel, Azure permet de s’assurer qu’une mise à jour se produit avec le moins possible d’impact du service en cours d’exécution. Pour plus d’informations, voir [comment la mise à jour commence](#howanupgradeproceeds) plus loin dans cet article.

> [AZURE.NOTE] Tandis que les termes de **mettre à jour** et **mettre à niveau** ont une signification légèrement différente dans le contexte Azure, ils peuvent être utilisés manière interchangeable pour les processus et les descriptions des fonctionnalités de ce document.

Votre service doit définir au moins deux instances d’un rôle pour ce rôle à mettre à jour sur place sans interruption de service. Si le service est constitué d’une instance d’un rôle, votre service n’est pas disponible jusqu'à ce que la mise à jour sur place terminée.

Cette rubrique couvre les informations suivantes sur les mises à jour Azure :

-   [Autorisées apportées au service pendant une mise à jour](#AllowedChanges)
-   [Comment s’effectue une mise à niveau](#howanupgradeproceeds)
-   [Annulation d’une mise à jour](#RollbackofanUpdate)
-   [Lancement de plusieurs opérations mutants sur un déploiement en cours](#multiplemutatingoperations)
-   [Distribution des rôles domaines mise à niveau](#distributiondfroles)

<a name="AllowedChanges"></a>
## <a name="allowed-service-changes-during-an-update"></a>Autorisées apportées au service pendant une mise à jour
Le tableau suivant indique les modifications autorisées à un service au cours d’une mise à jour :

|Modifications autorisées d’hébergement, les services et les rôles|Mise à jour sur place|Intermédiaire (permutation VIP)|Supprimer et redéployez|
|---|---|---|---|
|Version du système d’exploitation|Oui|Oui|Oui
|Niveau de confiance .NET|Oui|Oui|Oui|
|Machine virtuelle taille<sup>1</sup>|Oui<sup>2</sup>|Oui|Oui|
|Paramètres de stockage local|Augmenter uniquement<sup>2</sup>|Oui|Oui|
|Ajouter ou supprimer des rôles dans un service|Oui|Oui|Oui|
|Nombre d’instances d’un rôle particulier|Oui|Oui|Oui|
|Type nombre ou des points de terminaison d’un service|Oui<sup>2</sup>|N°|Oui|
|Noms et les valeurs de paramètres de configuration|Oui|Oui|Oui|
|Valeurs (mais pas les noms) des paramètres de configuration|Oui|Oui|Oui|
|Ajouter des certificats|Oui|Oui|Oui|
|Modifier les certificats existants|Oui|Oui|Oui|
|Déploiement de nouveau code|Oui|Oui|Oui|
<sup>1</sup> Modification de la taille limitée au sous-ensemble de tailles disponibles pour le service cloud.

<sup>2</sup> Nécessite Azure SDK 1.5 ou versions ultérieures.

> [AZURE.WARNING] Modifier la taille de la machine virtuelle est destroy données locales.


Les éléments suivants ne sont pas pris en charge pendant une mise à jour :

-   Modification du nom d’un rôle. Supprimer, puis ajoutez le rôle avec le nouveau nom.
-   Modification du nombre de mise à niveau de domaine.
-   Réduire la taille des ressources locales.

Si vous devez apporter d’autres mises à jour à définition de votre service, telles que réduire la taille des ressources locales, vous devez effectuer une mise à jour de permutation VIP à la place. Pour plus d’informations, voir [Déploiement échanger](https://msdn.microsoft.com/library/azure/ee460814.aspx).

<a name="howanupgradeproceeds"></a>
## <a name="how-an-upgrade-proceeds"></a>Comment s’effectue une mise à niveau
Vous pouvez décider si vous souhaitez mettre à jour tous les rôles dans votre service ou un seul rôle dans le service. Dans les deux cas, toutes les instances de chaque rôle qui est mis à niveau et appartiennent à la mise à niveau premier domaine sont arrêtés mis à niveau et remis en ligne. Une fois qu’ils sont en ligne, les instances dans le deuxième domaine de mise à niveau sont arrêtés, mis à niveau et remis en ligne. Un service cloud peut avoir au moins une mise à niveau active à la fois. La mise à niveau est toujours effectuée par rapport à la dernière version du service cloud.

Le diagramme suivant illustre l’exécution de la mise à niveau si vous mettez à jour tous les rôles dans le service :

![Mise à niveau de service] (media/cloud-services-update-azure-service/IC345879.png "Mise à niveau de service")

Ce diagramme suivant illustre l’exécution de la mise à jour si vous mettez à niveau un seul rôle uniquement :

![Rôle de mise à niveau] (media/cloud-services-update-azure-service/IC345880.png "Rôle de mise à niveau")  

> [AZURE.NOTE] Lorsque la mise à niveau d’un service à partir d’une seule instance à plusieurs instances votre service s’affiche vers le bas en pendant la mise à niveau est exécutée en raison des manière dont les services Azure mises à niveau. La disponibilité de service garantissant du contrat de niveau de service s’applique uniquement aux services qui sont déployées avec plusieurs instances. La liste suivante décrit comment les données sur chaque lecteur sont affectées par chaque scénario de mise à niveau du service Azure :
>
>Machine virtuelle redémarrer :
>
-   C : conservés
-   D : conservés
-   E : conservés
>
>Redémarrez portail :
>
-   C : conservés
-   D : conservés
-   E : destruction
>
>Créer une nouvelle image portail :
>
-   C : conservés
-   D : destruction
-   E : destruction

>Mise à niveau en Place :
>
-   C : conservés
-   D : conservés
-   E : destruction
>
>Migration de nœud :
>
-   C : destruction
-   D : destruction
-   E : destruction

>Notez que, dans la liste ci-dessus, le lecteur représente le lecteur racine du rôle et ne doit pas être codé en dur. À la place, utilisez la variable d’environnement % RoleRoot pour représenter le lecteur.

>Pour réduire le temps d’arrêt lors de la mise à niveau d’un service uniques, déployer un nouveau service plusieurs instances sur le serveur intermédiaire et effectuer une permutation VIP.

Au cours d’une mise à jour automatique, contrôleur de tissu Azure évalue régulièrement l’intégrité du service cloud pour déterminer quand il est suffisamment parcourir le UD suivant. Cette évaluation santé est effectuée sur une base par rôle et considère uniquement les instances dans la version la plus récente (c'est-à-dire les instances de UDs qui ont déjà été parcourus). Il vérifie si un nombre minimal d’instances de rôle, pour chaque rôle, ont permis d’atteindre un état terminal satisfaisant.

### <a name="role-instance-start-timeout"></a>Délai de démarrage Instance de rôle
Contrôleur de tissu attend 30 minutes pour chaque instance de rôle d’atteindre un état de mise en route. Si la durée de délai d’attente s’écoule, contrôleur de tissu continuera à remonter au rôle suivant.

<a name="RollbackofanUpdate"></a>
## <a name="rollback-of-an-update"></a>Annulation d’une mise à jour
Azure offre une flexibilité dans la gestion des services au cours d’une mise à jour en vous permettant de lancer des opérations supplémentaires sur un service, une fois que la requête mise à jour initiale est acceptée par le contrôleur de tissu Azure. Une annulation ne peut être effectuée lorsqu’une mise à jour (modification de la configuration) ou mise à niveau est à l’état **en cours** sur le déploiement. Une mise à jour ou la mise à niveau est considéré comme en cours dans la mesure où il existe au moins une instance du service qui n’a pas encore été mis à jour vers la nouvelle version. Pour tester si une annulation est autorisée, vérifiez la valeur de l’indicateur RollbackAllowed, renvoyée par les opérations [Obtenir le déploiement](https://msdn.microsoft.com/library/azure/ee460804.aspx) et [d’Obtenir les propriétés de Service Cloud](https://msdn.microsoft.com/library/azure/ee460806.aspx) , est défini sur true.

> [AZURE.NOTE] Il est uniquement préférable d’appeler retour arrière sur une mise à jour **sur place** ou mettre à niveau car les mises à niveau VIP permutation impliquent le remplacement d’une instance en cours d’exécution entière de votre service à un autre.

Annulation d’une mise à jour en cours comporte des effets suivants sur le déploiement :

-   Toutes les instances de rôle qui n'avaient pas encore été mis à jour ou mis à niveau vers la nouvelle version ne sont pas mis à jour ou mis à niveau, car ces instances s’exécutent déjà la version cible du service.
-   Toutes les instances de rôle qui avaient déjà été mis à jour ou mis à niveau vers la nouvelle version du package de service (\*.cspkg) fichier ou la configuration du service (\*.cscfg) fichier (ou les deux fichiers) sont remis à la version de pré-mise à niveau de ces fichiers.

Cette fonctionnalité est fournie par les fonctionnalités suivantes :

-   L’opération [Restauration mettre à jour ou mettre à niveau](https://msdn.microsoft.com/library/azure/hh403977.aspx) , qui peut être appelée sur une mise à jour de configuration (déclenchés par appelant [Modifier la Configuration déploiement](https://msdn.microsoft.com/library/azure/ee460809.aspx)) ou une mise à niveau (déclenchés par appelant [Déploiement mise à niveau](https://msdn.microsoft.com/library/azure/ee460793.aspx)) dans la mesure où il existe au moins une instance dans le service qui n’a pas encore été mis à jour vers la nouvelle version.
-   L’élément verrouillé et l’élément RollbackAllowed, qui sont retournés dans le cadre du corps du réponse opérations de [Déploiement obtenir](https://msdn.microsoft.com/library/azure/ee460804.aspx) et [Obtenir les propriétés de Service Cloud](https://msdn.microsoft.com/library/azure/ee460806.aspx) :
    1.  L’élément verrouillé permet de détecter le moment où une opération de mutants peut être appelée sur un déploiement donné.
    2.  L’élément RollbackAllowed permet de détecter le moment où l’opération [d’Annulation mise à jour ou mettre à niveau](https://msdn.microsoft.com/library/azure/hh403977.aspx) peut être appelée sur un déploiement donné.

    Pour effectuer une annulation, vous n’êtes pas obligé de vérifier la verrouillé et les éléments RollbackAllowed. Il suffit pour confirmer que RollbackAllowed est défini sur true. Ces éléments sont retournés uniquement si ces méthodes sont appelées à l’aide de l’en-tête de demande défini sur « x-ms-version : 2011-10-01 » ou une version ultérieure. Pour plus d’informations sur les en-têtes de contrôle de version, voir [Service de gestion des versions](https://msdn.microsoft.com/library/azure/gg592580.aspx).

Il existe certaines situations dans lesquelles une annulation d’une mise à jour ou mise à niveau n’est pas pris en charge, celles-ci sont les suivantes :

-   Réduction des ressources locales - si la mise à jour augmente les ressources locales pour un rôle la plateforme Azure n’autorise pas l’annulation. 
-   Limitations de quota - si la mise à jour une échelle interrompre les opérations que vous ne pouvez n’est plus a suffisamment quota cluster pour terminer l’opération de restauration. Chaque abonnement Azure a un quota associé qui spécifie le nombre maximal de cœurs pouvant être utilisés par tous les services hébergés appartenant à cet abonnement. Si effectuer une annulation d’une mise à jour donnée risque de placer votre abonnement sur quota puis qu’annulation ne sera pas activée.
-   Condition de concurrence - si la mise à jour initiale est terminée, une annulation n’est pas possible.

Il est un exemple de quand la restauration d’une mise à jour peut être utile si vous utilisez l’opération de [Déploiement de mise à niveau](https://msdn.microsoft.com/library/azure/ee460793.aspx) en mode manuel pour contrôler la fréquence à laquelle une mise à niveau en place principal votre Azure hébergé service est transférée.

Lors du déploiement de la mise à niveau vous appelez [Déploiement mise à niveau](https://msdn.microsoft.com/library/azure/ee460793.aspx) en mode manuel et commencez à parcourir les domaines mise à niveau. Si à un moment donné, en tant que vous contrôler la mise à niveau, notez amené rôle dans les domaines de mise à niveau premières que vous examinez est devenus ne répond pas, vous pouvez appeler l’opération [Restauration mise à jour ou mettre à niveau](https://msdn.microsoft.com/library/azure/hh403977.aspx) sur le déploiement, ce qui laisse intacts les instances qui n’avaient pas encore été mis à niveau et la restauration qui a été mis à niveau pour la configuration et le package de service précédent.

<a name="multiplemutatingoperations"></a>
## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Lancement de plusieurs opérations mutants sur un déploiement en cours
Dans certains cas, vous souhaiterez lancer plusieurs opérations mutants simultanées sur un déploiement en cours. Par exemple, vous pouvez effectuer une mise à jour de service et, tandis que cette mise à jour est déployée au sein de votre service, que vous voulez apporter des modifications, par exemple, pour restaurer la mise à jour précédent, appliquez une autre mise à jour ou même supprimer le déploiement. Un cas dans lequel il peut s’avérer nécessaire est si une mise à niveau du service contient code incorrect entraînant une instance de rôle mis à niveau à plusieurs reprises arrêté. Dans ce cas, le contrôleur de tissu Azure ne sera pas en mesure d’établir l’avancement dans l’application mise à niveau, car un nombre d’instances du domaine mis à niveau insuffisant fonctionnent correctement. Cet état est appelé un *bloqué déploiement*. Vous pouvez décoller le déploiement en annulation de la mise à jour ou en appliquant une mise à jour novateur au-dessus de l’échec d’une.

Une fois que la requête initiale à mettre à jour ou mettre à niveau du service a été reçue par le contrôleur de tissu Azure, vous pouvez démarrer mutants les opérations suivantes. En d’autres termes, vous n’avez pas d’attendre que l’opération initiale se termine avant de démarrer une autre opération mutants.

Lancer une opération de mise à jour deuxième alors que la première mise à jour est en cours effectuera similaire à l’opération de restauration. Si la deuxième mise à jour est en mode automatique, le premier domaine de mise à niveau mise à niveau immédiatement, et éventuellement causer instances de plusieurs domaines mise à niveau en cours en mode hors connexion au même point dans le temps.

Les opérations mutants sont les suivantes : [Modifier la Configuration déploiement](https://msdn.microsoft.com/library/azure/ee460809.aspx), mettre à niveau le [Déploiement](https://msdn.microsoft.com/library/azure/ee460793.aspx), [État de déploiement de mise à jour](https://msdn.microsoft.com/library/azure/ee460808.aspx), [Supprimer le déploiement](https://msdn.microsoft.com/library/azure/ee460815.aspx)et [Restauration mise à jour ou mettre à niveau](https://msdn.microsoft.com/library/azure/hh403977.aspx).

Deux opérations, [Obtenir le déploiement](https://msdn.microsoft.com/library/azure/ee460804.aspx) et [Obtenir les propriétés de Service Cloud](https://msdn.microsoft.com/library/azure/ee460806.aspx), retournent l’indicateur verrouillé qui peut être examiné pour déterminer si une opération de mutants peut être appelée sur un déploiement donné.

Pour appeler la version de ces méthodes qui retourne l’indicateur verrouillé, vous devez définir en-tête de la demande « x-ms-version : 2011-10-01 » ou une version ultérieure. Pour plus d’informations sur les en-têtes de contrôle de version, voir [Service de gestion des versions](https://msdn.microsoft.com/library/azure/gg592580.aspx).

<a name="distributiondfroles"></a>
## <a name="distribution-of-roles-across-upgrade-domains"></a>Distribution des rôles domaines mise à niveau
Azure répartit uniformément instances d’un rôle sur un nombre défini de domaines mise à niveau, qui peut être configuré dans le cadre du fichier de définition (.csdef) du service. Le nombre maximal de domaines mise à niveau est 20 et la valeur par défaut est 5. Pour plus d’informations sur la façon de modifier le fichier de définition de service, voir [Schéma de définition du Service Azure (fichier .csdef)](cloud-services-model-and-package.md#csdef).

Par exemple, si votre rôle possède des dix instances, par défaut chaque domaine mise à niveau contient deux instances. Si votre rôle possède 14 instances, puis quatre des domaines mise à niveau contiennent trois instances et un domaine cinquième contient deux.

Mise à niveau domaines sont identifiés avec un indice de base zéro : le premier domaine de mise à niveau a un ID de 0 et la deuxième domaine mise à niveau a un ID de 1 et ainsi de suite.

Le diagramme suivant illustre la répartition d’un service qui contient deux rôles lorsque le service définit deux domaines mise à niveau. Le service est en cours d’exécution huit instances de ce rôle web et neuf du rôle de collaborateur.

![Distribution de domaines mise à niveau] (media/cloud-services-update-azure-service/IC345533.png "Distribution de domaines mise à niveau")

> [AZURE.NOTE] Notez que Azure contrôles comment les instances sont réparties sur plusieurs domaines mise à niveau. Il n’est pas possible de spécifier les instances sont affectés à chaque domaine.

## <a name="next-steps"></a>Étapes suivantes
[Comment gérer les Services en nuage](cloud-services-how-to-manage.md)  
[Comment faire pour surveiller les Services en nuage](cloud-services-how-to-monitor.md)  
[Comment faire pour configurer les Services en nuage](cloud-services-how-to-configure.md)  
