<properties
    pageTitle="Sécuriser les ressources de cloud avec l’authentification Multifacteur Azure et AD FS"
    description="Il s’agit de la page de l’authentification multifacteur Azure qui explique comment commencer à utiliser l’authentification Multifacteur Azure et AD FS dans le cloud."
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
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>

# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>Protection des ressources de cloud avec l’authentification multifacteur Azure et AD FS

Si votre organisation fédérée avec Azure Active Directory, utilisez l’authentification multifacteur Azure ou Active Directory Federation Services pour sécuriser les ressources qui sont accessibles par Azure Active Directory. Utilisez les procédures suivantes pour sécuriser les ressources Azure Active Directory avec l’authentification multifacteur Azure ou Active Directory Federation Services.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Sécuriser les ressources Azure AD via AD FS

Pour sécuriser la ressource de cloud, tout d’abord activer un compte pour les utilisateurs, puis configurer une règle de revendications. Suivez cette procédure pour les différentes étapes :

1. Suivez les étapes décrites dans [l’authentification multifacteur fermeture pour les utilisateurs](active-directory/multi-factor-authentication-get-started-cloud.md#turn-on-multi-factor-authentication-for-users) pour permettre à un compte.
2. Démarrez la console de gestion des AD FS.
![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)
3. Naviguez jusqu’au **Lieu d’utiliser des approbations des parties** et avec le bouton droit sur le lieu d’utiliser des tiers gestion de la confidentialité. Sélectionnez **Modifier les règles réclamer...**
4. Cliquez sur **Ajouter une règle...**
5. Dans la liste déroulante, sélectionnez **Envoyer des demandes à l’aide d’une règle personnalisée** , puis cliquez sur **suivant**.
6. Entrez un nom pour la règle.
7. Sous règles de personnalisés : ajouter le texte suivant :

    ```
    => issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");
    ```

    Réclamer correspondante :

    ```
    <saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
    <saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
    </saml:Attribute>
    ```

8. Cliquez sur **OK** puis sur **Terminer**. Fermez la console de gestion des AD FS.

Les utilisateurs peuvent ensuite effectuer se connecter à l’aide de la méthode locaux (par exemple, des cartes à puce).

## <a name="trusted-ips-for-federated-users"></a>Adresses IP autorisées pour les utilisateurs fédérés
Adresses IP autorisées permettent aux administrateurs de la vérification de dérivation pour les adresses IP spécifiques, ou pour les utilisateurs fédérés ayant des demandes en provenance de leur propre intranet. Les sections suivantes décrivent comment configurer des adresses Azure multifacteur authentification approuvés IP avec les utilisateurs fédérés et la vérification de dérivation lorsqu’une demande de provenance de dans un intranet les utilisateurs fédérés. Pour cela en configurant AD FS pour utiliser un directe ou filtrer un modèle de déclaration entrants avec le type de déclaration à l’intérieur d’un réseau d’entreprise.

Cet exemple utilise Office 365 pour notre approbations des parties de confiance.

### <a name="configure-the-ad-fs-claims-rules"></a>Configurer les règles de revendications AD FS

La première chose à faire consiste à configurer les revendications AD FS. Nous allons créer des règles de deux revendications, une pour le type de déclaration à l’intérieur d’un réseau d’entreprise et une autre permettant de maintenir nos utilisateurs connectés.

1. Ouvrez l’outil Gestion AD FS.
2. Sur la gauche, sélectionnez **Approbations des parties de confiance**.
3. Avec le bouton droit sur **Microsoft Office 365 identité plate-forme** et sélectionnez **Modifier les règles de revendications** 
 ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. Sous règles de transformation d’émission, cliquez sur **Ajouter une règle.** 
 ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. Dans l’Assistant Ajout de transformer réclamer règle Sélectionnez **passage ou filtrer une réclamer entrant** dans la liste déroulante, puis cliquez sur **suivant**.
![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. Dans la zone en regard du nom de la règle réclamer, donnez un nom à votre règle. Par exemple : InsideCorpNet.
7. Dans la liste déroulante, en regard d’entrant réclamer type, sélectionnez **à l’intérieur d’un réseau d’entreprise**.
![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. Cliquez sur **Terminer**.
9. Sous règles de transformation d’émission, cliquez sur **Ajouter une règle**.
10. Dans l’Assistant Ajout de transformer réclamer règle Sélectionnez **revendications envoyer à l’aide d’une règle personnalisée** dans le menu déroulant, puis cliquez sur **suivant**.
11. Dans la zone sous nom de la règle réclamer : renseignez *conserver les utilisateurs connecté*.
12. Dans la zone de règle personnalisée, entrez :

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. Cliquez sur **Terminer**.
14. Cliquez sur **Appliquer**.
15. Cliquez sur **Ok**.
16. Fermez la gestion des AD FS.



### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>Configurer multifacteur Azure authentification approuvés adresses IP avec les utilisateurs fédérés
À présent que les revendications sont en place, nous pouvons configurer des adresses IP autorisées.

1. Connectez-vous au [portail classique Azure](https://manage.windowsazure.com).
2. Sur la gauche, cliquez sur **Active Directory**.
3. Sous répertoire, sélectionnez le répertoire où vous souhaitez définir des adresses IP autorisées.
4. Dans le répertoire que vous avez sélectionné, cliquez sur **configurer**.
5. Dans la section authentification multifacteur, cliquez sur **Gérer les paramètres de service**.
6. Dans la page Paramètres du Service, sous adresses IP autorisées, sélectionnez **Ignorer multiples l’authentification pour les demandes des utilisateurs fédérés sur mon intranet.** 
 ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. Cliquez sur **Enregistrer**.
8. Une fois que les mises à jour ont été appliquées, cliquez sur **Fermer**.


Voilà ! À ce stade, les utilisateurs fédérés Office 365 doivent suffit d’utiliser l’authentification Multifacteur lorsqu’une demande de remboursement provient d’en dehors de l’intranet d’entreprise.
