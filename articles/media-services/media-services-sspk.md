<properties 
    pageTitle="Gestion des licences Microsoft® lisse diffusion en continu Client porter Kit" 
    description="Découvrez comment Microsoft® lisse Streaming Client porter Kit de licence." 
    services="media-services" 
    documentationCenter="" 
    authors="xpouyat,vsood" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/06/2016"  
    ms.author="xpouyat"/>

#<a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Gestion des licences Microsoft® lisse diffusion en continu Client porter Kit

##<a name="overview"></a>Vue d’ensemble

Microsoft lisse Streaming porter Kit Client (**SSPK** en abrégé) est une implémentation client Smooth Streaming est optimisée pour vous aider fabricants incorporé, câble et les opérateurs mobiles, fournisseurs de services de contenu, fabricants combiné, éditeurs de logiciels (éditeurs de logiciels) et les fournisseurs de solutions pour créer des produits et services pour la diffusion en continu de contenu ADAPTATIF Smooth Streaming Format. SSPK est un appareil plateforme indépendamment mise en œuvre et du client Smooth Streaming qui peut être porté par la licence à n’importe quel appareil et la plateforme. 

Inclus sous est une architecture de haut niveau et zone douceur Streaming porter Kit IIS est l’implémentation du Client de diffusion en continu lisse fournie par Microsoft et inclut toute la logique de base pour la lecture du contenu Smooth Streaming. Ceci est puis porté par des partenaires d’un périphérique spécifique ou plate-forme en mettant en œuvre des interfaces appropriées. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

##<a name="description"></a>Description

SSPK est sous licence sur les termes qui offrent une valeur professionnelles optimales. Licence SSPK offre au secteur de :

- Source Kit de porter diffusion en continu lisse en C++ 
  - mettent en œuvre, une fonctionnalité de Client de diffusion en continu lisse
  - Ajoute le format de l’analyse, heuristique, mise en mémoire tampon logique, etc..
- API d’application du lecteur 
  - interfaces de programmation pour l’interaction avec une application de lecteur multimédia
- Interface de plateforme Abstraction calque (PAL) 
  - interfaces de programmation pour l’interaction avec le système d’exploitation (threads, sockets)
- Interface de couche d’Abstraction matérielle 
  - interfaces pour l’interaction avec du matériel A de programmation / décodeurs V (décodage, rendu)
- Droits numériques (DRM) gestion Interface 
  - interfaces de programmation pour la gestion des DRM via la DRM Abstraction couche
  - Microsoft PlayReady porter Kit est fourni séparément mais intègre via cette interface. Pour plus d’informations sur les licences Microsoft PlayReady Device, cliquez [ici](http://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
- Exemples d’implémentation 
  - exemple d’implémentation PAL pour Linux
  - exemple d’implémentation HAL pour GStreamer

##<a name="licensing-options"></a>Options de licence

Microsoft lisse Streaming porter Kit Client est mis à disposition détenteurs sous deux contrats de licence distincte : une pour le développement de Client de diffusion en continu lisse provisoire des produits et une autre pour la distribution lisse Streaming Client Final produits aux utilisateurs finaux.
 
- Pour puce fabricants, intégrateurs ou éditeurs de logiciels indépendants (éditeurs de logiciels) qui ont besoin d’un code source porter kit pour développer des produits de version préliminaire, Microsoft lisse Streaming porter Kit Client **Licence du produit intermédiaire** doit être exécutée.
- Pour les fabricants ou les éditeurs de logiciels nécessitant des droits de distribution pour lisse Streaming Final produits Client aux utilisateurs finaux, la douceur Streaming Client porter Kit Microsoft **Licence du produit Final** doit être exécutée.

###<a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Smooth Streaming Client porter licence du produit intermédiaire Kit est Microsoft

Sous cette licence Microsoft propose un bon Streaming porter Kit de Client et les droits de propriété intellectuelle nécessaire pour développer et distribuer le Client de diffusion en continu lisse provisoire des produits à d’autres bénéficiaires appareil lisse Streaming porter Kit Client que distribuez lisse Streaming Client Final produits.

####<a name="fee-structure"></a>Structure de frais

Frais de licence de 50 000 dollars US permet d’accéder au bon Streaming Client porter Kit. 

###<a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Smooth Streaming Client porter licence du produit Final Kit est Microsoft

Sous cette licence Microsoft offre tous les droits de propriété intellectuelle nécessaire pour recevoir le Client de diffusion en continu lisse provisoire des produits à partir d’autres bénéficiaires lisse Streaming porter Kit Client et distribuer société mène lisse Streaming Client Final produits aux utilisateurs finaux.

####<a name="fee-structure"></a>Structure de frais

Le bon produit Final Client de diffusion en continu est proposé sous un modèle de droits d’auteur en tant que sous :

- 0,10 $ par mise en œuvre appareil expédiée
- Les droits d’auteur est limitée à 50 000 $ par an
- Aucune royalty pour 10 000 premières mises en œuvre appareil chaque année 

##<a name="licensing-procedure-and-sspk-access"></a>Procédure de licences et SSPK d’accès

Veuillez envoyer un e-mail [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) pour toutes les requêtes de gestion des licences.

Le [portail de Distribution SSPK](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) est accessible à détenteurs intermédiaire enregistrés.

Version préliminaire et SSPK Final détenteurs peuvent poser des questions techniques aux [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com).

##<a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Smooth Streaming Client produit intermédiaires contrat détenteurs est Microsoft

- Adroit Business Solutions, Inc
- SA de diffusion numérique avancés
- AirTies Kablosuz Iletism Sanayive Dis Ticaret a.s.
- Albis Technologies Ltd.
- Alticast Corporation
- Amazon numérique Services, Inc.
- AVC multimédia logiciel Co., Ltd.
- Cavium, Inc.
- EchoStar achat Corporation
- Enseo, Inc.
- Fluendo Amérique du Sud
- HANDAN BroadInfoCom Co., Ltd.
- Infomir GMBH
- Irdeto USA Inc.
- Liberté Global Services VC
- MediaTek Inc.
- Co-création, Ltd MStar
- Nintendo Co., Ltd.
- OpenTV, Inc.
- Safran numérique limité
- Co. Ltd du Sichuan Changhong électrique
- SoftAtHome
- Sony Corporation
- Tatung technologie Inc.
- TCL Technoly Electronics (Huizhou) Co., Ltd.
- Vestel Elektronik Sanayi ve Ticaret a.s.
- VisualOn, Inc.
- ZTE Corporation

##<a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Smooth Streaming Client produit Final contrat licence est Microsoft

- SA de diffusion numérique avancés
- AirTies Kablosuz Iletism Sanayive Dis Ticaret a.s.
- Albis Technologies Ltd.
- Amazon numérique Services, Inc.
- AmTRAN technologie Co., Ltd.
- Arcadyan technologie Corporation
- ATMACA ELEKTRONİK SAN. ENREGISTRER TİC. A.Ş
- British Sky diffusion limité
- Technologie CastPal Inc. Shenzhen
- Intégrer Compal Electronics, Inc.
- Dongguan numérique AV technologie corp, Ltd.
- EchoStar achat Corporation
- Enseo, Inc.
- Filmflex films limité
- Fluendo Amérique du Sud
- Gibson Innovations limité
- Haier informations Applicantion S.R.L
- HANDAN BroadInfoCom Co., Ltd.
- Homecast Co., Ltd
- HON Hai précision industrie Co., Ltd.
- Infomir GMBH
- Kaonmedia Co., Ltd.
- Corporation KDDI
- Nintendo Co., Ltd.
- SA orange
- Safran numérique limité
- Haut débit Sagemcom associations de sécurité
- Shenzhen Coship Electronics CO., LTD
- Co. Ltd Shenzhen Jiuzhou électrique
- Technologie numérique Shenzhen Skyworth Co., Ltd
- Du Sichuan Changhong électrique Co., Ltd.
- Skardin industriel corp.
- Ciel Deutschland Fernsehen GmbH & Co. KG
- SmarDTV Amérique du Sud
- SoftAtHome
- Sony Corporation
- Limité TCL étranger Marketing (Macao commerciale Offshore)
- Technologies de remise technicolor, associations de sécurité
- Tongfang Global Ltd.
- Style de vie Toshiba produits et Services Corporation
- Universel Media Corporation /Slovakia/ s.r.o.
- VIZIO, Inc.
- Wistron Corporation
- ZTE Corporation

##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
