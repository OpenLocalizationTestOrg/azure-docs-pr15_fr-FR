<properties
    pageTitle="Créer des Services BizTalk Azure dans le portail Azure | Microsoft Azure"
    description="Apprenez à mettre en service ou créer Azure BizTalk Services dans le portail Azure ; MABS, WABS"
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
    ms.topic="hero-article"
    ms.date="08/15/2016"
    ms.author="mandia"/>



# <a name="create-biztalk-services-using-the-azure-portal"></a>Créer des Services BizTalk en utilisant le portail Azure

Créer des Services BizTalk Azure dans le portail Azure.

> [AZURE.TIP] Pour vous connecter au portail Azure, vous avez besoin d’un compte Azure et abonnement Azure. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit après quelques minutes. Voir [Azure version d’évaluation gratuite](http://go.microsoft.com/fwlink/p/?LinkID=239738).

## <a name="create-a-biztalk-service"></a>Création d’un Service BizTalk
En fonction de l’édition que vous choisissez, pas tous les paramètres de BizTalk Service soient disponibles.

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation en bas, sélectionnez **Nouveau**:  
![Sélectionnez le bouton Nouveau][NEWButton]

3. Sélectionnez les **SERVICES d’application** > **SERVICE BIZTALK** > **créer personnalisé**:  
![Sélectionnez le BizTalk Service, créer personnalisé][NewBizTalkService]

4. Entrez les paramètres de BizTalk Service :

    <table border="1">
    <tr>
    <td><strong>Nom du service BizTalk</strong></td>
    <td>Vous pouvez entrer n’importe quel nom mais être spécifiques. Voici quelques exemples :<br/><br/>
    <em>société</em>. biztalk.windows.net<br/>
    <em>mycompanymyapplication</em>. biztalk.windows.net<br/>
    <em>MyApplication</em>. biztalk.windows.net<br/><br/>«. biztalk.windows.net » sont automatiquement ajoutés au nom que vous entrez. Cela crée une URL qui est utilisée pour accéder à votre BizTalk Service, comme <strong>https://<em>myapplication</em>. biztalk.windows.net</strong>.
    </td>
    </tr>
    <tr>
    <td><strong>Édition</strong></td>
    <td>Si vous êtes dans la phase de développement/test, cliquez sur <strong>développeur</strong>. Si vous êtes dans la phase de production, utilisez le <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">Services BizTalk : graphique des éditions</a> pour déterminer si <strong>Premium</strong>, <strong>Standard</strong>ou <strong>base</strong> est le bon choix pour votre scénario d’entreprise.
    </td>
    </tr>
    <tr>
    <td><strong>Région</strong></td>
    <td>Sélectionnez la région géographique pour héberger votre BizTalk Service.</td>
    </tr>
    <tr>
    <td><strong>URL de domaine</strong></td>
    <td><strong>Facultatif</strong>. Par défaut, l’URL de domaine est <em>YourBizTalkServiceName</em>. biztalk.windows.net. Vous pouvez également entrer un domaine personnalisé. Par exemple, si votre domaine est <em>contoso</em>, vous pouvez entrer : <br/><br/>
    <em>Société</em>. contoso.com<br/>
    <em>MyCompanyMyApplication</em>. contoso.com<br/>
    <em>MyApplication</em>. contoso.com<br/>
    <em>YourBizTalkServiceName</em>. contoso.com<br/>
    </td>
    </tr>
    </table>
Cliquez sur la flèche suivante.

5. Entrez le stockage et paramètres de base de données :

    <table border="1">
    <tr>
    <td><strong>Compte de stockage de surveillance / l’archivage</strong></td>
    <td>Sélectionnez un compte de stockage existant ou créer un nouveau compte de stockage. <br/><br/>Si vous créez un nouveau compte de stockage, entrez le <strong>Nom de compte de stockage</strong>.</td>
    </tr>
    <tr>
    <td><strong>Base de données de suivi</strong></td>
    <td>Si vous utilisez une base de données SQL Azure, il ne peut pas être utilisé par un autre Service de BizTalk. Vous devez le nom d’utilisateur et mot de passe entré lorsque que le serveur de base de données SQL Azure a été créée.<br/><br/><strong>Conseil</strong> Créer le compte de stockage surveillance / l’archivage et le suivi de base de données dans la même région en tant que le BizTalk Service.</td>
    </tr>
    </table>
Cliquez sur la flèche suivante.

6. Entrez les paramètres de base de données :

    <table border="1">
    <tr>
    <td><strong>Nom</strong></td>
    <td>Disponible lors de la <strong>Création d’une nouvelle instance de base de données SQL</strong> est sélectionné dans l’écran précédent.
    <br/><br/>
   Entrez un nom de base de données SQL devant être utilisé par votre BizTalk Service.</td>
    </tr>
    <tr>
    <td><strong>Serveur</strong></td>
    <td>Disponible lors de la <strong>Création d’une nouvelle instance de base de données SQL</strong> est sélectionné dans l’écran précédent.
    <br/><br/>
   Sélectionnez un serveur de base de données SQL existant ou créez-en un nouveau serveur de base de données SQL.</td>
    </tr>
    <tr>
    <td><strong>Nom de connexion Server</strong></td>
    <td>Entrez le nom d’utilisateur.</td>
    </tr>
    <tr>
    <td><strong>Mot de passe serveur</strong></td>
    <td>Entrez le mot de passe.</td>
    </tr>
    <tr>
    <td><strong>Région</strong></td>
    <td>Disponible quand l’option <strong>créer une nouvelle instance de base de données SQL</strong> est sélectionnée. Sélectionnez la région géographique pour héberger votre base de données SQL.</td>
    </tr>
    </table>

Activez la case à cocher pour exécuter l’Assistant. L’icône de progression s’affiche :  
![Icône de progression s’affiche lorsque vous avez terminé][ProgressComplete]

Lorsque vous avez terminé, le BizTalk Service Azure est créé et vous êtes prêt pour vos applications. Les paramètres par défaut suffisent. Si vous souhaitez modifier les paramètres par défaut, sélectionnez **Les SERVICES BIZTALK** dans le volet de navigation gauche, puis votre BizTalk Service. Paramètres supplémentaires sont affichés dans les [onglets de tableau de bord, moniteur et l’échelle](biztalk-dashboard-monitor-scale-tabs.md) dans la partie supérieure.

En fonction de l’état du BizTalk Service, il existe certaines opérations ne peuvent pas être effectuées. Pour obtenir la liste de ces opérations, accédez à [BizTalk Services état graphique](biztalk-service-state-chart.md).


## <a name="post-provisioning-steps"></a>Étapes postérieures à la mise en service

-  [Installer le certificat sur un ordinateur local](#InstallCert)
-  [Ajouter un certificat opérationnelle](#AddCert)
-  [Obtenir de l’espace de noms de contrôle d’accès](#ACS)

#### <a name="InstallCert"></a>Installer le certificat sur un ordinateur local
Dans le cadre du BizTalk Service mise en service, un certificat auto-signé est créé et associé à votre abonnement BizTalk Service. Vous devez télécharger ce certificat et l’installer sur un ordinateur à partir de l’endroit où vous déployez des applications de BizTalk Service ou envoyez des messages à un point de terminaison du BizTalk Service.

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Sélectionnez **SERVICES BIZTALK** dans le volet de navigation gauche, puis votre abonnement BizTalk Service.
3. Sélectionnez l’onglet **tableau de bord** .
4. Sélectionnez **Télécharger le certificat SSL**:  
![Modifier le certificat SSL][QuickGlance]
5. Double-cliquez sur le certificat et exécuter l’Assistant pour installer le certificat. Veillez à qu'installer le certificat sous le magasin **d’Autorités de certification racine de confiance** .

#### <a name="AddCert"></a>Ajouter un certificat opérationnelle
Le certificat auto-signé est créé automatiquement lors de la création de Services BizTalk est destiné à utiliser dans les environnements de développement uniquement. Scénarios de production, remplacez-le par un certificat opérationnelle.

1. Sous l’onglet **tableau de bord** , sélectionnez le **Certificat SSL mise à jour**.
2. Accédez à votre certificat SSL privé (*CertificateName*.pfx) qui inclut le nom de votre BizTalk Service, entrez le mot de passe, puis cliquez sur la coche.

#### <a name="ACS"></a>Obtenir de l’espace de noms de contrôle d’accès

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Sélectionnez **SERVICES BIZTALK** dans le volet de navigation gauche, puis votre BizTalk Service.
3. Dans la barre des tâches, sélectionnez **Informations de connexion**:  
![Sélectionnez les informations de connexion][ACSConnectInfo]

4. Copiez les valeurs de contrôle d’accès.

Lorsque vous déployez un projet de BizTalk Service à partir de Visual Studio, vous entrez cet espace de noms de contrôle d’accès. L’espace de noms de contrôle d’accès est automatiquement créée pour votre BizTalk Service.

Les valeurs de contrôle d’accès peuvent être utilisées avec n’importe quelle application. Lorsque les Services BizTalk Azure est créé, cet espace de noms de contrôle d’accès contrôle l’authentification avec votre déploiement de BizTalk Service. Si vous souhaitez modifier l’abonnement ou gérer l’espace de noms, sélectionnez **ACTIVE DIRECTORY** dans le volet de navigation gauche, puis sélectionnez votre espace de noms. La barre des tâches répertorie les options disponibles.

Cliquez sur **Gérer** pour ouvrir le portail de gestion de contrôle de l’accès. Dans le portail de gestion de contrôle de l’accès, le BizTalk Service utilise des **identités de Service**:  
![Service ACS identités dans le niveau d’accès contrôlent portail de gestion][ACSServiceIdentities]

L’identité de service de contrôle d’accès est un ensemble d’informations d’identification qui permettent aux clients de s’authentifier directement auprès d’un contrôle d’accès et recevoir un jeton ou des applications.

> [AZURE.IMPORTANT]Le BizTalk Service utilise le **propriétaire** de l’identité du service par défaut et le **mot de passe** . Si vous utilisez la valeur de clé symétrique au lieu de la valeur de mot de passe, l’erreur suivante peut se produire.<br/><br/>*Impossible de créer le compte de Service de gestion des accès contrôle avec les informations d’identification spécifiées*

[Gestion de votre ACS Namespace](https://msdn.microsoft.com/library/azure/hh674478.aspx) répertorie quelques instructions et des recommandations.

## <a name="requirements-explained"></a>Configuration requise expliqués

Ces exigences ne s’appliquent pas à l’édition gratuite.
<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Vous avez besoin</strong></td>
        <td><strong>Pourquoi vous en avez besoin</strong></td>
</tr>
<tr>
<td>Abonnement Azure</td>
<td>L’abonnement détermine qui peut se connecter au portail Azure. Le titulaire du compte crée l’abonnement à <a HREF="https://account.windowsazure.com/Subscriptions">Azure abonnements</a>.
<br/><br/>
Le compte Azure peut avoir plusieurs abonnements et peut être géré par toute personne qui est autorisé. Par exemple, votre boîtier du compte Azure crée un abonnement nommé <em>BizTalkServiceSubscription</em> et donne aux administrateurs BizTalk au sein de votre société (par exemple, ContosoBTSAdmins@live.com) accès à cet abonnement. Dans ce scénario, les administrateurs BizTalk connectez-vous au portail Azure et droits d’administrateur complets pour tous les services hébergés dans l’abonnement, y compris les Services BizTalk Azure. Les administrateurs BizTalk ne sont pas les détenteurs des comptes Azure et par conséquent ne peuvent pas accéder à toute information de facturation.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">Gérer les abonnements et les comptes de stockage dans le portail Azure</a> donne des informations supplémentaires.
</td>
</tr>
<tr>
<td>Base de données SQL Azure</td>
<td>Stocke les tables, les vues et les procédures stockées utilisées par le BizTalk Service, y compris les données de suivi.
<br/><br/>
Lorsque vous créez un BizTalk Service, vous pouvez utiliser un serveur SQL Azure, base de données SQL Azure, ou créer automatiquement une nouvelle base de données ou un serveur.
<br/><br/>
L’échelle de base de données SQL est configuré automatiquement. En règle générale, l’échelle par défaut est suffisant pour un BizTalk Service. Modification de l’échelle affecte les tarifs. Voir <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930">comptes et facturation dans la base de données SQL Azure</a>
<br/><br/>
<strong>Notes</strong>
<br/>
<ul>
<li> Lorsque vous créez un nouveau Azure SQL Server et base de données, Azure Services est activé automatiquement. Le BizTalk Service nécessite des Services Azure être activé.</li>
<li>Si vous créez une nouvelle base de données SQL Azure sur un serveur SQL Azure, les règles de pare-feu du serveur ne sont pas modifiées. Par conséquent, il est possible d’autres Services Azure n’ont pas accès aux bases de données du serveur.</li>
</ul>
</td>
</tr>
<tr>
<td>Espace de noms de contrôle d’accès Azure</td>
<td>Authentifie avec les Services BizTalk Azure. Lorsque vous déployez un projet de BizTalk Service à partir de Visual Studio, vous entrez cet espace de noms de contrôle d’accès. Lorsque vous créez un BizTalk Service, l’espace de noms de contrôle d’accès est automatiquement créée.</td>
</tr>

<tr>
<td>Compte de stockage Azure</td>
<td>Permet d’accéder aux tables, des objets BLOB et les files d’attente utilisées par votre BizTalk Service pour enregistrer les éléments suivants :

<ul>
<li>Fichiers journaux qui contrôlent le BizTalk Service. Le résultat de la surveillance s’affiche également dans l’onglet **analyse** dans le portail Azure.</li>
<li>Lorsque vous créez un X12 ou AS2 accord entre partenaires, vous pouvez activer la fonctionnalité d’archivage stocker des propriétés de message. Ces données sont enregistrées dans le compte de stockage.</li>
</ul>
<br/>
Lorsque vous créez un BizTalk Service, vous pouvez utiliser un compte de stockage existant ou créer automatiquement un nouveau compte de stockage.
<br/><br/>
Les paramètres de stockage par défaut sont suffisantes pour un BizTalk Service.
<br/><br/>
Lorsque vous créez un compte de stockage, une clé primaire et clé secondaire sont automatiquement créés. Ces touches contrôlent l’accès à votre compte de stockage. Le BizTalk Service utilise automatiquement la clé primaire.
<br/><br/>
Pour plus d’informations, voir <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">le stockage</a> .
</td>
</tr>

<tr>
<td>Certificat privé SSL</td>
<td>
Création d’un BizTalk Service Azure, une URL HTTPS qui inclut le nom de votre BizTalk Service est également créée. Cette URL est automatiquement configurée pour utiliser un certificat auto-signé uniquement au développement. Pour la production, vous avez besoin d’un certificat SSL privé.
<br/><br/>
<strong>Informations sur le certificat SSL importantes</strong>

<ul>
<li>La date d’expiration du certificat doit être inférieure à 5 ans.</li>
<li>Tous les certificats privés exigent un mot de passe. Ce mot de passe et pour obtenir les meilleurs résultats, partager ce mot de passe avec vos administrateurs.</li>
<li>Certificats auto-signé sont utilisés dans un environnement de test/développement. Lorsque vous utilisez des certificats auto-signé, importer le certificat dans votre magasin de certificats personnels et le magasin de certificats des autorités de Certification racine de confiance.</li>
</ul>
<br/>Lorsque vous envoyez la demande de certificat de production à votre autorité de certification, donnez les propriétés du certificat suivants :
<br/>

<ul>
<li><strong>Utilisation de clé améliorée</strong>: au minimum, Azure BizTalk Services requiert une authentification de serveur.</li>
<li><strong>Nom commun</strong>: entrez le nom de domaine complet (FQDN) de votre URL du Service BizTalk Azure. Voir <a HREF="#BizTalk">Création d’un BizTalk Service</a> dans cet article.</li>
</ul>
<br/>
Un certificat nouveau ou différent peut être ajouté après avoir créé le BizTalk Service.
</td>
</tr>
</table>



## <a name="hybrid-connections"></a>Connexions hybride

Lorsque vous créez un BizTalk Service Azure, l’onglet **Connexions hybride** est disponible :

![Onglet Connexions hybride][HybridConnectionTab]

Connexions hybride sont utilisées pour connecter un site Web Azure ou service mobile Azure à toutes les ressources locales qui utilise un port TCP statique, telles que SQL Server, MySQL, API Web HTTP, les Services mobiles et la plupart des Services Web personnalisés.  Connexions hybride et le Service de carte BizTalk sont différents. Le Service de carte BizTalk utilisé pour la connexion Azure BizTalk Services à un système de ligne d’entreprise (métier) en local.

 Voir [Connexions hybride](integration-hybrid-connection-overview.md) pour plus d’informations, notamment la création et gestion des connexions hybride.


## <a name="next-steps"></a>Étapes suivantes

Maintenant qu’un BizTalk Service est créé, familiarisez-vous avec les différents [Services BizTalk : les onglets du tableau de bord, surveiller et échelle](biztalk-dashboard-monitor-scale-tabs.md). Votre BizTalk Service est prêt pour vos applications. Pour commencer la création d’applications, accédez aux [Services BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Voir aussi
- [BizTalk Services : Graphique des éditions](biztalk-editions-feature-chart.md)<br/>
- [Services BizTalk : Graphique de l’état](biztalk-service-state-chart.md)<br/>
- [Les Services BizTalk : Sauvegarder et restaurer](biztalk-backup-restore.md)<br/>
- [Services BizTalk : la limitation](biztalk-throttling-thresholds.md)<br/>
- [BizTalk Services : Nom de l’émetteur et la clé de l’émetteur](biztalk-issuer-name-issuer-key.md)<br/>
- [Comment puis-je à l’aide de démarrer le SDK Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
- [Connexions hybride](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
