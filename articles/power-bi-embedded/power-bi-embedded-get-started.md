<properties
   pageTitle="Prise en main avec Microsoft Power BI incorporé"
   description="Power BI incorporé, ajouter des rapports interactifs Power BI dans votre application décisionnelle"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="get-started-with-microsoft-power-bi-embedded"></a>Prise en main avec Microsoft Power BI incorporé

**Power BI incorporé** est un service Azure qui permet aux développeurs d’application Ajouter des rapports interactifs Power BI dans leurs propres applications. **Power BI incorporé** fonctionne avec les applications existantes sans avoir besoin de redéfinition ou modification des manière dont les utilisateurs de se connecter.

Ressources pour **Microsoft Power BI incorporé** sont mis en service via les [API de processeur Azure](https://msdn.microsoft.com/library/mt712306.aspx). Dans ce cas, la ressource que vous devez configurer est une **Collection de sites Power BI espace de travail**.

![](media\power-bi-embedded-get-started\introduction.png)

## <a name="create-a-workspace-collection"></a>Créer une collection de sites espace de travail
Une **Collection de sites espace de travail** est la ressource Azure niveau supérieur et un conteneur pour le contenu qui sera incorporé dans votre application. Une **Collection de sites espace de travail** peuvent être créés de deux façons :

   -    Manuellement à l’aide du portail Azure
   -    Programmation à l’aide de l’API Azure ressource Manager(ARM)

Nous allons étudier les étapes permettant de créer une **Collection de sites espace de travail** à l’aide du portail Azure.

   1.   Ouvrez et connectez-vous au **Portail Azure**: [http://portal.azure.com](http://portal.azure.com).

   2.   Cliquez sur **+ Nouveau** dans le volet supérieur.

       ![](media\power-bi-embedded-get-started\create-workspace-1.png)

   3.   Sous **données + Analytique** cliquez sur **Power BI incorporé**.
   4.   Sur la **Création d’une carte**, entrez les informations requises. Pour **tarification**, consultez [tarification Power BI incorporé](http://go.microsoft.com/fwlink/?LinkID=760527).

       ![](media\power-bi-embedded-get-started\create-workspace-2.png)

   5. Cliquez sur **créer**.

La **Collection de sites espace de travail** prendra quelques instants mise en service. Une fois terminé, vous serez redirigé vers la **Carte de collection de sites espace de travail**.

   ![](media\power-bi-embedded-get-started\create-workspace-3.png)

La **Création d’une carte** contient les informations nécessaires appeler les API qui créent des espaces de travail et déploiement le contenu vers les.

<a name="view-access-keys"/>
## <a name="view-power-bi-api-access-keys"></a>Touches d’accès à l’API vue Power BI

Un des éléments les plus importantes d’informations nécessaires pour appeler des API REST Power BI sont les **Touches d’accès rapide**. Celles-ci sont utilisées pour générer les **jetons d’application** qui permettent d’authentifier vos demandes API. Pour afficher les **Touches d’accès rapide**, cliquez sur les **Touches d’accès rapide** sur la **Carte de paramètres**. Pour plus d’informations sur les **jetons de l’application**, voir [authentification et l’autorisation avec Power BI incorporé](power-bi-embedded-app-token-flow.md).

   ![](media\power-bi-embedded-get-started\access-keys.png)

Vous devez « avis que vous avez deux clés.

   ![](media\power-bi-embedded-get-started\access-keys-2.png)

Copiez ces touches et les stocker en toute sécurité dans votre application. Il est très important de considérer ces touches comme vous le feriez pour un mot de passe, car ils devez fournir un accès à tout le contenu de votre **Collection de sites espace de travail**.

Tandis que les deux clés sont répertoriés, qu’une seule clé est nécessaire à un moment donné. La deuxième clé est fournie afin que vous pouvez régulièrement régénérer clés sans interrompre l’accès au service.

À présent que vous avez une instance de Power BI pour votre application et les **Touches d’accès rapide**, vous pouvez importer un rapport dans votre propre application. Avant de vous découvrirez comment importer un rapport, la section suivante décrit création jeux de données Power BI et les rapports d’incorporer dans une application.

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app"></a>Créer des groupes de données Power BI et des rapports à incorporer dans une application

À présent que vous avez créé une instance de Power BI pour votre application et que **Les touches d’accès**, vous devrez créer les jeux de données Power BI et les rapports que vous souhaitez incorporer. Jeux de données et les rapports peuvent être créés à l’aide de **Power BI Desktop**. Vous pouvez télécharger [Power BI Desktop pour libérer](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/). Ou, pour commencer rapidement, vous pouvez télécharger le [vente au détail analyse exemple PBIX] (http://go.microsoft.com/fwlink/?LinkID=780547). Pour plus d’informations sur l’utilisation de **Power BI Desktop**, voir [Prise en main Power BI Desktop](https://powerbi.microsoft.com/en-us/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

Avec **Power BI Desktop**, vous connectez à votre source de données en important une copie des données dans **Power BI Desktop** ou se connecter directement à la source de données à l’aide de **DirectQuery**.

Voici les différences entre l’utilisation **d’importation** et **DirectQuery**.

|Importation | DirectQuery
|---|---
|Tables, colonnes *et les données* sont importées ou copiées dans **Power BI Desktop**. Lorsque vous travaillez avec des visualisations, **Power BI Desktop** demande une copie des données. Pour afficher les modifications apportées aux données sous-jacentes, vous devez actualiser ou importer un dataset complète et actif à nouveau.|Uniquement les *tables et les colonnes* sont importées ou copiées dans **Power BI Desktop**. Lorsque vous travaillez avec des visualisations, **Power BI Desktop** demande la source de données sous-jacente, ce qui signifie que vous visualisez toujours les données actuelles.

Pour plus d’informations sur la connexion à une source de données, voir [se connecter à une source de données](power-bi-embedded-connect-datasource.md).

Après avoir enregistré votre travail dans **Power BI Desktop**, un fichier PBIX est créé. Ce fichier contient votre rapport. En outre, si vous importez des données la PBIX contient le jeu de données complète, ou si vous utilisez **DirectQuery**, la PBIX contient uniquement le schéma d’un dataset. Vous déployez par programme le PBIX dans votre espace de travail à l’aide de l' [API d’importation Power BI](https://msdn.microsoft.com/library/mt711504.aspx).

> [AZURE.NOTE] **Power BI incorporé** a API supplémentaires pour modifier le serveur et la base de données qui pointe vers votre jeu de données et configurer une information d’identification du compte de service qui utilisera le jeu de données pour vous connecter à votre base de données. Voir [SetAllConnections billet](https://msdn.microsoft.com/library/mt711505.aspx) et [correctifs passerelle Datasource](https://msdn.microsoft.com/library/mt711498.aspx).

## <a name="next-steps"></a>Étapes suivantes
Dans la procédure précédente, vous avez créé une collection de sites espace de travail, votre premier rapport et dataset. Maintenant, il est temps pour apprendre à écrire du code pour **Power BI incorporé**. Pour vous aider à commencer, nous avons créé un exemple d’application web : [prise en main l’échantillon](power-bi-embedded-get-started-sample.md). L’exemple vous explique comment procéder pour :

  - Contenu de la mise en service
      - Créer un espace de travail
      - Importer un fichier PBIX
      - Mettre à jour les chaînes de connexion et définir les informations d’identification pour vos jeux de données.

  - Incorporer en toute sécurité un rapport

## <a name="see-also"></a>Voir aussi
- [Prise en main exemple](power-bi-embedded-get-started-sample.md)
- [Authentification et autorisation avec Power BI incorporé](power-bi-embedded-app-token-flow.md)
- [Ordinateur de bureau Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
