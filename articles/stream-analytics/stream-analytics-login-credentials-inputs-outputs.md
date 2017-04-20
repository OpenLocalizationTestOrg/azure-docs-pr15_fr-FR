<properties 
    pageTitle="Flux Analytique : Faire pivoter le journal des informations d’identification pour les entrées et sorties | Microsoft Azure" 
    description="Découvrez comment mettre à jour les informations d’identification pour flux Analytique entrées et sorties."
    keywords="informations d’identification"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

#<a name="rotate-login-credentials-for-inputs-and-outputs-in-stream-analytics-jobs"></a>Faire pivoter des informations d’identification pour les entrées et sorties dans les flux Analytique tâches

##<a name="abstract"></a>Résumé
Azure flux Analytique aujourd'hui ne permet pas de remplacer les informations d’identification sur un sorties pendant l’exécution de la tâche.

Azure flux Analytique ne prend pas en charge la reprise d’une tâche à partir de la dernière sortie, nous voulons partager l’ensemble du processus de réduire le décalage entre l’arrêt et démarrage de la tâche et faisant pivoter les informations d’identification.

##<a name="part-1---prepare-the-new-set-of-credentials"></a>Partie 1 : préparer le nouveau jeu d’informations d’identification :
Cet article s’applique aux entrées/sorties suivantes :

* Stockage d’objets BLOB
* Événement Hubs
* Base de données SQL
* Stockage de table

Pour d’autres entrées/sorties, passez à la partie 2.

###<a name="blob-storagetable-storage"></a>Stockage de Table/de stockage d’objets BLOB
1.  Accédez à l’extension de stockage sur le portail de gestion Azure :  
![graphique1][graphic1]
2.  Recherchez le stockage utilisé par votre travail et accédez dedans :  
![graphique2][graphic2]
3.  Cliquez sur la commande gérer les touches d’accès :  
![graphic3][graphic3]
4.  Entre la touche d’accès principal et secondaire touche d’accès rapide, **sélectionnez celui ne pas utilisé par votre tâche**.
5.  Appuyez sur Régénérer :  
![graphic4][graphic4]
6.  Copiez la clé que vous venez générée :  
![graphic5][graphic5]
7.  Passez à la partie 2.

###<a name="event-hubs"></a>Hubs d’événement
1.  Accédez à l’extension de Bus des services sur le portail de gestion Azure :  
![graphic6][graphic6]
2.  Recherchez le Namespace Bus Service utilisé par votre tâche et accédez dedans :  
![graphic7][graphic7]
3.  Si votre projet utilise une stratégie d’accès partagé sur le Namespace Bus Service, passez à l’étape 6  
4.  Accédez à l’onglet événement Hubs :  
![graphic8][graphic8]
5.  Recherchez le Hub événement utilisée par votre travail et accédez dedans :  
![graphic9][graphic9]
6.  Accédez à l’onglet Configurer :  
![graphic10][graphic10]
7.  Dans la liste déroulante Nom de la stratégie, recherchez la stratégie d’accès partagé utilisée par votre travail :  
![graphic11][graphic11]
8.  Entre la clé primaire et la clé secondaire, **sélectionnez celui ne pas utilisé par votre tâche**.  
9.  Appuyez sur Régénérer :  
![graphic12][graphic12]
10. Copiez la clé que vous venez générée :  
![graphic13][graphic13]
11. Passez à la partie 2.  

###<a name="sql-database"></a>Base de données SQL

>[AZURE.NOTE] Remarque : vous devez vous connecter au Service de base de données SQL. Nous allons montrent comment procéder à l’aide de l’expérience de gestion sur le portail de gestion Azure, mais vous pouvez choisir d’utiliser un outil côté client telles que SQL Server Management Studio.

1.  Accédez à l’extension de bases de données SQL sur le portail de gestion Azure :  
![graphic14][graphic14]
2.  Recherchez la base de données SQL utilisée par votre lien tâche et **cliquez sur le serveur** sur la même ligne :  
![graphic15][graphic15]
3.  Cliquez sur la commande gérer :  
![graphic16][graphic16]
4.  Type de base de données Master :  
![graphic17][graphic17]
5.  Tapez votre nom d’utilisateur, mot de passe, puis cliquez sur journal sur :  
![graphic18][graphic18]
6.  Cliquez sur Nouvelle requête :  
![graphic19][graphic19]
7.  Entrer du texte dans la requête suivante en remplaçant < nom_de_connexion_du_propriétaire > par votre nom d’utilisateur et le remplacement <enterStrongPasswordHere> avec votre nouveau mot de passe :  
`CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>'`
8.  Cliquez sur Exécuter :  
![graphic20][graphic20]
9.  Revenez à l’étape 2 et cette fois, cliquez sur la base de données :  
![graphic21][graphic21]
10. Cliquez sur la commande gérer :  
![graphic22][graphic22]
11. Tapez votre nom d’utilisateur, mot de passe, puis cliquez sur connexion :  
![graphic23][graphic23]
12. Cliquez sur Nouvelle requête :  
![graphic24][graphic24]
13. Tapez la requête suivante en remplaçant < nom_utilisateur > par un nom par lequel vous voulez identifier cette connexion dans le cadre de cette base de données (vous pouvez fournir la même valeur que vous avez attribué < nom_de_connexion_du_propriétaire >, par exemple) et en remplaçant < nom_de_connexion_du_propriétaire > par votre nom d’utilisateur :  
`CREATE USER <user_name> FROM LOGIN <login_name>`
14. Cliquez sur Exécuter :  
![graphic25][graphic25]
15. Vous devez maintenant fournir votre nouvel utilisateur avec les rôles et les privilèges votre d’origine qu’il a même.
16. Passez à la partie 2.

##<a name="part-2-stopping-the-stream-analytics-job"></a>Partie 2 : Arrêter flux Analytique
1.  Accédez à l’extension Analytique de flux de données sur le portail de gestion Azure :  
![graphic26][graphic26]
2.  Recherchez votre travail et accédez dedans :  
![graphic27][graphic27]
3.  Accédez à l’onglet entrées ou sorties selon que vous faites pivoter les informations d’identification sur entrée ou sur un résultat.  
![graphic28][graphic28]
4.  Cliquez sur la commande Stop et confirmer que l’arrêt de la tâche :  
![graphic29][graphic29] attendre la tâche pour l’arrêter.
5.  Recherchez l’entrée/sortie que vous voulez faire pivoter des informations d’identification sur Composition à emporter dedans :  
![graphic30][graphic30]
6.  Passez à la partie 3.

##<a name="part-3-editing-the-credentials-on-the-stream-analytics-job"></a>Partie 3 : Modifier les informations d’identification pour la tâche Analytique flux

###<a name="blob-storagetable-storage"></a>Stockage de Table/de stockage d’objets BLOB
1.  Recherchez le champ de clé de compte de stockage, puis collez votre clé nouvellement créée dans ce :  
![graphic31][graphic31]
2.  Cliquez sur la commande Enregistrer et confirmez enregistrer vos modifications :  
![graphic32][graphic32]
3.  Un test de connexion démarre automatiquement lorsque vous enregistrez vos modifications, vérifiez que d’autres termes a réussi.
4.  Passez à la partie 4.

###<a name="event-hubs"></a>Hubs d’événement
1.  Recherchez le champ événement concentrateur stratégie clé, puis collez votre clé nouvellement créée dans ce :  
![graphic33][graphic33]
2.  Cliquez sur la commande Enregistrer et confirmez enregistrer vos modifications :  
![graphic34][graphic34]
3.  Un test de connexion démarre automatiquement lorsque vous enregistrez vos modifications, assurez-vous qu’il a réussi.
4.  Passez à la partie 4.

###<a name="power-bi"></a>Power BI
1.  Cliquez sur l’autorisation de renouveler :  
* ![graphic35][graphic35]
* Vous obtenez la confirmation suivante :  
* ![graphic36][graphic36]
2.  Cliquez sur la commande Enregistrer et confirmez enregistrer vos modifications :  
![graphic37][graphic37]
3.  Un test de connexion démarre automatiquement lorsque vous enregistrez vos modifications, assurez-vous qu’il a réussi.
4.  Passez à la partie 4.

###<a name="sql-database"></a>Base de données SQL
1.  Rechercher les champs nom d’utilisateur et mot de passe et coller votre jeu nouvellement créé des informations d’identification :  
![graphic38][graphic38]
2.  Cliquez sur la commande Enregistrer et confirmez enregistrer vos modifications :  
![graphic39][graphic39]
3.  Un test de connexion démarre automatiquement lorsque vous enregistrez vos modifications, assurez-vous qu’il a réussi.  
4.  Passez à la partie 4.

##<a name="part-4-starting-your-job-from-last-stopped-time"></a>4e partie : Lancer votre tâche à partir de la dernière fois arrêté
1.  Naviguer en dehors de l’entrée/sortie :  
![graphic40][graphic40]
2.  Cliquez sur la commande Start :  
![graphic41][graphic41]
3.  Sélectionnez la dernière fois arrêté, puis cliquez sur OK :  
 ![graphic42][graphic42]
4.  Passez à la partie 5.  

##<a name="part-5-removing-the-old-set-of-credentials"></a>Partie 5 : Supprimer les anciens paramètres des informations d’identification
Cet article s’applique aux entrées/sorties suivantes :
* Stockage d’objets BLOB
* Événement Hubs
* Base de données SQL
* Stockage de table

###<a name="blob-storagetable-storage"></a>Stockage de Table/de stockage d’objets BLOB
Répétez partie 1 pour la touche d’accès rapide qui a été précédemment utilisé par votre travail pour renouveler maintenant inutilisées touche d’accès rapide.

###<a name="event-hubs"></a>Hubs d’événement
Répétez partie 1 pour la clé qui a été précédemment utilisée par votre travail pour renouveler la clé maintenant inutilisée.

###<a name="sql-database"></a>Base de données SQL
1.  Revenir à la fenêtre de requête à partir de la partie 1 étape 7, entrez la requête suivante en remplaçant < previous_login_name > par le nom d’utilisateur qui a été précédemment utilisé par votre travail :  
`DROP LOGIN <previous_login_name>`  
2.  Cliquez sur Exécuter :  
    ![graphic43][graphic43]  

Vous devez obtenir la confirmation suivante : 

    Command(s) completed successfully.

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-login-credentials-inputs-outputs/1-stream-analytics-login-credentials-inputs-outputs.png
[graphic2]: ./media/stream-analytics-login-credentials-inputs-outputs/2-stream-analytics-login-credentials-inputs-outputs.png
[graphic3]: ./media/stream-analytics-login-credentials-inputs-outputs/3-stream-analytics-login-credentials-inputs-outputs.png
[graphic4]: ./media/stream-analytics-login-credentials-inputs-outputs/4-stream-analytics-login-credentials-inputs-outputs.png
[graphic5]: ./media/stream-analytics-login-credentials-inputs-outputs/5-stream-analytics-login-credentials-inputs-outputs.png
[graphic6]: ./media/stream-analytics-login-credentials-inputs-outputs/6-stream-analytics-login-credentials-inputs-outputs.png
[graphic7]: ./media/stream-analytics-login-credentials-inputs-outputs/7-stream-analytics-login-credentials-inputs-outputs.png
[graphic8]: ./media/stream-analytics-login-credentials-inputs-outputs/8-stream-analytics-login-credentials-inputs-outputs.png
[graphic9]: ./media/stream-analytics-login-credentials-inputs-outputs/9-stream-analytics-login-credentials-inputs-outputs.png
[graphic10]: ./media/stream-analytics-login-credentials-inputs-outputs/10-stream-analytics-login-credentials-inputs-outputs.png
[graphic11]: ./media/stream-analytics-login-credentials-inputs-outputs/11-stream-analytics-login-credentials-inputs-outputs.png
[graphic12]: ./media/stream-analytics-login-credentials-inputs-outputs/12-stream-analytics-login-credentials-inputs-outputs.png
[graphic13]: ./media/stream-analytics-login-credentials-inputs-outputs/13-stream-analytics-login-credentials-inputs-outputs.png
[graphic14]: ./media/stream-analytics-login-credentials-inputs-outputs/14-stream-analytics-login-credentials-inputs-outputs.png
[graphic15]: ./media/stream-analytics-login-credentials-inputs-outputs/15-stream-analytics-login-credentials-inputs-outputs.png
[graphic16]: ./media/stream-analytics-login-credentials-inputs-outputs/16-stream-analytics-login-credentials-inputs-outputs.png
[graphic17]: ./media/stream-analytics-login-credentials-inputs-outputs/17-stream-analytics-login-credentials-inputs-outputs.png
[graphic18]: ./media/stream-analytics-login-credentials-inputs-outputs/18-stream-analytics-login-credentials-inputs-outputs.png
[graphic19]: ./media/stream-analytics-login-credentials-inputs-outputs/19-stream-analytics-login-credentials-inputs-outputs.png
[graphic20]: ./media/stream-analytics-login-credentials-inputs-outputs/20-stream-analytics-login-credentials-inputs-outputs.png
[graphic21]: ./media/stream-analytics-login-credentials-inputs-outputs/21-stream-analytics-login-credentials-inputs-outputs.png
[graphic22]: ./media/stream-analytics-login-credentials-inputs-outputs/22-stream-analytics-login-credentials-inputs-outputs.png
[graphic23]: ./media/stream-analytics-login-credentials-inputs-outputs/23-stream-analytics-login-credentials-inputs-outputs.png
[graphic24]: ./media/stream-analytics-login-credentials-inputs-outputs/24-stream-analytics-login-credentials-inputs-outputs.png
[graphic25]: ./media/stream-analytics-login-credentials-inputs-outputs/25-stream-analytics-login-credentials-inputs-outputs.png
[graphic26]: ./media/stream-analytics-login-credentials-inputs-outputs/26-stream-analytics-login-credentials-inputs-outputs.png
[graphic27]: ./media/stream-analytics-login-credentials-inputs-outputs/27-stream-analytics-login-credentials-inputs-outputs.png
[graphic28]: ./media/stream-analytics-login-credentials-inputs-outputs/28-stream-analytics-login-credentials-inputs-outputs.png
[graphic29]: ./media/stream-analytics-login-credentials-inputs-outputs/29-stream-analytics-login-credentials-inputs-outputs.png
[graphic30]: ./media/stream-analytics-login-credentials-inputs-outputs/30-stream-analytics-login-credentials-inputs-outputs.png
[graphic31]: ./media/stream-analytics-login-credentials-inputs-outputs/31-stream-analytics-login-credentials-inputs-outputs.png
[graphic32]: ./media/stream-analytics-login-credentials-inputs-outputs/32-stream-analytics-login-credentials-inputs-outputs.png
[graphic33]: ./media/stream-analytics-login-credentials-inputs-outputs/33-stream-analytics-login-credentials-inputs-outputs.png
[graphic34]: ./media/stream-analytics-login-credentials-inputs-outputs/34-stream-analytics-login-credentials-inputs-outputs.png
[graphic35]: ./media/stream-analytics-login-credentials-inputs-outputs/35-stream-analytics-login-credentials-inputs-outputs.png
[graphic36]: ./media/stream-analytics-login-credentials-inputs-outputs/36-stream-analytics-login-credentials-inputs-outputs.png
[graphic37]: ./media/stream-analytics-login-credentials-inputs-outputs/37-stream-analytics-login-credentials-inputs-outputs.png
[graphic38]: ./media/stream-analytics-login-credentials-inputs-outputs/38-stream-analytics-login-credentials-inputs-outputs.png
[graphic39]: ./media/stream-analytics-login-credentials-inputs-outputs/39-stream-analytics-login-credentials-inputs-outputs.png
[graphic40]: ./media/stream-analytics-login-credentials-inputs-outputs/40-stream-analytics-login-credentials-inputs-outputs.png
[graphic41]: ./media/stream-analytics-login-credentials-inputs-outputs/41-stream-analytics-login-credentials-inputs-outputs.png
[graphic42]: ./media/stream-analytics-login-credentials-inputs-outputs/42-stream-analytics-login-credentials-inputs-outputs.png
[graphic43]: ./media/stream-analytics-login-credentials-inputs-outputs/43-stream-analytics-login-credentials-inputs-outputs.png
 
