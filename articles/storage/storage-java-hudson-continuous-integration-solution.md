<properties
    pageTitle="Comment utiliser Hudson avec le stockage Blob | Microsoft Azure"
    description="Décrit comment utiliser Hudson avec le stockage Blob Azure comme un référentiel pour les objets de génération."
    services="storage"
    documentationCenter="java"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/18/2016" 
    ms.author="dinesh"/>

# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>L’utilisation du stockage Azure avec une solution d’intégration continue Hudson

## <a name="overview"></a>Vue d’ensemble

Les informations suivantes montre comment utiliser stockage Blob sous forme d’un référentiel de générer les objets créés par une solution d’intégration continue Hudson (CI) ou en tant que source de fichiers téléchargeables à utiliser dans un processus de génération. L’un des scénarios où vous le feriez utile est lorsque vous êtes codage dans un environnement de développement agile (à l’aide de Java ou autres langues), générations en cours d’exécution en fonction de l’intégration continue, et vous devez un référentiel pour vos objets génération, afin que vous pouvez, par exemple, partagez-les avec d’autres membres de l’organisation, vos clients, ou mettre à jour une archive.  Un autre scénario est lorsque votre travail build lui-même requiert d’autres fichiers, par exemple, les dépendances à télécharger dans le cadre de l’entrée de construction.

Dans ce didacticiel vous utiliserez le plug-in stockage Azure pour CI Hudson mis à disposition par Microsoft.

## <a name="introduction-to-hudson"></a>Introduction aux Hudson ##

Hudson permet l’intégration continue d’un projet de logiciel en permettant aux développeurs d’intégrer facilement leurs modifications du code et ont générations produits automatiquement et fréquemment, ce qui augmente la productivité des développeurs. Versions préliminaires sont créées et objets de build peuvent être téléchargés sur différents référentiels. Cet article explique comment utiliser le stockage d’objets Blob Azure comme référentiel des objets de génération. Il apparaît également comment télécharger des dépendances de stockage d’objets Blob Azure.

Vous trouverez plus d’informations sur Hudson à [Répondre aux Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>Avantages de l’utilisation du service d’objets Blob ##

Avantages de l’utilisation du service Blob pour héberger votre objets de génération de développement agile :

- Disponibilité de votre build objets et/ou dépendances téléchargeables.
- Performances lors de votre solution Hudson CI télécharge vos objets générer.
- Performances lors de vos clients et partenaires téléchargent vos objets générer.
- Contrôler les stratégies d’accès utilisateur avec un choix entre l’accès anonyme, l’accès partagé d’expiration signature accès, privé accès, etc..

## <a name="prerequisites"></a>Conditions préalables ##

Vous aurez besoin pour utiliser le service Blob avec votre solution Hudson CI :

- Une solution d’intégration continue Hudson.

    Si vous n’avez pas une solution Hudson CI, vous pouvez exécuter une solution Hudson CI utilisant la méthode suivante :

    1. Sur un ordinateur équipé pour Java, téléchargez la guerre Hudson à partir de <http://hudson-ci.org/>.
    2. À l’invite de commandes qui s’ouvre dans le dossier qui contient la guerre Hudson, exécutez la guerre Hudson. Par exemple, si vous avez téléchargé version 3.1.2 :

        `java -jar hudson-3.1.2.war`

    3. Dans votre navigateur, ouvrez `http://localhost:8080/`. Le tableau de bord Hudson s’ouvre.

    4. Lors de la première utilisation d’Hudson, terminer l’installation initiale sur `http://localhost:8080/`.

    5. Après avoir terminé la configuration initiale, annuler l’instance en cours d’exécution de la guerre Hudson, redémarrez la guerre Hudson et ouvrez à nouveau le tableau de bord Hudson, `http://localhost:8080/`, que vous utiliserez pour installer et configurer le plug-in stockage Azure.

        Pendant une solution Hudson CI standard est configurée pour exécuter en tant que service, la guerre Hudson en cours d’exécution sur la ligne de commande sera suffisante pour ce didacticiel.

- Un compte Azure. Vous pouvez vous inscrire pour un compte Azure en <http://www.azure.com>.

- Un compte de stockage Azure. Si vous n’avez pas un compte de stockage, vous pouvez créer un à l’aide de la procédure à [créer un compte de stockage](storage-create-storage-account.md#create-a-storage-account).

- Vous familiariser avec la solution Hudson CI est recommandée mais pas obligatoire, comme le contenu suivant utilisera un exemple de base pour afficher les étapes à suivre lorsque vous utilisez le service Blob comme un référentiel pour Hudson CI générer des objets.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Comment utiliser le service Blob avec Hudson CI ##

Pour utiliser le service Blob avec Hudson, vous devez installer le plug-in stockage Azure et configurer le plug-in pour utiliser votre compte de stockage puis créez une action post-build qui consiste à télécharger vos objets build à votre compte de stockage. Ces étapes sont décrites dans les sections suivantes.

## <a name="how-to-install-the-azure-storage-plugin"></a>Comment faire pour installer le plug-in stockage Azure ##

1. Dans le tableau de bord Hudson, cliquez sur **Gérer Hudson**.
2. Dans la page **Gérer les Hudson** , cliquez sur **Gérer les plug-ins**.
3. Cliquez sur l’onglet **disponible** .
4. Cliquez sur **d’autres personnes**.
5. Dans la section **Télépartageurs objet** , sélectionnez le **plug-in Microsoft Azure stockage**.
6. Cliquez sur **installer**.
7. Une fois l’installation terminée, redémarrez Hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Comment faire pour configurer le plug-in de stockage Azure pour utiliser votre compte de stockage ##

1. Dans le tableau de bord Hudson, cliquez sur **Gérer Hudson**.
2. Dans la page **Gérer les Hudson** , cliquez sur **Configuration du système**.
3. Dans la section **Configuration du compte Microsoft Azure stockage** :

    un. Entrez votre nom de compte de stockage, que vous pouvez obtenir à partir du [Portail Azure](https://portal.azure.com).

    b. Entrez votre clé de compte de stockage, qui peut également être obtenu à partir du [Portail Azure](https://portal.azure.com).

    c. Utilisez la valeur par défaut de **l’URL du point de terminaison de Service Blob** si vous utilisez le nuage public Azure. Si vous utilisez un nuage Azure différent, utilisez le point de terminaison comme indiqué dans le [Portail Azure](https://portal.azure.com) pour votre compte de stockage.

    d. Cliquez sur **valider les informations d’identification de stockage** pour valider votre compte de stockage.

    e. [Facultatif] Si vous avez des comptes d’espace de stockage supplémentaire que vous souhaitez apporter disponibles pour votre CI Hudson, cliquez sur **ajouter d’autres comptes de stockage**.

    f. Cliquez sur **Enregistrer** pour enregistrer vos paramètres.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Comment créer une action post-build qui consiste à télécharger vos objets build à votre compte de stockage ##

À des fins de jeu d’instructions, tout d’abord nous devez créer une tâche qui doit créer plusieurs fichiers, puis ajouter dans l’action après génération pour charger des fichiers à votre compte de stockage.

1. Dans le tableau de bord Hudson, cliquez sur **Nouvelle tâche**.
2. Nommez la tâche **MyJob**et cliquez sur **créer une tâche de logiciels de forme libre**, puis cliquez sur **OK**.
3. Dans la section **Générer** de la configuration du travail, cliquez sur **Ajouter étape de génération** et choisissez **exécuter Windows par lots commande**.
4. **Commande**, utilisez les commandes suivantes :

        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt

5. Dans la section **Actions après génération** de la configuration du travail, cliquez sur **télécharger des objets à Microsoft Azure Blob storage**.
6. Pour **Nom de compte de stockage**, sélectionnez le compte de stockage à utiliser.
7. **Nom du conteneur**, spécifiez le nom du conteneur. (Le conteneur est créé si elle n’existe pas déjà lorsque les objets de génération sont téléchargés.) Vous pouvez utiliser des variables d’environnement, alors entrez **${nom_du_travail}** pour que cet exemple en tant que le nom du conteneur.

    **Conseil**

    Sous la **commande** section où vous avez entré un script pour **exécuter Windows par lots commande** est un lien vers les variables d’environnement reconnu par Hudson. Cliquez sur ce lien pour découvrir les noms des variables d’environnement et les descriptions. Notez que les variables d’environnement qui contiennent des caractères spéciaux, tels que la variable d’environnement **BUILD_URL** ne sont pas autorisées en tant que nom du conteneur ou chemin d’accès virtuel courantes.

8. Dans cet exemple, cliquez sur **Publier le nouveau conteneur par défaut** . (Si vous voulez utiliser un conteneur privé, vous devez créer une signature accès partagé pour autoriser l’accès. Il s’agit dépasse le cadre de cet article. Vous pouvez en savoir plus sur les signatures accès partagé en [à l’aide de l’accès des Signatures (sa partagées)](storage-dotnet-shared-access-signature-part-1.md).)
9. [Facultatif] Cliquez sur **Nettoyer conteneur avant de le télécharger** si vous souhaitez que le conteneur à effacer du contenu avant que les objets de génération sont téléchargées (laissez-la désactivée si vous ne souhaitez pas effacer le contenu du conteneur).
10. Pour **Liste des objets à télécharger**, entrez * *texte /*.txt**.
11. Pour **courantes chemin d’accès virtuel pour les objets téléchargés**, entrez **${créer\_ID} / ${créer\_nombre}**.
12. Cliquez sur **Enregistrer** pour enregistrer vos paramètres.
13. Dans le tableau de bord Hudson, cliquez sur **Créer maintenant** pour exécuter **MyJob**. Examinez la sortie console état. Messages d’état pour le stockage Azure sont être inclus dans le résultat de la console lors de l’action après génération commence à télécharger les objets de génération.
14. En cas de réussite de la tâche, vous pouvez examiner les objets de build en ouvrant le blob public.

    un. Connectez-vous au [portail Azure](https://portal.azure.com).

    b. Cliquez sur **espace de stockage**.

    c. Cliquez sur le nom du compte de stockage que vous avez utilisé pour Hudson.

    d. Cliquez sur **les conteneurs**.

    e. Cliquez sur le conteneur nommé **myjob**, qui est la version en minuscules du nom de la tâche que vous avez affecté lorsque vous avez créé la tâche Hudson. Conteneur et les noms d’objets blob sont en minuscule (et qui respecte la casse) dans le stockage Azure. Dans la liste des objets BLOB pour le conteneur nommé **myjob** vous devriez voir **hello.txt** et **date.txt**. Copiez l’URL pour chacun de ces éléments et ouvrez-le dans votre navigateur. Vous verrez le fichier texte qui a été téléchargé comme un objet de génération.

Qu’une action post-build qui télécharge des objets au stockage d’objets Blob Azure peut être créée par tâche. Notez que l’action après génération unique pour télécharger des objets sur stockage d’objets Blob Azure peut spécifier différents fichiers (y compris des caractères génériques) et les chemins d’accès aux fichiers au sein de **Liste des objets à télécharger** à l’aide d’un point virgule comme séparateur. Par exemple, si votre génération Hudson produit fichiersJAR et fichiers TXT dans le dossier de **build** de votre espace de travail, et que vous voulez télécharger les deux, à stockage d’objets Blob Azure, utilisez ce qui suit la valeur de la **Liste des objets à télécharger** : **créer /\*.jar ; génération /\*.txt**. Vous pouvez également utiliser double deux-points syntaxe pour spécifier un chemin d’accès à utiliser dans le nom d’objets blob. Par exemple, si vous souhaitez que les fichiers JAR à obtenir téléchargé à l’aide des **fichiers binaires** dans le chemin d’accès blob et les fichiers TXT à obtenir téléchargé à l’aide des **notifications** dans le chemin d’accès blob, utilisez ce qui suit la valeur de la **Liste des objets à télécharger** : **créer /\*. jar::binaries ; génération /\*. txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Comment créer une étape de génération qui télécharge à partir du stockage d’objets Blob Azure ##

Les étapes suivantes montrent comment configurer une étape de génération pour télécharger les éléments à partir du stockage Blob Azure. Elle peut être utile si vous voulez inclure des éléments dans votre build, par exemple, bocaux conservées dans le stockage Blob Azure.

1. Dans la section **Générer** de la configuration du travail, cliquez sur **Ajouter générer étape** et choisissez **télécharger à partir du stockage Blob Azure**.
2. Pour **nom de compte de stockage**, sélectionnez le compte de stockage à utiliser.
3. Pour **nom du conteneur**, spécifiez le nom du conteneur qui comporte les objets BLOB que vous souhaitez télécharger. Vous pouvez utiliser des variables d’environnement.
4. Pour **nom Blob**, spécifiez le nom d’objets blob. Vous pouvez utiliser des variables d’environnement. En outre, vous pouvez utiliser un astérisque comme caractère générique une fois que vous spécifiez la lettre initiale du nom blob. Par exemple, **projet\* ** spécifierez tous les objets BLOB qui commencent par **project**.
5. [Facultatif] Pour **Télécharger le chemin d’accès**, spécifiez le chemin d’accès sur l’ordinateur Hudson où vous souhaitez télécharger des fichiers à partir du stockage Blob Azure. Variables d’environnement peuvent également être utilisés. (Si vous ne fournissez pas une valeur pour **Télécharger le chemin d’accès**, les fichiers à partir du stockage Blob Azure sont téléchargés vers espace de travail du projet.)

Si vous avez d’autres éléments que vous voulez télécharger à partir du stockage Blob Azure, vous pouvez créer des étapes de génération supplémentaires.

Une fois que vous exécutez une version, vous pouvez vérifier la sortie de console de l’historique de génération ou examiner l’emplacement de téléchargement, pour voir si les objets BLOB que vous attendiez ont été téléchargés avec succès.

## <a name="components-used-by-the-blob-service"></a>Composants utilisés par le service d’objets Blob ##

Les éléments suivants fournit une vue d’ensemble des composants du service Blob.

- **Compte de stockage**: tout accès au stockage Azure s’effectue via un compte de stockage. Il s’agit le plus haut niveau de l’espace de noms pour accéder à des objets BLOB. Un compte peut contenir un nombre illimité de conteneurs, tant que leur taille totale est sous 100 To.
- **Conteneur**: un conteneur fournit un regroupement d’un ensemble d’objets BLOB. Tous les objets BLOB doivent être placé dans un conteneur. Un compte peut contenir un nombre illimité de conteneurs. Un conteneur peut stocker un nombre illimité d’objets BLOB.
- **Objets BLOB**: un fichier de n’importe quel type et la taille. Il existe deux types d’objets BLOB pouvant être stockés dans le stockage Azure : objets BLOB bloc et de page. La plupart des fichiers sont des objets BLOB bloc. Un blob bloc unique peut être jusqu'à 200 Go. Ce didacticiel utilise des objets BLOB bloc. Page des objets BLOB, un autre type d’objets blob, peuvent être jusqu'à 1 to taille et sont plus efficace lorsque les plages d’octets dans un fichier sont modifiés fréquemment. Pour plus d’informations sur les objets BLOB, voir [présentation bloc BLOB, ajouter des objets BLOB et des objets BLOB de Page](http://msdn.microsoft.com/library/azure/ee691964.aspx).
- **Format de l’URL**: objets BLOB sont dédié au format URL suivant :

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`

    (Le format ci-dessus s’applique au nuage public Azure. Si vous utilisez un nuage Azure différent, utilisez le point de terminaison au sein du [Portail Azure](https://portal.azure.com) pour déterminer votre point de terminaison URL.)

    Dans le format ci-dessus, `storageaccount` correspond au nom de votre compte de stockage, `container_name` correspond au nom de votre conteneur et `blob_name` correspond au nom de votre blob, respectivement. Dans le nom du conteneur, vous pouvez avoir plusieurs chemins d’accès, séparés par une barre oblique **/**. Le nom du conteneur exemple dans ce didacticiel a été **MyJob**, et **${créer\_ID} / ${créer\_nombre}** a été utilisé pour le chemin virtuel courantes, ce qui entraîne le blob présentant une URL de la forme suivante :

    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Étapes suivantes

- [Répondre aux Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
- [Stockage Azure SDK pour Java](https://github.com/azure/azure-storage-java)
- [Référence du Kit de développement logiciel Client stockage Azure](http://dl.windowsazure.com/storage/javadoc/)
- [Services de stockage Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx )
- [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/)

Pour plus d’informations, voir également le [Centre de développement Java](https://azure.microsoft.com/develop/java/).