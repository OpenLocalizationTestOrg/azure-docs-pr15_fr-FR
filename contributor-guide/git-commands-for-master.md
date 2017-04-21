<properties pageTitle="Commandes GIT pour créer un nouvel article ou mise à jour d’un article existant" description="Étapes pour travailler avec le Azure technique de contenu GitHub référentiels." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="carolz" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="01/16/2015" ms.author="tysonn" />

# <a name="git-commands-for-creating-a-new-article-or-updating-an-existing-article"></a>Commandes GIT pour créer un nouvel article ou mise à jour d’un article existant


## <a name="standard-process-working-from-master"></a>Processus standard (utilisation du masque)
Suivez les étapes décrites dans cet article pour créer une branche de travail local sur votre ordinateur afin que vous pouvez créer un nouvel article pour la section documents techniques du azure.microsoft.com ou mettre à jour un article existant.

1. Démarrez Git Bash (ou l’outil de ligne de commande que vous utilisez pour Git).

 **Remarque :** Si vous travaillez dans le référentiel public, placez-vous azure-contenu-valeur_nominale azure contenu dans toutes les commandes.

2. Remplacez azure-contenu-valeur_nominale :

        cd azure-content-pr
3. Consultez la branche maître :

        git checkout master

4. Créer une nouvelle branche travail local dérivée de la branche maître :

        git pull upstream master:<working branch>


5. Déplacer vers la nouvelle branche de travail :

        git checkout <working branch>

6. Indiquer votre branche que vous avez créé la branche travail local :

        git push origin <working branch>

7. Créer votre nouvel article ou apporter des modifications à un article existant. Utilisez l’Explorateur Windows pour ouvrir et créer de nouveaux fichiers démarque, utilisez Atom (http://atom.io) comme votre éditeur promotions. Une fois que vous avez créé ou modifié votre article et les images, accédez à l’étape suivante.

8. Ajouter et valider les modifications que vous avez effectuées :

        git add .
        git commit –m "<comment>"
        
   Ou, pour ajouter la spécifique uniquement les fichiers vous modifié :

        git add <file path>
        git commit –m "<comment>"

   Si vous avez supprimé des fichiers, vous devez utiliser ceci :
   
        git add --all
        git commit -m "<comment>"

9. Mettre à jour votre agence de travail avec les modifications en amont :

        git pull upstream master

10. Diffuser les changements à vos branche sur GitHub :

        git push origin <working branch>

12. Lorsque vous êtes prêt à envoyer votre contenu dans la branche en amont maître de mise en place, validation, et/ou publication, dans le GitHub UI, créez une demande d’extraction à partir de votre branche vers la branche maître.

13. Si vous êtes un employé actuellement dans le référentiel privé, les modifications que vous envoyez sont automatiquement transférées et un lien intermédiaire écrit à la demande d’extraction. Veuillez vérifier votre contenu intermédiaire et connectez-vous les commentaires de requête d’extraction en ajoutant le commentaire **#signe-désactiver** .  Cela indique que les modifications sont prêtes à être poussé live.  Si vous ne voulez pas la demande d’extraction pour être accepté - si vous mettez simplement en place les modifications - Ajouter la note **hold désactiver #** à la demande d’extraction.

14. L’acceptation de requête d’extraction examine votre demande d’extraction, fournit des commentaires, et/ou accepte votre demande d’extraction. 

15. Vous pouvez également vérifier votre article publié ou les modifications à

 http://Azure.Microsoft.com/documentation/articles/*name-of-your-article-without-the-MD-extension*

## <a name="publishing"></a>Publication

- Articles sont publiés à environ 10:00 AM et 3:00 heure du Pacifique, du lundi au vendredi. Il peut prendre jusqu'à 30 minutes pour articles qui s’affiche en ligne après publication. N’oubliez pas que votre demande d’extraction doit être fusionnées par un relecteur demande extraire avant que les modifications peuvent être incluses dans la prochaine publication planifiée exécuter. Vous devez utiliser avec votre relecteur demande extraire avance pour assurer qu'une demande d’extraction fusionnée pour une publication spécifique exécuter. Dans le cas contraire, PRs sont révisées dans l’ordre qu’ils ont été soumis.

- Si vous êtes un employé qui travaille dans le référentiel privé, toutes les demandes d’extraction sont soumis aux règles de validation qui doivent être résolus avant la demande d’extraction peut être fusionnée. 

## <a name="working-with-release-branches"></a>Utilisation de la version branches

Lorsque vous travaillez avec une branche de publication, la meilleure façon de créer une agence de travail à partir de la branche release consiste à utiliser la syntaxe de cette commande :

    git checkout upstream/<upstream branch name> -b <local working branch name>

Cela crée la succursale locale directement à partir de la branche en amont, pour éviter d’obtenir toute fusion locale.

