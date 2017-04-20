En raison de développement en cours, la version SDK Android installée dans Studio Android ne peut pas correspondre la version dans le code. Le Kit de développement Android référencées dans ce didacticiel est version 23 plus tard au moment de la rédaction. Le numéro de version peut augmenter comme apparaissent de nouvelles versions du Kit de développement, nous vous recommandons d’utiliser la dernière version disponible.

Deux problèmes de conflit de version sont :

1. Lorsque vous créez ou recréez le projet, vous pouvez obtenir Gradle des messages d’erreur comme «**Impossible de trouver la cible Google Inc.:Google APIs:n**».

2. Objets Android standards dans le code qui doit résoudre basés sur `import` instructions peuvent générer des messages d’erreur.

Si des options suivantes s’affichent, la version du Kit de développement Android installés dans Studio Android ne peut pas correspondre la cible du Kit de développement du projet téléchargé.  Pour vérifier la version, apportez les modifications suivantes :


1. Dans Studio Android, cliquez sur **Outils** => **Android** => **Manager SDK**. Si vous n’avez pas installé la dernière version de la plate-forme SDK, puis cliquez sur pour l’installer. Notez le numéro de version.

2. Dans l’onglet Explorateur de projets, sous **Scripts Gradle**, ouvrez le fichier **build.gradle (modeule : application)**. Assurez-vous que la **compileSdkVersion** et **buildToolsVersion** sont définies vers la dernière version SDK installée. Les balises peuvent ressembler à ceci :
 
            compileSdkVersion 'Google Inc.:Google APIs:23'
            buildToolsVersion "23.0.2"
    
3. Dans l’Explorateur de projets Studio Android droit sur le nœud de projet, sélectionnez **Propriétés**, puis dans la colonne de gauche **Android**. Assurez-vous que la **Cible Build de projet** est défini sur la même version SDK que le **targetSdkVersion**.

4. Dans Studio Android, le fichier manifeste n’est plus permet de spécifier la cible SDK et la version minimale de SDK, contrairement à l’instar de Eclipse.
