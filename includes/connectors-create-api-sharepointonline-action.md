Maintenant que vous avez ajouté un déclencheur, il est temps de faire quelque chose intéressantes avec les données qui sont générées par le déclencheur. Suivez ces étapes pour ajouter l’action **SharePoint Online : créer le fichier** . Cette action crée un fichier dans SharePoint Online chaque fois que le nouveau déclencheur d’élément s’exécute. 

Pour configurer cette action, vous devrez fournir les informations suivantes. Comme vous fournissez ces informations, vous remarquerez qu’il est facile d’utilisation données générées par le déclencheur comme entrée pour certaines des propriétés pour le nouveau fichier :

|Créer des propriétés de fichier|Description|
|---|---|
|URL du site|Il s’agit de l’URL du site SharePoint Online dans lequel vous voulez créer le nouveau fichier. Sélectionnez le site dans la liste affichée.|
|Chemin d’accès de dossier|C’est le dossier (à l’URL du Site sélectionné à l’étape précédente) où le nouveau fichier sera placé. Recherchez et sélectionnez le dossier.|
|Nom de fichier|Il s’agit du nom du fichier en cours de création.|
|Contenu du fichier|Le contenu qui est écrites dans le fichier.|

1. Sélectionnez **+ nouvelle étape** pour ajouter l’action.  
![Image d’action en ligne SharePoint 1](./media/connectors-create-api-sharepointonline/action-1.png)  
- Sélectionnez le lien **Ajouter une action** . Ce bouton ouvre la zone de recherche qui permet de rechercher d’une action que vous voulez prendre. Dans cet exemple, les actions de SharePoint sont utiles.    
![Image d’action en ligne SharePoint 2](./media/connectors-create-api-sharepointonline/action-2.png)    
- Entrez *sharepoint* pour rechercher des actions associées à SharePoint.
- Sélectionnez **SharePoint Online : créer le fichier** en tant que l’action à effectuer.   **Remarque**: vous devrez autoriser votre application logique pour accéder à votre compte SharePoint si vous n’avez pas précédemment créé une connexion à SharePoint Online.    
![Image d’action en ligne SharePoint 3](./media/connectors-create-api-sharepointonline/action-3.png)    
- Le contrôle de **créer un fichier** s’ouvre.   
![Image d’action en ligne SharePoint 4](./media/connectors-create-api-sharepointonline/action-4.png)     
- Sélectionnez **L’URL du Site** , puis naviguez jusqu’au site à l’endroit où vous voulez créer le fichier.     
![Image d’action en ligne SharePoint 5](./media/connectors-create-api-sharepointonline/action-5.png)  
- Sélectionnez le **chemin du dossier** et recherchez le dossier où le nouveau fichier sera placé.  
![Image d’action en ligne SharePoint 6](./media/connectors-create-api-sharepointonline/action-6.png)  
- Sélectionnez le contrôle de **nom de fichier** , entrez le nom du fichier que vous voulez créer. Dans ce cas, vous pouvez soit entrer le nom du fichier directement ou vous pouvez utiliser une des propriétés à partir du déclencheur que vous avez créé précédemment. Pour cela, en sélectionnant Propriétés dans la liste des **sorties à partir de la création d’un nouvel élément**. Cette liste est uniquement affichage après avoir sélectionné le contrôle de **nom de fichier** . Dans cette procédure pas à pas, j’ai sélectionné ID (l’ID de nouvel élément de liste) en tant que le nom du fichier créé par l’action **SharePoint Online : créer le fichier** .    
![Image d’action en ligne SharePoint 7](./media/connectors-create-api-sharepointonline/action-7.png)  
- Sélectionnez le contrôle de **contenu du fichier** , entrez le contenu qui est écrites dans le fichier qui sera créé. Pour le contenu du fichier, notez que vous pouvez utiliser les propriétés à partir du déclencheur que vous avez créé précédemment. Sélectionnez les propriétés de la liste affichée. Par ailleurs, vous pouvez entrer le **contenu d’un fichier** texte directement dans le contrôle. Dans cet exemple, j’ai sélectionné certaines propriétés et ajouter des espaces et des traits d’union entre chaque propriété.        
![Image d’action en ligne SharePoint 8](./media/connectors-create-api-sharepointonline/action-8.png)  
- Enregistrer les modifications dans votre flux de travail  
- Félicitations, vous avez à présent une application entièrement fonctionnelle logique qui se déclenche lorsqu’un nouvel élément est ajouté à une liste SharePoint Online. L’application crée un fichier, en utilisant certaines des propriétés de nouvel élément de liste.  Vous pouvez maintenant le tester en créant un nouvel élément dans la liste SharePoint. 
