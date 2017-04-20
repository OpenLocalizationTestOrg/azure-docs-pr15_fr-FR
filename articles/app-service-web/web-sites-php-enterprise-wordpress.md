<properties
    pageTitle="Entreprise WordPress Azure service d’application | Microsoft Azure"
    description="Découvrez comment héberger un site d’entreprise WordPress service d’application Azure"
    services="app-service\web"
    documentationCenter=""
    authors="sunbuild"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.devlang="php"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="web"
    ms.date="10/24/2016"
    ms.author="sumuth"/>

# <a name="enterprise-class-wordpress-on-azure-app-service"></a>Entreprise WordPress Azure service d’application

Azure Application Service fournit un environnement scalable, sécurisé et facile à utiliser pour mission critique à grande échelle [WordPress] [ wordpress] sites. Microsoft lui-même exécute des sites d’entreprise, tels [Office] [ officeblog] et [Bing] [ bingblog] blogs. Ce document vous montre comment vous pouvez utiliser Azure Application Service Web Apps pour établir et maintenir une échelle de l’entreprise, site WordPress sur le nuage qui peut gérer un grand nombre de visiteurs.

## <a name="architecture-and-planning"></a>Planification et architecture

Une installation WordPress base comporte uniquement deux conditions.

* **Base de données MySQL** - disponibles via [ClearDB sur le marché Azure][cdbnstore], ou vous pouvez gérer votre propre installation MySQL sur Machines virtuelles Azure à l’aide de [Windows] [ mysqlwindows] ou [Linux][mysqllinux].

  > [AZURE.NOTE] ClearDB fournit plusieurs configurations MySQL, avec les caractéristiques de performances différentes pour chaque configuration. Voir la [Banque d’Azure] [ cdbnstore] pour plus d’informations sur les offres fourni par le biais du store Azure ou directement comme illustré sur [site Web ClearDB](http://www.cleardb.com/pricing.view).

* **PHP 5.2.4 ou supérieur** -Azure Application Service fournissent actuellement [PHP versions5.5 5.4, 5.6][phpwebsite].

    > [AZURE.NOTE] Nous vous recommandons de toujours en cours d’exécution sur la dernière version de PHP pour vous assurer que vous possédez les derniers correctifs de sécurité.

### <a name="basic-deployment"></a>Déploiement de base

Utilise simplement la configuration minimale requise, vous pouvez créer une solution simple au sein d’une région Azure.

![une application web Azure et la base de données MySQL hébergé dans une seule région Azure][basic-diagram]

Alors que cela afin de pouvoir horizontale votre application en créant plusieurs instances d’applications Web du site, tout est hébergé dans les centres de données dans une zone géographique spécifique. Les visiteurs d’en dehors de cette zone peuvent voir délais de réponse lors de l’utilisation du site, et si les centres de données dans cette zone aller vers le bas, c’est votre application.


### <a name="multi-region-deployment"></a>Déploiement de plusieurs région

À l’aide Azure [Le trafic Gestionnaire][trafficmanager], il est possible d’adapter votre site WordPress dans plusieurs régions géographiques tout en garantissant que d’une URL pour les visiteurs. Tous les visiteurs sont fournis le trafic via le Gestionnaire de périphériques et sont ensuite routés vers une région basée sur la configuration d’équilibrage de charge.

![une application web Azure, hébergée dans plusieurs régions, à l’aide de routeur CDBR disponibilité router vers MySQL au sein de régions][multi-region-diagram]

Au sein de chaque région, le site WordPress serait toujours être mise à l’échelle sur plusieurs instances d’applications Web, mais cette mise à l’échelle est région spécifique ; zones de trafic élevé peuvent être adaptés différemment de faible trafic sont ceux qui.

Réplication et routage vers plusieurs bases de données MySQL peuvent être effectuées à l’aide de ClearDB [CDBR haute disponibilité routeur] [ cleardbscale] (comme illustré gauche) ou [MySQL Cluster CGE][cge].

### <a name="multi-region-deployment-with-media-storage-and-caching"></a>Déploiement de plusieurs région avec supports de stockage et la mise en cache
Si le site accepte les téléchargements, ou les fichiers multimédias hôte, utilisez le stockage Blob Azure. Si vous avez besoin de la mise en cache, vous pouvez [Redis cache][rediscache], [Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/)ou l’une des autres offres mise en cache dans le [Magasin d’Azure](http://azure.microsoft.com/gallery/store/).

![une application web Azure, hébergée dans plusieurs régions, à l’aide de routeur CDBR haute disponibilité pour MySQL, avec Cache géré, stockage d’objets Blob et CDN][performance-diagram]

Stockage d’objets BLOB étant geo-répercuté sur régions par défaut, vous n’avez pas à vous soucier de la réplication de fichiers sur tous les sites. Vous pouvez également activer le [Réseau de Distribution de contenu (CDN)] Azure[ cdn] pour le stockage Blob, qui distribue des fichiers à la fin des nœuds rapprocher à vos visiteurs.

### <a name="planning"></a>Planification

#### <a name="additional-requirements"></a>Configuration requise supplémentaire

Pour cela... | Utilisez cela...
------------------------|-----------
**Télécharger ou stocker des fichiers volumineux** | [Plug-in WordPress pour l’utilisation de stockage d’objets Blob][storageplugin]
**Envoyer des messages électroniques** | [SendGrid] [ storesendgrid] et le [plug-in WordPress pour l’utilisation de SendGrid][sendgridplugin]
**Noms de domaine personnalisé** | [Configurer un nom de domaine personnalisé dans le Service d’application Azure][customdomain]
**HTTPS** | [Activer HTTPS pour une application web dans le Service d’application Azure][httpscustomdomain]
**Validation avant production** | [Configurer la mise en environnements pour les applications web dans le Service d’application Azure][staging] <p>Passer d’une application web intermédiaire vers production également déplace la configuration WordPress. Vous devez vous assurer que tous les paramètres sont mises à jour sur la configuration requise pour votre application de production avant de changer l’application intermédiaire en production.</p>
**Surveillance et résolution des problèmes** | [Activer la journalisation des diagnostics pour les applications web dans le Service d’application Azure] [ log] et [Surveiller des applications Web dans le Service d’application Azure][monitor]
**Déployez votre site** | [Déployer une application web dans le Service d’application Azure][deploy]

#### <a name="availability-and-disaster-recovery"></a>Disponibilité et récupération d’urgence

Pour cela... | Utilisez cela...
------------------------|-----------
**Sites de solde de chargement** ou **geo-distribuer sites** | [Acheminer le trafic avec le Gestionnaire de trafic Azure][trafficmanager]
**Sauvegarder et restaurer** | [Sauvegarder une application web dans le Service d’application Azure] [ backup] et [restaurer une application web dans le Service d’application Azure][restore]

#### <a name="performance"></a>Performances

Performances dans le cloud sont obtenues principalement à l’aide de la mise en cache et horizontale ; Toutefois la mémoire, la bande passante et autres attributs des applications Web d’hébergement doivent être considéré comme.

Pour cela... | Utilisez cela...
------------------------|-----------
**Comprendre les fonctionnalités de l’instance Service d’application** | [Pour plus d’informations, y compris les fonctionnalités de niveaux de Service d’application][websitepricing]
**Ressources du cache** | [Redis cache][rediscache], [Memcache Cloud](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/)ou l’une des autres offres mise en cache dans le [Magasin d’Azure](/gallery/store/)
**Faites évoluer votre application** | [Adapter une application web dans Azure Application Service] [ websitescale] et le [Routage de disponibilité haute ClearDB][cleardbscale]. Si vous choisissez d’héberger et de gérer vos propres installation MySQL, vous devez prendre en compte [MySQL Cluster CGE] [ cge] pour horizontale

#### <a name="migration"></a>Migration

Il existe deux méthodes de migration d’un site existant WordPress Azure application service.

* ** [WordPress exporter] [ export] ** -cette action exporte le contenu de votre blog, lequel peut ensuite être importé vers un nouveau site WordPress service d’application Azure à l’aide du [plug-in d’importer WordPress][import].

    > [AZURE.NOTE] Bien que ce processus permette de migrer du contenu, il ne migre pas les plug-ins, thèmes ou autres personnalisations. Il doivent être installés à nouveau manuellement.

* **Migration manuelle** - [sauvegarder votre site] [ wordpressbackup] et [base de données][wordpressdbbackup], puis manuellement restaurez-la à une application web dans le Service d’application Azure et base de données MySQL pour migrer des sites personnalisables et d’éviter de l’installation manuelle des plug-ins, thèmes et les personnalisations fastidieuses associée.

## <a name="step-by-step-instructions"></a>Obtenir des instructions étape par étape

### <a name="create-a-wordpress-site"></a>Créer un site WordPress

1. Utiliser la [Azure Marketplace] [ cdbnstore] pour créer une base de données MySQL de la taille que vous avez identifiés dans la section [planification et Architecture](#planning) , dans l’ou les régions que vous devez héberger votre site.

2. Suivez les étapes décrites dans [créer une application web WordPress dans le Service d’application Azure] [ createwordpress] pour créer une application web WordPress. Lorsque vous créez l’application web, sélectionnez **utiliser une base de données MySQL existante** , sélectionnez la base de données créé à l’étape 1.

Si vous migrez un site WordPress existant, voir [migrer un site existant WordPress vers Azure](#Migrate-an-existing-WordPress-site-to-Azure) après la création d’une nouvelle application web.

### <a name="migrate-an-existing-wordpress-site-to-azure"></a>Migrer un site existant WordPress vers Azure

Comme indiqué dans la section [planification et Architecture](#planning) , il existe deux façons de migrer un site WordPress.

* **exporter et importer** - pour les sites sans personnalisation, ou l’endroit où vous voulez simplement déplacer le contenu.

* **sauvegarder et restaurer** - pour les sites avec un grand nombre de personnalisation de l’endroit où vous voulez déplacer tout le contenu.

Utilisez une des sections suivantes pour migrer votre site.

#### <a name="the-export-and-import-method"></a>La méthode d’importation et exportation

1. Utiliser [d’exportation WordPress] [ export] pour exporter votre site existant.

2. Créer une application web à l’aide de la procédure dans la section [créer un site WordPress](#Create-a-new-WordPress-site) .

3. Se connecter à votre site WordPress dans des applications Web, puis cliquez sur **plug-ins** -> **Ajouter nouveau**. Rechercher et installer le plug-in **WordPress Importer** .

4. Après avoir installé le plug-in importer, cliquez sur **Outils** -> **Importer**et puis sélectionnez **WordPress** pour utiliser le plug-in de WordPress importer.

5. Dans la page **Importer WordPress** , cliquez sur **Choisir un fichier**. Recherchez le fichier WXR exporté à partir de votre site existant WordPress, puis sélectionnez **Télécharger le fichier et importer**.

6. Cliquez sur **Envoyer**. Vous êtes invité que l’importation a réussi.

8. Une fois que vous avez terminé toutes ces étapes, redémarrez votre site à partir de sa carte de l’application web dans le [portail Azure][mgmtportal].

Après avoir importé le site, vous devrez peut-être effectuer les étapes suivantes pour activer les paramètres ne pas contenues dans le fichier d’importation.

Si vous utilisiez ceci... | Procédez comme ceci :
------------------ | ----------
**Permanents** | Dans le tableau de bord WordPress du nouveau site, cliquez sur **paramètres** -> **permanents** et mettez-le à jour la structure permanents
**liens image/média** | Pour mettre à jour des liens vers le nouvel emplacement, utilisez le [plug-in velours bleus mettre à jour les URL][velvet], un outil Rechercher et remplacer, ou manuellement dans votre base de données
**Thèmes** | Accédez à **apparence** -> mise à jour le thème du site selon vos besoins et**thème**
**Menus** | Si votre thème prend en charge les menus, des liens vers votre page d’accueil ait l’ancien répertoire sous-adresse incorporé dans les. Accédez à **apparence** -> **Menus** et les mettre à jour

#### <a name="the-backup-and-restore-method"></a>La méthode de sauvegarde et de restauration

1. Sauvegarder votre WordPress existantes du site en utilisant les informations à [des sauvegardes WordPress][wordpressbackup].

2. Sauvegarder votre base de données existante en utilisant les informations à [sauvegarder votre base de données][wordpressdbbackup].

3. Créer une base de données et restaurer la sauvegarde.

    1. Acheter une nouvelle base de données à partir de la [Azure Marketplace][cdbnstore], ou d’installation d’une base de données MySQL sur un [Windows] [ mysqlwindows] ou [Linux] [ mysqllinux] machine virtuelle.

    2. À l’aide d’un client MySQL comme [MySQL atelier][workbench], connectez-vous à la nouvelle base de données et importer votre base de données WordPress.

    3. Mettre à jour la base de données pour modifier les entrées de domaine vers votre nouveau domaine Azure Application Service. Par exemple, mywordpress.azurewebsites.net. Utiliser [recherche et remplacement de Script de bases de données WordPress] [ searchandreplace] en toute sécurité modifier toutes les instances.

4. Créer une application web dans le portail Azure et publier la sauvegarde WordPress.

    1. Créer une application web dans le [portail Azure] [ mgmtportal] avec une base de données à l’aide de **Nouveau** -> **Web + Mobile** -> **Azure Marketplace** -> **Web Apps** -> **application Web + SQL** (ou **dans le navigateur + MySQL**) -> **créer**. Configurer tous les paramètres requis pour créer une application web vide.

    2. Dans votre sauvegarde WordPress, recherchez le fichier **wp config.php** et ouvrez-la dans un éditeur. Remplacer les entrées suivantes avec les informations relatives à votre nouvelle base de données MySQL.

        * **DB_NAME** - le nom d’utilisateur de la base de données

        * **DB_USER** - le nom d’utilisateur utilisé pour accéder à la base de données

        * **DB_PASSWORD** - le mot de passe utilisateur

        Après avoir modifié ces entrées, enregistrez et fermez le fichier **wp config.php** .

    3. Utiliser la [déployer une application web dans le Service d’application Azure] [ deploy] informations pour activer la méthode de déploiement que vous souhaitez utiliser, puis déployez votre sauvegarde WordPress dans votre application web dans le Service d’application Azure.

5. Une fois que le site WordPress a été déployé, vous devez être en mesure d’accéder le nouveau site (comme une application web application Service) à l’aide du *. azurewebsite.net URL du site.

### <a name="configure-your-site"></a>Configurer votre site

Une fois que le site WordPress a été créé ou migré, utilisez les informations suivantes pour améliorer les performances ou activer des fonctionnalités supplémentaires.

Pour cela... | Utilisez cela...
------------- | -----------
**Définir le mode plan de Service d’application, la taille et la mise à l’échelle de l’activer** | [Mettre à l’échelle une application web dans le Service d’application Azure][websitescale]
**Activer des connexions de base de données persistante** | Par défaut, WordPress n’utilise pas de connexions de base de données permanente, qui peuvent entraîner votre connexion à la base de données pour devenir limité après plusieurs connexions. Pour activer les connexions permanentes, installez-le (connexions permanentes carte) [https://wordpress.org/plugins/persistent-database-connection-updater/installation/]. 
**Améliorer les performances** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Désactiver les cookies ARR</a> - peut améliorer les performances lorsque vous exécutez WordPress sur plusieurs instances d’applications Web</p></li><li><p>Activer la mise en cache. <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Redis cache</a> (preview) peut être utilisé avec la <a href="https://wordpress.org/plugins/redis-object-cache/">Redis objet cache WordPress plug-in</a>, ou utilisez une des autres offres mise en cache à partir de la <a href="/gallery/store/">Banque d’Azure</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Comment rendre WordPress plus rapidement avec Wincache</a> - Wincache est activée par défaut pour les applications Web</p></li><li><p><a href="../web-sites-scale/">Échelle une application web dans le Service d’application Azure</a> et utiliser <a href="http://www.cleardb.com/developers/cdbr/introduction">Le routage disponibilité ClearDB haute</a> ou <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a></p></li></ul>
**Utiliser des objets BLOB pour le stockage** | <ol><li><p><a href="../storage-create-storage-account/">Créer un compte de stockage Azure</a></p></li><li><p>Découvrez comment <a href="../cdn-how-to-use/">utiliser le réseau de Distribution de contenu (CDN)</a> vers geo-distribuer des données stockées dans des objets BLOB.</p></li><li><p>Installez et configurez le <a href="https://wordpress.org/plugins/windows-azure-storage/">Stockage Azure WordPress du plug-in</a>.</p><p>Pour le programme d’installation détaillée et informations de configuration pour le plug-in, consultez le <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">guide de l’utilisateur</a>.</p> </li></ol>
**Activer la messagerie électronique** | Activer <a href="https://azure.microsoft.com/en-us/marketplace/partners/sendgrid/sendgrid-azure/">SendGrid</a> à l’aide de la banque d’Azure. Installer le <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified">plug-in SendGrid</a> pour WordPress.
**Configurer un nom de domaine personnalisé** | [Configurer un nom de domaine personnalisé dans le Service d’application Azure][customdomain]
**Activer HTTPS pour un nom de domaine personnalisé** | [Activer HTTPS pour une application web dans le Service d’application Azure][httpscustomdomain]
**Charger solde ou geo-distribuer votre site** | [Acheminer le trafic avec le Gestionnaire de trafic Azure][trafficmanager]. Si vous utilisez un domaine personnalisé, voir [configurer un nom de domaine personnalisé dans le Service d’application Azure] [ customdomain] pour plus d’informations sur l’utilisation du trafic Gestionnaire des noms de domaine personnalisé
**Activer des sauvegardes automatisées** | [Sauvegarder une application web dans le Service d’application Azure][backup]
**Activer la journalisation des Diagnostics** | [Activer la journalisation des diagnostics pour les applications web dans le Service d’application Azure][log]

## <a name="next-steps"></a>Étapes suivantes

* [Optimisation WordPress](http://codex.wordpress.org/WordPress_Optimization)

* [Convertir WordPress Multisite dans le Service d’application Azure](web-sites-php-convert-wordpress-multisite.md)

* [ClearDB Assistant pour Azure mise à niveau](http://www.cleardb.com/store/azure/upgrade)

* [Hébergement WordPress dans un sous-dossier de votre application web dans le Service d’application Azure](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Étape par étape : Créer un site de WordPress avec Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Héberger votre blog WordPress existant sur Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [L’activation permanents convivial dans WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [Comment migrer et exécuter votre blog WordPress service d’application Azure](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [L’exécution de WordPress en libre service d’application Azure](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress sur Azure en 2 minutes ou moins](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Déplacement d’un blog WordPress sur Azure - partie 1 : créer un blog WordPress sur Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Déplacement d’un blog WordPress sur Azure - partie 2 : transfert de votre contenu](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Déplacement d’un blog WordPress sur Azure - partie 3 : configurer votre domaine personnalisé](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Déplacement d’un blog WordPress sur Azure - partie 4 : presque permanents et les règles de la réécriture d’URL](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Déplacement d’un blog WordPress sur Azure - partie 5 : déplacement d’un sous-dossier à la racine](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Comment configurer une application web WordPress dans votre compte Azure](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Propping la WordPress sur Azure](http://www.johnpapa.net/wordpress-on-azure/)

* [Conseils pour WordPress sur Azure](http://www.johnpapa.net/azurecleardbmysql/)

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courte immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

## <a name="whats-changed"></a>Ce qui a changé
* Pour un guide à la modification de sites Web Application Service voir : [Azure Application Service et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- URL List -->

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: ../traffic-manager/traffic-manager-overview.md
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: https://azure.microsoft.com/documentation/services/redis-cache/
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]: ../virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md
[mysqllinux]: ../virtual-machines/virtual-machines-linux-classic-mysql-on-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: web-sites-deploy.md
[posh]: ../powershell-install-configure.md
[Azure CLI]: ../xplat-cli-install.md
[storesendgrid]: https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/
[cdn]: ../cdn/cdn-overview.md
