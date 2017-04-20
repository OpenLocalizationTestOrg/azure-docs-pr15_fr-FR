<properties 
   pageTitle="Installer des mises à jour sur un tableau virtuel StorSimple | Microsoft Azure"
   description="Décrit comment utiliser l’interface utilisateur du web tableau virtuel StorSimple pour appliquer des mises à jour à l’aide de la méthode de portail et le correctif"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/07/2016"
   ms.author="alkohli" />

# <a name="install-updates-on-your-storsimple-virtual-array"></a>Installer des mises à jour dans votre tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble

Cet article décrit les étapes nécessaires pour installer les mises à jour dans votre tableau virtuel StorSimple via l’interface utilisateur web local et le portail classique Azure. Vous devez appliquer des mises à jour logicielles ou correctifs pour conserver votre tableau virtuel StorSimple à jour. 

N’oubliez pas que l’installation d’une mise à jour ou le correctif redémarrage de votre appareil. Étant donné que le tableau virtuel StorSimple est un appareil nœud unique, les e/s en cours est interrompue et temps d’arrêt des expériences votre appareil. 

Avant d’appliquer une mise à jour, nous vous conseillons les volumes ou les partages en mode hors connexion sur l’hôte première et puis le périphérique. Cela permet de réduire les risques d’altération des données.

> [AZURE.IMPORTANT] Si vous exécutez la mise à jour 0,1 ou versions du logiciel disponible, vous devez utiliser la méthode correctif via l’interface utilisateur web local pour installer la mise à jour 0,3. Si vous exécutez la mise à jour 0,2, nous vous recommandons d’installer les mises à jour via le portail classique Azure.

## <a name="use-the-local-web-ui"></a>Utiliser l’interface utilisateur web local 
 
Il existe deux étapes lors de l’utilisation de l’interface utilisateur web local :

- Télécharger la mise à jour ou le correctif
- Installer la mise à jour ou le correctif

### <a name="download-the-update-or-the-hotfix"></a>Télécharger la mise à jour ou le correctif

Procédez comme suit pour télécharger la mise à jour à partir du catalogue Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Pour télécharger la mise à jour ou le correctif

1. Démarrez Internet Explorer et accédez à [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Si c’est la première fois que l’utilisation du catalogue de mise à jour Microsoft sur cet ordinateur, cliquez sur **installer** lorsque vous êtes invité à installer le module complémentaire catalogue Microsoft Update.
  
3. Dans la zone de recherche de catalogue Microsoft Update, entrez le nombre de la Base de connaissances (Ko) du correctif que vous souhaitez télécharger. Entrez **3182061** pour mettre à jour 0,3, puis cliquez sur **Rechercher**.

    Dans la liste correctif s’affiche, par exemple, **mise à jour de la matrice StorSimple virtuelle 0,3**.

    ![Catalogue de recherche](./media/storsimple-ova-install-update-01/download1.png)

4. Cliquez sur **Ajouter**. La mise à jour est ajouté au panier.

5. Cliquez sur **Afficher le panier**.

6. Cliquez sur **Télécharger**. Spécifier ou **Rechercher** un emplacement local où vous souhaitez les téléchargements apparaisse. Les mises à jour sont téléchargés vers l’emplacement spécifié et placés dans un sous-dossier avec le même nom que la mise à jour. Le dossier peut également être copié sur un partage réseau est accessible à partir de l’appareil.

7. Ouvrez le dossier copié, vous devriez voir un fichier de Package autonome Microsoft `WindowsTH-KB3011067-x64`. Ce fichier est utilisé pour installer la mise à jour ou le correctif.


### <a name="install-the-update-or-the-hotfix"></a>Installer la mise à jour ou le correctif

Avant l’installation de mise à jour ou le correctif, vérifiez que vous avez la mise à jour ou le correctif téléchargé soit localement sur votre hôte ou accessible via un partage réseau. 

Utilisez cette méthode pour installer les mises à jour sur un appareil disponible en cours d’exécution ou mettre à jour de 0,1 versions du logiciel. Cette procédure prend moins de 2 minutes. Procédez comme suit pour installer la mise à jour ou le correctif.


#### <a name="to-install-the-update-or-the-hotfix"></a>Pour installer la mise à jour ou le correctif

1. Dans l’interface utilisateur web local, accédez à la **Maintenance** > **Mise à jour logicielle**.

    ![périphérique mise à jour](./media/storsimple-ova-install-update-01/update1m.png)

2. **Mettre à jour le chemin d’accès de fichier**, entrez le nom de fichier pour la mise à jour ou le correctif. Vous pouvez également recherchez le fichier d’installation mise à jour ou le correctif si placé sur un partage réseau. Cliquez sur **Appliquer**.

    ![périphérique mise à jour](./media/storsimple-ova-install-update-01/update2m.png)

3.  Un avertissement s’affiche. Donné ce n’est un appareil nœud unique, après la mise à jour est appliquée, redémarrage de l’appareil et il est temps d’arrêt. Cliquez sur l’icône de vérification.

    ![périphérique mise à jour](./media/storsimple-ova-install-update-01/update3m.png)

4. La mise à jour démarre. Une fois que l’appareil est correctement mis à jour, il redémarre. L’interface utilisateur local n’est pas accessible dans cette durée.

    ![périphérique mise à jour](./media/storsimple-ova-install-update-01/update5m.png)

5. Après que le redémarrage est terminé, vous accédez à la page **se connecter** . Pour vérifier que le logiciel du périphérique a mis à jour, dans le site web local l’interface utilisateur, accédez à la **Maintenance** > **Mise à jour logicielle**. La version du logiciel affichée doit être **10.0.0.0.0.10288.0** pour la mise à jour 0,3.

    > [AZURE.NOTE] Nous signaler les versions du logiciel dans une façon légèrement différente dans l’interface utilisateur web local et le portail classique Azure. Par exemple, l’interface utilisateur web local rapports **10.0.0.0.0.10288** et le portail classique Azure rapports **10.0.10288.0** pour la même version. 

    ![périphérique mise à jour](./media/storsimple-ova-install-update-01/update6m.png)





## <a name="use-the-azure-classic-portal"></a>Utiliser le portail classique Azure

Si la mise à jour 0,2 en cours d’exécution, nous vous recommandons d’installer les mises à jour via le portail classique Azure. La procédure portail nécessite l’utilisateur à analyser, téléchargez et installez les mises à jour. Cette procédure prend environ 7 minutes. Procédez comme suit pour installer la mise à jour ou le correctif.

[AZURE.INCLUDE [storsimple-ova-install-update-via-portal](../../includes/storsimple-ova-install-update-via-portal.md)]

Une fois l’installation terminée (comme indiqué par état du travail à 100 %), accédez à **appareils > Maintenance > mises à jour logicielles**. La version du logiciel affichée doit être 10.0.10288.0.

![périphérique mise à jour](./media/storsimple-ova-install-update-01/azupdate12m.png)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [l’administration de votre tableau virtuel StorSimple](storsimple-ova-web-ui-admin.md).
