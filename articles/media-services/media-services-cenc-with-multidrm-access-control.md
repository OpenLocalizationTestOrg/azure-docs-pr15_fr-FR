<properties 
    pageTitle="CENC avec Multi-DRM et de contrôle d’accès : une conception de référence et l’application sur Azure et Azure Media Services | Microsoft Azure" 
    description="Découvrez comment Microsoft® lisse Streaming Client porter Kit de licence." 
    services="media-services" 
    documentationCenter="" 
    authors="willzhan"  
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="willzhan;kilroyh;yanmf;juliako"/>

#<a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>CENC avec Multi-DRM et de contrôle d’accès : une conception de référence et l’application sur Azure et Azure Media Services

##<a name="key-words"></a>Mots clés
 
Azure Active Directory, Azure Media Services, Azure Media Player, chiffrement dynamiques, licence remise, PlayReady, Widevine, FairPlay, Encryption(CENC) courantes, Multi-DRM, Axinom, tiret, EME, MSE, JSON Web jeton (JWT), revendications, navigateurs modernes, clé survol, clé symétrique, clé asymétriques, OpenID à vous connecter, X509 certificat. 

##<a name="in-this-article"></a>Dans cet article

Les rubriques suivantes sont décrites dans cet article :

- [Introduction](media-services-cenc-with-multidrm-access-control.md#introduction)
    - [Vue d’ensemble de cet article](media-services-cenc-with-multidrm-access-control.md#overview-of-this-article)
- [Une conception de référence](media-services-cenc-with-multidrm-access-control.md#a-reference-design)
- [Création de mappage pour la technologie pour l’implémentation](media-services-cenc-with-multidrm-access-control.md#mapping-design-to-technology-for-implementation)
- [Mise en œuvre](media-services-cenc-with-multidrm-access-control.md#implementation)
    - [Procédures d’implémentation](media-services-cenc-with-multidrm-access-control.md#implementation-procedures)
    - [Certains problèmes de mise en œuvre](media-services-cenc-with-multidrm-access-control.md#some-gotchas-in-implementation)
- [Autres rubriques pour l’implémentation](media-services-cenc-with-multidrm-access-control.md#additional-topics-for-implementation)
    - [HTTP ou HTTPS](media-services-cenc-with-multidrm-access-control.md#http-or-https)
    - [Azure Active Directory substitution de la clé de signature](media-services-cenc-with-multidrm-access-control.md#azure-active-directory-signing-key-rollover)
    - [Où se trouve le jeton d’accès ?](media-services-cenc-with-multidrm-access-control.md#where-is-the-access-token)
    - [Qu’en est-il Live diffusion en continu ?](media-services-cenc-with-multidrm-access-control.md#what-about-live-streaming)
    - [Qu’en est-il des serveurs de licences en dehors d’Azure Media Services ?](media-services-cenc-with-multidrm-access-control.md#what-about-license-servers-outside-of-azure-media-services)
    - [Que se passe-t-il si je veux utiliser un STS personnalisé ?](media-services-cenc-with-multidrm-access-control.md#what-if-i-want-to-use-a-custom-sts)
- [Le système terminée et le test](media-services-cenc-with-multidrm-access-control.md#the-completed-system-and-test)
    - [Connexion de l’utilisateur](media-services-cenc-with-multidrm-access-control.md#user-login)
    - [Utilisez des Extensions multimédia chiffré pour PlayReady](media-services-cenc-with-multidrm-access-control.md#using-encrypted-media-extensipons-for-playready)
    - [À l’aide de EME pour Widevine](media-services-cenc-with-multidrm-access-control.md#using-eme-for-widevine)
    - [Utilisateurs non autorisés](media-services-cenc-with-multidrm-access-control.md#not-entitled-users)
    - [En cours d’exécution personnalisé jeton Service Banque d’informations sécurisé](media-services-cenc-with-multidrm-access-control.md#running-custom-secure-token-service)
- [Résumé](media-services-cenc-with-multidrm-access-control.md#summary)

##<a name="introduction"></a>Introduction

Il est bien connu qu’il est une tâche complexe à concevoir et créer un sous-système DRM pour un OTT ou en ligne en continu solution. Et il est une pratique courante pour les fournisseurs de vidéo opérateurs/en ligne à confier cette partie aux fournisseurs de services spécialisés DRM. L’objectif de ce document est de présenter une conception de référence et l’application de sous-système DRM de bout en bout dans OTT ou solution en continu en ligne.

Les lecteurs ciblés de ce document sont ingénieurs travaillant dans sous-système DRM de OTT online solutions de diffusion en continu/multi-screen ou les lecteurs intéressés par sous-système DRM. Il est supposé que les lecteurs sont familiarisés avec au moins l’une des technologies DRM sur le marché, tels que PlayReady, Widevine, FairPlay ou Adobe Access.

Par DRM, nous avons également inclure CENC (chiffrement courantes) avec multi-DRM. Une tendance principale dans la diffusion en ligne et industrie OTT consiste à utiliser CENC avec multi-native-DRM sur plusieurs plateformes client, qui est un décalage à partir de la tendance précédent de l’utilisation d’un seul DRM et son client SDK pour différentes plateformes clientes. Lorsque vous utilisez CENC avec plusieurs native-DRM, PlayReady et Widevine sont chiffrés par la spécification de [Chiffrement courantes (CENC ISO/IEC 23001-7)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

Les avantages des CENC avec multi-DRM sont les suivantes :

1. Réduit le chiffrement dans la mesure où un traitement de chiffrement est utilisé un ciblage différentes plateformes avec son DRMs natives ;
1. Réduit le coût de la gestion des biens chiffrés dans la mesure où une seule copie du bien chiffré est requis ;
1. Élimine client DRM licences coût dans la mesure où le composant native client DRM est généralement gratuit sur sa plate-forme native.

Microsoft a été un promoteur active de tiret et CENC ainsi que certains lecteurs industrielles majeures. Microsoft Azure Media Services propose prise en charge de tiret et CENC. Pour les annonces récentes, voir blogs de Mingfei : [services de remise de licence annonce Google Widevine dans Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)et [Azure Media Services ajoute emballage Google Widevine permettant de flux de données à plusieurs DRM](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).  

### <a name="overview-of-this-article"></a>Vue d’ensemble de cet article

L’objectif de cet article inclut les éléments suivants :

1. Propose une conception de référence de sous-système DRM à l’aide de CENC avec multi-DRM ;
1. Fournit une implémentation de référence sur la plateforme Microsoft Azure/Azure Media Services ;
1. Décrit certaines rubriques de conception et implémentation.

Dans l’article, « multi-DRM » décrit les aspects suivants :

1. Microsoft PlayReady
1. Google Widevine
1. Apple FairPlay (non pris en charge par Azure Media Services)

Le tableau suivant résume l’application native plateforme/natif et navigateurs pris en charge par chaque DRM.

**Plate-forme client**|**Prise en charge native DRM**|**Navigateur/application**|**Formats de diffusion en continu**
----|------|----|----
**TV actives, opérateur décodeurs, décodeurs OTT**|PlayReady principalement, et/ou Widevine, et/ou l’autre|Linux, Opera, WebKit, autres|Différents formats
**Appareils Windows 10 (PC Windows, tablettes Windows, Windows Phone, Xbox)**|PlayReady|MS bord/IE11/EME<br/><br/><br/>UWP|TIRET (pour TLS, PlayReady n'est pas pris en charge)<br/><br/>TIRET, Smooth Streaming (pour TLS, PlayReady n'est pas pris en charge) 
**Appareils Android (téléphone, tablette, TV)**|Widevine|Chrome/EME|TIRET
**iOS (iPhone, iPad), les clients OS X et TV Apple**|FairPlay|Safari 8 + / EME|TLS
**Plug-in : Adobe emploi**|Emploi Access|Plug-in de navigateur|HDS, TLS

En tenant compte de l’état actuel du déploiement pour chaque DRM, un service souhaitez généralement implémenter DRMs 2 ou 3 pour vous assurer que les adresses de tous les types de points de terminaison de la meilleure manière.

Il existe un compromis entre la complexité de la logique du service et la complexité sur le côté client pour atteindre un certain niveau de l’expérience utilisateur sur les différents clients.

Pour effectuer votre sélection, n’oubliez pas ces faits :

- PlayReady est activée en mode natif dans chaque appareil Windows, sur certains appareils Android et disponible via kits de développement logiciel sur pratiquement n’importe quelle plate-forme
- Widevine est activée en mode natif dans chaque appareil Android, Chrome et certaines autres appareils
- FairPlay est disponible uniquement sur iOS et clients de Mac OS ou via iTunes.

Pour un multi-DRM typique serait le suivant :

- Option 1 : PlayReady et Widevine
- Option 2 : PlayReady, Widevine et FairPlay


## <a name="a-reference-design"></a>Une conception de référence

Dans cette section, nous présentera une conception de référence qui est indépendantes par rapport aux technologies utilisées pour mettre en œuvre.

Un sous-système DRM peut contenir les éléments suivants :

1. Gestion de clés
1. Emballage DRM
1. Remise de licence DRM
1. À cocher droit
1. Authentification/autorisation
1. Lecteur
1. Origine/CDN

Le diagramme suivant illustre l’interaction entre les composants d’un sous-système DRM haut niveau.

![Sous-système DRM avec CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)
 
Il existe trois base « couches » dans la conception :

1. Couche office précédent (en noir) qui ne sont pas exposées en externe ;
1. Calque « DMZ » (bleu) contenant tous les points de terminaison facing public ;
1. Couche de Internet public (bleu clair) contenant CDN et joueurs avec le trafic via Internet public.
 
Il doit être un outil de gestion de contenu pour contrôler la protection DRM, quel que soit le chiffrement statique ou dynamique. Les entrées pour le chiffrement DRM doivent inclure :

1. Contenu vidéo MBR ;
1. Clé de contenu ;
1. URL d’acquisition de licences.

Durant la lecture, le flux de haut niveau est la suivante :

1. Utilisateur authentifié ;
1. Jeton d’autorisation est créée pour l’utilisateur ;
1. Contenu protégé par DRM (manifeste) est téléchargé dans le lecteur ;
1. Lecteur envoie la demande d’acquisition de licence pour les serveurs de licences ainsi que la clé ID et d’autorisation jeton.

Avant de passer à la rubrique suivante, quelques mots sur la conception de gestion de clés.

**ContentKey-à-biens**|**Scénario**
------|---------------------------
1-à-1|Le cas le plus simple. Il offre un contrôle plus fin. Mais cela se traduit généralement par les frais de remise de licence plus élevés. Au minimum une licence demande est requise pour chaque ressource protégée.
1-à-plusieurs|Vous pouvez utiliser la même clé de contenu pour plusieurs actifs. Par exemple, pour toutes les ressources dans un groupe logique tel qu’un genre ou un sous-ensemble de genre (ou film GÈNE), vous pouvez utiliser une clé de contenu unique.
1-à-plusieurs|Plusieurs touches contenus sont nécessaires pour chaque ressource. <br/><br/>Par exemple, si vous devez appliquer protection CENC dynamique avec multi-DRM pour MPEG-tiret et le chiffrement AES 128 dynamique pour TLS, vous devez deux clés de contenu distinctes, chacune avec sa propre ContentKeyType. (Pour la clé de contenu utilisée pour la protection CENC dynamique, ContentKeyType.CommonEncryption doit être utilisé, tandis que pour la clé de contenu utilisée pour le chiffrement AES 128 dynamique, ContentKeyType.EnvelopeEncryption doit être utilisée.)<br/><br/>Un autre exemple, dans la protection des CENC de contenu tiret, dans les principes, une clé de contenu peut être utilisée pour protéger des flux vidéo et une autre clé de contenu pour protéger les flux audio. 
Plusieurs-à - plusieurs|Combinaison des deux scénarios ci-dessus : un ensemble de clés de contenus sont utilisées pour chacun de plusieurs actifs dans la même actif « groupe ».


Un autre facteur important à prendre en considération est l’utilisation de licences permanentes et non permanente.

Pourquoi les considérations suivantes sont importants ? 

Ils disposent d’impact direct au coût de remise de licence si vous utilisez cloud public pour la remise de licence. Prenons les deux cas suivants de conception différente pour illustrer :



1. Abonnement mensuel : utiliser la licence permanente et mappage clé à bien contenu 1-à-plusieurs. Par exemple pour les films enfants, nous utilisons une clé de contenu unique pour le chiffrement. Dans ce cas : 

    Nombre total de licences # demandées pour tous les enfants films/appareil = 1

1. Abonnement mensuel : utilisez licence non permanent et le mappage 1-à-1 entre les biens et clé de contenu. Dans ce cas :

    Nombre total de licences # demandées pour tous les enfants films/périphérique = [films # observés] x [sessions #]

Comme vous pouvez le voir, les différentes deux conceptions entraînent dans les modèles de demande de licence très différente, par conséquent, remise licence coût si le service de remise de licence est fourni par un nuage public comme Azure Media Services.

## <a name="mapping-design-to-technology-for-implementation"></a>Création de mappage pour la technologie pour l’implémentation

Ensuite, nous allons associer notre conception générique aux technologies sur plate-forme Microsoft Azure/Azure Media Services, en spécifiant la technologie à utiliser pour chaque bloc de construction.

Le tableau suivant montre le mappage :

**Bloc de construction**|**Technologie**
------|-------
**Lecteur**|[Lecteur multimédia Azure](https://azure.microsoft.com/services/media-services/media-player/)
**Fournisseur d’identité (IDP)**|Azure Active Directory
**Service de jeton de sécurité (STS)**|Azure Active Directory
**Flux de travail de Protection DRM**|Azure Media Services Protection dynamique
**Remise de licence DRM**|1. azure Media Services licence remise (PlayReady, Widevine, FairPlay), ou <br/>2. serveur de licences Axinom <br/>3. serveur de licences PlayReady personnalisé
**Origine**|Azure Media Services point de terminaison de diffusion en continu
**Gestion de clés**|Pas nécessaire pour l’implémentation de référence
**Gestion de contenu**|Une application console c#

En d’autres termes, fournisseur d’identité (IDP) et Service (jeton de sécurité) sera Azure AD. Pour le lecteur, nous allons utiliser [API Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). Azure Media Services et Azure Media Player prennent en charge tiret et CENC avec multi-DRM.

Le diagramme suivant illustre la structure globale et du flux avec le mappage de technologie ci-dessus.

![CENC sur AMS](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Afin de configurer le chiffrement CENC dynamique, l’outil de gestion de contenu utilisera les entrées suivantes :

1. Contenu ouvert ;
1. Clé de contenu à partir de clé génération/gestion ;
1. URL d’acquisition de licence ;
1. Liste des informations à partir d’Azure AD.

Le résultat de l’outil de gestion de contenu sera :

1. ContentKeyAuthorizationPolicy contenant la spécification de la remise de licence vérifie un jeton JWT et spécifications de licence DRM ;
1. AssetDeliveryPolicy contenant les spécifications en continu format, protection DRM et URL d’acquisition de licences.

Lors de l’exécution, le flux est comme ci-dessous :

1. Lors de l’authentification des utilisateurs, un jeton JWT est généré ;
1. Parmi les affirmations contenues dans le jeton JWT est réclamer « groupes » qui contient l’ID d’objet groupe de « EntitledUserGroup ». Cette demande sera utilisée pour le passage « à cocher droit ».
1. Le lecteur téléchargements client manifeste d’un CENC du contenu protégé et « », le message suivant :
    1. ID, de clé 
    1. le contenu est CENC protégé,
    1. URL d’acquisition de licences.

1. Le lecteur effectue une demande d’acquisition de licence basée sur le navigateur/DRM pris en charge. ID de clé dans la demande d’acquisition de licence, et le jeton JWT est également envoyé. Service de remise de licence vérifie le jeton JWT et les revendications contenues avant de lancer la licence nécessaire.

##<a name="implementation"></a>Mise en œuvre

###<a name="implementation-procedures"></a>Procédures d’implémentation

La mise en œuvre inclut les étapes suivantes :

1. Préparer le test actifs : encoder/package une vidéo de test pour multi-débit fragmenté MP4 dans Azure Media Services. Cet actif n’est pas protégé par DRM. Protection DRM est effectuée par la protection dynamique ultérieurement.
1. Créer des clés ID et un contenu clé (éventuellement à partir de la valeur de départ clés). Dans notre cas, système de gestion de clés n’est pas nécessaire étant donné que nous avons affaire à un seul ensemble de clés ID et clé de contenu pour plusieurs éléments de test ;
1. Utiliser AMS API pour configurer les services de remise de licence multi-DRM pour les biens de test. Si vous utilisez les serveurs de licences personnalisé par votre société ou de fournisseurs de votre société à la place de services de licence dans Azure Media Services, vous pouvez ignorer cette étape et spécifiez URL d’acquisition de licences à l’étape de configuration de la remise de licence. API AMS est nécessaire pour spécifier détaillé configurations, telles que la restriction de la stratégie d’autorisation, les modèles de réponse de licence pour différents services licence DRM, etc.. Pour le moment, le portail Azure ne fournit pas encore l’interface utilisateur nécessaire pour cette configuration. Vous pouvez trouver des informations du niveau de l’API et exemples de code document de Julia Kornich : [à l’aide de PlayReady et/ou chiffrement courantes dynamiques Widevine](media-services-protect-with-drm.md). 
1. Utilisez AMS API pour configurer la stratégie de remise de biens pour les biens de test. Vous pouvez trouver des informations du niveau de l’API et exemples de code document de Julia Kornich : [à l’aide de PlayReady et/ou chiffrement courantes dynamiques Widevine](media-services-protect-with-drm.md).
1. Créer et configurer un client Azure Active Directory dans Azure ;
1. Créer plusieurs comptes d’utilisateurs et groupes dans votre client Azure Active Directory : vous devez créer au moins « EntitledUser » de groupe et ajouter un utilisateur à ce groupe. Utilisateurs de ce groupe passera à cocher droit dans l’acquisition de licences et les utilisateurs non dans ce groupe ne pourra pas passer vérification de l’authentification et ne seront pas en mesure d’acquérir une licence. Être membre de ce groupe « EntitledUser » est un réclamer requis « groupes » dans le jeton JWT émis par Azure AD. Cette exigence réclamer doit être indiquée dans configurez multi-DRM licence remise services étape.
1. Créer une application ASP.NET MVC qui organise votre lecteur vidéo. Cette application ASP.NET sera protégée avec l’authentification des utilisateurs contre le client Azure Active Directory. Des déclarations appropriées seront être incluses dans les jetons d’accès obtenus après l’authentification des utilisateurs. API de se connecter OpenID est recommandée pour cette étape. Vous devez installer les packages NuGet suivants :
    - Package d’installation Microsoft.Azure.ActiveDirectory.GraphClient
    - Package d’installation Microsoft.Owin.Security.OpenIdConnect
    - Package d’installation Microsoft.Owin.Security.Cookies
    - Package d’installation Microsoft.Owin.Host.SystemWeb
    - Package d’installation Microsoft.IdentityModel.Clients.ActiveDirectory
1. Créer un lecteur à l’aide de [L’API de Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). [Azure le lecteur ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/) permet de vous permettent de spécifier quelle est la technologie DRM à utiliser sur plate-forme DRM différente.
1. Matrice de test :

**DRM**|**Navigateur**|**Résultat de l’utilisateur autorisé**|**Résultat de l’utilisateur non autorisé**
---|---|-----|---------
**PlayReady**|MS bord ou IE11 sur Windows 10|Réussir|Fail
**Widevine**|Chrome sur Windows 10|Réussir|Fail
**FairPlay** |À DÉFINIR||

Georges Trifonov d’Azure Media Services équipe a écrit un blog fournissant des étapes détaillées pour la configuration d’Azure Active Directory pour une application de lecteur ASP.NET MVC : [intégrer Azure Media Services OWIN MVC en fonction de l’application avec Azure Active Directory et de limiter la remise de clés contenue basée sur les revendications JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Georges a également écrit un blog sur [l’authentification des jetons JWT dans Azure Media Services et le chiffrement dynamique](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/). Et Voici son [exemple sur l’intégration Azure AD avec remise clée Azure Media Services](https://github.com/AzureMediaServicesSamples/Key-delivery-with-AAD-integration/).

Pour plus d’informations sur Azure Active Directory :

- Vous trouverez des informations pour les développeurs dans [Azure Active Directory Guide développeur](../active-directory/active-directory-developers-guide.md).
- Vous trouverez des informations destinées aux administrateurs dans [Administrer votre Azure AD Directory](../active-directory/active-directory-administer.md).

### <a name="some-gotchas-in-implementation"></a>Certains problèmes de mise en œuvre

Il existe certaines « pièges » dans la mise en œuvre. Nous espérons que la liste suivante de « pièges » peut vous aider dépannage au cas où vous rencontrez des problèmes.

1. **Émetteur** URL doit se terminer par **« / »**.  

    **Audience** doit être l’ID client de l’application lecteur et vous devez également ajouter **« / »** à la fin de l’URL de l’émetteur.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" /> 

    Dans le [Filtre JWT](http://jwt.calebb.net/), vous devriez voir **aud** et **iss** comme en dessous du jeton JWT :
    
    ![1er piège n](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)


2. Ajouter des autorisations à l’application en AAD (sous l’onglet Configuration de l’application). Cela est nécessaire pour chaque application (versions locale et déployées).

    ![2e piège n](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)


3. Utilisez l’émetteur droite dans la configuration de la protection CENC dynamique :

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
    
    Les éléments suivants ne fonctionneront pas :
    
        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
    
    Le GUID est l’ID de client AAD. GUID sont accessibles dans le menu contextuel points de terminaison portail Azure.

4. Accorder l’appartenance aux groupes de revendications privilèges. Vérifiez que de fichier manifeste d’application AAD, nous disposer des éléments suivants

    « groupMembershipClaims » : « Tout », (la valeur par défaut est null)

5. Paramètre NOMPROPRE TokenType lors de la création des exigences de restriction.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Depuis l’ajout de prise en charge de JWT (DAS) en plus de SWT (ACS), la valeur par défaut TokenType est TokenType.JWT. Si vous utilisez SWT/ACS, vous devez définir sur TokenType.SWT.

## <a name="additional-topics-for-implementation"></a>Autres rubriques pour l’implémentation
Nous sera ensuite disque uss certaines rubriques supplémentaires dans notre conception et implémentation.

###<a name="http-or-https"></a>HTTP ou HTTPS ?

L’application lecteur ASP.NET MVC que nous avons créé doit prendre en charge les éléments suivants :

1. Authentification des utilisateurs via Azure AD qui doit être sous HTTPS ;
1. Exchange jeton JWT entre le client et Azure AD qui doit être sous HTTPS ;
1. Acquisition de licence DRM par le client qui ne doit pas être sous HTTPS si remise licence est fournie par Azure Media Services. Bien entendu, suite de produits PlayReady ne force pas HTTPS pour la remise de licence. Si votre serveur de licences PlayReady est en dehors d’Azure Media Services, HTTP ou HTTPS peut être utilisé.

Par conséquent, l’application lecteur ASP.NET utilisera HTTPS comme une meilleure pratique. Cela signifie que le lecteur multimédia Azure apparaîtra sur une page sous HTTPS. Toutefois, pour la diffusion en continu nous préférez HTTP, par conséquent, nous avons besoin de prendre en considération les problèmes liés au contenu mixte.

1. Navigateur ne permet pas de contenu mixte. Mais autoriser les plug-ins tels que les plug-in Silverlight et OSMF pour passer et tiret. Contenu mixte est un problème de sécurité : il s’agit en raison de la menace de la capacité à injection JS malveillants qui peuvent entraîner les données client exposés.  Navigateurs bloquent ce par défaut et la seule façon de travailler autour de celle-ci est sur le côté serveur (origin), pour permettre à tous les domaines (utilisiez https ou http). Il s’agit probablement pas une bonne idée soit.
1. Nous devons éviter contenu mixte : les deux utilisent HTTP ou les deux utilisent HTTPS. Lors de la lecture du contenu mixte, tech silverlightSS nécessite effacement d’un message d’avertissement contenu mixte. garantie flashSS gère le contenu mixte sans avertissement sur le contenu mixte.
1. Si votre point de terminaison de diffusion en continu a été créée avant d’août 2014, il ne prend pas en charge HTTPS. Dans ce cas, créer et utiliser un point de terminaison diffusion en continu pour HTTPS.

Dans l’implémentation de référence, pour le contenu protégé par DRM, applications et la diffusion en continu seront trouvent sous HTTTPS. Pour ouvrir contenu, le lecteur n’a pas besoin authentification ou la licence, pour avoir la liberté HTTP ou HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory substitution de la clé de signature

Il s’agit d’un point important de tenir compte de votre implémentation. Si vous ne le considérez pas dans votre implémentation, le système terminé ne fonctionnera finalement plus complètement au sein de six semaines au maximum.

Azure AD utilise norme pour établir l’approbation entre lui-même et les applications à l’aide d’Azure AD. Plus précisément, Azure AD utilise une clé de signature est constitué par une paire de clés publique et privée. Lorsque Azure AD crée un jeton de sécurité qui contient des informations sur l’utilisateur, ce jeton est signé par Azure AD à l’aide de sa clé privée avant d’être envoyé à l’application. Pour vérifier que le jeton est valide et réellement origines à partir d’Azure AD, l’application doit valider signature du jeton à l’aide de la clé publique exposée par Azure AD se trouvant dans le document de métadonnées de fédération du client. Cette clé publique – et la clé de signature dont il est dérivé – sont identique à celle utilisée pour toutes les installations dans Azure Active Directory.

Vous trouverez des informations détaillées sur substitution de la clé Azure AD dans le document : [Des informations importantes sur signature clé survol dans Azure Active Directory](../active-directory/active-directory-signing-key-rollover.md).

Entre la [paire clé publique-privée](https://login.windows.net/common/discovery/keys/), 

- La clé privée est utilisée par Azure Active Directory pour générer un jeton JWT ;
- La clé publique est utilisée par une application telle que les Services de remise de licence DRM dans AMS pour vérifier le jeton JWT ;
 
À des fins de sécurité, Azure Active Directory fait pivoter ce certificat régulièrement (toutes les semaines 6). En cas de violations de sécurité, le survol clé peut survenir chaque fois. Par conséquent, les services de livraison licence dans AMS doivent mettre à jour la clé publique utilisée comme Azure AD fait pivoter la paire de clés, dans le cas contraire l’authentification des jetons dans AMS échouera et aucune licence ne sera émise. 

Pour cela en définissant TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument lors de la configuration des services de livraison licence DRM.

Le flux de jetons JWT est comme ci-dessous :

1.  Azure AD émet le jeton JWT avec la clé privée en cours pour un utilisateur authentifié ;
2.  Lorsqu’un lecteur détecte un CENC avec du contenu protégé multi-DRM, il d’abord localiser le jeton JWT émis par Azure AD.
3.  Le lecteur envoie demande d’acquisition de licences avec le jeton JWT aux services de livraison licence dans AMS ;
4.  Les services de livraison licence dans AMS va utiliser la clé publique en cours/valides à partir d’Azure AD pour vérifier le jeton JWT, avant de délivrer des licences.

Services de livraison licence DRM recherchant toujours les la clé publique en cours/valides à partir d’Azure AD. La clé publique présentée par Azure AD sera la clé utilisée pour vérifier un jeton JWT émis par Azure AD.

Que se passe-t-il si le survol clé se passe-t-il une fois AAD génère un jeton JWT mais avant le JWT jeton est envoyé par les joueurs aux services de livraison licence DRM dans AMS pour la vérification ? 

Étant donné qu’une clé peut-être être appliquée à tout moment, il est toujours plus d’une clé publique valide disponibles dans le document de métadonnées de fédération. Remise de licence Azure Media Services pouvez utiliser les touches spécifiées dans le document, dans la mesure où une clé peut-être être appliquée plus tôt, une autre peut être son remplacement et ainsi de suite.

### <a name="where-is-the-access-token"></a>Où se trouve le jeton d’accès ?

Si vous observez comment une application web appelle une application API sous [Identité de l’Application avec autorisation d’informations d’identification OAuth 2.0 Client](active-directory-authentication-scenarios.md#web-application-to-web-api), le flux d’authentification est comme ci-dessous :

1.  Un utilisateur est connecté à Azure AD dans l’application web (voir le [Navigateur Web pour une Application Web](active-directory-authentication-scenarios.md#web-browser-to-web-application).
2.  Le point de terminaison d’autorisation Azure AD redirige l’agent utilisateur vers l’application cliente avec un code d’autorisation. L’agent utilisateur renvoie le code d’autorisation à l’URI de redirection de l’application cliente.
3.  L’application web doit acquérir un jeton d’accès afin qu’il puisse s’authentifier à l’API web et récupérer la ressource souhaitée. Elle effectue une requête au point de terminaison jeton de Azure AD, fournir les informations d’identification, l’ID de client et application web l’API ID URI. Il présente le code d’autorisation pour prouver que l’utilisateur a accepté.
4.  Azure AD authentifie l’application et renvoie un jeton d’accès JWT qui permet d’appeler l’API web.
5.  Sur HTTPS, l’application web utilise le jeton d’accès JWT renvoyé pour ajouter la chaîne JWT avec une désignation « Porteur » dans l’en-tête d’autorisation de la requête sur le web API. L’API web puis valide le jeton JWT et si la validation est réussie, retourne la ressource souhaitée.

Dans ce flux « identité de l’application », l’API web suppose que l’application web authentifié l’utilisateur. Pour cette raison, ce modèle est appelé un sous système approuvé. Le [diagramme de cette page](http://msdn.microsoft.com/library/azure/dn645542.aspx/) décrit comment code d’autorisation accorder flux fonctionne.

Dans l’acquisition de licences avec restriction jeton, nous allons suivant le même modèle de sous système approuvé. Et le service de remise de licence dans Azure Media Services est le web API ressource, le » et « une application web a besoin pour accéder à. Où est donc le jeton d’accès ?

En effet, nous allons obtention jeton d’accès à partir d’Azure AD. Une fois l’authentification réussie d’un utilisateur, code d’autorisation est renvoyée. Le code d’autorisation sert ensuite, ainsi que de clé ID et application client, à exchange pour jeton d’accès. Et le jeton d’accès pour accéder à une application « pointeur » en pointant ou représentant le service de remise de licence Azure Media Services.

Nous avons besoin inscrire et configurer l’application « pointeur » dans Azure AD en suivant les étapes ci-dessous :

1.  Dans le client Azure AD

    - Ajouter une application (ressource) avec l’authentification URL : 

    https://[resource_name].azurewebsites.NET/ et 

    - URL de l’ID de l’application : 
    
    https://[aad_tenant_name].onmicrosoft.com/[resource_name] ; 
2.  Ajouter une nouvelle clé pour l’application de la ressource ;
3.  Mettre à jour le fichier manifeste d’application afin que la propriété groupMembershipClaims a la valeur suivante : « groupMembershipClaims » : « Tout »  
4.  Dans l’application Azure AD en pointant sur l’application web du lecteur, dans la section « autorisations à d’autres applications », ajoutez l’application de ressource qui a été ajoutée à l’étape 1 ci-dessus. Sous « délégué des autorisations » Cochez coche « Access [nom_ressource] ». Cela vous donne l’autorisation de l’application web pour créer des jetons d’accès pour accéder à l’application de la ressource. Vous devez faire ceci pour version locale et déployée de l’application web si vous développez avec Visual Studio et Azure web app.
    
Par conséquent, le jeton JWT émis par Azure AD est bien le jeton d’accès pour accéder à cette ressource « pointeur ».

### <a name="what-about-live-streaming"></a>Qu’en est-il Live diffusion en continu ?

Dans l’exemple ci-dessus, notre discussion a été concentration sur actifs à la demande. Qu’en est-il live diffusion en continu ?

La bonne nouvelle est que vous pouvez utiliser exactement le même conception et implémentation de protection de diffusion en continu live dans Azure Media Services, en traitant de la ressource associée à un programme en tant que « VOD actif ».

En particulier, il est connu que pour faire live diffusion en continu dans Azure Media Services, vous devez créer un canal, puis un programme sous le canal. Pour créer le programme, vous devez créer un bien qui contiendra une archive en ligne pour le programme. Pour fournir CENC bénéficiant d’une protection à plusieurs DRM du contenu actif, tous les que vous devez effectuer, consiste à appliquer la même mise en/traitement de la ressource comme s’il s’agissait d’une « biens VOD » avant de démarrer le programme.

### <a name="what-about-license-servers-outside-of-azure-media-services"></a>Qu’en est-il des serveurs de licences en dehors d’Azure Media Services ?

Souvent, les clients peuvent ont investi dans la batterie de serveurs licence soit dans leurs propres données, Centrer ou hébergé par les fournisseurs de service DRM. Peut être effectué en, Protection du contenu Azure Media Services vous permet de fonctionner en mode hybride : contenu hébergé et protégé dynamiquement dans Azure Media Services, tandis que les licences DRM sont remis par les serveurs en dehors d’Azure Media Services. Il existe dans ce cas, les considérations suivantes de modifications :

1. Le Service Banque d’informations sécurisé de jeton doit émettre des jetons qui sont acceptables et peuvent être vérifiées par la batterie de serveurs de licence. Par exemple, les serveurs de licence Widevine fournis par Axinom nécessite un jeton JWT spécifique qui contient « message droit ». Par conséquent, vous devez avoir un STS à émission du jeton de ces JWT. Les auteurs ont terminé une telle implémentation et que vous pouvez consulter les détails dans le document suivant dans le [Centre de Documentation Azure](https://azure.microsoft.com/documentation/): [Axinom à l’aide de délivrer des licences Widevine Azure Media Services](media-services-axinom-integration.md). 
1. Vous n’avez plus besoin configurer le service de remise de licence (ContentKeyAuthorizationPolicy) dans Azure Media Services. Ce que vous devez faire consiste à fournir des URL d’acquisition de la licence (pour PlayReady, Widevine et FairPlay) lorsque vous configurez AssetDeliveryPolicy dans la configuration CENC avec multi-DRM.
 
### <a name="what-if-i-want-to-use-a-custom-sts"></a>Que se passe-t-il si je veux utiliser un STS personnalisé ?

Vous pouvez rencontrer quelques raisons pour lesquelles un client peut choisir d’utiliser un STS personnalisé (Secure jeton Service) pour fournir des jetons JWT. Certaines d'entre elles sont :

1.  Le fournisseur identité (IDP) utilisé par le client ne prend pas en charge STS. Dans ce cas un STS personnalisée peut être une option.
2.  Le client devra peut-être un contrôle plus flexible ou plus étroit dans l’intégration STS avec abonné du client système de facturation. Par exemple, un opérateur MVPD peut-être proposer plusieurs packages d’abonné OTT comme premium, base, sports, etc.. L’opérateur souhaitiez faire correspondre les revendications dans un jeton de package de l’abonné afin que seul le contenu dans le package de droite est mis à disposition. Dans ce cas, un STS personnalisé fournit la souplesse nécessaire et le contrôle.

Deux modifications doivent être effectuées lors de l’utilisation d’un STS personnalisé :

1.  Lorsque vous configurez le service de remise de licence pour un bien, vous devez spécifier la clé de sécurité utilisée pour la vérification par le STS personnalisé (plus de détails ci-dessous) au lieu de la clé actuelle à partir d’Azure Active Directory.
2.  Lorsqu’un jeton JTW est généré, une clé de sécurité est spécifiée à la place de la clé privée de la X509 actuel certificat dans Azure Active Directory.

Il existe deux types de clés de sécurité :

1.  Clé symétrique : la même clé est utilisée pour générer et vérifier un jeton JWT ;
2.  Clé asymétrique : une paire clée publique-privée dans un X509 certificat est utilisé avec une clé privée pour le chiffrement/générer un jeton JWT et la clé publique pour vérifier le jeton.

####<a name="tech-note"></a>Note technique

Si vous utilisez .NET Framework / c# comme votre plateforme de développement, le X509 certificat utilisé pour la clé de sécurité asymétriques doit avoir au moins 2048 la longueur de clé. Il s’agit d’une exigence de la classe System.IdentityModel.Tokens.X509AsymmetricSecurityKey dans .NET Framework. Dans le cas contraire, l’exception suivante sera levée :

IDX10630 : « System.IdentityModel.Tokens.X509AsymmetricSecurityKey » pour la signature ne peut pas être inférieure à bits « 2048 ». 

## <a name="the-completed-system-and-test"></a>Le système terminée et le test

Nous allons découvrir quelques scénarios dans le système de bout en bout complétée afin que les lecteurs peuvent avoir un basic « image » du comportement avant d’obtenir un compte de connexion.

L’application web du lecteur et sa connexion sont accessibles [ici](https://openidconnectweb.azurewebsites.net/).

Si vous devez est « non intégrée » scénario : ressources vidéo hébergés dans Azure Media Services qui sont protégés ou protégé DRM mais sans jeton d’authentification (émission une licence à quiconque demandant), vous pouvez tester sans connexion (en optant pour HTTP si votre vidéo en temps réel est sur HTTP).

Si vous avez besoin est scénario intégré de bout en bout : ressources vidéo se trouve sous protection DRM dynamique dans Azure Media Services, avec l’authentification des jetons et jeton JWT générées par Azure AD, vous devez vous connecter.

### <a name="user-login"></a>Connexion de l’utilisateur

Afin de tester le système DRM intégré de bout en bout, vous devez avoir un compte » « créé ou ajouté. 

Quel compte ?

Mais Azure autorise a accès uniquement aux utilisateurs de compte Microsoft, il maintenant autorise l’accès par les utilisateurs des deux systèmes. Ceci permet d’en demandant tous les l’approbation de propriétés Azure Azure AD pour l’authentification, ayant Azure AD authentifier les utilisateurs d’organisation et en créant une relation de fédération dans laquelle Azure AD approuve le système Microsoft compte consommateur identité pour authentifier les utilisateurs grand public. Par conséquent, Azure AD est pas s’authentifier comptes Microsoft « invités » ainsi que les comptes Azure AD « natives ».

Dans la mesure où Azure AD approuve le domaine de compte Microsoft (MSA), vous pouvez ajouter tous les comptes à partir d’un des domaines suivants personnalisé Azure Active Directory du client et utilisent le compte pour vous connecter :

**Nom de domaine**|**Domaine**
-----------|----------
**Domaine de client personnalisé Azure AD**|somename.onmicrosoft.com
**Domaine d’entreprise**|Microsoft.com
**Domaine de compte MSA (Microsoft)**|Outlook.com, live.com, hotmail.com

Vous pouvez contacter un des auteurs avoir un compte créé ou ajouté à votre place. 

Voici les captures d’écran de connexion différentes pages utilisées par différents comptes de domaine.

**Azure personnalisé compte de domaine Active Directory client**: dans ce cas, vous voyez la page de connexion personnalisée personnalisés domaine de client Azure AD.

![Compte de domaine personnalisé Azure AD client](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Compte de domaine Microsoft avec une carte à puce**: dans ce cas, vous voyez la page de connexion utilisée par Microsoft d’entreprise informatique avec authentification à deux facteurs.

![Compte de domaine personnalisé Azure AD client](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Compte Microsoft (MSA)**: dans ce cas, vous voyez la page de connexion de Microsoft Account pour les consommateurs.

![Compte de domaine personnalisé Azure AD client](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="using-encrypted-media-extensions-for-playready"></a>Utilisez des Extensions multimédia chiffré pour PlayReady

Sur un navigateur moderne avec les Extensions de média chiffrées (EME) pour la prise en charge PlayReady, tels qu’Internet Explorer 11 sur Windows 8.1 et vers le haut et navigateur Microsoft Edge sur Windows 10, PlayReady sera la DRM sous-jacente pour EME.

![À l’aide de EME pour PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

La zone du lecteur foncé est dû au fait que la protection PlayReady empêche une d’apporter des captures d’écran de la vidéo protégée. 

L’écran suivant montre les plug-ins du lecteur et le support MSE/EME.

![À l’aide de EME pour PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME dans Microsoft Edge et 11 Internet Explorer sur Windows 10 permet à l’appel de [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) sur les appareils Windows 10 qui prennent en charge. PlayReady SL3000 déverrouiller le flux de contenu premium amélioré (4K, en-tête, etc.) et de nouveaux modèles de distribution de contenu (fenêtre au plus tôt pour un contenu optimisé).

Se concentrer sur les appareils Windows : PlayReady est le seul DRM dans le matériel disponible sur les appareils Windows (PlayReady SL3000). Un service de diffusion en continu peut utiliser PlayReady via EME ou une application UWP et offre une meilleure qualité vidéo à l’aide de PlayReady SL3000 à un autre DRM. En règle générale, 2K contenu flux au sein de Chrome ou de Firefox et de contenu de 4K via Microsoft Edge/IE11 ou une application UWP sur le même appareil (selon les paramètres du service et mise en œuvre).


#### <a name="using-eme-for-widevine"></a>À l’aide de EME pour Widevine

Dans un navigateur moderne EME/Widevine prend en charge, tels que Chrome 41 + sur Windows 10, Windows 8.1, Mac OSX Yosemite et Chrome sur Android 4.4.4, Google Widevine est l’application DRM derrière EME.

![À l’aide de EME pour Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Notez que Widevine n’empêche pas une d’apporter des captures d’écran de la vidéo protégée.

![À l’aide de EME pour Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="not-entitled-users"></a>Utilisateurs non autorisés

Si un utilisateur n’est pas membre du groupe « Utilisateurs le droit », l’utilisateur ne sera pas en mesure de passer « à cocher droit » et le service de licence multi-DRM refuse d’émission la licence requise, comme illustré ci-dessous. La description détaillée est « acquérir des licences a échoué », qui est comme prévu.

![Utilisateurs non autorisés](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="running-custom-secure-token-service"></a>En cours d’exécution personnalisé jeton Service Banque d’informations sécurisé

Pour le scénario de l’exécution de personnalisé Service (jeton de sécurité), le jeton JWT sera émis par le STS personnalisé à l’aide de la clé symétrique ou asymétrique. 

Le cas d’utilisation de clé symétrique (à l’aide de Chrome) :

![STS personnalisés en cours d’exécution](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Le cas d’utilisation d’une clé asymétrique via un X509 de certificats (à l’aide du navigateur moderne Microsoft).

![STS personnalisés en cours d’exécution](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

Dans les deux cas ci-dessus, l’authentification des utilisateurs est le même – à Azure AD. La seule différence est que jetons JWT émis par le STS personnalisé au lieu d’Azure AD. Bien entendu, lorsque vous configurez protection CENC dynamique, la restriction de service de remise de licence spécifie le type de jeton JWT, clé symétrique ou asymétrique.

## <a name="summary"></a>Résumé

Dans ce document, nous l’avons vu CENC avec plusieurs native-DRM et contrôle d’accès via l’authentification des jetons : sa conception et sa mise en œuvre à l’aide d’Azure, Azure Media Services et Azure Media Player.

- Une conception de référence est présentée qui contient tous les composants nécessaires dans un sous-système DRM/CENC ;
- Implémentation de référence sur Azure, Azure Media Services et Azure Media Player.
- Vous trouverez également certaines rubriques directement impliqués dans la conception et implémentation.


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###<a name="acknowledgments"></a>Accusés de réception 

William Zhang, Mingfei Yan, fourni Le Franc, COLON Kilroy, Ariane Kornich
