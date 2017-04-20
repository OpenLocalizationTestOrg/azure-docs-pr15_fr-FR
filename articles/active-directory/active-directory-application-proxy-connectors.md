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


# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publier des applications sur des réseaux distincts et emplacements à l’aide de groupes de connecteur

> [AZURE.SELECTOR]
- [Portail Azure](active-directory-application-proxy-connectors-azure-portal.md)
- [Portail classique Azure](active-directory-application-proxy-connectors.md)


Groupes de connecteur sont utiles pour divers scénarios, notamment :

- Sites avec plusieurs centres de données interconnectés. Dans ce cas, vous souhaitez conserver autant le trafic au sein du centre de données que possible, car le centre de données entre des liens sont généralement coûteux et lente. Vous pouvez déployer des connecteurs dans chaque centre de données pour traiter uniquement les applications qui résident dans le centre de données. Cette approche réduit les liens entre-centre de données et fournit une expérience totalement transparente à vos utilisateurs.
- Gestion des applications installées sur les réseaux isolés qui ne font pas partie du réseau d’entreprise principal. Vous pouvez utiliser des groupes de connecteur installer connecteurs dédiés sur réseaux isolés isoler également des applications au réseau.
- Pour les applications installées sur IaaS pour l’accès au cloud, groupes de connecteur proposent des services courants pour sécuriser l’accès à toutes les applications. Groupes de connecteur ne créez dépendance supplémentaire sur votre réseau d’entreprise, ou fragment de l’expérience de l’application. Connecteurs peuvent être installés sur chaque centre de données cloud et servent uniquement les applications qui résident dans ce réseau. Vous pouvez installer plusieurs connecteurs pour atteindre une haute disponibilité.
- Prise en charge pour les environnements multi-forêts dans lequel les connecteurs spécifiques peuvent être déployés par forêt et définis pour répondre à des applications spécifiques.
- Connecteur groupes peuvent être utilisés dans les sites de sinistre soit détecter basculement ou en tant que sauvegarde pour le site principal.
- Connecteur groupes peuvent également être utilisés pour répondre à plusieurs sociétés à partir d’un client unique.

## <a name="prerequisite-create-your-connectors"></a>Au préalable : Créer votre connecteurs
Afin de regrouper vos connecteurs, vous devez Assurez-vous que vous avez [installé plusieurs connecteurs](active-directory-application-proxy-enable.md)et que vous nommez les et ensuite les regrouper. Enfin, vous devez les affecter à des applications spécifiques.

## <a name="step-1-create-connector-groups"></a>Étape 1 : Créer des groupes de connecteur
Vous pouvez créer des groupes de connecteur autant que vous le souhaitez. La création de groupes de connecteur s’effectue dans le portail classique Azure.

1. Sélectionnez votre répertoire et cliquez sur **configurer**.  
    ![Proxy de l’application, configurez capture d’écran, cliquez sur Gérer les groupes de connecteur](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)

2. Sous Proxy de l’Application, cliquez sur **Gérer les groupes de connecteur** et créer un nouveau groupe de connecteur en attribuant au groupe un nom.  
    ![Application proxy connecteur des groupes de capture d’écran - nom nouveau groupe](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## <a name="step-2-assign-connectors-to-your-groups"></a>Étape 2 : Affecter des connecteurs à vos groupes
Une fois que les groupes de connecteur sont créés, vous pouvez déplacer les connecteurs vers le groupe approprié.

1. Sous **Proxy de l’Application**, cliquez sur **Gérer les connecteurs**.
2. Sous **groupe**, sélectionnez le groupe souhaité pour chaque connecteur. Les connecteurs peut prendre jusqu'à 10 minutes actif dans le nouveau groupe.  
    ![Capture d’écran de connecteurs application proxy - sélectionner un groupe à partir du menu déroulant](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## <a name="step-3-assign-applications-to-your-connector-groups"></a>Étape 3 : Attribuer des applications à vos groupes de connecteur
La dernière étape consiste à définir chaque application au groupe de connecteur il servira.

1. Dans le portail classique Azure, dans votre annuaire, sélectionnez l’Application que vous voulez attribuer au groupe, cliquez sur **configurer**.
2. Sous **groupe de connecteur**, sélectionnez le groupe que vous souhaitez que l’application à utiliser. Cette modification est appliquée immédiatement.  
    ![Application proxy connecteur groupe capture d’écran - Sélectionner un groupe à partir du menu déroulant](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)


## <a name="see-also"></a>Voir aussi

- [Activer le Proxy d’Application](active-directory-application-proxy-enable.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
- [Résoudre les problèmes que vous rencontrez avec le Proxy d’Application](active-directory-application-proxy-troubleshoot.md)

Pour les dernières nouvelles et mises à jour, consultez le [blog de Proxy de l’Application](http://blogs.technet.com/b/applicationproxyblog/)
