Clients Azure peuvent déverrouiller 25 000 messages électroniques gratuites chaque mois. Ces 25 000 e-mails mensuels gratuits donne accès à la génération avancées et analytique et [toutes les API][] (Web, SMTP, événements, analyse et autres). Pour plus d’informations sur les services supplémentaires fournis par SendGrid, consultez la page [Fonctionnalités SendGrid][] .

### <a name="to-sign-up-for-a-sendgrid-account"></a>S’abonner à un compte SendGrid

1. Connectez-vous au [portail de gestion Azure][].

2. Dans le volet inférieur du portail de gestion, cliquez sur **Nouveau**.

    ![commande Nouveau de barre][command-bar-new]

3. Cliquez sur **Marketplace**.

    ![sendgrid store][sendgrid-store]

4. Dans la boîte de dialogue **Choisir une Application et le Service** , sélectionnez **SendGrid** et cliquez sur la flèche droite.

5. Dans la boîte de dialogue **Personnaliser les applications et services** , sélectionnez l’offre **SendGrid** à que vous souhaitez vous inscrire.

6. Entrez un nom pour identifier votre service **SendGrid** dans vos paramètres Azure, ou utilisez la valeur par défaut **SendGridEmailDelivery.Simplified.SMTPWebAPI**. Les noms doivent être compris entre 1 et 100 caractères et contenir des traits de soulignement, des tirets, des points et uniquement des caractères alphanumériques. Le nom doit être unique dans votre liste d’éléments de la banque Azure auquel vous êtes abonné.

    ![Store-écran-2][store-screen-2]

7. Sélectionnez une valeur pour la région ; par exemple, ouest US.

8. Cliquez sur la flèche droite.

9. Sous l’onglet **Achats réviser** , passez en revue le plan et informations sur les tarifs, puis passez en revue les conditions juridiques. Si vous acceptez les termes, cliquez sur la coche. Après avoir cliqué sur la case à cocher, votre compte SendGrid va commencer le [processus de mise à disponibilité SendGrid].

    ![Store-écran-3][store-screen-3]

10. Après avoir confirmé votre achat, vous êtes redirigé vers le tableau de bord modules complémentaires et vous verrez le message **SendGrid d’achat**.

    ![message sendgrid d’achat][sendgrid-purchasing-message]

    Votre compte SendGrid est mis en service immédiatement et vous verrez le message **bien acheté SendGrid module complémentaire**. Votre compte et les informations d’identification sont désormais créées. Vous êtes prêt à envoyer des messages électroniques à ce stade. 

    Pour modifier votre offre d’abonnement ou consultez le SendGrid paramètres de contact, cliquez sur le nom de votre service SendGrid pour ouvrir le tableau de bord SendGrid Marketplace. 

    ![sendgrid-ajouter-sur-tableau de bord][sendgrid-add-on-dashboard]

    Pour envoyer un message électronique à l’aide de SendGrid, vous devez fournir vos informations d’identification de compte (nom d’utilisateur et mot de passe).

### <a name="to-find-your-sendgrid-credentials"></a>Pour rechercher vos informations d’identification SendGrid ###

1. Cliquez sur **informations de connexion**.

    ![bouton sendgrid-connexion-informations][sendgrid-connection-info-button]

2. Dans la boîte de dialogue *informations sur la connexion* , copiez le **mot de passe** et le nom d’utilisateur à utiliser plus loin dans ce didacticiel.

    ![informations de connexion sendgrid][sendgrid-connection-info]

    Pour définir des paramètres de remise des messages de votre messagerie, cliquez sur le bouton **Gérer** . Cette opération redirigera vers votre panneau SendGrid. 

    ![sendgrid panneau][sendgrid-control-panel]

    Pour plus d’informations sur la prise en main SendGrid, voir [SendGrid mise en route][].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/sendgrid_BAR_NEW.PNG
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid_offerings_store.png
[store-screen-2]: ./media/sendgrid-sign-up/sendgrid_store_scrn2.png
[store-screen-3]: ./media/sendgrid-sign-up/sendgrid_store_scrn3.png
[sendgrid-purchasing-message]: ./media/sendgrid-sign-up/sendgrid_purchasing_message.png
[sendgrid-add-on-dashboard]: ./media/sendgrid-sign-up/sendgrid_add-on_dashboard.png
[sendgrid-connection-info]: ./media/sendgrid-sign-up/sendgrid_connection_info.png
[sendgrid-connection-info-button]: ./media/sendgrid-sign-up/sendgrid_connection_info_button.png
[sendgrid-control-panel]: ./media/sendgrid-sign-up/sendgrid_control_panel.png

<!--Links-->

[Fonctionnalités SendGrid]: http://sendgrid.com/features
[Portail de gestion Azure]: https://manage.windowsazure.com
[SendGrid mise en route]: http://sendgrid.com/docs
[Processus de mise en service de SendGrid]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[toutes les API]: https://sendgrid.com/docs/API_Reference/index.html

