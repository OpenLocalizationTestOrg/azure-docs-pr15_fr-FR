## <a name="how-to-deploy-with-powershell"></a>Comment déployer avec PowerShell

1. Connectez-vous à votre compte Azure.

          Add-AzureAccount

   Après avoir fourni vos informations d’identification, la commande renvoie des informations sur votre compte.

          Id                             Type       ...
          --                             ----    
          example@contoso.com            User       ...   

2. Si vous avez plusieurs abonnements, fournissent l’id de l’abonnement que vous souhaitez utiliser pour le déploiement. 

          Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Basculez vers le module Azure le Gestionnaire de ressources.

          Switch-AzureMode AzureResourceManager

4. Si vous ne disposez pas d’un groupe de ressources existant, créez un nouveau groupe de ressources. Indiquez le nom du groupe de ressources et emplacement dont vous avez besoin pour votre solution.

        New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"

   Un résumé du nouveau groupe de ressources est renvoyé.

        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. Pour créer un nouveau déploiement pour votre groupe de ressources, exécutez la commande **New-AzureResourceGroupDeployment** et fournissez les paramètres nécessaires. Les paramètres doit inclure un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d’accès ou l’URL pour le modèle que vous avez créé et d’autres paramètres nécessaires pour votre scénario. 
   
   Vous disposez des options suivantes pour fournir des valeurs de paramètre : 
   
   - Utiliser des paramètres en ligne.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

   - Utiliser un objet de paramètre.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

   - À l’aide d’un fichier de paramètres.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

  Lorsque le groupe de ressources a été déployé, vous verrez une synthèse du déploiement.

             DeploymentName    : ExampleDeployment
             ResourceGroupName : ExampleResourceGroup
             ProvisioningState : Succeeded
             Timestamp         : 4/14/2015 7:00:27 PM
             Mode              : Incremental
             ...

6. Pour obtenir des informations concernant les échecs de déploiement.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. Pour obtenir des informations détaillées concernant les échecs de déploiement.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput
