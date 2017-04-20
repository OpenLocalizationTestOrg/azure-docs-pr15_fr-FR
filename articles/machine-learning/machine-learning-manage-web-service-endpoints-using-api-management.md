<properties
    pageTitle="Découvrez comment gérer les services web AzureML à l’aide de la gestion des API | Microsoft Azure"
    description="Guide montrant comment gérer les services web AzureML à l’aide de gestion de l’API."
    keywords="apprentissage, gestion des api de l’ordinateur"
    services="machine-learning"
    documentationCenter=""
    authors="roalexan"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="roalexan" />


# <a name="learn-how-to-manage-azureml-web-services-using-api-management"></a>Découvrez comment gérer les services web AzureML à l’aide de gestion de l’API

##<a name="overview"></a>Vue d’ensemble

Ce guide vous montre comment commencer rapidement à l’aide de gestion de l’API pour gérer vos services web AzureML.

##<a name="what-is-azure-api-management"></a>Quelle est la gestion des API Azure ?

Gestion des API Azure est un service Azure qui vous permet de gérer vos points de terminaison API REST en définissant l’accès des utilisateurs, la limitation de l’utilisation et l’analyse de tableau de bord. Cliquez [ici](https://azure.microsoft.com/services/api-management/) pour plus d’informations sur la gestion des API Azure. Cliquez [ici](../api-management/api-management-get-started.md) pour un guide commencer avec la gestion des API Azure. Ce guide d’autres, ce guide est basé sur, couvre autres rubriques, y compris les configurations de la notification, la couche tarifs, la gestion des réponses, l’authentification des utilisateurs, un tableau de bord l’utilisation, les abonnements pour les développeurs et les produits.

##<a name="what-is-azureml"></a>Quelles sont les AzureML ?

AzureML est un service d’Azure pour apprentissage de l’ordinateur qui vous permet de créer, déployer et partager des solutions avancées analytique facilement. Cliquez [ici](https://azure.microsoft.com/services/machine-learning/) pour plus d’informations sur AzureML.

##<a name="prerequisites"></a>Conditions préalables

Pour exécuter ce guide, vous devez :

* Un compte Azure. Si vous n’avez pas un compte Azure, cliquez [ici](https://azure.microsoft.com/pricing/free-trial/) pour plus d’informations sur la création d’un compte d’évaluation gratuit.
* Un compte AzureML. Si vous n’avez pas un compte AzureML, cliquez [ici](https://studio.azureml.net/) pour plus d’informations sur la création d’un compte d’évaluation gratuit.
* L’espace de travail, le service et api_key pour une expérience AzureML déployée comme un service web. Cliquez [ici](machine-learning-create-experiment.md) pour plus d’informations sur la création d’une expérience AzureML. Cliquez [ici](machine-learning-publish-a-machine-learning-web-service.md) pour plus d’informations sur le déploiement d’une expérience AzureML comme un service web. Vous pouvez aussi appendice A compose des instructions pour savoir comment créer et tester une expérience AzureML simple et déployer comme un service web.

##<a name="create-an-api-management-instance"></a>Créer une instance de gestion de l’API

Voici la procédure d’utilisation de gestion de l’API pour gérer votre service web AzureML. Tout d’abord créer une instance de service. Connectez-vous au [Portail classique](https://manage.windowsazure.com/) et cliquez sur **Nouveau** > **Application Services** > **Gestion des API** > **créer**.

![créer une instance](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-instance.png)

Spécifiez une **URL**unique. Ce guide utilise **demoazureml** : vous devez choisir un autre nom. Sélectionnez **l’abonnement** et **région** souhaitée pour votre instance de service. Après avoir effectué vos sélections, cliquez sur le bouton suivant.

![créer-service-1](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-1.png)

Spécifier une valeur pour le **Nom de l’organisation**. Ce guide utilise **demoazureml** : vous devez choisir un autre nom. Entrez votre adresse de messagerie dans le champ **e-mail administrateur** . Cette adresse de messagerie est utilisée pour les notifications à partir du système de gestion de l’API.

![créer-service-2](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-2.png)

Cliquez sur la case à cocher pour créer votre instance de service. *Il faut jusqu'à 30 minutes pour un nouveau service à créer*.

##<a name="create-the-api"></a>Créer l’API

Une fois que l’instance de service est créé, l’étape suivante consiste à créer l’API. Une API se compose d’un ensemble d’opérations qui peut être appelé à partir d’une application cliente. Opérations de l’API sont traitées aux services web existants. Ce guide crée API comme proxy pour les services web AzureML RRS et BES existants.

API est créés et configurés à partir du portail de publisher API, qui est accessible via le portail classique Azure. Pour atteindre le portail publisher, sélectionnez l’instance du service.

![Sélectionnez instance de service](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-service-instance.png)

Cliquez sur **Gérer** dans le portail classique Azure pour votre service de gestion de l’API.

![gestion du service](./media/machine-learning-manage-web-service-endpoints-using-api-management/manage-service.png)

Cliquez sur **API** dans le menu de **Gestion de l’API** sur la gauche, puis cliquez sur **Ajouter des API**.

![menu de gestion de l’API](./media/machine-learning-manage-web-service-endpoints-using-api-management/api-management-menu.png)

Tapez le **nom de l’API Web** **AzureML démo API** . Tapez l' **URL du service Web** **https://ussouthcentral.services.azureml.net** . Tapez **azureml démo** le **suffixe dans l’URL de l’API Web**. Cochez la case **HTTPS** par le schéma **d’URL de l’API Web** . Sélectionnez **Starter** sous forme de **produits**. Lorsque vous avez terminé, cliquez sur **Enregistrer** pour créer l’API.

![Ajouter-nouvelle-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-new-api.png)

##<a name="add-the-operations"></a>Ajouter les opérations

Cliquez sur l' **opération d’ajout** pour ajouter des opérations à cette API.

![opération Ajouter](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-operation.png)

La fenêtre **nouvelle opération** s’affichera et l’onglet **Signature** est sélectionnée par défaut.

##<a name="add-rrs-operation"></a>Ajouter des enregistrements de ressources opération

Tout d’abord créer une opération pour le service AzureML RRS. Sélectionnez **Publier** en tant que le **verbe HTTP**. Type de **/services/ /workspaces/ {espace de travail} {service} / execute?api version = {apiversion} & détails = {détails}** en tant que le **modèle de l’URL**. En tant que le **nom complet**, tapez **Exécuter des enregistrements de ressources** .

![ajouter des enregistrements de ressources-opération-signatures](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

Cliquez sur **réponses** > **Ajouter** à gauche et sélectionnez **200 OK**. Cliquez sur **Enregistrer** pour enregistrer cette opération.

![ajouter des enregistrements de ressources-opération-réponse](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

##<a name="add-bes-operations"></a>Ajouter des opérations BES

Captures d’écran ne sont pas inclus pour les opérations BES qu’ils sont très semblables à ceux pour l’ajout de l’opération d’enregistrements.

###<a name="submit-but-not-start-a-batch-execution-job"></a>Soumettre (mais pas démarrer) un travail de l’exécution par lots

Cliquez sur **l’opération d’ajout** pour ajouter l’opération BES AzureML à l’API. Sélectionnez **Publier** pour le **verbe HTTP**. Type de **/services/ /workspaces/ {espace de travail} {service} / jobs?api version = {apiversion}** pour le **modèle de l’URL**. Tapez **BES soumettre** correspondant au **nom d’affichage**. Cliquez sur **réponses** > **Ajouter** à gauche et sélectionnez **200 OK**. Cliquez sur **Enregistrer** pour enregistrer cette opération.

###<a name="start-a-batch-execution-job"></a>Démarrage d’une tâche de l’exécution par lots

Cliquez sur **l’opération d’ajout** pour ajouter l’opération BES AzureML à l’API. Sélectionnez **Publier** pour le **verbe HTTP**. Type de **/jobs/ /services/ {service} /workspaces/ {espace de travail} {identificateur} / start?api version = {apiversion}** pour le **modèle de l’URL**. Tapez **BES Start** correspondant au **nom d’affichage**. Cliquez sur **réponses** > **Ajouter** à gauche et sélectionnez **200 OK**. Cliquez sur **Enregistrer** pour enregistrer cette opération.

###<a name="get-the-status-or-result-of-a-batch-execution-job"></a>Obtenir le résultat d’une tâche de l’exécution du lot ou l’état

Cliquez sur **l’opération d’ajout** pour ajouter l’opération BES AzureML à l’API. Sélectionnez **Insérer** pour le **verbe HTTP**. Type de **?api version /workspaces/ {espace de travail} /services/ {service} /jobs/ {identificateur} = {apiversion}** pour le **modèle de l’URL**. Tapez **BES Status** du **nom d’affichage**. Cliquez sur **réponses** > **Ajouter** à gauche et sélectionnez **200 OK**. Cliquez sur **Enregistrer** pour enregistrer cette opération.

###<a name="delete-a-batch-execution-job"></a>Supprimer une tâche de l’exécution par lots

Cliquez sur **l’opération d’ajout** pour ajouter l’opération BES AzureML à l’API. Sélectionnez **Supprimer** pour le **verbe HTTP**. Type de **?api version /workspaces/ {espace de travail} /services/ {service} /jobs/ {identificateur} = {apiversion}** pour le **modèle de l’URL**. Tapez **BES supprimer** pour le **nom d’affichage**. Cliquez sur **réponses** > **Ajouter** à gauche et sélectionnez **200 OK**. Cliquez sur **Enregistrer** pour enregistrer cette opération.

##<a name="call-an-operation-from-the-developer-portal"></a>Appeler une opération à partir du portail pour les développeurs

Opérations peuvent être appelées directement à partir du portail pour les développeurs, qui offre un moyen pratique pour afficher et tester les opérations d’une API. Dans cette étape guide vous appelle la méthode **Exécuter des enregistrements de ressources** qui a été ajoutée à l' **API de démonstration AzureML**. Cliquez sur **le portail développeur** dans le menu en haut à droite du portail classique.

![portail pour les développeurs](./media/machine-learning-manage-web-service-endpoints-using-api-management/developer-portal.png)

Cliquez sur **API** dans le menu supérieur, puis cliquez sur **AzureML démo API** pour afficher les opérations disponibles.

![api demoazureml](./media/machine-learning-manage-web-service-endpoints-using-api-management/demoazureml-api.png)

Sélectionnez **Enregistrements exécuter** l’opération. Cliquez sur **essayer**.

![Essayez informatique](./media/machine-learning-manage-web-service-endpoints-using-api-management/try-it.png)

Pour les paramètres de demande, tapez votre **espace de travail**, **service**, **2.0** pour **apiversion**et **true** pour obtenir des **Détails**. Vous pouvez trouver votre **espace de travail** et le **service** dans le tableau de bord du service web AzureML (voir **Test du service web** dans l’annexe A).

Pour les en-têtes de requête, cliquez sur **Ajouter un en-tête** et tapez le **Type de contenu** et d' **application/json**, puis cliquez sur **Ajouter un en-tête** et tapez **l’autorisation** et **PORTEUR <YOUR AZUREML SERVICE API-KEY> **. Vous pouvez trouver votre **clé de l’api** dans le tableau de bord du service web AzureML (voir **Test du service web** dans l’annexe A).

Type de **{« Entrées » : {« input1 » : {« ColumnNames » : [« Col2 »], « valeurs » : [[« C’est une bonne journée »]]}}, « GlobalParameters » : {}}** pour le corps de la requête.

![api de démonstration azureml](./media/machine-learning-manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

Cliquez sur **Envoyer**.

![Envoyer](./media/machine-learning-manage-web-service-endpoints-using-api-management/send.png)

Une fois qu’une opération est appelée, le portail de développement affiche l' **URL demandée** à partir du service de données principale, l' **état de la réponse**, les **en-têtes de réponse**et le **contenu de la réponse**.

![état de la réponse](./media/machine-learning-manage-web-service-endpoints-using-api-management/response-status.png)

##<a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Appendice A - création et test d’un simple AzureML du service web

###<a name="creating-the-experiment"></a>Création de l’expérience

Voici les étapes de création d’une expérience AzureML simple et déploiement comme un service web. Cette accepte de service web en entrée une colonne de texte arbitraire et renvoie un ensemble de fonctionnalités représentée sous forme d’entiers. Par exemple :

Texte | Texte haché
--- | ---
Il s’agit d’une bonne journée | 1 1 2 2 0 2 0 1

Tout d’abord, à l’aide d’un navigateur de votre choix, accédez à : [https://studio.azureml.net/](https://studio.azureml.net/) et entrez vos informations d’identification pour vous connecter. Ensuite, créez une nouvelle expérience vide.

![modèles d’expérience de recherche](./media/machine-learning-manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Renommez-le **SimpleFeatureHashingExperiment**. Développez **Les groupes de données enregistré** et faites glisser **Critiques à partir d’Amazon** sur votre expérience.

![simple-fonctionnalité-hachage-expérience](./media/machine-learning-manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Développez **La Transformation de données** et **Manipulation** et faites glisser **Sélectionner des colonnes dans le jeu de données** sur votre expérience. Se connecter **critiques à partir d’Amazon** pour **Sélectionner des colonnes dans le jeu de données**.

![Sélectionnez colonnes](./media/machine-learning-manage-web-service-endpoints-using-api-management/project-columns.png)

Cliquez sur **Sélectionner les colonnes dans le jeu de données** et cliquez sur **lancer le sélecteur de colonne** et sélectionnez **Col2**. Cliquez sur la case à cocher pour appliquer ces modifications.

![Sélectionnez colonnes](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-columns.png)

Développez **Analytique de texte** et faites glisser **Fonctionnalité hachage** sur l’expérience. Se connecter **Sélectionner des colonnes dans le jeu de données** à **fonctionnalité hachage**.

![colonnes de projet se connecter](./media/machine-learning-manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Tapez **3** pour le **hachage bitsize**. Cela créera 8 (23) colonnes.

![bitsize hachage](./media/machine-learning-manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

À ce stade, vous voudrez peut-être cliquer sur **exécuter** pour tester l’expérience.

![exécuter](./media/machine-learning-manage-web-service-endpoints-using-api-management/run.png)

###<a name="create-a-web-service"></a>Créer un service web

Créez maintenant un service web. Développez **Service Web** , puis faites glisser **Input** sur votre expérience. Se connecter **entrée** à **fonctionnalité hachage**. Faites également glisser **sortie** sur votre expérience. Se connecter à **sortie** à **fonctionnalité hachage**.

![sortie-à-fonctionnalité-hachage](./media/machine-learning-manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Cliquez sur **service web publier**.

![service web publier](./media/machine-learning-manage-web-service-endpoints-using-api-management/publish-web-service.png)

Cliquez sur **Oui** pour publier l’expérience.

![Oui pour publier](./media/machine-learning-manage-web-service-endpoints-using-api-management/yes-to-publish.png)

###<a name="test-the-web-service"></a>Tester le service web

Un service web AzureML se compose de RSS (service demande/réponse) et les points de terminaison BES (service de l’exécution du lot). RSS s’applique à l’exécution synchrone. BES concerne l’exécution du travail asynchrone. Pour tester votre service web avec la source de Python exemple ci-dessous, vous devrez télécharger et installer le Kit de développement Azure pour Python (voir : [comment installer Python](../python-how-to-install.md)).

Vous devez également **l’espace de travail**, le **service**et **api_key** de votre expérience de la source d’exemple ci-dessous. Vous pouvez trouver l’espace de travail et le service en cliquant sur **Demande/réponse** ou **L’exécution du lot** pour votre expérience dans le tableau de bord de service web.

![Rechercher espace de travail et service](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Vous pouvez trouver **api_key** en cliquant sur votre expérience dans le tableau de bord de service web.

![clé d’api rechercher](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-api-key.png)

####<a name="test-rrs-endpoint"></a>Point de terminaison enregistrements de ressources de test

#####<a name="test-button"></a>Bouton test

Un moyen facile de tester le point de terminaison enregistrements consiste à cliquer sur **tester** dans le tableau de bord de service web.

![test](./media/machine-learning-manage-web-service-endpoints-using-api-management/test.png)

Tapez **il s’agit d’une bonne journée** de **col2**. Cliquez sur la coche.

![entrer des données](./media/machine-learning-manage-web-service-endpoints-using-api-management/enter-data.png)

Vous verrez quelque chose comme

![exemple de sortie](./media/machine-learning-manage-web-service-endpoints-using-api-management/sample-output.png)

#####<a name="sample-code"></a>Exemples de Code

Un autre moyen de tester vos enregistrements de ressource est à partir de votre code client. Si vous cliquez sur **requête/réponse** dans le tableau de bord et faites défiler vers le bas, vous pouvez voir exemples de code pour c#, Python et R. Vous verrez également la syntaxe de la demande d’enregistrements, y compris la demande URI, les en-têtes et les corps.

Ce guide montre un exemple Python qui fonctionne. Vous devrez modifier à l’aide de **l’espace de travail**, le **service**et **api_key** de votre expérience.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
    data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
    }
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
    headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
    body = str.encode(json.dumps(data))
    try:
        req = urllib2.Request(url, body, headers)
        response = urllib2.urlopen(req)
        result = response.read()
        print "result:" + result
            except urllib2.HTTPError, error:
        print("The request failed with status code: " + str(error.code))
        print(error.info())
        print(json.loads(error.read()))

####<a name="test-bes-endpoint"></a>Point de terminaison BES de test
Cliquez sur **l’exécution du lot** dans le tableau de bord et faites défiler vers le bas. Vous verrez des exemples de code pour c#, Python et R. Vous verrez également la syntaxe des requêtes BES soumettre une tâche, démarrage d’une tâche, les résultats d’une tâche ou l’état, et suppression d’une tâche.

Ce guide montre un exemple Python qui fonctionne. Vous devez modifier à l’aide de **l’espace de travail**, le **service**et **api_key** de votre expérience. En outre, vous devez modifier le **nom de compte de stockage**, la **clé de compte de stockage**et le **nom du conteneur de stockage**. Enfin, vous devez modifier l’emplacement du **fichier d’entrée** et l’emplacement du **fichier de copie**.

    import urllib2
    import json
    import time
    from azure.storage import *
    workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
    service = "<REPLACE WITH YOUR SERVICE ID>"
    api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
    storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
    storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
    storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
    input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
    output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
    input_blob_name = "mydatablob.csv"
    output_blob_name = "myresultsblob.csv"
    def printHttpError(httpError):
    print("The request failed with status code: " + str(httpError.code))
    print(httpError.info())
    print(json.loads(httpError.read()))
    return
    def saveBlobToFile(blobUrl, resultsLabel):
    print("Reading the result from " + blobUrl)
    try:
        response = urllib2.urlopen(blobUrl)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    with open(output_file, "w+") as f:
        f.write(response.read())
    print(resultsLabel + " have been written to the file " + output_file)
    return
    def processResults(result):
    first = True
    results = result["Results"]
    for outputName in results:
        result_blob_location = results[outputName]
        sas_token = result_blob_location["SasBlobToken"]
        base_url = result_blob_location["BaseLocation"]
        relative_url = result_blob_location["RelativeLocation"]
        print("The results for " + outputName + " are available at the following Azure Storage location:")
        print("BaseLocation: " + base_url)
        print("RelativeLocation: " + relative_url)
        print("SasBlobToken: " + sas_token)
        if (first):
            first = False
            url3 = base_url + relative_url + sas_token
            saveBlobToFile(url3, "The results for " + outputName)
    return

    def invokeBatchExecutionService():
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
    blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
    print("Uploading the input to blob storage...")
    data_to_upload = open(input_file, "r").read()
    blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
    print "Uploaded the input to blob storage"
    input_blob_path = "/" + storage_container_name + "/" + input_blob_name
    connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
    payload =  {
        "Input": {
            "ConnectionString": connection_string,
            "RelativeLocation": input_blob_path
        },
        "Outputs": {
            "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
        },
        "GlobalParameters": {
        }
    }
        body = str.encode(json.dumps(payload))
    headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
    print("Submitting the job...")
    # submit the job
    req = urllib2.Request(url + "?api-version=2.0", body, headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = response.read()
    job_id = result[1:-1] # remove the enclosing double-quotes
    print("Job ID: " + job_id)
    # start the job
    print("Starting the job...")
    req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    url2 = url + "/" + job_id + "?api-version=2.0"

    while True:
        print("Checking the job status...")
        # If you are using Python 3+, replace urllib2 with urllib.request in the follwing code
        req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
        try:
            response = urllib2.urlopen(req)
        except urllib2.HTTPError, error:
            printHttpError(error)
            return
        result = json.loads(response.read())
        status = result["StatusCode"]
        print "status:" + status
        if (status == 0 or status == "NotStarted"):
            print("Job " + job_id + " not yet started...")
        elif (status == 1 or status == "Running"):
            print("Job " + job_id + " running...")
        elif (status == 2 or status == "Failed"):
            print("Job " + job_id + " failed!")
            print("Error details: " + result["Details"])
            break
        elif (status == 3 or status == "Cancelled"):
            print("Job " + job_id + " cancelled!")
            break
        elif (status == 4 or status == "Finished"):
            print("Job " + job_id + " finished!")
            processResults(result)
            break
        time.sleep(1) # wait one second
    return
    invokeBatchExecutionService()
