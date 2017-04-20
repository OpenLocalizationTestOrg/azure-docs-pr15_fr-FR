<properties
    pageTitle="Conseils de renouvellement pour les utilisateurs d’Office 365 et Azure Active Directory de certificats | Microsoft Azure"
    description="Cet article explique son objectif aux utilisateurs d’Office 365 comment résoudre les problèmes avec les messages électroniques comportant les informer de renouvellement d’un certificat."
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
    ms.date="08/08/2016"
    ms.author="billmath"/>


# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Renouveler des certificats de la fédération pour Office 365 et Azure Active Directory

##<a name="overview"></a>Vue d’ensemble

Pour la fédération réussie entre Azure Active Directory (AD Azure) et les Services de fédération Active Directory (AD FS), les certificats utilisés par les AD FS se jetons de sécurité à Azure AD doivent correspondre à ce qui est configuré dans Azure AD. Toute incompatibilité peut entraîner une approbation rompue. Azure AD garantit que ces informations sont synchronisées lorsque vous déployez AD FS et Proxy de l’Application Web (pour l’accès extranet).

Cet article fournit des informations supplémentaires pour gérer vos certificats de signature de jetons et les garder synchronisé avec Azure AD, dans les cas suivants :

* Vous ne déployez pas le Proxy d’Application Web, et par conséquent, les métadonnées de fédération ne sont pas disponible dans l’extranet.
* Vous n’utilisez pas la configuration par défaut de AD FS pour certificats de signature de jetons.
* Vous utilisez un fournisseur d’identité tiers.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Configuration par défaut de AD FS pour certificats de signature de jetons

La signature des jetons et du jeton déchiffrement des certificats sont généralement auto-signé certificats et conviennent d’un an. Par défaut, AD FS inclut un processus de renouvellement automatique appelé **AutoCertificateRollover**. Si vous utilisez AD FS 2.0 ou version ultérieure, Office 365 et Azure AD automatiquement à jour votre certificat avant son expiration.

### <a name="renewal-notification-from-the-office-365-portal-or-an-email"></a>Notification de renouvellement à partir du portail Office 365 ou un message électronique

>[AZURE.NOTE] Si vous avez reçu un message électronique ou une notification portail vous invitant à renouveler votre certificat pour Office, voir [Gestion des modifications apportées à des certificats de signature de jetons](#managecerts) pour vérifier si vous avez besoin d’aucune action. Microsoft connaît un problème possible peut affecter les notifications de renouvellement de certificat envoyé, même lorsqu’aucune action n’est requise.

Azure AD tente de surveiller les métadonnées de fédération et mettre à jour le jeton de certificats de signature comme indiqué par ces métadonnées. 30 jours avant la date d’expiration du jeton de certificats, de signature de Azure AD vérifie si les nouveaux certificats sont disponibles par l’interrogation les métadonnées de fédération.

* Si elle peut correctement interroger les métadonnées de fédération et récupérer les nouveaux certificats, aucune notification par courrier électronique ou un avertissement dans le portail Office 365 n’est délivré à l’utilisateur.
* Si elle ne peut pas extraire le nouveau jeton certificats de signature, soit parce que les métadonnées de fédération ne sont pas accessible ou survol certificat automatique n’est pas activé, Azure AD émet une notification par courrier électronique et un avertissement s’affiche dans le portail Office 365.

![Notification du portail Office 365](./media/active-directory-aadconnect-o365-certs/notification.png)

>[AZURE.IMPORTANT] Si vous utilisez AD FS, pour assurer la continuité, vérifiez que vos serveurs d’ont les mises à jour suivants pour que les échecs d’authentification pour des problèmes connus n’apparaissent pas. Cela permet d’atténuer problèmes connus de serveur du proxy AD FS pour cette renouvellement et les périodes de renouvellement futures :
>
>Server 2012 R2 - [Windows Server Report mai 2014](http://support.microsoft.com/kb/2955164)
>
>Server 2008 R2 et 2012 - [authentification par le biais proxy ne fonctionne pas dans Windows Server 2012 ou Windows 2008 R2 SP1](http://support.microsoft.com/kb/3094446)

## Vérifier si les certificats doivent être mis à jour<a name="managecerts"></a>

### <a name="step-1-check-the-autocertificaterollover-state"></a>Étape 1 : Vérifier l’état de AutoCertificateRollover

Sur votre serveur AD FS, ouvrez PowerShell. Vérifiez que la valeur AutoCertificateRollover est définie sur True.

    Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

[AZURE.NOTE] Si vous utilisez AD FS 2.0, tout d’abord exécuter Microsoft.Adfs.Powershell Add-Pssnapin.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Étape 2 : Vérifiez que AD FS et Azure AD sont synchronisés

Sur votre serveur AD FS, ouvrez l’invite Azure AD PowerShell et se connecter à Azure AD.

>[AZURE.NOTE] Vous pouvez télécharger Azure AD PowerShell [ici](https://technet.microsoft.com/library/jj151815.aspx).

    Connect-MsolService

Vérifiez les certificats configurés dans AD FS et Azure AD approuver des propriétés pour le domaine spécifié.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

Si les empreintes dans les deux sorties correspondent, vos certificats sont synchronisés avec Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Étape 3 : Vérifier si votre certificat est sur le point d’expirer

Dans le résultat de Get-MsolFederationProperty ou Get-AdfsCertificate, recherchez la date sous « Non après ». Si la date est inférieure à 30 jours, vous devez prendre action.

| AutoCertificateRollover | Certificats synchronisés avec Azure AD | Métadonnées de fédération sont accessible au public | Validité | Action |
|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|
| Oui | Oui | Oui | - | Aucune action n’est nécessaire. Voir [jeton de renouveler automatiquement le certificat de signature](#autorenew). |
| Oui | N°  | - | Inférieur à 15 jours | Renouveler immédiatement. Voir [jeton renouveler manuellement le certificat de signature](#manualrenew). |
| N° | - | - | Moins de 30 jours | Renouveler immédiatement. Voir [jeton renouveler manuellement le certificat de signature](#manualrenew). |

\[-] N’a pas d’importance

## Renouveler le jeton automatiquement le certificat de signature (recommandé)<a name="autorenew"></a>

Vous n’avez pas besoin d’effectuer les étapes manuelles si les deux actions suivantes sont remplies :
- Vous avez déployé Proxy d’Application Web, qui permet d’accéder aux métadonnées de fédération à partir de l’extranet.
- Vous utilisez la configuration par défaut AD FS (AutoCertificateRollover est activée).

Vérifiez les points suivants pour confirmer que le certificat pouvant être mis à jour automatiquement.

**1. la propriété AD FS AutoCertificateRollover doit être définie sur True.** Cela indique que AD FS génère automatiquement nouvelle signature des jetons, les certificats déchiffrement jeton, avant de l’ancien celles arriver à expiration.

**2. les métadonnées de fédération AD FS sont accessibles au public.** Vérifiez que les métadonnées de votre fédération sont accessibles au public en accédant à l’URL suivante à partir d’un ordinateur sur l’internet public (sortie du réseau d’entreprise) :


/federationmetadata/2007-06/federationmetadata.xml https:// (your_FS_name)

où `(your_FS_name) `est remplacé par le nom d’hôte service fédération votre organisation utilise, par exemple fs.contoso.com.  Si vous êtes en mesure de vérifier les deux de ces paramètres avec succès, il est inutile rien à faire.  

Exemple : https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml

## Renouveler le jeton manuellement le certificat de signature<a name="manualrenew"></a>

Vous pouvez choisir de renouveler le jeton de certificats de signature manuellement. Par exemple, les scénarios suivants peuvent fonctionner mieux pour le renouvellement manuel :
* Jetons de certificats de signature ne sont pas auto-signé certificats. Le plus souvent pour cela est que votre organisation gère les certificats AD FS inscrites à partir d’une autorité de certification d’organisation.
* Sécurité réseau n’autorise pas les métadonnées de fédération soit disponible publiquement.

Dans ces scénarios, chaque fois que vous mettez à jour les certificats de signature de jetons vous devez également mettre à jour votre domaine Office 365 à l’aide de la commande PowerShell, mise à jour-MsolFederatedDomain.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Étape 1 : Assurez-vous que AD FS a nouveau jeton de certificats de signature

**Configuration non standard**

Si vous utilisez une configuration ceux définis par défaut de AD FS (dont **AutoCertificateRollover** est défini sur **False**), vous utilisez probablement certificats personnalisés (ne pas auto-signé). Pour plus d’informations sur comment renouveler les certificats de signature de jetons AD FS, voir [conseils pour les clients sans utiliser AD FS auto-signé certificats](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Métadonnées de fédération ne sont pas accessible au public**

En revanche, si **AutoCertificateRollover** est défini sur **True**, mais les métadonnées de votre fédération ne sont pas accessible au public, tout d’abord Assurez-vous que nouveaux certificats de signature de jetons ont été générés par AD FS. Confirmer que vous avez nouveau jeton de certificats de signature en procédant comme suit :

1. Vérifiez que vous êtes connecté au serveur AD FS principal.
2. Vérifier les certificats de signature actuelles dans AD FS en ouvrant une fenêtre de commande PowerShell et en exécutant la commande suivante :

    \>signature de jetons Get-ADFSCertificate – CertificateType

    >[AZURE.NOTE] Si vous utilisez AD FS 2.0, vous devez exécuter tout d’abord Microsoft.Adfs.Powershell Add-Pssnapin.

3. Examinez le résultat de la commande à tous les certificats répertoriés. Si AD FS a généré un nouveau certificat, vous devriez voir deux certificats dans le résultat : une pour laquelle la valeur **IsPrimary** est **vraie** et la date **NotAfter** est sous 5 jours, et l’autre pour laquelle **IsPrimary** est **faux** et **NotAfter** sur une année à l’avenir.

4. Si vous ne voyez qu’un certificat et la date **NotAfter** est sous 5 jours, vous devez générer un nouveau certificat.

5. Pour générer un nouveau certificat, exécutez la commande suivante à l’invite PowerShell : `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.

6. Vérifier la mise à jour en exécutant la commande suivante à nouveau : PS C:\>signature de jetons Get-ADFSCertificate – CertificateType

Deux certificats doivent être répertoriés maintenant, d'entre eux possède une date **NotAfter** d’environ un an et dont la valeur **IsPrimary** est **fausse**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Étape 2 : Mettre à jour le nouveau jeton de signature de certificats pour la gestion de la confidentialité Office 365

Mettre à jour d’Office 365 avec le jeton nouvelle signature certificats à utiliser pour la gestion de la confidentialité, comme suit.

1.  Ouvrez le Module Active Directory Microsoft Azure pour Windows PowerShell.
2.  Exécuter $cred = Get-Credential. Lorsque cette applet de commande vous demande des informations d’identification, tapez vos informations d’identification de cloud service administrateur compte.
3.  Exécuter Connect MsolService – $cred des informations d’identification. Cette applet de commande vous connecte au service cloud. Création d’un contexte qui vous connecte au service cloud est nécessaire avant d’exécuter des applets de commande supplémentaires installés par l’outil.
4.  Si vous exécutez ces commandes sur un ordinateur qui n’est pas le serveur principal de fédération AD FS, exécutez Set-MSOLAdfscontext-ordinateur <AD FS primary server>, où <AD FS primary server> est le nom de domaine complet interne du serveur AD FS principal. Cette applet de commande crée un contexte qui vous connecte à AD FS.
5.  Exécuter la mise à jour-MSOLFederatedDomain-DomainName <domain>. Cette applet de commande met à jour les paramètres de AD FS dans le service cloud et configure la relation d’approbation entre les deux.


>[AZURE.NOTE] Si vous avez besoin prendre en charge plusieurs domaines de niveau supérieur, par exemple contoso.com et fabrikam.com, vous devez utiliser le commutateur **SupportMultipleDomain** avec les applets de commande. Pour plus d’informations, voir [prise en charge de plusieurs domaines de premier niveau](active-directory-aadconnect-multiple-domains.md).

## Réparer approbation Azure AD à l’aide de Azure AD Connect<a name="connectrenew"></a>

Si vous avez configuré votre batterie de serveurs AD FS et approbation Azure AD à l’aide de Azure AD Connect, vous pouvez utiliser Azure AD Connect pour détecter si vous devez installer aucune action pour vos certificats de signature de jetons. Si vous avez besoin renouveler les certificats, vous pouvez utiliser Azure AD Connect pour le faire.

Pour plus d’informations, voir [la réparation de la gestion de la confidentialité](./active-directory-aadconnect-federation-management.md#repairing-the-trust).
