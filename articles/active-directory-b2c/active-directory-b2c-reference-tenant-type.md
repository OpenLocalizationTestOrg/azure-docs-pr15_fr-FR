<properties
    pageTitle="Azure B2C annuaire Active : Utilisateurs de Production à l’échelle par rapport à Aperçu B2C | Microsoft Azure"
    description="Une rubrique sur les types de clients Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-production-scale-vs-preview-b2c-tenants"></a>Azure B2C annuaire Active : Utilisateurs de Production à l’échelle par rapport à Aperçu B2C

Si vous prévoyez d’écrire une application de production B2C Azure Active Directory (AD Azure), vous devez être certain que vous avez le bon client « type » soient publiées sur. Pour voir ce qui est installée, procédez comme suit pour [accéder à la carte de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure, consultez sous **type de client**.

## <a name="summary"></a>Résumé

Azure AD B2C prend en charge les applications de production uniquement sur **échelle de Production** B2C utilisateurs en Amérique du Nord.

| Type de client | Pays et régions | En règle générale-disponible ? |
| ----------- | -------------- | --------------------- |
| **Client échelle de production** | Pays et régions Amérique du Nord | Oui |
| **Client échelle de production** | Tous les pays et régions, à l’exception d’Amérique du Nord | N° |
| **Client Preview** | Tous les pays/régions | N° |

> [AZURE.NOTE]
Azure AD B2C clients (pour les consommateurs) sont actuellement indisponibles dans plusieurs pays ou régions dans lequel les clients Azure AD (pour les employés) sont disponibles. Lisez les sections suivantes pour plus d’informations.

## <a name="production-scale-b2c-tenant-in-north-america"></a>Client B2C de production à l’échelle en Amérique du Nord

Si vous avez [créé votre client B2C](active-directory-b2c-get-started.md) en Amérique du Nord, par exemple, dans un des pays ou régions suivantes : United États, Canada, Costa Rica, République dominicaine, le Salvador, Guatemala, Mexique, Panama, Porto Rico et Trinidad et Tobago et le **type de client** de votre interface utilisateur d’administration B2C indique **à l’échelle de Production**, votre client peut être utilisée pour les applications de production.

> [AZURE.NOTE]
Clients de production échelle sont en mesure de mise à l’échelle à 100 s de millions d’identités par client.

![Capture d’écran d’un client de production à l’échelle](./media/active-directory-b2c-reference-tenant-type/production-scale-b2c-tenant.png)

## <a name="preview-b2c-tenant-in-any-countryregion"></a>Afficher un aperçu de client B2C dans n’importe quel pays/région

Si vous avez créé un client B2C pendant la période d’aperçu de B2C Azure AD, il est probable que votre **client tapez** indique **Afficher un aperçu du client**. Si c’est le cas, vous devez utiliser votre client uniquement pour le développement et à des fins de tests et non pour les applications de production.

> [AZURE.IMPORTANT]
Il n’existe aucun chemin d’accès de migration à partir d’un aperçu B2C client sur un client B2C échelle de production. Notez qu’il existe des connus problèmes lorsque vous supprimez un client preview B2C puis recréez un client B2C production échelle portant le même nom de domaine. Vous devez créer un client B2C production échelle avec un nom de domaine différent.

![Capture d’écran d’un client (version préliminaire)](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)

## <a name="production-scale-b2c-tenant-outside-of-north-america"></a>Client B2C de production à l’échelle en dehors de l’Amérique du Nord

Azure AD B2C n’est actuellement pas généralement disponibles en dehors de l’Amérique du Nord. Toutefois vous pouvez créer et utiliser des clients d’échelle de production, de développement et de test à des fins, dans un des pays ou régions suivantes : Algérie, Autriche, Azerbaïdjan, Bahreïn, Belarus, Belgique, Bulgarie, Croatie, Chypre, République tchèque, Danemark, Égypte, Estonie, Finlande, France, Allemagne, Grèce, Hongrie, Islande, Irlande, Israël, Italie, Jordanie, Kazakhstan, Kenya, Koweït, Liban, la Lettonie, Liechtenstein, Lituania, Luxembourg, ex-république yougoslave de Macédoine, Malte, Monténégro, Maroc, pays-bas, Nigeria, Norvège , Oman, Pakistan, Pologne, Portugal, Qatar, Roumanie, Russie, Arabie saoudite, Serbie, Slovaquie, Slovénie, Afrique du Sud, Espagne, Suède, Suisse, Tunisie, Turquie, Ukraine, United Émirats Arabes Unis et anglais (Royaume-Uni).

Une fois que Azure AD B2C annonce la disponibilité générale dans au-dessus de pays ou régions, vous pouvez continuer à utiliser ces clients échelle de production et de mise en service avec vos applications de production sans perte de données.

## <a name="availability-of-b2c-tenants"></a>Disponibilité des clients B2C

Clients B2C sont actuellement indisponibles dans les pays suivants ou régions : Afghanistan, Argentine, Australie, Brésil, Chili, Colombie, Équateur, Hong Kong SAR, Inde, Indonésie, Irak, au Japon, Corée, Malaisie, Nouvelle-Zélande, Paraguay, Pérou, Philippines, Singapour, Sénégal, Taïwan, Thailand (ไทย), Uruguay et Venezuela. Nous prévoyons pour les inclure dans le futur.
