
<properties
    pageTitle="Gérer les ressources de l’infrastructure du langage commun Azure | Microsoft Azure"
    description="Utiliser l’Interface de ligne de commande Azure (CLI) pour gérer les groupes et les ressources Azure"
    editor=""
    manager="timlt"
    documentationCenter=""
    authors="dlepow"
    services="azure-resource-manager"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="danlep"/>

# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Utiliser l’infrastructure du langage commun Azure pour gérer les ressources Azure et groupes de ressources


> [AZURE.SELECTOR]
- [Portail](azure-portal/resource-group-portal.md) 
- [Azure infrastructure du langage commun](xplat-cli-azure-resource-manager.md)
- [PowerShell Azure](powershell-azure-resource-manager.md)
- [API REST](resource-manager-rest-api.md)


L’Interface de ligne Azure (commande Azure) est un des nombreux outils que vous pouvez utiliser pour déployer et gérer des ressources avec le Gestionnaire de ressources. Cet article présente courantes façons de gérer les ressources Azure et groupes de ressources à l’aide de l’infrastructure du langage commun Azure en mode Gestionnaire de ressources. Pour plus d’informations sur l’utilisation de l’infrastructure du langage commun pour déployer des ressources, consultez [ressources de déployer des modèles de gestionnaire de ressources et Azure infrastructure du langage commun](resource-group-template-deploy-cli.md). Pour davantage d’informations sur les ressources Azure et le Gestionnaire de ressources, consultez [Vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md).

>[AZURE.NOTE] Pour gérer les ressources Azure avec l’infrastructure du langage commun Azure, vous devez [installer l’infrastructure du langage commun Azure](xplat-cli-install.md)et [se connecter à Azure](xplat-cli-connect.md) à l’aide de la `azure login` commande. Vérifiez que l’infrastructure du langage commun est en mode Gestionnaire de ressources (exécuter `azure config mode arm`). Si vous avez terminé les éléments suivants, vous êtes prêt à l’envoi.



## <a name="get-resource-groups-and-resources"></a>Obtenir des ressources et des groupes de ressources

### <a name="resource-groups"></a>Groupes de ressources

Pour obtenir une liste de tous les groupes de ressources dans votre abonnement et leurs emplacements, exécutez la commande suivante.

    azure group list
    

### <a name="resources"></a>Ressources
 Pour répertorier toutes les ressources dans un groupe, comme une avec nom *testRG*, utilisez la commande suivante.

    azure resource list testRG

Pour afficher une ressource au sein du groupe, comme un ordinateur virtuel nommé *MyUbuntuVM*, utilisez une commande semblable à celui-ci.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"
    
Notez le paramètre **Microsoft.Compute/virtualMachines** . Ce paramètre indique le type de la ressource sur que vous demandent des informations.
    
>[AZURE.NOTE]Lorsque vous utilisez les commandes de **Ressources azure** différent de la commande de la **liste** , vous devez spécifier la version de l’API de la ressource avec le paramètre **-o** . Si vous ne savez pas la version de l’API, consultez le fichier de modèle et recherchez le champ apiVersion pour la ressource. Pour plus d’informations sur les versions de l’API dans le Gestionnaire de ressources, voir [Gestionnaire de ressources fournisseurs, régions, versions API et des schémas](resource-manager-supported-services.md).

Lorsque vous affichez des détails sur une ressource, il est souvent utile d’utiliser la `--json` paramètre. Ce paramètre permet la sortie plus lisible, car certaines valeurs sont des structures imbriquées ou des collections. L’exemple suivant montre comment renvoyer les résultats de la commande **Afficher** comme document de JSON.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json

>[AZURE.NOTE] Si vous le souhaitez, enregistrer les données JSON au fichier à l’aide de la &gt; caractère pour diriger le résultat vers un fichier. Par exemple :
>
> `azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

### <a name="tags"></a>Balises

[AZURE.INCLUDE [resource-manager-tag-resources-cli](../includes/resource-manager-tag-resources-cli.md)]

## <a name="manage-resources"></a>Gérer les ressources


Pour ajouter une ressource telle qu’un compte de stockage à un groupe de ressources, exécutez une commande semblable à :

    azure resource create testRG MyStorageAccount "Microsoft.Storage/storageAccounts" "westus" -o "2015-06-15" -p "{\"accountType\": \"Standard_LRS\"}"
    
Outre la spécification de la version de l’API de la ressource avec le paramètre **-o** , utilisez le paramètre **-p** pour passer d’une chaîne JSON avec toutes les propriétés requises ou supplémentaires.
    
    
Pour supprimer une ressource existante comme une ressource machine virtuelle, utilisez une commande à ce qui suit.

    azure resource delete testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

Pour déplacer des ressources existantes d’un autre groupe de ressources ou d’abonnement, utilisez la commande **déplacer ressource azure** . L’exemple suivant montre comment déplacer un Cache Redis vers un nouveau groupe de ressources. Dans le paramètre **-i** , fournir une liste séparée par des virgules de l’id de ressource à déplacer.


    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"

## <a name="control-access-to-resources"></a>Contrôler l’accès aux ressources

Vous pouvez utiliser l’infrastructure du langage commun Azure pour créer et gérer les stratégies pour contrôler l’accès aux ressources Azure. Pour davantage d’informations sur les définitions de stratégie et attribution de stratégies vers des ressources, voir [utiliser une stratégie pour gérer les ressources et contrôler l’accès](resource-manager-policy.md).

Par exemple, définir la stratégie suivante pour refuser toutes les demandes où emplacement n’est pas US Ouest ou Amérique centrale nous et enregistrez-le dans le policy.json de fichier de définition de stratégie :

    {
    "if" : {
        "not" : {
        "field" : "location",
        "in" : ["westus" ,  "northcentralus"]
        }
    },
    "then" : {
        "effect" : "deny"
    }
    }

Ensuite, exécutez la commande **créer de définition de la stratégie** :

    azure policy definition create MyPolicy -p c:\temp\policy.json
    
Cette commande montre sortie semblable au suivant.

    + Création de la définition de stratégie données MyPolicy : stratégie : données MyPolicy : PolicyDefinitionId : /subscriptions/###-###-###-###-###/providers/Microsoft.Authorization/policyDefinitions/MyPolicy

    données : élément PolicyType : données personnalisées : DisplayName : non défini de données : Description : non défini de données : PolicyRule : champ = emplacement, dans = [westus, northcentralus], effet = refuser

 Pour attribuer une stratégie à la portée souhaité, utilisez le **PolicyDefinitionId** retourné par la commande précédente. Dans l’exemple suivant, cette étendue est un abonnement, mais vous pouvez limiter l’étendue à des groupes de ressources ou des ressources :

    affectation de stratégie Azure créer MyPolicyAssignment -p /subscriptions/###-###-###-###-###/providers/Microsoft.Authorization/policyDefinitions/MyPolicy -s /subscriptions/###-###-###-###-### /

Vous pouvez obtenir, modifier ou supprimer des définitions de stratégie en utilisant les commandes de **définition de la stratégie Afficher**, **définition de stratégie**et **Supprimer de la définition de la stratégie** .

De même, vous pouvez obtenir, modifier ou supprimer les affectations de stratégie en utilisant les commandes de **l’affectation de stratégie Afficher**, **l’attribution des stratégies**et **Supprimer de l’affectation de stratégie** .


## <a name="export-a-resource-group-as-a-template"></a>Exporter un groupe de ressources en tant que modèle

Pour un groupe de ressources existant, vous pouvez afficher le modèle de gestionnaire de ressources du groupe de ressources. Exporter le modèle offre deux avantages :

1. Vous pouvez facilement automatiser les déploiements à venir de la solution, car toute l’infrastructure est défini dans le modèle.

2. Vous pouvez vous familiariser avec la syntaxe de modèle en consultant le JSON qui représente votre solution.

À l’aide de l’infrastructure du langage commun Azure, vous pouvez exporter un modèle qui représente l’état actuel de votre groupe de ressources, ou télécharger le modèle qui a été utilisé pour un déploiement particulier.

* **Exporter le modèle pour un groupe de ressources** - cette opération est utile lorsque vous apporté des modifications à un groupe de ressources et que vous avez besoin de récupérer la représentation JSON de son état actuel. Toutefois, le modèle généré contient uniquement un nombre minimal de paramètres et aucune variable. La plupart des valeurs dans le modèle est codé en dur. Avant de déployer le modèle généré, vous souhaiterez peut-être plusieurs valeurs convertir des paramètres afin que vous pouvez personnaliser le déploiement pour différents environnements.

    Pour exporter le modèle pour un groupe de ressources dans un répertoire local, exécutez la `azure group export` commande comme le montre l’exemple suivant. (Remplacez un répertoire local approprié pour votre environnement de système d’exploitation).

        azure group export testRG ~/azure/templates/

* **Télécharger le modèle pour un déploiement particulier** --cette opération est utile lorsque vous avez besoin de consulter le modèle réel qui a été utilisé pour déployer des ressources. Le modèle inclut tous les paramètres et les variables définies pour le déploiement d’origine. Toutefois, si une personne de votre organisation apporté des modifications au groupe de ressources en dehors de la définition du modèle, ce modèle ne représente pas l’état actuel du groupe de ressources.

    Pour télécharger le modèle utilisé pour un déploiement spécifique dans un répertoire local, exécutez la `azure group deployment template download` commande. Par exemple :

        azure group deployment template download TestRG testRGDeploy ~/azure/templates/downloads/
 
>[AZURE.NOTE] Exportation de modèle est dans l’aperçu, et pas tous les types de ressource pris en charge l’exportation d’un modèle. Lorsque vous tentez d’exporter un modèle, vous pouvez voir un message d’erreur indiquant que certaines ressources n’ont pas été exportés. Si nécessaire, définir manuellement ces ressources dans votre modèle après son téléchargement.



## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir des détails sur les opérations de déploiement et résoudre les erreurs de déploiement avec l’infrastructure du langage commun Azure, voir [Afficher les opérations de déploiement avec Azure infrastructure du langage commun](resource-manager-troubleshoot-deployments-cli.md).
* Si vous voulez utiliser l’infrastructure du langage commun pour configurer une application ou un script pour accéder aux ressources, voir [Azure infrastructure utiliser pour créer un service principal pour accéder aux ressources du langage commun](resource-group-authenticate-service-principal-cli.md).


