<properties
   pageTitle="IIS se connecte journal Analytique | Microsoft Azure"
   description="Internet Information Services (IIS) stocke l’activité des utilisateurs dans les fichiers journaux peuvent être collectées par journal Analytique.  Cet article décrit comment configurer la collecte des journaux IIS et les détails des enregistrements que créent dans le référentiel OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="iis-logs-in-log-analytics"></a>IIS se connecte Analytique de journal
Internet Information Services (IIS) stocke l’activité des utilisateurs dans les fichiers journaux peuvent être collectées par journal Analytique.  

![Journaux IIS](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Configuration des services Internet ouvre une session
Journal Analytique regroupe les entrées de fichiers journaux créés par IIS, c’est pourquoi vous devrez [configurer IIS pour l’enregistrement](https://technet.microsoft.com/library/hh831775.aspx).

Journal Analytique prend en charge les fichiers journaux IIS au format W3C uniquement et ne prend pas en charge champs personnalisés ou journalisation avancée IIS.  
Journal Analytique ne collecte les journaux au format natif NCSA ou IIS.

Configurer les journaux IIS dans journal Analytique à partir du [menu de données dans les paramètres du journal Analytique](log-analytics-data-sources.md#configuring-data-sources).  Il existe aucune configuration requise autre que la sélection du **format W3C collecte IIS les fichiers journaux**.

Nous vous recommandons que lorsque vous activez IIS journal collection, vous devez configurer le paramètre de survol du journal IIS sur chaque serveur.


## <a name="data-collection"></a>Collecte de données

Journal Analytique regroupe les entrées de journal IIS de chaque source connecté environ 15 minutes.  L’agent enregistre son emplacement dans chaque journal des événements qu’il collecte à partir de.  Si l’agent est hors connexion, puis journal Analytique collecte des événements à partir de l’endroit où la dernière interruption, même si les événements créés alors que l’agent était en mode hors connexion.


## <a name="iis-log-record-properties"></a>Propriétés de l’enregistrement journal IIS

Enregistrements du journal IIS ont un type de **W3CIISLog** et les propriétés dans le tableau suivant :

| Propriété | Description |
|:--|:--|
| Ordinateur | Nom de l’ordinateur sur lequel l’événement a été collectée à partir de. |
| cIP | Adresse IP du client. |
| csMethod | Méthode de la requête comme GET ou POST. |
| csReferer | Site que l’utilisateur suivi un lien à partir du site actuel. |
| csUserAgent | Type de navigateur du client. |
| csUserName | Nom de l’utilisateur authentifié ayant accédé au serveur. Les utilisateurs anonymes sont signalés par un trait d’union. |
| csUriStem | Cible de la requête comme une page web. |
| csUriQuery | La requête, le cas échéant, le client a essayé d’effectuer. |
| ManagementGroupName | Nom du groupe d’administration pour agents Operations Manager.  Pour les autres agents, il s’agit des AOI -\<ID de l’espace de travail\> |
| RemoteIPCountry | Pays de l’adresse IP du client. |
| RemoteIPLatitude | Latitude de l’adresse IP du client. |
| RemoteIPLongitude | Longitude de l’adresse IP du client. |
| scStatus | Code d’état HTTP. |
| scSubStatus | Code d’erreur sous-état. |
| scWin32Status | Code d’état Windows. |
| sIP | Adresse IP du serveur web. |
| SourceSystem  | Operations Manager |
| sPort | Port sur le client connecté au serveur. |
| sSiteName | Nom du site IIS. |
| TimeGenerated | Date et heure de que l’entrée a été enregistrée. |
| TimeTaken | Intervalle de temps pour traiter la requête en millisecondes. |

## <a name="log-searches-with-iis-logs"></a>Recherches de journaux avec les journaux IIS

Le tableau suivant fournit des exemples de requêtes de journal qui extraient des enregistrements du journal IIS.

| Requête | Description |
|:--|:--|
| Type = IISLog | Tous les enregistrements de journal IIS. |
| Type = IISLog EventLevelName = erreur | Tous les événements de Windows avec gravité d’erreur. |
| Type = W3CIISLog & #124 ; Mesurer Nb() par cIP | Nombre de IIS des entrées de journal par adresse IP du client. |
| Type = W3CIISLog csHost = « www.contoso.com » & #124 ; Mesurer Nb() par csUriStem | Nombre de IIS entrées du journal par URL pour l’hôte www.contoso.com. |
| Type = W3CIISLog & #124 ; Mesure Sum(csBytes) par ordinateur & #124 ; haut 500000| Nombre total d’octets reçu par chaque ordinateur IIS. |

## <a name="next-steps"></a>Étapes suivantes

- Configurer Analytique journal pour recueillir d’autres [sources de données](log-analytics-data-sources.md) pour l’analyse.
- En savoir plus sur [les recherches de journal](log-analytics-log-searches.md) analyser les données collectées à partir de sources de données et des solutions.
- Configurer des alertes de journal Analytique pour informer vous conditions importantes figurant dans les journaux IIS.
