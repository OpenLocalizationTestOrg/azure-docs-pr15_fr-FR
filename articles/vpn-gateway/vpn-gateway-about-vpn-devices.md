<properties 
   pageTitle="À propos des périphériques VPN pour les connexions de Site à Site VPN passerelle pour les réseaux virtuels Azure | Microsoft Azure"
   description="Cet article aborde les périphériques VPN et les paramètres IPsec pour les connexions S2S VPN passerelle et contient des liens vers des exemples et instructions de configuration."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
  tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/13/2016"
   ms.author="yushwang;cherylmc" />

# <a name="about-vpn-devices-for-site-to-site-vpn-gateway-connections"></a>À propos des périphériques VPN pour les connexions Site à Site VPN passerelle

Un périphérique VPN est nécessaire pour configurer une connexion VPN de Site à Site (S2S). Connexions de site à Site peuvent être utilisées pour créer une solution hybride ou chaque fois que vous voulez une connexion sécurisée entre votre réseau local et votre réseau virtuel. Cet article décrit les périphériques VPN compatibles et paramètres de configuration. 

>[AZURE.NOTE] Lorsque vous configurez une connexion à un Site, une adresse IP IPv4 NOT est nécessaire pour votre appareil VPN.                                                                                                                                                                               

Si votre appareil n’apparaît pas dans la table de [périphériques VPN validé](#devicetable) , consultez la section [périphériques VPN Non validées](#additionaldevices) de cet article. Il est possible que votre périphérique fonctionne toujours avec Azure. Pour la prise en charge des périphériques VPN, veuillez contacter le fabricant de votre appareil.

**Éléments à noter lorsque vous affichez les tables :**

- Il a été modifié la terminologie de routage statique et dynamique. Vous rencontrez probablement les deux termes. Il n’y a aucune modification des fonctionnalités, modifiant uniquement les noms.
    - Routage statique = PolicyBased
    - Routage dynamique = RouteBased
- Caractéristiques de la passerelle de haute Performance VPN et passerelle RouteBased VPN sont identiques, sauf indication contraire. Par exemple, les appareils VPN validées qui sont compatibles avec les passerelles RouteBased VPN sont également compatibles avec la passerelle Azure haute Performance VPN. 


## <a name="devicetable"></a>Appareils VPN validées 

Nous avons validé un ensemble de périphériques VPN standard en partenariat avec des fournisseurs de l’appareil. Tous les appareils dans les familles appareil contenues dans la liste suivante fonctionne avec les passerelles VPN Azure. Consultez la section [Sur la passerelle de VPN](vpn-gateway-about-vpngateways.md) pour vérifier le type de passerelle dont vous avez besoin pour créer pour la solution que vous voulez configurer. 

Pour aider à configurer votre appareil VPN, consultez les liens qui correspondent aux amis périphérique approprié. Pour la prise en charge des périphériques VPN, veuillez contacter le fabricant de votre appareil.



| **Fournisseur**                      | **Famille de périphériques**                                        | **Version du système d’exploitation minimale**                             | **PolicyBased**                                                                                                                                                                                                             | **RouteBased**                                                                                                                                                                    |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Des Telesis                  | AR série VPN routeurs                                    | 2.9.2                                              | À venir                                                                                                                                                                                                                                          | N’est pas compatible                                                                                                                                                                                               |
| Barracuda réseaux, Inc.        | Série Barracuda NextGen pare-feu F             | PolicyBased : 5.4.3, RouteBased : 6.2.0  | [Instructions de configuration](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) | [Instructions de configuration](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW)                                                                                                                                                                                              |
| Barracuda réseaux, Inc.        |  Série Barracuda NextGen pare-feu X                 | Barracuda du pare-feu 6.5 | [Pare-feu Barracuda](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | N’est pas compatible                                                                                                                                                                                               |
| Brocart                         | Égal à 5 400 Vyatta vRouter                                      | Disponibilité générale 6.6R3 routeur virtuel                            | [Instructions de configuration](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html)                                       | N’est pas compatible                                                                                                                                                                                               |
| Point de vérification                     | Passerelle de sécurité                                         | R75.40, R75.40VS                                     | [Instructions de configuration](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275)                                         | [Instructions de configuration](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco                           | ASA                                                      | 8.3                                                | [Exemples de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA)                                                                                                                                                                        | N’est pas compatible                                                                                                                                                                                               |
| Cisco                           | RÉCUPÉRATION AUTOMATIQUE DU SYSTÈME                                                      | IOS 15.1 (PolicyBased), IOS 15.2 (RouteBased)                | [Exemples de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                                                        | [Exemples de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                 |
| Cisco                           | ISR                                                      | IOS 15.0 (PolicyBased), IOS 15.1 (RouteBased *)               | [Exemples de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                                                        | [Exemples de Cisco *](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                |
| Citrix                          | MPX NetScaler, SDX, VPX      |10.1 et versions ultérieures                                           | [Instructions de l’intégration](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html)                                                                                                                                                                            | N’est pas compatible                                                                                                                                                                                               |
| Dell SonicWALL                  | Série TZ, ans, série SuperMassive, E-classe ans séries | SonicOS 5.8.x, [SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850), [SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646 )          | [Instructions - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Instructions - SonicOS 5,9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                   | [Instructions - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Instructions - SonicOS 5,9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                                                                      |
| F5                              | Série BIG-IP                                 |           12.0                                            | [Instructions de configuration](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip)                                                                                                                                                                          | [Instructions de configuration](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling)                                                                                                                                                                                         |
| Fortinet                        | FortiGate                                                | FortiOS 5.2.7                                      | [Instructions de configuration](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                                                          | [Instructions de configuration](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                  |
| Japon Initiative Internet (IIJ) | Série SEIL                                              | SEIL / X 4.60, 4.60 SEIL/B1, SEIL/x86 3,20            | [Instructions de configuration](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf)                                                                                                                                                                   | N’est pas compatible                                                                                                                                                                                               |
| Juniper                         | SRX                                                      | JunOS 10.2 (PolicyBased), JunOS 11.4 (RouteBased)            | [Exemples de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                                                                      | [Exemples de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                              |
| Juniper                         | J-série                                                 | JunOS 10.4r9 (PolicyBased), JunOS 11.4 (RouteBased)          | [Exemples de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                                                                 | [Exemples de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                         |
| Juniper                         | ISG                                                      | ScreenOS 6.3 (PolicyBased et RouteBased)                  | [Exemples de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                                                                      | [Exemples de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                              |
| Juniper                         | SSG                                                      | ScreenOS 6.2 (PolicyBased et RouteBased)                  | [Exemples de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                                                                      | [Exemples de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                              |
| Microsoft                       | Service d’accès distant et de routage                        | Windows Server 2012                                | N’est pas compatible                                                                                                                                                                                                                                       | [Exemples de Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=717761)                                                                                           |
| Ouvrir systèmes AG | Passerelle de sécurité contrôle mission | N/A | [Guide d’installation](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) | [Guide d’installation](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan                        | Openswan                                                 | 2.6.32                                             | (Bientôt disponible)                                                                                                                                                                                                                                        | N’est pas compatible                                                                                                                                                                                               |
| Palo Alto Networks              | Tous les appareils exécutant le système d’exploitation panoramique     | PANORAMIQUE-OS 6.1.5 ou version ultérieure (PolicyBased), panoramique-OS 7.0.5 ou version ultérieure (RouteBased)       | [Instructions de configuration]( https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065)                                                                                                                                                                                          | [Instructions de configuration](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340)                                                                                                                                                                                    |
| WatchGuard                      | Tous les                                                      | Fireware XTM v11.x                                 | [Instructions de configuration](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/)                                                                                                                                                                          | N’est pas compatible                                                                                                                                                                                               |

(*) Routeurs ISR 7200 série prend uniquement en charge PolicyBased VPN.

## <a name="additionaldevices"></a>Périphériques VPN non validées

Si vous ne voyez pas votre appareil répertoriée dans le tableau appareils VPN validé, il peut fonctionner avec une connexion à un Site. Vérifiez que votre appareil VPN répond à la configuration minimale requise sous forme de plan dans la section Configuration requise de passerelle de l’article [Sur les passerelles VPN](vpn-gateway-about-vpngateways.md#gateway-requirements) . Appareils la configuration minimale requise de la réunion doivent également fonctionnent correctement avec passerelles VPN. Contactez le fabricant de votre appareil pour obtenir des instructions supplémentaires prise en charge et la configuration.


## <a name="editing-device-configuration-samples"></a>Modification des exemples de configuration de périphériques

Après avoir téléchargé l’échantillon de configuration du dispositif VPN fourni, vous devez remplacer certaines des valeurs pour refléter les paramètres pour votre environnement. 

**Pour modifier un exemple :**

1. Ouvrez l’exemple en utilisant le bloc-notes. 
1. Recherchez et remplacez toutes les chaînes <*texte*> avec les valeurs qui s’appliquent à votre environnement. Veillez à inclure < et >. Lorsqu’un nom est spécifié, le nom que vous sélectionnez doit être unique. Si une commande ne fonctionne pas, consultez la documentation de fabricant de votre appareil.

| **Exemple de texte**                | **Remplacez**                                                                                                        |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| &lt;RP_OnPremisesNetwork&gt;           | Votre nom choisie pour cet objet. Exemple : myOnPremisesNetwork                                                       |
| &lt;RP_AzureNetwork&gt;                | Votre nom choisie pour cet objet. Exemple : myAzureNetwork                                                            |
| &lt;RP_AccessList&gt;                  | Votre nom choisie pour cet objet. Exemple : myAzureAccessList                                                         |
| &lt;RP_IPSecTransformSet&gt;           | Votre nom choisie pour cet objet. Exemple : myIPSecTransformSet                                                       |
| &lt;RP_IPSecCryptoMap&gt;              | Votre nom choisie pour cet objet. Exemple : myIPSecCryptoMap                                                          |
| &lt;SP_AzureNetworkIpRange&gt;         | Spécifiez la plage. Exemple : 192.168.0.0                                                                                  |
| &lt;SP_AzureNetworkSubnetMask&gt;      | Spécifiez le masque. Exemple : 255.255.0.0                                                                            |
| &lt;SP_OnPremisesNetworkIpRange&gt;    | Spécifiez la plage en local. Exemple : 10.2.1.0                                                                         |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; | Spécifiez masque en local. Exemple : 255.255.255.0                                                              |
| &lt;SP_AzureGatewayIpAddress&gt;       | Ces informations spécifiques à votre réseau virtuel et se trouve dans le portail de gestion comme **adresse IP de la passerelle**. |
| &lt;SP_PresharedKey&gt;                | Ces informations sont spécifiques à votre réseau virtuel et sont trouve dans le portail de gestion comme clé gérer.          |



## <a name="ipsec-parameters"></a>Paramètres IPsec

>[AZURE.NOTE] Bien que les valeurs indiquées dans le tableau suivant sont pris en charge par la passerelle VPN Azure, il n’existe actuellement aucun moyen vous permettant de spécifier ou sélectionnez une combinaison spécifique à partir de la passerelle VPN Azure. Vous devez spécifier les contraintes de l’appareil VPN en local. En outre, vous devez PINCE MSS en 1350.

### <a name="ike-phase-1-setup"></a>Programme d’installation IKE Phase 1

| **Propriété**                                       | **PolicyBased** | **Passerelle RouteBased et Standard ou haute Performance VPN** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| Version IKE                                        | IKEv1                          | IKEv2                                                            |
| Groupe Diffie­Hellman                               | Groupe 2 (1024 bits)             | Groupe 2 (1024 bits)                                               |
| Méthode d’authentification                              | Clé partagée                 | Clé partagée                                                   |
| Algorithmes de chiffrement                              | AES256 AES128 3DES             | 3DES AES256                                                      |
| Algorithme de hachage                                  | SHA1(SHA128)                   | SHA1(SHA128), SHA2(SHA256)                                                     |
| Durée de vie phase 1 sécurité Association (SA) (heure) | 28 800                 | secondes 10,800                                                   |


### <a name="ike-phase-2-setup"></a>Programme d’installation IKE Phase 2

| **Propriété**                                                             | **PolicyBased**                 | **Passerelle RouteBased et Standard ou haute Performance VPN**   |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| Version IKE                                                              | IKEv1                                          | IKEv2                                                              |
| Algorithme de hachage                                                        | SHA1(SHA128)                                   | SHA1(SHA128)                                                       |
| Durée de vie phase 2 sécurité Association (SA) (heure)                        | 3 600 secondes                                  | 3 600 secondes                                                                  |
| Durée de vie phase 2 sécurité Association (SA) (débit)                  | BASE DE CONNAISSANCES 102,400,000                                 | -                                                                  |
| Impossibilité de SA & authentification offre (dans l’ordre de préférence) | 1. 2 ESP AES256. ESP-AES128 3. ESP-3DES 4. N/A | Consultez une *Association de sécurité IPsec passerelle RouteBased (SA) offre* (ci-dessous) |
| Parfaite (PFS)                                            | N°                                             | Aucune (*)|
| Inactive homologue détection                                                      | Non pris en charge                                  | Prise en charge                                                          |

(*) Azure passerelle en tant que répondeur IKE peut accepter PFS DH groupe 1, 2, 5, 14, 24.

### <a name="routebased-gateway-ipsec-security-association-sa-offers"></a>Association de sécurité IPsec passerelle RouteBased (SA) offre

Le tableau suivant répertorie SA cette impossibilité et l’authentification offre. Offres figurent l’ordre de préférence que l’offre est présenté ou accepté.

| **Association de sécurité IPsec chiffrement et des offres d’authentification** | **Azure passerelle en tant qu’initiateur**                               | **Passerelle Azure comme répondeur**                                   |
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1                                                 | ÇA ESP AES_256                                              | ÇA ESP AES_128                                              |
| 2                                                 | ÇA ESP AES_128                                              | ESP 3_DES MD5                                                |
| 3                                                 | ESP 3_DES MD5                                                | ESP 3_DES ÇA                                                |
| 4                                                 | ESP 3_DES ÇA                                                | SHA1 AH avec ESP AES_128 avec HMAC null                      |
| 5                                                 | SHA1 AH avec ESP AES_256 avec HMAC null                      | SHA1 AH avec ESP 3_DES avec HMAC null                        |
| 6                                                 | SHA1 AH avec ESP AES_128 avec HMAC null                      | MD5 AH avec ESP 3_DES avec HMAC null, aucune durée de vie proposée |
| 7                                                 | SHA1 AH avec ESP 3_DES avec HMAC null                        | SHA1 AH avec ESP 3_DES SHA1, aucune durée de vie                    |
| 8                                                 | MD5 AH avec ESP 3_DES avec HMAC null, aucune durée de vie proposée | MD5 AH avec ESP 3_DES MD5, aucune durée de vie                     |
| 9                                                 | SHA1 AH avec ESP 3_DES SHA1, aucune durée de vie                    | ESP DES MD5                                                  |
| 10                                                | MD5 AH avec ESP 3_DES MD5, aucune durée de vie                     | ESP DES SHA1, aucune durée de vie                                   |
| 11                                                | ESP DES MD5                                                  | Aucune durée de vie nous constatons SHA1 avec DES ESP null HMAC, proposée        |
| 12                                                | ESP DES SHA1, aucune durée de vie                                   | Aucune durée de vie nous constatons MD5 avec DES ESP null HMAC, proposée        |
| 13                                                | Aucune durée de vie nous constatons SHA1 avec DES ESP null HMAC, proposée        | SHA1 AH avec ESP DES SHA1, aucune durée de vie                      |
| 14                                                | Aucune durée de vie nous constatons MD5 avec DES ESP null HMAC, proposée        | MD5 AH avec ESP DES MD5, aucune durée de vie                       |
| 15                                                | SHA1 AH avec ESP DES SHA1, aucune durée de vie                      | ESP ça, aucune durée de vie                                        |
| 16                                                | MD5 AH avec ESP DES MD5, aucune durée de vie                       | ESP MD5, aucune durée de vie                                        |
| 17                                                | -                                                            | Nous constatons ça, aucune durée de vie                                         |
| 18                                                | -                                                            | AH MD5, aucune durée de vie                                        |


- Vous pouvez spécifier le chiffrement IPsec ESP NULL avec des passerelles RouteBased et haute Performance VPN. NULL de chiffrement ne fournit pas de protection pour le transfert de données et ne doit être utilisé lorsque maximale latence débit et minimum est requise.  Les clients peuvent choisir de l’utiliser dans les scénarios de communication VNet-VNet, ou lorsque le chiffrement est appliqué ailleurs dans la solution.

- Pour la connectivité entre local via Internet, utilisez les paramètres par défaut de la passerelle VPN Azure avec chiffrement et des algorithmes de hachage répertoriées dans les tableaux ci-dessus pour garantir la sécurité de vos communications critiques.





