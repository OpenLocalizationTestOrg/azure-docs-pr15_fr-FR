<properties
    pageTitle="FAQ sur la gestion des API Azure | Microsoft Azure"
    description="Découvrez les réponses aux questions les plus courantes, modèles et meilleures pratiques de gestion de l’API Azure."
    services="api-management"
    documentationCenter=""
    authors="miaojiang"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="mijiang"/>

# <a name="azure-api-management-faqs"></a>Forum aux questions de gestion des API Azure

Obtenez les réponses aux questions les plus courantes, des modèles et des pratiques recommandées pour la gestion des API Azure.

## <a name="frequently-asked-questions"></a>Forum aux questions

-   [Comment puis-je demander l’équipe de gestion de l’API Microsoft Azure en une question ?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)
-   [Ce que cela signifie lorsqu’une fonctionnalité est en mode Aperçu avant ?](#what-does-it-mean-when-a-feature-is-in-preview)
-   [Comment puis-je sécuriser la connexion entre la passerelle de gestion des API et Mes services principale ?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
-   [Comment copier des mon instance du service de gestion de l’API vers une nouvelle instance ?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
-   [Puis-je gérer mes instance de gestion de l’API par programme ?](#can-i-manage-my-api-management-instance-programmatically)
-   [Comment ajouter un utilisateur au groupe Administrateurs ?](#how-do-i-add-a-user-to-the-administrators-group)
-   [Pourquoi y-la stratégie que je souhaite ajouter non disponibles dans l’éditeur de stratégie ?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
-   [Comment utiliser le contrôle de version API gestion des API ?](#how-do-i-use-api-versioning-in-api-management)
-   [Comment configurer plusieurs environnements dans une seule API ?](#how-do-i-set-up-multiple-environments-in-a-single-api)
-   [Puis-je utiliser SOAP avec la gestion des API ?](#can-i-use-soap-with-api-management)
-   [Est la constante d’adresse IP de gestion des API passerelle ? Puis-je utiliser celle-ci dans les règles de pare-feu ?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
-   [Puis-je configurer un serveur d’autorisation OAuth 2.0 avec sécurité AD FS ?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
-   [Quelle méthode routage gestion des API utilise-t-il dans les déploiements à plusieurs emplacements géographiques ?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
-   [Puis-je utiliser un modèle de gestionnaire de ressources Azure pour créer une instance de service de gestion de l’API ?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
-   [Puis-je utiliser un certificat SSL auto-signé pour un serveur principal ?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
-   [Je reçois un échec d’authentification lorsque j’essaie de cloner un référentiel GIT ?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
-   [Gestion des API fonctionne-t-il avec Azure ExpressRoute ?](#does-api-management-work-with-azure-expressroute)
-   [Puis-je déplacer un service de gestion de l’API d’un abonnement à un autre ?](#can-i-move-an-api-management-service-from-one-subscription-to-another)


### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Comment puis-je demander l’équipe de gestion de l’API Microsoft Azure en une question ?

Vous pouvez nous contacter à l’aide d’une des options suivantes :

-   Publiez vos questions dans notre [forum MSDN de gestion de l’API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
-   Envoyer un message électronique à <apimgmt@microsoft.com>.
-   Nous envoyer une demande de fonctionnalité dans le [forum de commentaires Azure](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Ce que cela signifie lorsqu’une fonctionnalité est en mode Aperçu avant ?

Lorsqu’une fonctionnalité est en mode Aperçu avant, cela signifie que nous allons activement à la recherche commentaires sur le fonctionne de la fonctionnalité pour vous. Une fonctionnalité d’aperçu est terminée fonctionne, mais il est possible que nous allons modifier un saut en réponse aux commentaires des clients. Nous vous recommandons que vous ne dépendez pas une fonctionnalité qui se trouve dans un aperçu de votre environnement de production. Si vous avez des commentaires sur les fonctionnalités de visualisation, n’hésitez pas à une des options de la visite [Comment puis-je demander l’équipe de gestion de l’API Microsoft Azure une question ?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Comment puis-je sécuriser la connexion entre la passerelle de gestion des API et Mes services principale ?

Vous disposez de plusieurs options pour sécuriser la connexion entre la passerelle de gestion des API et vos services principale. Vous pouvez :

-   Utilisez l’authentification de base HTTP. Pour plus d’informations, voir [API de configurer les paramètres](api-management-howto-create-apis.md#configure-api-settings).
- Utilisez l’authentification commun SSL comme décrit dans [la sécurisation de services principale à l’aide d’authentification par certificat client Gestion des API Azure](api-management-howto-mutual-certificates.md).
- Utiliser la création de listes IP autorisées service principale. Si vous avez une instance de gestion des API de niveau Standard ou Premium, l’adresse IP de la passerelle reste constante. Vous pouvez définir votre liste d’autorisation pour permettre à cette adresse IP. Vous pouvez obtenir l’adresse IP de votre instance de gestion de l’API dans le tableau de bord dans le portail Azure.
- Se connecter à votre instance de gestion de l’API à un réseau virtuel Azure. Pour plus d’informations, voir [comment configurer les connexions VPN gestion des API Azure](api-management-howto-setup-vpn.md).

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Comment copier des mon instance du service de gestion de l’API vers une nouvelle instance ?

Vous disposez de plusieurs options si vous voulez copier une instance de gestion de l’API dans une nouvelle instance. Vous pouvez :

-   Utilisez la sauvegarde et restauration fonction Gestion des API. Pour plus d’informations, voir [comment implémenter sinistre à l’aide de sauvegarde et de restauration Gestion des API Azure](api-management-howto-disaster-recovery-backup-restore.md).
-   Créer votre propre sauvegarde et restaurer la fonctionnalité à l’aide de l' [API REST de gestion des API](https://msdn.microsoft.com/library/azure/dn776326.aspx). Utilisez l’API REST pour enregistrer et restaurer les entités à partir de l’instance du service que vous voulez.
-   Téléchargez la configuration du service à l’aide de Git et puis téléchargez-le sur une nouvelle instance. Pour plus d’informations, voir [comment enregistrer et configurer votre configuration du service de gestion de l’API à l’aide de Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Puis-je gérer mes instance de gestion de l’API par programme ?

Oui, vous pouvez gérer gestion des API par programme à l’aide de :

-   La [Gestion des API API REST](https://msdn.microsoft.com/library/azure/dn776326.aspx).
-   La [bibliothèque de gestion des ApiManagement Service Microsoft Azure SDK](http://aka.ms/apimsdk).
-   Les applets de commande PowerShell de [Gestion des services](https://msdn.microsoft.com/library/mt613507.aspx) et de [déploiement de Service](https://msdn.microsoft.com/library/mt619282.aspx) .

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Comment ajouter un utilisateur au groupe Administrateurs ?

Voici comment vous pouvez ajouter un utilisateur au groupe Administrateurs :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Accédez au groupe de ressources qui comporte l’instance de gestion de l’API que vous souhaitez mettre à jour.
3. Gestion des API, attribuer le rôle de **Collaborateur de gestion de l’Api** à l’utilisateur.

La collaboration nouvellement ajoutée pouvez désormais utiliser PowerShell Azure [applets de commande](https://msdn.microsoft.com/library/mt613507.aspx). Voici comment procéder pour vous connecter en tant qu’administrateur :

1. Utiliser la `Login-AzureRmAccount` applet de commande pour vous connecter.
2. Définir le contexte à l’abonnement ayant le service en utilisant `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`.
3. Obtenir une URL à l’aide de `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Utiliser l’URL pour accéder au portail d’administration.


### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Pourquoi y-la stratégie que je souhaite ajouter non disponibles dans l’éditeur de stratégie ?

Si la stratégie que vous souhaitez ajouter apparaît en grisé ou ombré dans l’éditeur de stratégie, n’oubliez pas que vous êtes dans l’étendue de la stratégie correcte. Chaque déclaration de stratégie est conçue pour pouvoir utiliser dans les sections de stratégie et des étendues spécifiques. Pour consulter les sections de stratégie et les étendues pour une stratégie, voir la section de l’utilisation de la stratégie de [stratégies de gestion des API](https://msdn.microsoft.com/library/azure/dn894080.aspx).


### <a name="how-do-i-use-api-versioning-in-api-management"></a>Comment utiliser le contrôle de version API gestion des API ?

Vous avez plusieurs options pour utiliser le contrôle de version API gestion des API :

-   Gestion des API, vous pouvez configurer API pour représenter différentes versions. Par exemple, vous devrez deux différentes API, MyAPIv1 et MyAPIv2. Un développeur peut choisir la version que le développeur souhaite utiliser.
-   Vous pouvez également configurer votre API avec une URL de service qui n’inclut pas un version du segment, par exemple, https://my.api. Ensuite, configurez un segment version sur un modèle de chaque opération [La réécriture d’URL](https://msdn.microsoft.com/library/azure/dn894083.aspx#RewriteURL) . Par exemple, vous pouvez avoir une opération avec un [modèle d’URL](api-management-howto-add-operations.md#url-template) appelé /resource et un modèle de [La réécriture d’URL](api-management-howto-add-operations.md#rewrite-url-template) appelé /v1/Resource. Vous pouvez modifier la valeur de segment version séparément pour chaque opération.
-   Si vous souhaitez conserver un version du segment « par défaut » dans l’URL du service de l’API, sous opérations sélectionnées, définissez une stratégie qui utilise le [jeu de service en aval](https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBackendService) stratégies pour modifier le chemin d’accès demande principale.

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Comment configurer plusieurs environnements dans une seule API ?

Pour configurer plusieurs environnements, par exemple, un environnement de test et un environnement de production dans une seule API, vous avez deux possibilités. Vous pouvez :

-   Hôte différentes API sur le même client.
-   Héberger les mêmes API sur différents clients.

### <a name="can-i-use-soap-with-api-management"></a>Puis-je utiliser SOAP avec la gestion des API ?

Prise en charge [directe SOAP](http://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) est désormais disponible. Les administrateurs peuvent importer le WSDL de leur service SOAP et gestion de l’API Azure créera un frontal SOAP. Documentation du portail développeur, console de test, stratégies et analytique est disponibles pour les services SOAP.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>Est la constante d’adresse IP de gestion des API passerelle ? Puis-je utiliser celle-ci dans les règles de pare-feu ?

Les niveaux Standard et Premium, l’adresse IP publique (VIP) du client de gestion de l’API inclut statique pour la durée de vie du client, à quelques exceptions près. L’adresse IP change dans ces conditions :

-   Le service est supprimé et puis recréé.
-   L’abonnement au service est suspendue (par exemple, pour nonpayment) et puis relancé.
-   Ajouter ou supprimer réseau virtuel Azure (vous pouvez utiliser réseau virtuel uniquement au niveau Premium).

Pour les déploiements de plusieurs régions, l’adresse régionaux change si la zone est libérée et puis relancée (vous pouvez utiliser plusieurs région déploiement uniquement au niveau Premium).

Les clients de niveau Premium qui sont configurés pour le déploiement de plusieurs région affectés à une adresse IP publique par région.

Vous pouvez obtenir votre adresse IP (ou les adresses dans un déploiement de plusieurs région) dans la page client dans le portail Azure.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Puis-je configurer un serveur d’autorisation OAuth 2.0 avec sécurité AD FS ?

Pour savoir comment configurer un serveur d’autorisation OAuth 2.0 avec la sécurité des Services de fédération Active Directory (AD FS), voir [ADFS à l’aide de gestion des API](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Quelle méthode routage gestion des API utilise-t-il dans les déploiements à plusieurs emplacements géographiques ?

Gestion des API utilise la [méthode de routage de performances du trafic](../traffic-manager/traffic-manager-routing-methods.md#performance-traffic-routing-method) dans les déploiements à plusieurs emplacements géographiques. Le trafic entrant est acheminé vers la passerelle API le plus proche. Si une zone géographique est déconnectée, le trafic entrant est automatiquement acheminé vers la passerelle suivante le plus proche. Plus d’informations sur les méthodes de routage dans le [Gestionnaire de trafic routage méthodes](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Puis-je utiliser un modèle de gestionnaire de ressources Azure pour créer une instance de service de gestion de l’API ?

Oui. Voir les modèles de démarrage rapide de [Service de gestion des API Azure](http://aka.ms/apimtemplate) .

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Puis-je utiliser un certificat SSL auto-signé pour un serveur principal ?

Oui. Voici comment procéder pour utiliser un certificat auto-signé calque SSL (Secure Sockets) pour un serveur principal :

1. Créer une entité [principale](https://msdn.microsoft.com/library/azure/dn935030.aspx) à l’aide de gestion de l’API.
2. Définissez la propriété **skipCertificateChainValidation** sur **true**.
3. Si vous ne voulez plus autoriser les certificats auto-signé, supprimez l’entité de serveur principal ou définissez la propriété **skipCertificateChainValidation** sur **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Je reçois un échec d’authentification lorsque j’essaie de cloner un référentiel Git ?

Si vous utilisez le Gestionnaire d’informations d’identification Git, ou si vous essayez de dupliquer un référentiel Git à l’aide de Visual Studio, vous pouvez rencontrer un problème connu avec la boîte de dialogue informations d’identification Windows. Longueur de mot de passe à 127 caractères les limites de la boîte de dialogue, et il tronque le mot de passe générés par Microsoft. Nous travaillons sur raccourcir le mot de passe. Pour l’instant, utilisez Git Bash cloner votre référentiel Git.

### <a name="does-api-management-work-with-azure-expressroute"></a>Gestion des API fonctionne-t-il avec Azure ExpressRoute ?

Oui. Gestion de l’API fonctionne avec Azure ExpressRoute.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Puis-je déplacer un service de gestion de l’API d’un abonnement à un autre ?

Oui. Pour plus d’informations, voir [déplacer des ressources d’un nouveau groupe de ressources ou d’abonnement](../resource-group-move-resources.md).
