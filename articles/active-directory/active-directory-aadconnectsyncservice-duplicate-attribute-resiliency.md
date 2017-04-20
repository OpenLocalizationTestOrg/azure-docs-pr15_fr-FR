<properties
    pageTitle="Identité synchronisation et dupliquer attribut la résilience | Microsoft Azure"
    description="Nouveau comportement de la façon de gérer des objets avec des conflits de nom UPN ou ProxyAddress pendant la synchronisation d’annuaires à l’aide d’Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="markusvi"/>



# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Identité synchronisation et dupliquer attribut la résilience
Résilience attribut en double est une fonctionnalité dans Azure Active Directory afin de ne pas friction due **UserPrincipalName** et **ProxyAddress** les conflits lors de l’exécution d’un des outils de synchronisation de Microsoft.

Ces deux attributs sont généralement requises pour être unique dans tous les objets **utilisateur**, **groupe**ou **Contact** dans un client Azure Active Directory donné.

> [AZURE.NOTE] Seuls les utilisateurs peuvent avoir UPN.

Le nouveau comportement de cette fonctionnalité permet aux est dans la partie cloud du pipeline de synchronisation, il est donc client indépendante et approprié pour n’importe quel produit de synchronisation Microsoft, y compris Azure AD Connect, DirSync et MIM + connecteur. Dans ce document, le terme générique « client de synchronisation » permet de représenter l’une de ces produits.

## <a name="current-behavior"></a>Comportement en cours
S’il existe une tentative de mise en service un nouvel objet avec une valeur de nom UPN ou ProxyAddress qui enfreint cette contrainte d’unicité, Azure Active Directory bloque entraînerait la création de cet objet. De même, si un objet est mis à jour avec un nom UPN ou ProxyAddress non unique, la mise à jour échoue. La tentative de mise à disponibilité ou mise à jour est retentée par le client de synchronisation lors de chaque cycle exporter et continue à échouer jusqu'à ce que le conflit est résolu. Un message électronique rapport d’erreur est généré lors de chaque tentative et une erreur est enregistrée par le client de synchronisation.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Comportement avec la résilience attribut en double
Au lieu d’ignore complètement mise en service ou mettre à jour d’un objet avec un attribut en double, Azure Active Directory « met en quarantaine « l’attribut en double qui enfreint la contrainte unique. Si cet attribut est requis pour la mise en service, comme UserPrincipalName, le service affecte une valeur d’espace réservé. Le format de ces valeurs temporaires est  
«***<OriginalPrefix>+<4DigitNumber>@<InitialTenantDomain>. onmicrosoft.com***».  
Si l’attribut n’est pas requis, comme un **ProxyAddress**, Azure Active Directory simplement met en quarantaine de l’attribut de conflit et effectue la création d’un objet ou une mise à jour.

À la mise en quarantaine de l’attribut, les informations sur le conflit sont envoyées dans le même message de rapport d’erreur utilisé dans l’ancien comportement. Toutefois, cette information n’apparaît dans le rapport d’erreurs une seule fois, lorsque la mise en quarantaine se produit, il ne continue pas à enregistrer dans des messages électroniques futures. En outre, étant donné que l’exportation pour cet objet a réussi, le client de synchronisation n’enregistre pas une erreur et ne pas de nouvelle tentative la créer / mettre à jour opération lors de la synchronisation ultérieure cycles.

Pour prendre en charge ce comportement un attribut a été ajouté pour les classes d’objets utilisateur, groupe et Contact :  
**DirSyncProvisioningErrors**

Il s’agit d’un attribut à valeurs multiples qui permet de stocker les attributs en conflit qui enfreint la contrainte unicité doivent leur être ajoutés normalement. Une tâche de minuteur en arrière-plan a été activée dans Azure Active Directory qui s’exécute chaque heure pour rechercher des conflits d’attributs en double qui ont été résolus et supprime automatiquement les attributs en question de mise en quarantaine.

### <a name="enabling-duplicate-attribute-resiliency"></a>L’activation de la résilience attribut en double
Résilience attribut en double sera le nouveau comportement par défaut sur toutes les installations Azure Active Directory. Sera activé par défaut pour tous les clients qui activé la synchronisation pour la première fois sur 22 août 2016 ou version ultérieure. Clients concernés synchronisation avant cette date ont la fonctionnalité est activée par lots. Ce déploiement commencera dans septembre 2016 et recevront une notification par courrier électronique au contact de notification technique chaque locataire avec la date spécifique lorsque la fonctionnalité sera activée.

Une fois que dupliquer attribut résilience a été activée, il ne peut pas être désactivé.

Pour vérifier si la fonctionnalité est activée pour votre client, vous pouvez le faire en téléchargeant la dernière version du module Azure Active Directory PowerShell et en cours d’exécution :

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

Si vous voulez activer le fait de la fonction avant qu’il est activé pour votre client, vous pouvez le faire en téléchargeant la dernière version du module Azure Active Directory PowerShell et en cours d’exécution :

`Set-MsolDirSyncFeature -Feature DuplicateUPNResiliency -Enable $true`

`Set-MsolDirSyncFeature -Feature DuplicateProxyAddressResiliency -Enable $true`

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identification des objets avec DirSyncProvisioningErrors
Il existe actuellement deux méthodes pour identifier les objets qui comportent des ces erreurs en raison de propriété en double est en conflit, Azure Active Directory PowerShell et le portail d’administration d’Office 365. Il existe des plans d’étendre aux autres portail rapports à l’avenir.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
Pour les applets de commande PowerShell dans cette rubrique, les éléments suivants sont vrai :

- Toutes les applets de commande respectent la casse.
- **– ErrorCategory PropertyConflict** doivent toujours être inclus. Il n’existe actuellement aucune d’autres types de **ErrorCategory**, mais cela peut être prolongée à l’avenir.

Tout d’abord, commencez par en cours d’exécution **MsolService de se connecter** et en entrant des informations d’identification pour un administrateur client.

Puis, utilisez les opérateurs et les applets de commande suivantes pour afficher les erreurs de différentes manières :

1. [Afficher toutes les](#see-all)

2. [Par Type de propriété](#by-property-type)

3. [Par valeur en conflit](#by-conflicting-value)

4. [À l’aide d’une recherche de chaîne](#using-a-string-search)

5. [Tri](#sorted)

6. [Dans une quantité limitée ou l’intégralité](#in-a-limited-quantity-or-all)


#### <a name="see-all"></a>Afficher toutes les
Une fois connecté, pour afficher la liste générale des attributs de mise en service erreurs dans le client, exécutez :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Vous obtenez un résultat comme suit :  
 ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  


#### <a name="by-property-type"></a>Par type de propriété
Pour afficher les erreurs par type de propriété, ajoutez l’indicateur **- PropertyName** avec l’argument **UserPrincipalName** ou **ProxyAddresses** :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Ou

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Par valeur en conflit
Pour afficher les erreurs liées à une propriété spécifique ajouter l’indicateur **- PropertyValue** (**- PropertyName** doit être utilisé également lorsque vous ajoutez cet indicateur) :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`


#### <a name="using-a-string-search"></a>À l’aide d’une recherche de chaîne
Pour effectuer une recherche de chaîne large utilisez l’indicateur **- SearchString** . Cela peut être utilisée indépendamment de tous les indicateurs ci-dessus, à l’exception **-ErrorCategory PropertyConflict**, qui est toujours requis :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>Dans une quantité limitée ou l’intégralité
1. **MaxResults <Int> ** peut être utilisé pour limiter la requête à un certain nombre de valeurs.

2. **Tous les** peut servir à vérifier que tous les résultats sont extraits dans le cas où il existe un grand nombre d’erreurs.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Portail d’administration Office 365

Vous pouvez afficher les erreurs de synchronisation d’annuaire dans le centre d’administration Office 365. Le rapport dans le portail Office 365 affiche uniquement les objets **utilisateur** ayant ces erreurs. Il n’affiche pas d’informations sur les conflits entre **les groupes** et les **Contacts**.


![Utilisateurs actifs] (./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "Utilisateurs actifs")

Pour obtenir des instructions sur l’affichage des erreurs de synchronisation d’annuaire dans le centre d’administration Office 365, voir [identifier des erreurs de synchronisation d’annuaire dans Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).


### <a name="identity-synchronization-error-report"></a>Rapport d’erreur de synchronisation d’identité
Lorsqu’un objet avec un conflit d’attribut en double est géré avec ce nouveau comportement une notification est incluse dans le message de rapport d’erreur de synchronisation identité standard qui est envoyé à la Notification technique contact pour le client. Cependant, il existe des changements importants dans ce comportement. Auparavant, les informations sur un conflit d’attribut en double apparaît dans tous les rapports d’erreur suivants jusqu'à ce que le conflit a été résolu. Avec ce nouveau comportement, la notification d’erreur pour un conflit donné uniquement apparaît-il une fois - au moment où que l’attribut en conflit est mis en quarantaine.

Voici un exemple de quoi ressemble la notification par courrier électronique pour un conflit ProxyAddress :  
    ![Utilisateurs actifs](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Active Users")  

## <a name="resolving-conflicts"></a>Résolution des conflits
Résolution des problèmes tactiques de stratégie et la résolution de ces erreurs doivent être différents pas de la façon dont les erreurs attribut en double ont été gérées par le passé. La seule différence est que la tâche du minuteur balayage par le biais du client sur le côté de service pour ajouter automatiquement l’attribut en question à l’objet approprié une fois que le conflit est résolu.

L’article suivant décrit les diverses stratégies de résolution des problèmes et résolution : [attributs non valides empêchent la synchronisation d’annuaires dans Office 365 ou en double](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Problèmes connus
Aucune de ces problèmes connus entraîne une dégradation des perte ou un service de données. Plusieurs d'entre eux sont esthétiques, d’autres personnes provoquent des erreurs d’attribut dupliqué levée au lieu de l’attribut de conflit de mise en quarantaine standard «*résilience préliminaire*», et un autre entraîne certaines erreurs afin d’exiger manuelle complémentaire de réparation à distance.

**Comportement principal :**

1. Objets avec des configurations attribut spécifique continuent à recevoir des erreurs d’exportation plutôt que d’utiliser l’ou les attributs en double est mis en quarantaine.  
Par exemple :

    un. Nouvel utilisateur est créé dans Active Directory avec un nom UPN de **Joe@contoso.com** et ProxyAddress**smtp:Joe@contoso.com**

    b. Les propriétés de cet objet sont en conflit avec un groupe existant, où se trouve ProxyAddress **SMTP:Joe@contoso.com**.

    c. Lors de l’exportation, une erreur de **conflit ProxyAddress** est levée plutôt que de laisser les attributs de conflit mis en quarantaine. L’opération est retentée lors de chaque cycle de synchronisation suivantes, comme il aurait été avant l’activation de la fonctionnalité de permanence.

2. Si deux groupes sont créés en local avec la même adresse SMTP, une ne parvient pas à disposition sur la première tentative avec une erreur **ProxyAddress** en double standard. Toutefois, des valeurs en double sont correctement mis en quarantaine lors de la prochaine synchronisation.

**Rapport du portail d’office**:

1. Message d’erreur détaillé pour deux objets d’une série de conflit UPN est la même. Cela signifie qu’ils ont les deux eu leur UPN changé / mise en quarantaine, en fait un seul d'entre eux avait toutes les données modifiées.

2. Message d’erreur détaillé pour un conflit UPN affiche displayName incorrect pour un utilisateur qui a été leur UPN changé/mis en quarantaine. Par exemple :

    un. **L’utilisateur A** synchronise en premier avec **UPN = User@contoso.com **.

    b. **Utilisateur B** tenté d’être synchronisé ensuite avec **UPN = User@contoso.com **.

    c. **L’utilisateur B** UPN est modifié en **User1234@contoso.onmicrosoft.com** et **User@contoso.com** est ajouté à **DirSyncProvisioningErrors**.

    d. Le message d’erreur pour **l’Utilisateur B** doit indiquer **qu’un utilisateur** a déjà **User@contoso.com** comme un nom UPN, mais il montre displayName propre **Utilisateur B** .



**Rapport d’erreur de synchronisation d’identité**:

Le lien de la *procédure à suivre pour résoudre ce problème* est incorrect :  
    ![Utilisateurs actifs](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Active Users")  

Il doit pointer vers [https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency).


## <a name="see-also"></a>Voir aussi

- [Azure AD Connect synchronisation](active-directory-aadconnectsync-whatis.md)

- [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

- [Identifier les erreurs de synchronisation d’annuaires dans Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)
