<properties
   pageTitle="Prise en main SQL de base de données dynamique données Masking (Azure Portal)"
   description="Comment prendre en main SQL de base de données dynamique données Masking dans le portail Azure"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jhubbard"
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/10/2016"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>


# <a name="get-started-with-sql-database-dynamic-data-masking-azure-portal"></a>Prise en main SQL de base de données dynamique données Masking (Azure Portal)

> [AZURE.SELECTOR]
- [Masquage de l’échange dynamique de données - Azure Portal classique](sql-database-dynamic-data-masking-get-started-portal.md)

## <a name="overview"></a>Vue d’ensemble

Masquage des données dynamiques SQL de base de données limite exposition des données sensibles en masquant à des utilisateurs sans privilèges. Masquage de l’échange dynamique de données est prise en charge pour la version V12 de base de données SQL Azure.

Masquage échange dynamique de données permet d’empêcher tout accès non autorisé aux données sensibles en permettant aux clients désigner la quantité des données sensibles pour faire apparaître avec un impact minimal sur la couche d’application. Il est une fonctionnalité de sécurité basée sur des règles qui masque les données sensibles dans le jeu de résultats d’une requête sur les champs de la base de données désignée, tandis que les données dans la base de données ne sont pas modifiées.

Par exemple, un représentant du service à un centre d’appels peut identifier les appelants par plusieurs chiffres de son numéro de carte de crédit ou de numéro de sécurité sociale, mais ces éléments de données ne doivent pas être entièrement exposées au représentant du service. Une règle de masquage peut être définie que tous les masques, mais les quatre derniers chiffres de n’importe quel numéro de sécurité sociale ou numéro de carte de crédit dans le résultat de la valeur de toutes les requêtes. Un autre exemple, un masque de données appropriées peut être défini pour protéger vos données des informations d’identification personnelle (informations d’identification personnelle), afin qu’un développeur peut interroger des environnements de production pour résoudre ce problème sans violer les réglementations.

## <a name="sql-database-dynamic-data-masking-basics"></a>Concepts de base masque données dynamique SQL de base de données

Vous configurez un échange dynamique de données masking stratégie dans le portail Azure en sélectionnant l’opération de masquage des données dynamiques dans votre carte de configuration de base de données SQL ou une carte de paramètres.


### <a name="dynamic-data-masking-permissions"></a>Autorisations de masquage échange dynamique de données

Masquage de l’échange dynamique de données peut être configuré par l’administrateur de base de données Azure, administration du serveur ou les rôles de sécurité en uniforme.

### <a name="dynamic-data-masking-policy"></a>Stratégie de masquage échange dynamique de données

* **Les utilisateurs SQL exclus de masquage** - un ensemble d’utilisateurs SQL ou identités AAD obtiennent des données masquées dans les résultats de la requête SQL. Notez que les utilisateurs avec des privilèges d’administrateur seront toujours exclus de masquage et verront les données d’origine sans les masque.

* **Masquage des règles** - un ensemble de règles qui définissent les champs désignés à masquer et la fonction de masquage qui sera utilisée. Les champs désignés peuvent être définis à l’aide d’un nom de schéma de base de données, le nom de la table et le nom de colonne.

* **Masquage des fonctions** - un ensemble de méthodes qui contrôlent l’exposition des données pour différents scénarios.

| Fonction de masquage | Masquage logique |
|----------|---------------|
| **Par défaut**  |**Masquage complète selon les types de données des champs désignés**<br/><br/>• Utilisez XXXX ou moins x si la taille du champ est inférieur à 4 caractères pour les types de données chaîne (nchar, ntext, nvarchar).<br/>• Utilisez la valeur zéro pour les types de données numériques (bigint, bit, decimal, int, argent, numérique, smallint, smallmoney, tinyint, le flottement, réel).<br/>• Utilisez 01-01-1900 pour les types de données date/heure (date, datetime2, datetime, datetimeoffset, smalldatetime, emploi du temps).<br/>• De variante SQL, la valeur par défaut du type actuel est utilisé.<br/>• Pour le document XML <masked/> est utilisé.<br/>• Utilisez une valeur vide pour les types de données spéciaux (table horodatage, hierarchyid, GUID, binaire, image, varbinary spatiale types).
| **Carte de crédit** |**Méthode qui expose les quatre derniers chiffres des champs désignés de masquage** et ajoute une chaîne constante comme un préfixe sous la forme d’une carte de crédit.<br/><br/>XXXX-XXXX-XXXX-1234|
| **Numéro de sécurité sociale** |**Méthode qui expose les quatre derniers chiffres des champs désignés de masquage** et ajoute une chaîne constante comme un préfixe sous la forme d’un numéro de sécurité sociale américain.<br/><br/>XXX-XX-1234 |
| **Messagerie** | **Méthode qui expose la première lettre et remplace le domaine avec XXX.com de masquage** à l’aide d’un préfixe chaîne constante sous la forme d’une adresse de messagerie.<br/><br/>aXX@XXXX.com |
| **Nombre aléatoire** | **Méthode qui génère un nombre aléatoire de masquage** selon les frontières sélectionnées et les types de données réelles. Si les limites désignés sont égales, la fonction masquage sera un nombre constant.<br/><br/>![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Texte personnalisé** | **Méthode qui expose les premier et dernier caractères de masquage** et ajoute une chaîne de remplissage personnalisé au milieu. Si la chaîne d’origine est inférieure à l’exposée préfixe et suffixe, servira uniquement la chaîne de remplissage. <br/>préfixe [remplissage] suffixe<br/><br/>![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>
### <a name="recommended-fields-to-mask"></a>Champs recommandés pour masquer

Le moteur de recommandations DDM indicateurs certains champs à partir de votre base de données en tant que champs potentiellement sensibles, qui peuvent être adaptés à masquage. Dans la carte masquage des données dynamiques dans le portail, vous verrez les colonnes recommandés pour votre base de données. Il vous souhaitez est cliquez sur **Ajouter un masque** pour une ou plusieurs colonnes, puis sur **Enregistrer** pour appliquer un masque de ces champs.

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Configurer la masquage échange dynamique de données pour votre base de données à l’aide du portail Azure

1. Lancez le portail Azure à [https://portal.azure.com](https://portal.azure.com).

2. Accédez à la carte de paramètres de la base de données qui contient les données sensibles que vous souhaitez masquer.

3. Cliquez sur la vignette de **Masquage des données dynamiques** qui lance la carte **Dynamique masquage des données** de configuration.

    * Par ailleurs, vous pouvez faire défiler vers le bas jusqu'à la section **opérations** et cliquez sur **Masque dynamique de données**.

    ![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>


4. Dans la carte de configuration **Masque dynamique de données** , vous pouvez voir certaines colonnes de base de données du moteur de recommandations compte un indicateur de masquage. Afin d’accepter les recommandations, cliquez simplement sur **Ajouter un masque** pour une ou plusieurs colonnes, et un masque est créé en fonction du type par défaut pour cette colonne. Vous pouvez modifier la fonction de masquage en cliquant sur la règle de masquage et en modifiant le masquage format du champ dans un autre format de votre choix. Veillez à cliquer sur **Enregistrer** pour enregistrer vos paramètres.

    ![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>


5. Pour ajouter un masque pour n’importe quelle colonne dans votre base de données, en haut de la cuillère **Dynamique masquage des données** de configuration, cliquez sur **Ajouter un masque** pour ouvrir la carte de configuration **Ajouter une règle de masquage**

    ![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>

6. Sélectionnez le **schéma**, **Table** et **colonne** pour définir le champ désigné qui est caché.

7. Choisissez un **Format de masquage du champ** dans la liste des catégories de masquage des données sensibles.

    ![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>     

8. Dans les données masking carte règle pour mettre à jour l’ensemble de masquage des règles de la stratégie de masquage échange dynamique de données, cliquez sur **Enregistrer** .

9. Tapez les utilisateurs SQL ou les identités AAD qui doivent être exclues de masquage et aient accès aux données sensibles non masqués. Il s’agit d’une liste délimitée par des points-virgules des utilisateurs. Notez que les utilisateurs avec des privilèges d’administrateur ont toujours accès aux données d’origine non masqués.

    ![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    >[AZURE.TIP] Pour faire en sorte la couche d’application peut afficher des données sensibles pour les utilisateurs d’application privilégiée, ajoutez l’utilisateur SQL ou identité AAD l’application utilise pour interroger la base de données. Il est vivement recommandé que cette liste contiennent un nombre minimal d’utilisateurs dotés de privilèges pour réduire l’exposition des données sensibles.

10. Dans les données masking carte de configuration pour enregistrer la stratégie de masquage nouveau ou mis à jour, cliquez sur **Enregistrer** .

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Configurer l’échange dynamique de données pour votre base de données à l’aide des applets de commande Powershell de masquage

Voir [applets de commande de base de données SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx).


## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Configurer la masquage échange dynamique de données pour votre base de données à l’aide de l’API REST

Consultez les [opérations de bases de données SQL Azure](https://msdn.microsoft.com/library/dn505719.aspx).
