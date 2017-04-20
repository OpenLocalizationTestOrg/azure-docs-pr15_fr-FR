
<properties
    pageTitle="Quelles sont les nouveautés dans Azure RemoteApp ? | Microsoft Azure"
    description="En savoir plus sur les modifications et les améliorations apportées à Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="whats-new-in-azure-remoteapp"></a>Quelles sont les nouveautés dans Azure RemoteApp ?

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Un des avantages d’Azure RemoteApp est que nous nous efforçons toujours pour l’améliorer. Chaque fois que nous faire, nous allons annoncer ces modifications ici.

## <a name="future-updates"></a>Mises à jour ultérieures
Bonjour - Saviez-vous que l’équipe Azure RemoteApp publie des mises à jour mensuelles au blog RDS ? Vous trouverez pas seulement Nouveautés pour modification dans Azure RemoteApp, mais aussi d’autres informations sur l’utilisation de RDS. Consultez son blog [Blog de Services Bureau à distance](https://blogs.msdn.microsoft.com/rds/), pour plus d’informations. Par exemple, quelques semaines plus tôt, ils publié une entrée sur [soulevant et un décalage de charges de travail avec Azure RemoteApp et Azure AD](https://blogs.msdn.microsoft.com/rds/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services/).
 
## <a name="september-2015"></a>Septembre 2015
- Infopath ajouté à l’image de modèle et Galerie de Microsoft Office 365. Si vous souhaitez partager Infopath, veillez à mettre à jour vos collections avec la dernière image.
- Mises à jour du client :
    - Client Windows mis à jour pour permettre aux utilisateurs de partager des commentaires, notamment en termes de problèmes de connexion.
    - iOS client mis à jour pour corriger les messages d’erreur et résoudre un problème à l’endroit où vos informations d’identification expiré plus tôt que prévu.
- Nous travaillons sur l’obtention de prise en charge d’Office 2016 testé. Une fois terminée, recherchez les images mises à jour.
- Publier un nouvel article sur les [différences entre les collections cloud et hybride](remoteapp-collections.md) - cela vous permettra de choisir le type de collection de sites qui vous convient le mieux à vos applications - exclusivement le nuage, cloud + VNET ou hybride.
- Partager QuickBooks à l’aide de RemoteApp Azure, mais vous ne savez pas des étapes ? Consultez [l’article nouveau d’Éric](remoteapp-quickbooks.md) vous indiquant exactement la procédure à suivre.

## <a name="august-2015"></a>Août 2015
Principaux changements s’est-il passé dans août - Voici la surbrillance :

- Vous pouvez désormais utiliser un VNET Azure une collection de cloud ! Consultez les [instructions de la création de cloud](remoteapp-create-cloud-deployment.md) pour les nouvelles étapes.
- Fait en sorte qu’ajouter des applications dans le menu **Démarrer **pour le client Windows RemoteApp. Applications seront affichent dans la liste des applications et vous pouvez les épingler au menu **Démarrer **de Windows.
- Ajouté une nouvelle image dans la galerie de machine virtuelle Azure - Windows Server hôte Bureau à distance Session avec Microsoft Office 365 ProPlus.
- Fixe le client Mac actuelles avec les fenêtres modales arrêtera figer.
- Décrit comment vous pouvez utiliser votre [abonnement Office 365 ProPlus](remoteapp-officesubscription.md) avec Azure RemoteApp.
- Détaillée comment vous pouvez [sécuriser les applications et les données](remoteapp-secure.md) dans votre collection de sites RemoteApp Azure.

## <a name="july-2015"></a>Juillet 2015

Juillet définie la scène pour modifications bientôt en août, il est donc pas beaucoup parler maintenant, principalement les mises à jour un document. Voici les dernières modifications apportées :

- Ajouter un onglet **prise en charge** au portail afin d’y accéder plus facilement les ressources de support, comme les forums.
- Retravaillés les informations de dépannage pour la création d’une collection de sites hybride. Consultez [les dernières et les meilleures](remoteapp-hybridtrouble.md) pour la résolution des info-bulles comme comment identifier les ports à configurer pour votre VNET.
- Décrit comment les [données de l’utilisateur](remoteapp-upd.md) est créé et enregistré dans Azure RemoteApp.
- Décrit comment faire pour [Verrouiller des applications](remoteapp-secure.md).
- Publier les [applets de commande RemoteApp Azure](https://msdn.microsoft.com/library/mt428031.aspx).
- Et pour finir, nous avons commencé à une conversation avec certains utilisateurs Azure RemoteApp à propos de la terminologie. Recherchez les modifications apportées à la façon dont nous faire référence aux options de la collection de sites différents.

## <a name="june-2015"></a>Juin 2015

Nombre de modifications ! L’équipe n’a pas de juin :

- Nouvelle conception de RemoteApp Azure [page d’accueil](https://www.remoteapp.windowsazure.com/) - à son extraction !
- Mise à jour du logiciel dans toutes les images disponibles dans le cadre de votre abonnement.
- Apporté des améliorations aux collections hybride, y compris forcé tunnel prise en charge et vérification de taille de sous-réseau IP avant d’essayer de créer la collection de sites.
- Découvert que la * génériques ne fonctionne pas pour des webcams. À la place, vous devez spécifier l’ID de l’instance ou GUID. Nous allons mettre à jour les informations de redirection pour refléter celle.
- Fait en sorte que vous pouvez ajouter un logiciel antivirus personnalisé à votre image lorsque vous créez une image du modèle dans la galerie Azure.

Nous avons autres modifications introduites en juillet, afin que nous allons arrivant à avec une autre mise à jour.

## <a name="may-2015"></a>Mai 2015

Un certain nombre de compléments (mois) dans la mesure où tout d’abord créé cette rubrique, afin que cette liste dont un peu et est à partir du début du mois de mars ont été via mai. Consultez ces nouvelles fonctionnalités :

- Automatiser tout - Azure RemoteApp comporte maintenant [applets de commande dans le module Azure PowerShell](remoteapp-tutorial-arawithpowershell.md).
- [Créer une image Azure RemoteApp à partir d’une machine virtuelle Azure](remoteapp-image-on-azurevm.md). Permet de télécharger votre image personnalisée à Azure beaucoup plus rapide.
- Utilisez un VNET Azure au lieu d’un VNET RemoteApp pour connecter des ressources de votre réseau d’entreprise à Azure. Nous avons mis à jour les [instructions de collection de sites hybride](remoteapp-create-hybrid-deployment.md) pour vous aider à créer un VNET Azure (il s’agit étape 1).
- À propos de VNETs, consultez [le nouveau guide](remoteapp-vnetsizing.md) autour des limites de taille VNET et des limitations.
- Et à propos des limites - simplement quels sont les [limites de service et les valeurs par défaut](../azure-subscription-service-limits.md)?

Vous voulez en savoir plus sur RemoteApp Azure ? L’équipe RemoteApp était en vigueur au Ignite il y a quelques semaines. Consultez vidéo d’Éric, [les principes de base de Microsoft Azure RemoteApp gestion et d’Administration](http://channel9.msdn.com/Events/Ignite/2015/BRK3868).

Vous avez besoin pour voir RemoteApp Azure dans la réalité ? Consultez le didacticiel [exécuter une application sur n’importe quel appareil n’importe où](remoteapp-anyapp.md) : vous montre comment partager l’accès avec d’autres utilisateurs, y compris le partage des fichiers de base de données. Nous avons également un didacticiel sur la [rendre Office 365](remoteapp-tutorial-o365anywhere.md) exécutés aussi bien sur n’importe quel appareil.

Merci d’avoir conservé avec nous - sauvegarder mois prochain avec des mises à jour.


### <a name="help-us-help-you"></a>Aidez-nous à améliorer vous
Saviez-vous qu’outre évaluation de cet article et présenter des observations vers le bas en dessous, vous pouvez apporter des modifications à l’article lui-même ? Un élément manquant ? Un problème ? Écrire quelque chose qui se trouve juste confusion ? Défiler vers le haut, cliquez sur **Modifier sur GitHub** pour apporter des modifications - ceux soient transférés vers nous pour révision et puis, une fois que nous se les, vous voyez vos modifications et les améliorations ici.
