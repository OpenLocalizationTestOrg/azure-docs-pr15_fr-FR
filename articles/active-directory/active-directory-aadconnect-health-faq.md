<properties
    pageTitle="Azure AD Connect santé Forum aux questions"
    description="Ce forum aux questions répond aux questions concernant la santé Azure AD se connecter. Ce forum aux questions traite des questions sur l’utilisation du service, y compris le modèle de facturation, fonctionnalités, limitations et prise en charge."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>


# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Azure AD Connect santé Forum aux Questions (FAQ)

Ce forum aux questions répond aux questions concernant la santé Azure AD se connecter. Ce forum aux questions traite des questions sur l’utilisation du service, y compris le modèle de facturation, fonctionnalités, limitations et prise en charge.

## <a name="general-questions"></a>Questions d’ordre générales



**Q : puis-je gérer plusieurs annuaires Azure AD. Comment passer à l’avec Azure Active Directory Premium ?**

Vous pouvez basculer entre différentes clients Azure AD en sélectionnant actuellement signé dans nom d’utilisateur dans le coin supérieur droit, puis le compte approprié. Si le compte n’est pas répertorié ici, sélectionnez se déconnecter, puis utilisez les informations d’identification d’administrateur général du répertoire qui a Azure Active Directory Premium est activé pour vous connecter.

## <a name="installation-questions"></a>Questions sur l’installation



**Q : quelle est l’impact de l’installation de l’Agent d’intégrité connecter AD Azure sur des serveurs individuels ?**

L’impact de l’installation de la Microsoft Azure AD connecter santé Agents ADFS, les serveurs Proxy de l’Application Web, les serveurs Azure AD Connect (sycn), domaine est minime en ce qui concerne l’UC, le stockage et bande passante réseau de consommation de mémoire.

Les nombres ci-dessous sont une approximation.

- Utilisation de l’UC : ~ 1 % d’augmentation
- Utilisation de la mémoire : jusqu'à 10 % de la mémoire système totale

>[AZURE.NOTE] En cas de l’agent de l’incapacité de communiquer avec Azure, l’agent stocke les données localement, jusqu'à une limite maximale prédéfinie. L’agent remplace les données « mis en cache » sur une base « moins récemment pris en charge ».

- Espace de stockage mémoire tampon locale pour Agents d’Azure AD connecter intégrité : environ 20 Mo
- Pour les serveurs AD FS, il est recommandé de vous devez configurer un espace disque de 1 024 Mo (1 Go) pour le canal d’Audit AD FS pour Azure AD Agents d’intégrité se connecter à traiter toutes les données d’audit avant son remplacement.

**Q : que je dois redémarrer mon serveurs pendant l’installation des Azure AD connecter santé Agents ?**

Non. L’installation des agents s’applique uniquement à redémarrer le serveur. Toutefois, l’installation de certaines de ces étapes requises peut-être nécessiter un redémarrage du serveur.

Par exemple, sur Windows Server 2008 R2 l’installation de .net Framework 4.5 nécessite un redémarrage du serveur.


**Q : signifie Azure AD connecter Health Services fonctionnent via un proxy http SQL directe ?**

Oui.  Pour en cours opérations, vous pouvez configurer l’Agent d’intégrité pour transmettre les requêtes http sortant à l’aide d’un HTTP Proxy. Pour plus d’informations, voir [configurer Azure AD connecter Agents d’intégrité à utiliser le HTTP Proxy.](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)
Si vous avez besoin configurer un serveur proxy lors de l’inscription de l’Agent, vous devrez peut-être modifier vos paramètres de Proxy dans Internet Explorer au préalable.
1. Ouvrir Internet Explorer -> Paramètres -> Internet Options -> connexions -> Paramètres réseau.
2. Sélectionnez utiliser un serveur Proxy pour votre réseau local.
3. Cliquez sur Avancé si vous avez ports différents proxy HTTP et HTTPS/banque d’informations.

**Q : Azure AD connecter Health Services prend-il en charge l’authentification de base lors de la connexion aux serveurs proxy Http ?**

Non. Un mécanisme pour spécifier arbitraire de nom d’utilisateur et mot de passe pour l’authentification de base n’est pas actuellement pris en charge.


**Q : quelle est la version de AD DS sont prises en charge par la santé connecter Azure AD pour AD DS ?**

Surveillance des services AD DS est pris en charge pendant l’installation sur les systèmes d’exploitation suivants :

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

## <a name="operations-questions"></a>Questions d’opérations



**Q : dois-je activer l’audit sur mes serveurs de Proxy AD FS Application ou les serveurs Proxy de mon Application Web ?**

Non, l’audit n’a pas besoin être activé sur les serveurs Proxy d’Application Web (WAP). Activer sur les serveurs AD FS.


**Q : comment résolus Azure AD connecter les alertes de fonctionnement ?**

Azure AD connecter les alertes de fonctionnement obtenir résolus dans une condition de succès. Agents d’Azure AD connecter intégrité détecter et de signaler les conditions de réussite au service de façon régulière. Pour les alertes quelques, la suppression est temporelles. En d’autres termes, si la même condition d’erreur n’est pas observée pendant 72 heures à partir de génération d’alerte, l’alerte est résolue automatiquement.




**Q : quels ports de pare-feu dois-je ouvrir pour l’Azure AD connecter Agent d’intégrité pour l’utiliser ?**

Vous devez disposer de ports TCP/UDP 443 et 5671 ouvert pour l’Azure AD connecter Agent d’intégrité être en mesure de communiquer avec les points de terminaison du service d’intégrité Azure AD.


**Q : Pourquoi vois-je deux serveurs portant le même nom dans le portail d’intégrité de se connecter de Azure AD ?**

Lorsque vous supprimez un agent d’un serveur, le serveur n’est pas automatiquement supprimé à partir du portail de se connecter Azure AD.  Si vous manuellement supprimé un agent d’un serveur ou le serveur lui-même, vous devez supprimer manuellement l’entrée du serveur à partir du portail de la santé Azure AD se connecter. Pour plus d’informations, accédez à [Supprimer une instance du serveur ou le service.](active-directory-aadconnect-health-operations.md#delete-a-server-or-service-instance)

Si vous qu’un serveur ou créé un nouveau serveur avec les mêmes détails (par exemple, nom de l’ordinateur) et n’a pas supprimé le serveur déjà enregistré à partir du portail Azure AD connecter santé, d’installer l’agent sur le nouveau serveur, vous pouvez voir deux entrées portant le même nom.  
Dans ce cas, vous devez supprimer l’entrée appartenant à l’ancien serveur manuellement. Les données de ce serveur doivent être obsolètes.

## <a name="related-links"></a>Liens connexes

* [Azure AD Connect d’intégrité](active-directory-aadconnect-health.md)
* [Azure AD Connect Installation de l’Agent d’intégrité](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect opérations d’intégrité](active-directory-aadconnect-health-operations.md)
* [À l’aide de Azure AD connectez santé avec AD FS](active-directory-aadconnect-health-adfs.md)
* [L’utilisation d’état connecter Azure AD pour la synchronisation](active-directory-aadconnect-health-sync.md)
* [À l’aide de Azure AD connectez santé avec les services AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect historique des versions d’intégrité](active-directory-aadconnect-health-version-history.md)
