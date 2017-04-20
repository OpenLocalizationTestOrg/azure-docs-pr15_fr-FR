<properties 
    pageTitle="Résolution des problèmes RemoteApp Azure - échecs d’exécution et connexion d’Application | Microsoft Azure" 
    description="Découvrez comment résoudre les problèmes de démarrage et la liaison vers des applications dans Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



#<a name="troubleshoot-azure-remoteapp---application-launch-and-connection-failures"></a>Résoudre les problèmes de RemoteApp Azure - échecs de connexion et de lancement d’Application 

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Applications hébergées dans Azure RemoteApp peuvent échouer lancer pour plusieurs raisons différentes. Cet article décrit les diverses raisons et essayez d’utilisateurs peuvent recevoir lorsque des messages d’erreur lancer des applications. Il parle également des échecs de connexion. (Mais cet article ne décrit pas les problèmes lors de la signature dans le client Azure RemoteApp.)  

Poursuivez votre lecture pour plus d’informations sur les messages d’erreur courants en raison d’échecs d’exécution et connexion d’application.

##<a name="were-getting-you-set-up-try-again-in-10-minutes"></a>Nous améliorons prise configuration... Essayez à nouveau en 10 minutes.

Cette erreur signifie Qu'azure RemoteApp est mise à l’échelle vers le haut pour respecter la capacité nécessaire de vos utilisateurs. En arrière-plan plusieurs instances Azure RemoteApp machine virtuelle sont créées pour gérer les besoins de vos utilisateurs. En général cette opération dure environ 5 minutes mais peut prendre jusqu'à 10 minutes. Parfois, il ne se produit assez rapidement et ressources sont nécessaires immédiatement. Par exemple un scénario 9 AM où de nombreux utilisateurs devront utiliser votre application dans Azure RemoteAppn en même temps. Dans ce cas vous nous pouvons activer le **mode de capacité** sur le serveur principal. Pour ouvrir un tickets de Support Azure et ou nous envoyer un e-mail à [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com). Veillez à inclure votre ID de l’abonnement dans la demande.  

![Nous recevons configuration](./media/remoteapp-apptrouble/ra-apptrouble1.png)

## <a name="could-not-auto-reconnect-to-your-applications-please-re-launch-your-application"></a>Ne peut pas reconnexion automatique à vos applications, Veuillez relancer votre application  

Ce message d’erreur est souvent si vous utilisiez Azure RemoteApp et puis mettez votre ordinateur en veille plus de 4 heures, puis a votre PC vers le haut et le client Azure RemoteApp tente automatiquement vous reconnecter et délai d’expiration a été dépassée.  Demandez aux utilisateurs d’accéder à l’application et essayez d’ouvrir à partir du client Azure RemoteApp.

![Impossible automatique-de se reconnecter à vos applications](./media/remoteapp-apptrouble/ra-apptrouble2.png) 

## <a name="problems-with-the-temp-profile"></a>Problèmes avec le profil temporaire 

Cette erreur se produit lorsque votre profil utilisateur (disque de profil utilisateur) n’a pas pu monter et que l’utilisateur a reçu un profil temporaire.  Les administrateurs doivent accéder à la collection de sites dans le portail Azure et accédez à l’onglet **Sessions** et essayez de **Fermer la session** de l’utilisateur. Cela va forcer un journal complet supprimer la session utilisateur - puis permettre à l’utilisateur lancer une application à nouveau. En cas d’échec contacter le support technique Azure et ou nous envoyer un e-mail à [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

## <a name="azure-remoteapp-has-stopped-working"></a>RemoteApp Azure a cessé de fonctionner

Ce message d’erreur signifie que le client Azure RemoteApp rencontre un problème et doit être redémarré. Demander aux utilisateurs de fermer : sélectionnez **Fermer le programme** , puis relancez le client Azure RemoteApp.  Si le problème persiste ouverte et les tickets de Support Azure et ou nous envoyer un e-mail à [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

![RemoteApp Azure a cessé de fonctionner](./media/remoteapp-apptrouble/ra-apptrouble3.png)  

## <a name="an-error-occurred-while-remote-desktop-connection-was-accessing-this-resource-retry-the-connection-or-contact-your-system-administrator"></a>Une erreur s’est produite lors de la connexion Bureau à distance a été l’accès à cette ressource. Essayez de vous reconnecter ou contactez votre administrateur système

Il s’agit d’un message d’erreur générique - contacter le support technique Azure et ou nous envoyer un e-mail à [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com) afin que nous puissions examiner. 

![Message Azure RemoteApp générique](./media/remoteapp-apptrouble/ra-apptrouble4.png) 