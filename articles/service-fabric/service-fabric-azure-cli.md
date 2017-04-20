<properties
   pageTitle="Interaction avec les clusters tissu de Service à l’aide d’infrastructure du langage commun | Microsoft Azure"
   description="Comment utiliser Azure infrastructure du langage commun pour interagir avec un cluster tissu de Service"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>


# <a name="using-the-azure-cli-to-interact-with-a-service-fabric-cluster"></a>À l’aide de l’infrastructure du langage commun Azure pour interagir avec un Cluster de tissu de Service

Vous pouvez interagir avec cluster tissu de Service à partir d’ordinateurs Linux à l’aide de l’infrastructure du langage commun Azure sous Linux.

La première étape est d’obtenir la dernière version de l’infrastructure du langage commun à partir de la représentation git et configurez-la dans votre chemin d’accès à l’aide des commandes suivantes :

```sh
 git clone https://github.com/Azure/azure-xplat-cli.git
 cd azure-xplat-cli
 npm install
 sudo ln -s \$(pwd)/bin/azure /usr/bin/azure
 azure servicefabric
```

Pour chaque commande, il prend en charge, vous pouvez taper le nom de la commande pour obtenir de l’aide de cette commande. La saisie semi-automatique est pris en charge pour les commandes. Par exemple, la commande suivante vous offre aide pour toutes les commandes d’application. 

```sh
 azure servicefabric application 
```

Vous pouvez filtrer davantage de l’aide pour une commande spécifique, comme le montre l’exemple suivant :

```sh
 azure servicefabric application  create
```

Pour activer la saisie semi-automatique dans l’infrastructure du langage commun, exécutez les commandes suivantes :

```sh
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.sh\_profile
source ~/azure.completion.sh
```

Les commandes suivantes vous connecter au cluster et vous montrent les nœuds du cluster :

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric node show
```

Pour utiliser des paramètres nommés et rechercher ce qu’elles sont, vous pouvez taper--aide après une commande. Par exemple :

```sh
 azure servicefabric node show --help
 azure servicefabric application create --help
```

Lorsque vous vous connectez à un cluster comportant plusieurs ordinateur à partir d’un ordinateur **qui ne fait pas partie du cluster**, utilisez la commande suivante :

```sh
 azure servicefabric cluster connect http://PublicIPorFQDN:19080
```

Le cas échéant, remplacez la balise PublicIPorFQDN avec l’adresse IP réelle ou le nom de domaine complet. Lorsque vous vous connectez à un cluster comportant plusieurs ordinateur à partir d’un ordinateur **qui ne fait partie du cluster**, utilisez la commande suivante :

```sh
 azure servicefabric cluster connect --connection-endpoint http://localhost:19080 --client-connection-endpoint PublicIPorFQDN:19000
```

Vous pouvez utiliser PowerShell ou infrastructure du langage commun pour interagir avec votre Cluster Linux Service TISSU créé via le portail Azure. 

**Faire preuve de prudence :** Ces groupes ne sont pas sécurisés, par conséquent, vous ouvre votre boîte de celui en ajoutant l’adresse IP publique dans le manifeste cluster.



## <a name="using-the-azure-cli-to-connect-to-a-service-fabric-cluster"></a>À l’aide de l’infrastructure du langage commun Azure pour vous connecter à un Cluster de tissu de Service

Les commandes Azure infrastructure du langage commun suivantes décrivent comment se connecter à un cluster sécurisé. Les détails du certificat doivent correspondre à un certificat sur les nœuds du cluster.

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert
```
 
Si votre certificat a autorités de certification (AC), vous devez ajouter le paramètre--autorité de certification-certificat-chemin d’accès à l’exemple suivant : 

```
 azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
Si vous avez plusieurs autorités de certification, utilisez une virgule comme séparateur.
 
Si votre nom commun dans le certificat ne correspond pas à l’extrémité de la connexion, vous pouvez utiliser le paramètre `--strict-ssl` ignorer la vérification comme indiqué dans la commande suivante : 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl false 
```
 
Si vous voulez ignorer la vérification de l’autorité de certification, vous pouvez ajouter la--paramètre non autorisé rejeter comme indiqué dans la commande suivante : 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized false 
```
 
Une fois que vous vous connectez, vous devriez pouvoir exécuter d’autres commandes d’infrastructure du langage commun pour interagir avec le cluster. 

## <a name="deploying-your-service-fabric-application"></a>Déploiement de votre application de Service TISSU

Exécutez les commandes suivantes pour copier, enregistrer et lancer l’application de tissu de service :

```
azure servicefabric application package copy [applicationPackagePath] [imageStoreConnectionString] [applicationPathInImageStore]
azure servicefabric application type register [applicationPathinImageStore]
azure servicefabric application create [applicationName] [applicationTypeName] [applicationTypeVersion]
```


## <a name="upgrading-your-application"></a>Mise à niveau votre application

Le processus est semblable au [processus dans Windows](service-fabric-application-upgrade-tutorial-powershell.md)).

Créer, copier, enregistrer et créer votre application à partir du répertoire racine du projet. Si votre instance de l’application est nommé TISSU : / MySFApp et le type est MySFApp, les commandes se présente comme suit :

```
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
 azure servicefabric application create fabric:/MySFApp MySFApp 1.0
```

Apportez la modification à votre application, puis recréez le service modifié.  Mettre à jour le fichier manifeste du service modifiée (ServiceManifest.xml) avec les versions mises à jour pour le Service (et Code ou configuration ou données le cas échéant). Également modifier le manifeste de l’application (ApplicationManifest.xml) avec le numéro de version mise à jour de l’application et le service modifié.  À présent, copiez et enregistrer votre application mis à jour à l’aide des commandes suivantes :

```
 azure servicefabric cluster connect http://localhost:19080>
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
```

À présent, vous pouvez démarrer la mise à niveau de l’application avec la commande suivante :

```
 azure servicefabric application upgrade start -–application-name fabric:/MySFApp -–target-application-type-version 2.0  --rolling-upgrade-mode UnmonitoredAuto
```

Vous pouvez maintenant contrôler la mise à niveau de l’application à l’aide de SFX. En quelques minutes, l’application serait ont été mis à jour.  Vous pouvez également essayer une application mis à jour avec un message d’erreur et vérifiez la fonctionnalité de restauration automatique dans tissu de service.

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="copying-of-the-application-package-does-not-succeed"></a>Copie du package d’application ne fonctionne pas

Vérifier si `openssh` est installé. Par défaut, bureau Ubuntu n’est installée. Installer à l’aide de la commande suivante :

```
 sudo apt-get install openssh-server openssh-client**
```

Si le problème persiste, essayez de désactiver PAM pour ssh en modifiant le fichier **sshd_config** à l’aide des commandes suivantes :

```sh
 sudo vi /etc/ssh/sshd_config
#Change the line with UsePAM to the following: UsePAM no
 sudo service sshd reload
```

Si le problème persiste, essayez d’augmenter le nombre de ssh sessions en exécutant les commandes suivantes :

```sh
 sudo vi /etc/ssh/sshd\_config
# Add the following to lines:
# MaxSessions 500
# MaxStartups 300:30:500
 sudo service sshd reload
```
L’utilisation de clés pour ssh authentification (contrairement à des mots de passe) n’est pas encore prises en charge (étant donné que la plateforme utilise ssh pour copier les packages), donc utiliser l’authentification de mot de passe à la place.


## <a name="next-steps"></a>Étapes suivantes

Configurer l’environnement de développement et déployer une application de Service tissu vers un cluster Linux.
