<properties
    pageTitle="Fils solution des données dans le journal Analytique | Microsoft Azure"
    description="Données fils sont des données de réseau et de performances consolidées à partir d’ordinateurs avec agents OMS, y compris les Operations Manager et agents connecté à Windows. Données du réseau sont combinées avec vos données de journal pour vous aider à faire correspondre les données."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="banders"/>

# <a name="wire-data-solution-in-log-analytics"></a>Solution de données dans le journal Analytique de fils

Données fils sont des données de réseau et de performances consolidées à partir d’ordinateurs avec agents OMS, y compris les Operations Manager et agents connecté à Windows. Données du réseau sont combinées avec vos données de journal pour vous aider à faire correspondre les données. Agents OMS installés sur vos ordinateurs dans vos données de réseau informatique infrastructure moniteur envoyées vers et depuis ces ordinateurs pour les niveaux de réseau 2-3 dans le [modèle OSI](https://en.wikipedia.org/wiki/OSI_model) , y compris les différents protocoles et ports utilisés.

>[AZURE.NOTE] La solution fils données n’est pas actuellement disponible pour être ajoutés aux espaces de travail. Clients ayant déjà la solution fils données activée peuvent continuer à utiliser la solution de données fils.

Par défaut, OMS collecte des données connectées pour processeur, la mémoire, disque et données de performances réseau à partir des compteurs intégrés à Windows. Réseau et collecte d’autres données implique en temps réel pour chaque agent, y compris les sous-réseaux et les protocoles au niveau de l’application utilisées par l’ordinateur. Vous pouvez ajouter d’autres compteurs de performance dans la page Paramètres sous l’onglet journaux.

Si vous avez utilisé [sFlow](http://www.sflow.org/) ou autres logiciels avec [protocole de NetFlow de Cisco](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), puis les statistiques et les données que vous voyez à partir des données fils sera familiers.

Parmi les types de requêtes de recherche intégrées journal :

- Agents qui fournissent des données fils
- Adresse IP d’agents fournissant des données fils
- Communications sortantes par les adresses IP
- Nombre d’octets envoyés par protocole d’applications
- Nombre d’octets envoyés par un service d’application
- Octets reçus par différents protocoles
- Nombre total d’octets envoyés et reçus par IP
- Adresses IP ayant communiqué avec agents sur le sous-réseau 10.0.0.0/8
- Latence moyenne pour les connexions qui ont été mesurées fiable
- Processus de l’ordinateur qui engagé ou reçu le trafic réseau
- Volume de trafic réseau pour un processus

Lorsque vous recherchez à l’aide de données fils, vous pouvez filtrer et regrouper des données pour afficher des informations sur les protocoles supérieure et agents supérieure. Ou vous pouvez envisager de quand certains ordinateurs (adresses/MAC les adresses IP) communiqués avec d’autres, sur la façon de temps et la quantité de données a été envoyé, en fait, vous permet d’afficher les métadonnées sur le trafic réseau, qui est basé sur la recherche.

Toutefois, étant donné que vous affichez des métadonnées, il n’est pas nécessairement utile pour un dépannage avancé. Données fils dans OMS ne sont pas une capture complète de données réseau. Il est destiné donc pas approfondie au niveau des paquets résolution des problèmes.
L’avantage de l’utilisation de l’agent, par rapport aux autres méthodes de collection de sites, est que vous n’avez pas à installer appareils, reconfigurer vos commutateurs réseau ou préforme de configurations compliquées. Données fils sont simplement basée sur agent--vous installez l’agent sur un ordinateur et il contrôlerez sa propre le trafic réseau. Un autre avantage est lorsque vous souhaitez surveiller les charges de travail en cours d’exécution dans fournisseurs cloud ou fournisseur de services d’hébergement ou Microsoft Azure, où l’utilisateur ne possède la couche tissu.

En revanche, vous n’avez pas une visibilité complète de ce qui se produit sur le réseau si vous n’installez pas agents sur tous les ordinateurs dans votre infrastructure réseau.

## <a name="installing-and-configuring-the-solution"></a>Installation et configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

- La solution fils données obtient des données à partir d’ordinateurs exécutant Windows Server 2012 R2, Windows 8.1 et versions ultérieures.
- Microsoft .NET Framework 4.0 ou version ultérieure est requis sur les ordinateurs où vous souhaitez acquérir des données fils à partir de.
- Ajouter la solution données fils à votre espace de travail OMS à l’aide de la procédure décrite dans [solutions ajouter journal Analytique à partir de la galerie de Solutions](log-analytics-add-solutions.md).  Il n’existe aucune autre configuration requise.
- Si vous souhaitez afficher les données de fils pour une solution spécifique, vous devez avoir la solution déjà ajoutée à votre espace de travail OMS.

## <a name="wire-data-data-collection-details"></a>Connecter les détails de collecte de données de données

Données fils collecte des métadonnées sur le trafic réseau à l’aide d’agents que vous avez activés.

Le tableau suivant indique les méthodes de collecte de données et autres informations sur le mode de collecte des données pour les données de fils.


| plateforme | Agent directe | Agent SCOM | Stockage Azure | SCOM obligatoire ? | Données de l’agent SCOM envoyées par groupe d’administration | fréquence de collection de sites |
|---|---|---|---|---|---|---|
|Windows (2012 R2 / 8.1 ou version ultérieure)|![Oui](./media/log-analytics-wire-data/oms-bullet-green.png)|![Oui](./media/log-analytics-wire-data/oms-bullet-green.png)|![N°](./media/log-analytics-wire-data/oms-bullet-red.png)|            ![N°](./media/log-analytics-wire-data/oms-bullet-red.png)|![N°](./media/log-analytics-wire-data/oms-bullet-red.png)| toutes les minutes|


## <a name="combining-wire-data-with-other-solution-data"></a>Données de fils de combinaison avec d’autres données de solution

Données renvoyées par les requêtes intégrés ci-dessus peuvent être intéressantes par lui-même. Toutefois, l’utilité des données fils est réunis lorsque vous combinez les informations provenant d’autres solutions OMS. Par exemple, vous pouvez utiliser les données d’événements de sécurité collectées par la solution de sécurité et d’Audit et combiner des données fils pour rechercher des tentatives de connexion réseau inhabituels pour les processus nommées.  Dans cet exemple, vous utiliseriez les opérateurs IN et DISTINCT pour participer à des points de données dans votre requête de recherche.

Conditions requises : Pour pouvoir utiliser l’exemple suivant, vous devez disposer de la solution de sécurité et d’Audit installée. Toutefois, vous pouvez utiliser des données à partir d’autres solutions pour combiner des données fils pour obtenir des résultats similaires.

### <a name="to-combine-wire-data-with-security-events"></a>Pour combiner des données fils avec des événements de sécurité

1. Dans la page Vue d’ensemble, cliquez sur la vignette **WireData** .
2. Dans la liste des **Requêtes WireData courantes**, cliquez sur **Volume de trafic réseau (en octets) par processus** pour afficher la liste de processus retournés.
    ![requêtes wiredata](./media/log-analytics-wire-data/oms-wiredata-01.png)
3. Si la liste de processus est trop longue pour afficher facilement, vous pouvez modifier la requête de recherche pour ressembler à :

    ```
    Type WireData | measure count() by ProcessName | where AggregatedValue <40
    ```
    Montre l’exemple ci-dessous est un processus nommé DancingPigs.exe, qui peuvent apparaître suspect.
    ![résultats de la recherche wiredata](./media/log-analytics-wire-data/oms-wiredata-02.png)

4. À l’aide de données retournées dans votre liste, cliquez sur un processus nommé. Dans cet exemple, DancingPigs.exe l’utilisateur a cliqué. Les résultats ci-dessous décrivent le type de trafic réseau tels que les communications sortantes via les protocoles différents.
    ![résultats wiredata illustrant un processus nommé](./media/log-analytics-wire-data/oms-wiredata-03.png)

5. Étant donné que la solution de sécurité et d’Audit est installée, vous pouvez sonde dans les événements de sécurité qui ont la même valeur ProcessName en modifiant votre requête de recherche en utilisant les opérateurs de requête de recherche IN et DISTINCT. Vous pouvez le faire puis lorsque vos données fils et autres journaux solution ont des valeurs dans la même mise en forme. Modifier votre requête de recherche pour ressembler à :

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName}
    ```    

    ![wiredata les résultats d’affichage combiné des données](./media/log-analytics-wire-data/oms-wiredata-04.png)
6. Dans les résultats ci-dessus, vous verrez que les informations de compte sont affiche. Vous pouvez affiner votre recherche pour déterminer la fréquence à laquelle le compte, affichant des données de sécurité et d’Audit, a été utilisé par le processus avec une requête qui ressemble à :        

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName} | measure count() by Account
    ```

    ![résultats wiredata affichant des données de compte](./media/log-analytics-wire-data/oms-wiredata-05.png)



## <a name="next-steps"></a>Étapes suivantes

- [Les journaux de recherche](log-analytics-log-searches.md) pour afficher les enregistrements de recherche de données fils détaillées.
- Voir [à l’aide des données fils dans recherche journal Suite de gestion des opérations de blog publication de Dan](http://blogs.msdn.com/b/dmuscett/archive/2015/09/09/using-wire-data-in-operations-management-suite.aspx) comportant des informations supplémentaires sur la fréquence à laquelle les données sont collectées et comment vous pouvez modifier les propriétés de collection de sites pour Operations Manager agents.
