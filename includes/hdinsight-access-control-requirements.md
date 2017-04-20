Si vous utilisez un abonnement Azure où vous n’êtes pas administrateur/propriétaire, par exemple une société appartenant abonnement, vous devez vérifier les éléments suivants avant la procédure décrite dans ce document :

* Votre connexion Azure doit avoir au moins access __collaborateur__ au groupe de ressources Azure que vous utilisez lorsque vous créez HDInsight (et autres ressources Azure.)

* Une personne disposant au minimum __Collaboration__ accès à l’abonnement Azure doit avoir enregistré précédemment le fournisseur pour la ressource que vous utilisez. Inscription du fournisseur qui se produit lorsqu’un utilisateur avec accès collaborateurs à l’abonnement crée une ressource pour la première fois sur l’abonnement. Il également est possible sans création d’une ressource en [enregistrant un fournisseur à l’aide de reste](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Pour plus d’informations sur l’utilisation de gestion de l’accès, voir les documents suivants :

* [Prise en main la gestion des accès dans le portail Azure](../articles/active-directory/role-based-access-control-what-is.md)
* [Utiliser les affectations de rôle pour gérer l’accès à vos ressources abonnement Azure](../articles/active-directory/role-based-access-control-configure.md)
