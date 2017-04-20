<properties 
    pageTitle="Prise en main authentification par certificat sur iOS | Microsoft Azure" 
    description="Découvrez comment configurer l’authentification par certificat solutions avec des appareils iOS" 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/21/2016" 
    ms.author="markvi" />



# <a name="get-started-with-certificate-based-authentication-on-ios---public-preview"></a>Prise en main authentification par certificat sur iOS - version d’évaluation

> [AZURE.SELECTOR]
- [iOS](active-directory-certificate-based-authentication-ios.md)
- [Android](active-directory-certificate-based-authentication-android.md)


Cette rubrique vous explique comment configurer et utiliser l’authentification par certificat (CBA) sur un appareil iOS pour les utilisateurs de clients dans les plans Office 365 entreprise, entreprise et éducation. 

CBA vous permet d’être authentifié par Azure Active Directory avec un certificat client sur un appareil Android ou iOS lors de la connexion de votre compte Exchange online : 

- Applications mobiles Office tels que Microsoft Outlook et Microsoft Word   
- Clients Exchange ActiveSync (EAS) 

Configuration de cette fonctionnalité évite d’avoir à entrer une combinaison nom d’utilisateur et mot de passe dans certaines courrier et les applications Microsoft Office sur votre appareil mobile. 
 

## <a name="supported-scenarios-and-requirements"></a>Configuration requise et les scénarios pris en charge  



### <a name="general-requirements"></a>Configuration requise 


Pour tous les scénarios dans cette rubrique, les tâches suivantes sont requises :  

- Accès aux ou les autorités certificat pour émettre des certificats client.  

- L’ou les autorités certificats doit être configurée dans Azure Active Directory. Vous trouverez des instructions détaillées sur l’exécution de la configuration dans la section [Mise en route](#getting-started) .  

- Autorité de certification racine et les autorités de certification intermédiaires doivent être configurées dans Azure Active Directory.  

- Chaque autorité de certification peut comporter qu’une liste de révocation de certificats (révocation de certificats) qui peut être référencée via Internet en URL.  

- Le certificat client doit être émis pour l’authentification du client.  


- Pour des clients Exchange ActiveSync, le certificat client doit avoir adresse de messagerie prenant de l’utilisateur dans Exchange online dans le nom d’utilisateur Principal ou la valeur nom RFC822 du champ Subject Alternative Name. Azure Active Directory mappe la valeur RFC822 à l’attribut Adresse Proxy dans l’annuaire.  



### <a name="office-mobile-applications-support"></a>Prise en charge des applications mobiles Office 

| Applications                      | Prise en charge      |
| ---                       | ---          |
| Word / Excel / PowerPoint | ![Vérifier][1]  |
| OneNote                   | ![Vérifier][1]  |
| OneDrive                  | ![Vérifier][1]  |
| Outlook                   | ![Vérifier][1]  |
| Yammer                    | ![Vérifier][1]  |
| Skype entreprise        | À venir  |


### <a name="requirements"></a>Configuration requise  

La version du système d’exploitation périphérique doit être iOS 9 et versions ultérieures 

Un serveur de fédération doit être configuré.  

Azure authentificateur est requis pour les applications Office sur iOS.  

Pour Azure Active Directory révoquer un certificat client, le jeton ADFS doit avoir les affirmations suivantes :  

  - `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
(Le numéro de série du certificat client) 

  - `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
(La chaîne de l’émetteur du certificat client) 

Azure Active Directory ajoute ces réclamations au jeton d’actualisation s’ils sont disponibles dans le jeton ADFS (ou tout autre jeton SAML). Lorsque le jeton d’actualisation doit être validé, ces informations sont utilisées pour vérifier la révocation. 

Pour obtenir les meilleurs résultats, vous devez mettre à jour les pages d’erreur ADFS avec les éléments suivants :

- La configuration minimale requise pour installer l’authentificateur Azure sur iOS

- Obtenir des instructions sur comment obtenir un certificat de l’utilisateur. 

Pour plus d’informations, voir [Personnaliser les AD FS de Pages](https://technet.microsoft.com/library/dn280950.aspx).  



### <a name="exchange-activesync-clients-support"></a>Prise en charge des clients Exchange ActiveSync 


IOS 9 ou version ultérieure, le client de messagerie iOS native est pris en charge. Pour toutes les autres applications Exchange ActiveSync, pour déterminer si cette fonctionnalité est prise en charge, contactez votre développeur de l’application.  



## <a name="getting-started"></a>Prise en main 


Pour commencer, vous devez configurer les autorités de certification dans Azure Active Directory. Pour chaque autorité de certification, télécharger les éléments suivants : 

- La partie publique du certificat, au format *.cer* 

- Internet facing URL où se trouvent les listes révocation de certificats (listes de révocation)
 

Voici le schéma d’une autorité de certification : 

    class TrustedCAsForPasswordlessAuth 
    { 
       CertificateAuthorityInformation[] certificateAuthorities;    
    } 

    class CertificateAuthorityInformation 

    { 
        CertAuthorityType authorityType; 
        X509Certificate trustedCertificate; 
        string crlDistributionPoint; 
        string deltaCrlDistributionPoint; 
        string trustedIssuer; 
        string trustedIssuerSKI; 
    }                

    enum CertAuthorityType 
    { 
        RootAuthority = 0, 
        IntermediateAuthority = 1 
    } 


Pour télécharger les informations, vous pouvez utiliser le module Azure Active Directory par le biais de Windows PowerShell.  
Voici des exemples d’ajout, suppression ou modification d’une autorité de certification. 



### <a name="configuring-your-azure-ad-tenant-for-certificate-based-authentication"></a>Configuration de votre client Azure AD pour certificat authentification basée sur 

1. Démarrez Windows PowerShell en tant qu’administrateur. 

2. Installez le module Azure Active Directory. Vous devez installer la Version [1.1.143.0](http://www.powershellgallery.com/packages/AzureADPreview/1.1.143.0) ou une version ultérieure.  

        Install-Module -Name AzureADPreview –RequiredVersion 1.1.143.0 

3. Se connecter à votre client cible : 

        Connect-AzureAD 

### <a name="adding-a-new-certificate-authority"></a>Ajout d’une nouvelle autorité de certification

1. Définir diverses propriétés de l’autorité de certification et l’octroyer à Azure Active Directory : 

        $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]" 
        $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation 
        $new_ca.AuthorityType=0 
        $new_ca.TrustedCertificate=$cert 
        New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca 

5. Obtenir les autorités de certification : 

        Get-AzureADTrustedCertificateAuthority 


### <a name="retrieving-the-list-certificate-authorities"></a>Récupérer les autorités de certification de liste

Récupérer les autorités de certification actuellement stockées dans Azure Active Directory pour votre client : 

        Get-AzureADTrustedCertificateAuthority 


### <a name="removing-a-certificate-authority"></a>Suppression d’une autorité de certification

1.  Récupérer les autorités de certification : 

        $c=Get-AzureADTrustedCertificateAuthority 


2. Supprimer le certificat pour l’autorité de certification : 

        Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2] 


### <a name="modfiying-a-certificate-authority"></a>Modfiying une autorité de certification 

1.  Récupérer les autorités de certification : 

        $c=Get-AzureADTrustedCertificateAuthority 


2. Modifier les propriétés de l’autorité de certification : 

        $c[0].AuthorityType=1 

3. Définir l' **autorité de certification**: 

        Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0] 




## <a name="testing-office-mobile-applications"></a>Tester des applications mobiles Office  

Pour tester l’authentification par certificat dans votre application Office mobile : 

1.  Sur votre appareil de test, installez une application mobile Office (par exemple, OneDrive) à partir de l’App Store.

2.  Vérifiez que le certificat de l’utilisateur a été configurée pour votre périphérique de test. 

3.  Lancez l’application. 

4.  Entrez votre nom d’utilisateur, puis puis sélectionnez le certificat d’utilisateur que vous voulez utiliser. 

Vous devez être connecté avec succès. 





## <a name="testing-exchange-activesync-client-applications"></a>Tester les applications de client Exchange ActiveSync

Pour accéder à Exchange ActiveSync via l’authentification par certificat, un profil EAS contenant le certificat client doit être accessible à l’application. Le profil EAS doit contenir les informations suivantes :

- Le certificat de l’utilisateur à utiliser pour l’authentification 

- Le point de terminaison EAS doit être outlook.office365.com (tel que cette fonctionnalité est pris en charge uniquement dans l’environnement de multi-client Exchange online)

Un profil EAS peut être configuré et placé sur l’appareil par le biais d’un MDM comme Intune ou en plaçant manuellement le certificat dans le profil EAS sur l’appareil.  

### <a name="testing-eas-client-applications-on-ios"></a>Applications clientes EAS tests sur iOS 

Pour tester l’authentification par certificat avec l’application de messagerie natifs iOS 9 ou ci-dessus : 

1.  Configuration d’un profil EAS répondant aux critères ci-dessus. 

2.  Installer le profil sur l’appareil iOS (soit en utilisant un périphériques mobiles, tels que Intune ou l’application de configuration du Apple)

3.  Une fois que le profil est installé correctement, ouvrez l’application de messagerie native, puis vérifiez que courrier est en cours de synchronisation



## <a name="revocation"></a>Révocation de certificats

Pour révoquer un certificat client, Azure Active Directory extrait la liste de révocation de certificats (révocation de certificats) à partir des URL téléchargés dans le cadre des informations de l’autorité de certificats et met en cache. Publier le dernier horodatage (**Date d’effet** propriété) dans la liste de révocation est utilisé pour garantir la révocation de certificats est toujours valide. La liste de révocation est régulièrement référencée pour révoquer l’accès à des certificats qui font partie de la liste.

Si une révocation plus instantanée est nécessaire (par exemple, si un utilisateur perd un appareil), le jeton d’autorisation de l’utilisateur peut être annulée. Pour annuler le jeton d’autorisation, définir le champ **StsRefreshTokenValidFrom** pour cet utilisateur particulier à l’aide de Windows PowerShell. Vous devez mettre à jour le champ **StsRefreshTokenValidFrom** pour chaque utilisateur que vous voulez révoquer l’accès.
 
Pour vous assurer que la révocation persiste, vous devez définir la **Date d’effet** de la révocation de certificats à une date après la valeur définie par **StsRefreshTokenValidFrom** et assurer le certificat en question se trouve dans la liste de révocation.
 
Les étapes suivantes décrivent le processus de mise à jour et d’invalider le jeton d’autorisation en définissant le champ **StsRefreshTokenValidFrom** . 

1. Se connecter avec les informations d’identification d’administration du service MSOLUSER : 

        $msolcred = get-credential 
        connect-msolservice -credential $msolcred 

1.  Récupérer la valeur actuelle StsRefreshTokensValidFrom pour un utilisateur : 

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com` 
        $user.StsRefreshTokensValidFrom 


1.  Configurer une nouvelle valeur StsRefreshTokensValidFrom pour l’utilisateur égal à l’horodatage actuel : 

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")


La date que vous indiquez doit être à l’avenir. Si la date n’est pas à l’avenir, la propriété **StsRefreshTokensValidFrom** n’est pas définie. Si la date est à l’avenir, **StsRefreshTokensValidFrom** est défini à l’heure actuelle (pas la date indiquée par commande Set-MsolUser). 



<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png