
<properties 
    pageTitle="Sécurisation de l’accès à Azure RemoteApp et plus | Microsoft Azure"
    description="Découvrez comment sécuriser l’accès aux Azure RemoteApp à l’aide d’accès conditionnel dans Azure Active Directory"
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />

# <a name="securing-access-to-azure-remoteapp-and-beyond"></a>Sécurisation de l’accès à Azure RemoteApp et plus

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Dans cet article, nous donne un aperçu de la façon dont un administrateur peut configurer un canal un accès sécurisé à partir de l’utilisateur final, via Azure RemoteApp et se terminant par une ressource sécurisée telle qu’une base de données SQL ou une autre application principale. L’objectif est de s’assurer que seuls les utilisateurs autorisés répondant aux conditions souhaitées puissent accéder aux applications à distance, et que la principale sécurisé n’est accessible à partir de l’environnement Azure RemoteApp contrôlé et non à partir d’autres emplacements.

Il existe 3 zones principales, que l’administrateur doit examiner :

![Considérations relatives à la Azure RemoteApp accès conditionnel](./media/remoteapp-secureaccess/ra-conditionalenvironment.png)

Lecture sur des informations et des réponses à ces questions.

## <a name="who-can-access-the-collection"></a>Qui peut accéder à la collection de sites ?
L’administrateur sélectionne les utilisateurs qui peuvent accéder aux applications à distance dans la collection de sites. Vous pouvez utiliser Azure Active Directory (AD Azure) comptes professionnels ou scolaires (appelés auparavant, « comptes d’organisation ») ou les comptes Microsoft (par exemple, @outlook.com). La plupart des scénarios d’entreprise utilisent comptes Azure AD ; ils vous permettent d’utiliser access conditionnelle fonctionnalités présentées plus loin et sont également le choix uniquement pour les collections de sites à un domaine. Le reste de cet article suppose que vous utilisez les comptes Azure Active Directory avec Azure RemoteApp.

**Ce que nous avons accompli :**

À l’aide de comptes Azure AD pour contrôler l’accès à Azure RemoteApp permet de créer deux choses :

1.  Nous toujours savoir qui peut accéder aux applications nous ont publiés et accéder aux principaux ces applications se connectent à.
2.  Nous contrôler la publicité Azure sous-jacentes afin que nous pouvons créer et supprimer des comptes d’utilisateurs définir des stratégies de mot de passe, utilisent l’authentification multifacteur, etc.. 

## <a name="how-is-the-collection-accessed-from-where"></a>Comment accéder à la collection de sites ? À partir de laquelle ?
Les administrateurs veulent définir des stratégies pour l’accès à un environnement via Internet public, tels que RemoteApp Azure. Par exemple, ils souhaitent assurer aux utilisateurs l’accès à l’environnement d’en dehors du réseau d’entreprise à utiliser l’authentification multifacteur (MFA) pour accéder ; ou, peut-être qu’ils doivent être bloqués complètement.

Azure RemoteApp les administrateurs peuvent utiliser les fonctionnalités disponibles via Azure AD Premium pour définir les stratégies d’accès conditionnel pour leur environnement RemoteApp Azure. Ils permet également enrichi rapports et les fonctions d’alerte pour contrôler la façon dont l’environnement est effectué.

### <a name="how-to-set-up-conditional-access-for-azure-remoteapp"></a>Comment configurer l’accès conditionnel pour Azure RemoteApp
Nous allons expliquant en détail un exemple de scénario – la RemoteApp Azure administrateur souhaite bloquer l’accès à l’environnement lorsque les utilisateurs sont en dehors du réseau d’entreprise.

>[AZURE.NOTE] Nous part du principe que vous avez mis à niveau Azure AD vers le niveau Premium et que vous avez créé au moins une collection de sites RemoteApp Azure.

1.  Dans le portail Azure cliquez sur l’onglet **Active Directory** . Cliquez ensuite sur le répertoire que vous voulez configurer.

    N’oubliez pas : Accès conditionnel est une propriété de votre répertoire et non de RemoteApp Azure, de sorte que toute la configuration est effectuée au niveau du répertoire. Cela signifie également que vous devez être l’administrateur d’annuaire pour effectuer ces modifications.

2.  Cliquez sur **Applications**, puis cliquez sur **Microsoft Azure RemoteApp** à configurer l’accès conditionnelle. Notez que vous pouvez configurer l’accès conditionnel pour chaque application « logiciel en tant que service » dans votre annuaire séparément.
![La configuration d’accès conditionnel pour Azure RemoteApp](./media/remoteapp-secureaccess/ra-conditionalaccessscreen.png)
 

3.  Sous l’onglet **configurer** , définissez **Activer les règles d’accès** sur activé.
![Activer les règles d’accès pour Azure RemoteApp](./media/remoteapp-secureaccess/ra-enableaccessrules.png)
 

4.  Vous pouvez maintenant configurer des règles et sélectionnez la personne à leur appliquer :

    1. Cliquez sur **Bloquer l’accès pas au bureau** complètement empêcher les utilisateurs d’accéder aux RemoteApp Azure en dehors de l’environnement réseau que vous spécifiez.
    2. Cliquez sur l’option ci-dessous pour définir les plages d’adresses IP qui constituent votre « réseau approuvé ». Tout en dehors de celles sera rejeté.

5.  Testez votre configuration en lançant le client Azure RemoteApp à partir d’une adresse IP en dehors de la plage spécifiée. Une fois que vous êtes à l’aide de vos informations d’identification Azure AD vous devriez voir un message comme suit :

![Refuser l’accès à Azure RemoteApp](./media/remoteapp-secureaccess/ra-accessdenied.png)
 

### <a name="future-conditional-access-features"></a>Fonctionnalités d’accéder à l’avenir conditionnelle 
L’équipe Azure Active Directory fonctionne sur les nouvelles fonctionnalités dans Access conditionnelle. Les administrateurs pourront créer des règles selon l’emplacement des types de règles au-delà de réseau. Une version d’évaluation de la nouvelle fonctionnalité sera disponible bientôt.

### <a name="how-to-monitor-access-to-azure-remoteapp"></a>Comment contrôler l’accès à Azure RemoteApp
Une grande fonctionnalité à utiliser en parallèle avec accès conditionnel est la fonctionnalité de création de rapports Azure Active Directory Premium. Vous pouvez utiliser des rapports pour contrôler les personnes qui accède à votre environnement et détecter toute activité suspecte.

Par exemple, vous pouvez afficher les noms des utilisateurs pour lesquels accessible RemoteApp Azure, le nombre de fois qu’il et quand.

1.  Dans le portail Azure, cliquez sur **Active Directory**, puis cliquez sur dans l’annuaire.

2.  Accédez à l’onglet **rapports** .

3.  Dans la liste des rapports, sélectionnez **l’utilisation des applications** sous **applications intégrées**.

    Vous verrez certaines des statistiques agrégées pour Azure RemoteApp. 
![Statistiques accès Azure RemoteApp agrégés](./media/remoteapp-secureaccess/ra-accessstats.png)
 
5.  Cliquez sur l’application pour afficher des informations sur les utilisateurs qui accèdent à Azure RemoteApp.
![Statistiques de l’accès utilisateur pour Azure RemoteApp](./media/remoteapp-secureaccess/ra-userstats.png)
 
### <a name="summary"></a>Résumé
Avec Azure Active Directory Premium, vous pouvez définir des règles d’accès aux Azure RemoteApp (et autres logiciels en tant qu’une applications de service disponibles via Azure AD). Les règles sont actuellement limitées aux stratégies de selon l’emplacement réseau mais seront étendus à l’avenir d’autres aspects de gestion d’entreprise.

Azure AD Premium offre également de rapports et fonctionnalités d’étendre davantage le contrôle de l’administrateur de contrôle possède sur leur environnement RemoteApp Azure.

## <a name="how-do-i-make-sure-my-secure-resource-is-accessible-only-from-my-azure-remoteapp-environment"></a>Comment m’assurer que mon ressource sécurisée est accessible uniquement à partir de mon environnement RemoteApp Azure ?
Dans les sections précédentes de cet article nous axée sur la sécurisation de l’accès à l’environnement RemoteApp Azure. Nous avons accompli qui en choisissant les utilisateurs autorisés à accéder et en configurant des règles d’accès pour mieux contrôler comment ils peuvent utiliser le service.

Un scénario courant pour les déploiements Azure RemoteApp est que les applications à distance ont besoin communiquer avec une ressource principale, par exemple une base de données SQL. Cette ressource est hébergée localement (par exemple dans un réseau d’entreprise) ou dans le cloud (par exemple dans Azure IaaS). Les administrateurs souhaitent pour vous assurer que la ressource principale uniquement sont accessibles par les applications déployées via Azure RemoteApp et non par exemple par une application en cours d’exécution directement sur PC d’un utilisateur et accéder à via Internet public. Azure RemoteApp est souvent considéré comme l’environnement centralisée et sécurisée et donc le chemin d’accès uniquement aux utilisateurs doivent d’interagir avec la ressource principale.

La solution consiste à placer l’environnement Azure RemoteApp et la ressource sécurisée dans l’API Azure virtuel réseau (VNET) même. Si la ressource est dans un autre site, vous pouvez établir une connexion VPN site à site, par exemple pour créer un VNet sur le centre de données Azure et de l’environnement local du client.

RemoteApp Azure prend en charge les deux types de déploiements de collection de sites dans laquelle vous pouvez fournir vos propres VNET :

-   Non liés à un domaine : les applications ont des « ligne de vue « des autres ressources dans le VNET. Par exemple, cela peut servir à connecter des applications à une base de données SQL qui utilise l’authentification SQL (applications authentifier l’utilisateur directement par rapport à la base de données)

-   À un domaine : machines virtuelles utilisées par Azure RemoteApp sont jointes à un contrôleur de domaine dans le VNET. Cela est utile lorsque les applications doivent s’authentifier sur un contrôleur de domaine Windows afin d’obtenir l’accès à une ressource principale.
![Une collection de sites à un domaine dans Azure RemoteApp](./media/remoteapp-secureaccess/ra-domainjoined.png)
 
### <a name="how-to-create-a-secure-connection-between-azure-and-my-on-premises-environment"></a>Comment créer une connexion sécurisée entre Azure et mon environnement local
Il existe plusieurs options de configuration pour vous connecter vos environnements Azure et en local. Une bonne vue d’ensemble des options est disponible ici.

Avec Azure RemoteApp vous devez commencer par configurer votre VNet et utilisez-la pendant le processus de création de votre collection de sites. 

## <a name="the-complete-solution"></a>Solution complète
L’illustration ci-dessous montre la solution complète où nous avons intégré un canal un accès sécurisé à partir de l’utilisateur final, via Azure RemoteApp (ARA), dans la ressource serveur principal.
![Sécuriser Azure RemoteApp](./media/remoteapp-secureaccess/ra-secureoverview.png) dans étape 1 nous les utilisateurs sélectionnés et créé les règles d’accès qui régissent comment ARA sont accessibles. Dans l’exemple ci-dessous, nous uniquement autoriser l’accès pour les utilisateurs qui travaillent à partir du réseau d’entreprise. Les utilisateurs non conforme ne sera pas en mesure d’accéder à l’environnement ARA du tout.
Dans « Étape 2 » nous avons exposée la ressource principal uniquement par le biais de la configuration VNet/VPN qui nous contrôler. RemoteApp Azure a été placée dans la même VNet. Le résultat final est que la ressource peut uniquement être accessible via l’environnement ARA.


