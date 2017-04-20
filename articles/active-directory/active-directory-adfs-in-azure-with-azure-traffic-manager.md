<properties
    pageTitle="Déploiement de FS disponibilité AD croisée géographiques dans Azure avec le Gestionnaire de trafic Azure | Microsoft Azure"
    description="Dans ce document, vous allez apprendre à déployer ADFS dans Azure pour haute disponibilité."
    keywords="AD fs avec le trafic Azure manager, adfs avec le Gestionnaire de trafic Azure, géographiques, centre de données à plusieurs, centres de données géographiques, à plusieurs centres de données géographiques, déployer ADFS dans azure, déployer adfs azure, adfs azure, azure ad fs, déployer adfs, déployer ADFS, adfs dans azure, déployer adfs dans azure, déployer ADFS dans azure azure AD FS, introduction à AD FS, Azure, AD FS dans Azure, iaas , ADFS, atteindre adfs azure"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="anandy;billmath"/>
    
#<a name="high-availability-cross-geographic-ad-fs-deployment-in-azure-with-azure-traffic-manager"></a>Déploiement de FS disponibilité AD croisée géographiques dans Azure avec le Gestionnaire de trafic Azure

[Déploiement AD FS dans Azure](active-directory-aadconnect-azure-adfs.md) fournit des indications détaillées en comment vous pouvez déployer une infrastructure AD FS simple pour votre organisation dans Azure. Cet article fournit les étapes suivantes pour créer un déploiement croisée géographiques de AD FS dans Azure à l’aide du [Gestionnaire de trafic Azure](../traffic-manager/traffic-manager-overview.md). Gestionnaire de trafic Azure permet de créer un géographiquement double haute disponibilité et l’infrastructure de AD FS High performance pour votre organisation à l’aide d’utilisation de la plage des méthodes de routage disponibles en fonction des besoins différents à partir de l’infrastructure.

Une infrastructure AD FS croisée géographiques hautement disponible permet :

* **Suppression de point de défaillance :** Fonctions de basculement du Gestionnaire de trafic Azure, vous pouvez obtenir une infrastructure AD FS hautement disponible même si un des centres de données dans une partie du globe s’arrête
* **Performances accrues :** Vous pouvez utiliser le déploiement suggéré dans cet article pour fournir une infrastructure AD FS High performance qui permet aux utilisateurs de s’authentifier plus rapidement. 

##<a name="design-principles"></a>Principes de conception

![Conception globale](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/blockdiagram.png)

Principes de conception sera mêmes comme indiqué dans les principes de conception dans l’article déploiement AD FS dans Azure. Le schéma ci-dessus indique une extension simple de déploiement de base dans une autre région géographique. Voici quelques points à prendre en considération lorsque vous étendez votre déploiement à nouveau votre région géographique

* **Virtuelle réseau :** Vous devez créer un nouveau réseau virtuel dans la région géographique que vous voulez déployer une infrastructure AD FS supplémentaire. Dans le schéma ci-dessus, vous verrez Geo1 VNET et Geo2 VNET en tant que les deux réseaux virtuels dans chaque région géographique.

* **Domaine et les serveurs AD FS de nouveau VNET géographique :** Il est recommandé de déployer domaine contrôleurs dans la nouvelle zone géographique afin que les serveurs AD FS dans la nouvelle zone n’ont pas à contacter un contrôleur de domaine dans un autre éloigné réseau pour achever une authentification et ce qui améliore les performances.

* **Comptes de stockage :** Comptes de stockage sont associés à une région. Dans la mesure où vous déployez machines dans une nouvelle région géographique, vous devrez créer de nouveaux comptes de stockage à utiliser dans la région.  

* **Réseau de groupes de sécurité :** Comme comptes de stockage, les groupes de sécurité réseau créés dans une zone ne peut pas être utilisés dans une autre région géographique. Par conséquent, vous devrez créer nouveau réseau groupes de sécurité semblables à ceux de la première région géographique pour sous-réseau ent et DMZ dans la nouvelle zone géographique.

* **Étiquettes DNS pour les adresses IP publiques :** Gestionnaire de trafic Azure peut faire référence aux points de terminaison uniquement via étiquettes DNS. Par conséquent, vous êtes obligé de créer des étiquettes DNS pour les adresses IP publiques de l’équilibrage de charge externe.

* **Gestionnaire de trafic azure :** Microsoft Azure le trafic Manager vous permet de contrôler la distribution du trafic utilisateur vers vos points de terminaison du service en cours d’exécution dans différents centres de données dans le monde entier. Azure chef de trafic au niveau du DNS. Il utilise les réponses DNS pour diriger le trafic de l’utilisateur final aux points de terminaison globalement distribué. Clients puis se connecter à ces points de terminaison directement. Différentes options de routage de performances, Weighted et priorité, vous pouvez facilement choisir l’option routage adaptée aux besoins de votre organisation. 

* **V-net net V connectivité entre deux régions :** Vous n’avez pas besoin de disposer d’une connexion entre les réseaux virtuels lui-même. Dans la mesure où chaque réseau virtuel a accès au domaine et serveur AD FS et WAP dans lui-même, ils peuvent travailler sans les connexions entre les réseaux virtuels dans différentes régions. 

##<a name="steps-to-integrate-azure-traffic-manager"></a>Étapes à suivre pour intégrer au Gestionnaire de trafic Azure

###<a name="deploy-ad-fs-in-the-new-geographical-region"></a>Déployer ADFS dans la nouvelle zone géographique
Suivez les étapes et les instructions de [déploiement AD FS dans Azure](active-directory-aadconnect-azure-adfs.md) pour déployer la même topologie dans la nouvelle zone géographique.

###<a name="dns-labels-for-public-ip-addresses-of-the-internet-facing-public-load-balancers"></a>Étiquettes DNS pour les adresses IP publiques de l’équilibrage de charge Internet Facing (public)
Comme indiqué ci-dessus, le Gestionnaire de trafic Azure peuvent faire référence uniquement aux étiquettes DNS comme points de terminaison et par conséquent, il est important de créer des étiquettes DNS pour les adresses IP publiques de l’équilibrage de charge externe. En dessous de capture d’écran montre comment vous pouvez configurer votre nom DNS pour l’adresse IP. 

![Étiquette DNS](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfabstsdnslabel.png)

###<a name="deploying-azure-traffic-manager"></a>Déploiement d’Azure le trafic Manager

Suivez les étapes ci-dessous pour créer un profil de manager le trafic. Pour plus d’informations, vous pouvez également faire référence à [gérer un profil Azure le trafic Gestionnaire](../traffic-manager/traffic-manager-manage-profiles.md).

1. **Créer un profil le trafic Manager :** Donnez un nom unique à votre profil manager le trafic. Ce nom du profil fait partie du nom DNS et se comporte comme un préfixe pour l’étiquette de nom de domaine gestionnaire le trafic. Le nom / préfixe est ajouté à. trafficmanager.net pour créer une étiquette DNS pour le trafic de votre responsable. La capture d’écran ci-dessous montre le Gestionnaire de trafic préfixe DNS qui est défini comme mysts et étiquette DNS résultante sera mysts.trafficmanager.net. 

    ![Création de trafic Gestionnaire de profil](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/trafficmanager01.png)
 
2. **La méthode de routage de trafic :** Il existe trois options de routage disponibles dans le Gestionnaire de trafic :

    * Priorité 
    * Performances
    * Pondérée
    
    **Performances** est l’option recommandée pour atteindre hautement injoignable infrastructure AD FS. Toutefois, vous pouvez choisir n’importe quelle méthode routage plus adapté à vos besoins de déploiement. La fonctionnalité AD FS n’est pas affectée par l’option routage sélectionnée. Pour plus d’informations, voir [méthodes de routage de trafic gestionnaire le trafic](../traffic-manager/traffic-manager-routing-methods.md) . Dans l’exemple de capture d’écran ci-dessus, vous pouvez voir la méthode de **performances** sélectionnée.
   
3.  **Configurer des points de terminaison :** Dans la page Gestionnaire de trafic, cliquez sur les points de terminaison et sélectionnez Ajouter. Cette action ouvre une page de point de terminaison ajouter semblable à la capture d’écran ci-dessous
 
    ![Configurer les points de terminaison](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfsendpoint.png)
 
    Pour les entrées différentes, suivre les instructions ci-dessous :

    **Type :** Sélectionnez point de terminaison Azure comme nous sera pointant vers une adresse IP publique Azure.

    **Nom :** Créer un nom que vous souhaitez associer le point de terminaison. Ce n’est pas le nom DNS et n’a pas d’incidence sur les enregistrements DNS.

    **Cibler le type de ressource :** Sélectionnez adresse IP publique en tant que la valeur à cette propriété. 

    **Cibler ressource :** Cela vous donne une option pour choisir les différentes étiquettes DNS que vous sont disponibles sous votre abonnement. Choisir l’étiquette DNS pour qui.

    Ajouter un point de terminaison pour chaque que vous voulez que le Gestionnaire de trafic Azure à acheminer le trafic vers votre région géographique.
    Pour plus d’informations et pour obtenir la procédure détaillée comment ajouter / configurer des points de terminaison dans le Gestionnaire de trafic, reportez-vous à [Ajouter, désactiver, activer ou supprimer des points de terminaison](../traffic-manager/traffic-manager-endpoints.md)
    
4. **Sonde configurer :** Dans la page Gestionnaire de trafic, cliquez sur Configuration. Dans la page configuration, vous devez modifier les paramètres du moniteur pour détecter en HTTP port 80 et le chemin d’accès relatif /adfs/probe

    ![Configurer sonde](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/mystsconfig.png) 

    >[AZURE.NOTE] **Vérifiez que l’état des points de terminaison est en ligne une fois la configuration terminée**. Si tous les points de terminaison sont « dégradation », Azure le trafic Manager n’une tentative de meilleures pour acheminer le trafic en supposant que diagnostics est incorrecte et tous les points de terminaison sont accessibles.

5. **Enregistrements DNS de modification pour le Gestionnaire de trafic Azure :** Votre service de fédération doit être un enregistrement CNAME pour le nom Azure le trafic Gestionnaire DNS. Créer un enregistrement CNAME dans les enregistrements DNS publics pour permettre à toute personne essaie de contacter le service de fédération réellement atteint le Gestionnaire de trafic Azure.

    Par exemple, pour faire pointer le fs.fabidentity.com de service de fédération dans le Gestionnaire de trafic, vous devez mettre à jour votre enregistrement de ressource DNS pour être le suivant :

    <code>fs.fabidentity.com IN CNAME mysts.trafficmanager.net</code>

##<a name="test-the-routing-and-ad-fs-sign-in"></a>Tester le routage et connexion AD FS   

###<a name="routing-test"></a>Routage de test

Un test très simple pour le routage serait pour essayer de commande ping sur le nom du service DNS de fédération à partir d’un ordinateur dans chaque région géographique. Selon la méthode de routage choisie, il se connecte à réellement le point de terminaison est reflétée dans l’affichage de la commande ping. Par exemple, si vous avez sélectionné la gamme de performances, le point de terminaison plus proche de la région du client sera atteinte. Voici la capture instantanée de deux commandes ping à partir de deux ordinateurs client zone différente, un dans la région EastAsia et un aux États-Unis Ouest. 

![Routage de test](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/pingtest.png)

###<a name="ad-fs-sign-in-test"></a>AD FS connexion test

Pour tester AD FS, le plus simple consiste à l’aide de la page IdpInitiatedSignon.aspx. Afin de pouvoir effectuer qu’il est nécessaire pour activer la IdpInitiatedSignOn sur les propriétés AD FS. Suivez les étapes ci-dessous pour vérifier votre installation AD FS
 
1. Exécuter l’en dessous de l’applet de commande sur le serveur AD FS, à l’aide de PowerShell, à défini sur activé. Jeu-AdfsProperties - EnableIdPInitiatedSignonPage $true
2. À partir de n’importe quel https:// accès machine externe<yourfederationservicedns>/adfs/ls/IdpInitiatedSignon.aspx
3. Vous devez voir la page AD FS comme ci-dessous :

    ![ADFS test - demande d’authentification](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest1.png)

    puis sur connexion réussie, il vous propose un message de réussite comme indiqué ci-dessous :

    ![Test ADFS - succès de l’authentification](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest2.png)
 
##<a name="related-links"></a>Liens connexes
* [Déploiement de base de AD FS dans Azure](active-directory-aadconnect-azure-adfs.md)
* [Gestionnaire de trafic Microsoft Azure](../traffic-manager/traffic-manager-overview.md)
* [Méthodes de routage de trafic le trafic Manager](../traffic-manager/traffic-manager-routing-methods.md)

##<a name="next-steps"></a>Étapes suivantes
* [Gérer un profil Azure le trafic Manager](../traffic-manager/traffic-manager-manage-profiles.md)
* [Ajouter, désactiver, activer ou supprimer des points de terminaison](../traffic-manager/traffic-manager-endpoints.md) 

