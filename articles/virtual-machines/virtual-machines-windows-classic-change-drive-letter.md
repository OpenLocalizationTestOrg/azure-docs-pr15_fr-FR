<properties
    pageTitle="Rendre le lecteur d : d’un ordinateur virtuel un disque de données | Microsoft Azure"
    description="Décrit comment modifier des lettres de lecteur pour une machine virtuelle Windows afin que vous pouvez utiliser le lecteur d comme un lecteur de données."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Utiliser le lecteur d comme un lecteur de données sur un ordinateur Windows virtuel 

Si votre application a besoin d’utiliser le lecteur D pour stocker les données, suivez ces instructions pour utiliser une autre lettre pour le disque temporaire. Utilisez jamais le disque temporaire pour stocker les données que vous souhaitez conserver.

Si vous redimensionnez ou **Arrêter (Deallocate)** une machine virtuelle, cela peut déclencher placement de la machine virtuelle à un nouveau hyperviseur. Un événement de maintenance planifiée ou peut également déclencher cet emplacement. Dans ce scénario, le disque temporaire sera réaffecté à la première lettre disponible. Si vous avez une application qui requiert spécifiquement le lecteur d, vous devez suivre ces étapes pour temporairement déplacer pagefile.sys, connectez un nouveau disque de données et lui attribuer la lettre D et reculer pagefile.sys au lecteur temporaire. Une fois terminé, Azure pas ramène le d si la machine virtuelle se déplace vers un autre hyperviseur.

Pour plus d’informations sur la façon dont Azure utilise le disque temporaire, voir [Présentation du lecteur temporaire Microsoft Azure machines virtuelles en fonctionnement](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="attach-the-data-disk"></a>Connectez le disque de données

Tout d’abord, vous devez joindre le disque de données à la machine virtuelle. 

- Pour utiliser le portail, voir [l’insertion d’un disque de données dans le portail Azure](virtual-machines-windows-attach-disk-portal.md)
- Pour utiliser le portail classique, voir [l’insertion d’un disque de données pour une machine virtuelle Windows](virtual-machines-windows-classic-attach-disk.md). 


## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Déplacer temporairement pagefile.sys au lecteur C

1. Se connecter à la machine virtuelle. 

2. Cliquez sur le menu **Démarrer** , puis sélectionnez **système**.

3. Dans le menu de gauche, sélectionnez **paramètres système avancés**.

4. Dans la section **performances** , sélectionnez **paramètres**.

5. Sélectionnez l’onglet **Avancé** .

5. Dans la section **mémoire virtuelle** , sélectionnez **Modifier**.

6. Sélectionner le lecteur **C** et puis cliquez sur **Taille gérée par le système** , puis sur **définir**.

7. Sélectionnez le lecteur **D** et puis cliquez sur **aucun fichier d’échange** , puis sur **définir**.

8. Cliquez sur Appliquer. Vous recevez un message d’avertissement que l’ordinateur doit être redémarré pour que les modifications prennent effet.

9. Redémarrez l’ordinateur virtuel.




## <a name="change-the-drive-letters"></a>Modifier les lettres de lecteur 

1. Une fois que le redémarrage de la machine virtuelle, connectez-vous de nouveau à la machine virtuelle.

2. Cliquez sur le menu **Démarrer** et tapez **diskmgmt.msc** et appuyez sur ENTRÉE. Gestion des disques démarre.

3. Avec le bouton droit sur **D**, le lecteur de stockage temporaire, puis sélectionnez **Modifier la lettre de lecteur et chemins d’accès**.

4. Sous lettre, sélectionnez Lecteur **G** , puis sur **OK**. 

5. Avec le bouton droit sur le disque de données, puis sélectionnez **Modifier la lettre de lecteur et les chemins d’accès**.

6. Sous lettre, sélectionnez le lecteur **D** , puis sur **OK**. 

7. Avec le bouton droit sur **G**, le lecteur de stockage temporaire, puis sélectionnez **Modifier la lettre de lecteur et chemins d’accès**.

8. Sous lettre, sélectionnez Lecteur **E** , puis sur **OK**. 

> [AZURE.NOTE] Si votre ordinateur virtuel comporte d’autres disques ou les lecteurs, utilisez la méthode même réaffecter les lettres de lecteur des autres disques et lecteurs cachés. Vous souhaitez que la configuration de disque doivent :  
>- C : disque OS  
>- D : données disque  
>- E : disque temporaire



## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Reculer pagefile.sys au lecteur de stockage temporaire 

1. Cliquez sur le menu **Démarrer** , puis sélectionnez **système**

2. Dans le menu de gauche, sélectionnez **paramètres système avancés**.

3. Dans la section **performances** , sélectionnez **paramètres**.

4. Sélectionnez l’onglet **Avancé** .

5. Dans la section **mémoire virtuelle** , sélectionnez **Modifier**.

6. Sélectionnez le lecteur système d’exploitation **C** et cliquez sur **aucun fichier d’échange** , puis sur **définir**.

7. Sélectionnez le lecteur de stockage temporaire **E** et puis cliquez sur **Taille gérée par le système** , puis sur **définir**.

8. Cliquez sur **Appliquer**. Vous recevez un message d’avertissement que l’ordinateur doit être redémarré pour que les modifications prennent effet.

9. Redémarrez l’ordinateur virtuel.




## <a name="next-steps"></a>Étapes suivantes
- Vous pouvez augmenter l’espace de stockage disponible sur votre ordinateur virtuel en [associant un disque de données supplémentaires](virtual-machines-windows-attach-disk-portal.md).



