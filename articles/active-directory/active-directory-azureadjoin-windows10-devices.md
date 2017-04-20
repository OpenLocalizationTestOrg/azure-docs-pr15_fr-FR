<properties
    pageTitle="À l’aide d’appareils Windows 10 dans votre espace de travail | Microsoft Azure"
    description="Fournit un instantané de fonctionnalités pour les utilisateurs et informatiques, contraste les différentes façons un appareil peut être mis en service et utilisé dans une entreprise avec Windows 10."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="femila"/>

# <a name="using-windows-10-devices-in-your-workplace"></a>À l’aide d’appareils Windows 10 dans votre espace de travail

S’applique à : PC Windows 10

Windows 10 propose trois modèles pour les organisations qui permettent aux utilisateurs d’accéder à des ressources de travail de façon sécurisée et pratique.

- **Participer à Azure Active Directory** (Azure AD jonction) pour les travailleurs qui accéder aux ressources tels que Office 365 principalement dans le cloud. Azure AD jointure est travail libre-service mise en service expérience est les nouveautés dans Windows 10.
- **Jonction de domaine**, les organisations qui ont des investissements dans les applications en local et des ressources. Jonction de domaine offre une expérience améliorée dans Windows 10 lorsque connecté à Azure AD.
- **Une nouvelle expérience BYOD simplifiée**, pour les utilisateurs qui souhaitent ajouter un compte professionnel ou scolaire Windows et facilement accéder à des ressources sur les appareils personnels.

Le tableau suivant présente un instantané de fonctionnalités pour les utilisateurs et les administrateurs informatiques, avec contraste les différentes façons un appareil peut être mis en service et utilisé dans une entreprise avec Windows 10 :

|                                                                                                                                                                 | Jonction de domaine     | Azure AD jointure | APPAREIL personnel |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|---------------|-----------------|
| Windows appareil connexion pour les comptes professionnel ou scolaire.                                                                                                                      | Oui             | Oui           | N°              |
| Utilisateur unique-authentification (SSO) aux applications Office 365 et Azure AD. L’authentification unique est la capacité à se connecter une seule fois accéder aux ressources de l’organisation. | Oui             | Oui           | Oui             |
| Utilisateur SSO aux applications Kerberos/NTLM.                                                                                                                                  | Oui             | Limité       | Oui, via VPN         |
| L’autorisation forte et connexion pratique pour les comptes professionnel ou scolaire avec Microsoft Passport et Windows Hello.                                                                   | Oui             | Oui           | Oui             |
| Accès aux entreprise du Windows Store avec un compte professionnel ou scolaire (et non un compte Microsoft).                                                                                    | Oui             | Oui           | Oui             |
| Compatible entreprise d’itinérance des paramètres utilisateur sur tous les appareils avec des comptes scolaire ou Professionnel.                                                                                 | Oui             | Oui           | Oui             |
| La possibilité de limiter l’accès aux applications d’organisation aux périphériques qui sont compatibles avec les stratégies d’unité d’organisation.                                                      | Oui             | Oui           | Oui             |
| Utilisateur libre-service mise en service des appareils pour « travail où que vous soyez. »                                                                                                | N°              | Oui           | Oui             |
| Possibilité de gestion des appareils.                                                                                                                                       | Oui, via GP/SCCM | Oui           | Oui             |

## <a name="use-work-owned-devices-with-azure-ad-join-and-domain-join-in-windows-10"></a>Utilisation des appareils appartenant à travail avec Azure AD jointure et le domaine rejoindre dans Windows 10

Windows 10 propose des périphériques appartenant à travail accéder aux ressources de travail de deux façons :

- Azure AD jointure
- Jonction de domaine

 Deux peuvent être possibles selon vos besoins et configuration requise d’une organisation. Dans certains cas, les organisations peuvent bénéficier d’activation de ces deux méthodes de déploiement.

## <a name="when-to-use-azure-active-directory-join"></a>Quand utiliser Azure Active Directory rejoindre

Azure AD jointure est un nouveau travail libre-service mise en service expérience dans Windows 10.  Il est destiné aux travailleurs qui accèdent à des ressources de travail tels que Office 365 principalement dans le cloud. C’est un moyen léger pour configurer des ordinateurs, tablettes et téléphones pour l’entreprise. Appareils sont gérés via la gestion des appareils mobiles, à l’aide de contrôles cohérentes plateformes de Windows.

**Utiliser Azure AD participation à une des raisons suivantes**:

- Vous souhaitez activer l’attribution en libre-service des périphériques pour les travailleurs en déplacement.
- Vous fournissez aux utilisateurs appartenant à travail les appareils mobiles tels que les tablettes et téléphones.
- Vous voulez gérer un ensemble d’utilisateurs dans Azure AD plutôt que dans Active Directory, tels que saisonnier, sous-traitants ou les étudiants.
- Vous voulez fournir des fonctionnalités jonction pour les travailleurs dans vos sites distants avec infrastructure limitée en local.
- Vous n’avez pas un locale d’Active Directory.

Certaines organisations utilise Azure AD joindre en tant que le moyen principal de déployer des périphériques appartenant à travail, particulièrement lors de la migration tout ou partie de leurs ressources dans le cloud. Organisations hybride avec Active Directory et Azure AD peuvent également choisir déployer une méthode ou une autre en fonction de l’utilisateur ou le service.

Quels scolaire et universités, par exemple, peuvent gérer personnel dans Active Directory et les étudiants dans Azure Active Directory. Certaines sociétés souhaiterez gérer les sites distants ou départements des ventes dans Azure Active Directory. Azure AD jointure et méthodes de jointure domaine peuvent servir au sein d’une organisation hybride. Azure AD jointure peut servir à compléter une jointure domaine pour le déploiement de périphériques dans un environnement de travail.

**Si l’accès aux ressources d’entreprise plus habituel est via le cloud, votre organisation peut tirer parti des avantages supplémentaires si**:

- Vous pouvez supprimer des dépendances sur infrastructure d’identité local.
- Vous pouvez simplifier votre modèle de déploiement appareils, prise en s’éloignant de solutions d’image en autorisant configuration libre-service.
- Gestion des appareils mobiles vous permet de gérer tous vos appareils sur différentes plateformes.

Pour plus d’informations sur Azure AD rejoindre, voir [fonctionnalités de cloud extension sur les appareils Windows 10 par le biais Azure Active Directory rejoindre](active-directory-azureadjoin-overview.md).

## <a name="when-to-use-domain-join-or-keep-using-it"></a>Quand utiliser jointure domaine (ou conserver l’utiliser)

Pour les 15 dernières années, de nombreuses organisations ont utilisés jonction de domaine pour connecter des périphériques de travail. Il permet aux utilisateurs de se connecter à leur appareil avec leur travail Active Directory ou scolaire. Permet également de jonction de domaine IT de manière centralisée et entièrement gérer les appareils suivants. Organisations s’appuient généralement sur les méthodes d’images sur les appareils de mise en service et utilisent souvent System Center Configuration Manager (SCCM) ou groupe de stratégie pour gérer les.

**Votre entreprise doit utiliser jointure domaine (ou conserver l’utiliser) pour une des raisons suivantes**:

- Vous avez applications Win32 déployées sur les appareils suivants qui utilisent NTLM/Kerberos.
- Vous avez besoin GP ou SCCM/DCM pour gérer les appareils mobiles.
- Vous voulez continuer à utiliser les solutions d’image pour configurer les périphériques pour vos employés.

**Jonction de domaine dans Windows 10 vous offre également les avantages suivants lorsque connecté à Azure AD**:

- L’authentification renforcée liées aux périphériques et pratique connexion pour les comptes professionnel ou scolaire avec Microsoft Passport et Windows Hello.
- Accès à l’entreprise du Windows Store pour appareils mobiles qui utilisent Professionnel ou scolaire (aucun compte Microsoft requis).
- Compatible entreprise d’itinérance des paramètres utilisateur sur tous les appareils qui utilisent des comptes professionnel ou scolaire (aucun compte Microsoft requis).
- La possibilité de limiter l’accès aux applications d’organisation aux périphériques qui sont compatibles avec les stratégies d’unité d’organisation.

Pour plus d’informations sur Azure AD rejoindre, voir [fonctionnalités de cloud extension sur les appareils Windows 10 par le biais Azure Active Directory rejoindre](active-directory-azureadjoin-overview.md).

## <a name="enable-joining-of-personally-owned-devices-for-work-or-school"></a>Activer la participation à des appareils personnels pour professionnel ou scolaire

Pour prendre en charge BYOD dans l’entreprise, Windows 10 donne la possibilité d’ajouter un compte professionnel ou scolaire à leur ordinateur, une tablette ou un téléphone à l’utilisateur. Une fois que l’utilisateur ajoute un compte professionnel ou scolaire, l’appareil est inscrit avec Azure AD et éventuellement inscrit dans le système de gestion de l’appareil mobile l’organisation a configuré. Le répertoire vous montrent les appareils suivants en tant que « Enregistré » par rapport à « Azure AD rejoint ». INFORMATIQUE administraors pouvez appliquer des stratégies différentes en fonction de ces informations, fournir une touche plus claire sur les périphériques personnels que sur les appareils appartenant à travail si vous le souhaitez.

Les utilisateurs peuvent ajouter un compte ou scolaire à leur appareil personnel très facilement. Cela, ils peuvent s’ils accèdent à une application de travail pour la première fois ou qu’il peut le faire manuellement via le menu Paramètres. Ce compte fournira l’authentification unique aux ressources de l’organisation.

Pour plus d’informations sur Azure AD rejoindre, voir [se connecter à un domaine appareils à Azure Active Directory pour Windows 10 des expériences](active-directory-azureadjoin-devices-group-policy.md).

## <a name="enable-domain-join-or-azure-ad-join"></a>Activer la jointure domaine ou Azure AD

Toutes les méthodes décrites précédemment (jonction de domaine, Azure AD rejoindre et ajouter travail compte ou scolaire) ont des points d’entrée de l’expérience utilisateur Windows 10. Toutefois, toutes les requièrent un administrateur informatique activer la fonctionnalité de l’infrastructure avant de l’expérience est prises en charge.

## <a name="requirements-for-deploying-azure-ad-join"></a>Configuration requise pour le déploiement d’Azure AD rejoindre

Pour déployer Azure AD joindre pour tous les ensembles d’utilisateurs, vous devez les éléments suivants :

- Un abonnement Azure AD.
- Un abonnement Azure AD Premium, par exemple mobile device management inscription automatique, si vous avez besoin de davantage de fonctionnalités.
- Gestion des appareils mobiles, par exemple, un abonnement Microsoft Intune, gestion des périphériques mobiles pour Office 365 ou un fournisseur de gestion d’appareil mobile partenaire qui s’intègrent à Azure AD. (Voir la [section Forum aux questions](#frequently-asked-questions) à la fin de cet article pour plus d’informations).

Si vos installations sont hybride, que nous recommandons vivement que vous déployez Azure AD Connect pour développer le répertoire local dans Azure AD.

## <a name="requirements-for-using-domain-join-with-azure-ad"></a>Configuration requise pour utiliser le domaine participer avec Azure AD

Jonction de domaine continue à fonctionner comme il a toujours. Toutefois, pour obtenir les avantages Azure AD, vous devez les éléments suivants :

- Un abonnement Azure AD.
- Un déploiement d’Azure AD Connect pour étendre le répertoire local vers Azure Active Directory.
- Une stratégie qui permet aux périphériques à un domaine à conditionnelle accéder à Azure AD.
- Une stratégie qui autorise l’accès aux appareils « à un domaine » si vous voulez être en mesure de limiter l’accès pour certains appareils.
- Gestionnaire de Configuration système Centre version 1509 pour Technical Preview, pour activer des règles pour demander une périphériques compatibles. (Voir la documentation de TechNet et billet de blog).

Pour plus d’informations sur la jointure de domaines dans Windows 10, voir [se connecter à un domaine appareils à Azure Active Directory pour Windows 10 expériences](active-directory-azureadjoin-devices-group-policy.md)

## <a name="requirements-for-using-byod-and-add-a-work-or-school-account"></a>Configuration requise pour utiliser BYOD et « Ajouter un compte professionnel ou scolaire »

Pour activer « mettre votre propre périphérique » (BYOD) avec les comptes scolaire ou Professionnel, vous devez les éléments suivants :

- Un abonnement Azure AD.
- Un abonnement Azure AD Premium, par exemple mobile device management inscription automatique, si vous avez besoin de davantage de fonctionnalités.

## <a name="requirements-for-using-microsoft-passport"></a>Configuration requise pour utiliser Microsoft Passport

Pour permettre à Microsoft Passport, vous devez les éléments suivants :

- Une infrastructure à clé publique (infrastructure de clé publique) pour la prise en charge de l’authentification basée sur le certificat qui utilise Microsoft Passport.
- Un abonnement Intune pour la prise en charge de l’authentification basée sur le certificat qui utilise Microsoft Passport pour Azure AD joindre et comptes scolaire ou Professionnel.
- Gestionnaire de Configuration de System Center version 1509 pour Technical Preview (voir la documentation de TechNet et le billet de blog) pour la prise en charge de l’authentification basée sur le certificat qu’utilise Microsoft Passport pour jonction de domaine.
- Une stratégie d’activation de Microsoft Passport dans l’organisation.

Comme alternative à l’aide d’une infrastructure à clé publique, vous pouvez activer basé sur des clés Microsoft Passport en procédant comme suit :

- Déployer Windows Server 2016 « Production Preview 1 » DC (pas besoin de domaine ou une forêt niveaux fonctionnels ; plusieurs DC pour le service de redondance que suffit que chaque site Active Directory).
- Définir la stratégie à activer Microsoft Passport dans l’organisation.

Pour plus d’informations sur Microsoft Passport et Windows Hello dans Windows 10, voir < link-to-MS-Passport-and-Windows-Hello-document >.

## <a name="frequently-asked-questions"></a>Forum aux questions
### <a name="which-partner-mobile-device-management-products-integrate-with-azure-ad"></a>Quels sont les produits partenaire appareil mobile gestion intègrent Azure AD ?

Les produits des fournisseurs suivants intègrent Azure AD pour l’inscription unifiée et accès conditionnel dans Windows 10 :

- AirWatch par VMware
- Citrix Xenmobile
- Gestionnaire d’appareils mobiles Lightspeed
- Gestion des appareils mobiles SOTI en local
- MobileIron

### <a name="what-about-workplace-join-in-windows-10"></a>Qu’en est-il de poste de travail rejoindre dans Windows 10 ?
Jointure poste de travail a été utilisé pour activer BYOD Windows 8.1. Dans Windows 10, BYOD est activée via « Ajouter un compte professionnel ou scolaire » comme indiqué précédemment dans ce document. Pour les organisations qui ne s’intègrent leur gestion des appareils mobiles avec Azure Active Directory, les utilisateurs peuvent s’inscrire le périphérique en gestion manuellement via **paramètres** > **comptes** > **access de travail**.


### <a name="can-users-connect-their-microsoft-account-to-their-domain-account-in-windows-10"></a>Les utilisateurs peuvent se connecter leur propre compte Microsoft pour leur compte de domaine dans Windows 10 ?
Pas dans Windows 10. Dans Windows 8.1, les utilisateurs d’appareils à un domaine peuvent « se connecter » leur propre compte Microsoft (par exemple, Hotmail, Live, Outlook, Xbox, etc.) à leur compte de domaine pour activer certaines expériences comme l’authentification unique aux services Windows Live, l’utilisation de Windows Store et des paramètres utilisateur d’itinérance sur les périphériques. Dans Windows 10, le compte Microsoft « se connecter « fonctionnalité a été supprimé. L’utilisateur peut ajouter un ou plusieurs comptes Microsoft en tant que des comptes supplémentaires pour permettre l’authentification unique de services de fournisseurs tels que le Windows Store. Cela s’effectue dans **paramètres** > **comptes** > **votre compte**.

Les utilisateurs qui sont la mise à niveau à partir d’appareils à un domaine Windows 8.1 et qui ont leur propre compte Microsoft connecté, a automatiquement leur propre compte Microsoft connecté ajouté à la liste des comptes supplémentaires qu’ils utilisent.


## <a name="additional-information"></a>Informations supplémentaires
* [Windows 10 pour les entreprises : modalités d’utilisation des appareils pour le travail](active-directory-azureadjoin-windows10-devices-overview.md)
* [Étendre les fonctionnalités de cloud à des appareils Windows 10 par le biais Azure Active Directory rejoindre](active-directory-azureadjoin-user-upgrade.md)
* [Découvrez les scénarios d’utilisation pour Azure AD rejoindre](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Se connecter à un domaine appareils à Azure Active Directory pour Windows 10 expériences](active-directory-azureadjoin-devices-group-policy.md)
* [Configurer la Azure AD rejoindre](active-directory-azureadjoin-setup.md)
