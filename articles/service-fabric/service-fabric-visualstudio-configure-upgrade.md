<properties
   pageTitle="Configurer la mise à niveau d’une application de Service TISSU | Microsoft Azure"
   description="Découvrez comment configurer les paramètres pour la mise à niveau une application de Service tissu à l’aide de Microsoft Visual Studio."
   services="service-fabric"
   documentationCenter="na"
   authors="cawaMS"
   manager="paulyuk"
   editor="tglee" />
<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="07/29/2016"
   ms.author="cawa" />

# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Configurer la mise à niveau d’une application de Service tissu dans Visual Studio

Visual Studio tools pour tissu de Service Azure fournissent mise à niveau de prise en charge pour la publication sur clusters locales ou distantes. Il existe deux avantages à la mise à niveau votre application pour une version plus récente à la place de remplacement de l’application au cours de test et de débogage :

- Données d’application ne seront pas perdues lors de la mise à niveau.
- Disponibilité reste élevée il y toute interruption de service pendant la mise à niveau, s’il existe que suffisamment d’instances de service réparties domaines mise à niveau.

Tests peuvent être exécutés par rapport à une application pendant qu’il est mis à niveau.

## <a name="parameters-needed-to-upgrade"></a>Paramètres nécessaires à la mise à niveau

Vous avez le choix entre deux types de déploiement : normal ou mise à niveau. Un déploiement normal efface les données sur le cluster et les informations relatives au déploiement précédent pendant qu’un déploiement de mise à niveau conserve il. Lorsque vous mettez à niveau une application de Service tissu dans Visual Studio, vous devez fournir santé et paramètres de mise à niveau d’application vérifier les stratégies. Paramètres de mise à niveau d’application permettent de contrôler la mise à niveau, tandis que les stratégies de vérification d’intégrité déterminent si la mise à niveau a réussi. Voir [mise à niveau de Service TISSU application : paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md) pour plus d’informations.

Il existe trois modes de mise à niveau : *contrôlés*, *UnmonitoredAuto*et *UnmonitoredManual*.

  - Une mise à niveau contrôlés automatise la mise à niveau et de vérification d’intégrité d’application.

  - Une mise à niveau UnmonitoredAuto automatise la mise à niveau, mais ignore la vérification d’intégrité application.

  - Lorsque vous effectuez une mise à niveau UnmonitoredManual, vous devez mettre à niveau manuellement chaque domaine mise à niveau.

Chaque mode mise à niveau nécessite différents jeux de paramètres. Reportez-vous [Application mise à niveau des paramètres](service-fabric-application-upgrade-parameters.md) pour en savoir plus sur les options de mise à niveau disponibles.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Mettre à niveau une application de Service tissu dans Visual Studio

Si vous utilisez les outils tissu de Service Visual Studio pour mettre à niveau une application de Service tissu, vous pouvez spécifier un processus de publication pour être une mise à niveau au lieu d’un déploiement normal en cochant la case à cocher **mettre à niveau de l’application** .

### <a name="to-configure-the-upgrade-parameters"></a>Pour configurer les paramètres de mise à niveau

1. Cliquez sur le bouton **paramètres** en regard de la case à cocher. La boîte de dialogue **Modifier les paramètres de mise à niveau** s’affiche. La boîte de dialogue **Modifier les paramètres de mise à niveau** prend en charge les modes de mise à niveau contrôlés, UnmonitoredAuto et UnmonitoredManual.

2. Sélectionnez le mode de mise à niveau que vous souhaitez utiliser et puis remplissez la grille de paramètre.

    Chaque paramètre comporte les valeurs par défaut. Le paramètre facultatif *DefaultServiceTypeHealthPolicy* prend une entrée de table hachage. Voici un exemple du format d’entrée de table hachage pour *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* est un autre paramètre facultatif qui prend une entrée de table hachage au format suivant :

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Voici un exemple réels :

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```

3. Si vous sélectionnez le mode de mise à niveau UnmonitoredManual, vous devez démarrer manuellement une console PowerShell pour continuer et terminer le processus de mise à niveau. Reportez-vous à [mise à niveau de Service TISSU application : rubriques avancées](service-fabric-application-upgrade-advanced.md) pour découvrir comment manuel Mise à niveau fonctionne.

## <a name="upgrade-an-application-by-using-powershell"></a>Mise à niveau une application à l’aide de PowerShell

Vous pouvez utiliser les applets de commande PowerShell pour mettre à niveau une application de Service tissu. Pour plus d’informations, voir [application Service TISSU didacticiel de mise à niveau](service-fabric-application-upgrade-tutorial.md) et [Démarrage ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/mt125975.aspx) .

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Spécifier une stratégie de vérification d’intégrité dans le fichier manifeste d’application

Chaque service dans une application de Service TISSU peut avoir ses propres paramètres de stratégie d’intégrité qui remplacent les valeurs par défaut. Vous pouvez fournir ces valeurs de paramètre dans le fichier manifeste d’application.

L’exemple suivant montre comment appliquer une stratégie de vérification d’intégrité unique pour chaque service dans le manifeste d’application.

```
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le déploiement d’une application, voir [déployer une application existante dans Azure Service TISSU](service-fabric-deploy-existing-app.md).
