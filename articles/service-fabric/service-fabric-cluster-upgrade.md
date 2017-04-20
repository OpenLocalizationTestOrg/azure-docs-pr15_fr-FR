<properties
   pageTitle="Mettre à niveau un cluster Azure Service TISSU | Microsoft Azure"
   description="Mettre à niveau le code de Service tissu et/ou une configuration qui s’exécute un cluster tissu de Service, y compris la définition du mode de mise à jour de cluster, la mise à niveau des certificats, ajout de ports d’application, effectuant correctifs du système d’exploitation, et ainsi de suite. Ce que vous attendiez lorsque les mises à jour sont effectuées ?"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/10/2016"
   ms.author="chackdan"/>


# <a name="upgrade-an-azure-service-fabric-cluster"></a>Mettre à niveau un cluster tissu de Service Azure

> [AZURE.SELECTOR]
- [Cluster Azure](service-fabric-cluster-upgrade.md)
- [Cluster autonome](service-fabric-cluster-upgrade-windows-server.md)

Pour les systèmes moderne, conception de la mise à jour est crucial pour atteindre un résultat positif à long terme de votre produit. Un cluster Azure Service tissu est une ressource qui vous êtes propriétaire, mais partiellement géré par Microsoft. Cet article décrit ce qui est géré automatiquement et que vous pouvez configurer vous-même.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Contrôle de la version de tissu qui s’exécute sur votre Cluster

Vous pouvez définir votre cluster afin de recevoir des mises à jour automatiques tissu, lorsque Microsoft publie une nouvelle version ou cliquez pour sélectionner une version de tissu pris en charge que vous voulez faire figurer sur votre cluster.

Cela en définissant la configuration du cluster « upgradeMode » sur le portail ou à l’aide du Gestionnaire de ressources au moment de la création ou version ultérieure sur un cluster live 

>[AZURE.NOTE] Veillez à conserver votre cluster exécutant une version prise en charge TISSU toujours. Au fur et nous annoncer la sortie d’une nouvelle version de tissu de service, la version précédente est marquée après un minimum de 60 jours à partir de cette date de fin de prise en charge. la les nouvelles versions sont annoncé [sur le blog de l’équipe TISSU service](https://blogs.msdn.microsoft.com/azureservicefabric/ ). La nouvelle version n’est disponible pour choisir puis. 

14 jours avant l’expiration de la version de que votre cluster est en cours d’exécution, un événement d’état est généré et place votre cluster dans un état d’intégrité avertissement. Le cluster reste dans un état d’avertissement jusqu'à ce que vous mettez à niveau vers une version TISSU pris en charge.


### <a name="setting-the-upgrade-mode-via-portal"></a>Définition du mode de mise à niveau via le portail 

Vous pouvez définir le cluster sur automatique ou manuel lorsque vous créez le cluster.

![Create_Manualmode][Create_Manualmode]

Vous pouvez définir le cluster sur automatique ou manuel lorsque sur un cluster live, à l’aide de l’expérience de gérer. 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>La mise à niveau vers une nouvelle version sur un cluster qui est défini en mode manuel via le portail.
 
Pour mettre à niveau vers une nouvelle version, il vous souhaitez est sélectionner la version disponible dans le menu déroulant et enregistrer. La mise à niveau TISSU obtient lancée automatiquement. Les stratégies d’intégrité cluster (une combinaison de nœud État et la santé toutes les applications qui s’exécutent dans le cluster) sont respectées pendant la mise à niveau.

Si les stratégies d’intégrité cluster ne sont pas remplies, la mise à niveau est annulée. Défilement vers le bas de ce document pour en savoir plus sur la façon de définir les stratégies d’intégrité personnalisé. 

Une fois que vous avez corrigé les problèmes qui a donné lieu à l’annulation, vous devez démarrer la mise à niveau à nouveau, en suivant les mêmes étapes qu’avant.

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>Définition du mode de mise à niveau via un modèle de gestionnaire de ressources 

Ajouter la configuration de « upgradeMode » à la définition de ressources Microsoft.ServiceFabric/clusters et définir la « clusterCodeVersion » à l’une des versions TISSU pris en charge, comme indiqué ci-dessous et puis déployez le modèle. Les valeurs valides pour « upgradeMode » sont « Automatique » ou « Manuel »
 
![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>La mise à niveau vers une nouvelle version sur un cluster qui est défini en mode manuel via un modèle de gestionnaire de ressources.
 
Lorsque le cluster est en mode manuel, pour mettre à niveau vers une nouvelle version, modifier la « clusterCodeVersion » pour une version prise en charge et déployez. Le déploiement du modèle, plaisir de la mise à niveau TISSU obtient lancée automatiquement. Les stratégies d’intégrité cluster (une combinaison de nœud État et la santé toutes les applications qui s’exécutent dans le cluster) sont respectées pendant la mise à niveau.

Si les stratégies d’intégrité cluster ne sont pas remplies, la mise à niveau est annulée. Défilement vers le bas de ce document pour en savoir plus sur la façon de définir les stratégies d’intégrité personnalisé. 

Une fois que vous avez corrigé les problèmes qui a donné lieu à l’annulation, vous devez démarrer la mise à niveau à nouveau, en suivant les mêmes étapes qu’avant.

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>Obtenir la liste de tous les version disponible pour tous les environnements pour un abonnement donné

Exécutez la commande suivante, puis vous devez obtenir un résultat semblable à celui-ci.

« supportExpiryUtc » indique votre lorsqu’une version donnée arrive à expiration ou a expiré. La version la plus récente n’a pas une date valide - elle a la valeur « 9999-12-31T23:59:59.9999999 », ce qui signifie simplement que la date d’expiration n’est pas encore définie.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/clusterVersions?api-version= 2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }


```

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>Comportement de mise à niveau TISSU lorsque le Mode de mise à niveau de cluster est défini sur automatique

Microsoft met à jour le code de la structure et la configuration qui s’exécute dans un cluster Azure. Nous effectuer une mise à jour automatique de contrôlé au logiciel sur selon vos besoins. Ces mises à niveau peuvent code, la configuration ou les deux. Pour vous assurer que votre application ne connaît aucun ou impact minimal en raison de ces mises à niveau, nous effectuer les mises à niveau dans les phases suivantes :

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Phase 1 : Une mise à niveau est effectuée à l’aide de toutes les stratégies d’intégrité cluster

Au cours de cette phase, les mises à niveau passez un domaine de mise à niveau à la fois, et les applications qui ont été exécutés dans le cluster continuent à exécuter sans interruption de service. Les stratégies d’intégrité cluster (une combinaison de nœud État et la santé toutes les applications qui s’exécutent dans le cluster) sont respectées pendant la mise à niveau.

Si les stratégies d’intégrité cluster ne sont pas remplies, la mise à niveau est annulée. Puis un message électronique est envoyé au propriétaire de l’abonnement. Le courrier électronique contient les informations suivantes :

- Notification que nous avons dû restaurer une mise à niveau de cluster.
- Suggéré remédier, le cas échéant.
- Le nombre de jours (n) jusqu'à ce que nous exécuter Phase 2.

Nous essayez d’exécuter la mise à niveau même quelques heures supplémentaires au cas où les mises à niveau a échoué pour des raisons de l’infrastructure. Après les n jours entre la date de que la messagerie a été envoyé, nous passer à la Phase 2.

Si les stratégies d’intégrité cluster sont remplies, la mise à niveau est considérée comme réussie et marquée comme terminée. Cela peut se produire pendant la mise à niveau initiale ou une des mise à niveau rediffusions de cette phase. Il n’existe aucune confirmation par courrier électronique d’une exécution réussie. Il s’agit de ne pas envoyer vous trop de messages électroniques--recevoir un message électronique devez être considérée comme une exception normale. Nous prévoyons la plupart des mises à niveau cluster se dérouler sans ayant un impact sur votre disponibilité des applications.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Phase 2 : Une mise à niveau est effectuée à l’aide de stratégies d’intégrité par défaut uniquement

Les stratégies d’intégrité de cette phase sont définies de façon à ce que le nombre d’applications qui ont été exact au début de la mise à niveau reste la même pendant la durée du processus de mise à niveau. Selon la Phase 1, la mise à niveau de la Phase 2 passez un domaine de mise à niveau à la fois, et les applications qui ont été exécutés dans le cluster continuent à exécuter sans interruption de service. Les stratégies d’intégrité cluster (une combinaison de nœud État et la santé toutes les applications qui s’exécutent dans le cluster) sont respectées pendant la durée de la mise à niveau.

Si les stratégies d’intégrité cluster en vigueur ne sont pas respectés, la mise à niveau est annulée. Puis un message électronique est envoyé au propriétaire de l’abonnement. Le courrier électronique contient les informations suivantes :

- Notification que nous avons dû restaurer une mise à niveau de cluster.
- Suggéré remédier, le cas échéant.
- Le nombre de jours (n) jusqu'à ce que nous exécuter Phase 3.

Nous essayez d’exécuter la mise à niveau même quelques heures supplémentaires au cas où les mises à niveau a échoué pour des raisons de l’infrastructure. Un message de rappel est envoyé quelques jours avant les n jours. Après les n jours entre la date de que la messagerie a été envoyé, nous passer à la Phase 3. Les messages électroniques que nous vous envoyons à la Phase 2 doivent être prises au sérieux et remédier doit être prises.

Si les stratégies d’intégrité cluster sont remplies, la mise à niveau est considérée comme réussie et marquée comme terminée. Cela peut se produire pendant la mise à niveau initiale ou une des mise à niveau rediffusions de cette phase. Il n’existe aucune confirmation par courrier électronique d’une exécution réussie.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Phase 3 : Une mise à niveau est effectuée à l’aide de stratégies d’intégrité agressif

Ces stratégies d’intégrité de cette phase sont adressent à la fin de la mise à niveau plutôt que l’état des applications. Mises à niveau cluster très peu placés dans cette phase. Si votre cluster atteint cette phase, il est probable que votre application devient défectueuse et/ou perdent disponibilité.

Comme dans les deux phases, mises à niveau de la Phase 3 passez un domaine de mise à niveau à la fois.

Si les stratégies d’intégrité cluster ne sont pas remplies, la mise à niveau est annulée. Nous essayez d’exécuter la mise à niveau même quelques heures supplémentaires au cas où les mises à niveau a échoué pour des raisons de l’infrastructure. Après cela, le cluster est épinglé, afin qu’il ne recevrez plus prise en charge et/ou mises à jour.

Un message électronique avec cette information est envoyé au propriétaire d’un abonnement, ainsi que les actions de réparation. Nous ne prévoyez pas aucun cluster pour accéder à l’état dans lequel la Phase 3 a échoué.

Si les stratégies d’intégrité cluster sont remplies, la mise à niveau est considérée comme réussie et marquée comme terminée. Cela peut se produire pendant la mise à niveau initiale ou une des mise à niveau rediffusions de cette phase. Il n’existe aucune confirmation par courrier électronique d’une exécution réussie.

## <a name="cluster-configurations-that-you-control"></a>Configurations de cluster que vous contrôlez

Outre la possibilité de définir le cluster en mode de mise à niveau, voici les configurations que vous pouvez modifier sur un cluster live.

### <a name="certificates"></a>Certificats

Vous pouvez ajouter de nouveaux ou supprimer facilement des certificats pour le cluster et le client via le portail. Reportez-vous à [ce document pour obtenir des instructions détaillées](service-fabric-cluster-security-update-certs-azure.md)

![Capture d’écran montrant les empreintes de certificat dans le portail Azure.][CertificateUpgrade]


### <a name="application-ports"></a>Ports d’application

Vous pouvez modifier les ports d’application en modifiant les propriétés de ressources d’équilibrage de charge qui sont associées au type de nœud. Vous pouvez utiliser le portail, ou vous pouvez utiliser le Gestionnaire de ressources PowerShell directement.

Pour ouvrir un nouveau port sur tous les ordinateurs virtuels dans un type de nœud, procédez comme suit :

1. Ajouter une nouvelle sonde à l’équilibrage de charge approprié.

    Si vous avez déployé votre cluster à l’aide du portail, les programmes d’équilibrage de charge sont appelés « Kg-nom de la ressource groupe-NodeTypename », une pour chaque type de nœud. Étant donné que les noms d’équilibrage de charge sont uniques seulement au sein d’un groupe de ressources, il est préférable si vous recherchez les sous un groupe de ressources spécifique.

    ![Capture d’écran montrant l’ajout d’une sonde à un équilibrage de charge dans le portail.][AddingProbes]

2. Ajouter une nouvelle règle pour l’équilibrage de charge.

    Ajouter une nouvelle règle pour l’équilibrage de charge même à l’aide de la sonde que vous avez créé à l’étape précédente.

    ![Ajout d’une règle à un équilibrage de charge dans le portail.][AddingLBRules]


### <a name="placement-properties"></a>Propriétés de positionnement

Pour chacun des types de nœuds, vous pouvez ajouter des propriétés de positionnement personnalisé que vous souhaitez utiliser dans vos applications. NodeType est une propriété par défaut que vous pouvez utiliser sans l’ajouter explicitement.

>[AZURE.NOTE] Pour plus d’informations sur l’utilisation des contraintes de placement et comment définir les propriétés du nœud, reportez-vous à la section « Contraintes de Placement et propriétés nœud » dans le Document Gestionnaire de ressources Cluster Service tissu sur [Décrivant votre Cluster](service-fabric-cluster-resource-manager-cluster-description.md).

### <a name="capacity-metrics"></a>Mesures de capacité

Pour chacun des types de nœuds, vous pouvez ajouter des indicateurs de capacité personnalisé que vous souhaitez utiliser dans vos applications pour charger le rapport. Pour plus d’informations sur l’utilisation de mesures de capacité à rapport charger, reportez-vous aux Documents Gestionnaire de ressources Cluster Service tissu sur [Décrivant votre Cluster](service-fabric-cluster-resource-manager-cluster-description.md) et [indicateurs et charger](service-fabric-cluster-resource-manager-metrics.md).

### <a name="fabric-upgrade-settings---health-polices"></a>Paramètres de mise à niveau TISSU - santé stratégies

Vous pouvez spécifier l’état personnalisé stratégies de mise à niveau tissu. Si vous avez défini votre cluster aux mises à niveau automatique tissu, ces politiques obtenir appliquées à la Phase-1 de la mise à niveau automatique tissu.
Si vous avez configuré votre cluster pour tissu manuel mises à niveau, ces politiques obtenir appliqués chaque fois que vous sélectionnez une nouvelle version du système pour démarrer la mise à niveau de tissu dans votre cluster de déclenchement. Si vous ne remplacez pas les stratégies, les valeurs par défaut sont utilisés.

Vous pouvez spécifier les stratégies d’état personnalisé ou passez en revue les paramètres actuels sous la carte « mise à niveau tissu », en sélectionnant les paramètres de mise à niveau avancés. Passez en revue l’image sur la façon suivante. 

![Gérer les stratégies d’intégrité personnalisé][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>Personnaliser les paramètres de tissu pour votre cluster

Reportez-vous aux [paramètres du service TISSU cluster TISSU](service-fabric-cluster-fabric-settings.md) sur les éléments et comment les personnaliser.

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>Correctifs du système d’exploitation sur les ordinateurs virtuels qui composent le cluster

Cette fonctionnalité n’est planifiée pour l’avenir comme une fonction automatisée. Mais pour l’instant, vous êtes responsable à corriger vos ordinateurs virtuels. Vous devez faire cette machine une virtuelle à la fois, afin que vous ne prenez pas vers le bas plusieurs fois à la fois.

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>Mises à niveau du système d’exploitation sur les ordinateurs virtuels qui composent le cluster

Si vous devez mettre à niveau de l’image du système d’exploitation sur les machines virtuelles du cluster, vous devez faire une machine virtuelle à la fois. Vous êtes chargé de cette mise à niveau--il n’existe actuellement aucune automation pendant ce.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment personnaliser certains [paramètres du service TISSU cluster TISSU](service-fabric-cluster-fabric-settings.md)
- Découvrez comment faire [évoluer votre cluster et arrière](service-fabric-cluster-scale-up-down.md)
- En savoir plus sur [les mises à niveau de l’application](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG