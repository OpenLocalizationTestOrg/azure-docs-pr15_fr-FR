<properties
    pageTitle="Ajouter votre nom de domaine personnalisé Preview Azure Active Directory | Microsoft Azure"
    description="Comment ajouter des noms de domaine de votre entreprise à Azure Active Directory et comment vérifier le nom de domaine."
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="curtand"/>

# <a name="add-a-custom-domain-name-to-azure-active-directory-preview"></a>Ajouter un nom de domaine personnalisé Preview Azure Active Directory

> [AZURE.SELECTOR]
- [Portail Azure](active-directory-domains-add-azure-portal.md)
- [Portail classique Azure](active-directory-add-domain.md)

Vous avez un ou plusieurs noms de domaine dont votre organisation utilise pour affaires et vos utilisateurs se connecter à votre réseau d’entreprise à l’aide de votre nom de domaine d’entreprise. Utilisation de l’aperçu Azure Active Directory (AD Azure), vous pouvez ajouter votre nom de domaine d’entreprise à Azure AD également. [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) Ainsi, vous permet d’attribuer des noms d’utilisateur dans le répertoire que vous êtes habitués à vos utilisateurs, tel que ‘alice@contoso.com.’ le processus est simple :

1. Ajoutez le nom de domaine personnalisé à votre répertoire
2. Ajouter une entrée DNS pour le nom de domaine auprès du bureau d’enregistrement du nom de domaine
3. Vérifiez le nom de domaine personnalisé dans Azure Active Directory

## <a name="how-do-i-add-a-domain-name"></a>Comment ajouter un nom de domaine ?

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour l’annuaire.

2.  Sélectionnez **plusieurs services**, entrez **Azure Active Directory** dans la zone de texte, puis **entrée**.

    ![Gestion des utilisateurs de l’ouverture](./media/active-directory-domains-add-azure-portal/user-management.png)

3. Dans la carte de ***nom du répertoire*** , sélectionnez **les noms de domaine**.

4. Dans la carte de ** *nom du répertoire* : noms de domaine** , sélectionnez la commande **Ajouter** .

  ![Cliquez sur la commande Ajouter](./media/active-directory-domains-add-azure-portal/add-command.png)

5. Dans la carte de **nom de domaine** , entrez le nom de votre domaine personnalisé dans la zone, tel que « contoso.com », puis sélectionnez **Ajouter un domaine**. Veillez à inclure le .com, .net ou toute autre extension de niveau supérieur.

6. Sur la carte ***nom_domaine*** (autrement dit, la carte qui s’ouvre avec le nouveau nom de domaine dans le titre), recherchez les informations d’entrée DNS qui Azure AD utiliserez pour vérifier que votre organisation possède le nom de domaine personnalisé.

  ![obtenir des informations d’entrée DNS](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

Maintenant que vous avez ajouté le nom de domaine, Azure AD doit vérifier que votre organisation possède le nom de domaine. Avant d’Azure AD peut effectuer cette vérification, vous devez ajouter une entrée DNS dans le fichier de zone DNS pour le nom de domaine. Cette tâche est effectuée sur le site Web de bureau d’enregistrement du nom de domaine pour le nom de domaine.

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Ajouter l’entrée DNS auprès du bureau d’enregistrement de nom de domaine pour le domaine

L’étape suivante pour utiliser votre nom de domaine personnalisé avec Azure AD consiste à mettre à jour le fichier de zone DNS pour le domaine. Cela permet d’Azure AD vérifier que votre organisation possède le nom de domaine personnalisé.

1.  Se connecter pour le bureau d’enregistrement de nom de domaine pour le domaine. Si vous n’avez pas accès à mettre à jour l’entrée DNS, demandez à la personne ou l’équipe qui ont cet accès pour effectuer l’étape 2 et pour vous avertir lorsqu’il est terminé.

2.  Mettre à jour le fichier de zone DNS du domaine en ajoutant l’entrée DNS fournie par Azure AD. Cette entrée DNS Active Azure AD vérifier la propriété du domaine. L’entrée DNS ne modifie pas les comportements tels que le routage de messagerie ou d’hébergement web.

Pour ajouter cette l’entrée DNS, lisez les [Instructions permettant d’ajouter une entrée DNS auprès de bureaux d’enregistrement DNS populaires](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Vérifiez le nom de domaine avec Azure Active Directory

Une fois que vous avez ajouté l’entrée DNS, vous êtes prêt à vérifier le nom de domaine avec Azure AD.

Un nom de domaine peut être vérifié qu’une fois que les enregistrements DNS ont été propagées. Cette propagation souvent ne prend que quelques secondes, mais cela peut parfois prendre une heure ou plus. Si la vérification ne fonctionne pas la première fois, réessayez plus tard.

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour l’annuaire.

2.  Sélectionnez **Parcourir**, entrez la gestion des utilisateurs dans la zone de texte et appuyez ensuite sur **entrée**.

    ![Gestion des utilisateurs de l’ouverture](./media/active-directory-domains-add-azure-portal/user-management.png)

3. Dans la carte de **Gestion des utilisateurs - noms de domaine** , sélectionnez le nom de domaine non vérifié que vous voulez vérifier.

4. Sur la carte ***nom_domaine*** (autrement dit, la carte qui s’ouvre avec le nouveau nom de domaine dans le titre), sélectionnez **Vérifier** pour terminer la vérification.

Désormais, vous pouvez [affecter des noms d’utilisateur qui incluent votre nom de domaine personnalisé](active-directory-users-create-azure-portal.md).

## <a name="troubleshooting"></a>Résolution des problèmes

Si vous ne peut pas vérifier un nom de domaine personnalisé, procédez comme suit. Nous allons commencer avec les plus courants et descendez jusqu'à la moins courante.

1.  **Attendre une heure**. Enregistrements DNS doivent se propager avant Azure AD peut vérifier le domaine. Cela peut prendre une heure ou plus.

2.  **Vérifiez que l’enregistrement DNS a été entrée, et qu’il est correct**. Effectuer cette étape sur le site Web pour le bureau d’enregistrement de nom de domaine pour le domaine. Azure AD ne peut pas vérifier le nom de domaine si l’entrée DNS ne figure pas dans le fichier de zone DNS, ou si elle n’est pas une correspondance exacte avec l’entrée DNS ce Azure AD fourni. Si vous n’avez pas accès à mettre à jour les enregistrements DNS pour le domaine auprès du bureau d’enregistrement du nom de domaine, partager l’entrée DNS avec la personne ou une équipe à votre organisation qui dispose de cet accès et demandez-lui d’ajouter l’entrée DNS.

3.  **Supprimer le nom de domaine à partir d’un autre répertoire dans Azure AD**. Un nom de domaine peut être vérifié dans uniquement un seul répertoire. Si un nom de domaine a été vérifié précédemment dans un autre répertoire, il doit être supprimé il avant qu’il peut être vérifié dans votre nouveau répertoire. Pour en savoir plus sur la suppression des noms de domaine, lisez [Gérer les noms de domaine personnalisé](active-directory-domains-manage-azure-portal.md).    

## <a name="add-more-custom-domain-names"></a>Ajouter d’autres noms de domaine personnalisé

Si votre organisation utilise plusieurs noms de domaine personnalisé, tel que « contoso.com » et « contosobank.com », vous pouvez les ajouter jusqu'à maximum 900 des noms de domaine. Utilisez les mêmes étapes dans cet article pour chacun de vos noms de domaine ajouter.

## <a name="next-steps"></a>Étapes suivantes

[Gérer des noms de domaine personnalisé](active-directory-domains-manage-azure-portal.md)
