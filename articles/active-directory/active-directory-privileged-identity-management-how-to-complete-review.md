<properties
   pageTitle="Comment effectuer une révision access | Microsoft Azure"
   description="Une fois que vous avez commencé une révision d’accès dans la gestion des identités Azure AD dotés de privilèges, découvrez comment compléter et afficher les résultats"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/30/2016"
   ms.author="kgremban"/>

# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Comment effectuer une révision d’accès dans la gestion des identités Azure AD dotés de privilèges


Administrateurs de rôles dotés de privilèges peuvent consulter d’accès privilégié une fois une [révision de sécurité a été démarrée](active-directory-privileged-identity-management-how-to-start-security-review.md). Azure AD privilégié identité gestion envoie automatiquement un message électronique demander aux utilisateurs de consulter leur accès. Si un utilisateur n’a pas reçu un message électronique, vous pouvez leur envoyer des instructions de la rubrique [comment effectuer une révision de sécurité](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Une fois la période de révision de sécurité, ou tous les utilisateurs ont terminé leur réviser automatique, suivez les étapes décrites dans cet article pour gérer la révision et afficher les résultats.

## <a name="manage-security-reviews"></a>Gérer les révisions de sécurité

1. Accédez au [portail Azure](https://portal.azure.com/) et sélectionnez l’application de **Gestion des identités Azure AD dotés de privilèges** dans votre tableau de bord.
2. Sélectionnez la section **Access analyse** du tableau de bord.
3. Sélectionnez la révision access que vous voulez gérer.

Sur la carte de détails de la révision access, il existe un nombre d’options pour gérer cette révision.

![Boutons de révision access PIM - capture d’écran][1]

### <a name="remind"></a>Rappeler

Si une révision access a été configurée pour que les utilisateurs consultent eux-mêmes, le bouton **Envoyer un rappel aux** envoie une notification. 

### <a name="stop"></a>Arrêter

Toutes les révisions access ont une date de fin, mais vous pouvez utiliser le bouton **Arrêter** pour fin au plus tôt. Si tous les utilisateurs n’ont pas été révisés à ce stade, ils ne pourrez une fois que vous arrêtez la révision. Vous ne pouvez pas redémarrer une révision après est arrêté.

### <a name="apply"></a>Appliquer

Une fois une révision access terminée, soit car vous atteint la date de fin ou arrêté manuellement, le bouton **Appliquer** met en œuvre le résultat de la révision. Si l’accès d’un utilisateur a été refusé lors de la révision, il s’agit de l’étape destiné à supprimer leur attribution de rôle.  

### <a name="export"></a>Exporter

Si vous souhaitez appliquer les résultats de la révision de sécurité manuellement, vous pouvez exporter la révision. Le bouton **Exporter** commence à télécharger un fichier CSV. Vous pouvez gérer les résultats dans Excel ou d’autres programmes ouvrir des fichiers CSV.

### <a name="delete"></a>Supprimer

Si vous n’êtes pas la révision davantage d’informations, supprimez-le. Le bouton **Supprimer** supprime la révision de l’application PIM.

> [AZURE.IMPORTANT] Pas vous obtenez un message d’avertissement avant la suppression, assurez-vous que vous voulez supprimer cette révision.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png
