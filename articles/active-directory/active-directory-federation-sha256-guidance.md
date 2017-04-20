<properties
    pageTitle="Algorithme signature à modifier pour l’approbation des parties de la réponse Office 365 | Microsoft Azure"
    description="Cette page fournit des instructions pour la modification de ça algorithme pour approbation de fédération avec Office 365"
    keywords="SHA1, SHA256, Office 365, fédération, aadconnect, adfs, ad fs, modification ça, approbation de fédération, vous appuyer approbation fête"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="samueld"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/01/2016"
    ms.author="anandy"/>

# <a name="change-signature-hash-algorithm-for-office-365-replying-party-trust"></a>Modifier l’algorithme de signature pour Office 365 réponse approbation tiers

## <a name="overview"></a>Vue d’ensemble

Azure Active Directory Federation Services (AD FS) signe ses jetons à Microsoft Azure Active Directory pour vous assurer qu’ils ne peuvent pas être falsifiés. Cette signature peut être basée sur SHA1 ou SHA256. Azure Active Directory prend désormais en charge les jetons connectés avec un algorithme SHA256 et nous vous recommandons de définir l’algorithme de signature de jetons à SHA256 le plus haut niveau de sécurité. Cet article décrit les étapes nécessaires pour définir l’algorithme de signature de jetons à la plus sûre SHA256 niveau.

## <a name="change-the-token-signing-algorithm"></a>Modifier l’algorithme de signature de jetons

Une fois que vous avez défini l’algorithme de signature avec l’un des deux processus ci-dessous, AD FS signe les jetons pour Office 365 lieu d’utiliser des parties d’approbation avec SHA256. Vous n’avez pas besoin d’apporter des modifications de configuration supplémentaire, et cette modification n’a aucun impact sur votre capacité à accéder à Office 365 ou autres applications Azure AD.

### <a name="ad-fs-management-console"></a>Console de gestion AD FS

1. Ouvrez la console de gestion des AD FS sur le serveur ADFS principal.
2. Développez le nœud AD FS et cliquez sur **Approbations des parties de confiance**.
3. Avec le bouton droit de votre Office 365/Azure confiance fête gestion de la confidentialité, puis sélectionnez **Propriétés**.
4. Sélectionnez l’onglet **Avancé** , puis sélectionnez l’algorithme de hachage sécurisé SHA256.
5. Cliquez sur **OK**.

![Algorithme de signature SHA256--MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Applets de commande AD FS PowerShell

1. Sur n’importe quel serveur AD FS, ouvrez PowerShell sous des privilèges d’administrateur.
2. Définir l’algorithme de hachage sécurisé à l’aide de l’applet de commande **Set-AdfsRelyingPartyTrust** .

 <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Lire également

* [Réparer la confidentialité d’Office 365 avec Azure AD Connect](./active-directory-aadconnect-federation-management.md#repairing-the-trust)
