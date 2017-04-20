<properties
   pageTitle="Se connecter à un cluster de Service de conteneur Azure | Microsoft Azure"
   description="Se connecter à un cluster Azure conteneur Service à l’aide d’un tunnel SSH."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, conteneurs, Micro-services, DC/système d’exploitation, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>


# <a name="connect-to-an-azure-container-service-cluster"></a>Se connecter à un cluster de Service de conteneur Azure

Les DC/système d’exploitation et les clusters Docker par essaim déployées par Azure conteneur Service exposent les points de terminaison REST. Toutefois, ces points de terminaison ne sont pas ouverts à l’extérieur. Pour gérer ces points de terminaison, vous devez créer un tunnel SSH (Secure Shell). Après une SSH tunnel a été établie, vous pouvez exécuter des commandes sur les points de terminaison cluster et afficher le cluster l’interface utilisateur via un navigateur sur votre propre système. Ce document vous guide dans la création d’un tunnel SSH à partir de Linux, OS X et Windows.

>[AZURE.NOTE] Vous pouvez créer une session SSH avec un système de gestion de cluster. Toutefois, nous ne recommandons ceci. Travailler directement sur un système de gestion expose le risque de modifications de configuration par inadvertance.   

## <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>Créer un tunnel SSH sur Linux ou OS X

La première chose que vous effectuez lorsque vous créez un tunnel SSH sur Linux ou OS X est pour trouver le nom DNS public d’équilibrage de charge des masques. Pour ce faire, développez le groupe de ressources pour afficher chaque ressource est affiché. Recherchez et sélectionnez l’adresse IP de celle-ci. Une carte qui contient des informations sur l’adresse IP, qui inclut le nom DNS s’ouvre. Enregistrer ce nom pour une utilisation ultérieure. <br />


![Nom DNS public](media/pubdns.png)

Ouvrez un shell et exécutez la commande suivante où :

**PORT** est le port du point de terminaison que vous souhaitez exposer. Pour essaim, il s’agit de 2375. Pour DC/système d’exploitation, utilisez le port 80.  
**Nom d’utilisateur** est le nom d’utilisateur qui a été fourni lorsque vous avez déployé le cluster.  
**DNSPREFIX** est le préfixe DNS que vous avez fourni lorsque vous avez déployé le cluster.  
**Zone** est la région dans lequel se trouve votre groupe de ressources.  
**PATH_TO_PRIVATE_KEY** [Facultatif] est le chemin d’accès à la clé privée qui correspond à la clé publique que vous avez fourni lorsque vous avez créé le cluster Service conteneur. Utilisez cette option avec -i marquer.

```bash
ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
```
> Le port de connexion SSH est 2200--pas le standard port 22.

## <a name="dcos-tunnel"></a>Tunnel DC/système d’exploitation

Pour ouvrir un tunnel vers les points de terminaison DC/OS-liés, exécutez une commande similaire à ce qui suit :

```bash
sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Vous pouvez désormais accéder les points de terminaison DC/OS-liés à :

- CONTRÔLEUR DE DOMAINE/SYSTÈME D’EXPLOITATION :`http://localhost/`
- Marathon :`http://localhost/marathon`
- Mesos :`http://localhost/mesos`

De même, vous pouvez contacter le reste API pour chaque application via ce tunnel.

## <a name="swarm-tunnel"></a>Tunnel essaim

Pour ouvrir un tunnel vers le point de terminaison essaim, exécuter une commande qui ressemble à ce qui suit :

```bash
ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Vous pouvez maintenant définir votre variable d’environnement DOCKER_HOST comme suit. Vous pouvez continuer à utiliser votre interface de ligne de Docker (commande) comme d’habitude.

```bash
export DOCKER_HOST=:2375
```

## <a name="create-an-ssh-tunnel-on-windows"></a>Créer un tunnel SSH sur Windows

Il existe plusieurs options pour la création de tunnel SSH sous Windows. Décrit comment utiliser PuTTY pour effectuer cette action dans ce document.

Téléchargez PuTTY à votre système Windows et exécutez l’application.

Entrez un nom d’hôte est constitué du nom d’utilisateur cluster et le nom DNS public du premier masque dans le cluster. Le **Nom d’hôte** ressemble à ceci : `adminuser@PublicDNS`. Entrez 2200 pour le **Port**.

![Configuration mastic 1](media/putty1.png)

Sélectionnez **SSH** et **l’authentification**. Ajoutez votre fichier de clé privée pour l’authentification.

![Configuration mastic 2](media/putty2.png)

Sélectionnez **tunnel** et configurer les éléments suivants transférés ports :
- **Port source :** Vos préférences--utilisé 80 DC/système d’exploitation ou 2375 pour essaim.
- **Destination :** Utiliser localhost:80 pour DC/système d’exploitation ou localhost:2375 pour essaim.

L’exemple suivant est configuré pour DC/système d’exploitation, mais il ressemblera pour Docker par essaim.

>[AZURE.NOTE] Port 80 ne doit pas être utilisé lorsque vous créez ce tunnel.

![Configuration mastic 3](media/putty3.png)

Lorsque vous avez terminé, enregistrez la configuration de la connexion, puis connecter la session mastic. Lorsque vous vous connectez, vous pouvez voir la configuration du port dans le journal des événements mastic.

![Journal des événements mastic](media/putty4.png)

Lorsque vous avez configuré le tunnel pour DC/OS, vous pouvez accéder au point de terminaison associée à :

- CONTRÔLEUR DE DOMAINE/SYSTÈME D’EXPLOITATION :`http://localhost/`
- Marathon :`http://localhost/marathon`
- Mesos :`http://localhost/mesos`

Lorsque vous avez configuré le tunnel pour Docker par essaim, vous pouvez accéder au cluster essaim via l’infrastructure du langage commun Docker. Vous devez tout d’abord configurer une variable d’environnement Windows nommée `DOCKER_HOST` avec une valeur de ` :2375`.

## <a name="next-steps"></a>Étapes suivantes

Déployer et gérer des conteneurs avec DC/système d’exploitation ou essaim :

- [Travailler avec le Service de conteneur Azure et DC/système d’exploitation](container-service-mesos-marathon-rest.md)
- [Travailler avec le Service de conteneur Azure et Docker essaim](container-service-docker-swarm.md)
