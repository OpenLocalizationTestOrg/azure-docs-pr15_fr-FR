<properties
   pageTitle="Créer des liens dans les articles démarque" description="Explique comment croisements dans démarque de code." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="carolz" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="02/03/2015" ms.author="tysonn" />

# <a name="linking-guidance-for-azure-technical-content"></a>Conseils pour le contenu technique Azure de liaison

### <a name="links-from-one-acom-article-to-another"></a>Liens à partir d’un article ACOM vers un autre

Pour créer un lien inline à partir d’un article technique ACOM vers un autre article technique ACOM, utilisez la syntaxe de lien suivant :  

- Un article dans un service annuaire est liée à un autre article dans le même répertoire de service :

  `[link text](article-name.md)`

- Un article des liens à partir d’un sous-répertoire service vers un article dans le répertoire racine :

  `[link text](../article-name.md)`

- Un article dans les liens d’annuaire racine vers un article dans un sous-répertoire de service : 

  `[link text](./service-directory/article-name.md)`

- Un article dans un service sous-répertoire de liens vers un article dans un autre sous-répertoire de service :

  `[link text](../service-directory/article-name.md)`
 

## <a name="links-to-anchors"></a>Liens vers les points d’ancrage

Vous n’êtes pas obligé de créer des points d’ancrage : ils sont générées automatiquement au moment pour tous les titres de H2 de publication. La seule chose que vous avez à faire est de créer des liens vers les sections H2.

- Pour lier un titre dans le même article :

  `[link](#the-text-of-the-H2-section-separated-by-hyphens)`  
  `[Create cache](#create-cache)`

- Pour créer un lien vers une ancre dans un autre article dans le même sous-répertoire :

  `[link text](article-name.md#anchor-name)`
  `[Configure your profile](media-services-create-account.md#configure-your-profile)`

- Pour créer un lien vers une ancre dans un autre sous-répertoire de service :

  `[link text](../service-directory/article-name.md#anchor-name)`
  `[Configure your profile](../service-directory/media-services-create-account.md#configure-your-profile)`

Une des façons d’automatiser le processus de création de liens dans vos articles de liens d’ancrage généré sont [MarkdownAnchorLinkGenerator - un outil pour générer des liens d’ancrage pour ACOM dans le format approprié](https://github.com/Azure/Azure-CSI-Content-Tools/tree/master/Tools/ACOMMarkdownAnchorLinkGenerator).

## <a name="links-from-includes"></a>Inclut des liens à partir de

Inclure dans la mesure où sont situés dans un autre répertoire, vous devrez utiliser plus les chemins d’accès relatifs comme illustré ci-dessous. Pour créer un lien vers un article à partir d’un fichier include, utilisez ce format :

    [link text](../articles/service-folder/article-name.md)
    
En savoir plus sur l’utilisation d’un fichier inclus dans les [instructions d’extensions démarque personnalisé](custom-markdown-extensions.md#includes).

## <a name="links-in-selectors"></a>Liens de sélecteurs

Si vous avez sélecteurs incorporés dans un inclure, utilisez ce type de liaison : 

    > [AZURE. LISTE de sélection (Dropdown1 | Dropdown2)]     -  [(Texte1 | Exemple1)](../articles/service-folder/article-name1.md)
    - [(Texte1 | Example2)](../articles/service-folder/article-name2.md)
    - [(Texte2 | Exemple3)](../articles/service-folder/article-name3.md)
    - [(Texte2 | Exemple4)](../articles/service-folder/article-name4.md)


## <a name="reference-style-links"></a>Liens de style de référence

Vous pouvez utiliser des liens de style de référence pour améliorer la lisibilité de votre contenu source. Les liens de style de référence remplacent la syntaxe de la liaison en ligne avec la syntaxe simplifiée qui vous permet de déplacer les URL de grande taille à la fin de cet article. Voici un exemple de Daring boule de feu :

Texte en ligne :

    I get 10 times more traffic from [Google][1] than from [Yahoo][2] or [MSN][3].

Lier des références à la fin de cet article :

    <!--Reference links in article-->
    [1]: http://google.com/
    [2]: http://search.yahoo.com/  
    [3]: http://search.msn.com/

Veillez à qu'inclure l’espace après le signe deux-points, avant le lien. Lorsque vous liez à d’autres articles techniques, si vous avez oublié d’inclure un espace, le lien est incorrecte dans l’article publié. 

## <a name="link-to-acom-pages-that-are-not-part-of-the-technical-documentation-set"></a>Créer un lien vers les pages ACOM qui ne font pas partie de la documentation technique

Pour créer un lien vers une page sur ACOM (par exemple, une page tarification, SLA page ou une autre valeur qui n’est pas un article de la documentation), utilisez une URL absolue, tout en omettant les paramètres régionaux. L’objectif ici est acceptés des liens dans GitHub et sur le site rendu :

    [link text](http://azure.microsoft.com/pricing/details/virtual-machines/)


## <a name="link-to-msdn-or-technet"></a>Lien vers MSDN ou TechNet

Lorsque vous avez besoin de créer un lien vers MSDN ou TechNet, cliquez sur le lien vers la rubrique complè et supprimer l’en-us de paramètres régionaux à partir du lien. 

### <a name="use-friendly-link-text-for-all-links"></a>Utiliser le texte du lien convivial pour tous les liens

Les mots que vous incluez un lien doivent être conviviales : en d’autres termes, ils doivent être normales mots en anglais ou le titre de la page que cible. N’utilisez pas « cliquez ici ». Il est incorrect pour SEO et ne décrire la cible.

**Corriger :**

- `For more information, see the [contributor guide index](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).`

- `For more details, see the [SET TRANSACTION ISOLATION LEVEL](https://msdn.microsoft.com/library/ms173763.aspx) reference.`

**Incorrect :**

- `For more details, see [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).`

- `For more information, click [here](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).`


## <a name="fwlinks"></a>Liens vers

Éviter les liens vers (notre système redirection) dans le contenu de azure.microsoft.com. Ils doivent être utilisés uniquement en dernier recours lorsque vous avez besoin créer un lien vers une page dont vous ne savez pas encore l’URL. Ils sont pratiquement jamais réellement nécessaires. Pour ACOM, vous définissez le nom du fichier, afin que vous sachiez qu’il sera à l’avance. Pour une rubrique de la bibliothèque qui n’est pas encore été publiée, vous pouvez créer un lien qui utilise la rubrique GUID afin que vous n’êtes pas obligé d’utiliser un lien.

Si vous devez utiliser un lien dans une page web, incluez le paramètre P modifiez-en une redirection permanente :

    http://go.microsoft.com/fwlink/p/?LinkId=389595

Lorsque vous collez l’URL cible dans l’outil lien, n’oubliez pas de supprimer les paramètres régionaux si votre lien cible est ACOM, ou les MSDN ou TechNet la bibliothèque.

## <a name="remember-the-azure-library-chrome"></a>N’oubliez pas de chrome bibliothèque Azure !
Si vous voulez créer un lien vers une rubrique Azure bibliothèque qui se trouve sous [ce nœud](https://msdn.microsoft.com/library/azure), n’oubliez pas de spécifier la Azure chrome dans le lien (/ azure /). Le chrome Azure permet de partager les options de navigation ACOM et affiche uniquement le contenu Azure de la bibliothèque MSDN. Un lien dans l’étendue correctement ressemble à ceci :

    http://msdn.microsoft.com/library/azure/dd163896.aspx

Dans le cas contraire, la page apparaîtront dans la vue MSDN standard, avec l’arbre de MSDN entière.

### <a name="contributors-guide-links"></a>Liens de Guide des collaborateurs

- [Article de présentation](./../README.md)
- [Index des articles sur les instructions](./contributor-guide-index.md)

<!--image references-->
[1]: ./media/create-tables-markdown/table-markdown.png
[2]: ./media/create-tables-markdown/break-tables.png
