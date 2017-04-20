<properties
    pageTitle="Liste de compatibilité de fédération AD Azure"
    description="Cette page comporte des fournisseurs d’identité non Microsoft peuvent être utilisés pour implémenter l’authentification unique."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="billmath"/>

# <a name="azure-ad-federation-compatibility-list"></a>Liste de compatibilité de fédération AD Azure
Azure Active Directory fournit l’authentification unique sur et amélioré la sécurité access des applications pour Office 365 et autres Microsoft Online services pour hybride et mises en œuvre exclusivement le nuage, sans avoir besoin de n’importe quelle solution non Microsoft. Office 365, comme la plupart des services en ligne de Microsoft, est intégré à Azure Active Directory pour les services d’annuaire, l’authentification et d’autorisation. Azure Active Directory fournit également de l’authentification unique pour des milliers d’applications SaaS et applications web en local. Voir la galerie des applications Azure Active Directory pour les applications SaaS prises en charge.

Pour les organisations qui ont investi dans des solutions non Microsoft federation, cette rubrique contient la procédure de configuration de l’authentification unique pour leurs utilisateurs Active Directory Windows Server avec Microsoft Online services à l’aide de fournisseurs d’identité de non Microsoft à partir de la « Azure Active Directory federation compatibilité liste » ci-dessous. 


![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
[Groupe d’ordinateurs Oxford](http://oxfordcomputergroup.com/), un tiers, pour le compte Microsoft, testé ces unique authentification expériences à l’aide de fournisseurs d’identité non Microsoft par rapport à un ensemble de cas d’utilisation courants avec Azure Active Directory.

Pour plus d’informations sur comment vous pouvez obtenir votre fournisseur d’identité tiers répertorié ici, contactez le groupe d’ordinateurs Oxford en [idp@oxfordcomputergroup.com](mailto:idp@oxfordcomputergroup.com).

>[AZURE.IMPORTANT] Groupe d’ordinateurs Oxford testé uniquement les fonctionnalités de fédération de ces scénarios d’authentification unique. Groupe d’ordinateurs Oxford n’a pas effectué tout test de la synchronisation, authentification à deux facteurs, composants etc. de ces scénarios d’authentification unique.

>Utilisation de se connecter à autre ID à UPN n’est également pas testée dans ce programme.



- [Azure Active Directory](#azure-active-directory)
- [Services de fédération IDM optimale identité virtuelle Server](#optimal-idm-virtual-identity-server-federation-services) 
- [PingFederate 6.11](#pingfederate-611) 
- [7.2 PingFederate](#pingfederate-72) 
- [PingFederate 8.x](#pingfederate-8x)
- [Centrify](#centrify) 
- [IBM Tivoli fédérés Gestionnaire d’identité 6.2.2](#ibm-tivoli-federated-identity-manager-622) 
- [SecureAuth IdP 7.2.0](#secureauth-idp-720) 
- [Autorité de certification SiteMinder 12.52](#ca-siteminder-1252-sp1-cumulative-release-4) 
- [RadiantOne CFS 3.0](#radiantone-cfs-30) 
- [Okta](#okta) 
- [OneLogin](#onelogin) 
- [Gestionnaire d’accès NetIQ 4.0.1](#netiq-access-manager-401) 
- [BIG-IP avec le Gestionnaire de stratégie d’accès BIG-IP version 11.3 x – 11,6](#big-ip-with-access-policy-manager-big-ip-ver-113x-116x) 
- [Portail d’espace de travail VMware version 2.1](#vmware-workspace-portal-version-21) 
- [Connectez-vous et accédez 5.3](#signampgo-53) 
- [Version de fédération IceWall 3.0](#icewall-federation-version-30) 
- [Autorité de certification sécurisé Cloud](#ca-secure-cloud) 
- [Dell une identité Cloud Access Manager v7.1](#dell-one-identity-cloud-access-manager-v71) 
- [Authentification unique AuthAnvil 4.5](#authavil-single-sign-on-45) 

>[AZURE.IMPORTANT] Dans la mesure où il s’agit des produits tiers, Microsoft ne fournit pas prise en charge pour le déploiement, configuration, dépannage, meilleures pratiques, etc. problèmes et questions concernant ces fournisseurs d’identité. Pour la prise en charge et questions concernant ces fournisseurs d’identité, contacter directement le tiers pris en charge.

>Ces fournisseurs tiers identité ont été testés pour interopérabilité avec les services cloud Microsoft à l’aide de la fédération Web et les protocoles Web approbation uniquement. Test ne comprend pas l’aide du protocole SAML.

## <a name="azure-active-directory"></a>Azure Active Directory 
Azure Active Directory peut authentifier des utilisateurs à se fédérer avec votre Active Directory local ou sans un serveur de fédération local au moyen de la synchronisation de mot de passe. 

Voici la matrice de support scénario pour cette expérience d’authentification : 


| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office et CRM |  Prise en charge |Aucun|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Aucun|
|Applications modernes à l’aide du terme ADAL tels que Office 2016| Prise en charge|Aucun|

Pour plus d’informations sur l’utilisation de Azure Active Directory avec AD FS voir [Active Directory Federation Services (ADFS)](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)

Pour plus d’informations sur l’utilisation de Azure Active Directory avec la synchronisation de mot de passe voir [Azure AD Connect](active-directory-aadconnect.md).


## <a name="optimal-idm-virtual-identity-server-federation-services"></a>Services de fédération IDM optimale identité virtuelle Server 
Optimale IDM virtuel identité Server Federation Services peuvent authentifier les utilisateurs qui résident dans des répertoires Active clients locaux.

Voici le scénario matrice de support cette expérience authentification unique :


| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office et CRM |  Prise en charge |Authentification Windows intégrée|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Pour plus d’informations sur l’accès au client des stratégies voir [limiter l’accès à Office 365 Services selon l’emplacement du Client.](https://technet.microsoft.com/library/hh526961.aspx)|



## <a name="pingfederate-611"></a>PingFederate 6.11 

PingFederate 6.11 met en œuvre la norme d’identité Web fédération largement utilisée pour fournir une authentification unique et un attribut exchange cadre.

Voici la matrice de support scénario pour cette expérience authentification unique :


| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office et CRM |  Prise en charge |Aucun (les versions antérieures doivent mettre à niveau vers 6.11|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Aucun|

Pour les instructions PingFederate à suivre configurer ce STS pour fournir l’expérience de l’authentification unique à vos utilisateurs Active Directory, téléchargez le fichier pdf [ici.](http://go.microsoft.com/fwlink/?LinkID=266321)

## <a name="pingfederate-72"></a>7.2 PingFederate 
7.2 PingFederate met en œuvre la norme d’identité Web/Web-approbation de fédération largement utilisée pour fournir une authentification unique et un attribut exchange cadre.

Voici la matrice de support scénario pour cette expérience authentification unique :


| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office et CRM |  Prise en charge |Aucun|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Aucun|

Pour les instructions PingFederate comment configurer cette STS pour fournir l’expérience de l’authentification unique à vos utilisateurs Active Directory, voir [ici.](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)

## <a name="pingfederate-8x"></a>PingFederate 8.x 
PingFederate 8.x met en œuvre la norme d’identité Web/Web-approbation de fédération largement utilisée pour fournir une authentification unique et un attribut exchange cadre.

Voici la matrice de support scénario pour cette expérience authentification unique :


| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office et CRM |  Prise en charge |Aucun|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Aucun|

Pour les instructions PingFederate comment configurer cette STS pour fournir l’expérience de l’authentification unique à vos utilisateurs Active Directory, voir [ici.](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## <a name="centrify"></a>Centrify 
Centrify aide à fournit fédérés une seule expérience authentification pour Office 365 sans l’exigence d’héberger un serveur de fédération locale.

Voici la matrice de support scénario pour cette expérience authentification unique :


| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office et CRM |  Prise en charge |Aucun|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Contrôle d’accès client n’est pas pris en charge 

Pour plus d’informations sur Centrify, voir [ici.](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp)|

## <a name="ibm-tivoli-federated-identity-manager-622"></a>IBM Tivoli fédérés Gestionnaire d’identité 6.2.2 
IBM Tivoli fédérés Gestionnaire d’identité 6.2.2 avec IBM sécurité Access Manager pour Microsoft Applications 1.4 met en œuvre la norme d’identité Web/Web-approbation de fédération largement utilisée pour fournir une authentification unique et un attribut exchange cadre.

Voici la matrice de support scénario pour cette expérience authentification unique : 

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office et CRM |  Prise en charge |Aucun|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur IBM Tivoli fédérés identité Manager, voir [IBM sécurité Access Manager pour les Applications Microsoft.](http://www-01.ibm.com/support/docview.wss?uid=swg24029517)

## <a name="secureauth-idp-720"></a>SecureAuth IdP 7.2.0 
SecureAuth IdP 7.2.0 met en œuvre la norme d’identité Web/Web-approbation de fédération largement utilisée pour fournir une expérience d’authentification unique et la structure d’attribut d’exchange.

Voici la matrice de support scénario pour cette expérience authentification unique : 

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office et CRM |  Prise en charge |Aucun|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur SecureAuth, voir [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293).

## <a name="ca-siteminder-1252-sp1-cumulative-release-4"></a>Autorité de certification SiteMinder 12.52 SP1 version Cumulative 4
Autorité de certification SiteMinder fédération 12.52 met en œuvre la norme d’identité Web/Web-approbation de fédération largement utilisée pour fournir une authentification unique et un attribut exchange cadre.

Voici la matrice de support scénario pour cette expérience authentification unique : 

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office et CRM |  Prise en charge |Aucun|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur SiteMinder autorité de certification, consultez [autorité de certification SiteMinder fédération.](http://www.ca.com/us/products/ca-single-sign-on.html) 

## <a name="radiantone-cfs-30"></a>RadiantOne CFS 3.0 
RadiantOne Cloud Federation Service (CFS) 3.0 met en œuvre la norme d’identité Web/Web-approbation de fédération largement utilisée pour fournir une authentification unique et un attribut exchange cadre.

Voici la matrice de support scénario pour cette expérience authentification unique : 

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office et CRM |  Prise en charge |Authentification Windows intégrée|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur RadiantOne CFS, voir [RadiantOne CFS.](http://www.radiantlogic.com/products/radiantone-cfs/)


## <a name="okta"></a>Okta 
Okta met en œuvre la norme d’identité Web/Web-approbation de fédération largement utilisée pour fournir une authentification unique et un attribut exchange cadre.

Voici la matrice de support scénario pour cette expérience authentification unique : 


| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |L’authentification Windows intégrée requiert le programme d’installation du serveur web supplémentaires et Okta application.|
| Applications clientes telles que Lync, abonnement Office et CRM |  Prise en charge |Authentification Windows intégrée|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur Okta, voir [Okta.](https://www.okta.com/)
 
## <a name="onelogin"></a>OneLogin 
OneLogin comme testés en mai 2014 met en œuvre la norme d’identité Web/Web-approbation de fédération largement utilisée pour fournir une authentification unique et un attribut exchange cadre.

Voici la matrice de support scénario pour cette expérience authentification unique : 

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |Authentification Windows intégrée|
| Applications clientes telles que Lync, abonnement Office et CRM |  Prise en charge |Authentification Windows intégrée|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur OneLogin, voir [OneLogin.](https://www.onelogin.com/)

## <a name="netiq-access-manager-401"></a>Gestionnaire d’accès NetIQ 4.0.1 
Gestionnaire d’accès NetIQ 4.0.1 met en œuvre la norme d’identité Web/Web-approbation de fédération largement utilisée pour fournir une authentification unique et un attribut exchange cadre.

Voici la matrice de support scénario pour cette expérience authentification unique :

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |* Kerberos contrats pris en charge|
| Applications clientes telles que Lync, abonnement Office et CRM |  Prise en charge |L’authentification Windows intégrée n’est pas pris en charge|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Aucun|

* NetIQ prend en charge l’authentification Kerberos via la configuration d’un contrat Kerberos.  Pour obtenir une assistance avec cette configuration, contactez NetIQ ou afficher le guide de configuration. Pour plus d’informations à propos du gestionnaire NetIQ Access, voir [NetIQ Access Manager.](https://www.netiq.com/documentation/netiqaccessmanager4/identityserverhelp/data/b12iqp0m.html)

## <a name="big-ip-with-access-policy-manager-big-ip-ver-113x--116x"></a>BIG-IP avec la version du Gestionnaire de stratégie d’accès BIG-IP 11.3 x – 11,6 
BIG-IP avec Access stratégie Manager (APM) version BIG-IP x 11.3 – x 11,6 met en œuvre la norme d’identité SAML largement utilisée pour fournir une expérience d’authentification unique et la structure d’exchange attribut.

Voici la matrice de support scénario pour cette expérience authentification unique : 

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office et CRM |  Non pris en charge |Non pris en charge|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations à propos du Gestionnaire de stratégie BIG-IP Access, voir [Gestionnaire de stratégie d’accès BIG-IP.](https://f5.com/products/modules/access-policy-manager) 

Pour les Gestionnaire de stratégie d’accès BIG-IP des instructions sur cette procédure de configuration STS pour fournir l’expérience de l’authentification unique à vos utilisateurs Active Directory, téléchargez le fichier pdf [ici.](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf)

## <a name="vmware--workspace-portal-version-21"></a>Portail d’espace de travail VMware version 2.1 
Portail d’espace de travail VMware version 2.1 met en œuvre la norme d’identité Web/Web-approbation de fédération largement utilisée pour fournir une authentification unique et un attribut exchange cadre.

Voici la matrice de support scénario pour cette expérience authentification unique :

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |L’authentification Windows intégrée n’est pas pris en charge|
| Applications clientes telles que Lync, abonnement Office et CRM | Prise en charge |L’authentification Windows intégrée n’est pas pris en charge|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur le portail d’espace de travail VMware version 2.1, téléchargez le fichier pdf [ici.](http://pubs.vmware.com/workspace-portal-21/topic/com.vmware.ICbase/PDF/workspace-portal-21-resource.pdf)

## <a name="signgo-53"></a>Connectez-vous et accédez 5.3 
Se & accédez l’identité Web/Web-approbation de fédération largement utilisés 5.3 met en œuvre standard pour fournir une authentification unique et un attribut exchange cadre.

Voici la matrice de support scénario pour cette expérience authentification unique :

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |Contrats de Kerberos pris en charge |
| Applications clientes telles que Lync, abonnement Office et CRM | Prise en charge |Aucun|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Aucun|


Signe & atteindre 5.3 prend en charge l’authentification Kerberos via la configuration d’un contrat Kerberos.  Pour obtenir une assistance avec cette configuration, veuillez contacter Ilex ou d’afficher le guide de configuration [ici.](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)


## <a name="icewall-federation-version-30"></a>Version de fédération IceWall 3.0 
IceWall fédération Version 3.0 met en œuvre la norme d’identité Web/Web-approbation de fédération largement utilisée pour fournir une authentification unique et un attribut exchange cadre.

Voici la matrice de support scénario pour cette expérience authentification unique :

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |L’authentification Windows intégrée n’est pas pris en charge|
| Applications clientes telles que Lync, abonnement Office et CRM | Prise en charge |L’authentification Windows intégrée n’est pas pris en charge|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur la fédération IceWall, voir [ici](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/) et [ici.](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html)

## <a name="ca-secure-cloud"></a>Autorité de certification sécurisé Cloud 

Autorité de certification Secure Cloud met en œuvre la norme d’identité Web/Web-approbation de fédération largement utilisée pour fournir une authentification unique et un attribut exchange cadre.

Voici la matrice de support scénario pour cette expérience authentification unique :

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |L’authentification Windows intégrée n’est pas pris en charge|
| Applications clientes telles que Lync, abonnement Office et CRM | Prise en charge |L’authentification Windows intégrée n’est pas pris en charge|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur le Cloud Secure autorité de certification, consultez [autorité de certification Secure Cloud.](http://www.ca.com/us/products/security-as-a-service.aspx)

## <a name="dell-one-identity-cloud-access-manager-v71"></a>Dell une identité Cloud Access Manager v7.1 
Gestionnaire d’accès Dell une identité Cloud met en œuvre la norme d’identité Web/Web-approbation de fédération largement utilisée pour fournir une authentification unique et un attribut exchange cadre.

Voici la matrice de support scénario pour cette expérience authentification unique :

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |Aucun|
| Applications clientes telles que Lync, abonnement Office et CRM |  Prise en charge |Aucun|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Aucun|

Pour plus d’informations sur Dell une identité Cloud Access Manager, voir [Dell une identité Cloud Access Manager.](http://software.dell.com/products/cloud-access-manager)

 Pour obtenir des instructions sur la façon de configurer cette STS fournir l’expérience de l’authentification unique à vos utilisateurs d’Office 365, voir [configurer les utilisateurs d’Office 365.](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365) 

## <a name="authanvil-single-sign-on-45"></a>Authentification unique AuthAnvil 4.5 
AuthAnvil unique se sur 4.5 met en œuvre la norme d’identité Web/Web-approbation de fédération largement utilisée pour fournir une authentification unique et un attribut exchange cadre.

Voici la matrice de support scénario pour cette expérience authentification unique :

| Client |Prise en charge  |Exceptions|
| --------- | --------- |--------- |
| Clients Web tels que SharePoint Online et Exchange Web Access | Prise en charge |L’authentification Windows intégrée n’est pas pris en charge|
| Applications clientes telles que Lync, abonnement Office et CRM | Prise en charge |L’authentification Windows intégrée n’est pas pris en charge|
| Clients de messagerie enrichi, tels que Outlook et ActiveSync |  Prise en charge |Aucun|


Pour plus d’informations, voir [AuthAnvil Single Sign On.](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-)
