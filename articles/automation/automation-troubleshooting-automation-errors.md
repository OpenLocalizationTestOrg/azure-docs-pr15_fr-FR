<properties
   pageTitle="Gestion des erreurs automation Azure | Microsoft Azure"
   description="Cet article fournit des erreurs de base pour résoudre les problèmes et corriger les erreurs dans Azure automatisation des étapes de gestion."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="stevenka"
   editor="tysonn"
   tags="top-support-issue"
   keywords="Erreur d’automatisation, de gestion des erreurs"/>
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/06/2016"
   ms.author="sngun; v-reagie"/>

# <a name="error-handling-tips-for-common-azure-automation-errors"></a>Gestion des conseils erreurs Automation Azure d’erreurs

Cet article décrit certaines de ces erreurs Automation Azure courants rencontrés et qui suggère d’erreur possibles gestion des étapes.

## <a name="troubleshoot-authentication-errors-when-working-with-azure-automation-runbooks"></a>Résoudre les erreurs d’authentification lorsque vous travaillez avec des procédures opérationnelles Automation Azure  

### <a name="scenario-sign-in-to-azure-account-failed"></a>Scénario : Se connecter à compte Azure a échoué

**Erreur :** Vous recevez l’erreur « Unknown_user_type : Type inconnu d’utilisateur » lorsque vous travaillez avec les applets de commande Add-AzureAccount ou AzureRmAccount de connexion.

**Raison de l’erreur :** Cette erreur se produit si le nom de la ressource d’informations d’identification n’est pas valide ou si le nom d’utilisateur et mot de passe que vous avez utilisé pour configurer la ressource d’informations d’identification Automation ne sont pas valides.

**Des conseils de dépannage :** Afin de déterminer quel est le problème, procédez comme suit :  

1. Vérifiez que vous n’avez pas tous les caractères spéciaux, y compris la **@** caractères dans le nom de biens Automation d’informations d’identification que vous utilisez pour vous connecter à Azure.  

2. Vérifiez que vous pouvez utiliser le nom d’utilisateur et mot de passe qui sont stockés dans les informations d’identification Azure Automation dans votre éditeur de PowerShell ISE local. Vous pouvez effectuer ceci en exécutant les applets de commande dans PowerShell ISE :  

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred

3. Si votre authentification échoue localement, cela signifie que vous n’avez pas correctement configuré vos informations d’identification Azure Active Directory. Consultez le billet de blog [authentification Azure à l’aide d’Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) pour obtenir le compte Azure Active Directory est correctement configuré.  


### <a name="scenario-unable-to-find-the-azure-subscription"></a>Scénario : Impossible de trouver l’abonnement Azure

**Erreur :** Vous recevez l’erreur « l’abonnement nommé ``<subscription name>`` ne peut pas être trouvé » lorsque vous travaillez avec les applets de commande Sélectionner AzureSubscription ou sélectionnez AzureRmSubscription.

**Raison de l’erreur :** Cette erreur se produit si le nom de l’abonnement n’est pas valide ou si l’utilisateur Azure Active Directory essaie d’obtenir les détails de l’abonnement n’est pas configuré en tant qu’administrateur de l’abonnement.

**Des conseils de dépannage :** Afin de déterminer si vous avez correctement authentifié vers Azure et avez accès à l’abonnement que vous essayez de sélectionner, procédez comme suit :  

1. Vérifiez que vous exécutez **Ajouter AzureAccount** avant d’exécuter l’applet de commande **Sélectionner AzureSubscription** .  

2. Si vous voyez toujours ce message d’erreur, modifiez votre code en ajoutant l’applet de commande **Get-AzureSubscription** suivant l’applet de commande **Add-AzureAccount** , puis exécutez le code.  Maintenant, vérifiez si le résultat de Get-AzureSubscription contient les détails de votre abonnement.  
    * Si vous ne voyez pas les détails de l’abonnement dans le résultat, cela signifie que l’abonnement n’est pas encore initialisé.  
    * Si vous ne voyez pas les détails de l’abonnement dans le résultat, vérifiez que vous utilisez le nom de l’abonnement correct ou l’ID avec l’applet de commande **Sélectionner AzureSubscription** .   


### <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a>Scénario : Authentification Azure a échoué car l’authentification multifacteur est activée

**Erreur :** Vous recevez l’erreur « ajouter AzureAccount : AADSTS50079 : inscription de l’authentification renforcée (preuve en haut) est requise » lors de l’authentification Azure Azure nom d’utilisateur et mot de passe.

**Raison de l’erreur :** Si vous avez l’authentification multifacteur sur votre compte Azure, vous ne pouvez pas utiliser un utilisateur Azure Active Directory s’authentifier à Azure.  À la place, vous devez utiliser un certificat ou un service principal s’authentifier à Azure.

**Des conseils de dépannage :** Pour utiliser un certificat avec les applets de commande de gestion des services Azure, reportez-vous à [Création et l’ajout d’un certificat pour gérer les services Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Pour utiliser un principal de service avec le Gestionnaire de ressources Azure les applets de commande, voir la rubrique [Création principal du service à l’aide du portail Azure](./resource-group-create-service-principal-portal.md) et [authentifier un principal de service avec le Gestionnaire de ressources Azure.](./resource-group-authenticate-service-principal.md)


## <a name="troubleshoot-common-errors-when-working-with-runbooks"></a>Résoudre les erreurs courantes lorsque vous travaillez avec des procédures opérationnelles

### <a name="scenario-runbook-fails-because-of-deserialized-object"></a>Scénario : Runbook échoue en raison d’objet désérialisé

**Erreur :** Votre runbook échoue avec l’erreur « Impossible de lier le paramètre ``<ParameterName>``. Impossible de convertir le ``<ParameterType>`` valeur de type Deserialized ``<ParameterType>`` à taper ``<ParameterType>``».

**Raison de l’erreur :** Si votre runbook est un flux de travail PowerShell, il stocke les objets complexes dans un format désérialisé pour conserver votre état runbook si le flux de travail est suspendue.  

**Conseils de dépannage :**  
Un des trois solutions suivantes pour corriger ce problème :

1. Si vous soyez tuyauterie objets complexes à partir d’une applet de commande à l’autre, entourer ces applets de commande dans un InlineScript.  
2. Transmettre le nom ou la valeur dont vous avez besoin de l’objet complexe au lieu de passer de l’objet dans son intégralité.  

3. Utilisez un runbook PowerShell au lieu d’un flux de travail PowerShell runbook.  


### <a name="scenario-runbook-job-failed-because-the-allocated-quota-exceeded"></a>Scénario : Runbook tâche a échoué car le quota alloué dépassé

**Erreur :** Votre travail runbook échoue avec l’erreur « le quota pour le travail total mensuel exécution a été atteinte pour cet abonnement ».

**Raison de l’erreur :** Cette erreur se produit lorsque l’exécution du travail dépasse le quota gratuit 500 minutes pour votre compte. Ce quota s’applique à tous les types de tâches de l’exécution de projet tels que des tests d’une tâche, démarrage d’une tâche à partir du portail, l’exécution d’une tâche à l’aide de webhooks et planification d’un travail à exécuter à l’aide du portail Azure ou dans votre centre de données. Pour en savoir plus sur les tarifs pour Automation voir [Automation tarifs](https://azure.microsoft.com/pricing/details/automation/).

**Des conseils de dépannage :** Si vous souhaitez utiliser plus de 500 minutes de traitement par mois, vous devez modifier votre abonnement à partir de la couche vers le niveau de base. Vous pouvez mettre à niveau vers le niveau de base en procédant comme suit :  

1. Se connecter à votre abonnement Azure  
2. Sélectionnez le compte d’automatisation que vous souhaitez mettre à jour  
3. Cliquez sur **paramètres** > **couche de tarification et l’utilisation** > **couche tarification**  
4. Sur la carte de **Choisir votre niveau de tarification** , sélectionnez de **base**    


### <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a>Scénario : Applet de commande non reconnu lors de l’exécution d’une procédure opérationnelle

**Erreur :** Votre travail runbook échoue avec l’erreur «``<cmdlet name>``: le terme ``<cmdlet name>`` n’est pas reconnu comme le nom d’une applet de commande, fonction, fichier de script ou un programme exécutable. »

**Raison de l’erreur :** Cette erreur est due lorsque le moteur PowerShell ne peut pas trouver l’applet de commande que vous utilisez dans votre runbook.  Cela peut être parce que le module qui contient l’applet de commande n’apparaît pas dans le compte, il existe un conflit de noms avec un nom runbook, ou l’applet de commande existe également dans un autre module et Automation ne peut pas résoudre le nom.

**Des conseils de dépannage :** Une des solutions suivantes permet de corriger le problème :  

- Vérifiez que vous avez correctement entré le nom de l’applet de commande.  

- Vérifiez que l’applet de commande existe dans votre compte Automation et qu’il n’y a pas de conflit. Pour vérifier si l’applet de commande est présente, ouvrez un runbook en mode édition et recherchez l’applet de commande que vous recherchez dans la bibliothèque ou exécuter **Get-Command ``<CommandName>`` **.  Une fois que vous avez validé que l’applet de commande n’est disponible pour le compte et qu’il n’y a aucun nom est en conflit avec d’autres applets de commande ou les procédures opérationnelles, ajoutez-le à la zone de dessin et vous assurer que vous utilisez un paramètre valide défini dans votre runbook.  

- Si vous n’avez pas un conflit de noms et l’applet de commande n’est disponible dans deux modules différents, vous pouvez résoudre ce problème en utilisant le nom complet pour l’applet de commande. Par exemple, vous pouvez utiliser **ModuleName\CmdletName**.  

- Si vous exécutez la procédure opérationnelle en local dans un groupe de travail hybride, assurez-vous que le module/applet de commande est installé sur l’ordinateur qui héberge le travail hybride.


### <a name="scenario-a-long-running-runbook-consistently-fails-with-the-exception-the-job-cannot-continue-running-because-it-was-repeatedly-evicted-from-the-same-checkpoint"></a>Scénario : Une longue runbook ne parvient pas à l’exception : « le travail ne peut pas continuer en cours d’exécution, car il a été supprimé à plusieurs reprises le même point de contrôle ».

**Raison de l’erreur :** Il s’agit par comportement de création en raison de la surveillance « Partager juste » du processus au sein d’automatisation Azure, qui interrompt automatiquement une procédure opérationnelle si elle s’exécute plus de trois heures. Toutefois, le message d’erreur renvoyé ne fournit pas « ensuite » options. Un runbook peut être interrompu pour plusieurs raisons. Interrompt se produire principalement en raison des erreurs. Par exemple, une exception non gérée dans une procédure opérationnelle, une panne du réseau ou un blocage sur le travail Runbook du runbook en cours d’exécution, tous les entraînera le runbook est suspendue et démarrer à partir de son dernier point de contrôle lorsque relancée.

**Des conseils de dépannage :** La solution pour éviter ce problème consiste à utiliser des points de contrôle dans un flux de travail.  Pour en savoir plus faire référence aux [Flux de travail apprentissage PowerShell](automation-powershell-workflow.md#Checkpoints).  Vous trouverez une explication plus approfondie de « Partager juste » et du point de contrôle dans cet article de blog [à l’aide de points de contrôle dans les procédures opérationnelles](https://azure.microsoft.com/en-us/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).


## <a name="troubleshoot-common-errors-when-importing-modules"></a>Résoudre les erreurs courantes lors de l’importation de modules

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a>Scénario : Module ne parvient pas à importer ou applets de commande ne peut pas être exécutée après l’importation

**Erreur :** Un module ne parvient pas à importer ou importe avec succès, mais aucun applets de commande ne sont extraits.

**Raison de l’erreur :** Quelques-unes des raisons un module ne peut ne pas importe correctement à Azure Automation sont :  

- La structure ne correspond pas à la structure Automation a besoin d’être présent.  

- Le module est dépendant dans un autre module qui n’a pas été déployé à votre compte Automation.  

- Le module n’a pas ses dépendances dans le dossier.  

- L’applet de commande **New-AzureRmAutomationModule** est utilisé pour télécharger le module, et vous ne dispose le chemin d’accès complet de stockage ou le module n’avez pas chargées à l’aide d’une URL accessible au public.  

**Conseils de dépannage :**  
Une des solutions suivantes permet de corriger le problème :  

- Assurez-vous que le module suit le format suivant :  
ModuleName.Zip **->** ModuleName ou numéro de Version **->** (ModuleName.psm1, ModuleName.psd1)

- Ouvrez le fichier .psd1 et voyez si le module comporte des dépendances.  Si c’est le cas, télécharger ces modules sur le compte d’automatisation.  

- Vérifiez que les DLL référencée sont présents dans le dossier modules.  


## <a name="troubleshoot-common-errors-when-working-with-desired-state-configuration-dsc"></a>Résoudre les erreurs courantes lorsque vous travaillez avec vous le souhaitez état Configuration (DSC)  

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a>Scénario : Nœud est dans l’état d’échec avec une erreur « Non trouvé »

**Erreur :** Le nœud dispose d’un rapport avec l’état **Échec** et qui contient l’erreur « la tentative d’obtenir l’action de serveur https://``<url>``//accounts/``<account-id>``/Nodes(AgentId=``<agent-id>``)/GetDscAction a échoué car une configuration correcte ``<guid>`` ne peut pas être trouvé. »

**Raison de l’erreur :** Cette erreur se produit généralement lorsque le nœud est affecté à un nom de configuration (par exemple, ABC) au lieu d’un nom de configuration nœud (par exemple, ABC. Serveur Web).  

**Conseils de dépannage :**  

- Vérifiez que vous affectez le nœud avec « nœud configuration nom » et non la « configuration ».  

- Vous pouvez affecter une configuration nœud à un nœud à l’aide du portail Azure ou avec une applet de commande PowerShell.
    - Pour attribuer une configuration nœud à un nœud à l’aide du portail Azure, ouvrir la carte **DSC nœuds** , puis sélectionnez un nœud et cliquez sur bouton **affecter la configuration de nœud** .  
    - Pour affecter une configuration nœud à un nœud à l’aide d’applet de commande PowerShell, utiliser l’applet de commande **Set-AzureRmAutomationDscNode**


### <a name="scenario--no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a>Scénario : Aucune configuration nœud (fichiers MOF) ne produites lors de la compilation d’une configuration

**Erreur :** Votre travail de compilation DSC interrompt avec l’erreur : « Compilation est terminée, mais aucun .mofs configuration nœud ne générés ».

**Raison de l’erreur :** Lorsque l’expression après que le mot clé **nœud** dans la configuration DSC évalue $null puis aucune configuration nœud sera produit.    

**Conseils de dépannage :**  
Une des solutions suivantes permet de corriger le problème :  

- Assurez-vous que l’expression en regard du mot clé **nœud** dans la définition de la configuration n’est pas l’évaluation à $null.  
- Si vous passez ConfigurationData lors de la compilation la configuration, vérifiez que vous passez les valeurs prévues qui requiert la configuration de [ConfigurationData](automation-dsc-compile.md#configurationdata).


### <a name="scenario--the-dsc-node-report-becomes-stuck-in-progress-state"></a>Scénario : Le rapport de nœud DSC devient bloqué l’état « en cours »

**Erreur :** Agent DSC ne renvoie « Aucune instance trouvée avec les valeurs de propriété données. »

**Raison de l’erreur :** Vous avez mis à niveau votre version WMF et endommagé WMI.  

**Des conseils de dépannage :** Suivez les instructions dans le billet de blog [DSC problèmes connus et limitations](https://msdn.microsoft.com/powershell/wmf/limitation_dsc) de résoudre le problème.


### <a name="scenario--unable-to-use-a-credential-in-a-dsc-configuration"></a>Scénario : Impossible d’utiliser les informations d’identification dans une configuration DSC

**Erreur :** Votre travail de compilation DSC a été suspendue avec l’erreur : « erreur System.InvalidOperationException propriété « D’informations d’identification » du type de traitement ``<some resource name>``: convertir et stocker un mot de passe chiffré comme texte brut est autorisé uniquement si PSDscAllowPlainTextPassword est défini sur true ».

**Raison de l’erreur :** Vous avez utilisé des informations d’identification dans une configuration mais que vous n’a pas été fournissez NOMPROPRE **ConfigurationData** pour la valeur true pour chaque configuration nœud **PSDscAllowPlainTextPassword** .  

**Conseils de dépannage :**  
- Veillez à passer dans le bon **ConfigurationData** à **PSDscAllowPlainTextPassword** la valeur true pour chaque configuration nœud mentionnée dans la configuration. Pour plus d’informations, reportez-vous à [actifs dans Azure Automation DSC](automation-dsc-compile.md#assets).


## <a name="next-steps"></a>Étapes suivantes

Si vous avez suivi les étapes de dépannage ci-dessus et que vous avez besoin d’aide à tout moment dans cet article, vous pouvez :

- Obtenir de l’aide d’experts en Azure. Soumettre votre problème à le [forums MSDN Azure ou débordement de pile.](https://azure.microsoft.com/support/forums/)

- Un incident de support Azure du fichier. Accédez au [site Azure prend en charge](https://azure.microsoft.com/support/options/) , puis cliquez sur **obtenir une assistance technique** sous **support technique et de facturation**.

- Si vous recherchez une solution runbook Azure Automation ou un module d’intégration, publiez une demande de Script sur [Centre de scripts](https://azure.microsoft.com/documentation/scripts/) .

- Envoyer des demandes de fonctionnalité de commentaires ou d’automatisation Azure sur [Voix utilisateur](https://feedback.azure.com/forums/34192--general-feedback).
