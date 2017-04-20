# <a name="azure-technical-documentation-contributor-guide"></a>Guide de collaboration Azure Documentation technique

Vous avez trouvé le référentiel GitHub qui héberge la source de la documentation technique qui est publié dans le centre de Documentation Azure à [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation).

Ce référentiel contienne également des conseils pour vous aider à contribuer à notre documentation technique.  Pour obtenir la liste des articles dans guide des collaborateurs, voir [l’index](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).

## <a name="contribute-to-azure-documentation"></a>Contribuer à la documentation Azure

Nous vous remercions de votre intérêt dans la documentation Azure.

* [Méthodes de collaboration](#ways-to-contribute)
* [Code de conduite](#code-of-conduct)
* [À propos de vos contributions au contenu Azure](#about-your-contributions-to-azure-content)
* [Organisation du référentiel](#repository-organization)
* [Utiliser GitHub, Git et ce référentiel](#use-github-git-and-this-repository)
* [Comment utiliser démarque pour mettre en forme votre sujet](#how-to-use-markdown-to-format-your-topic)
* [Prise en charge, les commentaires et les commentaires](./contributor-guide/feedback-and-comments.md)
* [Ressources complémentaires](#more-resources)
* [Index des articles de guide de tous les collaborateurs](./contributor-guide/contributor-guide-index.md) (ouvre la nouvelle page)

## <a name="ways-to-contribute"></a>Méthodes de collaboration 

Vous pouvez contribuer aux [Azure documentation](http://azure.microsoft.com/documentation/) de différentes façons :

* Contribuer à un [forum de discussion](http://social.msdn.microsoft.com/Forums/windowsazure/home).
* Envoyer des commentaires Disqus dans la partie inférieure des articles.
* Vous pouvez facilement contribuer aux articles techniques dans l’interface utilisateur GitHub. Soit trouver l’article dans ce référentiel, ou consultez l’article sur [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) et cliquez sur le lien dans l’article qui accède à la source GitHub pour l’article.
* Si vous apportez des modifications substantielles à un article existant, ajouter ou modifier des images ou faisant partie d’un nouvel article, vous devez branche ce référentiel, installer Git Bash, pavé démarque et apprendre à utiliser certaines commandes git.

##<a name="code-of-conduct"></a>Code de conduite

Ce projet a adopté le [Microsoft Open Source Code de conduite](https://opensource.microsoft.com/codeofconduct/). Pour plus d’informations, voir le [Code de diriger Forum aux questions](https://opensource.microsoft.com/codeofconduct/faq/) ou le contact [opencode@microsoft.com](mailto:opencode@microsoft.com) avec les autres questions ou commentaires.

##<a name="about-your-contributions-to-azure-content"></a>À propos de vos contributions au contenu Azure

###<a name="minor-corrections"></a>Corrections mineures

Des corrections mineures ou précisions que vous soumettez pour les exemples de code et de documentation dans cette mis en pension sont couvertes par [Azure site Web conditions d’utilisation (conditions d’utilisation)](http://azure.microsoft.com/support/legal/website-terms-of-use/).


###<a name="larger-submissions"></a>Envois plus grandes

Si vous envoyez une demande d’extraction avec nouveau ou significatives les modifications apportées à la documentation et des exemples de code, nous allons envoyer un commentaire dans GitHub vous invitant à soumettre un contrat de licence de Contribution en ligne (CLA) si vous êtes dans un de ces groupes :

* Membres du groupe Technologies ouvertes de Microsoft.
* Collaborateurs qui ne fonctionnent pas pour Microsoft.

Nous avons besoin de vous pour remplir le formulaire en ligne avant que nous pouvons accepter votre demande d’extraction.

Informations plus complètes sont disponibles sur [http://azure.github.io/guidelines/#cla](http://azure.github.io/guidelines/#cla).

## <a name="repository-organization"></a>Organisation du référentiel

Le contenu dans le référentiel azure contenu provient de l’organisation de documentation sur [Azure.Microsoft.com](http://azure.microsoft.com). Ce référentiel contient deux dossiers racine :

### <a name="articles"></a>\articles

Le dossier *\articles* contient les articles de documentation mis en forme en tant que fichiers démarque avec une extension *.md* .

Articles dans le répertoire racine sont publiés sur Azure.Microsoft.com dans le chemin d’accès *http://azure.microsoft.com/documentation/articles/ {article-nom-sans-md} /*.

* **Article des noms de fichiers :** Consultez [notre dénomination des conseils de fichier](./contributor-guide/file-names-and-locations.md).

Articles dans leur propre dossier de service sont publiés sur Azure.Microsoft.com dans le chemin d’accès *http://azure.microsoft.com/documentation/articles/service-folder/ {article-nom-sans-md} /*

* **Sous-dossiers Media :** Le dossier *\articles* contient le dossier *\media* pour racine répertoire article des fichiers multimédias, à l’intérieur qui sont des sous-dossiers avec les images de chaque article.  Les dossiers de service contiennent un dossier de médias distincts pour les articles dans chaque dossier de service. Les dossiers d’image de l’article portent le même vers le fichier de l’article, moins l’extension de fichier *.md* .

### <a name="includes"></a>\Includes

Vous pouvez créer des sections de contenu réutilisables à inclure dans un ou plusieurs articles. Voir [extensions personnalisées utilisées dans notre contenu technique](./contributor-guide/custom-markdown-extensions.md).

### <a name="markdown-templates"></a>modèles de \markdown

Ce dossier contient notre modèle démarque standard avec la mise en forme de base démarque que vous avez besoin d’un article.

### <a name="contributor-guide"></a>\Contributor-Guide

Ce dossier contient des articles qui font partie du guide des nos collaborateurs de.  

## <a name="use-github-git-and-this-repository"></a>Utiliser GitHub, Git et ce référentiel

Pour plus d’informations sur la façon de contribuer, comment utiliser la GitHub UI pour y apporter des modifications petites et comment une branche et cloner le référentiel pour dons plus significatives, voir [installer et configurer les outils de création de GitHub](./contributor-guide/tools-and-setup.md).

Si vous installez GitBash et choisissez travailler en local, les étapes de création d’une branche travail local, apporter des modifications et envoyer les modifications sur la branche principale sont répertoriées dans le [Git commandes pour créer un nouvel article ou mise à jour d’un article existant](./contributor-guide/git-commands-for-master.md)

### <a name="branches"></a>Branches

Nous vous recommandons de créer local branches de travail afin de ciblent une étendue spécifique de modification. Branche doit être limité à un seul concept/article à la fois pour rationaliser les flux de travail et réduire les risques de conflits de fusion.  Les efforts suivants sont de l’étendue appropriée pour une nouvelle branche :

* Un nouvel article (et images associées)
* Modifications de l’orthographe et la grammaire dans un article.
* Application d’une modification de mise en forme unique sur un ensemble étendu des articles (par exemple, nouveau copyright pied de page).

## <a name="how-to-use-markdown-to-format-your-topic"></a>Comment utiliser démarque pour mettre en forme votre sujet

Tous les articles dans ce référentiel utilisent démarque AROMATISES GitHub.  Voici une liste de ressources.

- [Concepts de base démarque](https://help.github.com/articles/markdown-basics/)

- [Fiche récapitulative démarque imprimable](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

- Pour notre liste des éditeurs de démarque, voir les [Outils et le programme d’installation de sujets](./contributor-guide/tools-and-setup.md#install-a-markdown-editor).

## <a name="article-metadata"></a>Métadonnées de l’article

Métadonnées de l’article permet de certaines fonctionnalités sur le site web azure.microsoft.com, tels qu’auteur attribution attribution de collaboration, barre de navigation, les descriptions de l’article et SEO optimisations ainsi que la création de rapports Microsoft utilise pour évaluer les performances du contenu. Par conséquent, les métadonnées sont importante ! [Voici les instructions pour vérifier que les métadonnées de votre terminée vers la droite](./contributor-guide/article-metadata.md).

### <a name="labels"></a>Étiquettes

Étiquettes automatisés affectés à extraire des requêtes pour nous aider à gérer le flux de travail extraire demande et afin de vous avertir que se passe-t-il avec votre demande d’extraction :

* Contrat de licence de contribution liés
    * CLA non requis : la modification est relativement mineure et ne nécessite pas que vous vous connectez un CLA.
    * obligatoire CLA : l’étendue de la modification est relativement important et nécessite que vous vous connectez un CLA.
    * signé CLA : la collaboration connecté CLA, afin que la demande d’extraction peut désormais mettre en œuvre pour révision.
* Pilier étiquettes : PnP tels que des étiquettes, applications moderne et CDC permettent de classer les demandes d’extraction par l’organisation interne qui a besoin de passer en revue la demande d’extraction.
* Modification envoyée à créer : l’auteur a été informé de la demande d’extraction en attente.

## <a name="more-resources"></a>Ressources complémentaires

Consultez l' [index du guide de notre collaboration](./contributor-guide/contributor-guide-index.md) pour toutes les rubriques de notre guide.
