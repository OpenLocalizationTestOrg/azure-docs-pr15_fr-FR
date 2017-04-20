<properties
   pageTitle="Créer un Internet facing équilibrage de charge dans le modèle de déploiement classique à l’aide des services en nuage | Microsoft Azure"
   description="Apprenez à créer un Internet facing équilibrage de charge dans le modèle de déploiement classique des services en nuage"
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
   ms.date="03/17/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-for-cloud-services"></a>Créer un Internet facing équilibrage de charge des services en nuage

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement classique. Vous pouvez également [découvrir comment créer un Internet facing équilibrage de charge à l’aide du Gestionnaire de ressources Azure](load-balancer-get-started-internet-arm-cli.md).

Les services en nuage sont configurés automatiquement avec un équilibrage de charge et peuvent être personnalisées via le modèle de service.

## <a name="create-a-load-balancer-using-the-service-definition-file"></a>Créer un équilibrage de charge à l’aide du fichier de définition de service

Vous pouvez tirer parti du Kit de développement Azure pour .NET 2,5 mettre à jour votre service cloud. Paramètres de point de terminaison pour les services en nuage sont apportées dans le fichier .csdef [définition de service](https://msdn.microsoft.com/library/azure/gg557553.aspx).

L’exemple suivant illustre la manière dont est configuré un fichier servicedefinition.csdef pour un déploiement de cloud :

Vérification de la snipet pour le fichier .csdef généré par un déploiement de cloud, vous pouvez voir le point de terminaison externe configuré pour utiliser les ports HTTP sur le port 10000, 10001 et 10002.


    <ServiceDefinition name=“Tenant“>
    <WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
    </WorkerRole>
    </ServiceDefinition>




## <a name="check-load-balancer-health-status-for-cloud-services"></a>Vérifier l’état d’intégrité équilibrage de charge des services en nuage


Voici un exemple d’une sonde d’intégrité :

        <LoadBalancerProbes>
        <LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
        </LoadBalancerProbes>

L’équilibrage de charge combine les informations du point de terminaison et les informations de la sonde de créer une URL dans le formulaire de http://{DIP de VM}:80/Probe.aspx qui peut être utilisé pour interroger l’état du service.

Le service détecte sondes périodiques à partir de la même adresse IP. Il s’agit de la demande de sonde santé en provenance de l’hôte du nœud où la machine virtuelle est en cours d’exécution.
Le service doit répondre avec un code d’état HTTP 200 pour l’équilibrage de charge à part du principe que le service est correct. N’importe quel autre code d’état HTTP (par exemple 503) permet d’accéder directement la machine virtuelle se déconnecter de rotation.

La définition de la sonde contrôle également la fréquence de la sonde. Dans notre exemple ci-dessus, l’équilibrage de charge est détection le point de terminaison chaque 5 s. Si aucune réponse positive n’est reçue pour 10 secondes (deux intervalles sonde), la sonde est supposée vers le bas et la machine virtuelle est considérée se déconnecter de rotation. De même, si le service n’appartient pas rotation et réception d’une réponse positive, le service est placé en rotation immédiatement. Si le service est taux varie entre et incorrects, l’équilibrage de charge peut décider de retarder la nouvelle présentation du service en rotation jusqu'à ce qu’il a été exact pour un nombre de sondes.

Vérifiez le schéma de définition du service pour la [sonde santé](https://msdn.microsoft.com/library/azure/jj151530.aspx) pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes

[Démarrer la configuration d’un programme d’équilibrage de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configurer un mode de distribution d’équilibrage de charge](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’expiration TCP inactives pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)

