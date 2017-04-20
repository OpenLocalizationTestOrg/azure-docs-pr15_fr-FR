<properties
    pageTitle="Azure AD Connect synchronisation : pratiques recommandées pour la modification de la configuration par défaut | Microsoft Azure"
    description="Fournit les meilleures pratiques pour la modification de la configuration par défaut de synchronisation Azure AD Connect."
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
    ms.date="08/22/2016"
    ms.author="markvi;andkjell"/>


# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Azure AD Connect synchronisation : pratiques recommandées pour la modification de la configuration par défaut
L’objectif de cette rubrique est pour décrire prises en charge et non prises en charge les modifications apportées à la synchronisation Azure AD Connect.

La configuration créée par Azure AD Connect fonctionne « en l’état est » la plupart des environnements qui se synchronisent Active Directory locales avec Azure AD. Toutefois, dans certains cas, il est nécessaire d’appliquer des modifications à une configuration pour répondre à un besoin spécifique ou la configuration minimale requise.

## <a name="changes-to-the-service-account"></a>Modifications apportées au compte de service
Azure AD Connect synchronisation s’exécute sous un compte de service créé par l’Assistant installation. Ce compte de service conserve les clés de chiffrement utilisé par la synchronisation de base de données. Il est créé avec un mot de passe long 127 caractères et le mot de passe est configuré pour ne pas expirer.

- Il est **non prises en charge** pour modifier ou réinitialiser le mot de passe du compte de service. Cela supprime les clés de chiffrement et le service n’est pas en mesure d’accéder à la base de données et n’est pas en mesure de démarrer.

## <a name="changes-to-the-scheduler"></a>Modifications apportées au planificateur
En commençant par les versions de version 1.1 (février 2016) vous pouvez configurer le [Planificateur](active-directory-aadconnectsync-feature-scheduler.md) d’avoir un cycle de synchronisation autre que celui par défaut 30 minutes.

## <a name="changes-to-synchronization-rules"></a>Modifications apportées aux règles de synchronisation
L’Assistant installation propose une configuration qui est censé pour utiliser pour les scénarios courants. Au cas où vous devez apporter des modifications à la configuration, vous devez suivre ces règles pour vous avez une configuration prise en charge.

- Vous pouvez [Modifier les flux d’attributs](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes) si les flux direct des attributs par défaut ne conviennent pas pour votre organisation.
- Si vous voulez [flux pas un attribut](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute) et supprimez toutes les valeurs attribut existant dans Azure Active Directory, vous devez créer une règle pour ce scénario.
- [Désactiver une règle de synchronisation indésirables](#disable-an-unwanted-sync-rule) plutôt que de le supprimer. Une règle supprimée est recréée pendant une mise à niveau.
- Pour [Modifier une règle de prédéfinies](#change-an-out-of-box-rule), vous devez faire une copie de la règle d’origine et désactiver la règle prête à l’emploi. L’éditeur de règle de synchronisation invite et vous aide à vous.
- Exporter vos règles de synchronisation personnalisés à l’aide de l’éditeur de règles de synchronisation. L’éditeur vous propose un script PowerShell que vous pouvez utiliser pour facilement recréez-les dans un scénario de récupération d’urgence.

>[AZURE.WARNING] Les règles prédéfinies de synchronisation ont une empreinte numérique. Si vous apportez une modification à ces règles, l’empreinte numérique fait correspondre n’est plus. Vous pouvez avoir des problèmes à l’avenir lorsque vous essayez d’appliquer une nouvelle version de Azure AD Connect. N’apporter des modifications comme il est décrit dans cet article.

### <a name="disable-an-unwanted-sync-rule"></a>Désactiver une règle de synchronisation indésirables
Ne pas supprimer une règle prédéfinies de synchronisation. Il est recréé au cours de la prochaine mise à niveau.

Dans certains cas, l’Assistant installation a produit une configuration qui ne fonctionne pas pour votre topologie. Par exemple, si vous disposez d’une topologie de forêt compte ressource, mais vous avez étendu le schéma de la forêt compte avec le schéma Exchange, des règles pour Exchange sont créés pour la forêt compte et la ressource. Dans ce cas, vous devez désactiver la règle de synchronisation pour Exchange.

![Règle de synchronisation désactivé](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Dans l’image ci-dessus, l’Assistant installation a détecté un ancien schéma Exchange 2003 dans la forêt du compte. Cette extension de schéma a été ajoutée avant la forêt ressource a été introduite dans l’environnement de Fabrikam. Pour assurer la que synchronisation sans attributs à partir de l’ancienne implémentation Exchange, la règle de synchronisation doit être désactivée comme indiqué.

### <a name="change-an-out-of-box-rule"></a>Modifier une règle de prédéfinies
Si vous devez apporter des modifications à une règle de prédéfinies, vous devez faire une copie de la règle de prédéfinies et désactiver la règle d’origine. Apportez les modifications à la règle clonée. L’éditeur de règle de synchronisation est avec ces étapes pour vous aider. Lorsque vous ouvrez une règle de prédéfinies, cette boîte de dialogue s’affiche :  
![Avertissement de la règle de boîte de dialogue](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Sélectionnez **Oui** pour créer une copie de la règle. La règle clonée est ensuite ouvert.  
![Règle cloné](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

Cette règle cloné, apportez les modifications nécessaires à l’étendue, rejoindre et transformations.

## <a name="next-steps"></a>Étapes suivantes

**Rubriques de présentation**

- [Azure AD Connect synchronisation : comprendre et personnaliser la synchronisation](active-directory-aadconnectsync-whatis.md)
- [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
