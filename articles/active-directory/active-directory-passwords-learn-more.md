<properties
    pageTitle="En savoir plus : La gestion de mot de passe Azure AD | Microsoft Azure"
    description="Informations complémentaires sur la gestion de mot de passe Azure AD, y compris le fonctionnement de l’écriture différée de mot de passe, sécurité d’écriture différée de mot de passe, comment le mot de passe réinitialisé works portail et quelles données sont utilisées par la réinitialisation de mot de passe."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="asteen"/>

# <a name="learn-more-about-password-management"></a>Pour plus d’informations sur la gestion de mot de passe

> [AZURE.IMPORTANT] **Vous êtes ici parce que vous rencontrez des problèmes en vous connectant ?** Si c’est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).

Si vous avez déjà déployé gestion de mot de passe, ou simplement besoin pour en savoir plus sur la technique lançons-nous que détails du fonctionnement avant de déployer, cette section vous donne un aperçu des concepts techniques derrière le service. Nous aborderons les éléments suivants :

* [**Vue d’ensemble de mot de passe d’écriture différée**](#password-writeback-overview)
  - [Fonctionne de l’écriture différée de mot de passe](#how-password-writeback-works)
  - [Scénarios pris en charge pour l’écriture différée de mot de passe](#scenarios-supported-for-password-writeback)
  - [Modèle de sécurité de mot de passe d’écriture différée](#password-writeback-security-model)
* [**Comment le mot de passe réinitialiser des travail portail ?**](#how-does-the-password-reset-portal-work)
  - [Quelles sont les données sont utilisées par la réinitialisation de mot de passe ?](#what-data-is-used-by-password-reset)
  - [Comment accéder à votre mot de passe rétablir les données pour vos utilisateurs](#how-to-access-password-reset-data-for-your-users)

## <a name="password-writeback-overview"></a>Vue d’ensemble de mot de passe d’écriture différée
Mot de passe en écriture différée est un composant [Azure Active Directory Connect](active-directory-aadconnect.md) qui peut être activé et utilisé par les abonnés en cours d’Azure Active Directory Premium. Pour plus d’informations, voir [Azure Active Directory éditions](active-directory-editions.md).

Mot de passe en écriture différée permet de vous permettent de configurer votre client cloud pour écrire les mots de passe en vous locaux Active Directory.  Vous homologue rend d’avoir à configurer et gérer une solution de réinitialisation de mot de passe libre-service compliqué en local, et fournit un moyen pratique de pour vos utilisateurs, réinitialiser les mots de passe en local à l’endroit où ils sont sur le nuage.  Consultez les informations pour les principales fonctionnalités de mot de passe d’écriture différée :

- **Zéro commentaires retard.**  Écriture différée de mot de passe est une opération synchrone.  Vos utilisateurs seront informés immédiatement si leur mot de passe n’a pas atteint la stratégie ou n’a pas pu être réinitialiser ou modifiés pour une raison quelconque.
- **Prend en charge la réinitialisation des mots de passe pour les utilisateurs à l’aide de AD FS ou autres technologies de fédération.**  Avec écriture différée mot de passe, dans la mesure où les comptes d’utilisateurs fédérés sont synchronisés dans votre client Azure AD, ils seront chargées de gérer ses installations sur les mots de passe AD à partir du cloud.
- **Prend en charge la réinitialisation des mots de passe des utilisateurs à l’aide de la synchronisation de hachage de mot de passe.** Lorsque le service de réinitialisation du mot de passe détecte qu’un compte d’utilisateur synchronisé est activé pour la synchronisation de hachage de mot de passe, nous réinitialisons en local de ce compte et votre mot de passe cloud en même temps.
- **Prend en charge la modification des mots de passe à partir du Panneau de configuration access et Office 365.**  Lorsque fédérés ou synchronisation de mot de passe utilisateurs sont acheminés vers modifier leur mot de passe qui a expiré ou non expirée, nous allons répondre ces mots de passe pour votre environnement Active Directory local.
- **Prend en charge l’écriture les mots de passe lorsqu’un administrateur de réinitialise le** [**Portail de gestion azure**](https://manage.windowsazure.com).  Chaque fois qu’un administrateur réinitialisations de mot de passe d’un utilisateur dans le [Portail de gestion Azure](https://manage.windowsazure.com), si cet utilisateur fédéré ou la synchronisation de mot de passe, nous allons définir l’administrateur sélectionne à votre Active Directory local, ainsi que le mot de passe.  Ceci est actuellement pas pris en charge dans le portail d’administration d’Office.
- **Applique vos localement stratégies de mot de passe AD.**  Lorsqu’un utilisateur réinitialise son mot de passe, nous assurer qu’il répond à vos localement par une stratégie AD avant de valider dans ce répertoire.  Cela inclut l’historique, la complexité, l’âge, les filtres de mot de passe et d’autres restrictions de mot de passe que vous avez défini dans votre Active Directory local.
- **Ne nécessite pas les règles de pare-feu entrant.**  Mot de passe en écriture différée utilise un relais Bus des services Azure comme un canal de communication sous-jacent, ce qui signifie que vous n’êtes pas obligé d’ouvrez les ports entrants sur votre pare-feu pour cette fonctionnalité pour l’utiliser.
- **N’est pas prise en charge pour les comptes utilisateur qui existent au sein de groupes protégés dans Active Directory local.** Pour plus d’informations sur les groupes protégées, voir [comptes protégés et des groupes dans Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

### <a name="how-password-writeback-works"></a>Fonctionne de l’écriture différée de mot de passe
Mot de passe en écriture différée comporte trois composants principaux :

- Service de nuage de réinitialisation de mot de passe (cela est également intégré aux pages de modification du mot de passe d’Azure AD)
- Relais de Bus des services Azure client spécifique
- Point de terminaison de réinitialisation de mot de passe local

Elles s’adaptent comme décrit dans le schéma ci-dessous :

  ![][001]

Lors de la synchronisation d’un hachage fédéré ou mot de passe serait utilisateur est fourni pour réinitialiser ou modifier son mot de passe dans le cloud, le des opérations suivantes se produisent :

1.  Nous vérifier le type de mot de passe l’utilisateur a.  Si le mot de passe est géré en local, nous voyons, nous assurer que le service d’écriture différée est en cours d’exécution.  S’il s’agit, nous laisser l’utilisateur poursuivre, si elle n’est pas, nous transmettre à l’utilisateur qui leur mot de passe ne peuvent pas être réinitialisé ici.
2.  Ensuite, l’utilisateur passe les portails d’authentification appropriée et atteint l’écran de mot de passe réinitialiser.
3.  L’utilisateur sélectionne un nouveau mot de passe et confirme.
4.  Après avoir cliqué sur Envoyer, nous chiffrer le mot de passe en texte brut avec une clé symétrique qui a été créée pendant le processus de configuration d’écriture différée.
5.  Après le chiffrement le mot de passe, nous incluez-la dans une charge sur un canal HTTPS seront envoyée à votre client service spécifique bus relais de messagerie (nous également configurer à votre place pendant le processus de configuration d’écriture différée).  Ce relais est protégé par un mot de passe généré aléatoirement sait uniquement votre installation locale.
6.  Une fois que le message n’atteigne bus des services, le point de terminaison de réinitialiser votre mot de passe n’est plus automatiquement et découvre qu’une demande de réinitialisation en attente.
7.  Le service de recherche ensuite l’utilisateur en question à l’aide de l’attribut de point d’ancrage cloud.  Pour cette liste de choix réussir, l’objet utilisateur doit exister dans l’espace de connecteur AD, il doit être lié à l’objet MV correspondant, et il doit être lié à l’objet de connecteur AAD correspondant. Enfin, pour la synchronisation rechercher ce compte d’utilisateur, le lien d’objet de connecteur AD MV doit avoir la règle synchronisation `Microsoft.InfromADUserAccountEnabled.xxx` sur le lien.  Ceci est requise, car lorsque les appels entrants à partir du cloud, le moteur de synchronisation utilise l’attribut cloudAnchor pour rechercher l’objet AAD connecteur espace, puis suit le lien vers l’objet MV, puis le lien vers l’objet Active Directory. Étant donné que vous pouvez rencontrer plusieurs objets Active Directory (forêt à plusieurs) pour le même utilisateur, le moteur de synchronisation s’appuie sur la `Microsoft.InfromADUserAccountEnabled.xxx` lien choisir celui qui convient.
8.  Une fois que le compte d’utilisateur est trouvé, nous tentez de réinitialiser le mot de passe directement dans la forêt AD appropriée.
9.  Si l’opération de définition de mot de passe réussit, nous transmettre à l’utilisateur son mot de passe a été modifié et que leur peuvent-ils dirigés Joyeux.
10. Si le mot de passe défini opération échoue, nous renvoie l’erreur à l’utilisateur et lui permettre d’essayer de nouveau.  L’opération peut échouer, car le service a été vers le bas, car le mot de passe qu’ils ont sélectionné n’a pas atteint les stratégies de l’organisation, car nous n’avons pas trouvé l’utilisateur dans Active Directory local, ou un nombre quelconque de raisons.  Nous avons un message spécifique pour de nombreux cas et indiquer ce qu’il peuvent faire pour résoudre le problème à l’utilisateur.

### <a name="scenarios-supported-for-password-writeback"></a>Scénarios pris en charge pour l’écriture différée de mot de passe
Le tableau ci-dessous décrit les scénarios qui sont prises en charge pour les versions de nos capacités de synchronisation.  En règle générale, il est vivement recommandé installer la dernière version [d’Azure AD Connect](active-directory-aadconnect.md#install-azure-ad-connect) si vous souhaitez utiliser votre mot de passe d’écriture différée.

  ![][002]

### <a name="password-writeback-security-model"></a>Modèle de sécurité de mot de passe d’écriture différée
Écriture différée de mot de passe est un service hautement sécurisé et robuste.  Afin de garantir que vos informations sont protégées, nous activer un modèle de sécurité 4 hiérarchisée est décrite ci-dessous.

- **Relais bus de service spécifique client** – lorsque vous configurez le service, nous configurer un relais bus service client spécifiques qui est protégé par un mot de passe fort généré aléatoirement que Microsoft a jamais accès à.
- **Locked vers le bas, et forte, clé de chiffrement de mot de passe** – après avoir créé le relais de bus de service, nous créons une clé symétrique forte que nous utilisons pour chiffrer le mot de passe de l’année sur le réseau.  Cette clé se trouve uniquement dans le magasin des secrets de votre société dans le cloud, ce qui est très verrouillé et audit, comme un mot de passe dans l’annuaire.
- **TLS standard du secteur** – lorsqu’un mot de passe réinitialisé ou opération de modification se produit dans le cloud, nous prendre les mots de passe et chiffrer avec votre clé publique.  Nous constater puis qui dans un message HTTPS qui est envoyé sur un canal chiffré à l’aide de certificats SSL de Microsoft pour votre relais bus de service.  Une fois que le message arrive dans Bus des services, votre agent local est activé, s’authentifie Bus des services à l’aide du mot de passe fort qui a été généré précédemment, récupère le message chiffré, déchiffre à l’aide de la clé privée, que nous avons généré, puis tentatives pour définir le mot de passe via l’API AD DS SetPassword.  Cette étape est ce qui permet d’appliquer votre stratégie de mot de passe Active Directory local (la complexité, l’âge, l’historique, filtres, etc.) dans le cloud.
- **Stratégies d’expiration de message** – Enfin, si pour une raison quelconque le message reste dans Service marché étant votre service local vers le bas, il sera dépassé et supprimé après quelques minutes pour accroître la sécurité encore plus loin.

## <a name="how-does-the-password-reset-portal-work"></a>Comment le mot de passe réinitialiser des travail portail ?
Lorsqu’un utilisateur accède au mot de passe réinitialisé portail, un flux de travail est lancée pour déterminer si ce compte d’utilisateur est valide, quelle organisation à laquelle les utilisateurs appartient, où ce mot de passe est gérée et que l’utilisateur est une licence d’utilisation de la fonctionnalité ou non.  Parcourez les étapes ci-dessous pour en savoir plus sur la logique derrière la page de réinitialisation du mot de passe.

1.  Utilisateur clique sur le ne peut pas accéder à votre lien compte ou accède directement à [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com).
2.  Utilisateur entre un id utilisateur et renvoie un captcha.
3.  Azure AD vérifie si l’utilisateur est en mesure d’utiliser cette fonctionnalité en procédant comme suit :
    - Vérifie que l’utilisateur dispose de cette fonctionnalité est activée et une licence Azure AD affectées.
        - Si l’utilisateur ne dispose pas de cette fonctionnalité est activée ou une licence attribuée, l’utilisateur est invité à contacter son administrateur pour réinitialiser son mot de passe.
    - Vérifie que l’utilisateur a le droit défi données définies dans son compte, conformément à la stratégie de l’administrateur.
        - Si la stratégie ne requiert qu’un seul HIP, il est assuré que l’utilisateur possède les données appropriées définies pour au moins l’une des difficultés activées par la stratégie d’administrateur.
          - Si l’utilisateur n’est pas configuré, l’utilisateur est conseillé de contacter son administrateur pour réinitialiser son mot de passe.
        - Si la stratégie requiert deux défis, il est assuré que l’utilisateur possède les données appropriées définies pour au moins deux des défis activés par la stratégie d’administrateur.
          - Si l’utilisateur n’est pas configuré, puis nous l’utilisateur est conseillé de contacter son administrateur pour réinitialiser son mot de passe.
    - Vérifie ou non votre mot de passe de l’utilisateur est géré en local (fédérés ou avait synchronisation hachage de mot de passe).
       - Si l’écriture différée est déployée et le mot de passe est géré en local, l’utilisateur est autorisé à poursuivre authentifier et réinitialiser son mot de passe.
       - Si l’écriture différée n’est pas déployée et le mot de passe est géré en local, l’utilisateur est invité à contacter son administrateur pour réinitialiser son mot de passe.
4.  S’il s’avère que l’utilisateur est correctement réinitialiser son mot de passe, l’utilisateur est guidé dans le processus de réinitialisation.

En savoir plus sur le déploiement de mot de passe d’écriture différée en [mise en route : Azure AD Password Management](active-directory-passwords-getting-started.md).

### <a name="what-data-is-used-by-password-reset"></a>Quelles sont les données sont utilisées par la réinitialisation de mot de passe ?
Le tableau suivant indique où et comment ces données sont utilisées lors de la réinitialisation de mot de passe et sont conçues pour vous aider à décider les options d’authentification sont appropriées pour votre organisation. Ce tableau présente également les exigences de mise en forme dans les cas où vous fournissez des données au nom d’utilisateurs à partir de chemins d’entrée que ne pas valident ces données.

> [AZURE.NOTE] Téléphone de bureau n’apparaît pas dans le portail d’inscription car les utilisateurs ne sont actuellement pas en mesure de modifier cette propriété dans l’annuaire.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Nom de la méthode de contact</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Élément de données Azure Active Directory</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Utilisés / définissable où ?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Format requis</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Téléphone de bureau</p>
            </td>
            <td>
              <p>Numéro de téléphone</p>
              <p>par exemple : Set-MsolUser - UserPrincipalName JWarner@contoso.com - numéro de téléphone « 1234567890 + 1 x 1234 »</p>
            </td>
            <td>
              <p>Utilisées dans :</p>
              <p>Portail de réinitialiser votre mot de passe</p>
              <p>Réglable de :</p>
              <p>Numéro de téléphone ne peut être définie à partir de PowerShell, la synchronisation d’annuaire, portail de gestion Azure et le portail d’administration d’Office</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (par exemple, 1234567890 + 1)</p>
              <ul>
                <li class="unordered">
Doit fournir un code de pays<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Doit fournir un indicatif régional (le cas échéant)<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Doit avoir fournir un code un + au premier plan du pays<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Doit disposer d’un espace entre le code du pays et le reste du nombre<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Extensions ne sont pas prises en charge, si vous avez toutes les extensions spécifiées, nous est destiné à éliminer il à partir du nombre avant de répartir l’appel téléphonique.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Téléphone mobile</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>OR</p>
              <p>MobilePhone</p>
              <p>(Authentification que téléphone est utilisé s’il existe des données présentes, dans le cas contraire cela revient au champ téléphone mobile).</p>
              <p>par exemple : Set-MsolUser - UserPrincipalName JWarner@contoso.com - MobilePhone « 1234567890 + 1 x 1234 »</p>
            </td>
            <td>
              <p>Utilisées dans :</p>
              <p>Portail de réinitialiser votre mot de passe</p>
              <p>Portail de l’enregistrement</p>
              <p>Réglable de : </p>
              <p>AuthenticationPhone ne peut être définie à partir du portail de l’enregistrement de réinitialiser votre mot de passe ou un portail de l’enregistrement de l’authentification Multifacteur.</p>
              <p>MobilePhone ne peut être définie à partir de PowerShell, la synchronisation d’annuaire, portail de gestion Azure et le portail d’administration d’Office</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (par exemple, 1234567890 + 1)</p>
              <ul>
                <li class="unordered">
Doit fournir un code de pays.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Doit fournir un indicatif régional (le cas échéant).<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Doit avoir fournir un code un + au premier plan du pays.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Doit avoir un espace entre le code du pays et le reste du nombre.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Extensions ne sont pas prises en charge, si vous avez toutes les extensions spécifiées, nous ignorez-la lors de la répartition de l’appel téléphonique.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Messagerie de secours</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>OR</p>
              <p>AlternateEmailAddresses [0] </p>
              <p>(L’authentification de la que messagerie est utilisée s’il existe des données présentes, dans le cas contraire cela revient au champ adresse de messagerie secondaire).</p>
              <p>Remarque : le champ adresse de messagerie secondaire est spécifié comme un tableau de chaînes dans l’annuaire.  Nous utilisons la première entrée dans ce tableau.</p>
              <p>par exemple : Set-MsolUser - UserPrincipalName JWarner@contoso.com - AlternateEmailAddresses"email@live.com"</p>
            </td>
            <td>
              <p>Utilisées dans :</p>
              <p>Portail de réinitialiser votre mot de passe</p>
              <p>Portail de l’enregistrement</p>
              <p>Réglable de : </p>
              <p>AuthenticationEmail ne peut être définie à partir du portail de l’enregistrement de réinitialiser votre mot de passe ou un portail de l’enregistrement de l’authentification Multifacteur.</p>
              <p>AlternateEmail ne peut être définie à partir de PowerShell, le portail de gestion Azure et le portail d’administration d’Office</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a>ou甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
Messages électroniques doivent suivre la mise en forme standard en tant que par.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Messages électroniques Unicode sont prises en charge.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Sécurité Questions et réponses</p>
            </td>
            <td>
              <p>Non disponible pour la modification directement dans l’annuaire.</p>
            </td>
            <td>
              <p>Utilisées dans :</p>
              <p>Portail de réinitialiser votre mot de passe</p>
              <p>Portail de l’enregistrement </p>
              <p>Réglable de : </p>
              <p>La seule façon de définir des questions sur la sécurité consiste à utiliser le portail de gestion Azure.</p>
              <p>La seule façon de définir les réponses aux questions sur la sécurité pour un utilisateur donné consiste à utiliser le portail d’inscription.</p>
            </td>
            <td>
              <p>Questions sur la sécurité ont une limite maximale de 200 caractères et un minimum de 3 caractères</p>
              <p>Réponses ont une limite maximale de 40 caractères et un minimum de 3 caractères</p>
            </td>
          </tr>
        </tbody></table>

###<a name="how-to-access-password-reset-data-for-your-users"></a>Comment accéder à votre mot de passe rétablir les données pour vos utilisateurs
####<a name="data-settable-via-synchronization"></a>Définissables via la synchronisation des données
Les champs suivants peuvent être synchronisés locales :

* Téléphone mobile
* Téléphone de bureau

####<a name="data-settable-with-azure-ad-powershell"></a>Données définissables avec Azure AD PowerShell
Les champs suivants sont accessibles avec Azure AD PowerShell & l’API Graph :

* Messagerie de secours
* Téléphone mobile
* Téléphone de bureau
* L’authentification par téléphone
* L’authentification par courrier électronique

####<a name="data-settable-with-registration-ui-only"></a>Données définissables avec inscription l’interface utilisateur
Les champs suivants ne sont accessibles via l’inscription SSPR l’interface utilisateur (https://aka.ms/ssprsetup) :

* Sécurité Questions et réponses

####<a name="what-happens-when-a-user-registers"></a>Que se passe-t-il lorsqu’un utilisateur enregistre ?
Lorsqu’un utilisateur est enregistré, la page d’inscription sera **toujours** définir les champs suivants :

* L’authentification par téléphone
* L’authentification par courrier électronique
* Sécurité Questions et réponses

Si vous avez fourni une valeur pour **téléphone portable** ou de **Messagerie de secours**, les utilisateurs peuvent utiliser immédiatement ceux réinitialiser les mots de passe, même s’ils ne l’avez pas enregistrés pour le service.  En outre, les utilisateurs voir ces valeurs lors de l’enregistrement pour la première fois et modifiez-les s’ils le souhaitent.  Toutefois, une fois qu’ils enregistrement avec succès, ces valeurs seront conservées dans les champs **D’authentification téléphone** et la **Messagerie électronique d’authentification** , respectivement.

Il peut s’agir manière utile afin de débloquer grand nombre d’utilisateurs à utiliser SSPR tout en permettant aux utilisateurs de valider ces informations à travers le processus d’inscription.

####<a name="setting-password-reset-data-with-powershell"></a>Données avec PowerShell de réinitialisation du mot de passe de paramètre
Vous pouvez définir les valeurs pour les champs suivants avec Azure AD PowerShell.

* Messagerie de secours
* Téléphone mobile
* Téléphone de bureau

Pour commencer, vous devez tout d’abord [Télécharger](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)et installer le module Azure Active Directory PowerShell.  Une fois que vous avez installé l’application, vous pouvez suivre les étapes ci-dessous pour configurer chaque champ.

#####<a name="alternate-email"></a>Messagerie de secours
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
```

#####<a name="mobile-phone"></a>Téléphone mobile
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
```

#####<a name="office-phone"></a>Téléphone de bureau
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"
```

####<a name="reading-password-reset-data-with-powershell"></a>Données avec PowerShell de réinitialisation du mot de passe de lecture
Vous pouvez lire les valeurs pour les champs suivants avec Azure AD PowerShell.

* Messagerie de secours
* Téléphone mobile
* Téléphone de bureau
* L’authentification par téléphone
* L’authentification par courrier électronique

Pour commencer, vous devez tout d’abord [Télécharger](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)et installer le module Azure Active Directory PowerShell.  Une fois que vous avez installé l’application, vous pouvez suivre les étapes ci-dessous pour configurer chaque champ.

#####<a name="alternate-email"></a>Messagerie de secours
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
```

#####<a name="mobile-phone"></a>Téléphone mobile
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
```

#####<a name="office-phone"></a>Téléphone de bureau
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber
```

#####<a name="authentication-phone"></a>L’authentification par téléphone
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
```

#####<a name="authentication-email"></a>L’authentification par courrier électronique
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

## <a name="links-to-password-reset-documentation"></a>Des liens vers votre mot de passe réinitialisé documentation
Voici des liens vers toutes les pages de documentation de réinitialisation du mot de passe Azure AD :

* **Vous êtes ici parce que vous rencontrez des problèmes en vous connectant ?** Si c’est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [**Mode de fonctionnement**](active-directory-passwords-how-it-works.md) - en savoir plus sur les différents composants de service et de six chacun a
* [**Prise en main**](active-directory-passwords-getting-started.md) - obtenir des informations pour vous permettre aux utilisateurs de réinitialiser et modifier leur mot de passe cloud ou en local
* [**Personnaliser**](active-directory-passwords-customize.md) - Apprenez à personnaliser l’apparence et la convivialité et le comportement du service aux besoins de votre organisation
* [**Meilleures pratiques**](active-directory-passwords-best-practices.md) - Apprenez à déployer rapidement et efficacement gérer les mots de passe de votre organisation
* [**Obtenir des informations**](active-directory-passwords-get-insights.md) - en savoir plus sur notre fonctions intégrées de reporting
* [**Forum aux questions**](active-directory-passwords-faq.md) - obtenir des réponses aux questions fréquemment posées
* [**Résolution des problèmes**](active-directory-passwords-troubleshoot.md) - Apprenez à rapidement résoudre les problèmes avec le service



[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"
