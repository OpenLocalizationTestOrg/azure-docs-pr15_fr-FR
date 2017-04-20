<properties
   pageTitle="Catalogue de données Azure pris en charge les sources de données | Microsoft Azure"
   description="Spécification des sources de données actuellement pris en charge."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="jstrauss"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/15/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-supported-data-sources"></a>Catalogue de données Azure pris en charge les sources de données

Les utilisateurs du catalogue de données Azure peuvent publier des métadonnées à l’aide d’une API publique, un clic-une fois l’enregistrement outil, ou en entrant manuellement les informations directement dans le catalogue de données web portail. La grille suivante résume toutes les sources de prise en charge par le catalogue aujourd'hui et les fonctionnalités de publication pour chacun.  Les outils de données externes que chaque source peut lancer l’expérience de notre portail « ouvrir dans » sont également répertoriées. La deuxième grille dans l’article a spécifications techniques de chaque propriétés de connexion de sources de données.


## <a name="list-of-supported-data-sources"></a>Liste des sources de données prises en charge

<table>

    <tr>
       <td><b>Objet Source de données</b></td>
       <td><b>API</b></td>
       <td><b>Saisie manuelle</b></td>
       <td><b>Outil d’inscription</b></td>
       <td><b>Ouvrir dans les outils</b></td>
       <td><b>Notes</b></td>
    </tr>

    <tr>
      <td>Répertoire du magasin de Lake données Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Fichier de magasin de Lake données Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Stockage Azure Blob</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Répertoire de stockage Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Table de stockage Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>

    <tr>
      <td>Répertoire HADOOP</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Fichier HADOOP</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Table Hive</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Affichage Hive</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Table MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Affichage MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Table de base de données Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vue de base de données Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Autres (biens générique)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Table SQL Data Warehouse</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Mode de magasin de données SQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services Dimension</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Indicateurs de performance clés de SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services mesure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Table SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Outils SQL Server Reporting Services rapport</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Navigateur</font></td>
      <td><font size=2>Serveurs en mode natif uniquement. Mode SharePoint n’est pas pris en charge.</font></td>
    </tr>

    <tr>
      <td>Table SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vue SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Table Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Affichage Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Affichage de Hana SAP</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2>Affichages de calcul et des vues analytiques uniquement ; Affichages des attributs ne sont pas pris en charge.</font></td>
    </tr>

    <tr>
      <td>Table DB2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Affichage DB2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Fichier système</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Répertoire FTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Fichier FTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Rapport http</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Point de terminaison HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Fichier http</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Jeu d’entités OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Fonction OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Table PostgreSQL</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Affichage PostgreSQL</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Affichage de Hana SAP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td> Objet Salesforce</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Liste SharePoint </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>

Si vous devez prendre en charge d’autres sources, envoyez une demande de fonctionnalité via le [forum de catalogue de données Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).


<br>
<br>
## <a name="data-source-reference-specification"></a>Spécification de référence de source de données
> [AZURE.NOTE] Colonne « DSL la Structure » dans les tableau uniquement listes propriétés de connexion suivantes pour sac de propriétés de « adresse » qui sont utilisées par le catalogue de données Azure (c'est-à-dire sac de propriétés de « adresse » peut contenir d’autres propriétés de connexion de la source de données qui catalogue de données Azure persiste, mais n’utilise pas.)
<table>
    <tr>
       <td><b>Type de source</b></td>
       <td><b>Type d’élément</b></td>
       <td><b>Types d’objet</b></td>
       <td><b>Structure DSL<b></td>
    </tr>
    <tr>
      <td>Magasin de Lake données Azure</td>
      <td>Conteneur</td>
      <td>Données Lake</td>
      <td>
        <font size=2>protocole : webhdfs <br>authentification : {base, oauth} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Magasin de Lake données Azure</td>
      <td>Table</td>
      <td>Répertoire, fichier</td>
      <td>
        <font size=2>protocole : webhdfs <br>authentification : {base, oauth} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Stockage Azure</td>
      <td>Conteneur</td>
      <td>Conteneur</td>
      <td>
        <font size=2>protocole : objets BLOB azure <br>authentification : {azure-touches d’accès} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domaine <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;compte <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;conteneur</font>
      </td>
    </tr>
    <tr>
      <td>Stockage Azure</td>
      <td>Table</td>
      <td>BLOB, répertoire</td>
      <td>
        <font size=2>protocole : objets BLOB azure <br>authentification : {azure-touches d’accès} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domaine <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;compte <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;conteneur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;nom</font>
      </td>
    </tr>
    <tr>
      <td>Stockage Azure</td>
      <td>Conteneur</td>
      <td>Conteneur</td>
      <td>
        <font size=2>protocole : azure tables <br>authentification : {azure-touches d’accès} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domaine <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;compte</font>
      </td>
    </tr>
    <tr>
      <td>Stockage Azure</td>
      <td>Table</td>
      <td>Table</td>
      <td>
        <font size=2>protocole : azure tables <br>authentification : {azure-touches d’accès} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domaine <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;compte <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;nom</font>
      </td>
    </tr>
    <tr>
      <td>COSMOS</td>
      <td>Conteneur</td>
      <td>Cluster virtuel</td>
      <td>
        <font size=2>protocole : cosmos <br>authentification : {base, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>COSMOS</td>
      <td>Table</td>
      <td>Flux de données, jeu de flux de données, affichage</td>
      <td>
        <font size=2>protocole : cosmos <br>authentification : {base, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Conteneur</td>
      <td>Site</td>
      <td>
        <font size=2>protocole : http <br>authentification : {aucun, basic, windows, oauth} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Rapport</td>
      <td>Rapport de tableau de bord</td>
      <td>
        <font size=2>protocole : http <br>authentification : {aucun, basic, windows, oauth} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2>protocole : db2 <br>authentification : {base, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données</font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Table</td>
      <td>Affichage de table</td>
      <td>
        <font size=2>protocole : db2 <br>authentification : {base, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objet <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schéma</font>
      </td>
    </tr>
    <tr>
      <td>Système de fichiers</td>
      <td>Table</td>
      <td>Fichier</td>
      <td>
        <font size=2>protocole : fichier <br>authentification : {aucun, base, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;chemin d’accès</font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>Table</td>
      <td>Répertoire, fichier</td>
      <td>
        <font size=2>protocole : ftp <br>authentification : {aucun, base, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Système de fichiers DFS Hadoop</td>
      <td>Conteneur</td>
      <td>Cluster</td>
      <td>
        <font size=2>protocole : webhdfs <br>authentification : {base, oauth} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Système de fichiers DFS Hadoop</td>
      <td>Table</td>
      <td>Répertoire, fichier</td>
      <td>
        <font size=2>protocole : webhdfs <br>authentification : {base, oauth} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2>protocole : hive <br>authentification : {nom d’utilisateur de base, de hdinsight, aucun} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>connectionProperties : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serverProtocol : {hive2}</font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Table</td>
      <td>Affichage de table</td>
      <td>
        <font size=2>protocole : hive <br>authentification : {nom d’utilisateur de base, de hdinsight, aucun} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objet <br>connectionProperties : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serverProtocol : {hive2}</font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Conteneur</td>
      <td>Site</td>
      <td>
        <font size=2>protocole : http <br>authentification : {aucun, basic, windows, oauth} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Rapport</td>
      <td>Rapport de tableau de bord</td>
      <td>
        <font size=2>protocole : http <br>authentification : {aucun, basic, windows, oauth} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Table</td>
      <td>Point de terminaison, fichier</td>
      <td>
        <font size=2>protocole : http <br>authentification : {aucun, basic, windows, oauth} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2>protocole : mysql <br>authentification : {protocole, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données</font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Table</td>
      <td>Affichage de table</td>
      <td>
        <font size=2>protocole : mysql <br>authentification : {protocole, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objet</font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Conteneur</td>
      <td>Conteneur d’entités</td>
      <td>
        <font size=2>protocole : odata <br>authentification : {aucun, base, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Table</td>
      <td>Jeu d’entités, fonction</td>
      <td>
        <font size=2>protocole : odata <br>authentification : {aucun, base, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ressource</font>
      </td>
    </tr>
    <tr>
      <td>Base de données Oracle</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2>protocole : oracle <br>authentification : {protocole, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données</font>
      </td>
    </tr>
    <tr>
      <td>Base de données Oracle</td>
      <td>Table</td>
      <td>Affichage de table</td>
      <td>
        <font size=2>protocole : oracle <br>authentification : {protocole, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schéma <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objet</font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2>protocole : postgresql <br>authentification : {base, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données</font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Table</td>
      <td>Affichage de table</td>
      <td>
        <font size=2>protocole : postgresql <br>authentification : {base, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schéma <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objet</font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Conteneur</td>
      <td>Site</td>
      <td>
        <font size=2>protocole : http <br>authentification : {aucun, basic, windows, oauth} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Rapport</td>
      <td>Rapport de tableau de bord</td>
      <td>
        <font size=2>protocole : http <br>authentification : {aucun, basic, windows, oauth} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>Table</td>
      <td>Application Web hybride de données</td>
      <td>
        <font size=2>protocole : power query <br>authentification : {oauth} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Force de vente</td>
      <td>Table</td>
      <td>Objet</td>
      <td>
        <font size=2>protocole : salesforce com <br>authentification : {base, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;loginServer <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;cours <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;nom de l’élément</font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>Conteneur</td>
      <td>Serveur</td>
      <td>
        <font size=2>protocole : sap-hana-sql <br>authentification : {protocole, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur</font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>Table</td>
      <td>Affichage</td>
      <td>
        <font size=2>protocole : sap-hana-sql <br>authentification : {protocole, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schéma <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objet</font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Table</td>
      <td>Liste</td>
      <td>
        <font size=2>protocole : liste sharepoint <br>authentification : {base, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Data Warehouse SQL</td>
      <td>Commande</td>
      <td>Procédure stockée</td>
      <td>
        <font size=2>protocole : tds <br>authentification : {protocole, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schéma <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objet</font>
      </td>
    </tr>
    <tr>
      <td>Data Warehouse SQL</td>
      <td>TableValuedFunction</td>
      <td>Fonction table</td>
      <td>
        <font size=2>protocole : tds <br>authentification : {protocole, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schéma <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objet</font>
      </td>
    </tr>
    <tr>
      <td>Data Warehouse SQL</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2>protocole : tds <br>authentification : {protocole, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données</font>
      </td>
    </tr>
    <tr>
      <td>Data Warehouse SQL</td>
      <td>Table</td>
      <td>Affichage de table</td>
      <td>
        <font size=2>protocole : tds <br>authentification : {protocole, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schéma <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objet</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Commande</td>
      <td>Procédure stockée</td>
      <td>
        <font size=2>protocole : tds <br>authentification : {protocole, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schéma <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objet</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Fonction table</td>
      <td>
        <font size=2>protocole : tds <br>authentification : {protocole, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schéma <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objet</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2>protocole : tds <br>authentification : {protocole, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Table</td>
      <td>Affichage de table</td>
      <td>
        <font size=2>protocole : tds <br>authentification : {protocole, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schéma <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objet</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensionnels</td>
      <td>Conteneur</td>
      <td>Modèle</td>
      <td>
        <font size=2>protocole : analysis services <br>authentification : {windows, de base, anonyme, aucun} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modèle</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensionnels</td>
      <td>INDICATEUR DE PERFORMANCE CLÉ</td>
      <td>INDICATEUR DE PERFORMANCE CLÉ</td>
      <td>
        <font size=2>protocole : analysis services <br>authentification : {windows, de base, anonyme, aucun} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modèle <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objet <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;type d’objet : {indicateur de performance clé}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensionnels</td>
      <td>Mesure</td>
      <td>Mesure</td>
      <td>
        <font size=2>protocole : analysis services <br>authentification : {windows, de base, anonyme, aucun} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modèle <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objet <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;type d’objet : {mesure}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensionnels</td>
      <td>Table</td>
      <td>Dimension</td>
      <td>
        <font size=2>protocole : analysis services <br>authentification : {windows, de base, anonyme, aucun} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modèle <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objet <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;type d’objet : {Dimension}</font>
      </td>
    </tr>
    <tr>
      <td>Tabulaire SQL Server Analysis Services</td>
      <td>Conteneur</td>
      <td>Modèle</td>
      <td>
        <font size=2>protocole : analysis services <br>authentification : {windows, de base, anonyme, aucun} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modèle</font>
      </td>
    </tr>
    <tr>
      <td>Tabulaire SQL Server Analysis Services</td>
      <td>INDICATEUR DE PERFORMANCE CLÉ</td>
      <td>INDICATEUR DE PERFORMANCE CLÉ</td>
      <td>
        <font size=2>protocole : analysis services <br>authentification : {windows, de base, anonyme, aucun} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modèle <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objet <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;type d’objet : {indicateur de performance clé}</font>
      </td>
    </tr>
    <tr>
      <td>Tabulaire SQL Server Analysis Services</td>
      <td>Mesure</td>
      <td>Mesure</td>
      <td>
        <font size=2>protocole : analysis services <br>authentification : {windows, de base, anonyme, aucun} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modèle <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objet <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;type d’objet : {mesure}</font>
      </td>
    </tr>
    <tr>
      <td>Tabulaire SQL Server Analysis Services</td>
      <td>Table</td>
      <td>Table</td>
      <td>
        <font size=2>protocole : analysis services <br>authentification : {windows, de base, anonyme, aucun} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modèle <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objet <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;type d’objet : {Table}</font>
      </td>
    </tr>
    <tr>
      <td>Outils SQL Server Reporting Services</td>
      <td>Conteneur</td>
      <td>Serveur</td>
      <td>
        <font size=2>protocole : reporting services <br>authentification : {windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;version : {ReportingService2010}</font>
      </td>
    </tr>
    <tr>
      <td>Outils SQL Server Reporting Services</td>
      <td>Rapport</td>
      <td>Rapport</td>
      <td>
        <font size=2>protocole : reporting services <br>authentification : {windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;chemin d’accès <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;version : {ReportingService2010}</font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Conteneur</td>
      <td>Base de données</td>
      <td>
        <font size=2>protocole : teradata <br>authentification : {protocole, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données</font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Table</td>
      <td>Affichage de table</td>
      <td>
        <font size=2>protocole : teradata <br>authentification : {protocole, windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serveur <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;base de données <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objet</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Conteneur</td>
      <td>Modèle</td>
      <td>
        <font size="2">protocole : mssql mds <br>authentification : {windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modèle <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Version</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Table</td>
      <td>Entité</td>
      <td>
        <font size="2">protocole : mssql mds <br>authentification : {windows} <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modèle <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Version <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;entité</font>
      </td>
    </tr>
    <tr>
      <td>Autres (et non un des éléments ci-dessus)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2>protocole : générique biens <br>adresse : <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;réfbien</font>
      </td>
    </tr>
</table>
