<properties
   pageTitle="Instructions de personnalisation pour les Applications | Microsoft Azure"
   description="Un guide complet sur les ressources destinées aux développeurs pour Azure Active Directory"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/23/2016"
   ms.author="mbaldwin"/>


# <a name="branding-guidelines-for-applications"></a>Instructions de personnalisation pour les Applications


Cette rubrique décrit les instructions personnalisation, que vous devez utiliser lorsque vous développez des applications avec Azure Active Directory (AD Azure). Ces instructions aideront à rediriger vos clients lorsqu’ils souhaitent utiliser leur propre compte professionnel ou scolaire, géré dans Azure AD, ou leur personnel du compte d’abonnement et se connecter à votre application.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Comptes de scolaire auprès de Microsoft ou comptes personnels par rapport de travail

Microsoft gère deux types de comptes d’utilisateurs :

- **Comptes personnels** (auparavant appelé Windows Live ID). Ces comptes représentent la relation entre les utilisateurs *individuels* et Microsoft et sont utilisées pour accéder aux services et les périphériques grand public de Microsoft. Ces comptes sont destinés à usage personnel.

- **Comptes scolaire ou Professionnel.** Ces comptes sont gérés par Microsoft au nom d’organisations qui utilisent Azure Active Directory. Ces comptes sont utilisés pour se connecter à Office 365 et d’autres services professionnels de Microsoft.

Microsoft comptes professionnel ou scolaire sont généralement affectés aux utilisateurs finaux (employés, les étudiants, les employés federal) par leur organisation (société, scolaire, administration). Ces comptes sont maîtriser directement dans le cloud, dans la plateforme Azure AD, ou synchronisés à Azure Active Directory à partir d’un répertoire local, tels que Windows Server Active Directory. Microsoft est le *dépositaire* des comptes professionnel ou scolaire, mais les comptes sont appartenant et contrôlés par l’organisation.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Faisant référence à des comptes Azure AD dans votre application

Microsoft n’expose pas aux utilisateurs finaux de la Azure ou les noms de marque Active Directory et qu’aucune ne devez-vous.

- Une fois que les utilisateurs sont connectés, vous devez utiliser le nom de l’organisation et le logo autant que possible. Ceci est supérieure à celle à l’aide de termes génériques tels que « votre organisation ».

- Lorsque les utilisateurs n’êtes pas connectés, vous devez faire référence à leurs comptes en tant que « travail comptes scolaires ou » et utilisez le logo de Microsoft pour indiquer que ces comptes sont gérés par Microsoft. N’utilisez pas des termes comme « compte d’entreprise », « compte professionnel » ou « compte d’entreprise » qui créent confusion de l’utilisateur.

## <a name="user-account-pictogram"></a>Pictogramme de compte d’utilisateur
Dans une version antérieure de ces instructions, il est recommandé à l’aide d’un pictogramme « badge bleu ». En fonction des commentaires utilisateur et le développeur, nous maintenant recommande l’utilisation du logo Microsoft à la place. Cela vous aidera les utilisateurs comprendre qu’ils peuvent réutiliser le compte qu’ils utilisent les services avec Office 365 ou Microsoft pour vous connecter à votre application.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Inscription et pour vous connecter avec Azure AD

Votre application peut présenter des chemins d’accès distincts pour l’abonnement et se connecter et les sections suivantes fournissent des conseils visuels pour les deux scénarios.

**Si votre application prend en charge l’authentification de l’utilisateur final (par exemple, gratuit au modèle de version d’évaluation ou freemium) à distance**: vous pouvez afficher un bouton de **connexion** qui permet aux utilisateurs d’accéder à votre application avec leur compte professionnel ou de leur compte personnel. Azure AD affichera une invite de consentement la première fois qu’ils accèdent à votre application.

**Si votre application requiert des autorisations que seuls les administrateurs peuvent consentement, ou si votre application nécessite une licence d’organisation**: vous devez séparer acquisition d’administration à partir de connexion utilisateur. Le **bouton « obtenir cette application »** redirigera les administrateurs pour se connecter, puis demandez-leur de donner son accord au nom d’utilisateurs de leur organisation. Cela a l’avantage de suppression des utilisateurs finaux consentement invites pour votre application.

## <a name="visual-guidance-for-app-acquisition"></a>Guide visuel pour l’acquisition d’application

Votre lien « obtenir de l’application » doit rediriger l’utilisateur vers l’accorder l’accès Azure AD (autoriser) page, afin de permettre administrateur d’une organisation autoriser votre application pour avoir accès aux données de leur organisation qui sont hébergées par Microsoft. Connaître la procédure demander l’accès est décrits dans l’article [d’Intégration des Applications avec Azure Active Directory](active-directory-integrating-applications.md) .

Une fois que les administrateurs consentement dans votre application, ils peuvent choisir pour l’ajouter à application Lanceur expérience avec Office 365 leurs utilisateurs (accessible à partir de la GAUFRE et de [https://portal.office.com/myapps](https://portal.office.com/myapps)). Si vous souhaitez publier cette fonctionnalité, vous pouvez utiliser des termes comme « Ajouter cette application à votre organisation » et afficher un bouton à ceci :

![Types d’applications et scénarios](./media/active-directory-branding-guidelines/add-to-my-org.png)

Toutefois, nous vous recommandons de que vous rédigez un texte explicatif plutôt que sur les boutons. Par exemple :
> *Si vous utilisez déjà Office 365 ou autre service de l’entreprise de Microsoft, vous pouvez simplement accorder < your_app_name > accès aux données de votre organisation. Ainsi, vos utilisateurs pour accéder à < your_app_name > avec leurs comptes de travail existant.*


## <a name="visual-guidance-for-sign-in"></a>Guide visuel pour se connecter
Votre application doit afficher un signe bouton qui redirige les utilisateurs vers l’extrémité de connexion qui correspond au protocole que vous utilisez pour intégrer Azure AD. La section suivante fournit des détails sur ce bouton qui doit ressembler à.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Pictogramme et « Connectez-vous à l’aide de Microsoft »
Il est l’association du logo Microsoft et les termes « S’in with Microsoft » qui représente de façon unique Azure AD parmi les autres fournisseurs d’identité que votre application peut prendre en charge. Si vous n’avez pas suffisamment d’espace pour « S’in with Microsoft », il est OK pour réduire à « Connexion ».

![Types d’applications et scénarios](./media/active-directory-branding-guidelines/sign-in-with-microsoft-light.png)

![Types d’applications et scénarios](./media/active-directory-branding-guidelines/sign-in-light.png)

Vous pouvez également utiliser un modèle de couleurs foncé pour les boutons.

![Types d’applications et scénarios](./media/active-directory-branding-guidelines/sign-in-with-microsoft-dark.png)

![Types d’applications et scénarios](./media/active-directory-branding-guidelines/sign-in-dark.png)

## <a name="branding-dos-and-donts"></a>Conseils de personnalisation

**** Utilisez « compte professionnel ou scolaire » en combinaison avec le bouton « S’in with Microsoft » pour fournir une explication supplémentaire pour aider les utilisateurs finaux reconnaître si il peut l’utiliser. **** N’utilisez pas d’autres termes tels que « compte d’entreprise », « compte professionnel » ou « compte d’entreprise ».

**** N’utilisez pas « Office 365 ID » ou « ID Azure ». Office 365 est également le nom d’un offre à partir de Microsoft qui n’utilise pas Azure AD pour l’authentification client.

**Ne** modifiez pas le logo de Microsoft.

**** N’exposent pas aux utilisateurs finaux des marques Azure ou Active Directory. Il est toutefois OK à utiliser ces termes avec les développeurs, les professionnels de l’informatique et les administrateurs.

## <a name="navigation-dos-and-donts"></a>Conseils de navigation

**** Permettent aux utilisateurs de se déconnecter et basculer vers un autre compte d’utilisateur. Alors que la plupart des personnes ont un compte personnel unique à partir de Microsoft/Facebook/Google/Twitter, personnes sont souvent associées à plus d’une organisation. Prise en charge de plusieurs utilisateurs connecté sera bientôt disponible.
