<properties
pageTitle="Installer et configurer les outils de création de GitHub"
description="Outils et les étapes pour vous préparer pour la création de contenu à Azure dans GitHub."
services="contributor-guide"
documentationCenter=""
authors="tysonn"  
manager="carolz" />

<tags
ms.service="contributor-guide"
 ms.devlang=""
 ms.topic="article"
  ms.tgt_pltfrm=""
  ms.workload=""
  ms.date="01/19/2015"
  ms.author="tysonn" />

#<a name="install-and-set-up-tools-for-authoring-in-github"></a>Installer et configurer les outils de création de GitHub

Suivez les étapes décrites dans cet article pour configurer des outils pour contribuer à la documentation technique Azure. Collaborateurs occasionnels et minimes permet probablement le GitHub interface utilisateur décrite à l’étape 2.

Si vous ne maîtrisez pas Git, vous souhaiterez peut-être réviser terminologie Git : [https://help.github.com/articles/github-glossary](https://help.github.com/articles/github-glossary). En outre, ce thread StackOverflow contient un glossaire des termes Git que vous pouvez rencontrer dans cette série d’étapes : [http://stackoverflow.com/questions/7076164/terminology-used-by-git](http://stackoverflow.com/questions/7076164/terminology-used-by-git)

## <a name="contents"></a>Contenu

- [Créer un compte GitHub et configurer votre profil](#create-a-github-account-and-set-up-your-profile)
- [S’inscrire à Disqus](#sign-up-for-disqus)
- [Déterminer si vous devez réellement de suivre le reste des étapes suivantes](#determine-whether-you-really-need-to-follow-the-rest-of-these-steps)
- [Autorisations dans GitHub](#permissions-in-github)
- [Installer Git pour Windows](#install-git-for-windows)
- [Activer l’authentification à deux facteurs](#enable-two-factor-authentication)
- [Installer un éditeur promotions](#install-a-markdown-editor)
- [Configurer Atom](#configure-atom)
- [Branche dans le référentiel et copiez-le sur votre ordinateur](#fork-the-repository-and-copy-it-to-your-computer)
- [Configurer votre nom d’utilisateur et votre messagerie localement](#configure-your-user-name-and-email-locally)
- [Étapes suivantes](#next-steps)

## <a name="create-a-github-account-and-set-up-your-profile"></a>Créer un compte GitHub et configurer votre profil

Pour contribuer au contenu technique Azure, vous avez besoin d’un compte [GitHub](http://www.github.com) .

Si vous êtes un collaborateur Microsoft, vous devez configurer votre compte GitHub de sorte que vous êtes clairement identifié comme un employé de Microsoft. Configurer votre profil comme suit :

- **Image de profil**: une image de vous (obligatoire)
- **Nom**: votre nom et prénom (obligatoire)
- **Messagerie**: votre adresse de messagerie Microsoft (facultatif)
- **Société**: Microsoft Corporation (obligatoire)
- **Emplacement**: votre emplacement (facultatif) de la liste

Votre profil doit ressembler à ce profil :

<p align="center">
 ![Exemple de profil GitHub](./media/tools-and-setup/githubprofile.png)

## <a name="sign-up-for-disqus"></a>S’inscrire à Disqus

Chaque article technique Azure publié comporte un flux de commentaire fourni par le service Disqus.

 ![Logo de disque](./media/tools-and-setup/discus.png)

Si vous êtes un employé de Microsoft, et si vous êtes l’auteur d’ou un collaborateur vers un article, vous devez vous inscrire Disqus afin que vous pouvez participer au flux de commentaire pour l’article.

1. S’inscrire à un compte sur [http://www.disqus.com/](http://www.disqus.com/)
2. Remplissez votre profil comme suit :

 - **Nom complet**: votre nom complet sont affichées dans votre liste de carnet d’adresses Microsoft, ainsi que les informations entre crochets, ce qui correspond à votre alias plus @MSFT. Format : *tout d’abord derniers [alias@MSFT] *
 - **Emplacement**: votre emplacement
 - **Bio courte**: votre titre

## <a name="determine-whether-you-really-need-to-follow-the-rest-of-these-steps"></a>Déterminer si vous devez réellement de suivre le reste des étapes suivantes

Vous devrez pas suivez les étapes décrites dans cet article. Elle dépend le tri de contribution contenu souhaité ou souhaitez apporter.

###<a name="submit-a-text-only-change-to-an-existing-article"></a>Envoyez une modification en lecture seule à un article existant

Si uniquement, vous avez besoin ou que vous voulez mettre à jour textuelles à un article existant, il est probablement inutile de suivre le reste des étapes. Vous pouvez utiliser l’éditeur de GitHub-based démarque pour envoyer vos modifications. Cliquez simplement sur le lien GitHub dans l’article que vous souhaitez modifier :

 ![Exemple de profil GitHub](./media/tools-and-setup/contributetogit.png)

 Ensuite, cliquez sur l’icône edit dans la version GitHub de l’article

 ![Exemple de profil GitHub](./media/tools-and-setup/editicon.PNG)

 L’éditeur de site web facile à utiliser qui facilite la soumettre les modifications s’ouvre. Vous n’avez pas besoin de suivre les autres étapes décrites dans cet article.

###<a name="all-other-changes"></a>Toutes les autres modifications
La GitHub UI prend en charge la création de nouveaux fichiers et glisser-déplacer des images. Toutefois, lorsque vous travaillez dans l’interface utilisateur, gestion des branches peuvent prêter à confusion nous vous recommandons généralement vous installez les outils et apprendre à utiliser les commandes pour la création et gestion d’articles. Si vous souhaitez utiliser l’interface utilisateur, voir :

- [Création de fichiers sur Github](https://github.com/blog/1327-creating-files-on-github)
- [Télécharger des fichiers dans vos référentiels](https://github.com/blog/2105-upload-files-to-your-repositories)

Pour les types suivants de travail, nous vous recommandons vivement installer et découvrez comment utiliser les outils :

 - Apporter des modifications majeures vers un article
 - Créer et publier un nouvel article
 - Ajouter des images ou mise à jour des images
 - Mise à jour d’un article sur une période de jours sans publication modifications de ces jours
 - Création de contenu pour une mise à jour a pour accéder un jour donné à une certaine heure

##<a name="permissions-in-github"></a>Autorisations dans GitHub

Toute personne disposant d’un compte GitHub peut contribuer au contenu technique Azure via notre référentiel public en [https://github.com/Azure/azure-content](https://github.com/Azure/azure-content). Aucune autorisation spéciale n’est nécessaires.

Si vous êtes un PM Microsoft ou rédacteur personnes travaillant sur le contenu, vous devez travailler dans notre privé Azure référentiel de contenu, azure-contenu-valeur_nominale. Visitez [https://repos.opensource.microsoft.com](https://repos.opensource.microsoft.com ) pour demander les autorisations de lecture qui vous permet d’effectuer des versements via la mis en pension privé - se connecter à GitHub à l’aide du bouton > cliquez sur Azure > cliquez sur **participer à une équipe** ou **participer à une autre équipe**, puis recherchez et rejoindre le groupe **azure à contenu à lire** .

## <a name="install-git-for-windows"></a>Installer Git pour Windows

Installez Git pour Windows à partir de [http://git-scm.com/download/win](http://git-scm.com/download/win). Ce téléchargement installe le système de contrôle de version Git, et il installe Git Bash, l’application de ligne de commande que vous utiliserez pour interagir avec votre référentiel Git local.

Vous pouvez accepter les paramètres par défaut ; Si vous souhaitez que les commandes soit disponible dans la ligne de commande Windows, sélectionnez l’option qui lui permet de.

<p align="center">
 ![Exemple de profil GitHub](./media/tools-and-setup/gitbashinstall.png)

(Remarque : il s’agit pas le même que « Github pour Windows ». « Github pour Windows » est un autre outil graphique qui fonctionne également si vous voulez des recherches sur vous-même. [https://windows.github.com/](https://windows.github.com/))

## <a name="enable-two-factor-authentication"></a>Activer l’authentification à deux facteurs

Vous devez activer l’authentification à deux facteurs (2FA) sur votre compte GitHub s’il s’agit du référentiel de contenu privé. Il est requise dans le référentiel privé.

Pour ce faire, suivez les instructions dans les deux GitHub rubriques d’aide suivantes :

- [À propos de l’authentification à deux facteurs](https://help.github.com/articles/about-two-factor-authentication/)
- [Création d’un jeton d’accès pour une utilisation de ligne de commande](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)

Lorsque vous créez le jeton, sélectionnez toutes les étendues disponibles dans l’interface utilisateur de création de jeton ([plus de détails sur chaque étendue](https://developer.github.com/v3/oauth/#scopes))

Après avoir activé 2FA, vous devez entrer le jeton d’accès à la place de votre mot de passe GitHub à l’invite de commandes lorsque vous essayez d’accéder à un référentiel GitHub à partir de la ligne de commande. Le jeton d’accès n’est pas le code d’authentification que vous obtenez un message texte lorsque vous configurez 2FA. Il s’agit d’une chaîne longue qui ressemble à ceci : fdd3b7d3d4f0d2bb2cd3d58dba54bd6bafcd8dee. Quelques notes à ce sujet :

- Lorsque vous créez votre jeton d’accès, enregistrez-le dans un fichier texte à rendre accessibles lorsque vous en avez besoin.

- Lorsque vous avez besoin coller le jeton, connaître plus tard, il existe deux façons pour coller dans la ligne de commande :

 - Cliquez sur l’icône dans le coin supérieur gauche de la fenêtre de ligne de commande > Modifier > Coller.
 - Cliquez sur l’icône dans le coin supérieur gauche de la fenêtre, cliquez sur Propriétés > Options > Mode d’édition rapide. Cela configure la ligne de commande afin de le coller en cliquant dans la fenêtre de ligne de commande.

## <a name="install-a-markdown-editor"></a>Installer un éditeur de démarque

Nous créer du contenu à l’aide de la notation simple « démarque » dans les fichiers, plutôt que complexe « balisage » (HTML, XML, etc.). Par conséquent, vous devez installer un éditeur promotions.

- **Atom**: la plupart d'entre nous utilise l’éditeur promotions de GitHub Atom : [http://atom.io](http://atom.io). Il ne nécessite pas une licence pour une utilisation professionnelle. Elle doit vérifier l’orthographe.

- **Le bloc-notes**: vous pouvez utiliser le bloc-notes pour une option très léger.

- **Prose**: il s’agit un éditeur démarque légère, élégante, en ligne et open source qui offre un aperçu. Visitez [http://prose.io](http://prose.io) et autorisez les Prose dans votre référentiel.

- **[Code de visual Studio](https://www.visualstudio.com/products/code-vs.aspx)** - entrée de Microsoft dans cet espace.

## <a name="configure-atom"></a>Configurer Atom

Si vous utilisez Atom, vous devez configurer quelques points.

- Atom par défaut à l’aide de 2 espaces pour les onglets, mais démarque attend 4 espaces. Si vous laissez la valeur par défaut de deux, votre article est superbes dans l’aperçu local, mais pas lorsqu’elle est importée dans Azure. Par conséquent, configurez Atom pour utiliser les 4 espaces : ce paramètre se trouve sous fichier > Paramètres > Paramètres de l’éditeur > onglet longueur.
- Vous devrez probablement également souhaitez activer renvoyer à la ligne logiciel dans cette section trop, qui est identique à celle en tant que « retour » dans le bloc-notes.
- Pour activer l’aperçu démarque, cliquez sur Packages > Aperçu démarque > Aperçu bascule. Vous pouvez utiliser Ctrl-Maj-M pour activer ou désactiver l’aperçu du mode HTML.

## <a name="fork-the-repository-and-copy-it-to-your-computer"></a>Branche dans le référentiel et copiez-le sur votre ordinateur

1. Créer une branche du référentiel dans GitHub - atteindre le haut à droite de la page et cliquez sur le bouton branche. Si vous y êtes invité, sélectionnez votre compte en tant que l’emplacement où la branche doit être créée. Cela crée une copie du référentiel au sein de votre compte Git concentrateur. En règle générale, rédacteurs techniques et chefs de projet doivent branche azure-contenu-valeur_nominale, la mis en pension privé. Branche azure-types de contenu, le public mis en pension doivent collaborateurs de la Communauté. Vous ne devez branche une fois ; après la configuration de votre première, si vous voulez copier votre branche vers un autre ordinateur, vous ne devez exécuter les commandes qui suivent dans cette section pour copier la mis en pension sur votre ordinateur.  Si vous choisissez de créer des branches des deux référentiels, vous devrez créer une branche pour chaque référentiel.

2. Copier le personnel jeton d’accès que vous avez obtenu à partir de [https://github.com/settings/tokens](https://github.com/settings/tokens). Vous pouvez accepter les autorisations par défaut pour le jeton.  Enregistrer le jeton d’accès personnel dans un fichier texte pour une utilisation ultérieure.

3. Copiez ensuite le référentiel sur votre ordinateur avec vos informations d’identification incorporées dans la chaîne de commande.  Pour ce faire, ouvrez Git Bash et exécuter en tant qu’administrateur. À l’invite de commandes, entrez la commande suivante.  Cette commande crée un répertoire azure-content(-pr) sur votre ordinateur.  Si vous utilisez l’emplacement par défaut, il sera en c:\users<your Windows user name>\azure-content(-pr).

Mis en pension public :

        git clone https://[your GitHub user name]:[token]@github.com/<your GitHub user name>/azure-content.git

Mis en pension privé :

        git clone https://[your GitHub user name]:[token]@github.com/<your GitHub user name>/azure-content-pr.git

Par exemple, cette commande cloner peut ressembler à ceci :

        git clone https://smithj:b428654321d613773d423ef2f173ddf4a312345@github.com/smithj/azure-content-pr.git  

## <a name="set-remote-repository-connection-and-configure-credentials"></a>Connexion référentiel distant et configurer les informations d’identification

Créer une référence vers le référentiel racine en entrant ces commandes. Définit les connexions au référentiel dans GitHub afin que vous pouvez obtenir les dernières modifications sur votre ordinateur local et faire progresser vos modifications à GitHub. Cette commande configure également votre jeton localement afin que vous n’êtes pas obligé d’entrer votre nom et le mot de passe chaque fois que vous essayez d’accéder à la mis en pension en amont et votre branche sur GitHub.

Mis en pension public :

        cd azure-content
        git remote add upstream https://[your GitHub user name]:[token]@github.com/Azure/azure-content.git
        git fetch upstream

Mis en pension privé :

        cd azure-content-pr
        git remote add upstream https://[your GitHub user name]:[token]@github.com/Azure/azure-content-pr.git
        git fetch upstream

Généralement, cela prend un certain temps. Une fois que vous procédez ainsi, vous ne devez branche à nouveau ou entrez à nouveau vos informations d’identification. Vous devez copier les branches sur un ordinateur local à nouveau si vous configurez les outils sur un autre ordinateur.


## <a name="configure-your-user-name-and-email-locally"></a>Configurer votre nom d’utilisateur et votre messagerie localement

Pour vous assurer que vous êtes répertorié correctement en tant que collaborateur, vous devez configurer votre nom d’utilisateur et votre messagerie localement dans Git.

1. Démarrez Git Bash, puis basculez en azure contenu ou azure-contenu-valeur_nominale :

   ````
   cd azure-content
   ````

 ou

   ````
   cd azure-content-pr
   ````

2. Configurer votre nom d’utilisateur afin qu’elle corresponde à votre nom tel que vous le configuré dans votre profil GitHub :

    ````
    git config --global user.name "John Doe"
    ````
3. Configurer votre messagerie afin qu’elle corresponde à la messagerie principale désigné dans votre profil GitHub ; Si vous êtes un employé MSFT, elle doit être votre adresse de messagerie MSFT :

    ````
    git config --global user.email "alias@example.com"
    ````
4. Type de `git config -l` et passez en revue vos paramètres locaux pour vous assurer que le nom d’utilisateur et messagerie électronique dans la configuration sont corrects.

##<a name="next-steps"></a>Étapes suivantes

- Comprendre le type de contenu auquel appartient la mis en pension contenu technique et savoir ce qui n’appartient pas. Consultez les [conseils de canal contenu](./content-channel-guidance.md)!
- Suivez [ces étapes pour créer ou modifier un article et envoyez-la pour la publication](./git-commands-for-master.md).
- Copiez [le modèle démarque](../markdown templates/markdown-template-for-new-articles.md) comme base pour un nouvel article.
- Utilisez [cette liste de vérification pour vérifier votre demande d’extraction répondra les critères de qualité](./contributor-guide-pr-criteria.md) pour une fusion.


###<a name="contributors-guide-navigation"></a>Navigation de guide des collaborateurs

- [Article de présentation](./../README.md)
- [Index des articles sur les instructions](./contributor-guide-index.md)



<!--Anchors-->
[Use a customer-friendly voice]: #use-a-customer-friendly-voice
[Consider localization and machine translation]: #consider-localization-and-machine-translation
[other style and voice issues to watch for]: #other-style-and-voice-issues-to-watch-for


[Create a GitHub account and set up your profile]: #create-a-github-account-and-set-up-your-profile
[Determine whether you really need to follow the rest of these steps]: #determine-whether-you-really-need-to-follow-the-rest-of-these-steps
[Permissions in GitHub]: #permissions-in-github
[Install Git for Windows]: #install-git-for-windows
[Enable two-factor authentication]: #enable-two-factor-authentication
[Install a markdown editor]: #install-a-markdown-editor
[Fork the repository and copy it to your computer]: #fork-the-repository-and-copy-it-to-your-computer
[Install git-credential-winstore]: #install-git-credential-winstore
[Sign up for Disqus]: #sign-up-for-disqus
[Configure your user name and email locally]: #configure-your-user-name-and-email-locally
[Next steps]: #next-steps
