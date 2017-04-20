<properties 
    pageTitle="Intégration de vos identités locales avec Azure Active Directory."
    description="Il s’agit d’Azure AD Connect décrivant ce qu’il est et la raison pour laquelle vous souhaitez l’utiliser."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>Authentification multifacteur construction dans des applications personnalisées (SDK)

> [AZURE.IMPORTANT]  Si vous souhaitez télécharger le Kit de développement, vous devrez créer un fournisseur d’authentification multifacteur Azure même si vous avez l’authentification Multifacteur Azure, AAD Premium ou licences EMS.  Si vous créez un fournisseur d’authentification multifacteur Azure à cet effet et que vous avez déjà des licences, il est nécessaire pour créer le fournisseur avec le modèle **Par des utilisateurs activés** et lier le fournisseur au répertoire qui contient les licences l’authentification Multifacteur Azure, Azure AD Premium ou em.  Cela garantit que vous ne sont pas facturés à moins d’avoir plusieurs utilisateurs uniques à l’aide du Kit de développement que le nombre de licences que vous êtes propriétaire.

Le Kit de développement logiciel (SDK) Azure multifacteur authentification vous permet de générer un appel téléphonique et le texte vérification message directement dans les processus de connexion ou transaction des applications de votre client Azure AD.

Le Kit de développement de l’authentification multifacteur est disponible pour c#, Visual Basic (.NET), Java, Perl, PHP et Ruby. Le Kit de développement fournit une fine autour de l’authentification multifacteur. Il inclut tout ce que vous voulez écrire du code, y compris les fichiers de code source commentées, exemples de fichiers et un fichier Lisez-moi détaillé. Chaque SDK inclut également un certificat et une clé privée pour chiffrer les transactions unique pour votre fournisseur d’authentification multifacteur. Dans la mesure où vous avez un fournisseur, vous pouvez télécharger le Kit de développement dans les formats et les langues autant que nécessaire.

La structure de l’API dans le Kit de développement de l’authentification multifacteur est très simple. Effectuer une fonction d’appel à une API avec les paramètres d’option de plusieurs facteurs, tels que le mode de vérification et données utilisateur, tels que le numéro de téléphone à appeler ou le code confidentiel pour valider. Les API traduisent l’appel de fonction en demandes de services web au Service d’authentification multifacteur Azure sur le nuage. Tous les appels doivent inclure une référence au certificat privé qui est incluse dans chaque kit de développement logiciel.

Étant donné que les API n’ont pas accès aux utilisateurs enregistrés dans Azure Active Directory, vous devez fournir les informations utilisateur, tels que les numéros de téléphone et les codes de code confidentiel, dans un fichier ou une base de données. En outre, les API ne fournissent pas de fonctionnalités de gestion d’inscription ou utilisateur, et vous devez donc intégrer ces processus dans votre application.






## <a name="download-the-azure-multi-factor-authentication-sdk"></a>Télécharger l’authentification multifacteur Azure SDK

Télécharger le Kit de développement multifacteur Azure nécessite un [Fournisseur d’authentification multifacteur Azure](multi-factor-authentication-get-started-auth-provider.md).  Cette fonctionnalité nécessite un abonnement Azure complète, même si l’authentification Multifacteur Azure, Azure AD Premium ou entreprise mobilité Suite licences appartiennent.  Pour télécharger le Kit de développement, vous devez accéder au portail de gestion de plusieurs facteurs en gérant le fournisseur d’authentification multifacteur directement ou en cliquant sur le lien **« Accédez au portail »** dans la page de paramètres de service de l’authentification Multifacteur.


### <a name="to-download-the-azure-multi-factor-authentication-sdk-from-the-azure-portal"></a>Pour télécharger le Kit de développement du authentification multifacteur Azure à partir du portail Azure


1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sur la gauche, sélectionnez Active Directory.
3. Dans la page Active Directory, dans la partie supérieure, cliquez sur **Fournisseurs d’authentification multifacteur**
4. Cliquez sur **Gérer** en bas
5. Une nouvelle page s’ouvre.  Sur la gauche, en bas, cliquez sur Kit de développement logiciel.
<center>![Télécharger](./media/multi-factor-authentication-sdk/download.png)</center>
6. Sélectionnez la langue souhaitée, puis cliquez sur les liens de téléchargement associé.
7. Enregistrer le fichier téléchargé.



### <a name="to-download-the-azure-multi-factor-authentication-sdk-via-the-service-settings"></a>Pour télécharger le SDK de l’authentification multifacteur Azure via les paramètres du service


1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sur la gauche, sélectionnez Active Directory.
3. Double-cliquez sur votre instance d’Azure AD.
4. Dans la partie supérieure, cliquez sur **configurer**
5. Sous l’authentification multifacteur, sélectionnez **Gérer les paramètres de service**
![télécharger](./media/multi-factor-authentication-sdk/download2.png)
6. Dans la page Paramètres de services, dans la partie inférieure de l’écran, cliquez sur **accéder au portail**.
![Télécharger](./media/multi-factor-authentication-sdk/download3a.png)
7. Une nouvelle page s’ouvre.  Sur la gauche, en bas, cliquez sur Kit de développement logiciel.
8. Sélectionnez la langue souhaitée, puis cliquez sur les liens de téléchargement associé.
9. Enregistrer le fichier téléchargé.

## <a name="contents-of-the-azure-multi-factor-authentication-sdk"></a>Contenu de l’authentification multifacteur Azure SDK
À l’intérieur du Kit de développement, vous trouverez les éléments suivants :

- **Lisezmoi**. Explique comment utiliser les API de l’authentification multifacteur dans une application existante ou nouvelle.
- **Fichiers source** pour l’authentification multifacteur
- **Certificat client** que vous utilisez pour communiquer avec le service d’authentification multifacteur
- **Clé privée** pour le certificat
- **Appel.** Liste des codes de résultat d’appel. Pour ouvrir ce fichier, utilisez une application avec du texte mise en forme, comme le bloc-notes. Utilisez les codes de résultat appel pour tester et dépanner l’implémentation de l’authentification multifacteur dans votre application. Ils ne sont pas les codes d’état d’authentification.
- **Exemples.** Exemples de code pour une base implémentation active de l’authentification multifacteur.


>[AZURE.WARNING]Le certificat client est un certificat privé unique qui a été généré spécialement pour vous. Ne pas partager ou perdre ce fichier. Il s’agit de votre clé à assurer la sécurité de vos communications avec le service d’authentification multifacteur.

## <a name="code-sample-standard-mode-phone-verification"></a>Exemple de code : Mode Standard vérification par téléphone

Cet exemple montre comment utiliser les API dans le SDK de l’authentification multifacteur Azure pour ajouter la vérification d’appel en mode standard voix à votre application. En mode standard est un appel téléphonique répondant à l’utilisateur en appuyant sur la touche #.

Cet exemple utilise le c# .NET 2.0 multifacteur authentification SDK dans une application ASP.NET simple avec logique côté serveur c#, mais le processus est très similaire pour mises en œuvre simples dans d’autres langues. Parce que le Kit de développement inclut les fichiers source, fichiers non exécutables, vous pouvez générer les fichiers et y faire référence ou insérez-les directement dans votre application.

>[AZURE.NOTE]Lorsque vous implémentez l’authentification multifacteur, utilisez les facteurs supplémentaires en tant que vérification secondaire ou troisième pour compléter votre méthode d’authentification principal. Ces méthodes ne sont pas conçus pour être utilisé comme méthodes d’authentification principales.

### <a name="code-sample-overview"></a>Vue d’ensemble des exemples de code
Cet exemple de code pour une application de démonstration web très simple utilise un appel téléphonique avec une réponse clé # pour terminer l’authentification de l’utilisateur. Ce facteur appel téléphonique est appelé dans l’authentification multifacteur en mode standard.

Le code côté client n’inclut pas tous les éléments spécifiques à l’authentification multifacteur. Étant donné que les facteurs d’authentification supplémentaires sont indépendantes de l’authentification principale, vous pouvez les ajouter sans changer l’interface authentification existante. L’API dans le Kit de développement multifacteur vous permettent de personnaliser l’expérience utilisateur, mais vous devrez peut-être pas à modifier son du tout.

Le code côté serveur ajoute l’authentification en mode standard à l’étape 2. Il crée un objet PfAuthParams avec les paramètres requis pour la vérification en mode standard : nom d’utilisateur, nombre et mode ainsi que le chemin d’accès au certificat client (CertFilePath), qui est requis dans chaque appel de téléphone. Pour une démonstration de tous les paramètres dans PfAuthParams, consultez l’exemple de fichier dans le Kit de développement.

Le code passe ensuite l’objet PfAuthParams à la fonction pf_authenticate(). La valeur de retour indique la réussite ou l’échec de l’authentification. Les paramètres de sortie, callStatus et ID d’erreur, contiennent des informations de résultat appel supplémentaire. Les codes de résultat appel sont présentées dans le fichier de résultats appel dans le Kit de développement.

Cette implémentation minimale peut être écrites dans une quelques lignes. Toutefois, dans le code de production, vous pouvez inclure gestion d’erreurs plus sophistiquées, le code de base de données supplémentaires et une expérience utilisateur améliorée.

### <a name="web-client-code"></a>Code de Client Web

Le code suivant est web client pour une page de démonstration.


    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="_Default" %>

    <!DOCTYPE html>

    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
    <title>Multi-Factor Authentication Demo</title>
    </head>
    <body>
    <h1>Azure Multi-Factor Authentication Demo</h1>
    <form id="form1" runat="server">

    <div style="width:auto; float:left">
    Username:&nbsp;<br />
    Password:&nbsp;<br />
    </div>

    <div">
    <asp:TextBox id="username" runat="server" width="100px"/><br />
    <asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
    </div>

    <asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>

    <p><asp:Label ID="lblResult" runat="server"></asp:Label></p>

    </form>
    </body>
    </html>


### <a name="server-side-code"></a>Code côté serveur

Dans le code côté serveur suivant, l’authentification multifacteur est configurée et exécuter à l’étape 2. Mode standard (MODE_STANDARD) est un appel téléphonique auxquelles l’utilisateur doit répondre en appuyant sur la touche #.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;

    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
        }

        protected void btnSubmit_Click(object sender, EventArgs e)
        {
            // Step 1: Validate the username and password
            if (username.Text != "Contoso" || password.Text != "password")
            {
                lblResult.ForeColor = System.Drawing.Color.Red;
                lblResult.Text = "Username or password incorrect.";
            }
            else
            {
                // Step 2: Perform multi-factor authentication

                // Add call details from the user database.
                PfAuthParams pfAuthParams = new PfAuthParams();
                pfAuthParams.Username = username.Text;
                pfAuthParams.Phone = "9134884271";
                pfAuthParams.Mode = pf_auth.MODE_STANDARD;

                // Specify a client certificate
                // NOTE: This file contains the private key for the client
                // certificate. It must be stored with appropriate file
                // permissions.
                pfAuthParams.CertFilePath = "c:\\cert_key.p12";

                // Perform phone-based authentication
                int callStatus;
                int errorId;

                if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
                {
                    lblResult.ForeColor = System.Drawing.Color.Green;
                    lblResult.Text = "Multi-Factor Authentication succeeded.";
                }
                else
                {
                    lblResult.ForeColor = System.Drawing.Color.Red;
                    lblResult.Text = " Multi-Factor Authentication failed.";
                }
            }

        }
    }
