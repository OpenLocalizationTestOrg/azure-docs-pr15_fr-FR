<properties
   pageTitle="Créer un équilibrage de charge interne pour les services en nuage dans le modèle de déploiement classique | Microsoft Azure"
   description="Apprenez à créer un équilibrage de charge interne à l’aide de PowerShell dans le modèle de déploiement classique"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internal-load-balancer-classic-for-cloud-services"></a>Commencer la création d’un programme d’équilibrage de charge interne (classique) pour les services en nuage

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

<BR>

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](load-balancer-get-started-ilb-arm-ps.md).


## <a name="configure-internal-load-balancer-for-cloud-services"></a>Configurer l’équilibrage de charge interne pour les services en nuage

Équilibrage de charge interne est pris en charge pour les machines virtuelles et services de cloud. Un point de terminaison d’équilibrage de charge interne créé dans un service cloud qui se trouve en dehors d’un réseau virtuel régionaux seront accessible uniquement dans le service cloud.

La configuration d’équilibrage de charge interne doit être définie lors de la création du premier déploiement dans le service cloud, comme illustré dans l’exemple ci-dessous.

>[AZURE.IMPORTANT] Un réseau virtuel déjà créé pour le déploiement de cloud est requis pour exécuter la procédure ci-dessous. Vous devez le nom de réseau virtuel et sous-réseau pour créer l’équilibrage de charge interne.

### <a name="step-1"></a>Étape 1

Ouvrez le fichier de configuration de service (.cscfg) pour votre déploiement de cloud dans Visual Studio et ajoutez la section suivante pour créer l’équilibrage de charge interne sous la dernière «`</Role>`« élément pour la configuration du réseau.




    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="name of the load balancer">
          <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>


Nous allons ajouter les valeurs pour le fichier de configuration réseau afficher l’aspect final. Dans l’exemple, supposons que vous avez créé un sous-réseau appelé « test_vnet » avec un sous-réseau 10.0.0.0/24 intitulé test_subnet et une adresse IP statique 10.0.0.4. L’équilibrage de charge sera nommé testLB.

    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="testLB">
          <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>

Pour plus d’informations à propos du schéma d’équilibrage de charge, voir [Ajouter l’équilibrage de charge](https://msdn.microsoft.com/library/azure/dn722411.aspx).

### <a name="step-2"></a>Étape 2


Modifier le fichier de définition (.csdef) service pour ajouter des points de terminaison à l’équilibrage de charge interne. Au moment où qu'une instance de rôle est créée, le fichier de définition de service ajoute les instances de rôles à l’équilibrage de charge interne.


    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
      </Endpoints>
    </WorkerRole>

Suivre les mêmes valeurs de l’exemple ci-dessus, nous allons ajouter les valeurs dans le fichier de définition de service.

    <WorkerRole name=WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
      </Endpoints>
    </WorkerRole>

Le trafic réseau sera charge équilibrée à l’aide de l’équilibrage de charge testLB utilise le port 80 pour les demandes entrantes, envoi sur des instances de rôle de collaborateur également sur le port 80.


## <a name="next-steps"></a>Étapes suivantes

[Configurer un mode de distribution d’équilibrage de charge à l’aide de l’affinité IP source](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’expiration TCP inactives pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)