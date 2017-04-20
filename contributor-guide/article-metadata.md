

#<a name="metadata-for-azure-technical-articles"></a>Métadonnées des articles techniques Azure

Tous les articles techniques Azure contiennent deux sections de métadonnées - une section Propriétés et une section de balises. La section Propriétés permet à certains automation de site Web et les contenus SEO, tandis que la section balises permet un grand nombre de création de rapports contenu interne. Les deux sections sont requises.

- [Syntaxe]
- [Utilisation]
- [Attributs et les valeurs de la section Propriétés]
- [Attributs et les valeurs de la section de balises]

##<a name="syntax"></a>Syntaxe

La section Propriétés utilise la syntaxe suivante :

    <properties
       pageTitle="Page title that displays in search results and the browser tab | Microsoft Azure"
       description="Article description that will be displayed on landing pages and in most search results"
       services="service-name"
       documentationCenter="dev-center-name"
       authors="GitHub-alias-of-only-one-author"
       manager="manager-alias"
       editor=""
       tags="optional"
       keywords="For use by SEO champs only. Separate terms with commas. Check with your SEO champ before you change content in this article containing these terms."/>

La section balises utilise la syntaxe suivante :

    <tags
       ms.service="required"
       ms.devlang="may be required"
       ms.topic="article"
       ms.tgt_pltfrm="may be required"
       ms.workload="na"
       ms.date="mm/dd/yyyy"
       ms.author="Your MSFT alias or your full email address;semicolon separates two or more"/>

##<a name="usage"></a>Utilisation

- Le nom de l’élément et le nom de l’attribut respectent la casse.
- La <properties> section doit être la première ligne de votre fichier.
- Laisser une ligne vide après chaque section de métadonnées et avant votre titre de page pour vous assurer que le titre de page est correctement converti au format HTML au cours du processus de publication.

## <a name="attributes-and-values-for-the-properties-section"></a>Attributs et les valeurs de la section Propriétés

![](./media/article-metadata/checkmark-small.png)**pageTitle**: obligatoire ; important de SEO. Le texte pour cet attribut apparaît dans l’onglet du navigateur et sous le titre dans un résultat de recherche. Utiliser des 55-60 caractères, espaces compris et y compris l’identificateur de site *| Microsoft Azure* (tapée comme : espace de barre verticale Microsoft Azure d’espace).  La pageTitle doit être différent de la H1.

![](./media/article-metadata/checkmark-small.png)**Description**: obligatoire ; important pour SEO (pertinence) et les fonctionnalités de site. La description doit être au moins 125 caractères long à 155 caractères au maximum, espaces compris. Décrire l’objectif de votre contenu afin que les clients savent, si vous voulez choisir parmi une liste de résultats de recherche. La valeur est la suivante :

- Ce texte peut être affiché en tant que la description ou résumé paragraphe dans les résultats de recherche sur Google.
- Ce texte s’affiche dans [les résultats de l’index article](https://azure.microsoft.com/documentation/articles/).

![](./media/article-metadata/checkmark-small.png)**services**: requis pour les articles qui traitent avec un service. Cette valeur est ofter appelée la « service du ligne-bloc ». Liste tous les services applicables, séparées par des virgules. Le premier service que vous avez indiquée déterminera la barre de navigation de navigation pour la page et la navigation à gauche qui s’affiche avec la page.

Dans les articles qui spécifient une valeur de services et une valeur documentationCenter, la valeur de services déterminera la barre de navigation. Valeurs supplémentaires vous liste s’affichent sous forme de balises dans l’article publié. Valeurs :

- Active directory
- actif-répertoire-b2c
- Active directory service d’annuaire
- application service\api
- gestion des API
- application de service
- application servic\mobile
- application service\web
- application service\logic
- passerelle d’application
- analyse de l’application
- automatisation
- portail Azure
- Gestionnaire de ressources Azure
- pile d’Azure
- sauvegarde
- traitement par lots
- meilleures pratiques
- services BizTalk
- cache
- CDN
- services de cloud
- catalogue de données
- données usine
- données-lake-analytique
- magasin de données lake
- devtest-atelier
- DNS
- documentdb
- expressroute
- événement-hubs
- hdinsight
- IOT concentrateur
- archivage sécurisé clé
- équilibrage de charge
- apprentissage automatique
- Marketplace
- services de support
- Mobile engagement
- services mobiles
- multiples l’authentification
- notification-hubs
- perspectives opérationnelles
- suite de gestion des opérations
- powerapps
- Gestionnaire de récupération
- redis cache
- RemoteApp
- gestion des droits
- Planificateur
- recherche
- Centre de sécurité
- bus des services
- TISSU de service
- récupération de site
- base de données SQL
- magasin de données SQL
- Création de rapports SQL
- espace de stockage
- stocker
- storsimple
- flux analytique
- Gestionnaire de trafic
- machines virtuelles
- réseau virtuel
- online Visual studio
- passerelle VPN
- sites Web

![](./media/article-metadata/checkmark-small.png)**documentationCenter**: requis pour les articles orientée développement meilleures proposées via un centre de développement. Spécifiez le centre de développement unique ou d’une langue qui s’applique à l’article. La valeur de la liste vous déterminera la barre de navigation de navigation pour la page. Dans les articles qui spécifient une valeur de services et une valeur documentationCenter, la valeur de services déterminera la barre de navigation. Valeurs :

- **.NET**
- **nodejs**
- **Java**
- **PHP**
- **Python**
- **Ruby**
- **Mobile**: déconseillée. Remplacez plateforme mobile spécifique.
- **IOS**: vérification cette nouvelle valeur
- **Android**: vérification de cette nouvelle valeur
- **Windows**: vérification de cette nouvelle valeur
- **xamarin**: Vérifiez cette nouvelle valeur

![](./media/article-metadata/checkmark-small.png)**auteurs**: obligatoire, une seule valeur. Le compte GitHub de l’article PME ou auteur principal de la liste. Cet attribut lecteurs par la ligne dans l’article publié. La liste seul malgré le nom de l’attribut pluriel.

![](./media/article-metadata/checkmark-small.png)**Gestionnaire**: obligatoire si vous êtes un collaborateur Microsoft. Liste des alias de messagerie du Gestionnaire de publication du contenu pour la zone de technologie. Si vous êtes un collaborateur communautaire, incluez l’attribut mais laissez vide afin que nous puissions complétez-le.

![](./media/article-metadata/checkmark-small.png)**éditeur**: non utilisé. N’utilisez pas à d’autres fins.

![](./media/article-metadata/checkmark-small.png)**balises**: facultatif. Inclure uniquement si vous voulez activer un lien sous la barre de navigation de l’article à la page d’article index (http://azure.microsoft.com/documentation/articles/) à une liste prefiltered d’articles qui correspond à aucune des valeurs approuvés. Ces valeurs sont conçus pour offrir un moyen de regrouper du contenu lorsque le regroupement contenu n’est pas spécifique au service. Ces balises peuvent également fournir les étiquettes qui indique l’environnement technologique l’article s’applique à. Cette valeur **ne prend pas** en charge balises de forme libre ou hashtags ; les balises doivent être activés sur le site. Vous pouvez fournir plusieurs valeurs de balises pour un article, séparées par des virgules. Les valeurs approuvés sont :

  - architecture
  - Gestionnaire de ressources Azure
  - gestion des services Azure
  - facturation
  - MySQL

![](./media/article-metadata/checkmark-small.png)**mots clés**: facultatif. Pour une utilisation par champs SEO uniquement. Séparez les termes par des virgules. **Contactez votre champ SEO avant de modifier ou supprimer le contenu de cet article contenant ces termes.** Cet attribut enregistre les mots clés le champ SEO a ciblé et effectue le suivi afin d’améliorer le rang de la recherche. Les mots clés ne sont pas rendent dans la page HTML publiée. Validation ne requiert pas cet attribut.

## <a name="attributes-and-values-for-the-tags-section"></a>Attributs et les valeurs de la section de balises

![](./media/article-metadata/checkmark-small.png)**MS.service**: obligatoire. Spécifie le service Azure, un outil ou une fonctionnalité l’article s’applique à. Une valeur par page.

 Si une page s’appliquent à plusieurs services, sélectionnez le service auquel il la plupart directement s’applique ; par exemple, un article qui utilise une application hébergée sur les sites web pour montrer les fonctionnalités de Bus des services doit avoir la valeur **bus des services** , plutôt que **des sites web**. Si une page s’appliquent également à plusieurs services, sélectionnez **plusieurs**. Si une page ne s’applique pas à des services (il s’agit rare), choisissez **NA**.

 - **Active directory**
 - **actif-répertoire-b2c**
 - **Active directory service d’annuaire**
 - **gestion des API**
 - **application de service**: s’applique uniquement à des informations conceptuelles générales sur le Service d’application
 - **api de service d’application**
 - **logique du service d’application**
 - **application service-mobile**
 - **web du service d’application**
 - **analyse de l’application**
 - **passerelle d’application**
 - **automatisation**
 - **Gestionnaire de ressources Azure**
 - **Azure-sécurité**
 - **pile d’Azure**
 - **sauvegarde**
 - **traitement par lots**
 - **meilleures pratiques**
 - **services BizTalk**
 - **facturation**
 - **cache**
 - **CDN**
 - **services de cloud**
 - **catalogue de données**
 - **magasin de données lake**
 - **données-lake-analytique**
 - **devtest-atelier**
 - **expressroute**
 - **hdinsight**
 - **Internet des objets**
 - **IOT concentrateur**
 - **archivage sécurisé clé**
 - **apprentissage automatique**
 - **Marketplace**: Articles sur la Azure marketplace
 - **services de support**
 - **Mobile engagement**
 - **services mobiles**
 - **multiples l’authentification**
 - **plusieurs**: la page s’appliquent également à plusieurs services
 - **NA**: la page ne s’applique pas à des services (rares)
 - **notification-hubs**
 - **perspectives opérationnelles**
 - **powerapps**
 - **Gestionnaire de récupération**
 - **redis cache**
 - **RemoteApp**
 - **gestion des droits**
 - **Planificateur**
 - **Centre de sécurité**
 - **bus des services**
 - **TISSU de service**
 - **récupération de site**: services de récupération anciennement
 - **base de données SQL**
 - **magasin de données SQL**
 - **Création de rapports SQL**
 - **espace de stockage**
 - **stocker**: Articles sur les services disponibles via le magasin d’Azure
 - **storsimple**
 - **Gestionnaire de trafic**
 - **machines virtuelles**
 - **réseau virtuel**
 - **online Visual studio**
 - **passerelle VPN**
 - **sites Web**

![](./media/article-metadata/checkmark-small.png)**MS.DevLang**: obligatoire. Spécifie le langage de programmation qui l’article s’applique à. Valeur unique par page.

 Si une page s’appliquent également à deux langages de programmation, sélectionnez **plusieurs**. Si une page est principalement conceptuelle et son contenu est généralement applicable à plusieurs langages de programmation, choisissez **plusieurs**. Si une page n’est pas destinée aux développeurs et l’application de langage de programmation n’est pas pertinente, choisissez **NA**. **Api rest** permet d’identifier des rubriques de référence API REST.

 - **RPC**
 - **dotnet**
 - **Java**
 - **JavaScript**
 - **plusieurs**: la page s’appliquent également à plusieurs langages de programmation.
 - **NA**: la page n’est pas visant les développeurs et n’est pas spécifique à n’importe quel langages de programmation.
 - **nodejs**
 - **objectif c**
 - **PHP**
 - **Python**
 - **api REST**
 - **Ruby**


![](./media/article-metadata/checkmark-small.png)**MS.Topic**: obligatoire. Caractéristiques de la rubrique tapez. La plupart des nouvelles pages créées par les collaborateurs sera article ou une référence.

 - **article**: une rubrique conceptuelle, didacticiel, guide de fonctionnalité ou autre article non-référence

 - **page de campagne**: Azure.com uniquement.  Une page qui est spécialement conçue comme une page d’accueil pour les campagnes externe et n’est pas inclus dans le cadre du site principal IA.  Ne doit pas être utilisé pour les articles de documentation ou document normal pages de lancement.  Exemples : azure.microsoft.com/develop/net/aspnet/ ; Azure.Microsoft.com/Develop/mobile/IOS/

 - **développement-centre-page d’accueil**: Azure.com uniquement.  Un développement centrer page d’accueil, par exemple/développer/net /

 - **Get-mise en route-article**: affecter à des articles qui sont disponibles dans la section prise en main ou vue d’ensemble de la navigation à gauche d’un service.

 - **l’article Hero**: un didacticiel « hero » qui est conçu pour fournir une introduction à un service ou une fonctionnalité qui obtient visiteurs démarrés au moyen du service rapidement et lecteurs cachés d’essai gratuite-sportives et activations MSDN. Affecter cette valeur uniquement référence aux articles proposée en haut de la page de lancement de documentation de votre service.

 - **page d’accueil**: page d’accueil de documentation de niveau supérieur. Nous avons deux : azure.microsoft.com/documentation/ et msdn.microsoft.com/library/azure/

 - **page d’index**: pages de programmation langues, des services ou des fonctionnalités de lancement de Second niveau. Celles-ci sont réparties sur Azure.com et à la bibliothèque et sont utilisés comme points d’entrée pour des informations plus spécifiques dans l’étendue. Exemples : http://azure.microsoft.com/develop/mobile/resources-wp8/, http://msdn.microsoft.com/library/azure/jj673460.aspx, http://msdn.microsoft.com/library/azure/hh689864.aspx

 - **page d’infographic**: Azure.com uniquement. Une page qui propose un infographic peut être exploré ou des posters, par exemple http://azure.microsoft.com/documentation/infographics/windows-azure/

 - **référence**: une API de référence sur la page (y compris l’API REST) ou PowerShell applet de commande référence

 - **page d’accueil service**: Azure.com uniquement.  Une page d’accueil document de service, par exemple, /documentation/services/virtual-machines /

 - **site-section-page d’accueil**: Azure.com uniquement. Une page d’accueil « » pour un type particulier de contenu sur azure.com exemples : http://azure.microsoft.com/documentation/infographics/, http://azure.microsoft.com/documentation/scripts/, http://azure.microsoft.com/documentation/videos/home/, http://azure.microsoft.com/downloads/

 - **page de la vidéo**: Azure.com uniquement.  Une page qui propose une vidéo, par exemple http://azure.microsoft.com/documentation/videos/azure-webjobs-hosting-testing-net/

![](./media/article-metadata/checkmark-small.png)**MS.tgt_pltfrm**: obligatoire. Spécifie la plateforme cible, par exemple Windows, Linux, Windows Phone, iOS, Android ou plateformes cache spécial. Une valeur par page. Cette valeur sera **NA** pour la plupart des rubriques sauf mobile et machines virtuelles.

 - **rôle de cache**
 - **cache-multiples**
 - **cache redis**
 - **service de cache**
 - **cache partagé**
 - **interface de ligne de commande**
 - **Ibiza**: contenu qui utilise le portail Ibiza. Utilisez cette commande uniquement dans les cas où la fonctionnalité examinée est disponible sur le portail Ibiza et le portail actuel.
 - **Mobile android**: Azure.com uniquement immédiatement
 - **Mobile html**: Azure.com uniquement immédiatement
 - **Mobile ios**: Azure.com uniquement immédiatement
 - **Mobile kindle**: Azure.com uniquement immédiatement
 - **Mobile-multiples**
 - **Mobile-nokia-x**: Azure.com uniquement immédiatement
 - **Mobile phonegap**: Azure.com uniquement immédiatement
 - **Mobile sencha**: Azure.com uniquement immédiatement
 - **windows mobile**: Azure.com uniquement immédiatement ; Universel de Windows
 - **téléphone mobile-windows**
 - **magasin de windows mobile**
 - **Mobile xamarin**: Azure.com uniquement immédiatement ; Xamarin toutes les plateformes
 - **Mobile-xamarin-android**: Azure.com uniquement immédiatement
 - **Mobile-xamarin-ios**: Azure.com uniquement immédiatement
 - **plusieurs**: la page s’appliquent également à plusieurs plateformes
 - **NA**: un spécificateur de plate-forme n’est pas applicable pour cette page
 - **PowerShell**
 - **machine virtuelle linux**
 - **machine virtuelle-multiples**
 - **machine virtuelle windows**
 - **machine virtuelle-windows-sharepoint**
 - **machine virtuelle windows sql server**
 - **VS-mise en route**: identifie le groupe VS prise en main de page. Marqueur ajouté 1/12/14.
 - **VS-what-est devenu**: identifie la page VS prise en main est passée. Balise ajoutée 1/12/14.

![](./media/article-metadata/checkmark-small.png)**MS.Workload**: obligatoire. Spécifie la charge de travail Azure correspondant à la page. Une valeur uniquement par article.

**mettre à jour 8/6/15** La valeur ms.workload est mappée par un xls, et non la valeur dans le fichier .md. La valeur ms.workload est toujours nécessaire de validation jusqu'à ce que la fonctionnalité pouvant être mis à jour. Acceptés sont maintenant planifiée.  Utilisez **« na »** comme valeur pour l’instant.

![](./media/article-metadata/checkmark-small.png)**MS.date**: obligatoire. Spécifie la date de que la dernière vérification de l’article de la pertinence, précision, captures d’écran correct et utiliser des liens. Entrez la date au format jj/mm/aaaa. Cette date s’affiche également dans l’article publié comme la dernière date mis à jour.

![](./media/article-metadata/checkmark-small.png)**MS.Author**: obligatoire. Spécifie les auteurs associé à la rubrique. Rapports internes (par exemple, l’actualisation) utilisent cette valeur pour associer les auteurs de droite à l’article. Pour spécifier plusieurs valeurs doit séparés par des points-virgules. Alias Microsoft ou adresses de messagerie complète sont acceptables. La longueur peut être pas comporter plue de 200 caractères.


###<a name="contributors-guide-links"></a>Liens de Guide des collaborateurs

- [Article de présentation](./../README.md)
- [Index des articles sur les instructions](./contributor-guide-index.md)


<!--Anchors-->
[Syntaxe]: #syntax
[Utilisation]: #usage
[Attributs et les valeurs de la section Propriétés]: #attributes-and-values-for-the-properties-section
[Attributs et les valeurs de la section de balises]: #attributes-and-values-for-the-tags-section
