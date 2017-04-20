####<a name="configuring-the-ios-project-in-xamarin-studio"></a>Configuration du projet iOS dans Xamarin Studio

1. Dans Xamarin.Studio, ouvrez **Info.plist**et mettre à jour l' **Ensemble de guides identificateur** de l’ID de groupe que vous avez créée avec votre nouveau code de l’application.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)

2. Faites défiler jusqu'à **Modes d’arrière-plan** , vérifiez la zone **Activer les Modes de l’arrière-plan** et la zone de **notifications à distance** . 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)

3. Double-cliquez sur votre projet dans le panneau de configuration de Solution pour ouvrir les **Options de Project**.

4.  Choisissez **iOS offre groupée signature** sous **créer**, puis sélectionnez correspondante **identité** et **Provisioning profil** que vous venez de définir pour ce projet. 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

    Ainsi que le projet utilise le nouveau profil de signature de code. Pour l’appareil Xamarin officiel mise en service de documentation, voir [La configuration de périphériques Xamarin].

####<a name="configuring-the-ios-project-in-visual-studio"></a>Configuration du projet iOS dans Visual Studio

1. Dans Visual Studio, cliquez sur le projet, puis cliquez sur **Propriétés**.

2. Dans les pages de propriétés, cliquez sur l’onglet **Application iOS** et mettre à jour l' **identificateur** de l’ID que vous avez créé précédemment.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)

3. Dans l’onglet **iOS offre la signature** , sélectionnez correspondante **identité** et **Provisioning profil** que vous venez de définir pour ce projet. 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Cela garantit que le projet utilise le nouveau profil de signature de code. Pour l’appareil Xamarin officiel mise en service de documentation, voir [La configuration de périphériques Xamarin].

4. Double-cliquez sur Info.plist pour l’ouvrir, puis activez **RemoteNotifications** sous Modes d’arrière-plan. 



[APPAREIL Xamarin mise en service]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/