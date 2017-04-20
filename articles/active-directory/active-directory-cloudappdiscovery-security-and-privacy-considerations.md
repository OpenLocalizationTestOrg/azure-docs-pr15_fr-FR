<properties
    pageTitle="Sécurité de découverte d’application et les considérations relatives à la confidentialité en nuage | Microsoft Azure"
    description="Cette rubrique décrit les considérations en matière de sécurité et confidentialité concernent Cloud application découverte."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>

# <a name="cloud-app-discovery-security-and-privacy-considerations"></a>Sécurité de découverte application cloud et considérations relatives à la confidentialité

Microsoft s’engage à protéger votre confidentialité et sécurisez vos données, lors de l’exécution des logiciels et services qui vous aident à gérer la sécurité de votre organisation. <br>
Nous reconnaissent que lorsque vous confier vos données à d’autres personnes, que la confidentialité requiert investissements ingénierie sécurité rigoureux et l’expertise pour la restaurer.
Microsoft est conforme aux directives de sécurité et conformité stricte de pratiques du cycle de vie de développement de logiciels sécurisés à un service. <br>
Sécuriser et protection des données sont une priorité de Microsoft.

Cette rubrique explique comment les données sont collectées, traitées et sécurisées au sein de la recherche d’application Azure Active Directory Cloud




##<a name="overview"></a>Vue d’ensemble

Découverte d’application cloud est une fonctionnalité de Azure AD et il est hébergée dans Microsoft Azure. <br>
L’agent de point de terminaison Cloud application découverte est utilisé pour recueillir des données de découverte d’application à partir d’ordinateurs informatiques gérés. <br>
Les données collectées sont envoyées en toute sécurité via un canal chiffré au service de découverte d’application Azure AD Cloud. <br>
Les données de découverte d’application Cloud pour une organisation seront alors affichées dans le portail Azure. <br>


<center>![Fonctionnement de découverte d’application Cloud](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png)</center> <br>


Les sections suivantes suivre le flux des informations et décrivent comment il est sécurisé lorsqu’il se déplace à partir de votre organisation pour le service de découverte d’application Cloud et enfin sur le portail de découverte d’application Cloud.



## <a name="collecting-data-from-your-organization"></a>Collecte des données à partir de votre organisation

Pour pouvoir utiliser la fonctionnalité de découverte de Cloud application de Azure Active Directory pour obtenir des informations dans les applications utilisées par des employés de votre organisation, vous devez tout d’abord déployer l’agent de point de terminaison de découverte d’application Azure AD Cloud situées dans votre organisation.

Les administrateurs du client Azure Active Directory (ou leur délégué) peuvent télécharger le package d’installation à partir du portail Azure. L’agent peut être manuellement installée ou sur plusieurs ordinateurs dans l’organisation à l’aide de SCCM ou stratégie de groupe.

Pour plus d’informations sur les options de déploiement, voir le [Guide de déploiement de stratégie de groupe Cloud application découverte](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx).
<br>

### <a name="data-collected-by-the-agent"></a>Données collectées par l’agent

Les informations sous forme de plan dans la liste ci-dessous sont collectées par l’agent lors d’une connexion à une application Web. Les informations sont collectées uniquement pour les applications qui l’administrateur a configuré pour la découverte. <br>
Vous pouvez modifier la liste des applications cloud contrôlant l’agent via la carte Cloud application découverte dans Microsoft [Azure portal](https://portal.azure.com/), sous **paramètres**->**Collecte de données**->**liste des collections de l’application**. Pour plus d’informations, voir [Prise en main avec Cloud application découverte](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
<br>
**Catégorie d’informations**: les informations utilisateur <br>
**Description**: <br>
Le nom d’utilisateur Windows du processus qui a créé une requête à l’application Web cible (par exemple : domaine\nom_utilisateur) ainsi que l’identificateur de sécurité Windows (SID) de l’utilisateur.


**Catégorie d’information**: traitement des informations <br>
**Description**: <br>
Le nom du processus qui a créé la demande à l’application Web cible (par exemple : « iexplore.exe »)

**Catégorie d’information**: informations sur l’ordinateur <br>
**Description**: <br>
Le nom NetBIOS d’ordinateur sur lequel l’agent est installé.

**Catégorie d’information**: informations sur le trafic de l’application <br>
**Description**: <br>

Les informations de connexion suivantes :

- La source (ordinateur local) et les adresses IP de destination et les numéros de port

- L’adresse IP de l’organisation par le biais duquel la demande éteint.

- L’heure de la demande

- Le volume de trafic envoyés et reçus

- La version IP (4 ou 6)

- Pour les connexions TLS uniquement : le nom d’hôte cible à partir de l’extension de nom de serveur Indication ou le certificat de serveur.

Les informations HTTP suivantes :

- Méthode (GET, POST, etc.).

- Protocole (HTTP/1.1, etc.).

- Chaîne d’agent utilisateur

- Nom d’hôte

- URI (chaîne de requête à l’exception des) cible

- Informations sur le type de contenu

- Informations d’URL referrer (à l’exception de la chaîne de requête)



> [AZURE.NOTE] Les informations HTTP ci-dessus sont collectées pour toutes les connexions non chiffré.
Pour les connexions TLS, ces informations sont capturées uniquement lorsque le paramètre « Inspection approfondie » est activé dans le portail. Le paramètre est 'ON' par défaut.
Pour plus d’informations, voir ci-dessous et [Prise en main avec Cloud application découverte](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)


Outre les données que l’agent collecte sur l’activité du réseau, il collecte également des informations anonymes sur les logiciels et configuration matérielle, les rapports d’erreurs et d’informations sur l’utilisation de l’agent.

<br><br>
### <a name="how-the-agent-works"></a>Fonctionne de l’agent

Installation de l’agent inclut deux composants :

- Un composant en mode utilisateur

- Un composant de pilote en mode noyau (pilote plateforme de filtrage Windows)



Lors de la première installation de l’agent Il stocke un certificat approuvé spécifique à un ordinateur sur l’ordinateur qu’il utilise ensuite pour établir une connexion sécurisée avec le service de découverte d’application Cloud. <br>
L’agent récupère régulièrement configuration de la stratégie à partir du service Cloud application découverte sur cette connexion sécurisée. <br>
La stratégie inclut des informations sur les applications cloud à surveiller et si la mise à jour automatique doit être activé, entre autres choses.

Comme le trafic Web est envoyé et reçu sur l’ordinateur à partir d’Internet Explorer et Chrome, l’agent de découverte d’application Cloud analyse du trafic et extrait les métadonnées pertinentes (voir la section **données collectées par l’agent** ci-dessus). <br>
Minute, l’agent télécharge les métadonnées collectées au service de nuage application découverte sur un canal chiffré.

Le composant pilote intercepte du trafic chiffré et lui-même insère dans le flux chiffré. Obtenir plus d’informations dans la section **données modèle Intercepting de connexions chiffrées (inspection approfondie)** ci-dessous.


### <a name="respecting-user-privacy"></a>Confidentialité de l’utilisateur en respectant

Notre objectif est de proposer aux administrateurs les outils pour définir l’équilibre entre optique détaillée dans confidentialité de l’utilisation et de l’utilisateur application en fonction de leur organisation. À cet effet, nous fournissons les boutons suivants dans la page Paramètres dans le portail :

- **Collecte de données**: les administrateurs peuvent choisir spécifier les applications ou les catégories d’applications qu’ils souhaitent pour obtenir des données de découverte sur.

- **Inspection approfondie**: les administrateurs peuvent choisir pour spécifier si l’agent recueille le trafic HTTP pour les connexions SSL/TLS (également appelé **« Inspection approfondie »**). En savoir plus sur ce problème dans la section suivante.

- **Options de consentement**: les administrateurs peuvent utiliser le portail de découverte d’application Cloud permettent d’opter informer les utilisateurs de la collecte de données par l’agent, et si vous voulez obliger l’utilisateur acceptez avant le démarrage de l’agent de collecte des données de l’utilisateur.

L’agent de point de terminaison Cloud application découverte collecte uniquement les informations décrites dans la section **données collectées par l’agent** ci-dessus.


### <a name="intercepting-data-from-encrypted-connections-deep-inspection"></a>Intercepter les données à partir de connexions chiffrées (inspection approfondie)
Comme mentionné précédemment, les administrateurs peuvent configurer l’agent pour analyser les données à partir de connexions chiffrées (inspection approfondie). TLS ([Transport Layer Security](https://msdn.microsoft.com/library/windows/desktop/aa380516%28v=vs.85%29.aspx)) est un des protocoles plus courants en cours d’utilisation sur Internet aujourd'hui. En chiffrant les communications avec TLS, un client peut établir un canal de communication sécurisée et privée avec un serveur web ; TLS offre une protection essentielle pour la transmission des informations d’authentification et empêcher la divulgation d’informations sensibles.

Tandis que canal chiffré sécurisé bout en bout fourni par TLS permet de sécurité importants et la protection de la confidentialité, le protocole est fréquente à des fins malveillante ou malveillant. Tellement ainsi, en fait, cette TLS est souvent appelée à comme « protocole contournement de pare-feu universel ». La cause du problème est que la plupart des pare-feu ne peuvent pas inspecter les communications TLS, car les données de couches d’application sont chiffrées avec SSL. Sachant cela, les pirates exploitent fréquemment TLS pour présenter malveillants à un utilisateur certain que même les pare-feu de couches d’application plus intelligent voyants complètement sur TLS et doivent simplement relais communication TLS entre hôtes. Les utilisateurs finaux exploiter fréquemment TLS pour ignorer des mises en œuvre par leur pare-feu d’entreprise et les serveurs proxy, des contrôles d’accès à l’utiliser pour vous connecter aux proxys publics et pour les protocoles TLS non à travers le pare-feu qui peut être bloqué par la stratégie de tunnel.

Inspection approfondie permet à l’agent de découverte d’application Cloud pour l’utiliser comme un approuvé homme dans-the-milieu. Lorsqu’un client est demandé pour accéder à une ressource HTTPS protégé, le pilote de l’Agent de point de terminaison intercepte la connexion et établit qu'une connexion au serveur de destination pour récupère son certificat SSL au nom du client. L’agent puis vérifie que le certificat peut être approuvé (en vérifiant qu’il n’était pas révoqué et effectuer d’autres vérifications de certificat) et, si ces test, l’Agent de point de terminaison puis copie les informations à partir du certificat de serveur et crée son propre certificat de serveur--appelé un certificat interception--à l’aide de ces informations. Le certificat interception est connecté à la volée par l’agent de point de terminaison avec un certificat racine, qui est installé dans le magasin de certificats approuvé Windows. Ce certificat auto-signé est marqué non exportable et est et avait aux administrateurs. Il est destiné à jamais laisser l’ordinateur sur lequel il a été créé. Lors de l’application cliente de l’utilisateur final reçoit le certificat interception, il sera l’approbation, car il peut correctement valider la chaîne du certificat tout à fait au certificat racine. Ce processus est principalement transparent à partir point de vue d’un utilisateur final avec quelques inconvénients comme décrit ci-dessous.

En activant inspection approfondie, l’Agent de point de terminaison de découverte application Cloud déchiffrer et d’inspecter communications TLS chiffré, ce qui permet au service pour réduire le bruit et fournir des informations sur l’utilisation des applications cloud chiffré.

#### <a name="a-word-of-caution"></a>Un mot de faire preuve de prudence
Avant d’activer inspection approfondie, il est fortement recommandé que vous communiquez votre vos projets de votre juridiques et les départements RH et obtenez leur consentement. Inspecter des communications chiffré privées de l’utilisateur final peut être un sujet sensible, pour des raisons évidentes. Avant un production pellicule à la sortie d’inspection approfondie, assurez-vous que votre sécurité de l’entreprise et stratégies d’utilisation acceptable ont été mis à jour pour indiquer que les communications chiffrées vont être vérifiées. Notification de l’utilisateur et exonération de sites données sensibles (par exemple, bancaires et médicales sites) peuvent également être nécessaires si vous configurez découverte d’application Cloud pour les analyser. Comme indiqué ci-dessus, les administrateurs peuvent utiliser le portail de découverte d’application Cloud pour choisir si vous voulez informer les utilisateurs de la collecte de données par l’agent et s’il faut requérir consentement de l’utilisateur avant le démarrage de l’agent de collecte des données utilisateur.

### <a name="known-issues-and-drawbacks"></a>Inconvénients et les problèmes connus
Il existe plusieurs cas où interception TLS peut-être avoir un impact sur l’expérience utilisateur :

- Certificats de Validation (va) étendu rendre la barre d’adresses du navigateur web vert pour l’utiliser comme un indice que vous consultez un site web approuvé. Inspection TLS ne peut pas dupliquer va dans le certificat émis au client, pour les sites web qui utilisent des certificats EV fonctionne normalement, mais la barre d’adresses ne s’affichent pas vert.  

- Public épinglage clé (également connu sous le certificat épinglage) sont conçus pour aider les utilisateurs contre les attaques dans intermédiaire et non fiables autorités de certification. Lorsque le certificat racine pour un site épinglé ne correspond pas à une des connus bonne l’autorité de certification, le navigateur refuse la connexion avec une erreur. Dans la mesure où interception TLS est, en fait, un homme dans-the-milieu, ces connexions échouera.

- S’ils cliquent sur l’icône de verrou dans le navigateur de barre d’adresse navigateur à inspecter les informations du site, ils ne verront pas une chaîne se terminant par le certificat utilisé pour signer le certificat de site Web, mais plutôt une chaîne de certificat se terminant par les fenêtres approuvé magasin de certificats.

Pour réduire les occurrences de ces problèmes, nous effectuer le suivi des services cloud et les applications clientes connues pour utiliser une validation étendue ou épingler clé publique et demandez à l’Agent de point de terminaison pour éviter interception connexions concernées. Même dans ce cas, cependant, vous continuez à recevoir des rapports de l’utilisation de ces applications cloud et le volume de données transféré, mais qui ne sont pas approfondies inspectées, aucune plus d’informations sur la façon dont les applications ont été utilisées seront disponibles.


## <a name="sending-data-to-cloud-app-discovery"></a>Envoyer des données au Cloud application découverte

Une fois que les métadonnées ont été collectées par l’agent, il est mis en cache sur l’ordinateur pour une minute ou jusqu'à ce que les données mises en cache atteint une taille de 5 Mo. Il est ensuite compressé et envoyé via une connexion sécurisée au service de nuage application découverte.

Si l’agent ne parvient pas à communiquer avec le service de découverte d’application Cloud pour une raison quelconque, les métadonnées collectées sont stockées dans un cache local fichier qui sont accessibles par les utilisateurs dotés de privilèges sur l’ordinateur (par exemple, le groupe Administrateurs). <br>
L’agent tente automatiquement de renvoyer les métadonnées mises en cache jusqu'à ce qu’il a été correctement reçu par le service Cloud application découverte.



## <a name="receiving-the-data-at-the-service-end"></a>Reçoit les données à la fin de service

Les agents de s’authentifier à la découverte d’application Cloud service en utilisant le certificat d’authentification ordinateur client spécifique référencé ci-dessus et la transfère des données via un canal chiffré. <br>
Pipeline d’analytique du service Cloud application découverte traite des métadonnées pour chaque client séparément par logiquement le partition par toutes les étapes du pipeline analytique.
Les métadonnées analysées lecteurs différents rapports dans le portail.

Les métadonnées non transformées et les métadonnées analysées sont stockés pendant 180 jours. En outre, les clients peuvent choisir capturer les métadonnées analysées dans un compte de stockage blob Azure de leur choix.
Ceci est utile pour l’analyse en mode hors connexion des métadonnées ainsi que la conservation à long terme des données.

## <a name="accessing-the-data-using-the-azure-portal"></a>Accéder aux données à l’aide du portail Azure

Dans le but de sécuriser les métadonnées recueillies, par défaut seuls les administrateurs globaux du client ont accès à la fonctionnalité de découverte d’application Cloud dans le portail Azure. <br>
Toutefois, les administrateurs peuvent choisir de déléguer cet accès aux autres utilisateurs ou groupes.



> [AZURE.NOTE] Pour plus d’informations, voir [Prise en main avec Cloud application découverte](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)

<br>
Tout utilisateur accès aux données dans le portail, doit être dotée d’une licence Azure AD Premium.



##<a name="additional-resources"></a>Ressources supplémentaires


* [Comment puis-je connaître les applications cloud non sanctionnée qui sont utilisées dans mon organisation](active-directory-cloudappdiscovery-whatis.md)
* [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
