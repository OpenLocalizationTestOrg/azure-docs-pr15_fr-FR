<properties
    pageTitle="Résolution des problèmes échec d’allocation de Service Cloud | Microsoft Azure"
    description="Résolution des problèmes échec d’allocation lorsque vous déployez des Services Cloud dans Azure"
    services="azure-service-management, cloud-services"
    documentationCenter=""
    authors="simonxjx"
    manager="felixwu"
    editor=""
    tags="top-support-issue"/>

<tags
    ms.service="cloud-services"
    ms.workload="na"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="v-six"/>



# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Résolution des problèmes échec d’allocation lorsque vous déployez des Services Cloud dans Azure

## <a name="summary"></a>Résumé
Lorsque vous déployez instances sur un Service Cloud ou ajoutez le nouveau site web ou des instances de rôle de collaborateur, Microsoft Azure attribue des ressources de calcul. Que vous receviez erreurs lors de l’exécution de ces opérations même avant d’atteindre les limites de l’abonnement Azure. Cet article explique les causes de certaines des échecs d’allocation courantes et suggère conversion possible. Les informations peuvent également être utiles lorsque vous planifiez le déploiement de vos services.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Arrière-plan – fonctionne de répartition
Les serveurs de centres de données Azure sont répartis dans clusters. Une nouvelle demande d’allocation de service cloud est tentée dans plusieurs clusters. Lorsque la première instance est déployée sur un service cloud (dans l’environnement intermédiaire ou production), qui cloud service obtient épinglée à un cluster. Les déploiements supplémentaires pour le service cloud seront produit dans le même cluster. Dans cet article, nous allons faire référence à ce que « épinglée à un cluster ». Diagramme 1 ci-après illustre le cas d’une allocation normale qui est tenté dans multi-clusters ; Diagramme 2 illustre le cas d’une affectation qui a épinglées à 2 Cluster car il s’agit de l’endroit où le CS_1 Service Cloud existant est hébergé.

![Diagramme de répartition](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Pourquoi se produit-il Échec d’allocation
Quand une demande d’allocation est épinglée à un cluster, il existe plus de risque de ne parvient pas à trouver libérer des ressources dans la mesure où la liste des ressources disponibles est limitée à un cluster. En outre, si votre demande d’allocation est épinglée à un cluster mais que le type de ressource que vous avez demandé n’est pas pris en charge par ce cluster, votre demande échouera même si le cluster a ressources gratuit. Diagramme 3 ci-dessous montre le cas où une allocation en attente échoue, car le cluster candidate uniquement n’a pas de libérer des ressources. Diagramme 4 illustre le cas où une allocation en attente échoue, car le cluster candidate uniquement ne reconnaît pas la taille de mémoire virtuelle demandée, même si le cluster a libérer des ressources.

![Échec d’Allocation épinglé](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Échec d’allocation de dépannage pour les services en nuage
### <a name="error-message"></a>Message d’erreur
Vous pouvez voir le message d’erreur suivant :

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Problèmes courants
Voici les scénarios courants allocation occasionnant une demande d’allocation à être épinglée à un seul cluster.

- Déployer module de transfert - si un service cloud comporte un déploiement dans les deux emplacements, puis le service cloud entière est épinglé à un cluster spécifique.  Cela signifie que si un déploiement déjà existe dans l’emplacement de production, puis un nouveau déploiement intermédiaire peut uniquement être affecté dans le même cluster que l’emplacement de production. Si le cluster est pratiquement la capacité, la requête peut échouer.

- Mise à l’échelle : ajout de nouvelles instances à un service cloud existant doit allouer dans le même cluster.  Petite échelle demandes généralement peut être alloué, mais pas toujours. Si le cluster est pratiquement la capacité, la requête peut échouer.

- Groupe affinité - un nouveau déploiement d’un service cloud vide pouvant être alloué par le tissu dans n’importe quel cluster dans cette zone, à moins que le service en nuage est épinglé à un groupe affinité. Déploiements sur le même groupe affinité tente sur le même cluster. Si le cluster est pratiquement la capacité, la requête peut échouer.

- Groupe affinité vNet - anciens réseaux virtuels ont été liés au affinités au lieu de régions et services de cloud dans ces réseaux virtuels seraient être épinglées au cluster affinité du groupe. Déploiements à ce type de réseau virtuel tente sur le cluster épinglé. Si le cluster est pratiquement la capacité, la requête peut échouer.

## <a name="solutions"></a>Solutions

1. Redéployez à un nouveau service cloud : cette solution est susceptible d’être plus performantes car elle permet de la plateforme choisir parmi tous les groupes de dans cette zone.

    - Déployer la charge de travail sur un service en nuage  

    - Mettre à jour l’enregistrement CNAME ou un enregistrement pour faire pointer le trafic vers le nouveau service cloud

    - Une fois que l’ancien site va zéro le trafic, vous pouvez supprimer l’ancien service cloud. Cette solution doit subir aucune interruption de service.

2. Suppression de production et emplacements de mise en attente : cette solution conserve votre nom de DNS existant, mais va entraîner l’interruption à votre application.

    - Supprimer la production et les intermédiaires emplacements d’un service cloud existant afin que le service en nuage est vide, puis

    - Créer un nouveau déploiement dans le service cloud existant. Il ré-tentera allocation sur tous les groupes de dans la région. Vérifiez que le service en nuage n’est pas lié à un groupe affinité.

3. IP réservée - cette solution conserve votre adresse IP existant corrige, mais qui va entraîner l’interruption à votre application.  

    - Créer une adresse IP réservée pour votre déploiement existant à l’aide de Powershell

    ```
    New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
    ```

    - Suivez #2 point situé au-dessus, en prenant soin de spécifier l’adresse IP réservée nouveau dans CSCFG du service.

4. Supprimer le groupe affinité pour les nouveaux déploiements - affinités sont recommandés n’est plus. Suivez les étapes pour 1 # ci-dessus pour déployer un nouveau service cloud. Vérifiez que service cloud n’est pas dans un groupe d’affinité.

5. Convertir un réseau virtuel régionaux - voir [comment effectuer une migration à partir de groupes affinité à un réseau virtuel régionaux (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
