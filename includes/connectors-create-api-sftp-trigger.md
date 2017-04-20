Nous allons ajouter un déclencheur.

1. Entrez *sftp* dans la zone de recherche dans le Concepteur d’applications logique, puis sélectionnez le déclencheur **SFTP - lorsqu’un fichier est ajouté ou modifié**   
![Image de déclenchement SFTP 1](./media/connectors-create-api-sftp/trigger-1.png)  
- Ouvre le contrôle **lorsqu’un fichier est ajouté ou modifié**  
![Image de déclenchement SFTP 2](./media/connectors-create-api-sftp/trigger-2.png)  
- Sélectionnez le bouton **...** situé sur le côté droit du contrôle. Cette action ouvre le contrôle de sélecteur de dossier  
![Image de déclenchement SFTP 3](./media/connectors-create-api-sftp/action-1.png)  
- Sélectionnez le **SFTP** pour sélectionner le dossier racine que le dossier pour surveiller les fichiers nouveaux ou modifiés. Notez que le dossier racine est maintenant affiché dans le contrôle de **dossier** .  
![Image de déclenchement SFTP 4](./media/connectors-create-api-sftp/action-2.png)   

À ce stade, votre application logique a été configurée avec un déclencheur qui va commencer une exécution des autres déclencheurs et actions dans le flux de travail lorsqu’un fichier est modifié ou créé dans le dossier SFTP spécifique. 

>[AZURE.NOTE]Pour une application logique à être opérationnel, elle doit contenir au moins un déclencheur et une action. Suivez les étapes décrites dans la section suivante pour ajouter une action.  