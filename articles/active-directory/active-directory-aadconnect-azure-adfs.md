<properties
    pageTitle="Active Directory Federation Services dans Azure | Microsoft Azure"
    description="Dans ce document, vous allez apprendre à déployer ADFS dans Azure pour haute disponibilité."
    keywords="déployer ADFS dans azure, déployer adfs azure, adfs azure, azure ad fs, déployer adfs, déployer ADFS, adfs dans azure, déployer adfs dans azure, déployer ADFS dans azure azure AD FS, introduction à AD FS, Azure, AD FS dans Azure, iaas, ADFS, déplacer adfs d’azure"
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
    ms.date="10/03/2016"
    ms.author="anandy;billmath"/>

# <a name="ad-fs-deployment-in-azure"></a>Déploiement AD FS Azure 

AD FS fournit fédération des identités simplifié, sécurisé et fonctionnalités authentification unique (SSO) Web. Fédération avec Azure AD ou O365 permet aux utilisateurs de s’authentifier à l’aide des informations d’identification en local et accéder à toutes les ressources dans le cloud. Par conséquent, il est important de disposer d’une infrastructure AD FS hautement disponible pour garantir l’accès aux ressources à la fois en local et dans le cloud. Déploiement d’AD FS dans Azure permettent de disponibilité élevée requise avec efforts minimales.
Il existe plusieurs avantages du déploiement AD FS dans Azure, certains d'entre eux sont présentées ci-après :

* **Disponibilité** - grâce à la puissance des jeux de disponibilité Azure, vous assurer une infrastructure hautement disponible.
* **Facilité d’échelle** – besoin de meilleures performances ? Facilement migrer vers machines plus importants en quelques clics dans Azure
* **Entre-Geo redondance** – avec Azure Geo redondance vous pouvez être assuré que votre infrastructure est hautement disponible dans le monde
* **Facile à gérer** – avec les options de gestion hautement simplifiée portail Azure, gestion de votre infrastructure est très simple et optimisé 

## <a name="design-principles"></a>Principes de conception

![Conception de déploiement](./media/active-directory-aadconnect-azure-adfs/deployment.png)

Le schéma ci-dessus présente la topologie de base recommandée pour commencer à déployer votre infrastructure AD FS dans Azure. Les principes derrière les divers composants de la topologie sont présentées ci-après :

* **DC / serveurs ADFS**: Si vous avez moins de 1 000 utilisateurs que vous pouvez simplement installer rôle AD FS sur votre domaine. Si vous ne voulez pas d’incidence sur les performances sur le domaine ou si vous avez plus de 1 000 utilisateurs, puis déployer ADFS sur des serveurs distincts.
* **Serveur WAP** – il n’est nécessaire déployer des serveurs Proxy de l’Application Web, afin que les utilisateurs peuvent atteindre la AD FS lorsqu’ils ne sont pas sur le réseau d’entreprise également.
* **DMZ**: serveurs Proxy de l’Application Web The seront placés dans la zone DMZ et accès uniquement TCP/443 est autorisé entre la zone DMZ et le sous-réseau interne.
* **Programmes d’équilibrage de charge**: pour optimiser la disponibilité des serveurs AD FS et Proxy de l’Application Web, nous recommandons d’utiliser un programme d’équilibrage de charge interne pour les serveurs AD FS et équilibrage de charge Azure pour les serveurs Proxy de l’Application Web.
* **Jeux de disponibilité**: pour assurer la redondance à votre déploiement AD FS, il est recommandé de regrouper les deux ou plusieurs machines virtuelles dans un jeu de disponibilité pour les charges de travail similaires. Cette configuration garantit que pendant un événement de maintenance planifiée ou, au moins une machine virtuelle sera disponible
* **Comptes de stockage**: il est recommandé d’avoir deux comptes de stockage. Avoir un compte de stockage unique peut entraîner la création d’un point de défaillance unique et peut entraîner le déploiement indisponible dans un scénario peu probable où le compte de stockage va vers le bas. Deux comptes de stockage novice pour associer un compte de stockage pour chaque ligne de défaillance.
* **Répartition des réseau**: serveurs Proxy de l’Application Web doivent être déployés dans un réseau DMZ distinct. Vous pouvez diviser un réseau virtuel en deux sous-réseaux, puis déployez l’ou les serveurs Proxy de l’Application Web dans un sous-réseau isolé. Vous pouvez simplement configurer les paramètres de groupe de sécurité de réseau de chaque sous réseau et autoriser uniquement les communications entre les deux sous-réseaux requises. Plus de détails sont fournis par le scénario de déploiement ci-dessous

##<a name="steps-to-deploy-ad-fs-in-azure"></a>Étapes pour déployer ADFS dans Azure

Les étapes décrites dans cette section décrivent le guide de déploiement du dessous représenté infrastructure AD FS dans Azure.

### <a name="1-deploying-the-network"></a>1. déploiement du réseau

Comme indiqué ci-dessus, vous pouvez soit créer deux sous-réseaux dans un seul réseau virtuel sinon créer deux réseaux virtuels complètement différents (VNet). Cet article vous concentrer sur le déploiement d’un seul réseau virtuel et diviser en deux sous-réseaux. Il s’agit d’une approche plus facile comme deux VNets distinctes de verser une VNet VNet passerelle pour les communications actuellement.

**1.1 créer réseau virtuel**

![Créer le réseau virtuel](./media/active-directory-aadconnect-azure-adfs/deploynetwork1.png)
    
Dans le portail Azure, sélectionnez réseau virtuel et que vous pouvez déployer le réseau virtuel et un sous-réseau immédiatement avec un simple clic. INT sous-réseau est également définie et est maintenant prêt pour les machines virtuelles à ajouter.
L’étape suivante consiste à ajouter un autre sous-réseau au réseau, c'est-à-dire le sous-réseau DMZ. Pour créer le sous-réseau DMZ, il vous suffit

* Sélectionnez le réseau nouvellement créé
* Dans les propriétés sélectionnez sous-réseau
* Dans le sous-réseau panneau cliquez sur le bouton Ajouter
* Fournissez les informations sous-réseau nom et l’adresse espace pour créer le sous-réseau

![Sous-réseau](./media/active-directory-aadconnect-azure-adfs/deploynetwork2.png)


![Sous-réseau DMZ](./media/active-directory-aadconnect-azure-adfs/deploynetwork3.png)

**1.2. création du réseau de groupes de sécurité**

Un groupe de sécurité réseau (NSG) contient la liste des règles de liste de contrôle (accès) autoriser ou refuser le trafic réseau vers vos instances machine virtuelle dans un réseau virtuel. NSGs peuvent être associés aux sous-réseaux ou instances machine virtuelle individuelles dans ce sous-réseau. Lorsqu’un NSG est associé à un sous-réseau, les règles et s’appliquent à toutes les instances de ce sous-réseau machine virtuelle.
Aux fins de ce guide, nous allons créer deux NSGs : un pour chacun d’un réseau interne et une DMZ. Seront intitulées NSG_INT et NSG_DMZ respectivement.

![Créer NSG](./media/active-directory-aadconnect-azure-adfs/creatensg1.png)

Une fois la NSG est créé, il sera 0 règles sortantes entrants et 0. Une fois que les rôles sur les serveurs respectifs sont installés et opérationnels, les règles entrants et sortants peuvent être effectuées selon le niveau de sécurité souhaité.

![Initialisation NSG](./media/active-directory-aadconnect-azure-adfs/nsgint1.png)

Après la création des NSGs, associer NSG_INT sous-réseau ent et NSG_DMZ avec sous-réseau DMZ. Une capture d’écran de l’exemple est donné ci-dessous :

![Configurer NSG](./media/active-directory-aadconnect-azure-adfs/nsgconfigure1.png)

* Cliquez sur sous-réseaux pour ouvrir le panneau de configuration pour sous-réseaux
* Sélectionnez le sous-réseau à associer à la NSG 

Après la configuration, le panneau de configuration pour sous-réseaux doit ressembler à ci-dessous :

![Sous-réseaux après NSG](./media/active-directory-aadconnect-azure-adfs/nsgconfigure2.png)

**1.3. créer la connexion vers local**

Nous aurons besoin d’une connexion à local pour déployer le contrôleur de domaine (DC) dans azure. Azure propose différentes options de connectivité à connecter votre infrastructure en local à votre infrastructure Azure.

* Point-à-site
* Réseau virtuel à un site
* ExpressRoute

Il est recommandé d’utiliser ExpressRoute. ExpressRoute vous permet de créer des connexions entre Azure centres de données et l’infrastructure dans vos locaux ou dans un environnement de co-création emplacement. ExpressRoute connexions ne passent pas sur l’Internet public. Offrent plus la fiabilité, vitesses et latence inférieure et sécurité plus élevée que connexions classiques via Internet.
Tandis qu’il est recommandé d’utiliser ExpressRoute, vous pouvez choisir n’importe quelle méthode de connexion plus adapté à votre organisation. Pour en savoir plus sur les ExpressRoute et les différentes options de connectivité à l’aide de ExpressRoute, lisez [présentation technique ExpressRoute](https://aka.ms/Azure/ExpressRoute).

### <a name="2-create-storage-accounts"></a>2. créer des comptes de stockage

Pour conserver une disponibilité élevée et éviter de dépendre d’un compte de stockage unique, vous pouvez créer deux comptes de stockage. Diviser les ordinateurs dans chaque ensemble de disponibilité en deux groupes, puis affectez-leur chaque groupe à un compte de stockage distincte.

![Créer des comptes de stockage](./media/active-directory-aadconnect-azure-adfs/storageaccount1.png)

### <a name="3-create-availability-sets"></a>3. créer des jeux de disponibilité

Pour chaque rôle (DC/AD FS et WAP), créer des jeux de disponibilité contenant 2 machines au minimum. Cela vous aidera à atteindre une plus grande disponibilité pour chaque rôle. Création de la disponibilité de jeux, il est essentiel de choisir les éléments suivants :
* **Domaines d’erreur**: machines virtuelles dans le même domaine défaillance partager la même source d’alimentation et le commutateur de réseau physique. Un minimum de 2 domaines d’erreur sont recommandés. La valeur par défaut est 3 et vous pouvez laisser tel quel aux fins de ce déploiement
* **Mettre à jour les domaines**: redémarrage ensemble des Machines appartenant au même domaine mise à jour au cours d’une mise à jour. Vous voulez disposer de 2 domaines de mise à jour. La valeur par défaut est 5 et vous pouvez laisser tel quel aux fins de ce déploiement

![Jeux de disponibilité](./media/active-directory-aadconnect-azure-adfs/availabilityset1.png)

Créer des jeux de disponibilité suivant

| Jeu de disponibilité | Rôle | Domaines d’erreur | Mettre à jour des domaines |
|:----------------:|:----:|:-----------:|:-----------|
| contosodcset | CONTRÔLEUR DE DOMAINE/ADFS | 3 | 5 |
| contosowapset | WAP | 3 | 5 |

### <a name="4--deploy-virtual-machines"></a>4. déployer des machines virtuelles
L’étape suivante consiste à déployer machines virtuelles hébergeant les différents rôles dans votre infrastructure. Un minimum de deux machines sont recommandés dans chaque ensemble de disponibilité. Créer des machines virtuelles six pour le déploiement de base.

| Machine | Rôle | Sous-réseau | Jeu de disponibilité | Compte de stockage | Adresse IP |
|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|contosodc1|CONTRÔLEUR DE DOMAINE/ADFS|ENT|contosodcset|contososac1|Statique|
|contosodc2|CONTRÔLEUR DE DOMAINE/ADFS|ENT|contosodcset|contososac2|Statique|
|contosowap1|WAP|DMZ|contosowapset|contososac1|Statique|
|contosowap2|WAP|DMZ|contosowapset|contososac2|Statique|

Comme vous l’avez peut-être remarqué, aucune NSG n’a été spécifié. C’est parce qu’azure vous permet d’utiliser NSG au niveau du sous-réseau. Ensuite, vous pouvez contrôler le trafic réseau ordinateur à l’aide de la NSG individuel associé aux soit le sous-réseau sinon l’objet carte réseau. En savoir plus sur [ce qui est un groupe de sécurité réseau (NSG)](https://aka.ms/Azure/NSG).
Adresse IP statique est recommandé si vous gérez le DNS. Vous pouvez utiliser le système DNS Azure et à la place dans les enregistrements DNS pour votre domaine, reportez-vous aux nouvelles machines par leurs noms de domaine complets Azure.
Votre volet machine virtuelle doit ressembler à ci-dessous après la fin du déploiement :

![Machines virtuelles déployés](./media/active-directory-aadconnect-azure-adfs/virtualmachinesdeployed_noadfs.png)

### <a name="5-configuring-the-domain-controller--ad-fs-servers"></a>5. Configuration du contrôleur de domaine / serveurs AD FS
 Afin de s’authentifier toutes les demandes entrantes, AD FS devrez contacter le contrôleur de domaine. Pour enregistrer votre voyage coûteux à partir d’Azure à contrôleur de domaine local pour l’authentification, il est recommandé de déployer une réplique du contrôleur de domaine dans Azure. Pour atteindre une disponibilité élevée, il est recommandé pour créer un ensemble de disponibilité du domaine 2 au moins.

|Contrôleur de domaine|Rôle|Compte de stockage|
|:-----:|:-----:|:-----:|
|contosodc1|Réplica|contososac1|
|contosodc2|Réplica|contososac2|

* Promouvoir les deux serveurs comme contrôleurs de domaine réplica avec le système DNS
* Configurer les serveurs AD FS en installant le rôle AD FS en utilisant le Gestionnaire de serveur.

###<a name="6---deploying-internal-load-balancer-ilb"></a>6. déploiement d’équilibrage de charge interne (ILB)

**6.1. créer la ILB**

Pour déployer un ILB, sélectionnez programmes d’équilibrage de charge dans le portail Azure et cliquez sur Ajouter (+).
>[AZURE.NOTE] Si vous ne voyez pas les **Programmes d’équilibrage de charge** dans le menu, cliquez sur **Parcourir** dans le coin inférieur gauche le portail et faites défiler jusqu'à ce que vous voyiez **Programmes d’équilibrage de charge**.  Cliquez sur l’étoile jaune pour l’ajouter à votre menu. Sélectionnez maintenant la nouvelle icône d’équilibrage de charge pour ouvrir le panneau de configuration pour commencer la configuration de l’équilibrage de charge.

![Parcourir d’équilibrage de charge](./media/active-directory-aadconnect-azure-adfs/browseloadbalancer.png)

* **Nom**: donner un nom approprié pour l’équilibrage de charge
* **Modèle**: dans la mesure où cette équilibrage de charge sera placé devant les serveurs AD FS et est conçu pour les connexions réseau interne uniquement, sélectionnez « Interne »
* **Réseau virtuel**: choisissez le réseau virtuel où vous déployez votre AD FS
* **Sous-réseau**: choisissez le sous-réseau interne ici
* **Attribution d’adresses IP**: dynamique

![Équilibrage de charge interne](./media/active-directory-aadconnect-azure-adfs/ilbdeployment1.png)
 
Après avoir cliqué sur Créer et la ILB est déployé, vous devez le voir dans la liste des programmes d’équilibrage de charge :

![Programmes d’équilibrage de charge après ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment2.png)
 
Étape suivante consiste à configurer le pool principal et la sonde de serveur principal.

**6.2. configurer pool de serveur principal ILB**

Sélectionnez le ILB nouvellement créé dans le panneau d’équilibrage de charge. Le panneau Paramètres s’ouvre. 
1.  Sélectionnez des pools principal dans le panneau Paramètres
2.  Dans le panneau de configuration du pool de serveur principal ajouter, cliquez sur Ajouter une machine virtuelle
3.  Vous obtenez un panneau où vous pouvez choisir jeu de disponibilité
4.  Choisissez le jeu de disponibilité AD FS

![Configuration du pool de serveur principal ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment3.png)
 
**6.3. Configuration de sonde**

Dans la ILB panneau Paramètres, sélectionnez teste.
1.  Cliquez sur Ajouter
2.  Fournir des détails pour sonde un. **Nom**: sonde nom b. **Protocole**: TCP c. **Port**: 443 (HTTPS) d. **Intervalle**: 5 (valeur par défaut) : il s’agit de l’intervalle auquel ILB testent les ordinateurs dans le pool principal e. **Seuil d’intégrité**: 2 (par défaut ue val) – il s’agit du seuil d’échecs consécutifs sonde après laquelle ILB déclarer un ordinateur dans le pool principal qui ne répond pas et arrêter envoyant le trafic vers celle-ci.

![Configurer la sonde ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment4.png)
 
**6.4. créer des règles d’équilibrage de charge**

Pour équilibrer efficacement le trafic, la ILB doit être configuré avec des règles d’équilibrage de charge. Afin de créer une règle, d’équilibrage de charge 
1.  Sélectionnez la règle à partir du panneau Paramètres de la ILB d’équilibrage de charge
2.  Cliquez sur Ajouter dans la panneau de configuration de règle d’équilibrage de charge
3.  Dans la panneau de configuration de règle d’équilibrage de charge ajouter une. **Nom**: indiquez un nom pour la règle b. **Protocole**: sélectionnez TCP c. **Port**: 443 d. **Port principal**: 443 e. **Serveur principal pool**: sélectionnez le pool que vous avez créé pour les AD FS cluster f antérieur. **Sonde**: sélectionnez la sonde créée pour serveurs AD FS

![Configurer ILB équilibrage de règles](./media/active-directory-aadconnect-azure-adfs/ilbdeployment5.png)

**6.5. mettre à jour DNS avec ILB**

Accédez à votre serveur DNS et créer un enregistrement CNAME pour la ILB. L’enregistrement CNAME doit être du service de fédération avec l’adresse IP pointant vers l’adresse IP de l’ILB. Par exemple, si l’adresse fondu ILB est 10.3.0.8 et le service de fédération installée est fs.contoso.com, puis créer un enregistrement CNAME pour fs.contoso.com en pointant sur 10.3.0.8.
Cela permet de garantir que toutes les communications concernant fs.contoso.com fin vers le haut à la ILB et sont routées correctement.

###<a name="7---configuring-the-web-application-proxy-server"></a>7. Configuration du serveur Proxy de l’Application Web

**7.1. Configuration des serveurs Proxy de l’Application Web pour atteindre les serveurs AD FS**

Afin de vous assurer que les serveurs Proxy de l’Application Web sont en mesure d’atteindre les serveurs AD FS derrière le ILB, créez un enregistrement dans la %systemroot%\system32\drivers\etc\hosts pour la ILB. Notez que le nom unique (nom de domaine) doit être le nom de service de fédération, par exemple fs.contoso.com. Et l’entrée IP doit être celle de l’adresse IP de ILB (10.3.0.8 comme dans l’exemple).

**7.2. l’installation du rôle de Proxy de l’Application Web**

Une fois que vous vous assurer que les serveurs Proxy de l’Application Web sont en mesure d’atteindre les serveurs AD FS derrière ILB, vous pouvez ensuite installer les serveurs Proxy de l’Application Web. Les serveurs Proxy de l’Application Web ne pas être joint au domaine. Installer les rôles Proxy de l’Application Web sur les deux serveurs Proxy de l’Application Web en sélectionnant le rôle d’accès à distance. Le Gestionnaire de serveur vous guide pour terminer l’installation WAP.
Pour plus d’informations sur le déploiement WAP, lisez [installer et configurer le serveur Proxy d’Application Web](https://technet.microsoft.com/library/dn383662.aspx).

###<a name="8---deploying-the-internet-facing-public-load-balancer"></a>8. déploiement Internet Facing équilibrage de charge (Public)

**8.1. créer ouvert équilibrage de charge (Public) sur Internet**
 
Dans le portail Azure, sélectionnez programmes d’équilibrage de charge, puis cliquez sur Ajouter. Dans le panneau d’équilibrage de charge de créer, entrez les informations suivantes
1. **Nom**: nom de l’équilibrage de charge
2. **Modèle**: Public – cette option indique que cet équilibrage de charge devront une adresse publique à Azure.
3. **Adresse IP**: créer une nouvelle adresse IP (dynamique)

![Équilibrage de charge qui fait face à Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment1.png)

Après le déploiement, l’équilibrage de charge s’affichent dans la liste de programmes d’équilibrage de charge.

![Liste d’équilibrage de charge](./media/active-directory-aadconnect-azure-adfs/elbdeployment2.png)
 
**8.2. attribuer une étiquette DNS à l’adresse IP publique**

Cliquez sur l’entrée d’équilibrage de charge nouvellement créé dans le panneau de programmes d’équilibrage de charge pour faire apparaître le panneau de configuration. Procédez comme suit pour configurer l’étiquette DNS pour l’adresse IP publique :
1.  Cliquez sur l’adresse IP. Le panneau de configuration s’ouvre pour l’adresse IP publique et ses paramètres
2.  Cliquez sur Configuration
3.  Fournir une étiquette DNS. Cela devient l’étiquette DNS public que vous pouvez accéder à partir de n’importe où, par exemple contosofs.westus.cloudapp.azure.com. Vous pouvez ajouter une entrée dans le DNS externe pour le service de fédération (par exemple, fs.contoso.com) qui correspond à l’étiquette DNS de l’équilibrage de charge externe (contosofs.westus.cloudapp.azure.com).

![Configurer ouvert équilibrage de charge sur internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment3.png) 

![Configurer internet facing équilibrage de charge (DNS)](./media/active-directory-aadconnect-azure-adfs/elbdeployment4.png)

**8.3. configurer pool principal pour équilibrage de charge Internet Facing (Public)** 

Suivez les mêmes étapes comme dans la création de l’équilibrage de charge interne, pour configurer le pool principal pour face à Internet (Public) équilibrage de charge en tant que la disponibilité défini pour les serveurs WAP. Par exemple, contosowapset.

![Configuration du pool principal d’équilibrage de charge face à Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment5.png)
 
**8,4. configurer la sonde**

Suivez les mêmes étapes comme dans la configuration de l’équilibrage de charge interne pour configurer la sonde pour le pool principal de serveurs WAP.

![Configurer sonde d’équilibrage de charge face à Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment6.png)
 
**8.5. créer une ou plusieurs règles d’équilibrage de charge**

Suivez les mêmes étapes comme dans ILB pour configurer l’équilibrage de charge règle pour TCP 443.

![Configurer des règles de l’équilibrage de charge d’équilibrage de charge face à Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment7.png)
 
###<a name="9---securing-the-network"></a>9. Sécurisation du réseau

**9.1. Sécurisation du sous-réseau interne**

En général, vous devez les règles suivantes pour sécuriser efficacement votre sous-réseau interne (dans l’ordre comme indiqué ci-dessous)

|Règle|Description|Flux|
|:----|:----|:------:|
|AllowHTTPSFromDMZ| Autoriser la communication HTTPS de DMZ | Entrant |
|DenyInternetOutbound| Pas de l’accès à internet | Sortant |

![Règles d’accès INT (entrant)](./media/active-directory-aadconnect-azure-adfs/nsg_int.png)

[commentaire] : <> (![règles d’accès INT (entrant)](./media/active-directory-aadconnect-azure-adfs/nsgintinbound.png)) [commentaire] : <> (![règles d’accès INT (sortants)](./media/active-directory-aadconnect-azure-adfs/nsgintoutbound.png))
 
**9.2. Sécurisation du sous-réseau DMZ**

|Règle|Description|Flux|
|:----|:----|:------:|
|AllowHTTPSFromInternet| Autoriser HTTPS depuis internet vers la zone DMZ | Entrant|
|DenyInternetOutbound|  N’est pas HTTPS à internet est bloqué | Sortant |

![Règles d’accès de texte (entrant)](./media/active-directory-aadconnect-azure-adfs/nsg_dmz.png)

[commentaire] : <> (![règles d’accès de texte (entrant)](./media/active-directory-aadconnect-azure-adfs/nsgdmzinbound.png)) [commentaire] : <> (![règles d’accès texte (sortants)](./media/active-directory-aadconnect-azure-adfs/nsgdmzoutbound.png))

>[AZURE.NOTE] Si l’authentification des certificats utilisateur du client (authentification clientTLS à l’aide de X509 certificats de l’utilisateur) est requise, puis AD FS requiert ports TCP port 49443 être activé pour l’accès entrant.

###<a name="10--test-the-ad-fs-sign-in"></a>10. tester la connexion à AD FS

Le plus simple consiste à tester AD FS est à l’aide de la page IdpInitiatedSignon.aspx. Afin de pouvoir effectuer qu’il est nécessaire pour activer la IdpInitiatedSignOn sur les propriétés AD FS. Suivez les étapes ci-dessous pour vérifier votre installation AD FS
1.  Exécuter l’en dessous de l’applet de commande sur le serveur AD FS, à l’aide de PowerShell, à défini sur activé.
    Jeu-AdfsProperties - EnableIdPInitiatedSignonPage $true 
2.  À partir de n’importe quel https://adfs.thecloudadvocate.com/adfs/ls/IdpInitiatedSignon.aspx accès machine externe  
3.  Vous devez voir la page AD FS comme ci-dessous :

![Page de connexion de test](./media/active-directory-aadconnect-azure-adfs/test1.png)

Dans connexion réussie, il vous propose un message de réussite comme indiqué ci-dessous :

![Tester succès](./media/active-directory-aadconnect-azure-adfs/test2.png)

## <a name="template-for-deploying-ad-fs-in-azure"></a>Modèle de déploiement d’AD FS dans Azure

Le modèle déploie une configuration 6 machine, 2 pour le domaine, AD FS et WAP.

[AD FS dans le modèle de déploiement d’Azure](https://github.com/paulomarquesc/adfs-6vms-regular-template-based)

Vous pouvez utiliser un réseau virtuel existant ou créer un nouveau VNET lors du déploiement de ce modèle. Les différents paramètres disponibles pour la personnalisation du déploiement sont présentées ci-après avec la description de l’utilisation du paramètre dans le processus de déploiement. 

| Paramètre | Description |
|:--------|:-----|
|Emplacement| La région pour déployer les ressources dans, par exemple, Moyen-Orient contactez-nous. |
|StorageAccountType| Le type du compte de stockage créé|
|VirtualNetworkUsage| Indique si un réseau virtuel est créé ou utiliser une existante|
|VirtualNetworkName| Le nom du réseau virtuel pour créer, obligatoire sur l’utilisation du réseau virtuel nouveau ou existant|
|VirtualNetworkResourceGroupName| Spécifie le nom du groupe de ressources où se trouve le réseau virtuel existant. Lorsque vous utilisez un réseau virtuel, cela devient un paramètre obligatoire pour le déploiement puisse trouver l’ID du réseau virtuel existant|
|VirtualNetworkAddressRange| La plage d’adresses de la nouvelle VNET, obligatoire si vous créez un réseau virtuel|
|InternalSubnetName| Le nom du sous-réseau interne, obligatoire sur les deux options de l’utilisation de réseau virtuel (nouveau ou existantes)|
|InternalSubnetAddressRange| La plage d’adresses du sous-réseau interne, qui contient les serveurs de domaine et ADFS, obligatoires si vous créez un réseau virtuel.|
|DMZSubnetAddressRange| La plage d’adresses du sous-réseau dmz, qui contient les serveurs de proxy application Windows, obligatoires si vous créez un réseau virtuel.|
|DMZSubnetName| Le nom du sous-réseau interne, obligatoire sur les deux options de l’utilisation de réseau virtuel (nouveau ou existantes). |
|ADDC01NICIPAddress| L’adresse IP interne du premier contrôleur de domaine, cette adresse IP statique attribuée au contrôleur de domaine et doit constituer une adresse ip valide au sein du sous-réseau interne|
|ADDC02NICIPAddress| L’adresse IP interne du deuxième contrôleur de domaine, cette adresse IP statique attribuée au contrôleur de domaine et doit constituer une adresse ip valide au sein du sous-réseau interne|
|ADFS01NICIPAddress| L’adresse IP interne du premier serveur ADFS, cette adresse IP doivent être affectée statique vers le serveur ADFS et doit constituer une adresse ip valide au sein du sous-réseau interne|
|ADFS02NICIPAddress| L’adresse IP interne du deuxième serveur ADFS, cette adresse IP doivent être affectée statique vers le serveur ADFS et doit constituer une adresse ip valide au sein du sous-réseau interne|
|WAP01NICIPAddress| L’adresse IP interne du premier serveur WAP, cette adresse IP statique attribuée au serveur WAP et doit constituer une adresse ip valide au sein du sous-réseau DMZ|
|WAP02NICIPAddress| L’adresse IP interne du deuxième serveur WAP, cette adresse IP statique attribuée au serveur WAP et doit constituer une adresse ip valide au sein du sous-réseau DMZ|
|ADFSLoadBalancerPrivateIPAddress| L’adresse IP interne de l’équilibrage de charge ADFS, cette adresse IP statique attribuée à l’équilibrage de charge et doit constituer une adresse ip valide au sein du sous-réseau interne|
|ADDCVMNamePrefix| Machine virtuelle préfixe du nom de domaine|
|ADFSVMNamePrefix| Machine virtuelle préfixe du nom de serveurs ADFS|
|WAPVMNamePrefix| Machine virtuelle préfixe du nom de serveurs WAP|
|ADDCVMSize| La taille de la mémoire virtuelle contrôleur de domaine|
|ADFSVMSize| La taille de la mémoire virtuelle des serveurs ADFS|
|WAPVMSize| La taille de la mémoire virtuelle des serveurs WAP|
|AdminUserName| Le nom de l’administrateur local des machines virtuelles|
|AdminPassword| Le mot de passe pour le compte d’administrateur local des machines virtuelles|

## <a name="additional-resources"></a>Ressources supplémentaires
* [Jeux de disponibilité](https://aka.ms/Azure/Availability ) 
* [Équilibrage de charge Azure](https://aka.ms/Azure/ILB)
* [Équilibrage de charge interne](https://aka.ms/Azure/ILB/Internal)
* [Équilibrage de charge avec accès via Internet](https://aka.ms/Azure/ILB/Internet)
* [Comptes de stockage](https://aka.ms/Azure/Storage )
* [Réseaux virtuels Azure](https://aka.ms/Azure/VNet)
* [AD FS et liens Proxy d’Application Web](http://aka.ms/ADFSLinks) 

## <a name="next-steps"></a>Étapes suivantes

* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
* [Configurer et gérer votre Federation Services à l’aide d’Azure AD Connect](active-directory-aadconnectfed-whatis.md)
* [Déploiement de FS disponibilité AD croisée géographiques dans Azure avec le Gestionnaire de trafic Azure](active-directory-adfs-in-azure-with-azure-traffic-manager.md)




