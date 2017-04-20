<properties
    pageTitle="Flux Analytique données Lake stocker les résultats | Microsoft Azure"
    description="Configuration de l’authentification et l’autorisation d’un magasin Azure données Lake dans une tâche de flux de données Analytique"
    keywords=""
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="stream-analytics-data-lake-store-output"></a>Flux Analytique données Lake stocker les résultats

Tâches d’Analytique flux prend en charge plusieurs méthodes de sortie, un correspondant à un [Magasin de Lake données Azure](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store est un référentiel hyper-échelle échelle de l’entreprise pour les charges de travail analytique données volumineuses. Données Lake Store permet de stocker des données de n’importe quelle vitesse taille, le type et réception d’analytique opérationnelles et exploratoires.

## <a name="authorize-a-data-lake-store-account"></a>Autoriser un compte données Lake Store

1.  Lorsque données Lake Store est sélectionné comme résultat dans le portail de gestion Azure, vous devrez pour autoriser l’utilisation de votre Lake Data Warehouse existant ou pour demander l’accès à l’aperçu des données Lake Store via le portail classique Azure.

    ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)  

2.  Si vous avez déjà accès aux données Lake Store, cliquez sur « Autoriser maintenant » et un horaire brève une page s’affiche indiquant « Redirection d’une autorisation... ». La page se ferme automatiquement et s’affiche avec la page qui vous permet de configurer les données Lake stocker les résultats.

Si vous n’avez pas inscrit pour données Lake Store Preview, vous pouvez suivre le lien « S’inscrire » pour lancer la requête, ou suivez [obtenir des instructions de mise en route](../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="configure-the-data-lake-store-output-properties"></a>Configurer les propriétés de sortie de données Lake Store

Une fois que le compte de données Lake Store authentifié, vous pouvez configurer les propriétés pour vos données Lake stocker les résultats. Le tableau ci-dessous est la liste des noms de propriété et leur description pour configurer vos données Lake stocker les résultats.

<table>
<tbody>
<tr>
<td><B>NOM DE LA PROPRIÉTÉ</B></td>
<td><B>DESCRIPTION</B></td>
</tr>
<tr>
<td>Alias de sortie</td>
<td>Il s’agit d’un nom convivial permettant de diriger le résultat de la requête pour le magasin Lake de données dans des requêtes.</td>
</tr>
<tr>
<td>Données Lake Store compte</td>
<td>Le nom du compte de stockage où vous envoyez votre sortie. S’affiche avec une liste déroulante liste des comptes de données Lake Store qui a accès à l’utilisateur connecté au portail.</td>
</tr>
<tr>
<td>Chemin d’accès du préfixe motif [<I>facultatif</I>]</td>
<td>Le chemin d’accès de fichier utilisé pour écrire vos fichiers dans le compte de magasin de données Lake spécifié. <BR>{date}, {time}<BR>Exemple 1 : dossier 1/journaux / {date} / {time}<BR>Exemple 2 : dossier 1/journaux / {date}</td>
</tr>
<tr>
<td>Format de date [<I>facultatif</I>]</td>
<td>Si le jeton de date est utilisé dans le chemin d’accès de préfixe, vous pouvez sélectionner le format de date dans lequel vos fichiers sont organisées. Exemple : AAAA/MM/JJ</td>
</tr>
<tr>
<td>Format d’heure [<I>facultatif</I>]</td>
<td>Si le jeton de temps est utilisé dans le chemin d’accès de préfixe, spécifiez le format d’heure dans lequel vos fichiers sont organisées. Actuellement la seule valeur pris en charge est HH.</td>
</tr>
<tr>
<td>Format de sérialisation événement</td>
<td>Format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge.</td>
</tr>
<tr>
<td>Codage</td>
<td>Si le format CSV ou JSON, un codage doit être spécifié. UTF-8 est le format de codage pris en charge uniquement pour le moment.</td>
</tr>
<tr>
<td>Délimiteur</td>
<td>Uniquement applicable pour la sérialisation CSV. Flux Analytique prend en charge un certain nombre de délimiteurs communs pour sérialisation des données CSV. Valeurs prises en charge sont virgule, point-virgule, espace, onglet et barre verticale.</td>
</tr>
<tr>
<td>Mettre en forme</td>
<td>Uniquement applicable pour la sérialisation JSON. Ligne séparée indique que le résultat sera formaté en demandant à chaque objet JSON séparé par une nouvelle ligne. Tableau d’indique que le résultat sera mis en forme comme un tableau d’objets JSON.</td>
</tr>
</tbody>
</table>

## <a name="renew-data-lake-store-authorization"></a>Renouveler l’autorisation de données Lake Store

Pour l’instant, il existe certains une limitation où le jeton d’authentification doit être actualisé manuellement tous les 90 jours pour toutes les tâches avec les données Lake stocker les résultats. Vous devrez également s’authentifier à nouveau votre compte données Lake Store si vous avez modifié votre mot de passe dans la mesure où votre travail a été créé ou dernier authentifié. Un problème de ce problème est aucune sortie des tâches et un message d’erreur dans les journaux d’opération indiquant qu’il soit nécessaire pour ré l’autorisation.

Pour résoudre ce problème, arrêter votre travail en cours d’exécution et accédez à vos données Lake stocker les résultats. Cliquez sur le lien « Renouveler autorisation » et pour un peu de temps une page s’affiche indiquant « Redirection d’une autorisation... ». La page se ferme automatiquement et en cas de réussite, indiquera « Autorisation a été renouvelée ». Vous puis qu’il soit nécessaire de cliquer sur « Enregistrer » dans la partie inférieure de la page et pouvez passer en redémarrant votre travail à partir de la dernière fois arrêté pour éviter toute perte de données.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)
