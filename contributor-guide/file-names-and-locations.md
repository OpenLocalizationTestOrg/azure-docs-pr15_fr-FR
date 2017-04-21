<properties title="" pageTitle="Les noms de fichiers et les emplacements des articles techniques Azure" description="Explique la structure de fichier pour les articles et les conventions d’appellation, que vous devez suivre lorsque vous créez un nouvel article." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="required" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="03/14/2016" ms.author="tysonn" />

#<a name="file-names-and-locations-for-azure-technical-articles"></a>Les noms de fichiers et les emplacements des articles techniques Azure

Dans notre référentiel technique, nous utilisons un seul dossier (le dossier **articles** ) pour tous les articles. Il n’y a aucune hiérarchie des dossiers : live tous les articles dans la structure de fichier plat. Si vous créez des dossiers avec des articles qu’ils, vos articles ne peut pas être publiés.

Au lieu d’utiliser une structure de fichiers comme un principe organisation, nous utilisons une convention d’appellation de fichier strictes qui identifie clairement rubriques et qui contribue à assurer la détectabilité sur le web.

Voici ce que vous devez savoir :

+ [Règles]
+ [Motif]
+ [Exemples standards]
+ [Contenu Marketplace]
+ [Approbation de nom de fichier]

##<a name="rules"></a>Règles

- Noms de fichiers peuvent contenir des lettres minuscules, des nombres et des traits d’union uniquement. 
- Sans espace ni caractère de ponctuation. Utilisez les traits d’union pour séparer les mots et les nombres dans le nom du fichier.
- Pas plus de 80 caractères - il s’agit d’une publication limite du système
- Verbes d’action utilisation spécifiques, tels que développement, achètent, générer, résoudre les problèmes. Aucun mot - binaire.
- Aucun mot petite - ne contiennent pas une et, le, en ou, etc..
- Tous les fichiers doivent être en démarque et utiliser l’extension de fichier .md.
- Orthographe des mots éviter les acronymes approuvés ou inutiles dans les noms de fichier

Acronymes et initialisms dans les noms de fichier - instructions spécifiques :

- Sans utiliser d’abréviation les noms de service Azure - les premiers mots du nom de fichier doivent être la norme énoncés de nom de service ou d’une technologie Azure. 
-   N’autorisez pas rm ou processeur comme acronymes n’importe où dans un nom de fichier
- Autres abréviations normalisés sont acceptables comme bon vous semble dans les noms de fichier. 

##<a name="pattern"></a>Motif

Voici le modèle général :

 **service-Platform-Language-Content-Product-version.MD**

Utiliser les composants du modèle qui s’appliquent et examinez la liste des articles dans le référentiel pour avoir une idée des noms existants. Noms qui ne commencent pas par une plateforme de développement ou un nom de service est probablement suspect et glissement par le biais.

##<a name="standard-examples"></a>Exemples standards

Voici quelques exemples de noms valides qui suivent le modèle. :

- nuage-services-dotnet-continu-delivery.md
- Mobile-services-ios-get-started.md
- documentdb gérer account.md
- Mobile-Services-dotnet-backend-Get-Started-Settings-Sync.MD
- Active-Directory-Java-Authenticate-Users-Access-Control-Eclipse.MD
- Virtual-Machines-Install-Windows-Server-2008R2.MD
- fournisseur du état session cache aspnet
- Azure-SDK-dotnet-Release-Notes-2-8
- storsimple-Disaster-Recovery-Using-Azure-Site-Recovery

##<a name="marketplace-content"></a>Contenu Marketplace

Pour distinguer le contenu qui se concentre sur dons partenaire à la place de marché Azure, commencez les noms de fichiers avec « marketplace ». Ce contenu ne doit pas être trop courant, comme la plupart des contenus partenaire doivent être créés sur les sites web des partenaires.

- Marketplace-MongoDB-Virtual-Machines-Install-Windows-Server-2008R2.MD

##<a name="file-name-approval"></a>Approbation de nom de fichier

Il s’agit de la tâche de notre groupe d’extraire les relecteurs de requête permet de consulter des noms de fichiers lorsqu’un fichier est soumis au référentiel pour la première fois. Extraire demande réviseurs devraient Examinez le nom de fichier et fournir des commentaires via le flux de commentaire demande extraire si des modifications sont nécessaires. Le nom du fichier doit être corrigée avant la demande d’extraction ne soit acceptée. Les collaborateurs pouvant distribuer facilement la mise à jour à la demande d’extraction en attente.

##<a name="folder-names-in-the-repo"></a>Noms des dossiers dans le mis en pension

Dossiers doivent être créés uniquement pour les services et le nom du fichier doit correspondre à la ligne-bloc de service. Utilisez uniquement des lettres et des traits d’union et toutes les lettres minuscules. Obtenir l’approbation de l’administrateur de référentiel avant de créer un dossier qui n’est pas d’un service finale.

##<a name="changing-case-in-file-names"></a>Modification de la casse dans les noms de fichier

Systèmes d’exploitation Windows respectent la casse, afin que si vous devez modifier un nom de fichier pour corriger la casse, il est préférable d’effectuer un changement de fond, sauf si vous êtes en mesure d’apporter les modifications sur un Linux ou Mac. Par exemple :

  biztalk-administration-and-Development-Task-List-in-BizTalk-Services--> biztalk-services-administration-and-development-task-list

Pour renommer un fichier, utilisez la commande suivante :
```
  git mv <articles/service-folder/current-file-name.md> <articles/service-folder/new-file-name>
```

###<a name="contributors-guide-links"></a>Liens de Guide des collaborateurs

- [Article de présentation](./../README.md)
- [Index des articles sur les instructions](./contributor-guide-index.md)


<!--Anchors-->
[Règles]: #rules
[Motif]: #pattern
[Exemples standards]: #standard-examples
[Contenu Marketplace]: #marketplace-content
[Approbation de nom de fichier]: #file-name-approval
