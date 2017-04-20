<properties
    pageTitle="Points de terminaison de Service Azure"
    description="Décrit les paramètres de point de terminaison de Service Azure dans la boîte à outils Azure pour Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268600.aspx -->

# <a name="azure-service-endpoints"></a>Points de terminaison de Service Azure #

Points de terminaison de service Azure déterminent que si votre application est déployée sur et gérée par la plateforme Azure globale, Azure géré par 21Vianet en Chine, ou un privé plateforme Azure. La boîte de dialogue **Points de terminaison de Service** vous permet de spécifier les points de terminaison de service que vous voulez utiliser. Pour ouvrir la boîte de dialogue **Points de terminaison de Service** , au sein de Eclipse, cliquez sur **fenêtre**, cliquez sur **Préférences**, développez **Azure**, puis cliquez sur **Les points de terminaison de Service**. Définir le champ **Ensemble actif** détermine les points de terminaison de service Azure seront utilisés pour les projets Azure dans votre espace de travail en cours.

La figure suivante montre la boîte de dialogue **Points de terminaison de Service** .

![][ic719493]

## <a name="to-set-the-service-endpoints"></a>Pour définir les points de terminaison de service ##

Dans la boîte de dialogue **Points de terminaison de Service** , effectuez l’une des actions suivantes :

* Si vous souhaitez utiliser la plateforme Azure générale, dans la liste déroulante **Ensemble actif** , sélectionnez **windowsazure.com** et cliquez sur **OK**.
* Si vous souhaitez utiliser Azure géré par 21Vianet en Chine, dans la liste déroulante **Ensemble actif** , sélectionnez **windowsazure.cn (Chine)** , puis cliquez sur **OK**.
* Si vous souhaitez utiliser une plateforme Azure privée :
    1. Cliquez sur **Modifier**.
    2. Une boîte de dialogue s’ouvre, vous informant que la boîte de dialogue **Points de terminaison de Service** est fermée, et le fichier de jeux de préférence doit être ouvert. Cliquez sur **OK**.
    3. Dans le fichier preferencesets.xml, créez une nouvelle `preferenceset` élément. Pour ce nouvel élément, créez `name`, `blob`, `management`, `portalURL` et `publishsettings` les attributs et ajoutez les valeurs qui correspondent aux votre plateforme Azure privé. Vous pouvez utiliser les valeurs fournies pour le `preferenceset` éléments en tant que modèles. **Remarque**: la valeur utilisée pour la `blob` attribut doit contenir le texte « blob » dans l’URL.
    4. Enregistrez et fermez preferencesets.xml.
    5. Rouvrez la boîte de dialogue **Points de terminaison de Service** .
    6. Dans la liste déroulante **Ensemble actif** , sélectionnez le jeu d’actif que vous avez créé, puis cliquez sur **OK**.
    7. Une fois que vous avez créé votre plateforme Azure privé `preferenceset` élément, vous pouvez modifier les valeurs affectées en cliquant sur le bouton **Modifier** dans la boîte de dialogue **Services de point de terminaison** . Vous pouvez également créer plusieurs plateforme Azure privé `preferenceset` éléments, si vous le souhaitez.

## <a name="see-also"></a>Voir aussi ##

[Kit de ressources Azure pour Éclipse][]

[Installez le Kit de ressources de Azure pour Éclipse][] 

[Création d’une Application World Hello pour Azure dans Éclipse][]

Pour plus d’informations sur l’utilisation de Azure avec Java, voir le [Centre de développement Java Azure][].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de ressources Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d’une Application World Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installez le Kit de ressources de Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png
