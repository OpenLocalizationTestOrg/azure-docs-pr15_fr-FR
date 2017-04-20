<properties 
    pageTitle="Utiliser d’émoticônes Emoji dans Azure Mobile Engagement" 
    description="Comment utiliser d’émoticônes Emoji au sein de votre les notifications push"     
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="use-emoji-emoticon-within-push-notifications"></a>Utiliser des Emoji émoticône au sein de Notifications de transmission

Vous pouvez inclure des Emoji émoticônes dans votre notification d’émission en quelques étapes simples : 

1. Vous avez seulement que besoin pour rechercher la Emoji d’abord vous voulez envoyer dans un message. Assurez-vous que la Emoji que vous sélectionnez est pris en charge par le périphérique cible comme les fabricants de périphériques prennent un certain temps pour ajouter Emojis nouvellement approuvées aux plateformes d’appareil. 

2. Sur **Windows** - vous pouvez accéder à ce [lien](http://apps.timwhitlock.info/emoji/tables/unicode) et copiez l’icône « Native ».

    ![][7] 

3. Vous trouverez sur **Mac** - les Emojis dans l’application de dictionnaire sous Modifier -> Emoji et symboles.

    ![][6] 

4. À présent, accédez à l’onglet **atteigne** sur le portail Azure Mobile Engagement. Sélectionnez le type de votre notification push (interrogation annonce, etc.). Dans cet exemple, nous choisir un push annonce.

5. Spécifier les différents champs de la notification jusqu'à ce que vous atteigniez le texte de la notification. Il s’agit de l’endroit où vous allez ajouter votre émoticône Emoji. Vous pouvez choisir de le placer dans le titre, le message ou les deux. Vous devez faire glisser et déplacer ou copier le Emoji que vous trouvez à partir des emplacements ci-dessus. 

    ![][1]

6. Remplissez les autres champs de la notification et enregistrez-le. 

7. Lorsque vous exécutez un test ou activez l’annonce vous verrez une notification avec l’émoticône comme spécifié.   

    ![][3] ![][4] ![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png

