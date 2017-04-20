# <a name="pull-request-comment-automation"></a>Extraire demande commentaire automation

Nous utilisons automation de commentaire dans les commentaires de demande d’extraction pour autoriser les collaborateurs et processus de révision des auteurs pour attribuer des étiquettes conduire à la demande d’extraction.

| Commentaire | Fonction | Disponibilité|
| -------- |-------------|-------------|
|#déconnexion | Lorsque l’auteur d’un article tape le commentaire **#signe-désactiver** dans le flux de commentaire, l’étiquette **prêt à fusionner** est attribuée. | Publiques et privées|
|#déconnexion | Si un collaborateur qui n’est pas l’auteur répertoriés tente d’approuver une demande d’extraction public en utilisant le commentaire **#signe-désactiver** , un message est écrit à la demande d’extraction indiquant que l’étiquette peut être affectée uniquement par l’auteur. | Public |
|#désactiver la suspension | Si vous tapez **hold désactiver #** dans un commentaire de requête d’extraction, il supprime l’étiquette **prêt pour fusionner** - au cas où vous changez d’avis ou faites une erreur. Dans la mis en pension privé, cela affecte l’étiquette **not-fusion** . | Publiques et privées |
| #Veuillez-clôture | Auteurs peuvent taper le commentaire **#please-clôture** dans le flux de commentaire d’une demande d’extraction pour le fermer si vous décidez de ne pas faire les modifications fusionnées. | Public |

##<a name="troubleshooting-sign-offs-in-the-public-repo"></a>Résolution des problèmes de signatures dans la mis en pension public

Le public mis en pension d’approbation automation est permet uniquement l’auteur pour vous déconnecter. Un traitement manuel exceptionnel peut-être être nécessaires :

- **Article auteurs**: pour utiliser l’automatisation commentaire référentiel public, votre compte GitHub réel doit correspondre exactement à compte GitHub répertorié dans les métadonnées de l’article. La mise en majuscule de votre compte est également importante. Si vous êtes bloqués à partir de l’approbation en raison de ce problème, envoyer des messages à l’alias azdocprs.

- **D’autres employés**: Si vous êtes un employé signature au nom de l’auteur et vous sont bloqués par l’automatisation, contactez azdocprs avec le lien demande extraire. Indiquer qui vous soyez--PMs sur la même équipe de produit, des collègues sur l’équipe de rédaction et l’écriture responsables d’équipe sont considérées comme des sources fiables.



## <a name="related"></a>Liés

- [Étiquette de requête d’extraction et des pratiques recommandées pour les collaborateurs de Microsoft](contributor-guide-pull-request-etiquette.md)

- [Passez en revue les critères de qualité de demande d’extraction](contributor-guide-pr-criteria.md)
