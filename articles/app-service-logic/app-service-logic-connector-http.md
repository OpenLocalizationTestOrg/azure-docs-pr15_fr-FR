<properties
   pageTitle="À l’aide de la récepteur HTTP et le connecteur dans les applications logique | Service d’application Microsoft Azure "
   description="Comment créer et configurer le récepteur HTTP et HTTP action connecteur ou API application et l’utiliser dans une application logique dans le Service d’application Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/31/2016"
   ms.author="prkumar"/>


# <a name="get-started-with-the-http-listener-and-http-action-and-add-it-to-your-logic-app"></a>Prise en main le récepteur HTTP et l’action HTTP et l’ajouter à votre application logique

> [AZURE.NOTE]Nous allons termine prise en charge pour ce connecteur parce que ses fonctionnalités sont maintenant incluses par défaut dans le **déclenchement manuel** lorsque vous créez de nouvelles applications logique.  Nous vous conseillons de changer toutes vos applications logique qui utilisent ce connecteur.  
> Cette version de l’article s’applique à la version de schéma logique applications 2014-12-01-preview.

Se connecter directement aux ressources HTTP pour écouter les requêtes HTTP et configurer des demandes web HTTP. Il existe certains scénarios dans lequel vous devrez peut-être utiliser des connexions HTTP directes, y compris :

1.  Développement d’une logique de l’application qui prend en charge d’un site Web ou frontal interactif utilisateur mobile.
2.  Pour obtenir et traiter des données à partir d’un service web qui n’a pas une absence de connecteur zone.
3.  Pour effectuer des actions qui se trouvent déjà exposée comme un service web, mais non disponible sous la forme d’une application API.

Pour ces scénarios, il existe deux possibilités :

1. **Récepteur HTTP**: ce connecteur se comporte comme un déclencheur et reçoit des demandes HTTP sur un point de terminaison configuré. Lors de la réception d’un appel sur le point de terminaison configurée, il déclenche une nouvelle instance du flux et transmet les données reçues dans la demande pour le flux de traitement. Il peut également être configuré pour répondre automatiquement à la demande entrante quand le flux a commencé, ou vous permettent de créer une réponse basée sur l’exécution du flux et d’envoyer une réponse à l’appelant.
2. **Action HTTP**: cela vous permet de configurer une requête web à un point de terminaison disponible sur internet, récupère une réponse et rend disponible pour les actions supplémentaires dans le flux de consommer.

Logique applications peuvent déclencher basée sur une variété de sources de données et des connecteurs offre pour récupérer et traiter des données dans le cadre du flux. Vous pouvez ajouter le connecteur HTTP à vos données de flux de travail et processus métier dans le cadre de ce flux de travail au sein d’une application logique. 

## <a name="creating-an-http-listener-for-your-logic-app"></a>Création d’un récepteur HTTP pour votre application logique
Un connecteur peut être créé dans une application logique ou être créé directement à partir de la Azure Marketplace. Pour créer un lien à partir de la place de marché :  

1. Dans la startboard Azure, sélectionnez **Marketplace**.
2. Recherchez « HTTP », sélectionnez HTTP récepteur, puis sélectionnez **créer**.
3.  Configurer le récepteur HTTP comme suit :  
![][1]

4.  Lorsque vous configurez les paramètres du package, vous verrez l’option suivante si le récepteur doit répondre automatiquement ou nécessitent à envoyer une réponse explicite. Définir sur **False** pour envoyer votre propre réponse :  
![][2]

5.  Cliquez sur **OK** pour créer.
6.  Une fois que l’instance de l’application API est créée, ouvrez les paramètres pour configurer la sécurité. Le récepteur HTTP prend actuellement en charge l’authentification de base. Vous pouvez le configurer à l’aide de l’option de sécurité lorsque vous ouvrez le récepteur HTTP :  
![][3]
  
    **Problème connu** Paramètres de *la sécurité afficher « Aucun » comme la valeur par défaut, mais n’est pas définie. Vous devez modifier le paramètre pour Basic et revenir à aucun avant de l’enregistrer pour vous assurer que le récepteur HTTP est correctement configuré.*  

7. Enfin, définissez les paramètres de sécurité de l’application API au Public (anonyme) pour permettre aux clients externes à accéder au point de fin. Ce paramètre est disponible sous « tous les paramètres > Paramètres de l’Application » de l’application de l’API HTTP récepteur :![][10]

Une fois que cela est fait, vous pouvez désormais créer une application logique pour utiliser le récepteur HTTP.

## <a name="using-the-http-listener-in-your-logic-app"></a>À l’aide de l’écoute HTTP dans votre application logique
Une fois que votre application API est créée, vous pouvez désormais utiliser le récepteur HTTP comme un déclencheur pour votre application logique. Pour ce faire, vous devez :

4.  Créer une nouvelle application logique.
5.  Ouvrez « Déclencheurs et Actions » pour ouvrir le Concepteur d’applications logique et configurer votre flux. Le récepteur HTTP est répertorié dans la galerie. Faites votre choix.
6.  Vous pouvez maintenant définir la méthode HTTP et l’URL relative pour lesquels vous souhaitez que l’auditeur déclencher le flux :  
![][4]  
![][5]

7.  Pour obtenir l’URI complet, double-cliquez sur le récepteur HTTP pour afficher ses paramètres de configuration et copiez l’URL de le « hôte » de votre application API :  
![][6]
8.  Vous pouvez désormais utiliser les données reçues dans la demande HTTP dans d’autres actions dans le flux comme suit :  
![][7]  
![][8]
9.  Enfin, pour envoyer une réponse, ajoutez un autre récepteur HTTP, puis sélectionnez l’action Envoyer de réponse HTTP. Définir l’ID de demande pour l’ID de demande obtenu à partir de l’écoute HTTP et remplir le corps de la réponse et le statut HTTP que vous souhaitez revenir en arrière :  
![][9]

## <a name="using-the-http-action"></a>À l’aide de l’action HTTP
L’action HTTP est prise en charge par les applications logique et ne nécessite pas une application API à être créé pour pouvoir l’utiliser. Vous pouvez insérer une action HTTP à tout moment dans votre application logique et choisissez URI, en-têtes et le corps de l’appel.
L’action HTTP prend en charge plusieurs options de sécurité côté client. Afficher les [options de sécurité côté client](../scheduler/scheduler-outbound-authentication.md).

Le résultat de l’action HTTP est en-têtes et le corps, qui peuvent servir davantage en aval dans le flux similaire à la sortie des autres actions et des connecteurs est utilisée.

## <a name="do-more-with-your-connector"></a>Faire plus avec le lien
Maintenant que le lien est créé, vous pouvez l’ajouter à un flux de travail d’entreprise à l’aide d’une application logique. Voir [que sont les applications logique ?](app-service-logic-what-are-logic-apps.md).

Afficher la référence Swagger des API REST en [liens et des API applications référence](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques et contrôler les performances de sécurité au connecteur. Voir [gérer et analyser vos applications API et les connecteurs intégrés](app-service-logic-monitor-your-connectors.md).

> [AZURE.NOTE] Si vous voulez commencer à utiliser les applications de logique Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer une application de logique](https://tryappservice.azure.com/?appservice=logic). Vous pouvez immédiatement créer une application de logique starter courte dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

<!--Image references-->
[1]: ./media/app-service-logic-connector-http/1.png
[2]: ./media/app-service-logic-connector-http/2.png
[3]: ./media/app-service-logic-connector-http/3.png
[4]: ./media/app-service-logic-connector-http/4.png
[5]: ./media/app-service-logic-connector-http/5.png
[6]: ./media/app-service-logic-connector-http/6.png
[7]: ./media/app-service-logic-connector-http/7.png
[8]: ./media/app-service-logic-connector-http/8.png
[9]: ./media/app-service-logic-connector-http/9.png
[10]: ./media/app-service-logic-connector-http/10.png
