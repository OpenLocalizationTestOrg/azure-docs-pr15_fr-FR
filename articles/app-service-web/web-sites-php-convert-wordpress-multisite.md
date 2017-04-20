<properties 
    pageTitle="Convertir WordPress Multisite dans le Service d’application Azure" 
    description="Découvrez comment tirer une application web de WordPress existante créée via la galerie dans Azure et convertir en WordPress Multisite" 
    services="app-service\web" 
    documentationCenter="php" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>



# <a name="convert-wordpress-to-multisite-in-azure-app-service"></a>Convertir WordPress Multisite dans le Service d’application Azure

## <a name="overview"></a>Vue d’ensemble

*Par [Jean Philippe Lobaugh][ben-lobaugh], [Microsoft Open Technologies Inc.][ms-open-tech]*

Dans ce didacticiel, vous allez apprendre à prendre une application web de WordPress existante créée via la galerie dans Azure et le convertir en une installation WordPress Multisite. En outre, vous allez apprendre à affecter un domaine personnalisé à chacun des sous-sites au sein de votre programme d’installation.

Il est supposé que vous disposez d’une installation existante de WordPress. Si vous n’effectuez pas le cas, suivez les instructions fournies dans [créer un site web WordPress à partir de la galerie dans Azure][website-from-gallery].

Convertir un WordPress existant installer site unique pour Multisite est généralement assez simple, et la plupart des étapes initiales ici provenant directement de [Créer un réseau] [ wordpress-codex-create-a-network] page sur le [Code WordPress](http://codex.wordpress.org).

Prise en main.

## <a name="allow-multisite"></a>Autoriser Multisite

Vous devez d’abord activer Multisite via la `wp-config.php` de fichiers avec les **WP\_autoriser\_MULTISITE** constante. Il existe deux méthodes pour modifier vos fichiers d’application web : la première consiste à FTP et la deuxième à Git. Si vous ne connaissez pas la configuration d’une des méthodes suivantes, veuillez consulter les didacticiels suivants :

* [Site web PHP avec MySQL et FTP][website-w-mysql-and-ftp-ftp-setup]

* [Site web PHP avec MySQL et Git][website-w-mysql-and-git-git-setup]

Ouvrir le `wp-config.php` fichier avec l’éditeur de votre choix et ajoutez le code suivant ci-dessus la `/* That's all, stop editing! Happy blogging. */` ligne.

    /* Multisite */

    define( 'WP_ALLOW_MULTISITE', true );

Veillez à enregistrer le fichier et télécharger sur le serveur !

## <a name="network-setup"></a>Mise en réseau

Ouvrez une session dans vers la zone *wp administrateur* de votre site web app et vous devez voir un nouvel élément sous le menu **Outils** appelé **Mise en réseau**. Cliquez sur **Mise en réseau** et renseignez les détails de votre réseau.

![Écran de mise en réseau][wordpress-network-setup]

Ce didacticiel utilise le schéma de site *sous-répertoires* , car elle doit toujours fonctionner et nous configuration des domaines personnalisés pour chaque sous-site plus loin dans le didacticiel. Toutefois, il doit être possible d’un sous-domaine installer si vous mappez un domaine via le [Portail Azure](https://portal.azure.com) et le programme d’installation caractère générique DNS correctement le programme d’installation.

Pour plus d’informations sur vs sous-domaine installations sous-répertoires voir les [Types de réseau multisite] [ wordpress-codex-types-of-networks] article sur le code WordPress.

## <a name="enable-the-network"></a>Activez le réseau

Le réseau est à présent configuré dans la base de données, mais il existe une étape restante pour activer la fonctionnalité réseau. Finaliser la `wp-config.php` paramètres et vérifiez `web.config` correctement achemine chaque site.


Après avoir cliqué sur le bouton **installer** sur la page de *Mise en réseau* , WordPress tentera de mettre à jour la `wp-config.php` et `web.config` fichiers. Toutefois, vous devez toujours vérifier les fichiers pour vous assurer que les mises à jour ont été effectuées. Si ce n’est pas le cas, cet écran, vous proposera les mises à jour nécessaires. Modifier et enregistrer les fichiers.


Une fois ces mises à jour, que vous devez vous déconnecter et vous y connecter l’arrière dans le tableau de bord d’administration wp.

Il doit maintenant être un menu supplémentaire sur l’administrateur de la barre libellée **Mes Sites**. Ce menu permet de contrôler votre réseau via le tableau de bord **D’administrateur de réseau** .

## <a name="adding-custom-domains"></a>Ajouter des domaines personnalisés

Le [Mappage de domaine WordPress MU] [ wordpress-plugin-wordpress-mu-domain-mapping] plug-in rend un clin de œil pour ajouter des domaines personnalisés à un site de votre réseau. Afin que le plug-in fonctionner correctement, vous devez effectuer une installation supplémentaire sur le portail, ainsi qu’au bureau d’enregistrement de votre domaine.

## <a name="enable-domain-mapping-to-the-web-app"></a>Activer le mappage de domaine à l’application web

Le mode plan de [Service d’application](http://go.microsoft.com/fwlink/?LinkId=529714) **Free** n’autorise pas l’ajout des domaines personnalisés aux applications Web. Vous avez besoin passer en mode **partagé** ou **Standard** . Pour cela :

* Connectez-vous au portail Azure et recherchez votre application web. 
* Cliquez sur l’onglet **évoluer** dans **les paramètres**.
* Sous **Général**, sélectionnez *partagé* ou *STANDARD*
* Cliquez sur **Enregistrer**

Vous pouvez recevoir un message vous invitant à vérifier la modification et accepter que votre application web pouvant entraîner maintenant un coût, en fonction de l’utilisation et la configuration que vous définissez.

Il faut quelques secondes pour traiter les nouveaux paramètres, est maintenant un moment idéal pour commencer à configurer votre domaine.

## <a name="verify-your-domain"></a>Vérifier votre domaine

Avant d’Azure Web Apps vous permettra de cliquer mapper un domaine sur le site, vous devez tout d’abord vérifier que vous avez l’autorisation pour mapper le domaine. Pour ce faire, vous devez ajouter un enregistrement CNAME à votre entrée DNS.

* Se connecter au Gestionnaire DNS de votre domaine
* Créer un nouveau CNAME *awverify*
* Pointez sur *awverify* *awverify. YOUR_DOMAIN.azurewebsites.NET*

Il peut prendre un certain temps pour que vos modifications DNS entrent en vigueur complète, donc si les étapes suivantes ne fonctionnent pas immédiatement, accédez prendre un café, puis revenez et réessayez.

## <a name="add-the-domain-to-the-web-app"></a>Ajouter le domaine à l’application web

Revenir à votre application web via le portail d’Azure, cliquez sur **paramètres**, puis cliquez sur **domaines personnalisés et SSL**.

Lorsque les *paramètres SSL* s’affichent, vous verrez les champs où vous allez entrer tous les domaines que vous souhaitez affecter à votre application web. Si un domaine n’est pas répertorié ici, il ne sera pas disponible pour le mappage à l’intérieur WordPress, quelle que soit la façon dont le domaine DNS est le programme d’installation.

![Gérer la boîte de dialogue domaines personnalisés][wordpress-manage-domains]

Après avoir tapé votre domaine dans la zone de texte, Azure vérifie l’enregistrement CNAME que vous avez créé précédemment. Si le DNS n’ait pas complètement propagée, un indicateur rouge apparaît. Si elle a réussi, vous verrez une coche verte. 

Prenez note de l’adresse IP répertoriée dans la partie inférieure de la boîte de dialogue. Vous avez besoin de cette option pour configurer l’enregistrement A de votre domaine.

## <a name="setup-the-domain-a-record"></a>Configurer le domaine d’un enregistrement

Si les autres étapes ont réussi, vous pouvez maintenant affecter le domaine à votre application web Azure via un enregistrement DNS A. 

Il est important de noter que les applications web Azure acceptent CNAME et enregistrements A, mais vous *devez* utiliser un enregistrement A pour activer le mappage de domaine approprié. Un enregistrement CNAME ne peut pas être transféré vers un autre CNAME, c'est-à-dire qu’Azure créé avec YOUR_DOMAIN.azurewebsites.net.

À l’aide de l’adresse IP de l’étape précédente, revenez à votre responsable DNS et configurer l’enregistrement A pour pointer vers cette adresse IP.


## <a name="install-and-setup-the-plugin"></a>Installer et configurer le plug-in

Actuellement, les WordPress Multisite n’a pas d’une méthode intégrée pour mapper les domaines personnalisés. Cependant, il existe un plug-in appelé [WordPress MU domaine mappage] [ wordpress-plugin-wordpress-mu-domain-mapping] qui ajoute la fonctionnalité pour vous. Se connecter à la partie d’administrateur de réseau de votre site et installer le plug-in **WordPress MU domaine mappage** .

Une fois l’installation et l’activation du plug-in, consultez **paramètres** > **Mappage de domaine** pour configurer le plug-in. Dans la première zone de texte, *Adresse IP du serveur*, entrez l’adresse IP servi à la configuration de l’enregistrement A pour le domaine. Définissez les *Options de domaine* que vous souhaitez (les valeurs par défaut sont souvent fins) et cliquez sur **Enregistrer**.

## <a name="map-the-domain"></a>Carte du domaine

Reportez-vous au **tableau de bord** pour le site que vous souhaitez mapper le domaine à. Cliquez sur **Outils** > **Mappage de domaine** et tapez le nouveau domaine dans la zone de texte, cliquez sur **Ajouter**.

Par défaut, le nouveau domaine sera réécrits sur le domaine de site générée automatiquement. Si vous souhaitez que tout le trafic envoyé vers le nouveau domaine, cochez la case *domaine principal de ce blog* avant l’enregistrement. Vous pouvez ajouter un nombre illimité de domaines à un site, mais seule peut être principale.

## <a name="do-it-again"></a>Recommencer

Applications Web Azure permettent d’ajouter un nombre illimité de domaines pour une application web. Pour ajouter un autre domaine, vous devrez exécuter les sections **vérifier votre domaine** et **configurer le domaine d’un enregistrement** pour chaque domaine.  

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courte immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

## <a name="whats-changed"></a>Ce qui a changé
* Pour un guide à la modification de sites Web Application Service voir : [Azure Application Service et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

[ben-lobaugh]: http://ben.lobaugh.net
[ms-open-tech]: http://msopentech.com
[website-from-gallery]: https://www.windowsazure.com/develop/php/tutorials/website-from-gallery/
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-git/#header-1
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/

[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png

 
