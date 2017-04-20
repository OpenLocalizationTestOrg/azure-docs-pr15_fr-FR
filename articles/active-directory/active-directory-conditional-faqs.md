<properties
    pageTitle="Accès à conditionnelle Azure Active Directory FAQ | Microsoft Azure"
    description="Questions fréquemment posées sur accès conditionnel "
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-conditional-access-faq"></a>Accès à conditionnelle Azure Active Directory Forum aux questions

## <a name="which-applications-work-with-conditional-access-policies"></a>Quelles applications fonctionnent avec les stratégies d’accès conditionnelle ?

**A:** Consultez la rubrique, [les applications access quoi conditionnelle sont prises en charge](active-directory-conditional-access-supported-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Les stratégies d’accès conditionnelle sont appliquées pour la collaboration B2B et les utilisateurs invités ?

**A:** Les stratégies sont appliquées pour les utilisateurs de collaboration B2B. Toutefois, dans certains cas, un utilisateur peut ne pas être en mesure de respecter la stratégie si, par exemple, une organisation ne prend pas en charge l’authentification multifacteur. 

La stratégie est appliquée actuellement pas pour les utilisateurs invités SharePoint. La relation invité est conservée dans SharePoint. Les utilisateurs invités comptes ne sont pas soumis à access stratégies au niveau du serveur d’authentification. Accès invité peuvent être géré à SharePoint.

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Une stratégie de SharePoint Online ne s’applique également à SkyDrive Pro ?

**A:** Oui.
 
## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Pourquoi ne puis-je pas défini une stratégie dans les applications clientes, telle que Word ou Outlook ?

**A:** Une stratégie d’accès conditionnelle définit la configuration requise pour l’accès à un service et est appliquée lorsque l’authentification se produit à ce service. La stratégie n’est pas définie directement sur une application cliente ; au lieu de cela, il est appliqué lors de l’appel dans un service. Par exemple, une stratégie définie sur SharePoint s’applique aux clients appelant SharePoint et une stratégie définie sur Exchange s’applique à Outlook.


## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Une stratégie d’accès conditionnelle ne s’applique aux comptes de service ?

**A:** Stratégies d’accès conditionnel s’appliquent à tous les comptes d’utilisateur. Cela inclut les comptes d’utilisateurs utilisés comme comptes de service. Dans de nombreux cas, un compte de service qui s’exécute sans assistance n’est pas en mesure de satisfaire une stratégie. Il s’agit, par exemple le cas, lorsque l’authentification Multifacteur est requis. Dans ce cas, les comptes de services peuvent être exclus à partir d’une stratégie, à l’aide de paramètres de gestion des accès conditionnel stratégie. En savoir plus sur l’application d’une stratégie aux utilisateurs ici.
