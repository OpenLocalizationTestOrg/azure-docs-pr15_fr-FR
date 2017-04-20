1. Dans **L’Explorateur de solutions**Visual Studio, droit sur le projet et sélectionnez **Ajouter > prise en charge Docker** dans le menu contextuel.

    ![Ajouter le menu contextuel d’une prise en charge Docker](media/vs-azure-tools-docker-add-docker-support/docker-support-context-menu.png)

1. Ajouter un support Docker dans une base de ASP.NET projet web entraîne l’ajout de plusieurs fichiers liés à la Docker qui sont ajoutés au projet, y compris les fichiers composer Docker, de scripts Windows PowerShell déploiement et fichiers de propriété Docker. 

    ![Fichiers docker ajoutés au projet](media/vs-azure-tools-docker-add-docker-support/docker-files-added.png)
    
> [AZURE.NOTE]Si vous utilisez le [Docker pour Windows la version bêta](https://beta.docker.com), ouvrez Properties\Docker.props, supprimez la valeur par défaut et redémarrer Visual Studio pour la valeur soient prises en compte.
> 
> ```
> <DockerMachineName Condition="'$(DockerMachineName)'=='' "></DockerMachineName>
> ```
