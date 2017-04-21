
   * Connectez-vous au compte Azure en entrant vos informations d’identification.

     Cette méthode est plus rapide et plus facile, mais si vous utilisez cette méthode vous ne pourrez pas voir base de données SQL Azure ou les Services mobiles dans la fenêtre **Explorateur de serveurs** .

     Dans l' **Explorateur de serveurs**, cliquez sur le bouton **se connecter à Azure** . Une alternative consiste à droit sur le nœud **Azure** , puis cliquez sur **se connecter à Azure** dans le menu contextuel.

   * Installer un certificat de gestion qui permet d’accéder à votre compte.

     Dans l' **Explorateur de serveurs**, cliquez sur le nœud **Azure** , puis cliquez sur **Gérer les abonnements** dans le menu contextuel. Dans la boîte de dialogue **Gérer les abonnements Azure** , cliquez sur l’onglet **certificats** , puis cliquez sur **Importer**. Suivez les instructions pour télécharger, puis les importer un fichier d’abonnement (également appelé un fichier *.publishsettings* ) pour votre compte Azure.

     > [AZURE.NOTE] Télécharger le fichier d’abonnement à un dossier en dehors de vos répertoires de code source (par exemple, dans le dossier Téléchargements) et supprimez-le une fois l’importation terminée. Un utilisateur malveillant qui accède au fichier abonnement peut modifier, créer et supprimer vos services Azure.

    Pour plus d’informations, voir [comment se connecter à Azure à partir de Visual Studio](http://go.microsoft.com/fwlink/?LinkId=324796).
