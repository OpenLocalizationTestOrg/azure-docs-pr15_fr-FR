<properties
   pageTitle="Audit dans SQL Azure Data Warehouse | Microsoft Azure"
   description="Prise en main d’audit dans le magasin de données SQL Azure"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="auditing-in-azure-sql-data-warehouse"></a>Audit dans SQL Azure Data Warehouse

> [AZURE.SELECTOR]
- [L’audit](sql-data-warehouse-auditing-overview.md)
- [Détection des menaces](sql-data-warehouse-security-threat-detection.md)

L’audit SQL Data Warehouse vous permet d’enregistrement journal des événements dans votre base de données à un audit dans votre compte de stockage Azure. L’audit peut vous aider à assurer la conformité réglementaire, comprendre l’activité de base de données et découvrez les différences et les anomalies qui peuvent indiquer des problèmes d’entreprise ou suspects violations de sécurité. L’audit SQL Data Warehouse intègre également Microsoft Power BI pour les rapports et l’analyse exploration des niveaux inférieurs.

Outils d’audit activer et facilitent le respect des normes de conformité, mais ne garantissent la conformité. Pour plus d’informations sur les programmes Azure qui prennent en charge de la conformité aux normes, voir le <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Centre de gestion de la confidentialité Azure</a>.

+ [Concepts de base audit de base de données]
+ [Configurer l’audit pour votre base de données]
+ [Analyser les journaux d’audit et les rapports]

##<a id="subheading-1"></a>Bases d’audit de base de données de magasin données SQL Azure


Audit de base de données SQL Data Warehouse vous permet de :

- **Conserver** une trace d’audit des événements sélectionnés. Vous pouvez définir des catégories d’actions de base de données pour l’audit.
- **Rapport** sur l’activité de base de données. Vous pouvez utiliser des rapports préconfigurés et un tableau de bord pour commencer rapidement avec activité et les rapports d’événements.
- Rapports **d’analyse** . Vous pouvez trouver les événements suspects, activité inhabituelle et des tendances.

Vous pouvez configurer l’audit pour les catégories d’événements suivants :

**Simple** et **SQL paramétrées** dont les collectées journaux d’audit sont considérés comme  

- **Accès aux données**
- **Modifications de schéma (DDL)**
- **Modifications des données (DML)**
- **Comptes, les rôles et autorisations (DCL)**
- **Procédure stockée**, **connexion** et **Gestion des transactions**.

Pour chaque catégorie d’événement, audit de **Réussite** et **d’Échec** opérations sont configurés séparément.

Pour plus d’informations sur les activités et les événements à auditer, voir la <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">Référence de Format du journal d’Audit (téléchargement du fichier document)</a>.

Journaux d’audit sont stockés dans votre compte de stockage Azure. Vous pouvez définir une période de rétention du journal d’audit.

Peut être définie une stratégie d’audit pour une base de données ou comme une stratégie de serveur par défaut. Une stratégie d’audit serveur par défaut s’appliquent à toutes les bases de données sur un serveur qui ne disposent pas d’une substitution de base de données stratégie d’audit spécifique définie.

Avant de paramètre à distance d’audit à cocher audit si vous utilisez un [« Client de niveau inférieur »](sql-data-warehouse-auditing-downlevel-clients.md).


##<a id="subheading-2"></a>Configurer l’audit pour votre base de données

1. Lancez le <a href="https://portal.azure.com" target="_blank">portail Azure</a>.

2. Accédez à la carte de configuration de la base de données SQL Data Warehouse / SQL Server que vous voulez auditer. Cliquez sur le bouton **paramètres** en haut, puis, dans la carte de paramètre, puis sélectionnez **audit**.

    ![][1]

3. Dans la carte de configuration d’audit, désactivez d’abord la case à cocher **Conserver les paramètres d’audit de serveur** . Cela vous permet de spécifier les paramètres pour une base de données particulière.

    ![][2]

4. Ensuite, activer l’audit en cliquant **sur** le bouton.

    ![][3]

5. Dans la carte de configuration d’audit, sélectionnez **Détails de stockage** pour ouvrir la carte de stockage de journaux d’Audit. Sélectionnez le compte de stockage Azure où les journaux doivent être enregistrés et la période de rétention. **Conseil :** Utilisez le même compte de stockage pour toutes les bases de données vérifiés pour tirer le meilleur parti les modèles de rapports préconfigurés.

    ![][4]

6. Cliquez sur le bouton **OK** pour enregistrer la configuration des détails de stockage.


7. Sous **Journalisation par événement**, cliquez sur **Réussite** et **Échec** pour tous les événements ou choisissez catégories d’événements individuels.


8. Si vous configurez l’audit pour une base de données, vous devrez peut-être modifier la chaîne de connexion de votre client afin de garantir l’audit des données est correctement capturé. Consultez la rubrique [Modifier serveur FQDN dans la chaîne de connexion](sql-data-warehouse-auditing-downlevel-clients.md) pour les connexions de client de niveau inférieur.

9. Cliquez sur **OK**.


##<a id="subheading-3">Analyser les journaux d’audit et les rapports</a>

Journaux d’audit sont regroupées dans une collection de Tables Store avec un préfixe **SQLDBAuditLogs** dans le compte de stockage Azure que vous avez choisi lors de l’installation. Vous pouvez afficher les fichiers journaux à l’aide d’un outil comme <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Explorateur de stockage Azure</a>.

Un modèle de rapport de tableau de bord préconfigurés est disponible sous la forme d’une <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">feuille de calcul Excel téléchargeable</a> pour vous aider à analyser rapidement des données du journal. Pour utiliser le modèle sur les journaux d’audit, vous avez besoin d’Excel 2013 ou version ultérieure et Power Query, vous pouvez télécharger <a href="http://www.microsoft.com/download/details.aspx?id=39379">ici</a>.

Le modèle comporte des données d’exemple fictif, et vous pouvez configurer Power Query pour importer votre journal d’audit directement à partir de votre compte de stockage Azure.

Pour obtenir des instructions plus détaillées sur l’utilisation du modèle d’état, lisez la section <a href="http://go.microsoft.com/fwlink/?LinkId=506731">How To (à télécharger un document)</a>.

![][5]


##<a id="subheading-4">Méthodes conseillées pour l’utilisation de production</a>
La description dans cette section fait référence à des captures d’écran ci-dessus. <a href="https://portal.azure.com" target="_blank">Portail Azure</a> ou <a href= "https://manage.windowsazure.com/" target="_bank">Classique portail classique Azure</a> peut être utilisé.


##<a id="subheading-5"></a>Régénération de clé de stockage

Dans production vous sont susceptibles d’actualiser périodiquement vos clés de stockage. Lorsque actualisez vos clés vous devez ré-enregistrer la stratégie. Le processus se présente comme suit :.


1. Dans la carte de configuration audit (décrite ci-dessus dans le jeu de la section d’audit) basculez la **Touche d’accès rapide stockage** de *principal* pour *secondaire* et **Enregistrer**.
![][4]
2. Accédez à la carte de configuration de stockage et **Régénérer** la *Clé primaire d’accès*.

3. Accédez à la carte de configuration d’audit, basculez la **Touche d’accès rapide stockage** de *secondaire* *principale* et appuyez sur **Enregistrer**.

4. Revenez à l’interface utilisateur de l’espace de stockage et **Régénérer** la *Secondaire touche d’accès rapide* (dans la préparation de la prochaine actualisation clés.

##<a id="subheading-6"></a>Automatisation
Il existe plusieurs applets de commande PowerShell que vous pouvez utiliser pour configurer un audit dans la base de données SQL Azure. Pour accéder aux applets de commande audit vous devez exécuter PowerShell en mode Azure le Gestionnaire de ressources.

> [AZURE.NOTE] Le module [Azure le Gestionnaire de ressources](https://msdn.microsoft.com/library/dn654592.aspx) est en cours d’aperçu. Il peut ne pas fournir les mêmes fonctionnalités de gestion que du module Azure.

Lorsque vous êtes en mode directeur des ressources Azure, exécutez `Get-Command *AzureSql*` pour répertorier les applets de commande disponibles.


<!--Anchors-->
[Concepts de base audit de base de données]: #subheading-1
[Configurer l’audit pour votre base de données]: #subheading-2
[Analyser les journaux d’audit et les rapports]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


<!--Link references-->
