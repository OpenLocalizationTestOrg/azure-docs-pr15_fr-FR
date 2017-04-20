<properties
   pageTitle="Comment configurer des alertes de sécurité | Microsoft Azure"
   description="Découvrez comment configurer des alertes de sécurité pour le poste de gestion des identités dotés de privilèges Azure."
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
   ms.date="09/02/2016"
   ms.author="kgremban"/>

# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>Comment configurer des alertes de sécurité dans la gestion des identités Azure AD dotés de privilèges

## <a name="security-alerts"></a>Alertes de sécurité
Gestion d’identité dotés de privilèges Azure (PIM) génère des alertes lorsqu’il y a activité suspecte ou potentiellement dangereux dans votre environnement. Lorsqu’une alerte est déclenchée, il s’affiche sur le tableau de bord PIM. Sélectionnez l’alerte pour afficher un rapport qui répertorie les utilisateurs ou les rôles qui a déclenché l’alerte.

![Alertes de sécurité du tableau de bord PIM - capture d’écran][1]



| Alerte | Déclencheur | Recommandation |
| ----- | ------- | -------------- |
| **Affectation de rôles en dehors PIM** | Un administrateur a été définitivement affecté à un rôle, en dehors de l’interface PIM. | Passez en revue la nouvelle attribution de rôle. Étant donné que d’autres services peuvent uniquement affecter des administrateurs permanents, modifiez-le en une affectation éligible si nécessaire. |
| **Rôles sont activés trop fréquemment** | Trop de réactivations du même rôle se sont produites dans le temps imparti dans les paramètres. | Contactez l’utilisateur pour afficher la raison pour laquelle ils ont activé le rôle autant de fois. Le délai est peut-être trop courte pour les accomplir leurs tâches, ou peut-être qu’ils utilisez scripts activer automatiquement un rôle. |
| **Rôles ne nécessitent pas l’authentification multifacteur pour l’activation** | Il existe des rôles sans l’authentification Multifacteur activé dans les paramètres. | Nous nécessite l’authentification Multifacteur pour les rôles plus hautement privilégiés, mais que vous invitons que vous activez l’authentification Multifacteur pour l’activation de tous les rôles. |
| **Les administrateurs ne sont pas à l’aide de leurs rôles dotés de privilèges** | Il existe éligibles administrateurs qui n’ont pas activé leur rôle récemment. | Démarrer une révision access pour déterminer les utilisateurs qui n’avez plus besoin access. |
| **Il existe trop d’administrateurs globaux** | Il existe un administrateur global plus à recommandées. | Si vous avez un grand nombre d’administrateurs globaux, il est probable que les utilisateurs obtenez plus d’autorisations que dont elles ont besoin. Déplacer les utilisateurs vers moindre privilèges rôles ou certaines d'entre elles Vérifiez éligible pour le rôle au lieu d’attribuée de manière permanente. |

## <a name="configure-security-alert-settings"></a>Configurer les paramètres d’alertes de sécurité

Vous pouvez personnaliser certaines des alertes de sécurité dans PIM pour l’utiliser avec votre environnement et aux objectifs de sécurité. Suivez ces étapes pour accéder à la carte de paramètres :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et sélectionnez la vignette de **la gestion des identités Azure AD dotés de privilèges** du tableau de bord.
2. Sélectionnez **géré rôles dotés de privilèges** > **paramètres** > **les paramètres d’alerte**.

    ![Accédez à paramètres d’alertes de sécurité][2]

### <a name="roles-are-being-activated-too-frequently-alert"></a>Alerte « Rôles sont activés trop fréquemment »

Cette alerte se déclenche si un utilisateur Active le même rôle dotés de privilèges plusieurs fois pendant une période spécifiée. Vous pouvez configurer la période de temps et le nombre d’activations.

- **Période de renouvellement de l’activation**: spécifier en jours, heures, minutes et la seconde de la période de temps que vous souhaitez utiliser pour effectuer le suivi de renouvellement suspect.

- **Nombre de renouvellement de l’activation**: spécifier le nombre d’activations autorisées à partir de 2 à 100, que vous considérez comme digne d’alerte, dans le délai que vous avez choisi. Vous pouvez modifier ce paramètre en déplaçant le curseur, ou en tapant un nombre dans la zone de texte.


### <a name="there-are-too-many-global-administrators-alert"></a>Alerte « Il existe trop d’administrateurs globaux »

PIM déclenche cette alerte si deux différents critères sont remplies, et vous pouvez configurer les deux. Tout d’abord, vous devez joindre un certain seuil d’administrateurs globaux. Ensuite, un certain pourcentage vos total d’attributions des rôles doit être un administrateur global. Si vous seulement répond pas à un de ces mesures, l’alerte n’apparaît pas.  

- **Nombre minimal d’administrateurs globaux**: spécifier le nombre d’administrateurs globaux, à partir de 2 à 100, que vous considérez comme un montant potentiellement dangereux.

- **Pourcentage d’un administrateur global**: indiquer le pourcentage d’administrateurs sont des administrateurs globaux, de 0 à 100 %, qui est potentiellement dangereux dans votre environnement.

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>« Les administrateurs ne sont pas à l’aide de leurs rôles dotés de privilèges » alerte

Cette alerte se déclenche si un utilisateur prend un certain laps de temps sans l’activation d’un rôle.

- **Nombre de jours**: spécifier le nombre de jours, comprise entre 0 et 100, un utilisateur peut accéder sans l’activation d’un rôle.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png
