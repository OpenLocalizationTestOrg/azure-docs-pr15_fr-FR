<properties
    pageTitle="Ajouter votre nom de domaine personnalisé et configurer l’authentification fédéré à Azure Active Directory | Microsoft Azure"
    description="Comment ajouter des noms de domaine de votre entreprise à Azure Active Directory et comment configurer fédérés authentification entre Azure Active Directory et votre solution de fédération locale."
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
    ms.topic="get-started-article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="add-your-custom-domain-name-to-azure-active-directory"></a>Ajoutez votre nom de domaine personnalisé à Azure Active Directory

Vous pouvez configurer un nom de domaine personnalisé, tel que « contoso.com », afin que les utilisateurs dans contoso.com peuvent avoir une fédéré unique authentification expérience à partir de votre réseau d’entreprise. Si vous avez déjà des Services de fédération Active Directory (AD FS) ou un serveur de fédération différents s’exécutant sur votre réseau d’entreprise, vous pouvez configurer Azure AD pour utiliser votre nom de domaine personnalisé à l’aide de l’outil Azure AD Connect. Vous pouvez également utiliser Azure AD Connect pour déployer un nouvel environnement AD FS et que configurer pour fédérées de l’authentification unique à Azure Active Directory.

Si vous n’avez pas et que vous ne souhaitez pas déployer ADFS ou un autre serveur de fédération, suivez ces instructions : [Ajouter un nom de domaine personnalisé à Azure Active Directory](active-directory-add-domain.md).

## <a name="add-a-custom-domain-name-to-your-directory"></a>Ajouter un nom de domaine personnalisé dans votre annuaire

1. Connectez-vous au [portail classique Azure](https://manage.windowsazure.com/) avec un compte d’utilisateur qui est un administrateur global de votre répertoire Azure AD.

2. Dans **Active Directory**, ouvrez votre répertoire et sélectionnez l’onglet **Domains** .

3. Dans la barre de commande, sélectionnez **Ajouter**, puis entrez le nom de votre domaine personnalisé, tel que « contoso.com ». Veillez à inclure le .com, .net ou toute autre extension de niveau supérieur.

4. Activez la case à cocher **j’ai envisagez de configurer ce domaine pour l’authentification unique avec mon Active Directory local** .

5. Sélectionnez **Ajouter**.

Exécutez l’outil Azure AD Connect pour obtenir l’entrée de DNS Azure AD utiliseront pour vérifier le domaine. Vous verrez l’entrée DNS à l’étape **Azure AD domaine** dans l’Assistant. Vous pouvez voir à quoi cette étape dans l’Assistant ressemble [dans ces instructions](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Si vous n’avez pas l’outil Azure AD Connect, vous pouvez [télécharger ici](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Ajouter l’entrée DNS auprès du bureau d’enregistrement de nom de domaine pour le domaine

L’étape suivante pour utiliser votre nom de domaine personnalisé avec Azure AD consiste à mettre à jour le fichier de zone DNS pour le domaine. Cela permet d’Azure AD vérifier que votre organisation possède le nom de domaine personnalisé.

1. Connectez-vous au site Web de bureau d’enregistrement du nom de domaine pour votre nom de domaine. Si vous n’avez pas accès pour ce faire, demandez à la personne ou l’équipe de votre organisation qui dispose de cet accès pour effectuer l’étape 2 et pour vous avertir lorsqu’il est terminé.

2. Mettre à jour le fichier de zone DNS du domaine en ajoutant l’entrée DNS fournie par Azure AD. Cette entrée DNS Active Azure AD vérifier la propriété du domaine. L’entrée DNS ne modifie pas les comportements tels que le routage de messagerie ou d’hébergement web.

Pour obtenir de l’aide cette étape, lisez les [Instructions permettant d’ajouter une entrée DNS auprès de bureaux d’enregistrement DNS populaires](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Vérifiez le nom de domaine avec Azure Active Directory

Une fois que vous avez ajouté l’entrée DNS, vous êtes prêt à vérifier le nom de domaine avec Azure AD.

Pour vérifier le domaine, sélectionnez le **Domaine AD Azure** à l’étape de l’Assistant Azure AD Connect **suivante** . Azure AD se présentera pour l’entrée DNS dans le fichier de zone DNS pour le domaine. Azure AD vérifier uniquement le nom de domaine une fois que les enregistrements DNS ont été propagées. Propagation souvent ne prend que quelques secondes, mais cela peut parfois prendre une heure ou plus. Si la vérification ne fonctionne pas la première fois, réessayez plus tard.

Ensuite, passez aux étapes restantes dans l’Assistant Azure AD Connect. Les utilisateurs de votre serveur Windows AD Azure AD sont alors synchronisées. Utilisateurs synchronisés dans le domaine que vous avez configuré pour la fédération pourront accéder fédérés une seule expérience authentification Azure AD depuis votre réseau d’entreprise.

## <a name="troubleshooting"></a>Résolution des problèmes

Si vous ne peut pas vérifier un nom de domaine personnalisé, procédez comme suit. Nous allons commencer avec les plus courants et descendez jusqu'à la moins courante.

1.  **Attendre une heure**. Enregistrements DNS doivent se propager avant Azure AD peut vérifier le domaine. Cela peut prendre une heure ou plus.

2.  **Vérifiez que l’enregistrement DNS a été entrée, et qu’il est correct**. Effectuer cette étape sur le site Web pour le bureau d’enregistrement de nom de domaine pour le domaine. Azure AD ne peut pas vérifier le nom de domaine si l’entrée DNS ne figure pas dans le fichier de zone DNS, ou si elle n’est pas une correspondance exacte avec l’entrée DNS ce Azure AD fourni. Si vous n’avez pas accès à mettre à jour les enregistrements DNS pour le domaine auprès du bureau d’enregistrement du nom de domaine, partager l’entrée DNS avec la personne ou une équipe à votre organisation qui dispose de cet accès et demandez-lui d’ajouter l’entrée DNS.

3.  **Supprimer le nom de domaine à partir d’un autre répertoire dans Azure AD**. Un nom de domaine peut être vérifié dans uniquement un seul répertoire. Si un nom de domaine a été vérifié précédemment dans un autre répertoire, il doit être supprimé il avant qu’il peut être vérifié dans votre nouveau répertoire. Pour en savoir plus sur la suppression des noms de domaine, lisez [Gérer les noms de domaine personnalisé](active-directory-add-manage-domain-names.md).

## <a name="add-more-custom-domain-names"></a>Ajouter d’autres noms de domaine personnalisé

Si votre organisation utilise plusieurs noms de domaine personnalisé, tel que « contoso.com » et « contosobank.com », vous pouvez les ajouter jusqu'à maximum 900 des noms de domaine. Utilisez les mêmes étapes dans cet article pour chacun de vos noms de domaine ajouter.

## <a name="next-steps"></a>Étapes suivantes

-   [Gérer des noms de domaine personnalisé](active-directory-add-manage-domain-names.md)
-   [En savoir plus sur les concepts de gestion de domaine dans Azure Active Directory](active-directory-add-domain-concepts.md)
-   [Afficher que votre société de personnalisation lorsque vos utilisateurs se connecter](active-directory-add-company-branding.md)
-   [Utiliser PowerShell pour gérer des noms de domaine dans Azure Active Directory](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
