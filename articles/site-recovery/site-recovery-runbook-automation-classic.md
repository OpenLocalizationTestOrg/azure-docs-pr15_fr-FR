<properties
   pageTitle="Ajouter des procédures opérationnelles automation Azure pour les plans de récupération | Microsoft Azure"
   description="Cet article décrit comment récupération de Site Azure maintenant vous permet de développer les plans de récupération à l’aide de Automation Azure d’effectuer les tâches complexes pendant la récupération pour Azure"
   services="site-recovery"
   documentationCenter=""
   authors="ruturaj"
   manager="gauravd"
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required"
   ms.date="10/23/2016"
   ms.author="ruturajd@microsoft.com"/>


# <a name="add-azure-automation-runbooks-to-recovery-plans---classic"></a>Ajouter des procédures opérationnelles automation Azure pour les plans de récupération - classique


Ce didacticiel décrit comment récupération de Site Azure s’intègre à Automation Azure pour fournir extensibilité pour les plans de récupération. Plans de récupération peuvent gérer la récupération de vos machines virtuelles protégé à l’aide de la récupération de Site Azure pour la réplication dans le cloud secondaire et la réplication aux scénarios Azure. Ils permettent également à effectuer la récupération **précis et constants**, **répétitifs**et **automatisé**. Si vous ne pouvez pas vos machines virtuelles à Azure, intégration avec Azure Automation étend les plans de récupération et vous donne la possibilité d’exécuter des procédures opérationnelles, permettant ainsi des tâches d’automatisation puissantes.

Si vous n’avez pas encore entendu parler Azure automatisation, inscrire [ici](https://azure.microsoft.com/services/automation/) et télécharger les exemples de scripts [ici](https://azure.microsoft.com/documentation/scripts/). En savoir plus sur la [Récupération de Site Azure](https://azure.microsoft.com/services/site-recovery/) et comment organiser récupération vers Azure à l’aide de plans de récupération [ici](https://azure.microsoft.com/blog/?p=166264).

Dans ce didacticiel, nous allons examiner comment vous pouvez intégrer des procédures opérationnelles Azure Automation dans les plans de récupération. Nous automatiser des tâches simples nécessitant précédemment intervention manuelle et découvrez comment convertir une récupération étape à plusieurs en une action de récupération d’un seul clic. Nous allons étudier également comment vous pouvez résoudre un script simple si elle se produit.

## <a name="protect-the-application-to-azure"></a>Protéger l’application vers Azure

Commençons par une application simple qui se compose de deux machines virtuelles. Ici, nous avons une application Web de Fabrikam. Fabrikam-Web-frontend et Fabrikam-Web-principaux sont les deux machines virtuelles protégées à Azure à l’aide de récupération de Site Azure. Pour protéger les machines virtuelles à l’aide de la récupération de Site Azure, suivez les étapes ci-dessous.

1.  Activer la protection de vos machines virtuelles.

2.  Assurez-vous que les machines virtuelles ont terminé la réplication initiale et procédez à la réplication.

3.  Attendez que la réplication initiale est terminée et l’état de réplication indique protégé.

![](media/site-recovery-runbook-automation/01.png)
---------------------

Dans ce didacticiel, nous allons créer un plan de récupération pour l’application Fabrikam HRweb à basculement l’application à Azure. Puis nous il s’intègrent un runbook qui crée un point de terminaison sur l’échec sur Azure machine virtuelle pour traiter les pages web au port 80.

Tout d’abord, nous allons créer un plan de récupération de notre application.

## <a name="create-the-recovery-plan"></a>Créer le plan de récupération

Pour récupérer l’application Azure, vous devez créer un plan de récupération.
À l’aide d’un plan de récupération que vous pouvez spécifier l’ordre de la récupération des machines virtuelles. La machine virtuelle placée dans le groupe 1 sont récupérer et commencer en premier, et suivez ensuite la machine virtuelle dans le groupe 2.

Créer un Plan de récupération ressemblant à ci-dessous.

![](media/site-recovery-runbook-automation/12.png)

Pour en savoir plus sur les offres de récupération, consultez la documentation [ici](https://msdn.microsoft.com/library/azure/dn788799.aspx "ici").

Ensuite, nous allons créer les objets nécessaires dans Azure Automation.

## <a name="create-the-automation-account-and-its-assets"></a>Créer le compte automation et ses actifs

Vous avez besoin d’un compte Azure Automation pour créer des procédures opérationnelles. Si vous n’avez pas déjà un compte, accédez à l’onglet Automation Azure indiqué par ![](media/site-recovery-runbook-automation/02.png)et créer un nouveau compte.

1.  Attribuez un nom pour identifier avec le compte.

2.  Spécifier une région géographique où vous voulez placer le compte.

Il est recommandé de placer le compte dans la même région en tant que l’archivage sécurisé de récupération automatique du système.

![](media/site-recovery-runbook-automation/03.png)

Ensuite, créez les ressources suivantes sur le compte.

### <a name="add-a-subscription-name-as-asset"></a>Ajouter un nom de l’abonnement comme actif

1.  Ajouter un nouveau paramètre ![](media/site-recovery-runbook-automation/04.png) dans Azure Automation actifs et sélectionnez cette option pour![](media/site-recovery-runbook-automation/05.png)

2.  Sélectionnez le type de variable sous forme de **chaîne**

3.  Spécifiez le nom de la variable comme **AzureSubscriptionName**

    ![](media/site-recovery-runbook-automation/06.png)

4.  Indiquer le nom d’abonnement Azure réel en tant que la valeur de la variable.

    ![](media/site-recovery-runbook-automation/07_1.png)

Vous pouvez identifier le nom de votre abonnement à partir de la page Paramètres de votre compte sur le portail Azure.

### <a name="add-an-azure-login-credential-as-asset"></a>Ajouter des informations d’identification de connexion Azure comme actif

Automatisation Azure Azure PowerShell pour se connecter à l’abonnement et fonctionne sur les objets il. Pour ce faire, vous devez s’authentifier à l’aide de votre compte Microsoft ou un compte professionnel ou scolaire.
Vous pouvez stocker les informations d’identification de compte dans un bien devant être utilisé en toute sécurité par la procédure opérationnelle.

1.  Ajouter un nouveau paramètre ![](media/site-recovery-runbook-automation/04.png) dans l’Azure Automation actifs, sélectionnez![](media/site-recovery-runbook-automation/09.png)

2.  Sélectionnez le type d’informations d’identification **D’informations d’identification Windows PowerShell**

3.  Spécifiez le nom comme **AzureCredential**

    ![](media/site-recovery-runbook-automation/10.png)

4.  Spécifiez le nom d’utilisateur et mot de passe pour se connecter avec.

À présent les deux paramètres suivants sont disponibles dans vos ressources.

![](media/site-recovery-runbook-automation/11.png)

Plus d’informations sur la connexion à votre abonnement via PowerShell sont données [ici](../powershell-install-configure.md).

Ensuite, vous allez créer une procédure opérationnelle dans Automation Azure qui peut ajouter un point de terminaison de la machine virtuelle frontale après le basculement.

## <a name="azure-automation-context"></a>Contexte automation Azure

Récupération automatique du système passe une variable de contexte à la runbook pour vous aider à écrire des scripts déterministes. Les noms du Service Cloud et la Machine virtuelle sont prévisibles que se passe-t-il qu’il n’est pas toujours la casse en raison de certains scénarios tels que celui lorsque le nom de la machine virtuelle peut se sont modifiées en raison de caractères non pris en charge dans Azure disent. Par conséquent, ces informations sont passées pour le plan de récupération de récupération automatique du système dans le cadre du *contexte*.

Voici un exemple de l’aspect de la variable de contexte.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


Le tableau ci-dessous contient le nom et une description pour chaque variable dans le contexte.

**Nom d’une variable** | **Description**
---|---
RecoveryPlanName | Nom de plan en cours d’exécution. Vous permet d’agir en fonction de nom à l’aide de la même script
FailoverType | Indique si le basculement est test, planifié ou non.
FailoverDirection | Spécifier si la récupération est principal ou secondaire
GroupID | Identifier le nombre de groupe dans le plan de récupération lorsque le plan est en cours d’exécution
VmMap | Tableau de tous les ordinateurs virtuels dans le groupe
Clé VMMap | Clé unique (GUID) pour chaque machine virtuelle. Il est identique à l’ID de VMM de la machine virtuelle, le cas échéant.
RoleName | Nom de la machine virtuelle Azure qui est en cours de restauration
CloudServiceName | Nom de Service Cloud Azure sous lequel la machine virtuelle est créée.


Pour identifier le VmMap Key dans le contexte, vous pouvez également accéder à la page de propriétés de la machine virtuelle de récupération automatique du système et examinez la propriété VM GUID.

![](media/site-recovery-runbook-automation/13.png)

## <a name="author-an-automation-runbook"></a>Créer un runbook Automation

La procédure opérationnelle pour ouvrir le port 80 sur l’ordinateur virtuel frontale à présent créer.

1.  Créer un nouveau runbook dans le compte Azure Automation avec le nom **OpenPort80**

    ![](media/site-recovery-runbook-automation/14.png)

2.  Accéder au mode auteur de la runbook et entrez le mode brouillon.

3.  Tout d’abord spécifier la variable à utiliser dans le contexte du plan de restauration

    ```
        param (
            [Object]$RecoveryPlanContext
        )

    ```

4.  Ensuite se connecter à l’abonnement en utilisant le nom d’informations d’identification et d’abonnement

    ```
        $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

        # Connect to Azure
        $AzureAccount = Add-AzureAccount -Credential $Cred
        $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
        Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    ```

    Notez que vous utilisez les biens Azure – **AzureCredential** et **AzureSubscriptionName** ici.

5.  Spécifier les détails de point de terminaison et le GUID de la machine virtuelle pour lequel vous souhaitez exposer le point de terminaison. Dans ce cas, la machine virtuelle frontale.

    ```
        # Specify the parameters to be used by the script
        $AEProtocol = "TCP"
        $AELocalPort = 80
        $AEPublicPort = 80
        $AEName = "Port 80 for HTTP"
        $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"
    ```

    Spécifie le protocole de point de terminaison Azure, port local sur l’ordinateur virtuel et son port public mappé. Ces variables sont les paramètres requis par les commandes Azure ajouter des points de terminaison aux machines virtuelles. La VMGUID conserve le GUID de la machine virtuelle que vous deviez fonctionnent sur.

6.  Le script extrait le contexte pour le GUID VM donnée et créer un point de terminaison sur l’ordinateur virtuel qu’elle référencé maintenant.

    ```
        #Read the VM GUID from the context
        $VM = $RecoveryPlanContext.VmMap.$VMGUID

        if ($VM -ne $null)
        {
            # Invoke pipeline commands within an InlineScript

            $EndpointStatus = InlineScript {
                # Invoke the necessary pipeline commands to add a Azure Endpoint to a specified Virtual Machine
                # Commands include: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including parameters)

                $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                    Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                    Update-AzureVM
                Write-Output $Status
            }
        }
    ```

7. Une fois le processus terminé, appuyez sur Publier ![](media/site-recovery-runbook-automation/20.png) pour permettre à votre script soit disponible pour l’exécution.

Le script complet est donné ci-dessous pour référence

```
  workflow OpenPort80
  {
    param (
        [Object]$RecoveryPlanContext
    )

    $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

    # Connect to Azure
    $AzureAccount = Add-AzureAccount -Credential $Cred
    $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

    # Specify the parameters to be used by the script
    $AEProtocol = "TCP"
    $AELocalPort = 80
    $AEPublicPort = 80
    $AEName = "Port 80 for HTTP"
    $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"

    #Read the VM GUID from the context
    $VM = $RecoveryPlanContext.VmMap.$VMGUID

    if ($VM -ne $null)
    {
        # Invoke pipeline commands within an InlineScript

        $EndpointStatus = InlineScript {
            # Invoke the necessary pipeline commands to add an Azure Endpoint to a specified Virtual Machine
            # This set of commands includes: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including necessary parameters)

            $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                Update-AzureVM
            Write-Output $Status
        }
    }
  }
```

## <a name="add-the-script-to-the-recovery-plan"></a>Ajoutez le script pour le plan de récupération

Une fois que le script est prêt, vous pouvez l’ajouter au plan de récupération que vous avez créé précédemment.

1.  Dans le plan de récupération que vous avez créé, choisissez Ajouter un script après le groupe 2. ![](media/site-recovery-runbook-automation/15.png)

2.  Spécifiez un nom de script. Il s’agit d’un nom convivial pour ce script pour affichant dans le plan de récupération.

3.  Dans le basculement vers Azure script – sélectionnez le nom du compte d’automatisation Azure.

4.  Dans les procédures opérationnelles Azure, sélectionnez le runbook que vous avez créés.

![](media/site-recovery-runbook-automation/16.png)

## <a name="primary-side-scripts"></a>Scripts côté primaire

Lorsque vous exécutez un basculement vers Azure, vous pouvez également choisir d’exécution de scripts côté primaire. Ces scripts seront exécuter sur le serveur VMM pendant le basculement.
Scripts côté primaire ne sont disponibles uniquement pour la préparation à la fermeture et publient des étapes de l’arrêt. C’est parce que nous prévoyons au site principal d’être indisponibles généralement lorsqu’un sinistre.
Pendant un basculement non planifié, uniquement si vous choisissez cette option pour les opérations de site principal, il tentera d’exécuter les scripts côté primaire. Si elles ne sont pas accessibles ou délai d’attente, le basculement continuent à récupérer les machines virtuelles.
Les scripts côté primaire sont non disponibles pour les Sites VMware/physique/Hyper-v sans VMM protégé sur Azure - basculement vers Azure.
Toutefois, lorsque vous restauration à partir d’Azure en local, les scripts côté principal (procédures opérationnelles) peut être utilisée pour toutes les cibles à l’exception de VMware.

## <a name="test-the-recovery-plan"></a>Tester le plan de récupération

Une fois que vous avez ajouté le runbook à l’offre, vous pouvez lancer un basculement de test et voyez par vous-même. Il est toujours recommandé d’exécuter un basculement de test pour tester votre application et le plan de récupération pour vous assurer qu’il n’y a aucune erreur.

1.  Sélectionnez le plan de récupération et lancer un basculement de test.

2.  Pendant l’exécution du plan, vous pouvez voir si le runbook a été exécutée ou non via son statut.

    ![](media/site-recovery-runbook-automation/17.png)

3.  Vous pouvez également afficher l’état d’exécution runbook détaillées sur la page de travaux Azure Automation pour la procédure opérationnelle.

    ![](media/site-recovery-runbook-automation/18.png)

4.  Une fois le basculement terminé, hormis le résultat de l’exécution runbook, vous pouvez voir si l’exécution réussit ou non en accédant à la page Azure machine virtuelle et examinez les points de terminaison.

![](media/site-recovery-runbook-automation/19.png)

## <a name="sample-scripts"></a>Exemples de scripts

Tandis que nous parcouru une automatisation fréquemment utilisées tâche d’ajout d’un point de terminaison pour une machine virtuelle Azure dans ce didacticiel, vous pourriez faire un nombre d’autres tâches automation puissantes à l’aide d’automation Azure. Microsoft et la Communauté Azure Automation fournissent des procédures opérationnelles exemple qui peuvent vous aider à commencer à créer votre propre les solutions et les procédures opérationnelles utilitaire, que vous pouvez utiliser comme blocs de construction pour les tâches d’automatisation importantes. Commencer à les utiliser à partir de la galerie et créer les plans de vos applications à l’aide de la récupération de Site Azure puissantes récupération un seul clic.

## <a name="additional-resources"></a>Ressources supplémentaires

[Vue d’ensemble de Automation Azure] (http://msdn.microsoft.com/library/azure/dn643629.aspx "Vue d’ensemble de Automation Azure")

[Exemples de Scripts Automation Azure] (http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Exemples de Scripts Automation Azure")
