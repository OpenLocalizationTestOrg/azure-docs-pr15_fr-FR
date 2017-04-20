<properties
   pageTitle="Azure Active Directory création de rapports : Mise en route | Microsoft Azure"
   description="Répertorie les différents rapports disponibles dans le rapport d’Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/07/2016"
   ms.author="dhanyahk"/>

# <a name="getting-started-with-azure-active-directory-reporting"></a>Prise en main Azure Active Directory Reporting

## <a name="what-it-is"></a>Il est

Azure Active Directory (AD Azure) inclut la sécurité, l’activité et les rapports d’audit pour les recherches dans l’annuaire. Voici une liste des rapports fournis :

### <a name="security-reports"></a>Rapports de sécurité

- Signe-ins provenant de sources inconnues
- Signe-ins après plusieurs échecs
- Signe-ins à partir de plusieurs zones géographiques
- Signe-ins provenant d’adresses IP avec une activité suspecte
- Activité de connexion irrégulier
- Signe-ins à partir d’appareils éventuellement infectés
- Utilisateurs avec anormale connexion à une activité

### <a name="activity-reports"></a>Rapports d’activité

- Utilisation de l’application : synthèse
- Utilisation de l’application : détaillées
- Tableau de bord application
- Erreurs de mise en service du compte
- Appareils utilisateur individuel
- Activité de l’utilisateur individuelle
- Rapport d’activité de groupes
- Rapport d’activité de mot de passe réinitialiser l’enregistrement
- Activité de réinitialisation du mot de passe

### <a name="audit-reports"></a>Rapports d’audit

- Rapport d’audit d’annuaires

> [AZURE.TIP] Pour plus d’informations sur la création de rapports AD Azure, consultez [Afficher vos rapports access et l’utilisation](active-directory-view-access-usage-reports.md).



## <a name="how-it-works"></a>Mode de fonctionnement


### <a name="reporting-pipeline"></a>Pipeline de création de rapports

Le pipeline de création de rapports est composée de trois étapes principales. Chaque fois qu’un utilisateur se connecte ou une authentification est effectuée, les événements suivants se produisent :

- Tout d’abord, il est authentifié (avec ou sans succès) et le résultat est stocké dans les bases de données de service Azure Active Directory.
- À intervalles réguliers, signe récentes tous les compléments sont traités. À ce stade, notre sécurité et algorithmes activité anormale recherchez se récentes tous les compléments d’activité suspect.
- Après le traitement, les rapports sont écrits mis en cache et pris en charge dans le portail classique Azure.

### <a name="report-generation-times"></a>Déclarer les heures de génération

En raison du volume d’authentifications et se ins traitées par la plateforme Azure AD, les plus récents se-ins traitées sont en moyenne, datant d’une heures. Rarement, il peut prendre jusqu'à 8 heures pour traiter les plus récentes se-ins.

Vous pouvez trouver la plus récente transformé se connecter en examinant le texte d’aide en haut de chaque rapport.

![Texte d’aide en haut de chaque rapport](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [AZURE.TIP] Pour plus d’informations sur la création de rapports AD Azure, consultez [Afficher vos rapports access et l’utilisation](active-directory-view-access-usage-reports.md).



## <a name="getting-started"></a>Prise en main


### <a name="sign-into-the-azure-classic-portal"></a>Se connecter au portail classique Azure

Tout d’abord, vous devez vous connecter dans le [portail classique Azure](https://manage.windowsazure.com) en qu’un administrateur global ou administrateur de conformité. Vous doivent également être un administrateur du service d’abonnement Azure ou administrateur de co-création, ou être à l’aide de le « accès à Azure AD » abonnement Azure.

### <a name="navigate-to-reports"></a>Accédez aux rapports

Pour afficher les rapports, accédez à l’onglet rapports en haut de l’annuaire.

S’il s’agit d’affichage des rapports pour la première fois, vous devez accepter une boîte de dialogue avant de pouvoir afficher les rapports. Il s’agit pour vous assurer qu’il est acceptable pour les administrateurs de votre organisation d’afficher ces données, qui peuvent être considérées comme des informations personnelles dans certains pays.

![Boîte de dialogue](./media/active-directory-reporting-getting-started/dialogBox.png)

### <a name="explore-each-report"></a>Explorez chaque état

Naviguer dans chaque rapport pour afficher les données collectées et les signe-ins traitées. Vous pouvez rechercher une [liste de tous les rapports ici](active-directory-reporting-guide.md).

![Tous les rapports](./media/active-directory-reporting-getting-started/reportsMain.png)

### <a name="download-the-reports-as-csv"></a>Télécharger les rapports au format CSV

Chaque rapport peut être téléchargé comme un fichier CSV (valeurs séparées par des virgules). Vous pouvez utiliser ces fichiers dans Excel, PowerBI ou analyse tiers programmes supplémentaires pour analyser vos données.

Pour télécharger les rapports au format CSV, accédez au rapport, puis cliquez sur « Télécharger » dans la partie inférieure.

![Bouton Télécharger](./media/active-directory-reporting-getting-started/downloadButton.png)

> [AZURE.TIP] Pour plus d’informations sur la création de rapports AD Azure, consultez [Afficher vos rapports access et l’utilisation](active-directory-view-access-usage-reports.md).





## <a name="next-steps"></a>Étapes suivantes

### <a name="customize-alerts-for-anomalous-sign-in-activity"></a>Personnaliser les alertes pour s’anormale d’activité

Accédez à l’onglet « Configuration » de l’annuaire.

Faites défiler jusqu'à la section « Notifications ».

Activer ou désactiver la section « Envoyer par courrier électronique les Notifications de signe-ins anormales ».

![La section Notifications](./media/active-directory-reporting-getting-started/notificationsSection.png)

### <a name="integrate-with-the-azure-ad-reporting-api"></a>Intégrer la publicité Azure API de création de rapports

Voir [prise en main de l’API de création de rapports](active-directory-reporting-api-getting-started.md).

### <a name="engage-multi-factor-authentication-on-users"></a>Engager l’authentification multifacteur sur les utilisateurs

Sélectionnez un utilisateur dans un rapport.

Cliquez sur le bouton « Activer l’authentification Multifacteur » dans la partie inférieure de l’écran.

![Le bouton de l’authentification multifacteur en bas de l’écran](./media/active-directory-reporting-getting-started/mfaButton.png)

> [AZURE.TIP] Pour plus d’informations sur la création de rapports AD Azure, consultez [Afficher vos rapports access et l’utilisation](active-directory-view-access-usage-reports.md).




## <a name="learn-more"></a>Pour en savoir plus


### <a name="audit-events"></a>Événements d’audit

Obtenir des informations sur les événements à auditer dans l’annuaire dans [Azure Active Directory création de rapports d’Audit événements](active-directory-reporting-audit-events.md).

### <a name="api-integration"></a>Intégration de l’API

Voir la [documentation de référence API](https://msdn.microsoft.com/library/azure/mt126081.aspx)et [mise en route avec l’API de création de rapports](active-directory-reporting-api-getting-started.md) .

### <a name="get-in-touch"></a>Prendre contact

Messagerie [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com) pour les commentaires, l’aide ou des questions que vous deviez.

> [AZURE.TIP] Pour plus d’informations sur la création de rapports AD Azure, consultez [Afficher vos rapports access et l’utilisation](active-directory-view-access-usage-reports.md).
