<properties
    pageTitle="Qu’est de licence Microsoft Azure Active Directory ? | Microsoft Azure"
    description="Description du contrat de licence Microsoft Azure Active Directory, son fonctionnement, la mise en route et recommandations, y compris Office 365, Microsoft Intune et Azure Active Directory Premium et éditions de base"
    services="active-directory"
      keywords="Gestion des licences Azure AD"
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

# <a name="what-is-microsoft-azure-active-directory-licensing"></a>Qu’est de licence Microsoft Azure Active Directory ?

##<a name="description"></a>Description
Azure Active Directory (AD Azure) est identité de Microsoft comme une solution de Service (IDaaS) et la plateforme. Azure AD est proposé dans un nombre de versions fonctionnelles et techniques allant Azure AD libres, ce qui est disponible avec tous les services Microsoft tels que Office 365 et Dynamics, Intune Microsoft Azure (Azure AD ne génère pas les éventuels frais de consommation dans ce mode,) pour Azure AD payé les versions entreprise mobilité Suite (EMS), Azure AD Premium et Basic, ainsi que l’authentification multifacteur Azure Multifacteur. Comme la plupart des services en ligne Microsoft, plus Azure AD payé versions sont fournis par droits d’accès par utilisateur qu’ils sont dans Office 365, Microsoft Intune et Azure AD. Dans ce cas, l’achat de service est représenté par un ou plusieurs abonnements et chaque abonnement comprend un numéro avant achat de licences de votre client. Droits d’accès par utilisateur sont obtenus par le biais des affectations de licence, création d’un lien entre l’utilisateur et le produit, activer les composants du service pour l’utilisateur et l’utilisation d’une des licences prépayées.

[Essai d’Azure AD premium maintenant.](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

> [AZURE.NOTE] Portail d’administration AD Azure est un composant du portail classique Azure. Bien qu’à l’aide d’Azure AD ne nécessite pas les achats Azure, accéder à ce portail requiert un abonnement Azure actif ou un [abonnement d’évaluation Azure](https://azure.microsoft.com/pricing/free-trial/).

Pour obtenir une vue d’ensemble des fonctionnalités de service Azure AD, voir [Nouveautés Azure AD](active-directory-whatis.md).
[Pour plus d’informations sur les niveaux de service Azure AD](https://azure.microsoft.com/support/legal/sla/)

> [AZURE.NOTE]  Abonnements Azure retenues sont différents : tandis qu’également représentée dans votre annuaire, ces abonnements permettre la création de ressources Azure et faites-les correspondre à votre mode de paiement. Dans ce cas il n’existe aucun nombre de licences associé à l’abonnement. Association d’utilisateurs avec l’abonnement, l’accès des utilisateurs à la gestion des ressources de l’abonnement, est atteint en leur accordant des autorisations pour fonctionner sur Azure ressources mappées à l’abonnement.


##<a name="how-does-azure-ad-licensing-work"></a>Comment fonctionne la gestion des licences Azure AD ?

Licence basée (basées sur le bénéfice) Azure AD services fonctionnent par l’activation d’un abonnement de votre client de service d’annuaire Azure AD. Une fois que l’abonnement est active les fonctionnalités du service peuvent être gérées par les administrateurs de service d’annuaire et utilisées par les utilisateurs sous licence.

Lorsque vous achetez ou activez entreprise mobilité Suite, Azure AD Premium ou Azure AD base, dans l’annuaire est mis à jour avec l’abonnement, y compris la période de validité et licences prépayées. Informations de votre abonnement, notamment l’état, prochain événement du cycle de vie et le nombre de licences attribuées ou disponibles est disponible via le portail classique Azure sous l’onglet des licences pour le répertoire. C’est également à l’endroit idéal pour gérer vos affectations de licence.

Chaque abonnement est constitué par un ou plusieurs offres de service, chaque mappage le niveau fonctionnel inclus du type de service ; par exemple, Azure AD, l’authentification Multifacteur Azure, Microsoft Intune, Exchange Online ou SharePoint Online. Gestion des licences Azure AD ne nécessite pas de gestion au niveau du plan de service. Ceci est différent de Office 365 qui s’appuie sur ce mode configuration avancée pour gérer l’accès aux services inclus. Azure AD dépend de la configuration du service, activer les fonctionnalités et la gestion des autorisations individuelles.

En règle générale, les informations sur l’abonnement Azure AD sont gérées via le portail classique Azure, sous l’onglet des licences pour le répertoire. Les abonnements Azure Active Directory, à l’exception de Azure AD Premium, n’apparaissent pas dans le portail d’Office.

> [AZURE.IMPORTANT] Azure AD Premium et Basic, ainsi que les abonnements Enterprise mobilité Suite, sont limités à leur répertoire/client mis en service. Abonnements ne peuvent pas être fractionner entre annuaires ou utilisés pour autorisent les utilisateurs dans d’autres annuaires. Déplacement d’un abonnement entre annuaires est possible mais nécessite l’envoi d’un tickets de support ou des annulations et ré-achat dans le cas d’achats directs.

> Lorsque vous achetez Azure AD ou Enterprise mobilité Suite à l’activation de l’abonnement licence en Volume sera-t-elle automatique lorsque le contrat inclut d’autres Microsoft Online services, par exemple, Office 365.

Payé Azure fonctionnalités AD s’étalent sur la largeur de l’annuaire. Voici quelques exemples :
- Groupe affectation basée aux applications, qui est activée dans l’application spécifique que vous gérez.
- Avancées et les fonctionnalités de gestion des libre-service du groupe sont disponibles sous la configuration du répertoire ou au sein du groupe spécifique.
- Rapports de sécurité Premium sont sous l’onglet Création de rapports
- Découverte d’applications cloud s’affiche dans le portail Azure sous identité.

###<a name="assigning-licenses"></a>Attribution de licences
Obtention d’un abonnement est il que vous suffit de configurer les fonctionnalités payées, à l’aide de votre annonce Azure payé fonctionnalités requiert distribuer des licences aux personnes appropriées. En règle générale, tous les utilisateurs qui doivent y accéder à ou qui est géré tout au long une annonce Azure payé fonctionnalité doivent être affectée à une licence. Une attribution de licence est un mappage entre un utilisateur et un service que vous avez acheté, tel que Azure AD Premium, Basic ou entreprise mobilité Suite.

Gérer les utilisateurs dans votre annuaire doivent disposer d’une licence est simple. Il est possible par affectation à un groupe pour créer des règles d’affectation via le portail d’administration Azure AD ou par attribution de licences directement aux personnes appropriées, via un portail, PowerShell ou API. Lorsque vous attribuez des licences à un groupe, tous les membres du groupe doivent être affectées une licence. Si les utilisateurs sont ajoutés ou supprimés du groupe, ils doivent être affectées ou supprimer la licence appropriée. Affectation de groupe peut utiliser une gestion de groupe à votre disposition et est cohérente avec affectation basée sur un groupe pour les applications. À l’aide de cette approche, vous pouvez configurer des règles telles que tous les utilisateurs dans votre annuaire sont affectés automatiquement, vous assurer que tout le monde avec la fonction appropriée dispose d’une licence ou même déléguer la décision à d’autres responsables de l’organisation.

Avec les affectations de licence basée sur un groupe, tout utilisateur manque un lieu d’utilisation hériteront l’emplacement du répertoire lors de l’affectation. Cet emplacement peut être modifié par l’administrateur à tout moment. Dans les cas où l’affectation automatique a échoué en raison d’erreur, les informations utilisateur sous ce type de licence refléteront cet état.

##<a name="getting-started-with-azure-ad-licensing"></a>Prise en main avec Azure AD licences

Prise en main Azure AD est simple ; Vous pouvez toujours créer votre annuaire dans le cadre de l’inscription à un Azure gratuit d’évaluation. [En savoir plus sur l’inscription en tant qu’organisation](sign-up-organization.md). Les éléments suivants peuvent vous aider à vous assurer que votre annuaire est mieux aligné avec d’autres services Microsoft peuvent consommer ou envisagez d’utiliser et vos objectifs en obtenant le service.

Voici quelques recommandations :
- Si vous utilisez déjà les services d’organisation de Microsoft, vous disposez déjà d’un répertoire Azure AD. Dans ce cas, vous devez continuer à utiliser le même répertoire pour d’autres services, afin que la gestion des identités core, y compris la mise en service et hybride l’authentification unique, peut être utilisée dans les services. Vos utilisateurs auront une expérience d’ouverture de session unique et lui facilitera à partir des fonctions plus complètes sur les services. Par conséquent, si vous décidez d’acheter une annonce Azure payé service pour votre force de vente, nous vous recommandons d’utiliser le même répertoire pour effectuer cette action.
- Si vous envisagez d’utiliser un jeu différent des utilisateurs (partenaires, clients et ainsi de suite), ou si vous souhaitez évaluer services Azure AD et souhaitez le faire dans isolement de votre service de production, ou si vous recherchez pour configurer un environnement sandbox pour vos services, nous vous recommandons de créer un nouveau répertoire via le portail classique Azure Azure Azure AD. [En savoir plus sur la création d’un répertoire Azure AD dans le portail classique Azure](active-directory-licensing-directory-independence.md). Le nouveau répertoire est créé avec votre compte en tant qu’utilisateur externes avec des autorisations d’administrateur général. Lorsque vous connectez au portail classique Azure avec ce compte, vous ne pourrez pas voir ce répertoire et accéder à toutes les tâches d’administration directory. Nous vous conseillons de créer un compte local avec des privilèges appropriés pour gérer d’autres services de Microsoft (celles qui sont pas accessibles via le portail classique Azure). [En savoir plus sur la création de comptes d’utilisateur dans Active Directory Azure](active-directory-create-users.md).

> [AZURE.NOTE] Azure AD prend en charge externes « utilisateurs », les comptes d’utilisateur dans une instance d’Azure AD qui ont été créés à l’aide d’un compte Microsoft (MSA) ou une identité Azure AD à partir d’un autre répertoire. Si nous êtes en train extension de cette fonctionnalité dans tous les services d’organisation de Microsoft, immédiatement ceux-ci comptes pas pris en charge certains des expériences les services ; par exemple, le portail d’administration Office 365 ne prend pas en charge ces utilisateurs. Par conséquent, les utilisateurs externes avec les comptes Microsoft ne sera pas en mesure d’accéder à tout le portail d’administration Office 365 pendant que les utilisateurs externes à partir d’autres annuaires Azure AD sont ignorées. Dans ce cas, uniquement le compte d’utilisateur local, l’annonce Azure ou Office 365 annuaire dans lequel l’utilisateur a été créé, seraient accessibles via ces expériences.

Comme indiqué, Azure AD comporte différentes versions payants. Ces versions présentent de légères différences dans leur disponibilité achat :


| Produit   | EA/LICENCES EN VOLUME     | Ouvrir  |   FOURNISSEUR |   Droits d’utilisation du code produit du fabricant  |   Achat direct | Version d’évaluation |
|---|---|---|---|---|---|---|
| Entreprise mobilité Suite |   X | X | X | X |  |      X |
| Azure AD Premium  | X | X | X |   | X | X |
| Azure AD Basic    | X | X | X | X |  <br /> |  <br />  |

###<a name="select-one-or-more-license-trials"></a>Sélectionnez un ou plusieurs essais de licence
 Dans tous les cas, vous pouvez activer un abonnement d’évaluation Azure AD Premium ou entreprise mobilité Suite en sélectionnant la version d’évaluation spécifique souhaité sous l’onglet licences dans votre annuaire. Une version d’évaluation contient un abonnement de 30 jours avec 100 licences.

![Plans de licence d’évaluation Azure Active Directory](./media/active-directory-licensing-what-is/trial_plans.png)

![Plans de licence d’évaluation Enterprise mobilité Suite](./media/active-directory-licensing-what-is/EMS_trial_plan.png)

![Plans de licence d’évaluation Active](./media/active-directory-licensing-what-is/active_license_trials.png)

###<a name="assign-licenses"></a>Attribuer des licences
Une fois que l’abonnement est actif, vous devez vous attribuer une licence et actualisez le navigateur pour vous assurer que vous voyez toutes les fonctionnalités de votre. L’étape suivante consiste à attribuer des licences aux utilisateurs qui devront accéder ou figurer dans payé fonctionnalités Azure AD. Comme expliqué plus haut dans « Attribution des licences », la meilleure façon de procéder est d’identifier le groupe qui représente l’audience voulue et affectez-le à la licence ; de cette façon, les utilisateurs qui sont ajoutés ou supprimés du groupe sur son cycle de vie est affectés à ou supprimés de la licence.

Pour attribuer une licence à un groupe ou les utilisateurs individuels, sélectionnez le plan de la licence que vous voulez attribuer, puis cliquez sur **affecter** sur la barre de commandes.

![Plans de licence d’évaluation Active](./media/active-directory-licensing-what-is/assign_licenses.png)

Une fois dans la boîte de dialogue d’affectation pour le plan sélectionné, vous pouvez sélectionner les utilisateurs et en les ajoutant à la colonne **affecter** à droite. Vous pouvez parcourir la liste des utilisateurs ou recherchez des personnes spécifiques à l’aide de la recherche de verre dans la partie supérieure droite de la grille de l’utilisateur. Pour attribuer des groupes, sélectionnez « Groupes » dans le menu **Afficher** , puis cliquez sur le bouton Vérifier les droite pour actualiser les affectations qui sont affichent.

![Attribution de licences à des groupes](./media/active-directory-licensing-what-is/assign_licenses_to_groups.png)

Vous pouvez maintenant rechercher ou parcourir les groupes et les ajouter à la colonne **attribuer** de la même façon. Vous pouvez utiliser ces pour assigner une combinaison des utilisateurs et groupes en une seule opération. Pour terminer le processus d’affectation, cliquez sur le bouton de vérification dans le coin inférieur droit de la page.

![Message de progression affectations de licence](./media/active-directory-licensing-what-is/license_assignment_progress_message.png)

Lorsqu’un groupe est affecté, ses membres héritent les licences dans les 30 minutes, mais généralement quelques minutes 1 et 2.

Erreurs d’affectation peuvent se produire lors de l’affectation de licence Azure AD, mais sont relativement rares. Présence d’erreurs d’affectation est limitées à :
- Conflit d’affectation - lorsqu’un utilisateur a été affecté précédemment une licence qui est incompatible avec la licence actuelle. Dans ce cas, attribution de la nouvelle licence nécessite la suppression de l’option précédente.
- Les licences disponibles dépassées - lorsque le nombre d’utilisateurs dans les groupes sont affectés dépasse les licences disponibles, état de l’affectation utilisateurs refléteront une défaillance pour affecter en raison de licences manquants.

###<a name="view-assigned-licenses"></a>Consulter attribué les licences

Une vue récapitulative des licences attribuées, y compris les événements de cycle de vie abonnement disponible, affectées et suivant sont affichés dans l’onglet **licences** .

![Afficher le nombre de licences attribuées](./media/active-directory-licensing-what-is/view_assigned_licenses.png)

Une liste détaillée des utilisateurs et groupes, y compris l’état de l’affectation et le chemin d’accès (direct ou héritée à partir d’un ou plusieurs groupes) est disponible lors de la navigation dans un plan de licence.

![Affichage des détails des licences attribuées pour un plan de licence](./media/active-directory-licensing-what-is/assigned_licenses_detail.png)

Supprimer des licences est aussi simple que leur affectation. Si l’utilisateur est affectée directement ou pour un groupe affecté, vous pouvez supprimer la licence en sélectionnant le type de licence, **Supprimer**, ajout de l’utilisateur ou groupe à la liste de suppression et confirmer cette action. Par ailleurs, vous pouvez ouvrir un type de licence, sélectionnez l’utilisateur spécifique ou le groupe et appuyez sur **Supprimer** dans la barre de commandes. Pour mettre fin à l’héritage d’un utilisateur d’une licence d’un groupe, il vous suffit de supprimer l’utilisateur du groupe.

###<a name="extending-trials"></a>Développement d’essais

Extensions d’évaluation pour les clients sont disponibles en libre-service via le portail Office 365. Un administrateur de clients peut accéder au [portail Office](https://portal.office.com/#Billing) (access dépend des autorisations pour le portail Office), puis sélectionnez votre version d’évaluation Azure AD Premium. Cliquez sur le lien **étendre d’évaluation** et suivez les instructions. Vous devrez entrer une carte de crédit, mais elle ne sera pas facturé.

![Développement d’une version d’évaluation de licence dans le portail Office](./media/active-directory-licensing-what-is/extend_license_trial.png)

Les clients peuvent également demander une extension d’évaluation en envoyant une demande de support. Un administrateur client pouvez naviguer vers le portail d’Office 365 [prend en charge de la page](http://aka.ms/extendAADtrial) (accès dépend des autorisations de la page de support Office). Dans cette page, sélectionnez « Abonnements et essais » sous fonctionnalités et « Version d’évaluation questions » sous manifestation. Enfin, entrez les informations dans les cas

![Développement d’une version d’évaluation de licence à l’aide d’une demande de support](./media/active-directory-licensing-what-is/alternate_office_aad_trial_extension.png)

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez maintenant être prêt à configurer et utiliser certaines fonctionnalités Azure AD Premium.

- [Réinitialisation du mot de passe libre-service](active-directory-manage-passwords.md)
- [Gestion des groupes libre-service](active-directory-accessmanagement-self-service-group-management.md)
- [Intégrité des Azure AD Connect](active-directory-aadconnect-health.md)
- [Affectation de groupe pour les applications](active-directory-manage-groups.md)
- [Authentification multifacteur Azure](../multi-factor-authentication/multi-factor-authentication.md)
- [Achat direct de licences Azure AD Premium](http://aka.ms/buyaadp)
