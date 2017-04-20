<properties
    pageTitle="Accéder à l’archivage sécurisé clé derrière le pare-feu | Microsoft Azure"
    description="Découvrez comment accéder à l’archivage sécurisé clé à partir d’une application derrière un pare-feu"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/13/2016"
    ms.author="ambapat"/>

# <a name="accessing-key-vault-behind-firewall"></a>Accéder à l’archivage sécurisé clé derrière le pare-feu
### <a name="q-my-key-vault-client-application-needs-to-be-behind-a-firewall-what-portshostsip-addresses-should-i-open-to-enable-access-to-key-vault"></a>Q : mon application cliente de l’archivage sécurisé clé doit être derrière un pare-feu, les adresses hôtes/ports/IP dois-je ouvrir pour activer l’accès à l’archivage sécurisé clé ?

Pour accéder à un archivage sécurisé clé votre application cliente de l’archivage sécurisé clé doit être en mesure d’accéder à plusieurs points de terminaison pour différentes fonctionnalités.

- Authentification (via Azure Active Directory)
- Gestion de clé de l’archivage sécurisé (qui inclut les stratégies d’accès paramètre créer/en lecture/mise à jour/supprimer et également) par le biais du Gestionnaire de ressources Azure
- Accéder à et gérer des objets (clés et secrets) stockées dans l’archivage sécurisé clé lui-même, parcourt le point de terminaison spécifique clés de l’archivage sécurisé (par exemple, [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)).  

Selon votre environnement et de configuration, il existe quelques différences.   

## <a name="ports"></a>Ports

Tout le trafic vers l’archivage sécurisé clé pour toutes les 3 fonctions (accès de plan d’authentification, de gestion et de données) dépasse HTTPS : Port 443. Toutefois de révocation de certificats, il sera parfois le trafic HTTP (port 80). Les clients qui prennent en charge OCSP ne doivent pas atteindre révocation de certificats, mais peuvent parfois atteindre [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).  

## <a name="authentication"></a>Authentification

Application cliente de l’archivage sécurisé clé doit accéder aux points de terminaison Azure Active Directory pour l’authentification. Point de terminaison utilisé dépend de la configuration du client DAS et le type d’identité--UPN, principal du service et le type de compte, par exemple, Account Microsoft ou ID d’organisation.  

| Type de principal | Point de terminaison : port |
|----------------|---------------|
| Utilisateur à l’aide de Microsoft Account<br> (par exemple :user@hotmail.com) | **Général :**<br> Login.microsoftonline.com:443<br><br> **Chine Azure :**<br> Login.chinacloudapi.CN:443<br><br>**Azure gouvernement des États-Unis :**<br> connexion us.microsoftonline.com:443<br><br>**Allemagne Azure :**<br> Login.microsoftonline.de:443<br><br> et <br>Login.Live.com:443   |
| Principal utilisateur/Service (par exemple, à l’aide de ID Org avec AADuser@contoso.com) | **Général :**<br> Login.microsoftonline.com:443<br><br> **Chine Azure :**<br> Login.chinacloudapi.CN:443<br><br>**Azure gouvernement des États-Unis :**<br> connexion us.microsoftonline.com:443<br><br>**Allemagne Azure :**<br> Login.microsoftonline.de:443 |
| Principal utilisateur/Service à l’aide d’organisation ID + ADFS ou autre point de terminaison fédéré (par exempleuser@contoso.com) | Tous les points de terminaison ci-dessus pour ID Org plus ADFS ou autres points de terminaison fédérés |

Il existe d’autres scénarios possibles complexes. Reportez-vous au [Flux Azure Active Directory authentification](/documentation/articles/active-directory-authentication-scenarios/), [Intégration des Applications avec Azure Active Directory](/documentation/articles/active-directory-integrating-applications/) et [Protocoles de d’authentification Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx) pour plus d’informations.  

## <a name="key-vault-management"></a>Gestion de l’archivage sécurisé clés

Pour la clé de gestion de l’archivage sécurisé (CRUD et la stratégie d’accès), l’application cliente de l’archivage sécurisé clé doit accéder point de terminaison Azure le Gestionnaire de ressources.  

| Type d’opération | Point de terminaison : port |
|----------------|---------------|
| Opérations de plan de contrôle de l’archivage sécurisé de clé<br> via le Gestionnaire de ressources Azure | **Général :**<br> Management.Azure.com:443<br><br> **Chine Azure :**<br> Management.chinacloudapi.CN:443<br><br> **Azure gouvernement des États-Unis :**<br> Management.usgovcloudapi.NET:443<br><br> **Allemagne Azure :**<br> Management.microsoftazure.de:443 |
| Azure Active Directory Graph API | **Général :**<br> Graph.Windows.NET:443<br><br> **Chine Azure :**<br> Graph.chinacloudapi.CN:443<br><br> **Azure gouvernement des États-Unis :**<br> Graph.Windows.NET:443<br><br> **Allemagne Azure :**<br> Graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Clés de billets

Pour toute la gestion d’objet (clés et secrets) l’archivage sécurisé clés et opérations de chiffrement, l’archivage sécurisé clés client doit accéder au point de fin de l’archivage sécurisé clés. Selon l’emplacement de votre l’archivage sécurisé clé, le suffixe DNS de point de terminaison est différent. Le point de terminaison de l’archivage sécurisé clé est au format : < nom de l’archivage sécurisé >. < région--suffixe dns spécifique-> comme décrit dans le tableau ci-dessous.  

| Type d’opération | Point de terminaison : port |
|----------------|---------------|
| Clé de billets comme opérations de chiffrement sur les clés, créé/en lecture/mise à jour/supprimer clés et secrets, jeu/get balises et les autres attributs sur les objets de l’archivage sécurisé clé (touches/secrets)     | **Général :**<br> &lt;nom de l’archivage sécurisé&gt;. vault.azure.net:443<br><br> **Chine Azure :**<br> &lt;nom de l’archivage sécurisé&gt;. vault.azure.cn:443<br><br> **Azure gouvernement des États-Unis :**<br> &lt;nom de l’archivage sécurisé&gt;. vault.usgovcloudapi.net:443<br><br> **Allemagne Azure :**<br> &lt;nom de l’archivage sécurisé&gt;. vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>Plages d’adresses IP

Service de l’archivage sécurisé clé à son tour utilise d’autres ressources Azure comme infrastructure PaaS, par conséquent, il n’est pas possible de fournir une plage spécifique d’adresses IP ayant des points de terminaison de service l’archivage sécurisé clé à un moment donné. Cependant si votre pare-feu prend en charge les adresses IP des plages, consultez le document de [Plages d’adresses IP Microsoft Azure centre de données](https://www.microsoft.com/download/details.aspx?id=41653) .   Pour l’authentification et d’identité (Azure Active Directory), votre application doit être en mesure de vous connecter aux points de terminaison décrites dans [l’authentification et les adresses de l’identité](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="next-steps"></a>Étapes suivantes

- Si vous avez des questions sur l’archivage sécurisé clé, visitez les [Forums de l’archivage sécurisé clé Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)
