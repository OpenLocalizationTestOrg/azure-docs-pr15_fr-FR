<properties
    pageTitle="Notifications de création de rapports Azure Active Directory"
    description="Comment utiliser Azure Active Directory reporting notifications pour se suspect ins."
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/07/2016"
    ms.author="dhanyahk"/>

# <a name="azure-active-directory-reporting-notifications"></a>Notifications de création de rapports Azure Active Directory

## <a name="what-reports-generate-email-notifications"></a>Quels rapports générant des notifications par courrier électronique

Pour l’instant, uniquement l’irrégulier de connexion déclencheurs de rapport d’activité envoyer par courrier électronique les notifications.

## <a name="what-is-an-irregular-sign-in"></a>Qu’est une « irrégulier de connexion » ?

Signe-ins irréguliers sont ceux qui ont été identifiées par notre apprentissage algorithmes, sur la base d’une condition « impossible de voyage » associée à un emplacement de connexion anormale et appareil de l’ordinateur. Cela peut indiquer qu’un pirate informatique a tenté de se connecter à l’aide de ce compte.

## <a name="who-receives-the-email-notifications"></a>Qui reçoit les notifications par courrier électronique ?

Les messages sont envoyés à tous les administrateurs globaux auxquels ont été attribuées une licence Active Directory Premium. Pour vous assurer qu’il est remis, nous envoyer pour les administrateurs autre adresse de messagerie. Les administrateurs doivent inclure aad-alerts-noreply@mail.windowsazure.com dans leur liste des expéditeurs approuvés afin qu’ils ne pas manquer le message électronique.

## <a name="how-often-are-these-emails-sent"></a>La fréquence à laquelle sont ces messages électroniques envoyés ?

Le message électronique est envoyé si 10 nouvelles irréguliers connexion activités se produisent au cours des 30 derniers jours, ou depuis le dernier message a été envoyé, si elle est inférieure.

## <a name="how-do-i-access-the-report-mentioned-in-the-email"></a>Comment accéder à l’état mentionnée dans le courrier électronique ?

Lorsque vous cliquez sur le lien, vous êtes redirigé vers la page de rapport au sein du portail classique Azure. Pour accéder au rapport, vous devez être à la fois :

- Un administrateur ou la co-création-administrateur de votre abonnement Azure

- Un administrateur global dans l’annuaire et attribué une licence Active Directory Premium. Pour plus d’informations, voir [éditions Azure Active Directory](active-directory-editions.md).

## <a name="can-i-turn-off-these-emails"></a>Puis-je désactiver ces messages électroniques ?

Oui, pour désactiver les notifications relatives aux anormales se-ins au sein du portail Azure classique, cliquez sur **configurer**, puis **désactivé** sous la section **Notifications** .

## <a name="whats-next"></a>Ensuite ?
- Introduites dans les rapports de sécurité, d’audit et activité sont disponibles ? Regardez [Azure AD sécurité, d’Audit et les rapports d’activité](active-directory-view-access-usage-reports.md)
- [Prise en main Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Ajouter la marque de votre connexion et le panneau d’accès des pages de l’entreprise](active-directory-add-company-branding.md)
