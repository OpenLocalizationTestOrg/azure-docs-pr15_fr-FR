<properties 
    pageTitle="Comment créer un ASE ILB à l’aide de modèles Gestionnaire de ressources Azure | Microsoft Azure" 
    description="Apprenez à créer un ASE équilibrage de la charge interne à l’aide de modèles Azure le Gestionnaire de ressources." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="nirma" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="stefsch"/>   

# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Comment créer un ASE ILB à l’aide de modèles Gestionnaire de ressources Azure

## <a name="overview"></a>Vue d’ensemble ##
Environnements de Service d’application peuvent être créés avec une adresse interne réseau virtuel au lieu d’un VIP publique.  Cette adresse interne est fournie par un composant Azure appelé l’équilibrage de charge interne (ILB).  ASE ILB peuvent être créé à l’aide du portail Azure.  Il peut également être créé à l’aide d’automatisation au moyen de modèles Azure le Gestionnaire de ressources.  Cet article décrit les étapes et syntaxe nécessaires pour créer un ASE ILB avec les modèles Azure le Gestionnaire de ressources.

Trois étapes sont impliquées automatiser la création d’un ASE ILB :
1. Tout d’abord la base ASE est créé dans un réseau virtuel à l’aide d’une adresse d’équilibrage de charge interne au lieu d’un VIP publique.  Dans le cadre de cette étape, un nom de domaine racine est affecté à la ASE ILB.
2. Une fois que le ASE ILB est créé, un certificat SSL est téléchargé.  
3. Le certificat SSL téléchargé est explicitement affecté à la ASE ILB en tant que son certificat SSL « par défaut ».  Ce certificat SSL sera utilisé pour le trafic SSL aux applications sur la ASE ILB lorsque les applications sont adressées avec le domaine racine courantes affecté à la ASE (par exemple, https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Création de la Base de ILB ASE ##
Un exemple de modèle Azure le Gestionnaire de ressources et son fichier paramètres associés, sont disponibles sur GitHub [ici][quickstartilbasecreate].

La plupart des paramètres dans le fichier *azuredeploy.parameters.json* sont communes pour la création de ILB ASEs, ainsi que ASEs liés à un public VIP.  La liste ci-dessous appels les paramètres de note spécial de sortie, ou qui sont uniques, lorsque vous créez un ASE ILB :


- *interalLoadBalancingMode*: dans la plupart des cas définir cette option pour 3, ce qui signifie que le trafic HTTP/HTTPS sur les ports 80/443 et les données du contrôle/ports de canal pris en compte par le service FTP sur ASE, sera lié à un ILB alloué adresse interne réseau virtuel.  Si cette propriété est définie à la place à 2, seul le service FTP liés ports (canaux de contrôle et de données) seront liés à une adresse ILB, alors que le trafic HTTP/HTTPS reste sur l’adresse VIP publique.
-  *dnsSuffix*: ce paramètre définit le domaine racine par défaut qui doivent être affecté à la ASE.  Dans la variation publique du Service d’application Azure, le domaine racine par défaut pour toutes les applications web est *azurewebsites.net*.  Toutefois ASE ILB étant interne au réseau virtuel d’un client, il ne fait pas appel préférable d’utiliser le domaine racine de par défaut du service public.  En revanche, ASE ILB doit avoir un domaine racine par défaut qui est pertinent pour au sein de votre réseau interne virtuel d’une société.  Par exemple, une entreprise Contoso hypothétique peuvent utiliser un domaine racine par défaut *contoso.com interne* pour les applications qui sont destinées à être uniquement peut être résolu et accessibles au sein du réseau virtuel de Contoso. 
-  *ipSslAddressCount*: ce paramètre par défaut est automatiquement celle à une valeur de 0 dans le fichier *azuredeploy.json* car ASEs ILB n'avez qu’une seule adresse ILB.  Aucune adresse IP SSL explicite pour ASE ILB et donc du pool d’adresses IP SSL pour ASE ILB doit être défini à zéro, sinon provoquera une erreur mise en service. 

Une fois que le fichier *azuredeploy.parameters.json* a été rempli pour ASE ILB, la ASE ILB peut ensuite créé à l’aide de l’extrait de code Powershell suivant.  Modifier le fichier chemins d’accès pour correspondre à l’endroit où se trouvent les fichiers de modèle Azure le Gestionnaire de ressources sur votre ordinateur.  Pensez également à fournir vos propres valeurs pour le Gestionnaire de ressources Azure déploiement nom et le nom de groupe de ressources.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Une fois le Gestionnaire de ressources Azure modèle est soumis qu'il faudra quelques heures pour la ASE ILB à créer.  Une fois la création terminée, le ASE ILB s’affichent dans le portail UX dans la liste des environnements de Service d’application pour l’abonnement au déclenchée le déploiement.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Téléchargement et la configuration du certificat SSL « Par défaut » ##

Une fois la ASE ILB est créé, un certificat SSL doit être associé à la ASE recours à la « valeur par défaut « certificat SSL pour établir des connexions SSL aux applications.  Pour continuer avec l’exemple Contoso Corporation hypothétique, si la ASE par défaut DNS suffixe est *interne contoso.com*, puis une connexion à *https://some-random-app.internal-contoso.com* nécessite un certificat SSL valide pour **.internal contoso.com*. 

Il existe plusieurs façons d’obtenir un certificat SSL valide, y compris les autorités de certification internes, l’achat d’un certificat à partir d’un émetteur externes et à l’aide d’un certificat auto-signé.  Quelle que soit la source du certificat SSL, les attributs de certificat suivants doivent être configurés correctement :

- *Objet*: cet attribut doit être défini **votre-racine-domaine-here.com*
- *Autre nom du sujet*: cet attribut doit inclure les deux * *votre-racine-domaine-here.com*, et * *.scm.your-racine-domaine-here.com*.  La raison de la deuxième entrée est que connexions SSL au site SCM/Kudu associé à chaque application seront effectuées à l’aide d’une adresse du formulaire *your-app-name.scm.your-root-domain-here.com*.

Avec un certificat SSL valide en main, deux étapes préliminaires supplémentaires sont nécessaires.  Le certificat SSL doit être converti/enregistrées dans un fichier .pfx.  N’oubliez pas que le fichier .pfx doit inclure tout intermédiaire et certificats racine et doit également être sécurisé avec un mot de passe.

Puis le fichier .pfx qui en résulte doit être converti en une chaîne en base 64, car le certificat SSL est téléchargé à l’aide d’un modèle Azure le Gestionnaire de ressources.  Étant donné que le Gestionnaire de ressources Azure modèles sont des fichiers texte, le fichier .pfx doit être converti en une chaîne en base 64 afin de pouvoir être incluse en tant que paramètre du modèle.

L’extrait de code Powershell ci-dessous montre un exemple de générer un certificat auto-signé, exportez le certificat dans un fichier .pfx, convertissant le fichier .pfx un en base 64 chaîne encodée et puis en enregistrant l’en base 64 codé chaîne vers un fichier distinct.  Le code de Powershell pour le codage en base 64 a été adapté du [Blog des Scripts Powershell][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
    
    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")
    
Une fois que le certificat SSL a été généré et converti en une en base 64 codé de chaîne, l’exemple de modèle Azure le Gestionnaire de ressources sur GitHub pour [configurer le certificat SSL par défaut] [ configuringDefaultSSLCertificate] peut être utilisé.

Les paramètres dans le fichier *azuredeploy.parameters.json* sont présentées ci-après :

- *appServiceEnvironmentName*: le nom de la ASE ILB en cours de configuration.
- *existingAseLocation*: chaîne de texte contenant la région Azure où la ASE ILB a été déployée.  Par exemple : « Sud centrale nous ».
- *pfxBlobString*: le based64 codé chaîne représentant le fichier .pfx.  À l’aide de l’extrait de code indiqué précédemment, vous souhaitez copier la chaîne contenue dans « exportedcert.pfx.b64 » et collez-le dans comme la valeur de l’attribut *pfxBlobString* .
- *mot de passe*: le mot de passe utilisé pour sécuriser le fichier .pfx.
- *certificateThumbprint*: l’empreinte du certificat.  Si vous récupérez cette valeur à partir de Powershell (par exemple, *$certificate. Empreinte* à partir de l’extrait de code antérieur), vous pouvez utiliser la valeur en tant que-est.  Cependant si vous copiez la valeur à partir de la boîte de dialogue certificat Windows, n’oubliez pas de supprimer les espaces superflus.  La *certificateThumbprint* doit ressembler à : AF3143EB61D43F6727842115BB7F17BBCECAECAE
- *certificateName*: un identificateur de chaîne convivial de votre choix permet d’identifier le certificat.  Le nom est utilisé dans le cadre de l’identificateur unique Azure le Gestionnaire de ressources pour l’entité *Microsoft.Web/certificates* représentant le certificat SSL.  Le nom **doit** se termine par le suffixe suivant : \_yourASENameHere_InternalLoadBalancingASE.  Ce suffixe est utilisé par le portail en tant qu’indicateur que le certificat est utilisé pour sécuriser ASE compatibles avec les ILB.


Voici un exemple abrégé de *azuredeploy.parameters.json* :


    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

Une fois que le fichier *azuredeploy.parameters.json* a été rempli, le certificat SSL par défaut peut être configuré à l’aide de l’extrait de code Powershell suivant.  Modifier le fichier chemins d’accès pour correspondre à l’endroit où se trouvent les fichiers de modèle Azure le Gestionnaire de ressources sur votre ordinateur.  Pensez également à fournir vos propres valeurs pour le Gestionnaire de ressources Azure déploiement nom et le nom de groupe de ressources.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Une fois le modèle de gestionnaire de ressources Azure envoyé que prendra plus ou moins 40 minutes minutes par ASE frontale pour appliquer les modifications.  Par exemple, avec un ASE par défaut Tabloïd à l’aide de deux serveurs frontaux, le modèle prendra environ une heure et plus de 20 minutes pour terminer.  Pendant que le modèle s’exécute le ASE ne sera pas en mesure de mise à l’échelle.  

Une fois que le modèle est terminée, applications sur la ASE ILB sont accessibles via HTTPS et les connexions seront sécurisées en utilisant le certificat SSL par défaut.  Le certificat SSL par défaut sera utilisé lorsque les applications sur la ILB ASE adressées à l’aide d’une combinaison de nom de l’application ainsi que le nom d’hôte par défaut.  Par exemple *https://mycustomapp.internal-contoso.com* utilisez le certificat SSL par défaut pour **.internal contoso.com*.

Toutefois, comme applications en cours d’exécution sur le service client multiples public, les développeurs peuvent également configurer les noms d’hôte personnalisés pour les applications individuelles et puis configurer les liaisons de certificat SNI SSL uniques pour les applications individuelles.  


## <a name="getting-started"></a>Prise en main

Pour commencer avec les environnements de Service d’application, consultez [Introduction à l’environnement de Service d’application](app-service-app-service-environment-intro.md)

Tous les articles et comment-du pour les environnements de Service d’application sont disponibles dans le [fichier Lisez-moi pour les environnements de Service d’Application](../app-service/app-service-app-service-environments-readme.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 
 
