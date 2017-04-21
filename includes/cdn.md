# <a name="using-cdn-for-azure"></a>À l’aide de CDN pour Azure

Le réseau de remise de contenu (CDN) Azure offre aux développeurs une solution globale pour offrir un contenu haut débit en mettant en cache d’objets BLOB et le contenu statique des instances de calcul en nœuds physiques aux États-Unis, Europe, Asie, Australie et Amérique du Sud. Pour connaître la liste des emplacements de nœud CDN, voir [Azure CDN nœud emplacements].

Cette tâche inclut les étapes suivantes :

* [Étape 1 : Créer un compte de stockage](#Step1)
* [Étape 2 : Créer un nouveau point de terminaison CDN pour votre compte de stockage](#Step2)
* [Étape 3 : Accéder à votre contenu CDN](#Step3)
* [Étape 4 : Supprimer le contenu de votre fournisseur](#Step4)

Avantages de l’utilisation de CDN en cache des données Azure :

-   Optimiser les performances et utilisateur expérience pour les utilisateurs finaux qui ne sont pas une source de contenu ou utilisez des applications où nombre 'voyages internet' nécessaire au chargement de contenu
-   Grande échelle distribué afin de mieux gérer instantanée élevée, par exemple, au début d’un événement comme lancement d’un produit

Les clients existants CDN peuvent désormais utiliser le CDN Azure dans le [portail classique Azure]. Le CDN est une fonctionnalité complémentaire de votre abonnement et a un [plan de facturation]séparée.

<a id="Step1"> </a>
<h2>Étape 1 : Créer un compte de stockage</h2>

La procédure suivante permet de créer un nouveau compte de stockage pour un abonnement Azure. Un compte de stockage permet d’accéder aux services de stockage Azure. Le compte de stockage représente le niveau de l’espace de noms pour accéder à chacun des composants du service de stockage Azure : Blob services, services file d’attente et services de tableau. Pour plus d’informations sur les services de stockage Azure, voir [utiliser les Services de stockage Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx).

Pour créer un compte de stockage, vous devez être l’administrateur de service ou un administrateur de co-création pour l’abonnement associé.

> [AZURE.NOTE] Pour plus d’informations sur l’exécution de cette opération à l’aide de l’API de gestion du Service Azure, consultez la rubrique [Créer un compte de stockage](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx) .

**Pour créer un compte de stockage pour un abonnement Azure**

1.  Connectez-vous au [portail classique Azure].
2.  Dans le coin inférieur gauche, cliquez sur **Nouveau**. Dans la boîte de dialogue **Nouveau** , sélectionnez les **Services de données**, puis sur **espace de stockage**, puis **Création rapide**.

    La boîte de dialogue **Créer un compte de stockage** s’affiche.

    ![Créer le compte de stockage][create-new-storage-account]

4. Dans le champ **URL** , tapez un nom de sous-domaine. Cette entrée peut contenir des lettres minuscules 3-24 et des nombres.

    Cette valeur devient le nom d’hôte dans l’URI qui est utilisée pour adresser Blob, file d’attente ou tableau des ressources pour l’abonnement. Pour répondre à une ressource conteneur dans le service d’objets Blob, vous utiliseriez un URI au format suivant, où * &lt;StorageAccountLabel&gt; * fait référence à la valeur que vous avez tapés dans la zone **Entrez une URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt; *

    **Importantes :** L’étiquette URL le sous-domaine du compte de stockage URI des formulaires et doit être unique parmi tous les services hébergés dans Azure.

    Cette valeur est également utilisée comme le nom de ce compte de stockage dans le portail, ou lorsque vous accédez à ce compte par programme.

5.  Dans la liste déroulante **Région/affinité du groupe** , sélectionnez une région ou affinité du groupe pour le compte de stockage. Sélectionnez un groupe affinité au lieu d’une zone Si vous voulez que vos services stockage se trouver dans le même centre de données avec d’autres services de Windows Azure que vous utilisez. Cela peut améliorer les performances et sans frais sont engagés pour sortie.  

    **Remarque :** Pour créer un groupe affinité, ouvrez la zone **paramètres** du portail de gestion et cliquez sur **affinités**puis cliquez sur **Ajouter un groupe affinité** ou sur **Ajouter**. Vous pouvez également créer et gérer des groupes affinité à l’aide de l’API de gestion du Service Windows Azure. Pour plus d’informations, voir [opérations sur affinités].

6. Dans la liste déroulante **abonnement** , sélectionnez l’abonnement qui est utilisé avec le compte de stockage.
7.  Cliquez sur **créer un compte de stockage**. Le processus de création du compte de stockage peut prendre plusieurs minutes.
8.  Pour vérifier que le compte de stockage a été créé avec succès, vérifiez que le compte s’affiche dans les éléments répertoriés pour le **stockage** avec le statut **en ligne**.

<a id="Step2"> </a>
<h2>Étape 2 : Créer un nouveau point de terminaison CDN pour votre compte de stockage</h2>

Une fois que vous activez l’accès à un compte de stockage CDN ou service hébergé, tous les objets accessibles sont éligibles pour la mise en cache de bord CDN. Si vous modifiez un objet qui se trouve en cache dans le CDN, le nouveau contenu ne sera pas disponible via le CDN jusqu'à ce que le CDN actualise son contenu à la fin de la période de temps de vie contenue mis en cache.

**Pour créer un nouveau point de terminaison CDN pour votre compte de stockage**

1. Dans le [portail classique Azure], dans le volet de navigation, cliquez sur **CDN**.

2. Dans le ruban, cliquez sur **Nouveau**. Dans la boîte de dialogue **Nouveau** , sélectionnez **Services d’application**, puis **CDN**, puis **Création rapide**.

3. Dans la liste déroulante de **Domaine d’origine** , sélectionnez le compte de stockage que vous avez créé dans la section précédente dans la liste de vos comptes de stockage disponible. 

4. Cliquez sur le bouton **créer** pour créer le nouveau point de terminaison.

5. Une fois que le point de terminaison est créé, il apparaît dans la liste des points de terminaison pour l’abonnement. L’affichage de liste affiche l’URL à utiliser pour accéder aux contenu mis en cache, ainsi que le domaine d’origine. 

    Le domaine d’origine est l’emplacement à partir duquel le CDN met en cache du contenu. Le domaine d’origine peut être un compte de stockage ou un service cloud ; un compte de stockage est utilisé pour les besoins de cet exemple. Contenu de stockage est mis en cache sur les serveurs edge en fonction des paramètres de contrôle du cache que vous spécifiez, ou à l’heuristique par défaut du réseau mise en cache. 


    > [AZURE.NOTE] La configuration créée pour le point de terminaison pas immédiatement sera disponible ; Il peut prendre jusqu'à 60 minutes pour l’enregistrement de se propager via le réseau CDN. Les utilisateurs essaient d’utiliser le nom de domaine CDN immédiatement peuvent recevoir code d’état 400 (demande incorrecte) jusqu'à ce que le contenu est disponible via le CDN.

<a id="Step3"> </a>
<h2>Étape 3 : Accès CDN contenu</h2> 

Pour accéder au contenu mis en cache sur le CDN, utilisez l’URL CDN fournie dans le portail. L’adresse pour un blob mis en cache sera semblable à ce qui suit :

http://< ;*CDNNamespace*\>.vo.msecnd.net/ <*myPublicContainer*\>/<*BlobName*\>

<a id="Step4"> </a>
<h2>Étape 4 : Supprimer le contenu à partir du CDN</h2>

Si vous ne souhaitez plus cache d’un objet dans le réseau de remise contenu Azure (CDN), vous pouvez prendre une des opérations suivantes :

-   Pour un blob Azure, vous pouvez supprimer le blob à partir du conteneur public.
-   Vous pouvez rendre le conteneur privé au lieu de baisse. Pour plus d’informations, voir [Restreindre l’accès à des conteneurs et des objets BLOB](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/#restrict-access-to-containers-and-blobs) .
-   Vous pouvez désactiver ou supprimer le point de terminaison CDN à l’aide du portail de gestion.
-   Vous pouvez modifier votre service hébergé pour ne plus répondre aux demandes de l’objet.

Un objet déjà mis en cache dans le CDN restera mis en cache avant l’expiration de la période de temps de vie de l’objet. À la fin de la période de temps de vie, le CDN vérifie si le point de terminaison CDN est toujours valide et l’objet toujours accessible de manière anonyme. Si elle n’est pas le cas, puis l’objet est ne peut plus être mis en cache.

La possibilité de purge immédiatement de contenu n’est actuellement pas pris en charge sur le portail de gestion Azure. Veuillez contacter le [support Azure](https://azure.microsoft.com/support/options/) si vous avez besoin pour supprimer définitivement immédiatement du contenu à. 

## <a name="additional-resources"></a>Ressources supplémentaires

-   [Comment créer un groupe affinité dans Azure]
-   [Comment : gérer les comptes de stockage pour un abonnement Azure]
-   [À propos de l’API de gestion de Service]
-   [Comment mapper un CDN contenu à un domaine personnalisé]

  [Create Storage Account]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/
  [Emplacements de nœud CDN Azure]: http://msdn.microsoft.com/library/windowsazure/gg680302.aspx
  [Portail classique Azure]: https://manage.windowsazure.com/
  [plan de facturation]: /pricing/calculator/?scenario=full
  [Comment créer un groupe affinité dans Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Overview of the Azure CDN]: http://msdn.microsoft.com/library/windowsazure/ff919703.aspx
  [À propos de l’API de gestion de Service]: http://msdn.microsoft.com/library/windowsazure/ee460807.aspx
  [Comment mapper un CDN contenu à un domaine personnalisé]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Previous Management Portal]: ../../Shared/Media/previous-portal.png
