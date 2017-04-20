<properties
    pageTitle="Configurer un nom de domaine personnalisé pour une application web dans le Service d’application Azure qui utilise le trafic Manager pour l’équilibrage de charge."
    description="Utiliser un nom de domaine personnalisé pour une une application web dans le Service d’application Azure qui inclut le trafic gestionnaire pour l’équilibrage de charge."
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Configuration d’un nom de domaine personnalisé pour une application web dans le Service d’application Azure à l’aide du trafic Manager

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article fournit des instructions d’ordre général pour l’utilisation d’un nom de domaine personnalisé avec Azure Application Service qui utilisent le Gestionnaire de trafic pour l’équilibrage de charge.

[AZURE.INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
## <a name="understanding-dns-records"></a>Présentation des enregistrements DNS

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## <a name="configure-your-web-apps-for-standard-mode"></a>Configurer vos applications web en mode standard

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## <a name="add-a-dns-record-for-your-custom-domain"></a>Ajouter un enregistrement DNS pour votre domaine personnalisé

> [AZURE.NOTE] Si vous avez acheté domaine via Azure Application Service Web Apps puis ignorez comme suit et faire référence à la dernière étape de l’article [Acheter un domaine pour les applications Web](custom-dns-web-site-buydomains-web-app.md) .

Pour associer votre domaine personnalisé à une application web dans le Service d’application Azure, vous devez ajouter une nouvelle entrée dans la table DNS pour votre domaine personnalisé à l’aide des outils fournis par le bureau d’enregistrement du domaine que vous avez acheté votre nom de domaine à partir de. Procédez comme suit pour rechercher et utiliser les outils DNS.

1. Se connecter à votre compte sur votre bureau d’enregistrement du domaine, puis recherchez une page pour la gestion des enregistrements DNS. Recherchez les liens ou les zones du site comme **Nom de domaine**, de **DNS**ou de **Gestion des noms de serveur**. Souvent un lien vers cette page vous pouvez trouver l’affichage de vos informations de compte, puis recherche d’un lien par exemple, **mon domaine**.

1. Une fois que vous avez trouvé la page management pour votre nom de domaine, recherchez un lien qui vous permet de modifier les enregistrements DNS. Cela peut être répertoriée en tant que **fichier de Zone**, **Les enregistrements DNS**, ou comme un lien de configuration **avancés** .

    * La page aura probablement quelques enregistrements déjà créés, par exemple une association d’entrée «**@**« ou »\*» avec une page « parking domaine ». Il peut également contenir des enregistrements pour sous-domaines courantes telles que **www**.
    * La page doit mentionner **enregistrements CNAME**ou fournir une liste déroulante pour sélectionner un type d’enregistrement. Il peut également mentionner d’autres enregistrements tels que les **enregistrements MX**et **enregistrements A** . Dans certains cas, les enregistrements CNAME seront appelées par d’autres noms comme un **Enregistrement d’Alias**.
    * La page aura également les champs qui vous permettent de **mappage** à partir d’un **nom d’hôte** ou le **nom de domaine** vers un autre nom de domaine.

1. Alors que les détails de chaque bureau d’enregistrement varient, en général vous mappez *à partir de* votre nom de domaine personnalisé (par exemple, **contoso.com**,) *pour* le trafic Gestionnaire nom de domaine (**contoso.trafficmanager.net**) qui est utilisé pour votre application web.

    > [AZURE.NOTE] Par ailleurs, si un enregistrement est déjà en cours d’utilisation et que vous avez besoin de manière préemptive le lier vos applications, vous pouvez créer un enregistrement CNAME supplémentaire. Par exemple, pour lier manière préemptive **www.contoso.com** à votre application web, créer un enregistrement CNAME à partir de **awverify.www** à **contoso.trafficmanager.net**. Vous pouvez ensuite ajouter « www.contoso.com » dans votre application Web sans modifier l’enregistrement CNAME « www ». Pour plus d’informations, voir [créer des enregistrements DNS pour une application web dans un domaine personnalisé][CREATEDNS].

1. Une fois que vous avez terminé d’ajouter ou modifier des enregistrements DNS auprès de votre bureau d’enregistrement, enregistrer les modifications.

<a name="enabledomain"></a>
## <a name="enable-traffic-manager"></a>Activer le Gestionnaire de trafic

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir le [Centre de développement Node.js](/develop/nodejs/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
