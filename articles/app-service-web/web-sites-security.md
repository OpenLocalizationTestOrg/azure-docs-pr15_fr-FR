<properties
    pageTitle="Sécuriser une application dans le Service d’application Azure"
    description="Découvrez comment faire pour sécuriser une application web, principal de l’application mobile ou API app dans le Service d’application Azure."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="01/12/2016"
    ms.author="cephalin"/>


#<a name="secure-an-app-in-azure-app-service"></a>Sécuriser une application dans le Service d’application Azure

Cet article vous permet de prise en main sur la sécurisation de votre application web, principal de l’application mobile ou API app dans le Service d’application Azure. 

Sécurité dans le Service d’application Azure comporte deux niveaux : 

- **Sécurité infrastructure et de plateforme** - faites-vous confiance à Azure pour que les services que vous avez besoin d’exécuter en toute sécurité dans le nuage.
- **Sécurité de l’application** : vous devez concevoir en toute sécurité de l’application elle-même. Cela inclut la façon dont vous intégrez avec Azure Active Directory, la gestion des certificats et comment vous assurer que vous pouvez en toute sécurité échanger avec les différents services. 

#### <a name="infrastructure-and-platform-security"></a>Sécurité infrastructure et de plateforme
Étant donné que l’application Service conserve les ordinateurs virtuels Azure, stockage, connexions réseau, structures web, gestion et les fonctionnalités d’intégration et bien plus encore, il est activement sécurisé et renforcée et parcourt vigoureuse conformité et vérifie en continu pour vous assurer que :

- Vos applications de Service d’application sont isolées à partir de deux Internet et de ressources Azure d’autres clients.
- La communication de secrets (par exemple, des chaînes de connexion) entre votre application de Service d’application et d’autres ressources Azure (par exemple, base de données SQL) dans un groupe de ressources est conforme à Azure et ne croisées les limites du réseau. Secrets sont toujours chiffrés.
- Toutes les communications entre votre application de Service d’application et les ressources externes, tels que PowerShell gestion interface de ligne de commande, Azure SDK, API REST et connexions hybride, sont chiffrés correctement.
- menace 24 heures gestion protège les ressources du Service d’application à partir de programmes malveillants, distribué refus de service (DDoS), homme dans-the-milieu (l’homme) et autres menaces. 

Pour plus d’informations sur la sécurité infrastructure et de plateforme dans Azure, voir le [Centre de gestion de la confidentialité Azure](/support/trust-center/security/).

#### <a name="application-security"></a>Sécurité de l’application

Azure est responsable de la sécurisation de l’infrastructure et plateforme votre application s’exécute sur, il est de votre responsabilité pour sécuriser votre application proprement dite. En d’autres termes, vous devez développer, déployer et gérer votre code de l’application et le contenu de façon sécurisée. Sans cela, votre code de l’application ou le contenu peut toujours à menaces telles que :

- Injection SQL
- Piratage de session
- Site-croisée
- HOMME au niveau de l’application
- Niveau de l’application DDoS

Une description détaillée des considérations relatives à la sécurité pour les applications web est au-delà de la portée de ce document. Comme point de départ pour plus d’informations sur la sécurisation de votre application, voir l' [Ouvrir Web Application sécurité projet (OWASP avoir)](https://www.owasp.org/index.php/Main_Page), spécifiquement le [projet 10 premiers.](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), qui répertorie les défauts de sécurité application en cours de supérieure 10 web critique, telle que déterminée par les membres OWASP avoir.

## <a name="perform-penetration-testing-on-your-app"></a>Effectuer le test de votre application de pénétration

Une des façons à encore commencé avec les vulnérabilités dans votre application de Service d’application des tests plus simples consiste à utiliser l' [intégration avec la sécurité Tinfoil](/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) pour effectuer votre application d’analyse des vulnérabilités un seul clic. Vous pouvez consulter les résultats dans un rapport facile à comprendre et découvrez comment résoudre chaque problème de sécurité des instructions étape par étape.

Si vous préférez effectuer vos propres tests de pénétration ou que vous souhaitez utiliser un autre suite scanneur ou fournisseur, vous devez suivre le [processus d’approbation tests de pénétration Azure](https://security-forms.azure.com/penetration-testing/terms) et obtenir l’approbation préalable pour exécuter les tests de pénétration de votre choix.

##<a name="https"></a>Sécuriser les communications avec les clients

Si vous utilisez la ** \*. azurewebsites.net** nom de domaine créé pour votre application de Service d’application, vous pouvez utiliser immédiatement HTTPS, comme un certificat SSL est fourni pour toutes les ** \*. azurewebsites.net** noms de domaine. Si votre site utilise un [nom de domaine personnalisé](web-sites-custom-domain-name.md), vous pouvez télécharger un certificat SSL à [Activer HTTPS](web-sites-configure-ssl-certificate.md) pour le domaine personnalisé.

L’activation de [HTTPS](https://en.wikipedia.org/wiki/HTTPS) peut contribuer à protéger contre les attaques de l’homme sur la communication entre votre application et ses utilisateurs.

## <a name="secure-data-tier"></a>Niveau de sécuriser les données

Application Service intègre hautement SQL de base de données, de sorte que toutes les chaînes de connexion sont chiffrés à tous les niveaux et sont déchiffrés uniquement sur l’ordinateur virtuel qui s’exécute à l’application *et* uniquement lors de l’exécution de l’application. En outre, base de données SQL Azure contient de nombreuses fonctionnalités de sécurité pour vous aider à protéger vos données d’application contre les menaces, y compris [le chiffrement au reste](https://msdn.microsoft.com/library/dn948096.aspx), [Toujours chiffrées](https://msdn.microsoft.com/library/mt163865.aspx), [Masque dynamique de données](../sql-database/sql-database-dynamic-data-masking-get-started.md)et [Détection des menaces](../sql-database/sql-database-threat-detection-get-started.md). Si vous avez des données sensibles ou aux exigences de conformité, consultez [sécurisation de votre base de données SQL](../sql-database/sql-database-security.md) pour plus d’informations sur la façon de protéger vos données.

Si vous utilisez un fournisseur tiers de base de données, tels que ClearDB, vous devez consulter la documentation du fournisseur directement sur les meilleures pratiques de sécurité.  

##<a name="develop"></a>Déploiement et développement sécurisé

### <a name="publishing-profiles-and-publish-settings"></a>Publication des profils et paramètres de publication

Lorsque vous développez des applications, gestion des tâches ou automatisation des tâches à l’aide d’utilitaires tels que **Visual Studio**, **Une matrice Web**, **Azure PowerShell** ou l' **Interface de ligne Azure (Azure commande)**, vous pouvez utiliser un fichier de *paramètres de publication* ou un *profil de publication*. Les deux types de fichiers vous authentifieront avec Azure et doivent être sécurisées pour empêcher tout accès non autorisé.

* Contient un fichier de **paramètres de publication**

    * Votre ID d’abonnement Azure

    * Un certificat de gestion qui permet de vous permet d’effectuer des tâches de gestion de votre abonnement *sans avoir à fournir un nom de compte ou le mot de passe*.

* Contient un fichier de **profil de publication**

    * Informations pour la publication dans votre application

Si vous utilisez un utilitaire qui utilise un fichier de paramètres de publication ou un fichier profil de publication, importez le fichier contenant les paramètres de publication ou un profil dans l’utilitaire, puis **Supprimer** le fichier. Si vous devez conserver le fichier à partager avec d’autres personnes travaillent sur le projet, par exemple, stockez-le dans un emplacement sécurisé tel qu’un répertoire *chiffré* avec des autorisations restreintes.

En outre, vous devez vous assurer que les informations d’identification importées sont sécurisées. Par exemple, **PowerShell Azure** et l' **Interface de ligne Azure (Azure commande)** stockent les informations importées dans votre **répertoire de base** (*~* sur les systèmes Linux ou OS X et */users/yourusername* sur les systèmes Windows.) Pour plus de sécurité, vous souhaiterez peut-être **chiffrer** ces emplacements à l’aide des outils de chiffrement disponibles pour votre système d’exploitation.

### <a name="configuration-settings-and-connection-strings"></a>Paramètres de configuration et des chaînes de connexion
Il est courant pour stocker les chaînes de connexion, les informations d’authentification et les autres informations sensibles dans les fichiers de configuration. Malheureusement, ces fichiers risquent d’être exposées sur votre site Web ou archivés dans un référentiel public, exposant ces informations. Une recherche simple sur [GitHub](https://github.com), par exemple, pouvez découvrir un nombre illimité de fichiers de configuration avec secrets exposées dans les référentiels publics.

La meilleure solution consiste à éviter de ces informations se fichiers de configuration de votre application. Service d’application vous permet de stocker les informations de configuration dans le cadre de l’environnement d’exécution en tant que **paramètres de l’application** et des **chaînes de connexion**. Les valeurs sont présentés à votre application en cours d’exécution via des *variables d’environnement* pour la plupart des langages de programmation. Pour les applications .NET, ces valeurs sont injecte dans votre configuration .NET en cours d’exécution. En dehors de ces situations, ces paramètres de configuration resteront chiffrés, sauf si vous affichez ou configurez à l’aide du [Portail Azure](https://portal.azure.com) ou utilitaires de PowerShell ou de l’infrastructure du langage commun Azure. 

Stocker les informations de configuration dans le Service d’application rend possible pour l’administrateur de l’application verrouiller des informations sensibles pour les applications de production. Les développeurs peuvent utiliser un ensemble de paramètres de configuration distinct pour le développement d’application et les paramètres peuvent être automatiquement remplacées par les paramètres définis dans l’application de Service. Même pas les développeurs doivent connaître les secrets configurés pour l’application de production. Pour plus d’informations sur la configuration des paramètres de l’application et des chaînes de connexion dans le Service d’application, voir [configuration web apps](web-sites-configure.md).

### <a name="ftps"></a>ENVOIE VIA FTP

Azure Application Service offre un accès sécurisé FTP au système de fichiers pour votre application par le biais **FTPS**. Ainsi, vous permet d’accéder en toute sécurité le code d’application dans l’application web ainsi que les diagnostics dans les journaux. Il est recommandé de toujours utiliser FTPS au lieu de FTP. 

Le lien FTPS pour votre application, vous pouvez trouver les étapes suivantes :

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Sélectionnez **Rechercher tout**.
3. À partir de la carte **Parcourir** , sélectionnez **Les Services d’application**.
4. À partir de la carte de **Services d’application** , sélectionnez l’application souhaitée.
5. À partir de la carte de l’application, sélectionnez **tous les paramètres**.
6. Dans la carte de **paramètres** , cliquez sur **Propriétés**.
7. Les liens FTP et FTPS sont fournies sur la carte de **paramètres** . 

Pour plus d’informations sur FTPS, voir [File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la sécurité de la plateforme Azure, d’informations sur les signaler un **incident de sécurité ou d’utilisation inappropriée**, ou pour informer Microsoft que vous exécutez **des tests de pénétration** de votre site, consultez la section sécurité du [Centre de gestion de la confidentialité de Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

Pour plus d’informations sur les fichiers **web.config** ou **applicationhost.config** dans les applications de Service d’application, voir [options de Configuration déverrouillées dans Azure Application Service web apps](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Pour plus d’informations sur les informations de journalisation pour les applications de Service d’application, qui peut être utile pour détecter les attaques, voir [Activer la journalisation des diagnostics](web-sites-enable-diagnostic-log.md).

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application starter courte immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

## <a name="whats-changed"></a>Ce qui a changé

* Pour un guide à la modification de sites Web Application Service voir : [Azure Application Service et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)
