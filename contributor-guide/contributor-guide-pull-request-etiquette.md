# <a name="pull-request-etiquette-and-best-practices-for-microsoft-contributors-to-azure-documentation"></a>Extraire l’étiquette de demande et des pratiques recommandées pour les collaborateurs de Microsoft Azure documentation

Pour publier les modifications apportées à la documentation, vous envoyez des demandes d’extraire à partir de votre branche. Chaque demande d’extraction doit réviser avant en cours de fusion. Lisez cet article pour mieux comprendre comment vous devez travailler avec les réviseurs demande extraire et comment vous pouvez créer des demandes d’extraction qui sont plus simple et rapide de passer en revue pour que la file d’attente de la demande extraire fonctionne mieux pour tout le monde.

## <a name="working-with-pull-request-reviewers"></a>Utilisation des réviseurs de requête d’extraction

Voici les notions de base que vous devez savoir sur l’utilisation des réviseurs de requête d’extraction. 

- <b>Comprendre le rôle du relecteur demande extraire. Le réviseur :</b>
  - Garantit la qualité de base du contenu
  - Empêche régression dans le référentiel
  - Fournit des commentaires avant la fusion

  Extraire demande réviseurs sont dans un rôle de gouvernance du contenu. Le but principal n’est ne pas à fusionner simplement tout ce qui est soumis aussi rapidement que possible. Ils doivent s’attendre commentaires qui vous demandera de mettre à jour, en particulier dans les articles de nouvelles et révisées intensément.

- <b>Planifiez avec votre réviseur de demande d’extraction :</b>
  - Pour les demandes d’extraction avec une priorité élevée
  - Extraire les demandes de versions minuté/daté
  - Demandes extraire que modifient ou ajouter un nombre important de fichiers

- <b>SLA pour extraire demande de révision</b>

  Dans le référentiel privé, chaque fois que votre demande d’extraction entre la file d’attente de demande extraire avec l’étiquette prêt à fusionner, l’équipe tente de consulter la demande d’extraction dans les 12 heures ouvrables (lundi au vendredi, 8 h à 17 h) et fournir des commentaires ou le fusionner si aucun commentaire n’est requise. Ce contrat SLA s’applique à la loi de révision Valeur_nominale, ne pas fusionner. PRs seront fusionnés lorsqu’ils répondent aux [critères pour une fusion](contributor-guide-pr-criteria.md). 

## <a name="make-the-pull-request-queue-work-better-for-everyone"></a>Vérifiez la file d’attente de la demande extraire fonctionnent mieux pour tout le monde

Il existe deux réalités de base dans la file d’attente Valeur_nominale :

- Demande d’extraction qui sont petite dans l’étendue et qui contient les modifications beaucoup prend moins de temps à passer en revue. 
- Extraire des demandes qui sont volumineux dans l’étendue ou qui contiennent des types différents, mixtes de changements durer plus longtemps passer en revue.

Vous pouvez rendre la file d’attente de la demande extraire fonctionnent mieux en suivant les meilleures pratiques suivantes :

- Distinctes mises à jour mineures vers des articles existants à partir de nouveaux articles ou réécritures principales. Travailler sur ces modifications dans branches séparées de travail. 

- Lorsque vous supprimez des articles ou des images, ne pas combiner les suppressions en fonction des additions de contenu nouveau ou mises à jour. Gérer le modifications/nouveau contenu dans une branche de travail séparée.

- Pour les versions ou refactorisation du contenu, planifiez avec votre relecteur Valeur_nominale. Vous devrez peut-être sa aide pour créer une branche de publication ou pour coordonner les heures de fusion dont l’heure de publication pour que votre contenu est publié au moment opportun.

- Si vous tentez de coordonner les mises à jour effectuées dans la mis en pension ACOM (Internet Explorer, les modifications apportées aux navigation à gauche, lancement des pages, des redirections ou des cartes de formation) avec les modifications que vous apportez dans le référentiel azure-contenu-valeur_nominale, vous devez coordonner que le travail avance avec votre relecteur Valeur_nominale. Dans le cas contraire, vous risquez d’avoir un grand nombre de liens rompus.

## <a name="criteria-for-expedited-pull-requests"></a>Critères pour les demandes d’extraction expédiées

- Contactez azdocprs afin d’accélérer la PRs uniquement lorsque cela est absolument nécessaire. Vous pouvez demander expédiées Valeur_nominale gestion Zone rouge, confidentialité, juridique et des problèmes de sécurité ; pour des expériences client véritablement incorrectes ; et pour les augmentations direction. 
- Contenu pour les versions de la fonctionnalité ne sont pas définies pour la gestion des prioritaire - contenu de la version fonctionnalité nécessite une planification préalable ou qu’il doit être géré via la file d’attente de priorité standard.


## <a name="in-a-hurry-submit-prs-that-can-be-accepted-automatically"></a>En retard ? Soumettre PRs qui peuvent être acceptés automatiquement

Utilisez les règles d’automatisation PRMerger pour obtenir plus de votre PRs au quotidien fusionnées automatiquement.

PRMerger peut accepter votre Valeur_nominale automatiquement, si :
* Il n’affecte 10 fichiers ou au maximum.
* Il contient plus de 15 validations.
* Inférieures à 20 % des modifications de texte.
* Sélecteurs/consoles ne sont pas mis à jour.
* Aucun fichiers sont supprimés ou ajoutés.
* Aucune image n’est nouveaux, modifiés ou supprimés.

Si votre demande d’extraction ne répond pas aux critères, l’étiquette « PRmerger ne peut pas fusionner » reçoit automatiquement afin de déterminer qu’il doit être révisé par un relecteur Valeur_nominale humain.

### <a name="need-to-make-a-lot-of-little-changes"></a>Vous avez besoin pour effectuer un grand nombre de petites modifications ?

Effectuez votre repère à partir des règles d’automatisation PRMerger ci-dessus et procédez comme suit :
* Soumettre des articles avec des changements d’éclairage collaborer dans un Valeur_nominale fichiers 10 ou moins.
* Créer un Valeur_nominale distinct pour les articles dans quelle modification sélecteurs ou d’images. Cette fonctionnalité nécessite révision humaine.
* Créer un Valeur_nominale distincte pour articles nouvelles ou supprimées. Cette fonctionnalité nécessite révision humaine.

## <a name="related"></a>Liés

- [Passez en revue les critères de qualité de demande d’extraction](contributor-guide-pr-criteria.md)

- [Extraire demande commentaire automation](contributor-guide-pull-request-comments.md)
