
<properties
    pageTitle="Utilisation des attributs pour créer des règles avancées | Microsoft Azure"
    description="Comment-de créer des règles avancées pour un groupe, y compris prises en charge les opérateurs de règle expression et les paramètres."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="curtand"/>


# <a name="using-attributes-to-create-advanced-rules"></a>Utilisation des attributs pour créer des règles avancées

Le portail classique Azure vous offre la possibilité de créer des règles avancées pour activer l’appartenance dynamique basée sur les attributs plus complexes pour les groupes Azure Active Directory (AD Azure).  

Lorsque tous les attributs d’un changement d’utilisateur, le système évalue toutes les règles de groupe dynamique dans un répertoire pour voir si la modification de l’attribut de l’utilisateur susceptibles de déclencher n’importe quel groupe ajoute ou supprime. Si un utilisateur satisfait à une règle sur un groupe, ils sont ajoutés en tant que membre à ce groupe. Si elles satisfont n’est plus à la règle d’un groupe, qu'il s’agit d’un membre, ils sont supprimés en tant que membres de ce groupe.

## <a name="to-create-the-advanced-rule"></a>Pour créer la règle avancée

1. Dans le [portail classique Azure](https://manage.windowsazure.com), sélectionnez **Active Directory**et puis ouvrez le répertoire de votre organisation.

2. Sélectionnez l’onglet **groupes** et ouvrez le groupe que vous voulez modifier.

3. Sélectionnez l’onglet **configurer** , sélectionnez l’option **règle avancée** , puis entrez la règle avancée dans la zone de texte.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Construire le corps d’une règle avancée

La règle avancée que vous pouvez créer pour les appartenances dynamiques pour les groupes est pour l’essentiel une expression binaire, se compose de trois parties et les résultats dans un résultat true ou false. Les trois parties sont :

- Paramètre de gauche
- Opérateur binaire
- Constante de droite

Une règle avancée complet ressemble à ceci : (leftParameter binaryOperator « RightConstant »), où l’ouverture et la parenthèse fermante sont requises pour l’expression entière binaire, entre guillemets doubles sont requis pour la constante droite et la syntaxe pour le paramètre gauche est user.property. Une règle avancée peut comporter plusieurs expressions binaires séparées par- et, - ou et - des opérateurs logiques pas.
Voici quelques exemples d’une règle avancée construite :

- (user.department - eq « Ventes »)- ou (user.department - eq « Marketing »)
- (user.department - eq « Ventes »)- et - pas (user.jobTitle-contient « SDE »)

Pour obtenir la liste complète des paramètres pris en charge et d’opérateurs de règle d’expressions, voir les sections ci-dessous.

La longueur totale du corps de votre règle avancée ne peut pas dépasser 2 048 caractères.

> [AZURE.NOTE]
>Opérations de chaîne et regex sont la casse. Vous pouvez également effectuer des tests Null, à l’aide de $null sous forme de constante, par exemple, user.department - eq $null.
Chaînes contenant devis » doit être une séquence d’échappement à l’aide de « caractère, par exemple, user.department - eq \`« Ventes ».

## <a name="supported-expression-rule-operators"></a>Opérateurs de règle d’expressions prises en charge
Le tableau suivant répertorie tous les opérateurs de règle expression pris en charge et leur syntaxe devant être utilisé dans le corps de la règle avancée :

| Opérateur        | Syntaxe         |
|-----------------|----------------|
| Pas égal à      | garanties-            |
| Égal à          | -eq            |
| Ne commence pas par | -notStartsWith |
| Commence par     | -startsWith    |
| Ne contient pas    | -notContains   |
| Contient        | -contient      |
| Ne correspondent pas       | -notMatch      |
| EQUIV           | -correspond à         |


## <a name="query-error-remediation"></a>Correction d’erreur de requête
Le tableau suivant répertorie les erreurs potentielles et comment les corriger s’ils se produisent

| Erreur d’analyse de requête     | Utilisation d’erreur       | Utilisation corrigée             |
|-----------------------|-------------------|-----------------------------|
| Erreur : Attribut non pris en charge.                                      | (user.invalidProperty - eq « Valeur »)       | (user.department - eq « valeur »)<br/>Propriété doit correspondre un dans la [liste des propriétés de prise en charge](#supported-properties).                          |
| Erreur : Opérateur n’est pas pris en charge sur attribut.                       | (user.accountEnabled-contient la valeur vrai)                                                                               | (user.accountEnabled - eq vrai)<br/>Propriété est de type booléen. Utiliser les opérateurs pris en charge (-eq ou - garanties) sur type booléen dans la liste ci-dessus.                                                                                                                                   |
| Erreur : Erreur de compilation de requête.                                      | (user.department - eq « Ventes »)- et (user.department - eq « Marketing »)(user.userPrincipalName-match"*@domain.ext") | (user.department - eq « Ventes »)- et (user.department - eq « Marketing »)<br/>Opérateurs logiques doivent correspondre un dans la liste Propriétés prises en charge ci-dessus. (user.userPrincipalName-correspond à ".*@domain.ext")or(user.userPrincipalName -correspond à "@domain.ext$")Error dans des expressions régulières. |
| Erreur : Expression binaire n’est pas au format correct.                     | (user.department – eq « Ventes ») (user.department - eq « Ventes ») (user.department-eq « Ventes »)                             | (user.accountEnabled - eq vrai)- et (user.userPrincipalName-contient"alias@domain")<br/>Requête comporte plusieurs erreurs. Parenthèse pas dans le bon endroit.                                                                                                                            |
| Erreur : Une erreur inconnue s’est produite lors de la configuration d’appartenance dynamique. | (user.accountEnabled - eq « True » AND user.userPrincipalName-contient"alias@domain")                               | (user.accountEnabled - eq vrai)- et (user.userPrincipalName-contient"alias@domain")<br/>Requête comporte plusieurs erreurs. Parenthèse pas dans le bon endroit.                                                                                                                            |

## <a name="supported-properties"></a>Propriétés prises en charge
Voici toutes les propriétés utilisateur que vous pouvez utiliser dans votre règle avancée :

### <a name="properties-of-type-boolean"></a>Propriétés de type booléen

Opérateurs autorisés

* -eq


* garanties-


| Propriétés     | Valeurs autorisées  | Utilisation                          |
|----------------|-----------------|--------------------------------|
| accountEnabled | True en false      | user.accountEnabled - eq vrai)  |
| dirSyncEnabled | null false true | (user.dirSyncEnabled - eq vrai) |

### <a name="properties-of-type-string"></a>Propriétés de type chaîne

Opérateurs autorisés

* -eq


* garanties-


* -notStartsWith


* -StartsWith


* -contient


* -notContains


* -correspond à


* -notMatch

| Propriétés                 | Valeurs autorisées                                                                                        | Utilisation                                                     |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| Ville                       | Toute valeur de chaîne ou $null                                                                           | (user.city - eq « valeur »)                                   |
| pays                    | Toute valeur de chaîne ou $null                                                                            | (user.country - eq « valeur »)                                |
| département                 | Toute valeur de chaîne ou $null                                                                          | (user.department - eq « valeur »)                             |
| nom complet                | Toute valeur de chaîne                                                                                 | (user.displayName - eq « valeur »)                            |
| facsimileTelephoneNumber   | Toute valeur de chaîne ou $null                                                                           | (user.facsimileTelephoneNumber - eq « valeur »)               |
| Prénom                  | Toute valeur de chaîne ou $null                                                                           | (user.givenName - eq « valeur »)                              |
| jobTitle                   | Toute valeur de chaîne ou $null                                                                           | (user.jobTitle - eq « valeur »)                               |
| courrier                       | Toute valeur de chaîne ou $null (adresse SMTP de l’utilisateur)                                                  | (user.mail - eq « valeur »)                                   |
| mailNickName               | Toute valeur de chaîne (alias de messagerie de l’utilisateur)                                                            | (user.mailNickName - eq « valeur »)                           |
| Mobile                     | Toute valeur de chaîne ou $null                                                                           | (user.mobile - eq « valeur »)                                 |
| objectId                   | GUID de l’objet utilisateur                                                                            | (user.objectId - eq « 1111111-1111-1111-1111-111111111111 ») |
| passwordPolicies           | Aucun DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |   (user.passwordPolicies - eq « DisableStrongPassword »)                                                      |
| physicalDeliveryOfficeName | Toute valeur de chaîne ou $null                                                                            | (user.physicalDeliveryOfficeName - eq « valeur »)             |
| code postal                 | Toute valeur de chaîne ou $null                                                                            | (user.postalCode - eq « valeur »)                             |
| preferredLanguage          | Code ISO 639-1                                                                                        | (user.preferredLanguage - eq « en-US »)                      |
| sipProxyAddress            | Toute valeur de chaîne ou $null                                                                            | (user.sipProxyAddress - eq « valeur »)                        |
| état                      | Toute valeur de chaîne ou $null                                                                            | (user.state - eq « valeur »)                                  |
| streetAddress              | Toute valeur de chaîne ou $null                                                                            | (user.streetAddress - eq « valeur »)                          |
| nom de famille                    | Toute valeur de chaîne ou $null                                                                            | (user.surname - eq « valeur »)                                |
| numéro de téléphone            | Toute valeur de chaîne ou $null                                                                            | (user.telephoneNumber - eq « valeur »)                        |
| usageLocation              | Code du pays classés deux                                                                           | (user.usageLocation - eq « US »)                             |
| userPrincipalName          | Toute valeur de chaîne                                                                                     | (user.userPrincipalName - eq"alias@domain")               |
| userType                   | invité membre $null                                                                                    | (user.userType - eq « Membre »)                              |

### <a name="properties-of-type-string-collection"></a>Propriétés de type chaîne collection

Opérateurs autorisés

* -contient


* -notContains

| Poperties      | Valeurs autorisées                        | Utilisation                                                |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails     | Toute valeur de chaîne                      | (user.otherMails-contient"alias@domain")           |
| proxyAddresses | SMTP : alias@domain smtp :alias@domain | (user.proxyAddresses-contient « SMTP :alias@domain") |

## <a name="extension-attributes-and-custom-attributes"></a>Extension attributs et attributs personnalisés
Attributs d’extension et les attributs personnalisés sont pris en charge dans les règles d’appartenance dynamique.

Attributs d’extension sont synchronisées à partir de Windows Server Active Directory local et prennent le format de « ExtensionAttributeX », où X est égal à 1-15.
Un exemple d’une règle qui utilise un attribut d’extension serait

(user.extensionAttribute15 - eq « Marketing »)

Attributs personnalisés sont synchronisées à partir de Windows Server Active Directory local ou d’une application SaaS connectée et le format de « user.extension_[GUID]\__ [attribut] », où [GUID] est l’identificateur unique dans AAD pour l’application qui a créé l’attribut dans AAD et [attribut] est le nom de l’attribut telle qu’elle a été créée.
Est un exemple d’une règle qui utilise un attribut personnalisé

User.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

Nom de l’attribut personnalisés sont accessibles dans le répertoire en demander un utilisateur l’attribut à l’aide de l’Explorateur de graphique et en recherchant le nom de l’attribut.

## <a name="direct-reports-rule"></a>Règle de collaborateurs directs
Vous pouvez maintenant remplir membres dans un groupe en fonction de l’attribut Gestionnaire d’un utilisateur.

**Pour configurer un groupe en groupe « Gestionnaire »**

1. Dans le portail Azure classique, cliquez sur **Active Directory**, puis cliquez sur le nom de l’annuaire de votre organisation.

2. Sélectionnez l’onglet **groupes** et ouvrez le groupe que vous voulez modifier.

3. Sélectionnez l’onglet **configurer** , puis **Règle avancée**.

4. Tapez la règle avec la syntaxe suivante :

    Rapports directs pour *collaborateurs directs pour {obectID_of_manager}*. Est un exemple d’une règle valide pour les collaborateurs directs

                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

    où « 62e19b97-8b3d-4d4a-a106-4ce66896a863 » est objectID du gestionnaire. L’ID d’objet sont accessibles dans Azure Active Directory dans l' **onglet profil** de la page de l’utilisateur pour l’utilisateur qui est le responsable.

3. Lorsque vous enregistrez cette règle, tous les utilisateurs qui satisfont la règle seront jointes en tant que membres du groupe. Il peut prendre quelques minutes pour le groupe pour le remplissage initial.


## <a name="using-attributes-to-create-rules-for-device-objects"></a>L’utilisation des attributs pour créer des règles pour les objets du périphérique

Vous pouvez également créer une règle qui sélectionne les objets du périphérique pour l’appartenance d’un groupe. Les attributs d’appareil suivants peuvent être utilisées :

| Propriétés              | Valeurs autorisées                  | Utilisation                                                       |
|-------------------------|---------------------------------|-------------------------------------------------------------|
| nom complet             | toute valeur de chaîne                | (device.displayName - eq « René Iphone »)                       |
| deviceOSType            | toute valeur de chaîne                | (device.deviceOSType - eq « IOS »)                             |
| deviceOSVersion         | toute valeur de chaîne                | (appareil. OSVersion - eq « 9.1 »)                                |
| isDirSynced             | null false true                 | (device.isDirSynced - eq « true »)                             |
| isManaged               | null false true                 | (device.isManaged - eq « false »)                              |
| isCompliant             | True null faux                 | (device.isCompliant - eq « true »)                             |
| deviceCategory          | toute valeur de chaîne                | (device.deviceCategory - eq « »)                              |
| deviceManufacturer      | toute valeur de chaîne                | (device.deviceManufacturer - eq « Microsoft »)                 |
| deviceModel             | toute valeur de chaîne                | (device.deviceModel - eq « IPhone 7 + »)                        |
| deviceOwnership         | toute valeur de chaîne                | (device.deviceOwnership - eq « »)                             |
| nom_domaine              | toute valeur de chaîne                | (device.domainName - eq « contoso.com »)                       |
| enrollmentProfileName   | toute valeur de chaîne                | (device.enrollmentProfileName - eq « »)                       |
| isRooted                | null false true                 | (device.deviceOSType - eq « true »)                            |
| managementType          | toute valeur de chaîne                | (device.managementType - eq « »)                              |
| unité d’organisation      | toute valeur de chaîne                | (device.organizationalUnit - eq « »)                          |
| ID de périphérique                | un identificateur valide                | (device.deviceId - eq « d4fe7726-5966-431c-b3b8-cddc8fdb717d » |

> [AZURE.NOTE]
> Ces règles appareil ne peut pas être créés à l’aide de la liste déroulante « règle simple » dans le portail classique Azure.


## <a name="additional-information"></a>Informations supplémentaires
Les articles suivants fournissent des informations supplémentaires sur Azure Active Directory.

* [Résolution des problèmes d’appartenance dynamique pour les groupes](active-directory-accessmanagement-troubleshooting.md)

* [Gestion de l’accès aux ressources des groupes Azure Active Directory](active-directory-manage-groups.md)

* [Azure Active Directory applets de commande pour configurer les paramètres de groupe](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
