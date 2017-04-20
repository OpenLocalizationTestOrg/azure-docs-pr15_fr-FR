<properties
    pageTitle="Quel est l’inscription en libre-service à Azure ? | Microsoft Azure"
    description="Une vue d’ensemble libre-service l’inscription à Azure, comment gérer le processus d’inscription et à prendre le contrôle un nom de domaine DNS."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/23/2016"
    ms.author="curtand"/>


# <a name="what-is-self-service-signup-for-azure"></a>Quel est l’inscription en libre-service à Azure ?

Cette rubrique explique la procédure d’inscription libre-service et comment prendre le contrôle un nom de domaine DNS.  

## <a name="why-use-self-service-signup"></a>Pourquoi utiliser inscription libre-service ?

- Obtenir des clients aux services qu’ils souhaitent plus rapidement.
- Créer des offres basées sur le courrier électronique d’un service.
- Créer des flux de l’inscription des e-mails qui rapidement permettent aux utilisateurs de créer des identités à l’aide de son alias de messagerie professionnel facile à mémoriser.
- Répertoires Azure non gérés peuvent être effectuées dans des répertoires gérées plus tard et être réutilisées pour d’autres services.

## <a name="terms-and-definitions"></a>Termes et définitions

+ **Inscription en libre service s’inscrire**: il s’agit de la méthode par laquelle un utilisateur s’inscrit à un service cloud et a une identité créée automatiquement dans Azure Active Directory (AD Azure) en fonction de leur domaine de messagerie.
+ **Répertoire Azure non géré**: il s’agit du répertoire où cette identité est créée. Un répertoire non géré est un qui n’a aucun administrateur global.
+ **Utilisateur vérifié messagerie**: il s’agit d’un type de compte d’utilisateur dans Active Directory Azure. Un utilisateur disposant d’une identité créée automatiquement après qui s’inscrit à une offre libre-service est connu en tant qu’utilisateur vérifié de messagerie. Un utilisateur vérifié messagerie est membre normal d’un répertoire marqué avec creationmethod = EmailVerified.

## <a name="user-experience"></a>Expérience utilisateur

Par exemple, supposons un utilisateur dont la messagerie est Dan@BellowsCollege.com reçoit les fichiers sensibles par courrier électronique. Les fichiers ont été protégés par Azure Rights Management (RMS Azure). Mais organisation de Dan, soufflets universitaire, n’a pas signé pour Azure RMS, ni il a déployé Active Directory RMS. Dans ce cas, Dan peut s’inscrire pour un abonnement gratuit à RMS pour les personnes pour pouvoir lire les fichiers protégés.

Si Dan est le premier utilisateur avec une adresse de messagerie à partir de BellowsCollege.com pour vous inscrire cette libre-service offrant, puis un répertoire non géré est créé pour BellowsCollege.com dans Azure Active Directory. Si d’autres utilisateurs à partir du domaine BellowsCollege.com s’inscrire à cette offre ou d’une offre libre-service similaire, ils devront également des comptes d’utilisateur vérifié messagerie créés dans le même répertoire non géré dans Azure.

## <a name="admin-experience"></a>Expérience d’administration

Un administrateur qui possède le nom de domaine DNS d’un répertoire Azure non géré peut prendre le contrôle ou le fusionner le répertoire après prouvant la propriété. Les sections suivantes expliquent l’expérience d’administration plus en détail, mais voici un résumé :

- Lorsque vous prenez sur un répertoire Azure non géré, vous simplement Devenez l’administrateur global du répertoire non géré. Cette option est parfois appelée une prise en charge interne.
- Lorsque vous fusionnez un répertoire Azure non géré, vous ajoutez le nom de domaine DNS du répertoire non géré à votre répertoire Azure géré et un mappage des ressources de l’utilisateur est créé pour les utilisateurs peuvent continuer à accéder aux services sans interruption. Cette option est parfois appelée une prise en charge externe.

## <a name="what-gets-created-in-azure-active-directory"></a>Quel est créé dans Azure Active Directory ?

#### <a name="directory"></a>répertoire

- Un répertoire Azure Active Directory pour le domaine est créé, un répertoire par domaine.
- Le répertoire Azure AD n’a aucun administrateur général.

#### <a name="users"></a>Utilisateurs

- Pour chaque utilisateur qui s’inscrit, un objet utilisateur est créé dans le répertoire Azure AD.
- Chaque objet utilisateur est marqué comme externe.
- Chaque utilisateur dispose de l’accès au service de laquelle ils inscrit.

### <a name="how-do-i-claim-a-self-service-azure-ad-directory-for-a-domain-i-own"></a>Comment réclamer une annonce Azure libre-service répertoire pour un domaine dont vous êtes propriétaire ?

Vous pouvez demander une annonce Azure libre-service répertoire en effectuant une validation des domaines. Validation de domaine s’avère que vous êtes propriétaire du domaine en créant des enregistrements DNS.

Il existe deux façons d’effectuer une prise en charge DNS d’un répertoire Azure AD :

- prise en charge interne (administrateur découvre un répertoire Azure non géré et souhaite à transformer en un répertoire géré)
- prise en charge externes (administrateur tente d’ajouter un nouveau domaine dans leur répertoire Azure gérée)

Vous intéresser dans la validation que vous possédez un domaine parce que vous prenez sur un répertoire non géré après l’inscription libre-service exécutée par un utilisateur ou ajouter un nouveau domaine à un répertoire géré existant. Par exemple, vous avez un domaine nommé contoso.com et que vous voulez ajouter un nouveau domaine nommé contoso.co.uk ou contoso.uk.

## <a name="what-is-domain-takeover"></a>Qu’est prise en charge de domaine ?  

Cette section explique comment valider que vous êtes propriétaire d’un domaine

### <a name="what-is-domain-validation-and-why-is-it-used"></a>Qu’est une validation des domaines et pourquoi l’utiliser ?

Pour effectuer les opérations sur un répertoire, Azure AD nécessite valider la propriété du domaine DNS.  Validation du domaine vous permet de réclamer le répertoire et soit promouvoir le répertoire libre-service à un répertoire géré, ou fusionner le répertoire libre-service dans un répertoire géré existant.

## <a name="examples-of-domain-validation"></a>Exemples de validation de domaine

Il existe deux façons d’effectuer une prise en charge DNS d’un répertoire :

+ prise en charge interne (par exemple, un administrateur découvre un répertoire libre-service, non géré et souhaite à transformer en répertoire géré)
+ prise en charge externe (par exemple, un administrateur tente d’ajouter un nouveau domaine vers un répertoire géré)

### <a name="internal-takeover---promote-a-self-service-unmanaged-directory-to-be-a-managed-directory"></a>Prise en charge interne - promouvoir un répertoire non géré, libre-service pour être un répertoire géré

Lorsque vous effectuez une prise en charge interne, le répertoire obtient converti à partir d’un répertoire non géré dans un répertoire géré. Vous devez effectuer la validation de nom de domaine DNS, dans lequel vous créez un enregistrement MX ou un enregistrement TXT dans la zone DNS. Cette action :

+ Valide que vous êtes propriétaire du domaine
+ Rend le répertoire géré
+ Vous rend l’administrateur global du répertoire

Supposons qu'un administrateur informatique de soufflets universitaire découvre que les utilisateurs de l’établissement êtes inscrit à offres libre-service. Comme le propriétaire du système DNS enregistré nom BellowsCollege.com, l’administrateur informatique peut valider la propriété du nom du DNS dans Azure et se chargent ensuite de l’annuaire non géré. Le répertoire puis devient un répertoire géré, et l’administrateur informatique du rôle d’administrateur général pour le répertoire BellowsCollege.com.

### <a name="external-takeover---merge-a-self-service-directory-into-an-existing-managed-directory"></a>Prise en charge externe - fusionner un répertoire libre-service dans un répertoire géré existant

Dans une prise en charge externe, vous disposez déjà d’un répertoire géré et vous souhaitez que tous les utilisateurs et groupes à partir d’un répertoire non géré à rejoindre ce répertoire géré, plutôt que deux propre séparez les répertoires.

En tant qu’administrateur d’un répertoire géré, vous ajoutez un domaine, et ce domaine qui se passe pour avoir un répertoire non géré associé.

Par exemple, supposons que vous êtes un administrateur informatique et que vous disposez déjà d’un répertoire géré pour Contoso.com, un nom de domaine est inscrit dans votre organisation. Vous découvrez que les utilisateurs de votre organisation ont effectuées libre-service vers le haut pour une offre à l’aide de nom du domaine de messagerie user@contoso.co.uk, qui est un autre nom de domaine dont votre organisation est propriétaire. Ces utilisateurs ont actuellement des comptes dans un répertoire non géré pour contoso.co.uk.

Vous ne voulez pas gérer deux répertoires distincts, afin de vous fusionnez le répertoire non géré pour contoso.co.uk dans votre annuaire informatiques gérés existant pour contoso.com.

Prise en charge externes suit le processus de validation DNS même en tant que prise en charge interne.  Différence étant : les utilisateurs et services sont remappés à l’annuaire informatiques gérés.

#### <a name="whats-the-impact-of-performing-an-external-takeover"></a>Quel est l’impact de l’exécution d’une prise en charge externe ?

Avec une prise en charge externe, un mappage des utilisateurs aux ressources est créé afin que les utilisateurs peuvent toujours accéder aux services sans interruption. De nombreuses applications, y compris les services RMS pour les personnes, traité par le mappage des utilisateurs aux ressources, et les utilisateurs peuvent continuer à accéder à ces services sans modifier. Si une application ne traite pas efficacement le mappage des utilisateurs aux ressources, prise en charge externes peut-être être explicitement bloqué pour empêcher les utilisateurs d’une expérience médiocre.

#### <a name="directory-takeover-support-by-service"></a>prise en charge Directory prise en charge par service

Les services suivants prend actuellement en charge prise en charge :

- RMS


Les services suivants seront bientôt être prise en charge de prise en charge :

- PowerBI

Les éléments suivants ne pas et nécessitent une action d’administration supplémentaires pour migrer les données de l’utilisateur après une prise en charge externe.

- SharePoint/OneDrive


## <a name="how-to-perform-a-dns-domain-name-takeover"></a>Comment effectuer une prise en charge du nom de domaine DNS

Vous avez plusieurs options pour savoir comment effectuer une validation de domaine (et effectuez une prise en charge si vous le souhaitez) :

1.  Portail de gestion Azure

    Une prise en charge se déclenche en effectuant un ajout de domaine.  Si un répertoire existe déjà pour le domaine, vous avez la possibilité d’effectuer une prise en charge externe.

    Connectez-vous au portail Azure à l’aide de vos informations d’identification.  Accédez à votre répertoire existant, puis à **Ajouter un domaine**.

2.  Office 365

    Vous pouvez utiliser les options de la page [Gérer les domaines](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/) dans Office 365 pour gérer vos domaines et enregistrements DNS. Voir [vérifier votre domaine dans Office 365](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/).

3.  Windows PowerShell

    Les étapes suivantes sont nécessaires pour effectuer une validation à l’aide de Windows PowerShell.

    Étape    |   Applet de commande à utiliser
    ------- | -------------
    Créer un objet d’informations d’identification | Get-Credential
    Se connecter à Active Directory Azure | Se connecter MsolService
    obtenir une liste de domaines   | Get-MsolDomain
    Créer un défi  | Get-MsolDomainVerificationDns
    Créer l’enregistrement DNS   | Procédez comme suit sur votre serveur DNS
    Vérifier le défi    | MsolEmailVerifiedDomain confirmer

Par exemple :

1. Connexion à Azure Active Directory à l’aide des informations d’identification utilisées pour répondre à l’offre libre-service : module d’importation MSOnline $msolcred = get-credential connecter-msolservice-$msolcred des informations d’identification

2. Obtenir une liste de domaines :

    Get-MsolDomain

3. Ensuite, exécutez l’applet de commande Get-MsolDomainVerificationDns pour créer un défi :

    Get-MsolDomainVerificationDns-DomainName *Votre_nom_de_domaine* – Mode DnsTxtRecord

    Par exemple :

    Get-MsolDomainVerificationDns-DomainName contoso.com – Mode DnsTxtRecord

4. Copiez la valeur (le défi) qui est renvoyée à partir de cette commande.

    Par exemple :

    MS = 32DD01B82C05D27151EA9AE93C5890787F0E65D9

5. Dans votre espace de noms DNS public, créez un enregistrement txt DNS qui contient la valeur que vous avez copié à l’étape précédente.

    Le nom de cet enregistrement est le nom du domaine parent, si vous créez cet enregistrement de ressource en utilisant le rôle DNS à partir de Windows Server, laissez le collage enregistrement nom vide et juste la valeur dans la zone de texte

6. Exécutez l’applet de commande confirmer MsolDomain pour vérifier le défi :

    MsolEmailVerifiedDomain confirmer - DomainName *Votre_nom_de_domaine*

    par exemple :

    MsolEmailVerifiedDomain confirmer - DomainName contoso.com

Compliqué réussie pour revenir à l’invite sans une erreur.

## <a name="how-do-i-control-self-service-settings"></a>Comment contrôler les paramètres libre-service ?

Les administrateurs ont deux contrôles libre-service aujourd'hui. Ils peuvent contrôler :

- Si les utilisateurs peuvent participer à l’annuaire par courrier électronique.
- Les utilisateurs peuvent ou non licence eux-mêmes pour les applications et services.


### <a name="how-can-i-control-these-capabilities"></a>Comment puis-je contrôler ces fonctionnalités ?

Un administrateur peut configurer ces fonctionnalités à l’aide de ces paramètres de l’applet de commande Set-MsolCompanySettings Azure AD :

+ **AllowEmailVerifiedUsers** détermine si un utilisateur peut créer ou participer à un répertoire non géré. Si vous définissez ce paramètre sur $false, aucun utilisateurs vérifié messagerie ne peuvent participer à l’annuaire.
+ **AllowAdHocSubscriptions** contrôle la possibilité pour les utilisateurs à effectuer libre-service vers le haut. Si vous définissez ce paramètre sur $false, sans les utilisateurs peuvent effectuer l’inscription libre-service.


### <a name="how-do-the-controls-work-together"></a>Comment les contrôles fonctionnent ensemble ?

Ces deux paramètres peuvent être utilisés conjointement pour définir un contrôle plus précis sur connexion en libre service vers le haut. Par exemple, la commande suivante permet aux utilisateurs d’exécuter connexion en libre service, mais uniquement si les utilisateurs ont déjà un compte dans Azure AD (autrement dit, les utilisateurs devront un compte de messagerie-vérifié à créer ne peut pas effectuer un libre-service vers le haut) :

    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

Le diagramme suivant explique les différentes combinaisons de ces paramètres et les conditions qui en résulte de l’annuaire et connexion en libre service vers le haut.

![][1]

Pour plus d’informations et d’exemples d’utilisation de ces paramètres, voir [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx).

## <a name="see-also"></a>Voir aussi

-  [Comment installer et configurer PowerShell Azure](../powershell-install-configure.md)

-  [PowerShell Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx)

-  [Azure référence applet de commande](https://msdn.microsoft.com/library/azure/jj554330.aspx)

-  [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
