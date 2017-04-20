<properties
    pageTitle="Utilisation des connecteurs Proxy d’Application Azure AD | Microsoft Azure"
    description="Explique comment créer et gérer des groupes de connecteurs dans Azure AD Application Proxy."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/09/2016"
    ms.author="kgremban"/>


# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups---public-preview"></a>Publier des applications sur des réseaux distincts et emplacements à l’aide de groupes de connecteur - version d’évaluation

> [AZURE.SELECTOR]
- [Portail Azure](active-directory-application-proxy-connectors-azure-portal.md)
- [Portail classique Azure](active-directory-application-proxy-connectors.md)


Groupes de connecteur sont utiles pour divers scénarios, notamment :

- Sites avec plusieurs centres de données interconnectés. Dans ce cas, vous souhaitez conserver autant le trafic au sein du centre de données que possible des liens entre-centre de données étant coûteux et lente. Vous pouvez déployer des connecteurs dans chaque centre de données pour traiter uniquement les applications qui résident dans le centre de données. Cette approche réduit les liens entre-centre de données et fournit une expérience totalement transparente à vos utilisateurs.
- Gestion des applications installées sur les réseaux isolés qui ne font pas partie du réseau d’entreprise principal. Vous pouvez utiliser des groupes de connecteur installer connecteurs dédiés sur réseaux isolés isoler également des applications au réseau.
- Pour les applications installées sur IaaS pour l’accès au cloud, groupes de connecteur proposent des services courants pour sécuriser l’accès à toutes les applications. Groupes de connecteur ne créez dépendance supplémentaire sur votre réseau d’entreprise, ou fragment de l’expérience de l’application. Connecteurs peuvent être installés sur chaque centre de données cloud et servent uniquement les applications qui résident dans ce réseau. Vous pouvez installer plusieurs connecteurs pour atteindre une haute disponibilité.
- Prise en charge pour les environnements multi-forêts dans lequel les connecteurs spécifiques peuvent être déployés par forêt et définis pour répondre à des applications spécifiques.
- Connecteur groupes peuvent être utilisés dans les sites de sinistre soit détecter basculement ou en tant que sauvegarde pour le site principal.
- Connecteur groupes peuvent également être utilisés pour répondre à plusieurs sociétés à partir d’un client unique.

## <a name="prerequisite-create-your-connectors"></a>Au préalable : Créer votre connecteurs
Pour regrouper vos connecteurs, vous devez vous assurer que vous [plusieurs connecteurs installés](active-directory-application-proxy-enable.md). Lorsque vous installez un connecteur, il joint automatiquement le groupe de connecteur **par défaut** .

## <a name="step-1-create-connector-groups"></a>Étape 1 : Créer des groupes de connecteur
Vous pouvez créer des groupes de connecteur autant que vous le souhaitez. La création de groupes de connecteur s’effectue dans le [portail Azure](https://portal.azure.com).

1. Sélectionnez **Azure Active Directory** pour accéder au tableau de bord de gestion de votre annuaire. À partir de là, sélectionnez **applications d’entreprise** > **proxy de l’Application**.

2. Sélectionnez le bouton **Connector groupes** . La carte de nouveau groupe de connecteur s’affiche.

3. Donnez un nom à votre nouveau groupe de connecteur, puis utilisez le menu déroulant pour sélectionner les connecteurs appartiennent à ce groupe.

4. Une fois votre groupe de connecteur terminée, sélectionnez **Enregistrer** .

## <a name="step-2-assign-applications-to-your-connector-groups"></a>Étape 2 : Affecter des applications dans vos groupes de connecteur
La dernière étape consiste à définir chaque application au groupe de connecteur il servira.

1. Tableau de bord de gestion de votre annuaire, sélectionnez **applications d’entreprise** > **toutes les applications** > l’application que vous voulez affecter à un groupe de connecteur > **Proxy de l’Application**.
2. Sous le **groupe lien**, utilisez le menu déroulant pour sélectionner le groupe que vous souhaitez que l’application à utiliser.
3. Sélectionnez **Enregistrer** pour appliquer les modifications.


## <a name="see-also"></a>Voir aussi

- [Activer le Proxy d’Application](active-directory-application-proxy-enable.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
- [Résoudre les problèmes que vous rencontrez avec le Proxy d’Application](active-directory-application-proxy-troubleshoot.md)

Pour les dernières nouvelles et mises à jour, consultez le [blog de Proxy de l’Application](http://blogs.technet.com/b/applicationproxyblog/)
