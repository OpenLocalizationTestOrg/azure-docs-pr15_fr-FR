<properties
    pageTitle="Configurer la facturation des alertes pour vos abonnements Microsoft Azure | Microsoft Azure"
    description="Décrit comment vous pouvez définir des alertes sur votre facture Azure afin d’éviter de surprise facturation."
    services=""
    documentationCenter=""
    authors="vikdesai"
    manager="mbaldwin"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/18/2016"
    ms.author="vikdesai"/>

# <a name="set-up-billing-alerts-for-your-microsoft-azure-subscriptions"></a>Configurer des alertes de facturation pour vos abonnements Microsoft Azure

Sont préoccupés combien vous dépensez pour chaque mois pour votre abonnement Azure ? Si vous êtes l’administrateur de compte pour un abonnement Azure, vous pouvez utiliser le Service d’alerte facturation Azure pour créer un personnalisé facturation alertes pour vous aider à surveiller et gérer les activités de facturation pour vos comptes Azure.

Ce service est un service aperçu, afin que la première chose que vous avez à faire est d’ouvrir celui-ci. Visitez [la page fonctionnalités de visualisation](https://account.windowsazure.com/PreviewFeatures) dans le portail de gestion de compte Azure pour activer cette fonctionnalité.

## <a name="set-the-alert-threshold-and-email-recipients"></a>Définir les destinataires alertes seuil et la messagerie électronique

Une fois que vous recevez la confirmation de messagerie du service de facturation est activé pour votre abonnement, visitez [la page Abonnements](https://account.windowsazure.com/Subscriptions) dans le portail de compte. Cliquez sur l’abonnement que vous voulez analyser, puis cliquez sur **alertes**.

![][Image1]

Ensuite, cliquez sur **Ajouter une alerte** pour créer votre première : vous pouvez configurer un total de cinq alertes facturation par abonnement, avec un seuil différent et jusqu'à deux destinataires de messagerie pour chaque alerte.

![][Image2]

Lorsque vous ajoutez une alerte, vous lui donnez un nom unique, sélectionnez un seuil de dépense, puis les adresses de messagerie où seront envoyés les alertes. Lorsque vous configurez le seuil, vous pouvez choisir un **Total de facturation** ou un **Crédit monétaire** dans la liste **D’alerte** . Pour un total de facturation, une alerte est envoyée lors de l’abonnement dépenses dépassent le seuil. Pour un crédit monétaire, une alerte est envoyée lorsque crédits monétaires en-dessous de la limite. Crédits monétaires s’appliquent généralement à abonnements associées aux comptes MSDN et les versions d’essai gratuites.

![][Image3]

Azure prend en charge de n’importe quelle adresse de messagerie, mais ne vérifie pas que l’adresse de courrier fonctionne, vérifiez si vos fautes de frappe.

## <a name="check-on-your-alerts"></a>Vérifier vos alertes

Une fois que vous définissez des alertes, le centre de compte répertorie les et indique combien plus vous pouvez configurer. Pour chaque alerte, vous voyez la date et heure de qu'envoi, qu’il s’agisse d’une alerte pour facturation Total ou monétaires de crédit et la limite de que vous configurez. Format de date et heure est coordonner de temps universel 24 heures (UTC) et la date est aaaa-mm-jj format. Cliquez sur le signe d’une alerte dans la liste pour le modifier, ou cliquez sur la Corbeille pour la supprimer.

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png
