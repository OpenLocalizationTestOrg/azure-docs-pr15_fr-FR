<properties
   pageTitle="Description détaillée à l’aide de l’aperçu de collaboration Azure Active Directory B2B | Microsoft Azure"
   description="Azure Active Directory B2B collaboration prend en charge les relations de votre société croisée en activant partenaires professionnels de manière sélective accéder à vos applications d’entreprise"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-detailed-walkthrough"></a>Aperçu de collaboration Azure AD B2B : procédure pas à pas détaillé

Cette procédure explique comment utiliser Azure AD B2B collaboration. En tant que l’administrateur informatique de Contoso, nous voulons partager des applications avec les employés à partir de trois sociétés partenaires. Aucune des sociétés partenaires doivent être Azure AD.

- Alice d’organigramme simplicité pour les partenaires
- Alexandre, à partir de l’organisation partenaire de support, a besoin d’accéder à un ensemble d’applications
- Carole, à partir de l’organisation partenaire complexes, a besoin d’accéder à un ensemble d’applications, puis l’appartenance aux groupes de Contoso

Une fois que les invitations sont envoyées aux utilisateurs partenaire, nous pouvons les configurer dans Azure AD pour accorder l’accès aux applications et l’appartenance aux groupes via le portail d’Azure. Nous allons commencer par ajouter Alice.

## <a name="adding-alice-to-the-contoso-directory"></a>Ajout de Alice à l’annuaire de Contoso
1. Créez un fichier .csv avec les en-têtes comme illustré, complétez uniquement Alice **messagerie** **DisplayName**et **InviteContactUsUrl**. **Nom complet** est le nom qui apparaît dans l’invitation, ainsi que le nom qui apparaît dans le répertoire Contoso Azure AD. **InviteContactUsUrl** est une méthode Alice contacter Contoso. Dans l’exemple suivant, InviteContactUsUrl Spécifie le profil LinkedIn de Contoso. Il est important de l’orthographe dans les étiquettes dans la première ligne du fichier .csv exactement comme spécifié dans la [référence de format de fichier CSV](active-directory-b2b-references-csv-file-format.md).  
![Exemple de fichier CSV pour Alice](./media/active-directory-b2b-detailed-walkthrough/AliceCSV.png)

2. Dans le portail Azure, ajouter un utilisateur dans le répertoire Contoso (Active Directory > Contoso > utilisateurs > Ajouter un utilisateur). Dans la liste déroulante Type de « utilisateurs », sélectionnez « Utilisateurs aux partenaires ». Téléchargez le fichier .csv. Vérifiez que le fichier .csv est fermé avant de le télécharger.  
![Téléchargement de fichier CSV pour Alice](./media/active-directory-b2b-detailed-walkthrough/AliceUpload.png)

3. Alice est maintenant représentée par un utilisateur externe dans le répertoire Contoso Azure AD.  
![Alice est répertoriée dans Azure Active Directory](./media/active-directory-b2b-detailed-walkthrough/AliceInAD.png)

4. Alice reçoit le message suivant.  
![Message électronique d’invitation Alice](./media/active-directory-b2b-detailed-walkthrough/AliceEmail.png)

5. Alice clique sur le lien, et elle est invitée à accepter l’invitation et se connecter à l’aide de ses informations d’identification de travail. Si Alice n’est pas dans le répertoire Azure AD, Alice est invitée à s’inscrire.  
![S’inscrire après invitation Alice](./media/active-directory-b2b-detailed-walkthrough/AliceSignUp.png)

6. Alice est redirigée vers l’application Access Panneau de configuration, vide jusqu'à ce qu’elle a accès aux applications.  
![Panneau d’accès pour Alice](./media/active-directory-b2b-detailed-walkthrough/AliceAccessPanel.png)

Cette procédure permet de la forme la plus simple de collaboration B2B. En tant qu’utilisateur dans le répertoire Contoso Azure AD, Alice peut être autorisée à accéder aux applications et aux groupes via le portail d’Azure. Maintenant nous allons ajouter Bob, qui a besoin d’accéder aux applications Moodle et Salesforce.

## <a name="adding-bob-to-the-contoso-directory-and-granting-access-to-apps"></a>Ajout de Bob au répertoire Contoso et accorder l’accès aux applications
1. Utiliser Windows PowerShell avec le Module Azure AD installé pour rechercher l’ID d’application de Moodle et Salesforce. Les ID peuvent être récupérés à l’aide de l’applet de commande : `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` Cela affiche une liste de toutes les applications disponibles dans Contoso et leurs AppPrincialIds.  
![Extraire les ID pour Alexandre](./media/active-directory-b2b-detailed-walkthrough/BobPowerShell.png)

2. Créer un fichier .csv contenant Bob messagerie et nom complet, **InviteAppID**, **InviteAppResources**et InviteContactUsUrl. Remplir **InviteAppResources** avec la AppPrincipalIds de Moodle et Salesforce trouvé à partir de PowerShell, séparé par un espace. Remplir **InviteAppId** avec la même AppPrincipalId de Moodle pour personnaliser le message électronique et se connecter pages avec un logo Moodle.  
![Exemple de fichier CSV pour Alexandre](./media/active-directory-b2b-detailed-walkthrough/BobCSV.png)

3. Téléchargez le fichier .csv à partir du portail Azure comme il a été fait Alice. Alexandre est désormais un utilisateur externe dans le répertoire Contoso Azure AD.

4. Bob reçoit le message suivant.  
![Message électronique d’invitation à Bob](./media/active-directory-b2b-detailed-walkthrough/BobEmail.png)

5. Alexandre clique sur le lien et est invité à accepter l’invitation. Une fois qu’il est connecté, il est redirigé vers le panneau d’accès et déjà utiliser Moodle et Salesforce.  
![Panneau d’accès pour Bob](./media/active-directory-b2b-detailed-walkthrough/BobAccessPanel.png)

Nous allons ajouter Carole ensuite ayant besoin d’accéder à des applications, ainsi que l’appartenance aux groupes dans le répertoire de Contoso.

## <a name="adding-carol-to-the-contoso-directory-granting-access-to-apps-and-giving-group-membership"></a>Ajout de Carole au répertoire Contoso, accorder l’accès aux applications et donnant l’appartenance aux groupes

1. Utiliser Windows PowerShell avec le Module Azure AD installé pour rechercher l’ID de l’application et de groupe au sein de Contoso.
 - Récupérer AppPrincipalId à l’aide de la cmdlet `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`, même en ce qui concerne Alexandre
 - Récupérer ObjectId pour les groupes à l’aide de la cmdlet `Get-MsolGroup | fl DisplayName, ObjectId`. Cela affiche une liste de tous les groupes de Contoso et leurs ObjectID. ID de groupe peuvent également être récupérés en tant que l’ID d’objet dans l’onglet Propriétés du groupe dans le portail Azure.  
![Récupérer ID et groupes de Carole](./media/active-directory-b2b-detailed-walkthrough/CarolPowerShell.png)

2. Créer un fichier .csv, remplissage de messagerie de Carole, DisplayName, InviteAppID, InviteAppResources, **InviteGroupResources**et InviteContactUsUrl. **InviteGroupResources** est remplie par l’ObjectID des groupes MyGroup1 et externes, séparés par un espace.  
![Exemple de fichier CSV pour Carole](./media/active-directory-b2b-detailed-walkthrough/CarolCSV.png)

3. Téléchargez le fichier .csv via le portail d’Azure.

4. Carole est un utilisateur dans le répertoire Contoso et est également un membre des groupes MyGroup1 et externes, comme illustré dans le portail Azure.  
![Carole est répertoriée dans un groupe dans Active Directory Azure](./media/active-directory-b2b-detailed-walkthrough/CarolGroup.png)

5. Carole permet de recevoir un message électronique contenant un lien pour accepter l’invitation. Une fois qu’elle se connecte, il est redirigée vers le panneau d’accès App à accéder à Moodle et Salesforce.  

C’est tout est à l’ajout d’utilisateurs à partir d’entreprises partenaire en collaboration Azure AD B2B. Cette procédure pas à pas a montré comment ajouter des utilisateurs Alice, Bob et Carole à l’annuaire de Contoso à l’aide de trois fichiers .csv distincts. Ce processus peuvent être effectué plus facilement par condensation les fichiers .csv distincts en un seul fichier.  
![Exemple de fichier CSV pour Alice, Bob et Carole](./media/active-directory-b2b-detailed-walkthrough/CombinedCSV.png)

## <a name="related-articles"></a>Articles connexes
Visitez notre d’autres articles sur la collaboration Azure AD B2B :

- [Quelle est la collaboration Azure AD B2B ?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Mode de fonctionnement](active-directory-b2b-how-it-works.md)
- [Référence des formats de fichier CSV](active-directory-b2b-references-csv-file-format.md)
- [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
- [Modifications de l’attribut objet utilisateur externe](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitations aperçu en cours](active-directory-b2b-current-preview-limitations.md)
- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
