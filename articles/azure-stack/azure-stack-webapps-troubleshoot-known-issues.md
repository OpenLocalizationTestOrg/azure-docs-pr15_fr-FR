<properties
    pageTitle="Applications sur la pile Azure - problèmes connus et résolution des problèmes Web | Microsoft Azure"
    description="Instructions détaillées pour le déploiement des applications Web dans la pile d’Azure"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="web-apps-resource-provider---known-issues-and-troubleshooting"></a>Fournisseur de ressources des applications Web - problèmes connus et résolution des problèmes

> [AZURE.NOTE] Les informations suivantes s’applique uniquement aux déploiements Azure pile TP1.

Si vous rencontrez des problèmes lors du déploiement ou utiliser des applications Web sur pile Azure consultez les conseils ci-dessous.

## <a name="azure-stack-web-apps-not-available-in-the-portal"></a>Azure pile Web applications non disponible dans le portail

![Pile Azure Web Apps Créer nouvelle application Web][1]

Une fois que vous avez terminé l' [enregistrement du fournisseur applications Web Azure pile](azure-stack-webapps-deploy.md#register-the-newly-deployed-azure-stack-web-apps-provider-with-arm) si vous ne pouvez pas rechercher le Web + carte Mobile puis effectuez les étapes suivantes :
* **Se déconnecter du portail** et **Fermer votre navigateur** , puis dans une **nouvelle connexion de fenêtre de navigateur sur le portail** et essayez à nouveau.

Si vous ne voyez toujours pas le web et la carte mobile essayez les opérations suivantes :

1.  Sur l' **Ordinateur hôte de pile Azure** dans le **Gestionnaire Hyper-V** localiser les **xRPVM** et **se connecter** à la machine virtuelle.
2.  Ouvrez une **invite de commandes en tant qu’administrateur** , puis exécutez **IISRESET**
3.  Revenir à la **ClientVM** et ouvrir une **nouvelle fenêtre de navigateur**, **Connectez-vous au portail** et réessayez.

## <a name="deployment-fails-when-creating-a-web-app-in-a-new-resource-group"></a>Déploiement échoue lors de la création d’une application Web dans un nouveau groupe de ressources

Dans la première aperçu des applications Web, **Toutes les applications Web** doivent être créés dans le même groupe de ressources que le fournisseur de ressources des applications Web (**AppService-LOCAL**).  Ceci est un problème connu et sera résolu dans un aperçu future.

## <a name="other-issues"></a>Autres problèmes

Si vous rencontrez d’autres problèmes avec les applications Web sur Azure pile publient dans [le Forum de la pile Azure] (https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) où les membres de notre équipe sont surveillance billets.


<!--Image references-->
[1]: ./media/azure-stack-webapps-troubleshoot-known-issues/NewWebandMobile.png



<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[WebAppsDeployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
