

<properties
   pageTitle="Contrôler présentation Azure Application passerelle le bon fonctionnement | Microsoft Azure"
   description="En savoir plus sur les fonctionnalités de contrôle de passerelle d’Application Azure"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="application-gateway-health-monitoring-overview"></a>Présentation de l’analyse d’intégrité application passerelle

Azure passerelle d’Application par défaut surveille l’état de toutes les ressources dans sa liste principale et supprime automatiquement les ressources considérés comme mauvais état à partir du pool. Application passerelle continue à surveiller les instances défectueux et les ajoute au pool de principale correct une fois qu’elles sont disponibles et répondent aux sondes d’intégrité. Application passerelle envoie que la santé teste avec le même port défini dans les paramètres HTTP principale. Ainsi que la sonde teste le même port clients utilisaient pour vous connecter à la version serveur.

![exemple de sonde application passerelle][1]

Outre le contrôle sonde d’état par défaut, vous pouvez également personnaliser la sonde santé pour répondre aux exigences de votre application. Dans cet article, par défaut et sondes état personnalisés sont traités.

## <a name="default-health-probe"></a>Sonde de santé par défaut

Une passerelle d’application configure automatiquement une sonde d’intégrité par défaut lorsque vous ne définissez aucune configuration sonde personnalisé. Le comportement de surveillance fonctionne à l’aide d’une requête HTTP aux adresses IP configurés pour le pool principale. Pour sondes par défaut si les paramètres de http principaux sont configurés pour HTTPS, la sonde utilisera https ainsi que pour tester l’état des serveurs principaux.

Par exemple : vous configurez votre passerelle d’application pour utiliser les serveurs principaux A, B et C pour recevoir le trafic réseau HTTP sur le port 80. Le contrôle d’état par défaut des tests de trois serveurs toutes les 30 secondes une réponse HTTP correct. Une réponse HTTP exact comporte un [code d’état](https://msdn.microsoft.com/library/aa287675.aspx) entre 200 et 399.

Si la vérification de sonde par défaut échoue pour le serveur A, la passerelle d’application supprime de son pool principale et arrêt du trafic réseau à ce serveur. La sonde par défaut est toujours continue à vérifier pour serveur un toutes les 30 secondes. Lorsque le serveur A répond correctement à une demande à partir d’une sonde d’intégrité par défaut, il est ajouté précédent comme exact au pool de données principale, et le trafic commence s’étalant à nouveau sur le serveur.

### <a name="default-health-probe-settings"></a>Santé sonde paramètres par défaut

|Propriété sonde | Valeur | Description|
|---|---|---|
| URL sonde| http://127.0.0.1 :\<port\>/ | Chemin d’URL |
| Intervalle | 30 | Intervalle sonde en secondes |
| Délai d’attente  | 30 | Délai de sonde en secondes |
| Seuil incorrecte | 3 | Sonde nombre de tentatives. Le serveur principal est marquée comme vers le bas après que le nombre d’échecs consécutifs sonde atteint le seuil mauvais état. |

> [AZURE.NOTE] Le port sera toujours le même port que les paramètres HTTP principale.

La sonde par défaut examine uniquement http://127.0.0.1 :\<port\> pour déterminer l’état d’intégrité. Si vous devez configurer la sonde santé pour atteindre une URL personnalisée ou modifier les autres paramètres, vous devez utiliser sondes personnalisé comme décrit dans les étapes suivantes.

## <a name="custom-health-probe"></a>Sonde d’état personnalisé

Autoriser les sondes personnalisé que vous ayez un contrôle plus précis sur le contrôle d’état. Lorsque vous utilisez sondes personnalisé, vous pouvez configurer l’intervalle sonde, l’URL et chemin d’accès pour tester et nombre de réponses échec pour accepter avant de marquer l’instance principale pool comme étant non fonctionnelle.

### <a name="custom-health-probe-settings"></a>Paramètres de sonde état personnalisé

Le tableau suivant fournit des définitions pour les propriétés d’une sonde d’état personnalisé.

|Propriété sonde| Description|
|---|---|
| Nom | Nom de la sonde. Ce nom est utilisé pour faire référence à la sonde dans les paramètres principale HTTP. |
| Protocole | Protocole utilisé pour envoyer la sonde. La sonde utilise le protocole défini dans les paramètres HTTP principale |
| Hôte |  Nom d’hôte pour envoyer la sonde. Applicable uniquement lorsque plusieurs sites est configuré sur Application passerelle, sinon utilisez '127.0.0.1'. Ceci est différent de nom d’hôte machine virtuelle. |
| Chemin d’accès | Chemin d’accès relatif de la sonde. Le chemin d’accès valide commence à partir de « / ». |
| Intervalle | Sonde intervalle en secondes. Il s’agit de l’intervalle de temps entre deux tests consécutifs.|
| Délai d’attente | Sonde du délai d’attente en secondes. La sonde est marquée comme ayant échoué si une réponse valide n’est pas reçue pendant cette période d’expiration. |
| Seuil mauvais état | Sonde nombre de tentatives. Le serveur principal est marquée comme vers le bas après que le nombre d’échecs consécutifs sonde atteint le seuil mauvais état. |

> [AZURE.IMPORTANT] Si l’Application passerelle est configuré pour un site unique, par défaut l’hôte de nom doit être spécifié comme '127.0.0.1', à moins que dans le cas contraire configuré dans sonde personnalisé.
Pour référence une sonde personnalisé est envoyée à \<protocole\>://\<hôte\>:\<port\>\<chemin d’accès\>. Le port utilisé est le même port tel que défini dans les paramètres HTTP principale.

## <a name="next-steps"></a>Étapes suivantes

Après régulièrement vos connaissances sur le contrôle d’état passerelle d’Application, vous pouvez configurer une [sonde état personnalisé](application-gateway-create-probe-portal.md) dans le portail Azure ou une [sonde d’état personnalisé](application-gateway-create-probe-ps.md) à l’aide de PowerShell et le modèle de déploiement d’Azure le Gestionnaire de ressources.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png