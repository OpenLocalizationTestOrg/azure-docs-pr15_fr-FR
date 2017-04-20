<properties
    pageTitle="Tester les paramètres du Gestionnaire de trafic | Microsoft Azure"
    description="Cet article vous aidera à tester les paramètres du Gestionnaire de trafic"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="test-your-traffic-manager-settings"></a>Testez vos paramètres de trafic Manager

Pour tester vos paramètres du Gestionnaire de trafic, vous devez disposer de plusieurs clients, à différents emplacements, à partir de laquelle vous pouvez exécuter vos tests. Puis, mettre les points de terminaison dans votre profil gestionnaire le trafic vers le bas une à la fois.

* Définissez la valeur DNS TTL basse afin que les modifications propagées rapidement (par exemple, 30 secondes).
* Connaître les adresses IP de vos services cloud Azure et sites Web dans le profil que vous testez.
* Utiliser les outils qui vous permettent de résoudre un nom DNS vers une adresse IP et afficher cette adresse.

Vous vérifiez que les noms DNS résoudre aux adresses IP des points de terminaison dans votre profil. Les noms doivent résoudre de manière cohérente avec la méthode de routage le trafic définie dans le profil de trafic Manager. Vous pouvez utiliser les outils, tels que **nslookup** ou **creuser** pour résoudre les noms DNS.

Les exemples suivants vous aident à tester votre profil le trafic Manager.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Vérifier le trafic Gestionnaire profil à l’aide de nslookup et ipconfig dans Windows

1. Ouvrez une commande ou une invite de Windows PowerShell en tant qu’administrateur.
2. Type de `ipconfig /flushdns` pour vider le cache de résolution DNS.
3. Type de `nslookup <your Traffic Manager domain name>`. Par exemple, la commande suivante vérifie le nom de domaine avec le préfixe *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Un résultat classique affiche les informations suivantes :

    * Le nom DNS et l’adresse IP du serveur DNS utilisé pour résoudre ce nom de domaine gestionnaire le trafic.
    * Le nom de domaine le trafic gestionnaire que vous avez tapés dans la ligne de commande après « nslookup » et l’adresse IP à laquelle le domaine le trafic Manager résout. La deuxième adresse IP est celle importantes à vérifier. Il doit correspondre à une public adresse IP virtuelle (VIP) pour un des services cloud ou des sites Web dans le profil de trafic Manager que vous testez.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Comment tester la méthode de routage de trafic basculement

1. Conservez tous les points de terminaison vers le haut.
2. À l’aide d’un seul client, demander la résolution DNS pour votre nom de domaine d’entreprise à l’aide de nslookup ou un utilitaire similaire.
3. Assurez-vous que l’adresse IP résolue correspond au point de terminaison principal.
4. Arrêter votre point de terminaison primaire ou supprimez le fichier surveillance afin que le Gestionnaire de trafic pense que l’application est vers le bas.
5. Attendez que la DNS Time-to-Live (TTL) du profil Manager le trafic plus un deux minutes supplémentaires. Par exemple, si votre DNS TTL est 300 secondes (5 minutes), vous devez attendre sept minutes.
6. Vider votre DNS client cache demande de résolution DNS et l’utilisation de nslookup. Dans Windows, vous pouvez vider le cache de résolution DNS avec la commande ipconfig /flushdns.
7. Assurez-vous que l’adresse IP résolue correspond à votre point de terminaison secondaire.
8. Répétez le processus, en bénéficiant à son tour vers le bas de chaque point de terminaison. Vérifiez que le système DNS renvoie l’adresse IP du point de terminaison suivant dans la liste. Lorsque tous les points de terminaison sont vers le bas, vous devez obtenir l’adresse IP du point de terminaison principal à nouveau.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Comment tester la méthode de routage le trafic pondérée

1. Conservez tous les points de terminaison vers le haut.
2. À l’aide d’un seul client, demander la résolution DNS pour votre nom de domaine d’entreprise à l’aide de nslookup ou un utilitaire similaire.
3. Assurez-vous que l’adresse IP résolue correspond à un de vos points de terminaison.
4. Vider le cache de client DNS et répétez les étapes 2 et 3 pour chaque point de terminaison. Vous devriez voir les différentes adresses IP renvoyées pour chacun de vos points de terminaison.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Comment tester la méthode de routage de trafic performances

Pour tester efficacement une méthode routage le trafic de performances, vous devez disposer des clients situés dans différentes parties du monde. Vous pouvez créer des clients dans différentes régions Azure pouvant être utilisées pour tester vos services. Si vous avez un réseau général, vous pouvez à distance se connecter aux clients dans d’autres parties du monde et exécuter les tests à partir de là.

Par ailleurs, il existe Libérez de recherche DNS basé sur le web et retrouvez les services disponibles. Certains de ces outils vous offre la possibilité de résolution de noms DNS à partir de divers emplacements dans le monde entier. Effectuer une recherche sur « Recherche DNS » pour obtenir des exemples. Services tiers comme systèmes Gomez ou discours peuvent être utilisés pour confirmer que vos profils distribuez le trafic comme prévu.

## <a name="next-steps"></a>Étapes suivantes

* [À propos du Gestionnaire de trafic du trafic méthodes de routage](traffic-manager-routing-methods.md)
* [Remarques sur les performances du trafic Manager](traffic-manager-performance-considerations.md)
* [Résolution des problèmes le trafic gestionnaire est détérioré état](traffic-manager-troubleshooting-degraded.md)




