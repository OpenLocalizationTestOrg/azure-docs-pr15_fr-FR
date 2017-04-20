<properties
   pageTitle="Obtenir des données à partir d’Azure Analysis Services | Microsoft Azure"
   description="Découvrez comment vous connecter à et obtenir des données à partir d’un serveur Analysis Services dans Azure."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="get-data-from-azure-analysis-services"></a>Obtenir des données à partir d’Azure Analysis Services
Une fois que vous avez créé un serveur dans Azure et déployée un modèle tabulaire sur celui-ci, les utilisateurs de votre organisation êtes prêts à vous connecter et commencer à Explorer ces données.

Azure Analysis Services prend en charge les connexions de client à l’aide de [mise à jour des modèles d’objets](#client-libraries); TOM, AMO, Adomd.Net ou MSOLAP, pour vous connecter via xmla sur le serveur. Par exemple, Power BI, Power BI Desktop, Excel ou toute application cliente tiers qui prend en charge les modèles d’objet.

## <a name="server-name"></a>Nom du serveur
Lorsque vous créez un serveur Analysis Services dans Azure, vous spécifiez un nom unique et la zone où le serveur doit être créé. Lorsque vous spécifiez le nom du serveur dans une connexion, le jeu d’attribution de nom de serveur est la suivante :
```
<protocol>://<region>/<servername>
```
 Protocole étant chaîne **asazure**, la région est l’Uri de la zone dans laquelle le serveur a été créé (par exemple, pour les États-Unis Ouest, westus.asazure.windows.net) et le nom de votre serveur unique dans la zone Nom du serveur.

## <a name="get-the-server-name"></a>Obtenir le nom du serveur
Avant de vous connecter, vous devez obtenir le nom du serveur. Dans le **portail Azure** > serveur > **vue d’ensemble** > **nom du serveur**, copiez le nom du serveur dans son intégralité. Si d’autres utilisateurs de votre organisation se connectent à ce serveur trop, vous souhaiterez partager ce nom de serveur avec eux. Lorsque vous spécifiez un nom de serveur, le chemin d’accès complet doit être utilisé.

![Obtenir le nom du serveur dans Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)


## <a name="connect-in-power-bi-desktop"></a>Se connecter dans Power BI Desktop

> [AZURE.NOTE] Cette fonctionnalité est aperçu.

1. Dans [Power BI Desktop](https://powerbi.microsoft.com/desktop/), cliquez sur **Obtenir des données** > **bases de données** > **Azure Analysis Services**.

2. **Serveur**, collez le nom du serveur à partir du Presse-papiers.

3. Dans la **base de données**, si vous connaissez le nom de la base de données de modèle tabulaire ou la perspective que vous voulez vous connecter, collez-la ici. Dans le cas contraire, vous pouvez laisser ce champ vide. Vous pouvez sélectionner une base de données ou une perspective dans l’écran suivant.

4. Laissez l’option **connexion live** par défaut sélectionnée, puis appuyez sur **se connecter**. Si vous êtes invité à entrer un compte, entrez votre compte professionnel.

5. Dans **le navigateur**, développez le serveur, puis sélectionnez le modèle ou un point de vue que vous voulez vous connecter, puis cliquez sur **se connecter**. Un simple clic sur un modèle ou une perspective montre tous les objets de cette vue.


## <a name="connect-in-power-bi"></a>Se connecter dans Power BI
1. Créer un fichier de Power BI Desktop qui a une connexion permanente à votre modèle sur votre serveur.

2. Dans [Power BI](https://powerbi.microsoft.com), cliquez sur **Obtenir des données** > **fichiers**. Recherchez et sélectionnez votre fichier.


## <a name="connect-in-excel"></a>Se connecter dans Excel
Connexion au serveur Azure Analysis Services dans Excel est prise en charge à l’aide d’obtenir des données dans Excel 2016 ou Power Query dans les versions antérieures. [Fournisseur MSOLAP.7](https://aka.ms/msolap) est requis. Connexion à l’aide de l’Assistant Importation de Table dans PowerPivot n’est pas pris en charge.

1. Dans Excel 2016, dans le ruban **données** , cliquez sur **Obtenir des données externes** > **à partir d’autres Sources** > **Provenance : Analysis Services**.

2. Dans l’Assistant connexion de données, dans la zone **nom du serveur**, collez le nom du serveur à partir du Presse-papiers. Puis, dans **les informations d’identification d’ouverture de session**, sélectionnez **utiliser les nom d’utilisateur et mot de passe suivants**et puis tapez le nom d’utilisateur professionnel, par exemple nancy@adventureworks.com, et mot de passe.

    ![Se connecter dans d’ouverture de session Excel](./media/analysis-services-connect/aas-connect-excel-logon.png)

4. Dans la **Table et sélectionnez la base de données**, sélectionnez la base de données et un modèle ou un point de vue, puis cliquez sur **Terminer**.

    ![Se connecter dans Sélectionnez un modèle Excel](./media/analysis-services-connect/aas-connect-excel-select.png)

## <a name="connection-string"></a>Chaîne de connexion
Lorsque vous vous connectez à l’aide du modèle objet tabulaire Azure Analysis Services, utilisez les formats de chaîne de connexion suivants :

###### <a name="integrated-azure-active-directory-authentication"></a>Authentification Azure Active Directory intégrée
```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```
L’authentification intégrée chercher le cache d’informations d’identification Azure Active Directory s’il est disponible. Si ce n’est pas le cas, la fenêtre de connexion Azure s’affiche.

###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Authentification Active Directory Azure avec nom d’utilisateur et mot de passe
```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

## <a name="client-libraries"></a>Bibliothèques de client
Lorsque vous vous connectez à Azure Analysis Services à partir d’Excel ou d’autres interfaces telles que TOM, AsCmd et ADOMD.NET, vous devrez peut-être installer les dernières bibliothèques du client fournisseur. Obtenir la dernière version :  

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>



## <a name="next-steps"></a>Étapes suivantes
[Gérer votre serveur](analysis-services-manage.md)
