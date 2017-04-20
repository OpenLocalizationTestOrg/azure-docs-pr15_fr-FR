<properties 
    pageTitle="Tableau de bord, le moniteur, l’échelle, configurer et connexions hybride dans les Services BizTalk | Microsoft Azure" 
    description="En savoir plus sur les contrôles et surveiller les performances sur les onglets portails classiques pour les Services BizTalk : tableau de bord, moniteur, échelle, configurer et connexions hybride. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="mandia"/>




# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>Passez en revue les onglets de tableau de bord, moniteur, échelle, configurer et connexion hybride

Une fois que vous créez votre BizTalk Service et déployez votre application, vous pouvez modifier certains des paramètres du BizTalk Service et surveiller les performances de l’application. 

Lorsque vous ouvrez le portail Azure classique, vous revenez automatiquement à l’onglet **Tous les éléments** . Pour afficher votre BizTalk Service, sélectionnez votre BizTalk Service dans l’onglet **Tous les éléments** ou sélectionnez l’onglet **SERVICES BIZTALK** ; puis sélectionnez le nom de votre BizTalk Service.

Cette action ouvre une nouvelle fenêtre avec les onglets suivants. Cette rubrique décrit les onglets suivants.

## <a name="quick-start-quick-startquickstart"></a>Démarrage rapide (.)![Démarrage rapide][QuickStart])
En fonction de l’édition de Services BizTalk, toutes les options répertoriées peuvent ne pas être disponibles. 
<table border="1">
    <tr>
        <td><strong>Obtenez les outils</strong></td>
        <td>Télécharger le SDK pour installer les modèles de projet Visual Studio sur votre ordinateur de développement local. Ces modèles créent les <strong>Services BizTalk</strong> (pont) et les projets Visual Studio <strong>BizTalk Service objets</strong> (transformation) déployées à votre BizTalk Service.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">Comment puis-je à l’aide de démarrer le SDK Azure</a> et en <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">installant le SDK Azure</a> répertorie les étapes pour commencer.
        </td>
    </tr>
    <tr>
        <td><strong>Créer des accords partenaire</strong></td>
        <td>Ouvre le portail de Services de BizTalk Azure hébergés sur Azure où vous pouvez ajouter des partenaires et créer X12, AS2 et les contrats EDIFACT EDI.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuration des composants pour la messagerie EDI dans BizTalk Services portail</a> répertorie les étapes pour commencer.
        </td>
    </tr>

<tr>
        <td><strong>Pour plus d’informations sur les Services BizTalk</strong></td>
        <td>Accédez au <a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">Centre de formation</a> pour en savoir plus sur les Services BizTalk Azure.</td>
</tr>
</table>


Dans la barre des tâches en bas, vous pouvez :

<table border="1">

<tr>
<td><strong>Gérer les</strong> votre déploiement d’application</td>
<td>Ouvre le portail Azure BizTalk Services. Le portail de Services BizTalk est l’entrée de configuration EDI, notamment l’ajout des partenaires et création de X12, AS2 et les contrats EDIFACT.
<br/><br/>
Il s’agit identique à le <strong>contrats avec les partenaires créer</strong> sous l’onglet <strong>Quick Start</strong> .
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuration des composants pour la messagerie EDI dans BizTalk Services portail</a> donne des informations supplémentaires sur le portail de Services BizTalk.</td>
</tr>

<tr>
<td><strong>Informations de connexion</strong> de la Namespace de contrôle d’accès</td>
<td>Lorsque vous sélectionnez informations de connexion, puis la Namespace de contrôle d’accès, l’émetteur par défaut et clé par défaut sont affichées. Vous pouvez copier les valeurs suivantes.
<br/><br/>
Vous pouvez également ouvrir le portail de contrôle d’accès. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Créer un contrôle d’accès Namespace</a> donne des informations supplémentaires sur le portail de contrôle d’accès.</td>
</tr>

<tr>
<td><strong>Synchroniser des touches</strong> dans le compte de stockage</td>
<td>Lorsque vous créez un compte de stockage, une clé primaire et clé secondaire sont automatiquement créés. Ces touches chiffrement contrôlent l’accès à votre compte de stockage. Votre BizTalk Service utilise automatiquement la clé primaire. <strong>Clés de synchronisation</strong> permettent aux utilisateurs de basculer entre la clé primaire et la clé secondaire sans interrompre le BizTalk Service.
<br/><br/>
Par exemple, vous souhaitez que le BizTalk Service à utiliser une nouvelle clé primaire pour le compte de stockage. Pour cela :
<br/><br/>
<ol>
<li>Sélectionnez votre BizTalk Service et sélectionnez <strong>Clés de synchronisation</strong>. Sélectionnez la clé secondaire. Lorsque vous effectuez cette opération, le BizTalk Service démarre à l’aide de la clé secondaire.</li>
<li>Dans le portail classique Azure, sélectionnez votre compte de stockage et régénérer la clé primaire. N’oubliez pas votre BizTalk Service est à l’aide de la clé secondaire.</li>
<li>Sélectionnez votre BizTalk Service et sélectionnez <strong>Clés de synchronisation</strong>. À présent, sélectionnez la clé primaire. Il s’agit de la nouvelle clé primaire vous régénérer.</li>
<li>Dans le portail classique Azure, sélectionnez votre compte de stockage et régénérer la clé secondaire.</li>
</ol>
<br/>
Ce processus est appelé « touches survol ». L’objectif consiste à permettre aux utilisateurs de basculer entre la clé primaire et la clé secondaire sans interrompre le BizTalk Service.</td>
</tr>

<tr>
<td><strong>Supprimer</strong> votre application</td>
<td>Lorsque vous sélectionnez Supprimer, votre BizTalk Service et tous les éléments déployées sur celui-ci sont supprimés.</td>
</tr>
</table>


## <a name="dashboard"></a>Tableau de bord
En fonction de l’édition de Services BizTalk, toutes les options répertoriées peuvent ne pas être disponibles. 

Lorsque vous sélectionnez le nom de votre BizTalk Service, l’onglet tableau de bord s’affiche. Dans le tableau de bord, vous pouvez :

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>Vue d’ensemble de l’utilisation : Affiche le nombre de connexions hybride utilisées
Affiche également l’utilisation de données en Go. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>Métrique Graph : Affiche une liste fixe des indicateurs de performance
Ces métriques fournissent des valeurs en temps réel en ce qui concerne l’état du BizTalk Service. Vous pouvez également choisir les valeurs **absolue** ou **Relative** et la plage horaire **intervalle** des mesures qui sont affichent dans le graphique. 

Pour obtenir une description de ces indicateurs de performance, accédez à [Statistiques disponibles](#Metrics) dans cette rubrique.


##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>Clin de œil rapide : Répertorie les propriétés de votre BizTalk Service

<table border="1">

<tr>
<td><strong>Mettre à jour les informations d’identification de la base de données de suivi</strong></td>
<td>Modifie le nom d’utilisateur et mot de passe utilisés pour vous connecter à la base de données de suivi.</td>
</tr>
<tr>
<td><strong>Mettre à jour le certificat SSL</strong></td>
<td>Peut mettre à jour le BizTalk Service pour utiliser un certificat SSL différent. Un certificat SSL auto-signé est créé automatiquement lorsque vous <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">créez le BizTalk Service</a>.</td>
</tr>
<tr>
<td><strong>Télécharger le certificat</strong></td>
<td>Vous pouvez télécharger le certificat SSL utilisé par votre BizTalk Service sur un ordinateur local.</td>
</tr>
<tr>
<td><strong>État</strong></td>
<td>Affiche l’état actuel de votre BizTalk Service. Voir <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">Services BizTalk : graphique d’état de Service</a>. </td>
</tr>
<tr>
<td><strong>URL du service</strong></td>
<td>L’URL de votre Service BizTalk. Ceci est identique à l' <strong>URL de domaine</strong> entrée lorsque votre BizTalk Service est créé.</td>
</tr>
<tr>
<td><strong>Adresse IP virtuelle public (VIP)</strong></td>
<td>L’adresse IP attribuée à votre BizTalk Service. Il est utilisé pour tous les points de terminaison d’entrée et est l’adresse source pour le trafic sortant. Cette adresse IP appartient à votre BizTalk Service dans la mesure où il est créé. Si vous supprimez le BizTalk Service, l’adresse IP est affectée à un autre BizTalk Service.</td>
</tr>
<tr>
<td><strong>ACS Namespace</strong></td>
<td>Authentifie avec le Service BizTalk.</td>
</tr>
<tr>
<td><strong>Édition</strong></td>
<td>Listes de l’édition entré lorsque le BizTalk Service est créé.</td>
</tr>
<tr>
<td><strong>Emplacement</strong></td>
<td>Affiche la région géographique qui héberge votre BizTalk Service.</td>
</tr>
<tr>
<td><strong>Créé</strong></td>
<td>Affiche la date et l’heure que du BizTalk Service a été créé.</td>
</tr>
<tr>
<td><strong>Base de données de suivi</strong></td>
<td>Le nom de base de données SQL Azure qui stocke les tables de suivi utilisées par votre BizTalk Service. 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Configuration requise expliqués</a> fournit des détails sur la base de données de suivi.</td>
</tr>
<tr>
<td><strong>Stockage de surveillance / l’archivage</strong></td>
<td>Le nom du compte de stockage Azure qui stocke la sortie surveillance de votre BizTalk Service.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Configuration requise expliqués</a> fournit des détails sur le compte de stockage.</td>
</tr>
<tr>
<td><strong>Nom de l’abonnement</strong></td>
<td>Répertorie l’abonnement qui héberge votre BizTalk Service. L’abonnement régit l’accès au portail classique Azure.</td>
</tr>
<tr>
<td><strong>ID de l’abonnement</strong></td>
<td>Lorsqu’un abonnement est créé, un ID de l’abonnement est généré automatiquement. Lorsque vous utilisez des API REST, vous devrez peut-être entrer l’ID de l’abonnement.</td>
</tr>
</table>

[Services BizTalk : portail classique Azure à l’aide de mise à disponibilité](http://go.microsoft.com/fwlink/p/?LinkID=302280) répertorie les étapes pour créer un BizTalk Service.


##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>Gérer les informations de connexion, clés de synchronisation et supprimer dans la barre des tâches :

<table border="1">

<tr>
<td><strong>Gérer les</strong> votre déploiement d’application</td>
<td>Ouvre le portail de Services BizTalk Azure. Le portail de Services BizTalk est l’entrée de configuration EDI, notamment l’ajout des partenaires et création de X12, AS2 et les contrats EDIFACT.
<br/><br/>
Il s’agit identique à le <strong>contrats avec les partenaires créer</strong> sous l’onglet <strong>Quick Start</strong> .
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuration des composants pour la messagerie EDI dans BizTalk Services portail</a> donne des informations supplémentaires sur le portail de Services BizTalk.</td>
</tr>
<tr>
<td><strong>Informations de connexion</strong> de la Namespace de contrôle d’accès</td>
<td>Affiche les Namespace de contrôle d’accès, l’émetteur par défaut et valeurs de clés par défaut ; qui peut être copié.
<br/><br/>
Vous pouvez également ouvrir le portail de contrôle d’accès. Ce portail de contrôle d’accès est identique à l’aide de l’option Active Directory dans le volet de navigation gauche.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Gestion de votre ACS Namespace</a> donne des informations supplémentaires sur le portail de contrôle d’accès.</td>
</tr>
<tr>
<td><strong>Synchroniser des touches</strong> dans le compte de stockage</td>
<td>Lorsque vous créez un compte de stockage, une clé primaire et clé secondaire sont automatiquement créés. Ces touches chiffrement contrôlent l’accès à votre compte de stockage. Votre BizTalk Service utilise automatiquement la clé primaire. <strong>Clés de synchronisation</strong> permettent aux utilisateurs de basculer entre la clé primaire et la clé secondaire sans interrompre le BizTalk Service.
<br/><br/>
Par exemple, vous souhaitez que le BizTalk Service à utiliser une nouvelle clé primaire pour le compte de stockage. Pour cela :
<br/><br/>
<ol>
<li>Sélectionnez votre BizTalk Service et sélectionnez <strong>Clés de synchronisation</strong>. Sélectionnez la clé secondaire. Lorsque vous effectuez cette opération, le BizTalk Service démarre à l’aide de la clé secondaire.</li>
<li>Dans le portail classique Azure, sélectionnez votre compte de stockage et régénérer la clé primaire. N’oubliez pas votre BizTalk Service est à l’aide de la clé secondaire.</li>
<li>Sélectionnez votre BizTalk Service et sélectionnez <strong>Clés de synchronisation</strong>. À présent, sélectionnez la clé primaire. Il s’agit de la nouvelle clé primaire vous régénérer.</li>
<li>Dans le portail classique Azure, sélectionnez votre compte de stockage et régénérer la clé secondaire.</li>
</ol>
<br/>
Ce processus est appelé « touches survol ». L’objectif consiste à permettre aux utilisateurs de basculer entre la clé primaire et la clé secondaire sans interrompre le BizTalk Service.</td>
</tr>

<tr>
<td><strong>Supprimer</strong> votre application</td>
<td>Votre BizTalk Service et tous les éléments déployées sur celui-ci sont supprimés.</td>
</tr>
</table>


## <a name="monitor"></a>Moniteur
Ne s’applique pas à l’édition gratuite.

Lorsque vous sélectionnez le nom de votre BizTalk Service, l’onglet Moniteur est disponible et affiche les éléments suivants :

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>Graphique métrique : Affiche les indicateurs de performance sélectionné
Ces métriques fournissent des valeurs en temps réel en ce qui concerne l’état du BizTalk Service. Vous choisissez les indicateurs de performance sont affichées. Un maximum de six métriques de performances peut être affiché simultanément. 

Vous pouvez également choisir les valeurs **absolue** ou **Relative** et la plage horaire **intervalle** des mesures qui sont affichent. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>Pour supprimer ou afficher des indicateurs dans le graphique :
1. Sélectionnez l’onglet **Moniteur** .
2. Sélectionnez **Ajouter des indicateurs** dans la barre des tâches :  
![Sélectionnez Ajouter des indicateurs][AddMetrics]
3. Vérifier les mesures de performances que vous voulez afficher.
4. Activez la case à cocher pour revenir à l’onglet **Moniteur** .
5. Sélectionnez le cercle en regard de la mesure afin d’afficher la valeur de ce métrique dans le graphique.  

    Par exemple, la mesure de **l’Utilisation de l’UC** est grisée ; le résultat n’est pas affiché dans le graphique :  
![Métrique de l’UC est grisée][GrayedMetric]  

    Sélectionnez la grisée cercle pour activer la mesure **De l’UC** afficher le résultat dans le graphique :  
![Métrique de l’UC est activée][EnabledMetric]

6. Pour supprimer une mesure du graphique de l’affichage et la liste, sélectionnez **Métrique supprimer** dans la barre des tâches. Pour ajouter l’arrière métrique à la liste, sélectionnez **Ajouter des indicateurs** dans la barre des tâches, vérifiez la mesure, puis sélectionnez la case à cocher pour revenir à l’onglet **Moniteur** . Sélectionnez la grisée cercle pour activer la métrique.

## <a name="Metrics"></a>Statistiques disponibles
Les compteurs/mesures de performances suivantes sont disponibles :

<table border="1">

<tr>
<td><strong>Latence RountdTrip</strong></td>
<td>Affiche la durée moyenne dans (en millisecondes) pour traiter un message à partir de l’heure de que réception du message jusqu'à ce que le message est entièrement traité par le BizTalk Service sur tous les ponts. Uniquement les messages avec succès traités sont prises en compte.<br/><br/>
Lorsque les événements suivants se produisent, un horodatage est créé :
<ul>
<li>Message entre dans la passerelle</li>
<li>Message est acheminé vers la destination</li>
<li>Réponse destination</li>
<li>Réponse d’accusé de réception de destination envoyée à la passerelle</li>
</ul>
<br/>
Cette métrique affiche le résultat du calcul suivant :
<br/><br/>
[Destination accusé de réception réponse envoyée à la passerelle] - [Message entre dans la passerelle]</td>
</tr>
<tr>
<td><strong>Échecs au niveau de la Source</strong></td>
<td>Affiche le nombre total de messages qui a échoué par le BizTalk Service lorsque extraient des messages des points de terminaison source.</td>
</tr>
<tr>
<td><strong>Utilisation de l’UC</strong></td>
<td>Répertorie le % temps processeur moyen de toutes les instances de rôle.</td>
</tr>
<tr>
<td><strong>Latence de traitement</strong></td>
<td>Affiche la durée moyenne nécessaire en (en millisecondes) pour traiter un message par le BizTalk Service sur tous les ponts, à l’exclusion du temps passé dans les destinations. Uniquement les messages avec succès traités sont prises en compte.<br/><br/>
Lorsque chacun des événements suivants se produisent, un horodatage est créé :

<ul>
<li>Message entre dans la passerelle</li>
<li>Message est acheminé vers la destination</li>
<li>Réponse destination</li>
<li>Réponse d’accusé de réception de destination envoyée à la passerelle</li>
</ul>
<br/>Cette métrique affiche le résultat du calcul suivant :<br/><br/>
[Destination accusé de réception réponse envoyée à la passerelle] - [Message entre dans la passerelle] - [Destination réponse] + [Message routé vers la destination]</td>
</tr>
<tr>
<td><strong>Échecs de processus</strong></td>
<td>Affiche le nombre total de messages qui a échoué pendant le traitement par le BizTalk Service sur tous les ponts dans un intervalle de temps.</td>
</tr>
<tr>
<td><strong>Messages envoyés</strong></td>
<td>Affiche le nombre total de messages envoyés par le BizTalk Service sur tous les ponts dans un intervalle de temps. Cette métrique est incrémenté lorsqu’un message envoyé à partir d’un pipeline atteint la destination de l’itinéraire. Cette métrique n’indique pas qu’un message est traité avec succès.<br/><br/>
Dans un scénario de demande de réponse, la mesure est incrémentée lorsque la destination de l’itinéraire envoie un accusé de réception revenir au pipeline.</td>
</tr>
<tr>
<td><strong>Messages reçus</strong></td>
<td>Affiche le nombre total de messages reçus par le BizTalk Service sur tous les ponts dans un intervalle de temps. Cette métrique est incrémenté lorsqu’un nouveau message est reçu par le pipeline.</td>
</tr>
<tr>
<td><strong>Messages de processus</strong></td>
<td>Affiche le nombre total de messages actuellement traités par le BizTalk Service dans un intervalle de temps.</td>
</tr>
<tr>
<td><strong>Messages traités</strong></td>
<td>Affiche le nombre total de messages avec succès traitées par le BizTalk Service sur tous les ponts dans un intervalle de temps. Cette métrique est incrémenté lorsqu’un message est correctement reçu par le pipeline et correctement routé vers la destination.</td>
</tr>
</table>


## <a name="scale"></a>Échelle
Dans l’onglet échelle, vous pouvez ajouter ou soustraire le nombre d’unités utilisée par votre BizTalk Service. Par défaut, il est une valeur unité configurée. Unités supplémentaires peuvent être ajoutées à l’échelle de votre BizTalk Service. Lorsque vous augmentez l’échelle, vous augmentez débit. La quantité de ressources augmente également, notamment des ponts déployés accords, connexions métier et puissance de traitement. Par exemple, vous augmentez l’échelle de 1 unité à 2 unités. Dans ce cas, vous pouvez déployer double du nombre de ponts, double-cliquez accords, double-cliquez les connexions professionnelles et double-cliquez la puissance de traitement.

Certaines éditions BizTalk n’offrent pas une option de redimensionnement. Dans ce cas, une seule unité est autorisée. Pour déterminer le nombre d’unités votre édition peut être mise à l’échelle, reportez-vous à [Services BizTalk : graphique des éditions](biztalk-editions-feature-chart.md).

Augmenter le nombre d’unités peut-être avoir un impact sur tarifs. Si vous augmentez l’intensité d’utilisation, sélectionnez **Enregistrer** affiche un message qui vous indique si la facturation est affectée. Vous souhaitez puis continuer. Lorsque vous augmentez le nombre d’unités, le BizTalk Service statut devient actif mise à jour. Dans l’état de la mise à jour, votre BizTalk Service continue de fonctionner.

[Services BizTalk : graphique des éditions](biztalk-editions-feature-chart.md) définit une « unité ».


## <a name="configure"></a>Configurer
Ne s’applique pas aux connexions hybride.

Définit l’état de sauvegarde sur aucune ou automatique. Lorsque la valeur Aucun, aucune sauvegarde est automatiquement créés. Si défini sur automatique, configurez l’emplacement de sauvegarde, la fréquence de la sauvegarde et l’intervalle de temps pour conserver les fichiers de sauvegarde. 

[Services BizTalk : sauvegarder et restaurer](biztalk-backup-restore.md) fournit les informations. 


## <a name="HybridConnections"></a>Connexions hybride
Connexions hybride connecter à une application Azure, telle que des applications Web ou des applications mobiles dans le Service d’application Azure, à une ressource locale qui utilise un port TCP statique, tels que SQL Server, MySQL, API Web HTTP et la plupart des Services Web personnalisés. Connexions hybride sont gérées dans les Services BizTalk dans le portail classique Azure.

Pour créer des connexions hybride dans le Service d’application Azure, voir [accès local ressources à l’aide de connexions hybride dans le Service d’application Azure](../app-service-web/web-sites-hybrid-connection-get-started.md).

Pour créer ou gérer les connexions hybride dans les Services BizTalk Azure, voir [Connexions hybride](integration-hybrid-connection-overview.md).



## <a name="next"></a>Suivant
À présent que vous avez l’habitude avec les différents onglets, vous pouvez en savoir plus sur les fonctionnalités Azure BizTalk Services :

- [Services BizTalk : la limitation](biztalk-throttling-thresholds.md)  
- [BizTalk Services : Nom de l’émetteur et la clé de l’émetteur](biztalk-issuer-name-issuer-key.md)  
- [Les Services BizTalk : Sauvegarder et restaurer](biztalk-backup-restore.md)

## <a name="see-also"></a>Voir aussi
- [Connexions hybride](integration-hybrid-connection-overview.md)  
- [BizTalk Services : Pour les développeurs, basique, Standard et Premium éditions graphique](biztalk-editions-feature-chart.md)  
- [Services BizTalk : Portail classique Azure à l’aide de la mise en service](biztalk-provision-services.md)  
- [BizTalk Services : Graphique de l’état du Service BizTalk](biztalk-service-state-chart.md)  
- [Comment puis-je à l’aide de démarrer le SDK Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[QuickStart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png
 
