<properties 
   pageTitle="Notes de publication de version StorSimple 8000 relâchez | Microsoft Azure"
   description="Décrit les nouvelles fonctionnalités, les problèmes ouverts et les solutions de contournement disponibles pour le juillet 2014 Microsoft Azure StorSimple release."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="storsimple-8000-series-release-version-release-notes---july-2014"></a>Notes de publication StorSimple 8000 série relâchez Version - juillet 2014 

## <a name="overview"></a>Vue d’ensemble

Les notes de publication suivre identifient les problèmes ouverts critiques pour la série 8000 StorSimple version officielle (disponibilité générale) de juillet 2014 de Microsoft Azure StorSimple. Cette version correspond à la version du logiciel 6.3.9600.17215.  

Sauf indication contraire, ces notes de publication s’appliquent à tous les modèles de l’appareil StorSimple. Les notes de publication sont à jour en permanence ; dès la découverte de problèmes critiques nécessitant une solution de contournement, elles sont ajoutées. Avant de déployer votre solution Microsoft Azure StorSimple, tenez compte des informations suivantes.  

## <a name="known-issues-in-this-release"></a>Problèmes connus dans cette version
Le tableau suivant fournit un récapitulatif des problèmes connus dans cette version.  
 
| Non. | Fonctionnalité | Problème | Commentaires/solution de contournement | S’applique au périphérique physique | S’applique au périphérique virtuel |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Usine par défaut | Dans certains cas, lorsque vous effectuez une réinitialisation par défaut, le périphérique StorSimple peut être bloqué et affiche ce message : **Rétablir par défaut est en cours (phase 8)**. Cela se produit si vous appuyez sur CTRL + C alors que l’applet de commande est en cours. | N’appuyez sur CTRL + C après le lancement d’une réinitialisation factory. Si vous êtes déjà dans cet état, contactez le Support Microsoft pour obtenir la procédure suivante. | Oui | N° |
| 2 | Quorum de disque | Parfois, si la majorité des disques du boîtier EBOD d’un appareil 8600 est déconnectée résultant dans aucun quorum de disque, puis le pool de stockage sera en mode hors connexion. Il restera en mode hors connexion, même si les disques sont connectés à nouveau. | Vous devrez redémarrez l’appareil. Si le problème persiste, contactez le Support Microsoft pour obtenir la procédure suivante. | Oui | N° |
| 3 | Échecs d’instantané cloud | Parfois, un instantané de cloud peut échouer avec l’erreur **maximale sauvegarde atteint**. Cela se produit si vous dépassez 255 clones en ligne sur le même appareil, à partir du même volume d’origine qui a été supprimé. | | Oui | Oui |
| 4 | ID de contrôleur incorrecte | Lorsqu’un remplacement contrôleur est effectué, contrôleur 0 peut apparaissent sous forme de contrôleur de 1. Pendant le remplacement de contrôleur, lors du chargement de l’image à partir du nœud homologue, l’ID du contrôleur peut s’afficher initialement comme ID. du contrôleur égal Parfois, ce problème peut également se produire après le redémarrage d’un système. | Aucune action utilisateur n’est requise. Cette situation se résoudra de lui-même une fois le remplacement contrôleur terminée. | Oui | N° |
| 5 | Dispositif de surveillance des graphiques | Dans le service Gestionnaire StorSimple, les graphiques de surveillance appareil ne fonctionnent pas lors de la base ou l’authentification NTLM est activée dans la configuration du serveur proxy pour l’appareil. | Modifier la configuration du proxy web pour le périphérique enregistré auprès de votre service Manager StorSimple afin que l’authentification est définie sur aucun. Pour ce faire, exécutez la Windows PowerShell pour l’applet de commande Set-HcsWebProxy StorSimple. | Oui | Oui |
| 6 | Comptes de stockage | Au moyen du service de stockage pour supprimer le compte de stockage est un scénario non pris en charge. Cela vous aboutissez à une situation dans laquelle les données de l’utilisateur ne sont pas accessibles. | | Oui | Oui |
| 7 | Retour arrière | Un retour arrière dans les 24 heures de sinistre (DR) n’est pas pris en charge. | | Oui | N° |
| 8 | Basculement entre périphériques | Plusieurs basculement d’un conteneur de volume à partir du même périphérique source pour appareils différents cible n’est pas pris en charge. Basculement à partir d’un seul périphérique inactif sur plusieurs appareils fera les conteneurs de volume sur le premier basculé appareil perdent propriété des données. Après une telle reprise, ces conteneurs volume apparaît ou se comportent différemment lorsque vous affichez les dans le portail classique Azure. | | Oui | N° |
| 9 | Installation | Au cours de carte StorSimple pour l’installation SharePoint, vous devez fournir une adresse IP périphérique pour l’installation s’exécute correctement. | | Oui | N° |
| 10 | Interfaces réseau | Interfaces réseau DATA 2 et 3 de données ont été échangées du logiciel. | Contactez le Support Microsoft si vous avez besoin configurer ces interfaces. | Oui | N° |


 
