<properties 
    pageTitle="Comment utiliser Blitline d’une image traitement - Azure fonctionnalité guide" 
    description="Découvrez comment utiliser le service Blitline pour traiter les images dans une application Azure." 
    services="" 
    documentationCenter=".net" 
    authors="blitline-dev" 
    manager="jason@blitline.com" 
    editor="jason@blitline.com"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/09/2014" 
    ms.author="support@blitline.com"/>
# <a name="how-to-use-blitline-with-azure-and-azure-storage"></a>Comment utiliser Blitline avec Azure et stockage Azure

Ce guide vous explique comment accéder aux services Blitline et soumettre des travaux à Blitline.

## <a name="what-is-blitline"></a>Quelles sont les Blitline ?

Blitline est un service qui fournit le traitement d’image au niveau entreprise à une fraction du prix de coût pour créer vous-même de traitement d’images sur le nuage.

Le fait est le traitement d’images a été effectué par boucle, généralement reconstruit d’emblée pour chaque site Web. Nous sommes conscients Ceci étant donné que nous avons créé les milliers de fois trop. Un jour, nous avons décidé qu’il est peut-être temps nous simplement le faire pour tout le monde. Nous savoir comment y parvenir, pour le faire rapidement et efficacement et enregistrer tout le monde professionnel dans l’intervalle.

Pour plus d’informations, voir [http://www.blitline.com](http://www.blitline.com).

## <a name="what-blitline-is-not"></a>Ce que Blitline n’est pas...

Pour accroître la lisibilité que Blitline est utile pour, il est souvent plus facile d’identifier que Blitline ne fait pas avant de passer.

- Blitline n’a pas widgets HTML pour télécharger des images. Vous devez disposer des images disponibles publiquement ou avec des autorisations restreintes disponibles pour Blitline atteindre.

- Blitline ne fait pas image en direct traitement comme Aviary.com

- Blitline n’accepte pas le téléchargement des images, vous ne pouvez pas de transmission vos images directement à Blitline. Vous devez les dirige vers le stockage Azure ou d’autres emplacements Blitline prend en charge et ensuite d’indiquer où les obtenir Blitline.

- Blitline est considérablement en parallèle et ne fait pas tout traitement synchrone. Ce qui signifie que vous devez nous fournir un postback_url et nous pouvons vous dire quand nous avons terminé traitement.

## <a name="create-a-blitline-account"></a>Créer un compte Blitline

[AZURE.INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a name="how-to-create-a-blitline-job"></a>Comment créer une tâche Blitline

Blitline utilise JSON pour définir les actions que vous souhaitez appliquer à une image. Cette JSON se compose de plusieurs champs simples.

Voici un exemple simple :

        json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

Nous avons ici JSON qui vous permettront d’une image « src » «... boys.jpeg » et que vous redimensionnez cette image à 240 x 140.

L’ID de l’Application est quelque chose que vous pouvez trouver dans votre onglet **Infos sur la connexion** ou **Gérer** Azure. Il s’agit votre identificateur secrète qui vous permet d’exécuter des travaux sur Blitline.

Le paramètre « Enregistrer » identifie d’informations sur l’endroit où vous souhaitez placer l’image une fois que nous avons la traités. Dans ce cas ordinaire, nous n’avons pas défini une. Si aucun emplacement est défini Blitline stockera il localement (et temporairement) à un emplacement unique cloud. Vous ne pourrez pas obtenir cet emplacement à partir de la JSON retourné par Blitline lorsque vous effectuez la Blitline. L’identificateur « image » est requis et est renvoyée pour vous lors de l’enregistrement pour identifier les donnée image.

Vous pouvez trouver plus d’informations sur les *fonctions* que nous prenons en charge ici : <http://www.blitline.com/docs/functions>

Vous pouvez également trouver la documentation sur les options de travail ici : <http://www.blitline.com/docs/api>

Une fois que votre JSON tout ce que vous devez faire est **billet** pour`http://api.blitline.com/job`

Vous obtiendrez revenir JSON qui ressemble à ceci :

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }


Cela vous indique que Blitline a reçu votre demande, elle compte le placer dans une file d’attente de traitement et lorsqu’il a terminé l’image sera disponible à : **https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_application\_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a name="how-to-save-an-image-to-your-azure-storage-account"></a>Comment enregistrer une image à votre compte de stockage Azure

Si vous avez un compte de stockage Azure, vous pouvez facilement avoir Blitline transmission des images traitées dans votre conteneur Azure. En ajoutant un « azure_destination », vous définissez l’emplacement et les autorisations pour Blitline pousser vers.

Voici un exemple :

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'


En renseignant les valeurs CAPITALIZED avec vos propres, vous pouvez envoyer ce JSON à http://api.blitline.com/job et l’image « src » sont traitée avec un filtre de flou et ensuite envoyée sur vous destination Azure.

###<a name="please-note"></a>Remarque :

Les associations de sécurité doivent contenir l’url de sa entière, y compris le nom de fichier du fichier de destination.

Exemple :

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


Vous pouvez également consulter la dernière édition de documents de stockage Azure de Blitline [ici](http://www.blitline.com/docs/azure_storage).


## <a name="next-steps"></a>Étapes suivantes

Visitez blitline.com pour en savoir plus sur notre autres fonctionnalités :

* Point de terminaison de l’API Blitline documents <http://www.blitline.com/docs/api>
* Fonctions Blitline API <http://www.blitline.com/docs/functions>
* Exemples de Blitline API <http://www.blitline.com/docs/examples>
* Troisième partie Nuget bibliothèque <http://nuget.org/packages/Blitline.Net>
