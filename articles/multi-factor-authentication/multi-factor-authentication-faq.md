<properties
    pageTitle="Forum aux questions sur l’authentification multifacteur Azure"
    description="Fournit une liste des Forum aux questions et réponses liées à l’authentification multifacteur Azure. L’authentification multifacteur est une méthode de vérification de l’identité d’un utilisateur qui requiert plus de nom d’utilisateur et mot de passe. Il fournit une couche supplémentaire de sécurité aux utilisateurs de se connecter et transactions."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="kgremban"/>

# <a name="azure-multi-factor-authentication-faq"></a>Forum aux questions sur l’authentification multifacteur Azure


Ce forum aux questions répond aux questions fréquentes sur l’authentification multifacteur Azure et l’utilisation du service de l’authentification multifacteur, y compris les questions sur le modèle de facturation et de facilité d’utilisation.

## <a name="general"></a>Général

**Q : comment serveur de l’authentification multifacteur Azure gérer des données utilisateur ?**

Avec le serveur d’authentification multifacteur, les données utilisateur sont stockées uniquement sur les serveurs locaux. Aucune donnée utilisateur permanente n’est stockée dans le cloud. Lorsque l’utilisateur effectue la vérification, serveur d’authentification multifacteur envoie des données au service cloud l’authentification multifacteur Azure pour l’authentification. Communication entre serveur d’authentification multifacteur et le service de nuage l’authentification multifacteur utilise la couche SSL (Secure Sockets) ou sécurité TLS (Transport Layer) sur le port 443 sortant.

Lorsque les demandes d’authentification sont envoyées au service cloud, les données sont collectées pour l’authentification et l’utilisation des rapports. Champs de données incluses dans les journaux de vérification en deux étapes sont les suivantes :

- **Nº unique** (soit nom ou en local multifacteur d’authentification serveur ID utilisateur)
- **Prénom et nom** (facultatif)
- **Adresse de messagerie** (facultatif)
- **Numéro de téléphone** (si vous utilisez un appel vocal ou authentification SMS)
- **Appareil jeton** (si vous utilisez l’authentification de l’application mobile)
- **Mode d’authentification**
- **Résultat de l’authentification**
- **Nom de serveur de l’authentification multifacteur**
- **Serveur de l’authentification multifacteur IP**
- **Client IP** (le cas échéant)

Les champs facultatifs peuvent être configurées de serveur d’authentification multifacteur.

Le résultat de la vérification (succès ou refus) et la raison pour laquelle si elle a été refusée, sont stockées avec les données d’authentification et n’est disponibles dans les rapports d’authentification et l’utilisation.


## <a name="billing"></a>Facturation

La plupart des questions facturation peut être m’a répondu en faisant référence à la [page tarifs de l’authentification multifacteur](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

**Q : mon organisation facturée appels téléphoniques ou des messages texte permet de s’authentifier mes utilisateurs ?**

Organisations ne seront pas imputées pour le texte ou des appels téléphoniques placés les messages envoyés aux utilisateurs via l’authentification multifacteur Azure. Propriétaires de téléphone peuvent être facturées pour les appels téléphoniques ou des messages texte qu’ils reçoivent, en fonction de leur service de numéro de téléphone personnel.

**Q : a-t-il les frais de modèle de facturation par utilisateur basé sur le nombre d’utilisateurs qui sont configurés pour utiliser l’authentification multifacteur ou le nombre d’utilisateurs effectuent des vérifications ?**

Facturation est basée sur le nombre d’utilisateurs configurés pour utiliser l’authentification multifacteur.

**Q : comment fonctionne la facturation de l’authentification multifacteur ?**

Lorsque vous utilisez le « par utilisateur » ou « par authentification » modèle, Azure l’authentification Multifacteur est une ressource basée sur la consommation. Les frais sont facturés sur abonnement Azure de l’organisation comme machines virtuelles, des sites Web, etc..

Lorsque vous utilisez le modèle de licence, l’authentification multifacteur Azure licences sont achetées et puis attribuées aux utilisateurs, comme pour Office 365 et d’autres produits d’abonnement.

**Q : y a-t-il une version gratuite de l’authentification multifacteur Azure pour les administrateurs ?**

Dans certains cas, Oui. L’authentification multifacteur pour les administrateurs Azure propose un sous-ensemble des fonctionnalités de l’authentification Multifacteur Azure sans frais. Cette offre s’applique aux membres du groupe administrateurs globaux Azure dans instances Azure Active Directory qui ne sont pas liées à un fournisseur de l’authentification multifacteur Azure en fonction de la consommation. Utilisation d’un fournisseur de l’authentification multifacteur met à jour tous les administrateurs et utilisateurs dans l’annuaire qui sont configurés pour utiliser l’authentification multifacteur vers la version complète de l’authentification multifacteur Azure.

**Q : y a-t-il une version gratuite de l’authentification multifacteur Azure pour les utilisateurs Office 365 ?**

Dans certains cas, Oui. L’authentification multifacteur pour Office 365 propose un sous-ensemble des fonctionnalités de l’authentification Multifacteur Azure sans frais. Cette offre s’applique aux utilisateurs qui ont une licence Office 365 affectée, lorsqu’un fournisseur de l’authentification multifacteur Azure basée sur la consommation n’a pas été lié à l’instance correspondante d’Azure Active Directory. En utilisant le fournisseur de l’authentification multifacteur met à jour tous les administrateurs et utilisateurs dans l’annuaire qui sont configurés pour utiliser l’authentification multifacteur vers la version complète de l’authentification multifacteur Azure.

**Q : mon organisation basculer entre par utilisateur et par authentification modèles de facturation de consommation à tout moment ?**

Votre organisation choisit un modèle de facturation lorsqu’il crée une ressource. Vous ne pouvez pas modifier un modèle de facturation une fois que la ressource est mis en service. Toutefois, vous pouvez créer une autre ressource l’authentification multifacteur pour remplacer les paramètres d’origine. Paramètres utilisateur et options de configuration ne peut pas être transférées vers la nouvelle ressource.

**Q : mon organisation basculer entre la facturation de consommation et le modèle de licence à tout moment ?**

Lorsque des licences sont ajoutés à un répertoire qui comporte déjà un fournisseur de l’authentification multifacteur Azure par utilisateur, en fonction de la consommation de facturation est diminue par le nombre de licences appartient. Si tous les utilisateurs configurés pour utiliser l’authentification multifacteur ont licences attribuées, l’administrateur peut supprimer le fournisseur d’authentification multifacteur Azure.

Vous ne pouvez pas mélanger facturation de consommation par authentification avec un modèle de licence. Lorsqu’un fournisseur de l’authentification multifacteur par authentification est lié à un répertoire, l’organisation est facturée pour toutes les demandes de vérification de l’authentification multifacteur, indépendamment des licences de la propriété.

**Q : mon organisation possède à utiliser et synchroniser les identités pour utiliser l’authentification multifacteur Azure ?**

Lorsqu’une organisation utilise un modèle de facturation basée sur la consommation, Azure Active Directory n’est pas obligatoire. Liaison d’un fournisseur de l’authentification multifacteur à un répertoire est facultative. Si votre organisation n’est pas liée à un répertoire, elle peut désormais déployer serveur de l’authentification multifacteur Azure ou les SDK de l’authentification multifacteur Azure en local.

Azure Active Directory est requis pour le modèle de licence, car les licences sont ajoutées à l’annuaire lorsque vous achetez et les affectez à des utilisateurs dans l’annuaire.


## <a name="usability"></a>Facilité d’utilisation

**Q : que faire un utilisateur s’ils ne reçoivent une réponse sur leur téléphone, ou si le téléphone n’est pas disponible pour l’utilisateur ?**

Si l’utilisateur a configuré un téléphone de sauvegarde, ils doivent essayez à nouveau et sélectionnez ce téléphone lorsque vous y êtes invité dans la page de connexion. Si l’utilisateur ne dispose d’une autre méthode configurée, l’administrateur de l’organisation peut mettre à jour le numéro attribué au numéro de téléphone de l’utilisateur principal.


**Q : quelle est l’administrateur ? si un utilisateur de l’administrateur sur un compte de l’utilisateur ne puisse plus accéder contacts**

L’administrateur peut rétablir le compte d’utilisateur en leur demandant traitée à nouveau le processus d’inscription. Plus d’informations sur la [Gestion des utilisateurs et des paramètres du périphérique avec l’authentification multifacteur Azure dans le cloud](multi-factor-authentication-manage-users-and-devices.md).

**Q : quelle est un administrateur ? si perte ou de vol de téléphone d’un utilisateur qui utilise les mots de passe application**

L’administrateur peut supprimer les mots de passe de tous les utilisateurs application pour empêcher tout accès non autorisé. Une fois que l’utilisateur a un appareil de remplacement, l’utilisateur peut recréer les mots de passe. Plus d’informations sur la [Gestion des utilisateurs et des paramètres du périphérique avec l’authentification multifacteur Azure dans le cloud](multi-factor-authentication-manage-users-and-devices.md).

**Q : que se passe-t-il si l’utilisateur ne peut pas se connecter aux applications non navigateur ?**

Un utilisateur qui est configuré pour utiliser l’authentification multifacteur nécessite un mot de passe d’application à se connecter à certaines applications autres que des navigateurs. Un utilisateur doit effacer les informations de connexion (supprimer), redémarrez l’application et se connecter à l’aide de leur nom et application passe.

Obtenir plus d’informations sur la création de mots de passe de l’application et autres [aide sur les mots de passe application](multi-factor-authentication-end-user-app-passwords.md).


>[AZURE.NOTE] Authentification moderne pour les clients Office 2013
>
> Clients Office 2013 (y compris Outlook) prennent en charge les nouveaux protocoles d’authentification. Vous pouvez configurer Office 2013 pour prendre en charge l’authentification multifacteur. Après avoir configuré Office 2013, les mots de passe application ne sont pas requis pour les clients Office 2013. Pour plus d’informations, voir l' [annonce de version d’évaluation d’authentification moderne Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**Q : quel est un utilisateur ? si l’utilisateur ne reçoit pas de message texte, ou si l’utilisateur répond à un message texte bidirectionnel, mais la vérification arrive à expiration**

Diffuser des messages texte et réception de réponses dans SMS bidirectionnel n’est pas garantie, car il existe incontrôlables facteurs peuvent influencer la fiabilité du service. Ces facteurs incluent le pays de destination, l’opérateur de téléphonie mobile et le signal.

Les utilisateurs qui rencontrent des difficultés à recevoir des messages texte fiable doivent sélectionner la méthode de l’application ou un appel téléphonique mobile à la place. L’application mobile peut recevoir des notifications à la fois sur connexion cellulaires et Wi-Fi. En outre, l’application mobile peut générer des codes de vérification du même lorsque l’appareil n’a aucun signal. L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Si vous devez utiliser des messages texte, nous recommandons d’utiliser SMS à sens unique plutôt que SMS bidirectionnel lorsque cela est possible. SMS à sens unique est plus fiable et empêche les utilisateurs d’entraîner de frais SMS globales à partir de la réponse à un message texte envoyé à partir d’un autre pays.


**Q : puis-je utiliser jetons matériels avec serveur de l’authentification multifacteur Azure ?**

Si vous utilisez un serveur de l’authentification multifacteur Azure, vous pouvez importer des jetons de tiers d’authentification Open (formule) temporelles mot de passe unique (TOTP) et les utiliser pour la vérification.

Vous pouvez utiliser des jetons de ActiveIdentity qui sont des jetons TOTP formule si vous placez le fichier de clé secrète dans un fichier CSV et que vous importez au serveur de l’authentification multifacteur Azure. Vous pouvez utiliser les jetons formule avec Active Directory Federation Services (ADFS), RADIUS Remote Authentication Dial-In User Service () lorsque le système client peut traiter les réponses aux demandes access et l’authentification basée sur les formulaires de Internet Information Server (IIS).

Vous pouvez importer des jetons de formule TOTP tiers avec les formats suivants :  
- Conteneur portable de clé symétrique (PSKC)  
- CSV si le fichier contient un numéro de série, une clé secrète au format Base 32 et un intervalle de temps  

**Q : puis-je utiliser le serveur de l’authentification multifacteur Azure pour sécuriser les Services Terminal Server ?**

Oui, mais, si vous êtes à l’aide de Windows Server 2012 R2 ou version ultérieure, uniquement à l’aide de passerelle Bureau à distance (passerelle Bureau à distance).

Modifications de sécurité dans Windows Server 2012 R2 ont changé la façon dont le serveur de l’authentification multifacteur Azure se connecte au package de sécurité de sécurité autorité (locale) dans Windows Server 2012 et les versions antérieures. Pour les versions des Services Terminal Server dans Windows Server 2012 ou version antérieure, vous pouvez [sécuriser une application avec l’authentification Windows](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Si vous utilisez Windows Server 2012 R2, vous devez passerelle Bureau à distance.

**Q : Pourquoi est-ce qu’un utilisateur serait recevoir un appel de l’authentification multifacteur à partir d’un appelant anonyme après la configuration d’ID de l’appelant ?**

Lorsque l’authentification multifacteur appels sont placés à travers le réseau téléphonique public, parfois ils sont routés via un opérateur qui ne prennent pas en charge les ID de l’appelant. Pour cette raison, ID de l’appelant n’est pas garantie, même si le système de l’authentification multifacteur lui envoie toujours.


## <a name="errors"></a>Erreurs

**Q : que doivent faire utilisateurs si verra un message d’erreur « demande d’authentification n’est pas pour un compte activé » lors de l’utilisation de notifications de l’application mobile ?**

Demandez-leur de suivre cette procédure pour supprimer leur propre compte de l’application mobile, puis ajoutez-la à nouveau :

1. Accédez à [votre profil portail Azure](https://account.activedirectory.windowsazure.com/profile/) et connectez-vous avec votre compte professionnel.
2. Sélectionnez la **vérification de sécurité supplémentaires**.
4. Supprimer le compte existant à partir de l’application mobile.
5. Cliquez sur **configurer**et suivez les instructions pour reconfigurer l’application mobile.


**Q : que doivent faire utilisateurs si verra un message d’erreur 0x800434D4L lorsque vous vous connectez à une application non navigateur ?**

Pour l’instant, un utilisateur peut utiliser la vérification de la sécurité supplémentaires uniquement avec les applications et services que l’utilisateur peut accéder via un navigateur. Applications non-navigateur (également appelées *applications clientes enrichies*) qui sont installées sur un ordinateur local, tels que Windows PowerShell, ne fonctionne pas avec les comptes nécessitant une vérification de la sécurité supplémentaires. Dans ce cas, l’utilisateur peut s’afficher à l’application génère une erreur 0x800434D4L.

Une solution de contournement pour cela est d’avoir utilisateur distinct des comptes pour liés à l’administrateur et opérations non administrateur. Plus tard, vous pouvez lier des boîtes aux lettres entre votre compte d’administrateur et un compte non administrateur afin que vous pouvez vous connecter à Outlook à l’aide de votre compte non administrateur. Pour plus d’informations à ce sujet, découvrez comment [permettre à un administrateur pour ouvrir et afficher le contenu des boîtes aux lettres d’un utilisateur](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Étapes suivantes

Si votre question n’est pas une réponse ici, veuillez laisser dans les commentaires dans la partie inférieure de la page. Ou bien, voici quelques options supplémentaires pour obtenir de l’aide :


**Q : Comment puis-je obtenir de l’aide avec l’authentification multifacteur Azure ?**

- Rechercher des solutions aux problèmes techniques courants la [Base de connaissances Microsoft](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) .

- Rechercher et parcourir les questions et réponses de la Communauté techniques ou demandez à votre propre question dans les [forums Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

- Si vous êtes un client PhoneFactor hérité et que vous avez des questions ou besoin d’aide pour la réinitialisation d’un mot de passe, cliquez sur le lien [Réinitialiser votre mot de passe](mailto:phonefactorsupport@microsoft.com) pour ouvrir une demande de support.

- Contactez le support technique via le [support de serveur de l’authentification multifacteur Azure (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Quand nous contacter, il est utile si vous pouvez inclure toutes les informations concernant votre problème que possible. Vous pouvez fournir des informations contient la page où vous l’avez vu l’erreur, le code d’erreur spécifique, l’ID de session spécifique et l’ID de l’utilisateur qui ont vu l’erreur.
