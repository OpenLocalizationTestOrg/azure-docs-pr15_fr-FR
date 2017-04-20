<properties
   pageTitle="Mettre à l’échelle votre cluster ACS avec l’infrastructure du langage commun Azure | Microsoft Azure"
   description="Comment faire évoluer votre cluster Azure conteneur Service à l’aide de l’infrastructure du langage commun Azure."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, conteneurs, Micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/03/2016"
   ms.author="timlt"/>

# <a name="scale-an-azure-container-service"></a>Échelle un Service conteneur Azure

Vous pouvez augmenter le nombre de nœuds a votre Azure conteneur ACS (Service) à l’aide de l’outil Azure infrastructure du langage commun. Lorsque vous utilisez l’infrastructure du langage commun Azure à l’échelle, l’outil renvoie un nouveau fichier de configuration représentant la modification appliquée au conteneur.

## <a name="about-the-command"></a>À propos de la commande

L’infrastructure du langage commun Azure doit être en mode Azure le Gestionnaire de ressources pour vous permet d’interagir avec Azure conteneurs. Vous pouvez basculer en mode directeur des ressources en appelant `azure config mode arm`. La `acs` commande comporte une commande enfant nommée `scale` qui effectue toutes les opérations d’échelle d’un service de conteneur. Vous pouvez obtenir de l’aide sur les différents paramètres utilisés dans la commande échelle en exécutant `azure acs scale --help`, qui renvoie doit ressembler à ceci :

```azurecli
azure acs scale --help

help:    The operation to scale a container service.
help:
help:    Usage: acs scale [options] <resource-group> <name> <new-agent-count>
help:
help:    Options:
help:      -h, --help                               output usage information
help:      -v, --verbose                            use verbose output
help:      -vv                                      more verbose with debug output
help:      --json                                   use json output
help:      -g, --resource-group <resource-group>    resource-group
help:      -n, --name <name>                        name
help:      -o, --new-agent-count <new-agent-count>  New agent count
help:      -s, --subscription <subscription>        The subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="use-the-command-to-scale"></a>Utilisez la commande Mettre à l’échelle

Pour redimensionner un service de conteneur, vous devez tout d’abord connaître le **groupe de ressources** et le **nom de Service de conteneur Azure (ACS)**et également spécifier le nombre de nouveaux d’agents. En utilisant une quantité inférieure ou une version ultérieure, vous pouvez mettre à l’échelle vers le bas ou vers le haut respectivement.

Vous souhaiterez savoir quels le nombre actuel d’agents d’un service de conteneur avant de vous mettre à l’échelle. Utiliser la `azure acs show <resource group> <ACS name>` commande pour renvoyer la configuration de la ACS. Remarque le résultat <mark>Count</mark> .

#### <a name="see-current-count"></a>Voir nombre actuel

```azurecli
azure acs show containers-test containerservice-containers-test

info:    Executing command acs show
data:
data:     Id                 : /subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test
data:     Name               : containerservice-containers-test
data:     Type               : Microsoft.ContainerService/ContainerServices
data:     Location           : westus
data:     ProvisioningState  : Succeeded
data:     OrchestratorProfile
data:       OrchestratorType : DCOS
data:     MasterProfile
data:       Count            : 1
data:       DnsPrefix        : myprefixmgmt
data:       Fqdn             : myprefixmgmt.westus.cloudapp.azure.com
data:     AgentPoolProfiles
data:       #0
data:         Name           : agentpools
data:         <mark>Count          : 1</mark>
data:         VmSize         : Standard_D2
data:         DnsPrefix      : myprefixagents
data:         Fqdn           : myprefixagents.westus.cloudapp.azure.com
data:     LinuxProfile
data:       AdminUsername    : azureuser
data:       Ssh
data:         PublicKeys
data:           #0
data:             KeyData    : ssh-rsa <ENCODED VALUE>
data:     DiagnosticsProfile
data:       VmDiagnostics
data:         Enabled        : true
data:         StorageUri     : https://<storageid>.blob.core.windows.net/
```  

#### <a name="scale-to-new-count"></a>Mettre à l’échelle à nouveau

Comme il est déjà probablement évidents, vous pouvez adapter le service de conteneur en appelant `azure acs scale` et fournissant le **groupe de ressources**, le **nom de ACS**et le **nombre de l’agent**. Lorsque vous redimensionnez un service de conteneur, infrastructure du langage commun Azure renvoie une chaîne JSON représentant la nouvelle configuration du service conteneur, y compris le nouveau décompte agent.

```azurecli
azure acs scale containers-test containerservice-containers-test 10

info:    Executing command acs scale
data:    {
data:        id: '/subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test',
data:        name: 'containerservice-containers-test',
data:        type: 'Microsoft.ContainerService/ContainerServices',
data:        location: 'westus',
data:        provisioningState: 'Succeeded',
data:        orchestratorProfile: { orchestratorType: 'DCOS' },
data:        masterProfile: {
data:            count: 1,
data:            dnsPrefix: 'myprefixmgmt',
data:            fqdn: 'myprefixmgmt.westus.cloudapp.azure.com'
data:        },
data:        agentPoolProfiles: [
data:            {
data:                name: 'agentpools',
data:                <mark>count: 10</mark>,
data:                vmSize: 'Standard_D2',
data:                dnsPrefix: 'myprefixagents',
data:                fqdn: 'myprefixagents.westus.cloudapp.azure.com'
data:            }
data:        ],
data:        linuxProfile: {
data:            adminUsername: 'azureuser',
data:            ssh: {
data:                publicKeys: [
data:                    { keyData: 'ssh-rsa <ENCODED VALUE>' }
data:                ]
data:            }
data:        },
data:        diagnosticsProfile: {
data:            vmDiagnostics: { enabled: true, storageUri: 'https://<storageid>.blob.core.windows.net/' }
data:        }
data:    }
info:    acs scale command OK
``` 

## <a name="next-steps"></a>Étapes suivantes

- [Déployer un cluster](container-service-deployment.md)