<properties
    pageTitle="Mise à niveau à partir de synchronisation d’annuaire et la synchronisation Active Directory Azure | Microsoft Azure"
    description="Décrit comment mettre à niveau à partir de la synchronisation d’annuaire et Azure AD Sync à Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="billmath"/>


# <a name="upgrade-windows-azure-active-directory-sync-dirsync-and-azure-active-directory-sync-azure-ad-sync"></a>Mise à niveau de Windows Azure Active Directory Sync (« DirSync ») et Azure Active Directory Sync (« Azure AD Sync »)
Azure AD Connect est la meilleure façon de connecter votre annuaire locales avec Azure AD et Office 365. Il s’agit d’un moment idéal pour mettre à niveau vers Azure AD Connect à partir de Windows Azure Active Directory Sync (DirSync) ou Azure AD Sync que ces outils sont maintenant déconseillées et fin du support technique sur 13 avril 2017.

Les outils de la synchronisation de deux identité sont déconseillées ont été proposés pour les clients forêt unique (DirSync) et pour forêt multiples et d’autres avancées clients (Azure AD Sync). Ces outils plus anciens ont été remplacés par une solution qui n’est disponible pour tous les scénarios : Azure AD Connect. Il propose des nouvelles fonctionnalités et améliorations de la fonctionnalité prise en charge de nouveaux scénarios. Pour pouvoir continuer à synchroniser vos données locales identité AD Azure et Office 365, nous vous recommandons vivement de mettre à niveau vers Azure AD Connect.

La dernière version de synchronisation d’annuaire a été publiée en juillet 2014 et la dernière version de la synchronisation Azure Active Directory a été publiée en mai 2015.

## <a name="what-is-azure-ad-connect"></a>Qu’est Azure AD Connect
Azure AD Connect est le successeur de synchronisation d’annuaire et Azure AD Sync. Il combine tous les scénarios ces deux pris en charge. Vous pouvez en savoir plus sur l’intégration de [vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

## <a name="deprecation-schedule"></a>Planification d’amortissement

Date | Commentaire
 --- | ---
13 avril 2016 | Windows Azure Active Directory Sync (« DirSync ») et Microsoft Azure Active Directory Sync (« Azure AD Sync ») sont annoncé comme déconseillée.
13 avril 2017 | Prise en charge terminée. Clients sera n’est plus en mesure d’ouvrir une demande de support sans mise à niveau vers Azure AD Connect tout d’abord.

## <a name="how-to-transition-to-azure-ad-connect"></a>Comment faire pour la transition vers Azure AD Connect
Si vous exécutez DirSync il existe deux façons, vous pouvez mettre à niveau : mise à niveau ou en parallèle déploiement sur place. Une mise à niveau en place est recommandée pour la plupart des clients et si vous avez un récent système d’exploitation et inférieur à 50 000 objets. Dans les autres cas, il est recommandé pour effectuer un déploiement en parallèle à l’endroit où votre configuration DirSync est déplacée vers un nouveau serveur exécutant Azure AD Connect.

Si vous utilisez une mise à niveau en place est recommandé de la synchronisation Azure Active Directory. Si vous le souhaitez, il est possible d’installer un nouveau serveur Azure AD Connect en parallèle et effectuer une migration de basculement à partir de votre serveur Azure AD Sync à Azure AD Connect.

Solution | Scénario
----- | -----
[Mise à niveau à partir de la synchronisation d’annuaire](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) | <li>Si vous disposez d’un serveur de synchronisation d’annuaire existant déjà en cours d’exécution.</li>
[Mise à niveau à partir de la synchronisation Active Directory Azure](active-directory-aadconnect-upgrade-previous-version.md)| <li>Si vous migrez à partir de la synchronisation Azure Active Directory.</li>

Si vous voulez voir comment effectuer une mise à niveau à partir de synchronisation d’annuaire vers Azure AD Connect, consultez cette vidéo Channel 9 :

> [AZURE.VIDEO azure-active-directory-connect-in-place-upgrade-from-legacy-tools]

## <a name="faq"></a>FAQ
**Q : j’ai reçu une notification par courrier électronique de l’équipe Azure et/ou un message à partir du centre de messages Office 365, mais je suis à l’aide de se connecter.**  
La notification a été envoyée également aux clients qui utilisent Azure AD Connect avec un numéro de version 1.0. \*.0 (à l’aide d’une version antérieure 1.1). Microsoft recommande clients afin de rester à jour avec les versions Azure AD Connect. Avec 1.1 que la fonctionnalité de [mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md) rend très facile ont toujours une version récente d’Azure AD Connect installé.

**Q : sera DirSync/Azure AD Sync dysfonctionnement sur 13 avril 2017 ?**  
Non. La date de lorsqu’ils ne seront plus en mesure de communiquer avec Azure AD est annoncée à une date ultérieure. Vous ne pourrez pas trouver ces informations dans cette rubrique lorsqu’elles sont disponibles.

**Q : quelles versions DirSync puis-je mettre à niveau ?**  
Il est pris en charge pour mettre à niveau à partir de n’importe quelle version de synchronisation d’annuaire en cours d’utilisation.

**Q : qu’en est-il du connecteur AD Azure pour FIM/MIM ?**  
Connecteur Azure AD pour FIM/MIM n’a **pas** été annoncé que déconseillée. Il est en **fonctionnalité figer**; Aucune nouvelle fonctionnalité n’est ajoutée et qu’il ne reçoit aucune correctifs. Microsoft recommande les clients qui utilisent au plan atteindre à partir de celui-ci Azure AD Connect. Il est fortement recommandé ne pas démarrer les nouveaux déploiements à l’utiliser. Ce connecteur est annoncé déconseillée à l’avenir.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
