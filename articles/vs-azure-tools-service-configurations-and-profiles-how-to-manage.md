<properties
   pageTitle="Comment gérer les configurations de service et des profils | Microsoft Azure"
   description="Découvrez comment utiliser des fichiers de configuration des configurations et des profils service | les stockent des paramètres pour les environnements de déploiement et paramètres de publication des services cloud."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="how-to-manage-service-configurations-and-profiles"></a>Comment gérer les profils et les configurations de service

## <a name="overview"></a>Vue d’ensemble

Lorsque vous publiez un service cloud, Visual Studio stocke les informations de configuration dans les deux types de fichiers de configuration : configurations et des profils de service. Configurations de service (fichiers .cscfg) stockent des paramètres pour les environnements de déploiement d’un service cloud Azure. Azure utilise ces fichiers de configuration lorsqu’il gère vos services cloud. En revanche, banque de profils (fichiers .azurePubxml) les paramètres des services en nuage de publication. Ces paramètres sont un enregistrement de ce que vous choisissez lorsque vous utilisez l’Assistant Publication et sont utilisés localement par Visual Studio. Cette rubrique explique comment travailler avec les deux types de fichiers de configuration.

## <a name="service-configurations"></a>Configurations de service

Vous pouvez créer plusieurs configurations de service à utiliser pour chacun de vos environnements de déploiement. Par exemple, vous pouvez créer une configuration du service pour l’environnement local que vous utilisez pour exécuter et tester une application Azure et une autre configuration des services pour votre environnement de production.

Vous pouvez ajouter, supprimer, renommer et modifier ces configurations de service selon vos besoins. Vous pouvez gérer ces configurations de service à partir de Visual Studio, comme le montre l’illustration suivante.

![Gérer les Configurations de Service](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

Vous pouvez également ouvrir la boîte de dialogue **Gérer les Configurations** à partir des pages de propriété du rôle. Pour ouvrir les propriétés d’un rôle dans votre projet Azure, ouvrir le menu contextuel pour ce rôle, puis sur **Propriétés**. Sous l’onglet **paramètres** , développez la liste de **Configuration du Service** , puis sélectionnez **Gérer** pour ouvrir la boîte de dialogue **Gérer les Configurations** .

### <a name="to-add-a-service-configuration"></a>Pour ajouter une configuration de service

1. Dans l’Explorateur ouvrir le menu contextuel pour le projet Azure, puis sélectionnez **Gérer les Configurations**.

    La boîte de dialogue **Gérer les Configurations de Service** s’affiche.

1. Pour ajouter une configuration de service, vous devez créer une copie d’une configuration existante. Pour ce faire, choisissez la configuration que vous voulez copier dans la liste nom, puis choisissez **créer une copie**.

1. (Facultatif) Pour attribuer un nom différent à la configuration du service, choisissez la configuration du service dans la liste nom, puis sélectionnez **Renommer**. Dans la zone de texte **nom** , tapez le nom que vous souhaitez utiliser pour cette configuration du service, puis sélectionnez **OK**.

    Un nouveau fichier de configuration de service nommé ServiceConfiguration. [Nom du nouveau] .cscfg est ajoutée au projet Azure dans l’Explorateur de solutions.


### <a name="to-delete-a-service-configuration"></a>Pour supprimer une configuration de service

1. Dans l’Explorateur de solutions, ouvrez le menu contextuel pour le projet Azure, puis sélectionnez **Gérer les Configurations**.

    La boîte de dialogue **Gérer les Configurations de Service** s’affiche.

1. Pour supprimer une configuration du service, choisissez la configuration que vous souhaitez supprimer de la liste **nom** , puis sélectionnez **Supprimer**. Une boîte de dialogue vous invite à confirmer que vous voulez supprimer cette configuration.

1. Sélectionnez **Supprimer**.

     Le fichier de configuration de service est supprimé à partir du projet Azure dans l’Explorateur de solutions.


### <a name="to-rename-a-service-configuration"></a>Pour renommer une configuration de service

1. Dans l’Explorateur de solutions, ouvrez le menu contextuel pour le projet Azure, puis sur **Gérer les Configurations**.

    La boîte de dialogue **Gérer les Configurations de Service** s’affiche.

1. Pour renommer une configuration du service, choisissez la configuration du service dans la liste **nom** , puis sélectionnez **Renommer**. Dans la zone de texte **nom** , tapez le nom que vous souhaitez utiliser pour cette configuration du service, puis sur **OK**.

    Le nom du fichier de configuration du service est modifié dans le projet Azure dans l’Explorateur de solutions.

### <a name="to-change-a-service-configuration"></a>Pour modifier une configuration de service

- Si vous souhaitez modifier une configuration du service, ouvrir le menu contextuel pour le rôle spécifique que vous souhaitez modifier dans le projet Azure, puis sélectionnez **Propriétés**. Voir [Comment : configurer les rôles d’un Service Cloud Azure avec Visual Studio](https://msdn.microsoft.com/library/azure/hh369931.aspx) pour plus d’informations.

## <a name="make-different-setting-combinations-by-using-profiles"></a>Apporter différentes combinaisons de paramètres en utilisant des profils

En utilisant un profil, vous pouvez incrémenter automatiquement dans l' **Assistant Publication** avec plusieurs combinaisons des paramètres, à des fins différentes. Par exemple, vous pouvez avoir un profil pour le débogage et une autre page de virtualisation crée. Dans ce cas, votre profil **Déboguer** aurait **IntelliTrace** est activé et la configuration **Déboguer** sélectionnée, et votre profil **Release** aurait **IntelliTrace** désactivé et la configuration de la **publication** sélectionnée. Vous pouvez également utiliser des profils distincts pour déployer un service à l’aide d’un compte de stockage différents.

Lorsque vous exécutez l’Assistant pour la première fois, un profil par défaut est créé. Visual Studio stocke le profil dans un fichier avec une extension .azurePubXml, qui est ajoutée à votre projet Azure sous le dossier **profils** . Si vous spécifiez manuellement des choix différents lorsque vous exécutez l’Assistant ultérieurement, le fichier met automatiquement à jour. Avant d’exécuter la procédure suivante, vous devez ont déjà publiées votre service cloud au moins une fois.

### <a name="to-add-a-profile"></a>Pour ajouter un profil

1. Ouvrir le menu contextuel pour votre projet Azure, puis sur **Publier**.

1. En regard de la liste **cible profil** , sélectionnez le bouton **Enregistrer le profil** , comme le montre l’illustration suivante. Cela crée un profil pour vous.

    ![Créer un nouveau profil](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)

1. Une fois que le profil est créé, sélectionnez **< gérer... >** dans la liste des **profils cible** .

    La boîte de dialogue **Gérer les profils** s’affiche, comme le montre l’illustration suivante.

    ![Gérer la boîte de dialogue profils](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)

1. Dans la liste **nom** , sélectionnez un profil, puis **Créer une copie**.

1. Cliquez sur le bouton **Fermer** .

    Le nouveau profil s’affiche dans la liste des profils cible.

1. Dans la liste **cible profil** , sélectionnez le profil que vous venez de créer. Les paramètres de l’Assistant publication sont remplis avec les choix du profil que vous avez sélectionné.

1. Sélectionnez les boutons **précédent** et **suivant** pour afficher chaque page de l’Assistant Publication, puis personnalisez les paramètres pour ce profil. Pour plus d’informations, voir [Publier Assistant Création d’applications Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) .

1. Une fois que vous avez terminé de personnaliser les paramètres, sélectionnez **suivant** pour revenir à la page Paramètres. Le profil est enregistré lorsque vous publiez le service à l’aide de ces paramètres ou si vous cliquez sur **Enregistrer** en regard de la liste des profils.

### <a name="to-rename-or-delete-a-profile"></a>Pour renommer ou supprimer un profil

1. Ouvrir le menu contextuel pour votre projet Azure, puis sur **Publier**.

1. Dans la liste **cible profil** , sélectionnez **Gérer**.

1. Dans la boîte de dialogue **Gérer les profils** , sélectionnez le profil que vous voulez supprimer, puis sélectionnez **Supprimer**.

1. Dans la boîte de dialogue de confirmation qui s’affiche, sélectionnez **OK**.

1. Sélectionnez **Fermer**.

### <a name="to-change-a-profile"></a>Pour modifier un profil

1. Ouvrir le menu contextuel pour votre projet Azure, puis sur **Publier**.

1. Dans la liste **cible profil** , sélectionnez le profil que vous souhaitez modifier.

1. Sélectionnez les boutons **précédent** et **suivant** pour afficher chaque page de l’Assistant Publication et modifiez les paramètres souhaités. Pour plus d’informations, voir [Publier Assistant Création d’applications Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) .

1. Une fois que vous avez terminé de modifier les paramètres, cliquez sur **suivant** pour revenir à la page **paramètres** .

1. (Facultatif) cliquez sur **Publier** pour publier le service de nuage en utilisant les nouveaux paramètres. Si vous ne voulez pas publier votre service cloud pour l’instant, et que vous fermez l’Assistant Publication, Visual Studio vous demande si vous voulez enregistrer les modifications dans le profil.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la configuration d’autres parties de votre projet Visual Studio Azure, consultez [configuration d’un projet d’Azure](http://go.microsoft.com/fwlink/p/?LinkID=623075)
