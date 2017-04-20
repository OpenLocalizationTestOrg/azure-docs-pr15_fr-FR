<properties
   pageTitle="Prise en main SQL de base de données dynamique données Masking (Azure classique Portal)"
   description="Comment prendre en main SQL de base de données dynamique données Masking dans le portail classique Azure"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/10/2016"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>

# <a name="get-started-with-sql-database-dynamic-data-masking-azure-classic-portal"></a>Prise en main SQL de base de données dynamique données Masking (Azure classique Portal)

> [AZURE.SELECTOR]
- [Masquage de l’échange dynamique de données - portail Azure](sql-database-dynamic-data-masking-get-started.md)

## <a name="overview"></a>Vue d’ensemble

Masquage des données dynamiques SQL de base de données limite exposition des données sensibles en masquant à des utilisateurs sans privilèges. Masquage de l’échange dynamique de données est prise en charge pour la version V12 de base de données SQL Azure.

Masquage échange dynamique de données permet d’empêcher tout accès non autorisé aux données sensibles en permettant aux clients désigner la quantité des données sensibles pour faire apparaître avec un impact minimal sur la couche d’application. Il est une fonctionnalité de sécurité basée sur des règles qui masque les données sensibles dans le jeu de résultats d’une requête sur les champs de la base de données désignée, tandis que les données dans la base de données ne sont pas modifiées.

Par exemple, un représentant du service à un centre d’appels peut identifier les appelants par plusieurs chiffres de son numéro de carte de crédit ou de numéro de sécurité sociale, mais ces éléments de données ne doivent pas être entièrement exposées au représentant du service. Une règle de masquage peut être définie que tous les masques, mais les quatre derniers chiffres de n’importe quel numéro de sécurité sociale ou numéro de carte de crédit dans le résultat de la valeur de toutes les requêtes. Un autre exemple, un masque de données appropriées peut être défini pour protéger vos données des informations d’identification personnelle (informations d’identification personnelle), afin qu’un développeur peut interroger des environnements de production pour résoudre ce problème sans violer les réglementations.

## <a name="sql-database-dynamic-data-masking-basics"></a>Concepts de base masque données dynamique SQL de base de données

Vous configurez échange dynamique de données masking stratégie dans le portail classique Azure sous l’onglet audit et de sécurité pour votre base de données.


> [AZURE.NOTE] Pour configurer l’échange dynamique de données masking dans le portail Azure, voir [prise en main SQL de base de données dynamique données Masking (Azure Portal)](sql-database-dynamic-data-masking-get-started.md).


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
| **Nombre aléatoire** | **Méthode qui génère un nombre aléatoire de masquage** selon les frontières sélectionnées et les types de données réelles. Si les limites désignés sont égales, la fonction masquage sera un nombre constant.<br/><br/>![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started-portal/1_DDM_Random_number.png) |
| **Texte personnalisé** | **Méthode qui expose les premier et dernier caractères de masquage** et ajoute une chaîne de remplissage personnalisé au milieu. Si la chaîne d’origine est inférieure à l’exposée préfixe et suffixe, servira uniquement la chaîne de remplissage.<br/>préfixe [remplissage] suffixe<br/><br/>![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started-portal/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-classic-portal"></a>Configurer la masquage échange dynamique de données pour votre base de données à l’aide du portail classique Azure

1. Lancez le portail classique Azure à [https://manage.windowsazure.com](https://manage.windowsazure.com).

2. Cliquez sur la base de données que vous souhaitez masquer, puis cliquez sur l’onglet **audit et sécurité** .

3. Sous **masking échange dynamique de données**, cliquez sur **activé** pour activer la fonctionnalité de masquage échange dynamique de données.  

4. Tapez les utilisateurs SQL ou les identités AAD qui doivent être exclues de masquage et aient accès aux données sensibles non masqués. Il s’agit d’une liste délimitée par des points-virgules des utilisateurs. Notez que les utilisateurs avec des privilèges d’administrateur ont toujours accès aux données d’origine non masqués.

    >[AZURE.TIP] Pour faire en sorte la couche d’application peut afficher des données sensibles pour les utilisateurs d’application privilégiée, ajoutez l’utilisateur SQL ou identité AAD l’application utilise pour interroger la base de données. Il est vivement recommandé que cette liste contiennent un nombre minimal d’utilisateurs dotés de privilèges pour réduire l’exposition des données sensibles.

    ![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started-portal/4_ddm_policy_classic_portal.png)

5. Dans la partie inférieure de la page dans la barre de menus, cliquez sur **Ajouter un masque** pour ouvrir le masquage fenêtre configuration de la règle.

6. Sélectionnez le **schéma**, de **Table** et de **colonne** dans les listes déroulantes pour définir les champs désignés seront cachés.

7. Sélectionnez une **Fonction MASKING** dans la liste des catégories de masquage des données sensibles.

    ![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started-portal/5_DDM_Add_Masking_Rule_Classic_Portal.png)

8. Cliquez sur **OK** dans les données masking fenêtre règle pour mettre à jour l’ensemble de masquage des règles de la stratégie de masquage échange dynamique de données.

9. Cliquez sur **Enregistrer** pour enregistrer la stratégie de masquage nouveau ou mis à jour.


## <a name="set-up-dynamic-data-masking-for-your-database-using-transact-sql-statements"></a>Configurer l’échange dynamique de données masking pour votre base de données à l’aide des instructions Transact-SQL

Voir [échange dynamique de données de masquage](https://msdn.microsoft.com/library/mt130841.aspx).

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Configurer l’échange dynamique de données pour votre base de données à l’aide des applets de commande Powershell de masquage

Voir [applets de commande de base de données SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Configurer la masquage échange dynamique de données pour votre base de données à l’aide de l’API REST

Consultez les [opérations de bases de données SQL Azure](https://msdn.microsoft.com/library/dn505719.aspx).
