# <a name="steps-to-follow-when-you-retire-or-change-the-name-of-an-acom-technical-article"></a>Étapes à suivre lorsque vous retirer ou modifiez le nom d’un article technique ACOM

Ce guide est destiné petites et moyennes entreprises qui sont répertoriées sous forme de l’auteur d’un article qui doit être supprimée à partir de la section documents techniques de azure.microsoft.com. Les étapes s’appliquent également si un fichier est renommé.

Si vous êtes membre de notre communauté Azure alors que vous estimez qu'un article doit être supprimée pour une raison quelconque, n’hésitez pas laisser un commentaire dans le flux de commentaire Disqus pour l’article afin d’indiquer l’auteur qu'est erronée avec l’article.

Auteurs PME inutile de suivre plusieurs étapes pour désactiver normalement du contenu, les utilisateurs du site Web n’auront une mauvaise expérience lorsque nous annulez du contenu à partir du site. Suppression de l’article ou en modifiant son nom doit être la dernière chose qui se passe !

## <a name="step-1-set-the-article-to-no-indexno-follow-and-republish-it-recommended"></a>Étape 1 : Définir l’article à aucun index/non suivre et republier (recommandé)

La première chose à faire est republier l’article comme non-index/non-suivre quelques semaines avant de le supprimer. Il s’agit la meilleure pratique « préliminaires » de suppression de contenu. Cela supprime l’article index des moteurs de recherche afin de ne trouver l’article dans Rechercher. [Consultez le wiki interne pour plus d’informations.](https://microsoft.sharepoint.com/teams/azurecontentguidance/wiki/Pages/Remove%20published%20pages%20and%20request%20redirects.aspx)

## <a name="step-2-manage-inbound-links-required"></a>Étape 2 : Gérer les liens entrants (obligatoire)

Déterminer si certains liens entrants non Microsoft à votre contenu sont. Fréquemment, blogs, forums et autres contenus sur le web pointe vers des articles. Fréquemment, vous pouvez travailler avec les propriétaires du blog pour modifier ces liens, et vous pouvez supprimer ou mettre à jour les liens à partir de publications de forums. Outils analytique Web peuvent vous indiquer si certains liens entrants trafic élevé que vous devrez peut-être gérer de cette manière sont.

## <a name="step-3-remove-all-crosslinks-to-the-article-from-the-technical-content-repository-required"></a>Étape 3 : Supprimer tous les croisements à l’article à partir du référentiel de contenu technique (obligatoire)

Ne comptez pas sur des redirections pour prendre en charge des croisements à partir d’autres articles. Mise à jour ou supprimer la croix fait référence à l’article vous supprimez ou renommer, y compris dans les articles appartenant à d’autres personnes.

1. Vous assurer que vous utilisez une branche locale à jour – exécuter `git pull upstream master` (ou la variation appropriée sur cette commande.

2.  Passez en revue le dossier azure-contenu-valeur_nominale/articles et le dossier azure-contenu-valeur_nominale/inclut un des articles et comprend que le lien vers l’article que vous voulez retirer et supprimer les croisements ou les remplace par un nouveau croisements approprié. Vous pouvez utiliser une recherche et remplacement utilitaire pour trouver les croisements si vous avez installé. Si vous n’avez pas, vous pouvez utiliser Windows PowerShell gratuitement ! Voici comment utiliser PowerShell pour trouver les croisements :

 un. Démarrez Windows PowerShell.

 b. À l’invite PowerShell, remplacez le dossier azure-contenu-pr\articles :

 `cd azure-content-pr\articles`

 c. Tapez cette commande, qui permet de répertorier tous les fichiers qui contiennent une référence à l’article que vous voulez supprimer :

 `Get-ChildItem -Recurse -Include *.md* | Select-String "<the name of the topic you are deleting>" | group path | select name`

  Si vous préférez envoyer la liste des noms de fichiers dans un fichier texte (dans ce psoutput.txt cas, nommée), vous pouvez :

  `Get-ChildItem -Recurse -Include *.md* | Select-String "<the name of the topic you are deleting>" | group path | select name | Out-File C:\Users\<your account>\psoutput.txt`

3. Ajouter et valider toutes vos modifications, les pousser à votre branche et créer une demande d’extraction pour déplacer vos modifications à partir de votre branche vers la branche maître du référentiel principal.

## <a name="step-4-update-the-fwlink-tool-required"></a>Étape 4 : Mettre à jour l’outil lien (obligatoire)

Vérifier l’outil lien pour les liens vers qui peuvent pointent vers l’article. Pointez sur les liens vers au contenu de remplacement ; Si vous n’êtes pas l’alias qui possède le lien, rejoindre une réunion. Si les propriétaires ne mettre à jour le lien, fichier un tickets avec MSCOM pour que le lien modifié. Plus d’informations - [wiki interne](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Manage%20inbound%20links%20to%20retired%20topics.aspx).

## <a name="step-5-remove-all-crosslinks-to-the-article-from-other-pages-on-azuremicrosoftcom-and-create-a-redirect-for-the-retired-page-if-appropriate-required"></a>Étape 5 : Supprimer tous les croisements à l’article à partir d’autres pages azure.microsoft.com et créer une redirection pour la page déclassée, si approprié (obligatoire)

Vous devrez utiliser la personne qui gère et met à jour de la page de lancement de documentation de votre service pour cette partie. Contactez votre partenaire de contenu d’équipe si vous ne savez pas qui est cette personne. La personne qui gère et met à jour de la page d’accueil document devez effectuer deux opérations :

1. Dans Visual Studio, rechercher dans **l’ensemble** ACOM web solution renvois vers le fichier à supprimer. Supprimer des références croisées, ou les remplacer par une référence croisée mis à jour. Vous devez supprimer les liens HTML, ainsi que les chaînes de ressources connexes pour les liens HTML. Plus d’informations - voir le [wiki interne](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Create%20or%20edit%20a%20service%20landing%20page%20or%20left%20nav.aspx)

2. Si un article de remplacement existe, créez une redirection. Plus d’informations - voir le [wiki interne](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Remove%20published%20pages%20and%20request%20redirects.aspx).

3. Vérifier les modifications dans le référentiel.

## <a name="step-6-retire-the-article"></a>Étape 6 : Supprimer l’article

Une fois que vous avez terminé les étapes précédentes et ces modifications sont publiées, vous pouvez supprimer l’article à partir du référentiel. 

**Importantes :** Lorsque vous supprimez des fichiers, vous devez utiliser le `git add --all` commande.

## <a name="step-7-remove-links-from-msdn-required"></a>Étape 7 : Supprimer des liens dans MSDN (obligatoire)

Passez en revue l’outil q & a contenu les liens rompus à la rubrique supprimée ou renommée et supprimer/fix les liens dans toutes les rubriques MSDN affectées.

## <a name="step-8-remove-cached-pages-from-search-engines-only-if-absolutely-necessary"></a>Étape 8 : Supprimez les pages mises en cache les moteurs de recherche (uniquement si cela est absolument nécessaire)

Effectuez ce uniquement si le contenu doit être supprimé rapidement en raison de problèmes de client légales ou difficiles. Par recommandations à partir de Google, suppressions de page priorité normale doivent simplement être gérées par les processus de moteur de recherche Natural Keyboard. Accéder à ces pages web à supprimer des pages web mises en cache de moteurs de recherche :

[Bing](https://www.bing.com/webmaster/tools/content-removal?rflid=1)
[Google](https://www.google.com/webmasters/tools/removals?pli=1)


### <a name="contributors-guide-links"></a>Liens de guide des collaborateurs

- [Article de présentation](./../README.md)
- [Index des articles sur les instructions](./contributor-guide-index.md)
