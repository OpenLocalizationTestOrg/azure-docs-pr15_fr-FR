<properties
    pageTitle="Configurer le trafic Gestionnaire des méthodes de routage | Microsoft Azure"
    description="Cet article explique comment configure les différentes méthodes de routage dans le Gestionnaire de trafic"
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
   ms.date="10/18/2016"
   ms.author="sewhee" />
<!-- repub for nofollow -->

# <a name="configure-traffic-manager-routing-methods"></a>Configurer le trafic Gestionnaire des méthodes de routage

Gestionnaire de trafic Azure fournit trois méthodes routage qui contrôlent la façon dont le trafic est acheminé aux points de terminaison de service disponible. La méthode le routage du trafic est appliquée à chaque requête DNS reçu afin de déterminer quel point de terminaison doit être retournée dans la réponse DNS.

Il existe trois méthodes de routage de trafic disponibles dans le Gestionnaire de trafic :

- **Priorité :** Sélectionnez 'Priority' lorsque vous voulez utiliser un point de terminaison du service principal et fournir des sauvegardes au cas où le serveur principal n’est pas disponible.
- **Pondérés :** Sélectionnez « pondérée » lorsque vous voulez répartir le trafic dans un jeu de points de terminaison, soit uniformément ou en fonction de grammages et que vous définissez.
- **Performances :** Sélectionnez « Performance » lorsque vous avez des points de terminaison dans différentes zones géographiques et que vous souhaitez aux utilisateurs finaux d’utiliser le point de terminaison « le plus proche » en ce qui concerne la latence du réseau les plus faibles.

## <a name="configure-priority-routing-method"></a>Configurer la méthode de routage priorité

Quel que soit le mode de site Web, des sites Web Azure fournissent déjà des fonctionnalités de basculement pour les sites Web dans un centre de données (également appelé une région). Gestionnaire de trafic fournit basculement pour les sites Web dans différents centres de données.

Un modèle commun pour le basculement de service consiste à acheminer le trafic vers un service principal et fournir un ensemble de services de sauvegarde identiques pour le basculement. Les étapes suivantes expliquent comment configurer ce basculement hiérarchisé avec des sites Web et services cloud Azure :

1. Dans le portail classique Azure, dans le volet gauche, cliquez sur l’icône **Gestionnaire de trafic** pour ouvrir le volet Gestionnaire de trafic.
2. Dans le volet Gestionnaire le trafic dans le portail classique Azure, recherchez le profil de trafic gestionnaire qui contient les paramètres que vous souhaitez modifier. Pour ouvrir la page de paramètres de profil, cliquez sur la flèche située à droite du nom du profil.
3. Dans votre page de profil, cliquez sur les **points de terminaison** en haut de la page. Vérifiez que les services en nuage et les sites Web que vous souhaitez inclure dans votre configuration sont présents.
4. Dans la partie supérieure pour ouvrir la page de configuration, cliquez sur **configurer** .
5. Pour les **paramètres de la méthode routage le trafic**, vérifiez que la méthode d’acheminement du trafic est **basculement**. Si elle n’est pas le cas, cliquez sur **Basculer** dans la liste déroulante.
6. Pour la **Liste priorité basculement**, ajustez le basculement afin que vos points de terminaison. Lorsque vous sélectionnez la méthode de routage de trafic **basculement** , l’ordre des points de terminaison sélectionné est également importante. Le point de terminaison principal se trouve au-dessus. Utilisez le haut et bas pour modifier l’ordre selon vos besoins. Pour plus d’informations sur la façon de définir la priorité de basculement à l’aide de Windows PowerShell, voir [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Vérifiez que les **Paramètres de surveillance** sont correctement configurés. Surveillance garantit que les points de terminaison qui sont en mode hors connexion ne sont pas envoyés le trafic. Pour analyser les points de terminaison, vous devez spécifier un chemin d’accès et le nom de fichier. Une barre oblique « / » est une entrée valide pour le chemin d’accès relatif et implique que le fichier se trouve dans le répertoire racine (par défaut).
8. Après avoir effectué vos modifications de configuration, cliquez sur **Enregistrer** dans la partie inférieure de la page.
9. Tester les modifications dans votre configuration.
10. Une fois que votre profil le trafic Manager fonctionne, modifiez l’enregistrement DNS sur votre serveur DNS faisant autorité pour pointez sur le nom de domaine de votre entreprise le nom de domaine gestionnaire le trafic.

## <a name="configure-weighted-routing-method"></a>Configurer la méthode de routage pondérée

Un modèle de méthode routage le trafic courant consiste à fournir un jeu de points de terminaison identiques, qui incluent des services cloud et sites Web, et acheminer le trafic vers chacun de manière cyclique. Les étapes suivantes décrivent comment configurer ce type de la méthode de routage du trafic.

>[AZURE.NOTE] Sites Web Azure fournissent déjà cyclique équilibrage de charge des fonctionnalités pour les sites Web dans un centre de données (également appelé une région). Gestionnaire de trafic permet de vous permettent de spécifier la méthode de routage cyclique le trafic pour les sites Web dans différents centres de données.

1. Dans le portail classique Azure, dans le volet gauche, cliquez sur l’icône **Gestionnaire de trafic** pour ouvrir le volet Gestionnaire de trafic.
2. Dans le volet le trafic Manager, recherchez le profil de trafic gestionnaire qui contient les paramètres que vous souhaitez modifier. Pour ouvrir la page de paramètres de profil, cliquez sur la flèche située à droite du nom du profil.
3. Dans la page de votre profil, cliquez sur les **points de terminaison** en haut de la page et vérifiez que les points de terminaison de service que vous souhaitez inclure dans votre configuration sont présents.
4. Dans votre page de profil, cliquez sur **configurer** dans la partie supérieure pour ouvrir la page de configuration.
5. Pour **le trafic routage méthode paramètres**, vérifiez que la méthode d’acheminement du trafic est **alternées**. Si elle n’est pas le cas, cliquez sur **alternées** dans la liste déroulante.
6. Vérifiez que les **Paramètres de surveillance** sont correctement configurés. Surveillance garantit que les points de terminaison qui sont en mode hors connexion ne sont pas envoyés le trafic. Pour analyser les points de terminaison, vous devez spécifier un chemin d’accès et le nom de fichier. Une barre oblique « / » est une entrée valide pour le chemin d’accès relatif et implique que le fichier se trouve dans le répertoire racine (par défaut).
7. Après avoir effectué vos modifications de configuration, cliquez sur **Enregistrer** dans la partie inférieure de la page.
8. Tester les modifications dans votre configuration.
9. Une fois que votre profil le trafic Manager fonctionne, modifiez l’enregistrement DNS sur votre serveur DNS faisant autorité pour pointez sur le nom de domaine de votre entreprise le nom de domaine gestionnaire le trafic.

## <a name="configure-performance-traffic-routing-method"></a>Configurer la méthode de routage le trafic performances

La méthode de routage de trafic performances vous permet de diriger le trafic au point de terminaison avec la latence la plus faible à partir du réseau du client. En règle générale, le centre de données avec la latence la plus faible est le plus proche dans la distance géographique. Cette méthode de routage le trafic ne peut pas tenir compte des modifications en temps réel dans la configuration réseau ou charger.

1. Dans le portail classique Azure, dans le volet gauche, cliquez sur l’icône **Gestionnaire de trafic** pour ouvrir le volet Gestionnaire de trafic.
2. Dans le volet le trafic Manager, recherchez le profil de trafic gestionnaire qui contient les paramètres que vous souhaitez modifier. Pour ouvrir la page de paramètres de profil, cliquez sur la flèche située à droite du nom du profil.
3. Dans la page de votre profil, cliquez sur les **points de terminaison** en haut de la page et vérifiez que les points de terminaison de service que vous souhaitez inclure dans votre configuration sont présents.
4. Dans la page de votre profil, cliquez sur **configurer** dans la partie supérieure pour ouvrir la page de configuration.
5. Pour les **paramètres de la méthode routage le trafic**, vérifiez que la méthode d’acheminement du trafic est * *performances*. Si elle n’est pas le cas, cliquez sur * *performances** dans la liste déroulante.
6. Vérifiez que les **Paramètres de surveillance** sont correctement configurés. Surveillance garantit que les points de terminaison qui sont en mode hors connexion ne sont pas envoyés le trafic. Pour analyser les points de terminaison, vous devez spécifier un chemin d’accès et le nom de fichier. Une barre oblique « / » est une entrée valide pour le chemin d’accès relatif et implique que le fichier se trouve dans le répertoire racine (par défaut).
7. Après avoir effectué vos modifications de configuration, cliquez sur **Enregistrer** dans la partie inférieure de la page.
8. Tester les modifications dans votre configuration.
9. Une fois que votre profil le trafic Manager fonctionne, modifiez l’enregistrement DNS sur votre serveur DNS faisant autorité pour pointez sur le nom de domaine de votre entreprise le nom de domaine gestionnaire le trafic.

## <a name="next-steps"></a>Étapes suivantes

* [Gérer les profils de trafic Manager](traffic-manager-manage-profiles.md)
* [Méthodes de routage de trafic Manager](traffic-manager-routing-methods.md)
* [Tester les paramètres du Gestionnaire de trafic](traffic-manager-testing-settings.md)
* [Pointez sur un domaine Internet d’entreprise un domaine le trafic Manager](traffic-manager-point-internet-domain.md)
* [Gérer les points de terminaison du trafic Manager](traffic-manager-manage-endpoints.md)
* [Résolution des problèmes le trafic gestionnaire est détérioré état](traffic-manager-troubleshooting-degraded.md)
