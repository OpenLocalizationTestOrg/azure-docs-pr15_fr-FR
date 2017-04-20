<properties
   pageTitle="Azure AD Connect : Conditions préalables et le matériel | Microsoft Azure"
   description="Cette rubrique décrit les conditions préalables et la configuration matérielle requise pour Azure AD Connect"
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/12/2016"
   ms.author="billmath"/>

# <a name="prerequisites-for-azure-ad-connect"></a>Conditions préalables pour Azure AD se connecter
Cette rubrique décrit les conditions préalables et la configuration matérielle requise pour Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Avant d’installer Azure AD Connect
Avant d’installer Azure AD Connect, voici quelques éléments dont vous avez besoin.

### <a name="azure-ad"></a>Azure AD
- Un abonnement Azure ou un [abonnement d’évaluation Azure](https://azure.microsoft.com/pricing/free-trial/). Cette option n’est requise pour l’accès au portail Azure et non pour à l’aide d’Azure AD Connect. Si vous utilisez PowerShell ou Office 365 vous n’avez pas besoin d’un abonnement Azure utiliser Azure AD Connect. Si vous disposez d’une licence Office 365, vous pouvez également utiliser le portail Office 365. Avec une licence Office 365 payant vous pouvez également accéder au portail Azure à partir du portail Office 365.
    - Vous pouvez également utiliser la fonctionnalité Aperçu Azure AD dans le [portail Azure](https://portal.azure.com). Ce portail ne requiert pas de licence Azure.
- [Ajouter et vérifier le domaine](active-directory-add-domain.md) vous prévoyez d’utiliser dans Azure AD. Par exemple, si vous envisagez d’utiliser contoso.com pour vos utilisateurs, puis vérifiez que ce domaine a été vérifié et vous n’utilisez pas uniquement le domaine par défaut contoso.onmicrosoft.com.
- Permet à un client Azure AD par les objets par défaut 50 Ko. Lorsque vous vérifiez votre domaine, la limite est augmentée à 300 objets k. Si vous avez besoin d’autres objets dans Azure Active Directory, vous devez ouvrir une demande de support pour que la limite est augmentée encore plus loin. Si vous avez besoin de plus de 500 objets k puis vous avez besoin d’une licence, tels que Office 365, Azure AD base, Azure AD Premium ou Enterprise mobilité Suite.

### <a name="prepare-your-on-premises-data"></a>Préparez vos données en local
- Examiner [les fonctionnalités de synchronisation facultatif que vous pouvez activer dans Azure Active Directory](active-directory-aadconnectsyncservice-features.md) et évaluer les fonctionnalités, vous devez activer.

### <a name="on-premises-servers-and-environment"></a>Environnement et sur les serveurs locaux
- L’annonces schéma version et la forêt niveau fonctionnel doit être Windows Server 2003 ou version ultérieure. Le domaine peut exécuter n’importe quelle version dans la mesure où les conditions de niveau schéma et une forêt sont remplies.
- Si vous envisagez d’utiliser la fonctionnalité **mot de passe en écriture différée** le domaine doit être sur Windows Server 2008 (avec dernier SP) ou version ultérieure. Si votre contrôleur de domaine se trouvent sur 2008 (pré-R2) vous devez également appliquer [le correctif KB2386717](http://support.microsoft.com/kb/2386717).
- Le contrôleur de domaine utilisé par Azure AD doit être accessible en écriture. Il n’est pas pris en charge pour utiliser un contrôleur (contrôleur de domaine en lecture seule) et Azure AD Connect n’est pas conforme chaque redirection d’écriture.
- Azure AD Connect ne peut pas être installé sur Small Business Server ou Windows Server Essentials. Le serveur doit être à l’aide de Windows Server standard ou mieux.
- Le serveur Azure AD Connect doit avoir une interface utilisateur complète installée. Il n’est pas pris en charge pour l’installer sur server core.
- Azure AD Connect doit être installé sur Windows Server 2008 ou version ultérieure. Ce serveur peut être un contrôleur de domaine ou un serveur membre si vous utilisez les paramètres express. Si vous utilisez des paramètres personnalisés, le serveur peut également être autonome et ne doit pas être associé à un domaine.
- Si vous installez Azure AD Connect sur Windows Server 2008, veillez à appliquer le dernier correctif correctifs à partir de Windows Update. L’installation n’est pas en mesure de démarrer avec un serveur non corrigé.
- Si vous prévoyez d’utiliser la fonctionnalité **synchronisation de mot de passe**, le serveur Azure AD Connect doit être sur Windows Server 2008 R2 SP1 ou version ultérieure.
- Le serveur Azure AD Connect doit avoir [.NET Framework 4.5.1](#component-prerequisites) ou version ultérieure et [Microsoft PowerShell 3.0](#component-prerequisites) ou version ultérieure est installé.
- Si Active Directory Federation Services est déployé, les serveurs où AD FS ou Proxy de l’Application Web sont installés doivent être Windows Server 2012 R2 ou version ultérieure. [Gestion à distance Windows](#windows-remote-management) doit être activé sur ces serveurs pour l’installation à distance.
- Si est déployé Active Directory Federation Services, vous devez [Les certificats SSL](#ssl-certificate-requirements).
- Si est déployé Active Directory Federation Services, vous devez configurer [la résolution de noms](#name-resolution-for-federation-servers).
- Azure AD Connect nécessite une base de données SQL Server pour stocker les données d’identité. Par défaut un SQL Server 2012 Express LocalDB (une version légère de SQL Server Express) est installé et le compte de service pour le service est créé sur l’ordinateur local. SQL Server Express a une limite de 10 Go qui vous permet de gérer environ 100 000 objets. Si vous avez besoin gérer un volume plus important d’objets d’annuaire, vous devez pointez sur l’Assistant installation à une autre installation de SQL Server.
- Si vous utilisez un serveur SQL distinct, ces exigences s’appliquent :
    - Azure AD Connect prend en charge toutes les versions de Microsoft SQL Server à partir de SQL Server 2008 (avec Service Pack 4) pour SQL Server 2014. Base de données SQL Microsoft Azure est **pas pris en charge** comme une base de données.
    - Vous devez utiliser un classement SQL sans respecter la casse. Ils sont identifiés avec un \_CI_ dans son nom. Il est **pas pris en charge** pour utiliser un classement qui respecte la casse, identifié par \_CS_ dans son nom.
    - Vous ne pouvez avoir un moteur de synchronisation de chaque instance de base de données. Il est **pas pris en charge** pour partager l’instance de base de données avec synchronisation FIM/MIM, DirSync ou Azure AD Sync.

### <a name="accounts"></a>Comptes
- Un compte Azure AD administrateur Global pour le répertoire Azure AD que vous souhaitez intégrer. Cela doit être un **compte scolaire ou de l’organisation** et ne peut pas être un **compte Microsoft**.
- Si vous utilisez les paramètres express ou mettre à niveau à partir de la synchronisation d’annuaire, vous devez disposer un compte d’administrateur d’entreprise pour votre Active Directory local.
- [Comptes dans Active Directory](./connect/active-directory-aadconnect-accounts-permissions.md) si vous utilisez le chemin d’installation des paramètres personnalisés.

### <a name="azure-ad-connect-server-configuration"></a>Configuration du serveur Azure AD Connect
- Si votre administrateur global ont l’authentification Multifacteur activé, l' URL **https://secure.aadcdn.microsoftonline-p.com** doit être dans la liste des sites de confiance. Vous êtes invité à ajouter à la liste des sites de confiance s’il n’est pas ajouté avant que vous êtes invité à un défi l’authentification Multifacteur. Vous pouvez utiliser Internet Explorer pour l’ajouter à vos sites de confiance.

### <a name="connectivity"></a>Connectivité
- Le serveur Azure AD Connect doit résolution DNS pour intranet et internet. Le serveur DNS doit être en mesure de résoudre des noms à la fois à votre locale d’Active Directory, ainsi que les points de terminaison Azure AD.
- Si vous avez un pare-feu sur votre Intranet, et vous devez ouvrir les ports entre les serveurs Azure AD Connect et votre domaine, voir [Azure AD Connect Ports](active-directory-aadconnect-ports.md) pour plus d’informations.
- Si votre serveur proxy ou un pare-feu limite URL accessible, puis les URL présentées dans les [plages d’adresses IP et URL Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) doit être ouvert.
    - Si vous utilisez le Cloud Microsoft en Allemagne ou dans le nuage pour le gouvernement de Microsoft Azure, consultez [Considérations relatives aux instances de Azure AD Connect sync services](active-directory-aadconnect-instances.md) pour les URL.
- Azure AD Connect est par défaut à l’aide de TLS 1.0 pour communiquer avec Azure AD. Vous pouvez le modifier à la version 1.2 TLS en suivant les étapes décrites dans [Activer TLS 1.2 pour Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
- Si vous utilisez un proxy sortant pour vous connecter à Internet, le paramètre suivant dans le fichier **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** doit être ajouté pour l’Assistant installation et synchronisation Azure AD Connect pouvoir vous connecter à Internet et Azure AD. Ce texte doit figurer dans la partie inférieure du fichier. Dans ce code, &lt;PROXYADRESS&gt; représente le proxy réel adresse IP ou hôte nom.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

- Si votre serveur proxy requiert une authentification, puis le [compte de service](./connect/active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts) doit se trouver dans le domaine et vous devez utiliser le chemin d’installation des paramètres personnalisés pour spécifier un [compte de service personnalisé](./connect/active-directory-aadconnect-get-started-custom.md#install-required-components). Vous devez également une autre modification machine.config. Avec ce changement dans l’Assistant installation machine.config et moteur de synchronisation de répondent à des demandes d’authentification à partir du serveur proxy. Dans l’Assistant installation toutes les pages, exclure la page **configurer** les signé dans les informations d’identification de l’utilisateur sont utilisées. Dans la page **configurer** à la fin de l’Assistant installation, le contexte est basculé sur le [compte de service](./connect/active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts) qui a été créé par vous. La section machine.config doit ressembler à ceci.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

Voir MSDN pour plus d’informations sur l' [élément du proxy par défaut](https://msdn.microsoft.com/library/kd3cf2ex.aspx).

Si vous avez des problèmes de connectivité, voir [résoudre les problèmes de connectivité](active-directory-aadconnect-troubleshoot-connectivity.md).

### <a name="other"></a>Autres
- Facultatif : Un compte d’utilisateur test pour vérifier la synchronisation.

## <a name="component-prerequisites"></a>Composants requis

### <a name="powershell-and-net-framework"></a>PowerShell et .net Framework
Azure AD Connect dépend de Microsoft PowerShell et .NET Framework 4.5.1. Vous avez besoin de cette version ou une version ultérieure est installé sur votre serveur. Selon votre version de Windows Server, procédez comme suit :

- Windows Server 2012R2
  - Microsoft PowerShell est installé par défaut, aucune action n’est requise.
  - .NET framework 4.5.1 et versions ultérieures sont offerts par Windows Update. Vérifiez que vous avez installé les dernières mises à jour à Windows Server dans le panneau de configuration.
- Windows Server 2008 R2 et Windows Server 2012
  - La dernière version de Microsoft PowerShell est disponible dans **Windows Management Framework 4.0**, disponible sur le [Centre de téléchargement Microsoft](http://www.microsoft.com/downloads).
  - .NET framework 4.5.1 et versions ultérieures sont disponibles sur le [Centre de téléchargement Microsoft](http://www.microsoft.com/downloads).
- Windows Server 2008
  - La dernière version de PowerShell est disponible dans **Windows Management Framework 3.0**, disponible sur le [Centre de téléchargement Microsoft](http://www.microsoft.com/downloads).
 - .NET framework 4.5.1 et versions ultérieures sont disponibles sur le [Centre de téléchargement Microsoft](http://www.microsoft.com/downloads).

### <a name="enable-tls-12-for-azure-ad-connect"></a>Activer TLS 1.2 pour Azure AD Connect
Azure AD Connect utilise TLS 1.0 par défaut pour chiffrer les communications entre le serveur de moteur de synchronisation et Azure AD. Vous pouvez le modifier en configurant les applications .net à utiliser TLS 1.2 par défaut sur le serveur. Vous trouverez plus d’informations sur TLS 1.2 dans [2960358 avis de sécurité Microsoft](https://technet.microsoft.com/security/advisory/2960358).

1. TLS 1.2 ne peut pas être activé sur Windows Server 2008. Vous avez besoin de Windows Server 2008 R2 ou version ultérieure. Vérifiez que vous le correctif .net 4.5.1 pour votre système d’exploitation a été, voir [2960358 avis de sécurité Microsoft](https://technet.microsoft.com/security/advisory/2960358). Vous pouvez rencontrer ce ou une version ultérieure est déjà installé sur votre serveur.
2. Si vous utilisez Windows Server 2008 R2, puis vérifiez que TLS 1.2 est activée. Sur Windows Server 2012 et versions ultérieures, TLS 1.2 doit déjà être activé.
```
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
```
3. Pour tous les systèmes d’exploitation, définissez cette clé de Registre et redémarrez le serveur.
```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
"SchUseStrongCrypto"=dword:00000001
```
4. Si vous souhaitez également activer TLS 1.2 entre le serveur de moteur de synchronisation et un serveur SQL distant, vérifiez que vous respectez les versions requis installées pour [prendre en charge TLS 1.2 pour Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Conditions préalables pour l’installation de fédération et configuration

### <a name="windows-remote-management"></a>Gestion à distance Windows
Lorsque vous utilisez Azure AD Connect pour déployer Active Directory Federation Services ou le Proxy d’Application Web, vérifiez la configuration requise ci-dessous pour garantir la réussiront de connectivité et configuration :

- Si le serveur cible est joint au domaine, vérifiez que Windows distant gérées est activée
    - Dans une fenêtre de commande PSH avec élévation de privilèges, utilisez la commande`Enable-PSRemoting –force`
- Si le serveur cible est un ordinateur WAP joint non à un domaine, il existe quelques configuration requise supplémentaire
    - Sur l’ordinateur cible (machine WAP) :
         - Assurer la winrm (gestion à distance Windows / gestion) service est en cours d’exécution par le biais du composant logiciel enfichable Services
         - Dans une fenêtre de commande PSH avec élévation de privilèges, utilisez la commande`Enable-PSRemoting –force`
    - Sur l’ordinateur sur lequel l’Assistant s’exécute (si l’ordinateur cible est non-joint ou non fiable un domaine) :
        - Dans une fenêtre de commande PSH avec élévation de privilèges, utilisez la commande`Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
        - Dans le Gestionnaire de serveur :
             - ajouter l’hôte WAP DMZ au pool de machines (Gestionnaire de serveur -> Gérer -> ajouter des serveurs... utiliser l’onglet DNS)
             - Onglet Gestionnaire de serveur tous les serveurs : cliquez avec le bouton droit sur le serveur WAP et cliquez sur Gérer sous..., entrez Réf. local (pas de domaine) pour la machine WAP
             - Pour valider la connectivité à distance PSH, sous l’onglet Gestionnaire de serveur tous les serveurs : cliquez avec le bouton droit sur le serveur WAP et choisissez Windows PowerShell.  Une session distante PSH doit ouvrir pour vous assurer que permettant d’établir des sessions PowerShell à distance.

### <a name="ssl-certificate-requirements"></a>Configuration requise des certificats SSL
**Important :** il est fortement recommandé d’utiliser le même certificat SSL sur tous les nœuds de votre batterie de serveurs AD FS, ainsi que tous les serveurs proxy Application Web.

- Le certificat doit être un X509 certificat.
- Vous pouvez utiliser un certificat auto-signé sur les serveurs de fédération dans un environnement de test. Toutefois, pour un environnement de production, nous vous recommandons d’obtenir le certificat à partir d’une autorité de certification publique.
    - Si vous utilisez un certificat qui n’est pas confiance publique, assurez-vous que le certificat est installé sur chaque serveur Proxy de l’Application Web est fiable sur le serveur local et sur tous les serveurs de fédération
- L’identité du certificat doit correspondre au nom de service de fédération (par exemple, sts.contoso.com).
    - L’identité est soit une extension de nom remplacement (SAN) objet de type NomDNS ou, s’il n’y a aucune entrée SAN, le nom de sujet spécifié comme un nom commun.  
    - Plusieurs entrées SAN peuvent être présentes dans le certificat, à condition qu’un d’eux correspond au nom de service de fédération.
    - Si vous envisagez d’utiliser l’espace de travail rejoindre, un SAN supplémentaire est requis avec la valeur **enterpriseregistration.** suivi par le suffixe de nom d’utilisateur Principal (UPN) de votre organisation, par exemple, **enterpriseregistration.contoso.com**.
- Les certificats basés sur CryptoAPI prochaine génération (CNG) clés et les fournisseurs de stockage de clés ne sont pas pris en charge. Cela signifie que vous devez utiliser un certificat basé sur un fournisseur (fournisseur de services de chiffrement) et non un KSP (fournisseur de stockage de clé).
- Certificats avec des caractères génériques sont pris en charge.

### <a name="name-resolution-for-federation-servers"></a>Résolution de noms pour les serveurs de fédération
- Configurer les enregistrements DNS pour le nom de service de fédération AD FS (par exemple, sts.contoso.com) pour l’intranet (votre serveur DNS interne) et l’extranet (public DNS via votre enregistrement de domaines). Pour l’intranet DNS enregistrement assurer que vous utilisez A enregistrements et pas les enregistrements CNAME. Cela est nécessaire pour l’authentification windows fonctionner correctement à partir de votre ordinateur joints de domaine.
- Si vous déployez plusieurs serveur AD FS ou serveur Proxy de l’Application Web, puis vérifiez que vous avez configuré votre équilibrage de charge et que les enregistrements DNS pour le nom de service de fédération AD FS (par exemple sts.contoso.com) pointent vers l’équilibrage de charge.
- Authentification windows pour fonctionne pour les applications de navigateur à l’aide d’Internet Explorer dans votre intranet, vérifiez que le nom de service de fédération AD FS (par exemple sts.contoso.com) est ajouté à la zone intranet dans Internet Explorer. Cela peut être contrôlée via la stratégie de groupe et déployée sur tous les ordinateurs de votre domaine joint.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect prenant en charge des composants
Voici une liste des composants Azure AD Connect installée sur le serveur où Azure AD Connect est installé. Cette liste est une installation Express base.  Si vous choisissez d’utiliser un autre serveur SQL Server dans la page de services de synchronisation installer, puis SQL Express LocalDB n’est pas installé localement.

- Azure AD Connect d’intégrité
- Assistant Microsoft Online Services se connecter pour les informaticiens (installé mais aucune dépendance dessus)
- Utilitaires de ligne de commande Microsoft SQL Server 2012
- Microsoft SQL Server 2012 Express LocalDB
- Microsoft SQL Server 2012 Native Client
- Microsoft Visual C++ 2013 le redistribuer Package

## <a name="hardware-requirements-for-azure-ad-connect"></a>Configuration matérielle requise pour Azure AD Connect
Le tableau ci-dessous indique la configuration minimale requise pour l’ordinateur de synchronisation Azure AD Connect.

| Nombre d’objets dans Active Directory | PROCESSEUR | Mémoire | Taille du disque dur |
| ------------------------------------- | --- | ------ | --------------- |
| Moins de 10 000 | 1,6 GHz | 4 GO | 70 GO |
| 10 000 – 50 000 | 1,6 GHz | 4 GO | 70 GO |
| 50 000 ou 100 000 | 1,6 GHz | 16 GO | 100 GO |
| Pour les objets 100 000 ou plus la version complète de SQL Server est requise|  |  |  |
| 100 000 et 300 000 | 1,6 GHz | 32 GO | 300 GO |
| 300 000 – 600 000 | 1,6 GHz | 32 GO | 450 GO |
| Plus de 600 000 | 1,6 GHz | 32 GO | 500 GO |

La configuration minimale requise pour les ordinateurs exécutant AD FS ou serveurs d’applications Web est la suivante :

- Processeur : Double essentielles 1,6 GHz ou supérieur
- MÉMOIRE : 2 Go ou une version ultérieure
- Machine virtuelle Azure : Configuration de A2 ou une version ultérieure

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
