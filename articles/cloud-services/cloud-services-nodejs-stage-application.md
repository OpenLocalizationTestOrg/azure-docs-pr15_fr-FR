<properties 
    pageTitle="Planifier un déploiement de service cloud (Node.js) | Microsoft Azure" 
    description="Découvrez comment déployer votre application Azure dans un environnement intermédiaire, puis déployez dans un environnement de production à l’aide de permutation (virtuelle IP)." 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>



# <a name="staging-an-application-in-azure"></a>Mise en attente d’une Application dans Azure

Une application peut être déployée dans l’environnement intermédiaire dans Azure soit testée avant de passer à l’environnement de production dans lequel l’application est accessible sur Internet. L’environnement intermédiaire est identique à l’environnement de production, sauf que vous ne pouvez accéder à l’application intermédiaire avec une URL masquée est générée par Azure. Après avoir vérifié que votre application fonctionne correctement, il peut être déployé à l’environnement de production en effectuant une permutation (virtuelle IP).

> [AZURE.NOTE] Les étapes décrites dans cet article s’applique uniquement aux applications nœud hébergées comme un Service Cloud Azure.

## <a name="step-1-stage-an-application"></a>Étape 1 : Préparer une Application

Cette tâche explique comment préparer une application à l’aide de **Microsoft Azure PowerShell**.

1.  Lorsque vous publiez un service, il suffit de transmettre la **-emplacement** paramètre à l’applet de commande **Publier AzureServiceProject** .

    ```powershell
    Publish-AzureServiceProject -Slot staging
    ```

2.  Connectez-vous au [portail classique Azure] , puis sélectionnez **Les Services en nuage**. Une fois que le service cloud est créé et l’état de la colonne **mise en attente** a été mis à jour en cours **d’exécution**, cliquez sur le nom du service.

    ![portail affichant un service en cours d’exécution][cloud-service]

3.  Sélectionnez le **tableau de bord**, puis **mise en attente**.

    ![tableau de bord de service cloud][cloud-service-dashboard]

4. Notez la valeur de l’entrée de **L’URL du Site** vers la droite. Le nom DNS est un ID interne masqué qui Azure générée.

    ![url du site][cloud-service-staging-url]

Désormais, vous pouvez vérifier que l’application fonctionne correctement dans l’environnement intermédiaire à l’aide de l’URL du site intermédiaire.

## <a name="step-2-upgrade-an-application-in-production-by-swapping-vips"></a>Étape 2 : Mettre à niveau une Application en Production par échange VIP

Une fois que vous avez vérifié la version mise à niveau d’une application dans l’environnement intermédiaire, vous pouvez rapidement le rendez disponible en production en remplaçant les adresses IP virtuelle (VIP) des environnements intermédiaires et de production.

> [AZURE.NOTE] Cette étape part du principe que vous avez déjà déployé une application en production et mis en place de la version mise à niveau de l’application.

1.  Se connecter au [portail classique Azure], cliquez sur **Les Services en nuage** , puis sélectionnez le nom du service.

2.  À partir du **tableau de bord**, sélectionnez **mise en attente**, puis cliquez sur **intervertir** en bas de la page. Cette action ouvre la boîte de dialogue permutation VIP.

    ![boîte de dialogue VIP permutation][vip-swap-dialog]

3.  Vérifiez les informations, puis cliquez sur **OK**. Les deux déploiements mettre à jour les alors que le déploiement intermédiaire bascule en production et le déploiement de production passe mise en attente.

Vous avez correctement mis en place un déploiement et mis à niveau un déploiement de production par inversion VIP avec le déploiement de reclassement.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Comment déployer une mise à niveau du Service sur Production en remplaçant VIP dans Azure]

[Portail classique Azure]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
[cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
[vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
[Comment déployer une mise à niveau du Service sur Production par inversion VIP dans Azure]: cloud-services-how-to-manage.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
