La plupart des messages d’erreur d’authentification heure proviennent des paramètres de configuration incorrects ou n’est pas cohérente. Voici quelques suggestions spécifiques pour les points à vérifier.

* Vérifiez que vous n’avez pas manquez n’importe où le bouton **Enregistrer** . Il s’agit souvent facile d’y parvenir et le résultat est que vous consulterez les valeurs correctes sur une page du portail, mais ils n’ont pas été enregistrés dans l’environnement Azure ou l’application Azure AD.
* Pour les paramètres définis dans la carte de **Paramètres de l’Application** du portail Azure, assurez-vous que l’application API correcte ou web app a été activé lorsque les paramètres ont été entrées.  Assurez-vous également que les paramètres entrés en tant que **paramètres de l’application** et non des **chaînes de connexion**, comme le format des deux sections est similaire.
* Pour l’authentification frontale JavaScript, téléchargez le manifeste à nouveau pour vérifier que `oauth2AllowImplicitFlow` a été remplacé par `true`.
* Vérifiez que vous avez utilisé HTTPS à l’endroit où vous avez configuré URL :

    * Dans le code de projet
    * Dans le CORS
    * Dans les paramètres d’application Azure environnement pour chaque application API et web app
    * Dans paramètres d’application Azure AD.
    
    Notez que si vous copiez l’URL d’une application API à partir du portail, il souvent a `http://` et que vous avez modifier manuellement à `https://`.

* Vérifiez que les modifications de code ont été déployées correctement. Par exemple, dans une solution à plusieurs projets, il est possible de modifier le code d’un projet et accidentellement choisissez une des autres lorsque vous voulez déployer la modification.
* Vérifiez que vous vous apprêtez à URL HTTPS dans votre navigateur, pas des URL HTTP. Par défaut, Visual Studio crée des profils avec des URL HTTP de publication, et voilà ce qui s’ouvre dans le navigateur après le déploiement d’un projet.
* Pour l’authentification à un serveur frontal de JavaScript, assurez-vous que CORS est correctement configuré sur l’application de l’API qui appelle le code JavaScript. En cas de doute concernant si le problème est lié à CORS, essayez de « * » en tant que l’URL d’origine autorisés. 
* Pour un frontal JavaScript, ouvrir l’onglet Développeur outils Console de votre navigateur pour obtenir des informations d’erreur et examiner les requêtes HTTP sur le réseau. Toutefois, les messages d’erreur Console peuvent être confusion. Si vous obtenez un message indiquant une erreur CORS, le problème réel peut être l’authentification. Vous pouvez vérifier si c’est le cas en exécutant l’application avec l’authentification temporairement temporairement désactivée.
* Pour une application API .NET, vérifiez que vous obtenez plus d’informations dans les messages d’erreur que possible en configurant [mode customErrors sur Désactiver](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* Pour une application API .NET, démarrer une [session de débogage distant](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)et examiner les valeurs des variables qui sont passées au code qui utilise le terme ADAL pour acquérir un jeton porteur, ou un code qui vérifie revendications contre l’ID service prévu principal. Notez que votre code peut récupère les valeurs de configuration à partir de nombreuses sources différentes, il est possible de trouver surprise de cette manière. Par exemple, si vous orthographiez `ida:ClientId` en tant que `ida:ClientID` lors de la configuration des paramètres d’environnement Azure Application Service, le code rencontrée la `ida:ClientId` valeur qu’elle recherche à partir du fichier Web.config, en ignorant le paramètre Azure Application Service. 
* Si les éléments ne fonctionnent pas dans une fenêtre Internet Explorer normale, un journal connexion existante peut interférer ; Essayez InPrivate et Chrome ou Firefox.
