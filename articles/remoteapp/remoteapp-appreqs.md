
<properties
    pageTitle="Configuration requise de l’application pour Azure RemoteApp | Microsoft Azure"
    description="En savoir plus sur la configuration requise pour les applications que vous souhaitez utiliser dans Azure RemoteApp"
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



# <a name="app-requirements"></a>Configuration requise pour l’application

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

RemoteApp Azure prend en charge la diffusion en continu 32 bits ou 64 bits des applications Windows à partir d’une image de Windows Server 2012 R2. La plupart des 32 bits ou 64 bits basées sur Windows applications existantes exécutées « en l’état est » dans Azure RemoteApp (Services Bureau à distance ou les Services Terminal Server anciennement nommé) environnement. Toutefois, il existe une différence entre en cours d’exécution et l’exécution bien : certaines applications de fonctionnent correctement et ils fonctionnent bien, tandis que d’autres personnes n’est pas le cas. Les informations suivantes fournissent des conseils pour développer des applications dans un environnement de Services Bureau à distance et le test pour garantir la compatibilité.

Conseil : Nous travaillons sur la création de quelques exemples d’utilisation des applications à votre place. Vous verrez nouvelles rubriques qui décrivent l’utilisation de Microsoft Access, QuickBooks et App-V dans RemoteApp.

## <a name="requirements"></a>Configuration requise
Ces trois conditions, si suivi, aideront à votre application de fonctionner correctement dans RemoteApp :

1.  Applications qui répondent à toutes les [conditions de Certification pour les applications de bureau de Windows](https://msdn.microsoft.com/library/windows/desktop/hh749939.aspx) et respectent les [instructions de programmation Services Bureau à distance](https://msdn.microsoft.com/library/aa383490.aspx) aura une compatibilité totale avec RemoteApp.
2.  Applications doivent jamais stocker des données localement sur l’image ou les instances RemoteApp qui peuvent être perdues.  Après avoir créé une collection de sites RemoteApp, les instances sont cloner et sont sans état et doivent contenir uniquement des applications. Stocker des données dans une source externe ou à l’intérieur du profil utilisateur.
3.  Images personnalisées doivent contenir jamais des données qui peuvent être perdues.  

## <a name="testing-your-apps"></a>Test de vos applications
Utilisez ces étapes pour tester les applications :

1.  Installer Windows Server 2012 R2 et votre application
2.  Activer le Bureau à distance
3.  Créez deux comptes d’utilisateurs, utilisateur a et utilisateur b, ajouter les deux comptes d’utilisateurs au groupe de sécurité Bureau à distance.
4.  Vérifier la compatibilité de session multiples en établissant deux sessions RDS simultanées au PC lors du lancement de l’application.
5.  Valider le comportement de l’application

## <a name="application-development-guidelines"></a>Instructions de développement d’applications
Suivez les instructions suivantes pour développer des applications pour RemoteApp.

### <a name="multiple-users"></a>Plusieurs utilisateurs

- Installer une [application pour un seul utilisateur ](https://msdn.microsoft.com/library/aa380661.aspx)peut créer des problèmes dans un environnement multi-utilisateur.
- Applications doivent [stocker des informations spécifiques à l’utilisateur](https://msdn.microsoft.com/library/aa383452.aspx) dans des emplacements spécifiques à l’utilisateur, indépendamment de globale de l’information qui s’applique à tous les utilisateurs.
- RemoteApp utilise plusieurs [espaces de noms des objets noyau](https://msdn.microsoft.com/library/aa382954.aspx); un espace de noms global est utilisé principalement par les services dans les applications client/serveur.
- Il n’est pas possible sans part du principe que le nom de l’ordinateur ou l' [adresse IP](https://msdn.microsoft.com/library/aa382942.aspx) affectée à l’ordinateur sont associés à un seul utilisateur, car plusieurs utilisateurs peuvent être connectés simultanément à un serveur hôte de Session Bureau à distance (hôte de Session RD).

### <a name="performance"></a>Performances
- Désactiver les [effets graphiques](https://msdn.microsoft.com/library/aa380822.aspx) avant d’ajouter votre application à RemoteApp.
- Pour optimiser la disponibilité du processeur pour tous les utilisateurs, désactivez les [tâches en arrière-plan](https://msdn.microsoft.com/library/aa380665.aspx) ou créer des tâches en arrière-plan efficace qui ne sont pas beaucoup de ressources.
- Vous devez régler et équilibrer [l’utilisation des threads](https://msdn.microsoft.com/library/aa383520.aspx) des applications pour un environnement multi-utilisateur, plusieurs processeurs.
- Pour optimiser les performances, il est recommandé pour les applications pour [détecter](https://msdn.microsoft.com/library/aa380798.aspx) si elles s’exécutent dans une session du client.
