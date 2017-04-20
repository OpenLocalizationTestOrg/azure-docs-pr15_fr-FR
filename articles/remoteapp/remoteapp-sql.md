<properties
   pageTitle="SQL Azure avec Azure RemoteApp | Microsoft Azure"
   description="Découvrez comment utiliser SQL Azure avec Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="ericorman"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="sql-azure-with-azure-remoteapp"></a>SQL Azure avec Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Souvent lorsque les clients choisissent héberger leurs applications Windows dans le cloud avec Azure RemoteApp qu’ils souhaitent également migrer leurs données telles que des serveurs SQL vers le cloud pour un déploiement de cloud entière. Cela permet de solution entière cloud hébergé accessible à tout moment en n’importe quel appareil n’importe où à l’aide de RemoteApp Azure. Vous trouverez ci-dessous des liens et des références ainsi que des conseils pour vous aider à ce processus.  

## <a name="migrate-your-sql-data"></a>Migrer vos données SQL

Commencez à la [migration d’une base de données SQL Server pour la base de données SQL Azure](../sql-database/sql-database-cloud-migrate.md). 

## <a name="configure-azure-remoteapp"></a>Configurer RemoteApp Azure
Héberger votre application Windows dans Azure RemoteApp. Vous trouverez ci-dessous un niveau très haut étape par étape :

1.     Créer le [modèle Azure RemoteApp machine virtuelle](remoteapp-imageoptions.md). 
2.     Installer l’application requise sur l’ordinateur virtuel.
3.     Configurer l’application afin qu’il se connecte à la base de données SQL et vérifiez qu’il fonctionne.
4.     Sysprep et arrêt de la machine virtuelle. Capturer ce sous forme d’image pour une utilisation avec Azure. **Remarque :** Vous devez vous assurer que l’application est en mesure de conserver les informations de connectivité de base de données à travers le processus de sysprep. Si l’application ne peut pas conserver les informations de connexion de base de données, vous souhaiterez peut-être engager le fournisseur de l’application pour vérifier comment nous pouvons spécifier la chaîne de connexion.
5.     Importer l’image personnalisée dans votre bibliothèque Azure RemoteApp sélection de l’emplacement géographique NOMPROPRE résidant dans votre déploiement SQL Azure. 
6.     Déployer une collection RemoteApp dans le même centre de données en tant que votre déploiement SQL Azure en utilisant le modèle ci-dessus et publier l’application. Déploiement d’Azure RemoteApp dans le même centre de données en tant que votre SQL Azure déploiement permet de garantir la vitesse de connexion plus rapide et réduire la latence. 

## <a name="app-and-sql-configuration-considerations"></a>Application et SQL considérations sur la configuration :
Il existe quelques points à prendre en considération lorsque vous utilisez SQL Azure avec RemoteApp :

Découvrez [comment configurer un pare-feu de base de données SQL Azure](../sql-database/sql-database-firewall-configure.md). Un extrait des États article, « au départ, tous les accès à votre serveur de base de données SQL Azure est bloqué par le pare-feu. Pour commencer à utiliser votre serveur de base de données SQL Azure, vous devez accéder au portail classique et spécifiez une ou plusieurs règles de pare-feu au niveau du serveur permettant d’accéder à votre serveur de base de données SQL Azure. Utiliser les règles de pare-feu pour spécifier les plages d’adresses IP à partir d’Internet sont autorisés et applications Azure peuvent ou non essayez de vous connecter à votre serveur de base de données SQL Azure. »

En outre, lorsqu’un ordinateur tente de se connecter à votre serveur de base de données à partir d’Internet, le pare-feu vérifie l’adresse IP d’origine de la demande par rapport à l’ensemble au niveau du serveur et (si nécessaire) règles de pare-feu au niveau de base de données. « Si l’adresse IP de la demande est comprise dans une des plages spécifiés dans les règles de pare-feu au niveau du serveur, la connexion est accordée à votre serveur de base de données SQL Azure. » Par conséquent, nous pouvons rendre l’utilisation de plages d’adresses IP et pas seulement les adresses IP source individuelle.

Suivez les instructions étape par étape de [Comment : configurer les paramètres de pare-feu sur base de données SQL à l’aide du portail Azure](../sql-database/sql-database-configure-firewall-settings.md) pour spécifier la plage IP. Lorsque vous configurez les règles de pare-feu SQL, spécifiez la plage IP du sous-réseau spécifié pour la collection RemoteApp Azure. Cela devrait permettre les serveurs ARA pour vous connecter à la base de données SQL, même s’ils doivent avoir dynamiquement-affectées adresses IP.

## <a name="troubleshooting"></a>Résolution des problèmes
Si l’expérience de l’utilisation d’une application cliente hébergé dans Azure RemoteApp qui se connecte à un SQL de base de données où hébergés sur Azure ou en local est lente quelques facteurs peuvent pourquoi.  

- Latence du réseau de votre appareil à Azure est élevée. Atteindre la connexion de réseau meilleures et le plus rapide que vous pouvez pour de meilleures performances. Utilisez [azurespeed.com](http://azurespeed.com/) comme un outil général pour tester votre latence appareils du centre de données Azure.  
- Application client hébergée dans Azure RemoteApp soit sous tension. Sélection d’une autre offre facturation comme facturation Premium pour améliorer les performances. Une autre astuce consiste à surveiller les ressources de votre application consomme : au cours d’une session active effectuer une combinaison de touches ctrl-alt-fin qui lancera l’écran associations de sécurité, sélectionnez Gestionnaire des tâches et observer l’utilisation des ressources pour votre application.
- SQL server soit sous tension ou non optimisé. Suivez les instructions SQL de résoudre les problèmes. 

