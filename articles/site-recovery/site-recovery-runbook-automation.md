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


# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Ajouter des procédures opérationnelles automation Azure pour les plans de récupération


Ce didacticiel décrit comment récupération de Site Azure s’intègre à Automation Azure pour fournir extensibilité pour les plans de récupération. Plans de récupération peuvent gérer la récupération de vos machines virtuelles protégé à l’aide de la récupération de Site Azure pour la réplication dans le cloud secondaire et la réplication aux scénarios Azure. Ils permettent également à effectuer la récupération **précis et constants**, **répétitifs**et **automatisé**. Si vous ne pouvez pas vos machines virtuelles à Azure, intégration avec Azure Automation étend les plans de récupération et vous donne la possibilité d’exécuter des procédures opérationnelles, permettant ainsi des tâches d’automatisation puissantes.

Si vous n’avez pas encore entendu parler Azure automatisation, inscrire [ici](https://azure.microsoft.com/services/automation/) et télécharger les exemples de scripts [ici](https://azure.microsoft.com/documentation/scripts/). En savoir plus sur la [Récupération de Site Azure](https://azure.microsoft.com/services/site-recovery/) et comment organiser récupération vers Azure à l’aide de plans de récupération [ici](https://azure.microsoft.com/blog/?p=166264).

Dans ce didacticiel, nous allons examiner comment vous pouvez intégrer des procédures opérationnelles Azure Automation dans les plans de récupération. Nous automatiser des tâches simples nécessitant précédemment intervention manuelle et découvrez comment convertir une récupération en plusieurs étapes en une action de récupération d’un seul clic. Nous allons étudier également comment vous pouvez résoudre un script simple si elle se produit.

## <a name="customize-the-recovery-plan"></a>Personnaliser le plan de récupération

1. Commençons par operning la cuillère ressource du plan de récupération. Vous pouvez voir que le plan de récupération a deux machines virtuelles ajoutés pour la récupération. 

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
---------------------

2. Cliquez sur le bouton Personnaliser pour commencer à ajouter une procédure opérationnelle. Cette action ouvre le plan de récupération personnaliser la carte.


    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


3. Cliquez avec le bouton droit sur dans le groupe démarrage 1 et sélectionnez cette option pour ajouter une action de publication d’Ajouter.

4. Sélectionnez Choisir un script dans la nouvelle carte.

5. Nommez le script « Hello World ».

6. Sélectionnez un nom de compte Automation. C’est le compte Azure Automation. Notez que ce compte peut se trouver dans un emplacement géographique Azure mais doit se trouver dans le même abonnement en tant que l’archivage sécurisé récupération de Site.

7. Sélectionnez un runbook à partir du compte Automation. Il s’agit le script qui s’exécute pendant l’exécution du plan de récupération après la récupération du premier groupe.

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)


8. Sélectionnez OK pour enregistrer le script. Cette action ajoutera le script au groupe action post du groupe 1 : Démarrer.


    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>Points importants de l’ajout d’un script

1. Cliquer avec le bouton droit sur le script et choisissez « Supprimer étape » ou « mettre à jour de script ».

2. Un script peut exécuter sur la Azure lors de basculement locales pour Azure et peut exécuter sur Azure sous forme de script côté primaire avant l’arrêt, au cours de restauration à partir d’Azure en local.

3. Lorsqu’un script s’exécute, il sera injection un contexte du plan de récupération.

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
CloudServiceName (dans le modèle de déploiement de gestionnaire de ressources) | Nom du groupe de ressources Azure sous lequel la machine virtuelle est créée.


## <a name="using-complex-variables-per-recovery-plan"></a>Utilisation des variables complexe par plan de récupération

Parfois, une procédure opérationnelle nécessite davantage d’informations que simplement la RecoveryPlanContext. Il n’existe aucun mécanisme première classe pour passer d’un paramètre à une procédure opérationnelle. Toutefois, si vous souhaitez utiliser le même script via plusieurs plans de récupération vous pouvez utiliser la variable de récupération planifier contexte 'RecoveryPlanName' et utiliser l’en dessous de l’expérience technique à utiliser une variable complexe Azure Automation dans une procédure opérationnelle. L’exemple ci-dessous montre comment vous pouvez créer trois actifs variable complexe différente et les utiliser dans la procédure opérationnelle basé sur le nom du plan de récupération.

Prendre en compte que vous souhaitez utiliser 3 paramètres supplémentaires dans une procédure opérationnelle. Laissez-nous les coder dans un formulaire JSON {« Var1 » : « testautomation », « Var2 » : « Non », « Var3 » : « PrimaryToSecondary »}

[AA complexe variable](../automation/automation-variables.md#variable-types Complex variable) permet de créer une nouvelle ressource Automation.
Nom de la variable en tant que <RecoveryPlanName>- paramètres.
Vous pouvez utiliser la référence ici pour créer une [variable complexe](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396).

Pour les plans de récupération différent, nommez la variable en tant que

1. recoveryPlanName1 >-Paramètres
2. recoveryPlanName2 >-Paramètres
3. recoveryPlanName3 >-Paramètres

À présent, dans le script faire les paramètres en tant que

1. Obtenir le nom RP à partir de la $rpname = $Recoveryplancontext variable
2. Obtenir des biens de $paramValue = « $($rpname)-paramètres »
3. Utilisez cette comme étant une variable complexe pour le plan de récupération en appelant Get-AzureAutomationVariable [-AutomationAccountName] <String> -nommer $paramValue.

Par exemple, pour obtenir le paramètre/variable complexe pour le plan de récupération SharepointApp, créez une variable de complexe Azure Automation appelée « Paramètres SharepointApp ».

Utiliser dans le plan de récupération en extrayant la variable de l’actif à l’aide de l’instruction Get-AzureAutomationVariable [-AutomationAccountName] <String> [-nom] $paramValue. [Cette référence pour plus d’informations](https://msdn.microsoft.com/library/dn913772.aspx)

De cette façon le même script peut être utilisée pour plan de récupération différent en enregistrant la variable complexe spécifique de plan dans les biens.

## <a name="sample-scripts"></a>Exemples de scripts

Pour un référentiel de scripts que vous pouvez importer directement dans votre compte automatisation, voir [référentiel OMS de Kristian Nese des scripts](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation)

Le script ici est un modèle de gestionnaire de ressources Azure déployer l’en dessous de scripts

* NSG

La procédure opérationnelle NSG sera affecter des adresses IP Public à chaque machine virtuelle dans le Plan de récupération et joindre leurs cartes réseau virtuelles à un groupe de sécurité réseau qui vous permettront de communication par défaut

* PublicIP

L’adresse IP publique runbook attribue des adresses IP Public à chaque machine virtuelle dans le Plan de récupération. Accès aux ordinateurs et aux applications varient selon les paramètres du pare-feu au sein de chaque invité


* CustomScript

La procédure opérationnelle CustomScript sera affecter des adresses IP Public à chaque machine virtuelle dans le Plan de récupération et installer une extension de script personnalisé qui extrait le script que faire au cours du déploiement du modèle

* NSGwithCustomScript

La NSGwithCustomScript runbook attribue des adresses IP Public à chaque machine virtuelle dans le Plan de récupération, installation personnalisée à l’aide de l’extension de script et vous connecter les cartes réseau virtuelles à un NSG autorisant par défaut des communications entrantes et sortantes pour l’accès à distance

## <a name="additional-resources"></a>Ressources supplémentaires

[Service d’automatisation Azure exécuter en tant que compte](../automation/automation-sec-configure-azure-runas-account.md)

[Vue d’ensemble de Automation Azure] (http://msdn.microsoft.com/library/azure/dn643629.aspx "Vue d’ensemble de Automation Azure")

[Exemples de Scripts Automation Azure] (http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Exemples de Scripts Automation Azure")
