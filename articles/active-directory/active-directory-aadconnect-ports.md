<properties
    pageTitle="Azure AD Connect : Ports | Microsoft Azure"
    description="Cette page est une page d’informations techniques de référence pour les ports qui doivent être ouverts pour Azure AD Connect"
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
    ms.date="08/25/2016"
    ms.author="billmath"/>

# <a name="hybrid-identity-required-ports-and-protocols"></a>Identité hybride obligatoire Ports et protocoles
Le document suivant est une référence technique pour fournir des informations sur les ports et protocoles requis qui sont requises pour implémenter une solution d’identité hybride. Utilisez l’illustration suivante et reportez-vous à la table correspondante.

![Qu’est Azure AD Connect](./media/active-directory-aadconnect-ports/required1.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tableau 1 : Azure AD se connecter et Active Directory local
Ce tableau décrit les ports et protocoles requis pour les communications entre le serveur Azure AD Connect et Active Directory local.

Protocole | Ports | Description
--------- | --------- |---------
DNS|53 (TCP/UDP)| Recherches DNS sur la forêt de destination.
Kerberos|88 (TCP/UDP)| Authentification Kerberos à la forêt Active Directory.
MS-RPC |135 (TCP/UDP)| Utilisé pendant la configuration initiale de l’Assistant Azure AD Connect lorsqu’il établit la forêt Active Directory.
LDAP|389 (TCP/UDP)| Utilisé pour l’importation de données depuis Active Directory. Données sont chiffrées avec authentification Kerberos & joint.
LDAP/SSL|636 (TCP/UDP)| Utilisé pour l’importation de données depuis Active Directory. Le transfert de données est signé et chiffré. Utilisé uniquement si vous utilisez SSL.
RPC |49152 à 65535 (aléatoire haute RPC Port)(TCP/UDP)| Utilisée pendant la configuration initiale de Azure AD Connect lorsqu’il est lié aux forêts AD. Pour plus d’informations, voir [KB929851](https://support.microsoft.com/kb/929851) [KB832017](https://support.microsoft.com/kb/832017)et [KB224196](https://support.microsoft.com/kb/224196) .

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tableau 2 - Azure AD se connecter et Azure AD
Ce tableau décrit les ports et protocoles requis pour les communications entre le serveur Azure AD Connect et Azure AD.

Protocole |Ports |Description
--------- | --------- |---------
HTTP|80 (TCP/UDP)| Permet de télécharger des listes de révocation (listes révocation de certificats) pour vérifier les certificats SSL.
HTTPS|443(TCP/UDP)| Utilisé pour la synchronisation avec Active Directory Azure.

Pour obtenir une liste d’URL et adresses IP adresses, vous devez l’ouvrir dans votre pare-feu, voir [Office 365 URL et plages d’adresses IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-federation-serverswap"></a>Tableau 3 : Azure AD se connecter et les serveurs de fédération/WAP
Ce tableau décrit les ports et protocoles requis pour les communications entre le serveur Azure AD Connect et les serveurs de fédération/WAP.  

Protocole |Ports |Description
--------- | --------- |---------
HTTP|80 (TCP/UDP)| Permet de télécharger des listes de révocation (listes révocation de certificats) pour vérifier les certificats SSL.
HTTPS|443(TCP/UDP)| Utilisé pour la synchronisation avec Active Directory Azure.
WinRM|5985| Récepteur WinRM

## <a name="table-4---wap-and-federation-servers"></a>Tableau 4 : WAP et les serveurs de fédération
Ce tableau décrit les ports et protocoles requis pour les communications entre les serveurs de fédération et WAP.

Protocole |Ports |Description
--------- | --------- |---------
HTTPS|443(TCP/UDP)| Utilisé pour l’authentification.

## <a name="table-5---wap-and-users"></a>Tableau 5 : WAP et les utilisateurs
Ce tableau décrit les ports et protocoles requis pour les communications entre les utilisateurs et les serveurs WAP.

Protocole |Ports |Description
--------- | --------- |--------- |
HTTPS|443(TCP/UDP)| Utilisé pour l’authentification de l’appareil.
TCP|49443 (TCP)| Utilisé pour l’authentification par certificat.

## <a name="table-6a--6b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tableau 6 a et 6 b - Azure AD connecter santé agent pour AD FS/Sync () et Azure AD
Les tableaux suivants décrivent les points de terminaison, les ports et protocoles requis pour la communication entre agents Azure AD connecter santé et Azure AD

### <a name="table-6a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tableau 6 a - Ports et protocoles pour agent Azure AD connecter santé pour AD FS/Sync () et Azure AD
Ce tableau décrit les ports sortants suivants et les protocoles requis pour les communications entre les agents Azure AD connecter santé et Azure AD.  

Protocole |Ports  |Description
--------- | --------- |--------- |
HTTPS|443(TCP/UDP)| Sortant
Bus des services Azure|5671 (TCP/UDP)| Sortant

### <a name="6b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>6 b - points de terminaison pour agent Azure AD connecter santé pour AD FS/Sync () et Azure AD
Pour une liste des points de terminaison, consultez [la section Configuration requise pour l’agent d’Azure AD connecter intégrité](active-directory-aadconnect-health-agent-install.md#requirements).
