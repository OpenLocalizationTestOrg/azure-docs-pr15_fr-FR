<properties
 pageTitle="Envoyer des travaux à un module HPC cluster dans Azure | Microsoft Azure"
 description="Découvrez comment configurer un ordinateur local à soumettre des tâches à un cluster HPC Pack dans Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="10/14/2016"
 ms.author="danlep"/>

# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>Soumettre des travaux HPC à partir d’un ordinateur local à un cluster HPC Pack déployée dans Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Configurer un ordinateur client local pour soumettre les tâches à un cluster [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) dans Azure. Cet article vous explique comment configurer un ordinateur local avec les outils clients pour envoyer un travail sur HTTPS pour le cluster dans Azure. De cette façon, plusieurs utilisateurs cluster peuvent soumettre les tâches à un cluster HPC Pack sur le nuage, mais sans se connecter directement au nœud de tête machine virtuelle ou accéder à un abonnement Azure.

![Soumettre une tâche à un cluster dans Azure][jobsubmit]

## <a name="prerequisites"></a>Conditions préalables

* **Nœud de tête HPC Pack déployée dans une machine virtuelle Azure** - nous vous recommandons d’utiliser des outils automatisés comme un [modèle de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/) ou un [script PowerShell Azure](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) pour déployer le nœud de tête et cluster. Vous devez le nom DNS du nœud principal et les informations d’identification d’un administrateur de cluster pour accomplir les étapes décrites dans cet article.

* **Ordinateur client** - vous avez besoin d’un ordinateur client Windows ou Windows Server peut exécuter utilitaires de client HPC Pack (voir configuration [requise](https://technet.microsoft.com/library/dn535781.aspx)). Si vous voulez uniquement utiliser le portail web HPC Pack ou API REST à soumettre des tâches, vous pouvez utiliser n’importe quel ordinateur client de votre choix.

* **Support d’installation HPC Pack** - installer les utilitaires de client HPC Pack, le package d’installation gratuit pour la dernière version du Pack HPC (HPC Pack 2012 R2) est disponible à partir du [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). Assurez-vous que vous téléchargez la même version d’HPC Pack est installé sur le nœud de tête machine virtuelle.

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>Étape 1 : Installer et configurer les composants WebPart sur le nœud de tête

Pour activer une interface reste à soumettre des tâches au cluster via HTTPS, assurez-vous que les composants WebPart HPC Pack sont configurés sur le nœud de tête HPC Pack. S’ils ne sont pas déjà installés, tout d’abord installer les composants WebPart en exécutant le fichier d’installation HpcWebComponents.msi. Ensuite, configurez les composants en exécutant le script PowerShell HPC **Set-HPCWebComponents.ps1**.

Pour obtenir des procédures détaillées, consultez [installer Microsoft HPC Pack Web Components](http://technet.microsoft.com/library/hh314627.aspx).

>[AZURE.TIP] Certains modèles de démarrage rapide Azure HPC Pack pour installer et configurer automatiquement les composants WebPart. Si vous utilisez le [script de déploiement HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) pour créer le cluster, vous pouvez éventuellement installer et configurer les composants WebPart dans le cadre du déploiement.

**Pour installer les composants WebPart**

1. Connectez-vous au nœud principal machine virtuelle en utilisant les informations d’identification d’un administrateur de cluster.

2. À partir du dossier d’installation du Pack HPC, exécutez HpcWebComponents.msi sur le nœud principal.

3. Suivez les étapes de l’Assistant pour installer les composants WebPart

**Pour configurer les composants WebPart**

1. Sur le nœud de tête, démarrez HPC PowerShell en tant qu’administrateur.

2. Pour modifier le répertoire à l’emplacement de configuration du script, tapez la commande suivante :

    ```powershell
    cd $env:CCP_HOME\bin
    ```
3. Pour configurer l’interface REST et démarrer le Service Web HPC, tapez la commande suivante :

    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```

4. Lorsque vous êtes invité à sélectionner un certificat, sélectionnez le certificat qui correspond au nom DNS public du nœud principal. Par exemple, si vous déployez le nœud de tête machine virtuelle à l’aide du modèle de déploiement classique, le nom du certificat ressemble à CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net. Si vous utilisez le modèle de déploiement du Gestionnaire de ressources, le nom du certificat ressemble à CN =&lt;*HeadNodeDnsName*&gt;. &lt; *région*&gt;. cloudapp.azure.com.

    >[AZURE.NOTE] Vous sélectionnez ce certificat ultérieurement lorsque vous envoyez des travaux pour le nœud de tête à partir d’un ordinateur local. Ne sélectionnez ou configurer un certificat qui correspond au nom de l’ordinateur du nœud principal dans le domaine Active Directory (par exemple, CN =*MyHPCHeadNode.HpcAzure.local*).

5. Pour configurer le portail web de soumission de la tâche, tapez la commande suivante :

    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. Une fois le script terminé, arrêter et redémarrer le Service de planificateur de tâches HPC en tapant les commandes suivantes :

    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>Étape 2 : Installez les utilitaires de client HPC Pack sur un ordinateur local

Si vous voulez installer les utilitaires de client HPC Pack sur votre ordinateur, téléchargez les fichiers d’installation de HPC Pack (installation complète) à partir du [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). Lorsque vous commencez l’installation, choisissez l’option d’installation pour les **utilitaires de client HPC Pack**.

Pour utiliser les outils clients HPC Pack à soumettre des tâches pour le nœud de tête machine virtuelle, vous devez également exporter un certificat depuis le nœud de tête et installez-le sur l’ordinateur client. Le certificat doit être placé dans. Format limitée.

**Pour exporter le certificat du nœud de tête**

1. Sur le nœud principal, ajoutez le composant logiciel enfichable Certificats à une Console de gestion de Microsoft pour le compte d’ordinateur Local. Pour ajouter le composant logiciel enfichable, voir [Ajouter le composant logiciel enfichable Certificats à une console MMC](https://technet.microsoft.com/library/cc754431.aspx).

2. Dans l’arborescence, développez **certificats-ordinateur Local** > **personnel**, puis cliquez sur **certificats**.

3. Recherchez le certificat que vous avez configuré pour les composants WebPart HPC Pack dans [étape 1 : installer et configurer les composants WebPart sur le nœud de tête](#step-1:-install-and-configure-the-web-components-on-the-head-node) (par exemple, CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net).

4. Cliquez sur le certificat, puis cliquez sur **Toutes les tâches** > **Exporter**.

5. Dans l’Assistant Exportation de certificat, cliquez sur **suivant**et vérifiez que l’option **non, ne pas exporter la clé privée** est sélectionné.

6. Suivez les étapes restantes de l’Assistant Exporter le certificat dans binaire codé DER X.509 (. Format limitée).


**Pour importer le certificat sur l’ordinateur client**


1. Copiez le certificat que vous avez exporté du nœud de tête vers un dossier sur l’ordinateur client.

2. Sur l’ordinateur client, exécutez certmgr.msc.

3. Dans le Gestionnaire de certificats, développez **certificats – utilisateur actuel** > **Autorités de Certification racine de confiance**, cliquez sur **certificats**, puis cliquez sur **Toutes les tâches** > **Importer**.

4. Dans l’Assistant Importation de certificat, cliquez sur **suivant** et suivez les étapes pour importer le certificat que vous avez exporté du nœud de tête au magasin des autorités de Certification racine de confiance.



>[AZURE.TIP] Vous pouvez voir un avertissement de sécurité, car l’autorité de certification sur le nœud principal n’est pas reconnue par l’ordinateur client. Pour effectuer un test, vous pouvez ignorer ce message d’avertissement et terminer l’importation du certificat.

## <a name="step-3-run-test-jobs-on-the-cluster"></a>Étape 3 : Exécuter test tâches sur le cluster

Pour vérifier votre configuration, essayez d’exécuter des tâches sur le cluster dans Azure à partir de l’ordinateur local. Par exemple, vous pouvez utiliser les outils d’interface graphique HPC Pack ou des commandes de ligne de commande à soumettre des tâches au cluster. Vous pouvez également utiliser un portail web à soumettre des tâches.


**Pour exécuter les commandes d’envoi de travail sur l’ordinateur client**


1. Sur un ordinateur client dans lequel les utilitaires client HPC Pack sont installés, démarrez une invite de commande.

2. Tapez un exemple de commande. Par exemple, pour répertorier toutes les tâches sur le cluster, tapez une commande semblable à une des opérations suivantes, selon le nom DNS complet du nœud principal :

    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
    
    ou
    
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```

    >[AZURE.TIP] Utilisez le nom DNS complet du nœud principal, pas l’adresse IP, l’URL du planificateur. Si vous spécifiez l’adresse IP, une erreur s’affiche semblable à « le certificat de serveur nécessite soit disposer d’une chaîne de confiance valide ou pour être placé dans le magasin racine de confiance. »

3. Lorsque vous y êtes invité, tapez le nom d’utilisateur (sous la forme &lt;nom_domaine&gt;\\&lt;nom d’utilisateur&gt;) et mot de passe de l’administrateur de cluster HPC ou un autre utilisateur cluster que vous avez configuré. Vous pouvez choisir de stocker les informations d’identification localement pour plusieurs opérations de la tâche.

    Une liste de tâches s’affiche.


**Utiliser le Gestionnaire de projet HPC sur l’ordinateur client**

1. Si vous n’a pas été précédemment stocker les informations d’identification de domaine pour un utilisateur cluster lors de l’envoi d’une tâche, vous pouvez ajouter les informations d’identification dans le Gestionnaire d’informations d’identification.

    un. Dans le panneau de configuration de l’ordinateur client, démarrez le Gestionnaire d’informations d’identification.

    b. Cliquez sur **Informations d’identification Windows** > **Ajouter une information d’identification générique**.

    c. Spécifier l’adresse Internet (par exemple, https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler ou https://&lt;HeadNodeDnsName&gt;.&lt; région&gt;.cloudapp.azure.com/HpcScheduler) et le nom d’utilisateur (&lt;nom_domaine&gt;\\&lt;nom d’utilisateur&gt;) et mot de passe de l’administrateur de cluster ou un autre utilisateur cluster que vous avez configuré.

2. Sur l’ordinateur client, démarrez le Gestionnaire de projet HPC.

3. Dans la boîte de dialogue **Sélectionner le nœud principal** , tapez l’URL pour le nœud de tête dans Azure (par exemple, https://&lt;HeadNodeDnsName&gt;. cloudapp.net ou https://&lt;HeadNodeDnsName&gt;.&lt; région&gt;. cloudapp.azure.com).

    Gestionnaire des travaux HPC s’ouvre et affiche une liste de tâches sur le nœud principal.

**Pour utiliser le portail web s’exécutant sur le nœud de tête**

1. Démarrez un navigateur web sur l’ordinateur client, puis entrez une des adresses suivantes, selon le nom DNS complet du nœud principal :

    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
    
    ou
    
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. Dans la boîte de dialogue de sécurité s’affiche, tapez les informations d’identification de domaine de l’administrateur de cluster HPC. (Vous pouvez également ajouter d’autres utilisateurs cluster dans des rôles différents. Reportez-vous à [gestion de regrouper des utilisateurs.)](https://technet.microsoft.com/library/ff919335.aspx)

    Le portail web s’ouvre à l’affichage de liste de tâches.

3. Pour envoyer un travail d’exemple qui retourne la chaîne « Hello World » à partir du cluster, cliquez sur **nouvelle tâche** dans le volet de navigation gauche.

4. Dans la page **Nouvelle tâche** , sous **à partir des pages d’envoi**, cliquez sur **HelloWorld**. La page de soumission de travail s’affiche.

5. Cliquez sur **Envoyer**. Si vous y êtes invité, fournissent les informations d’identification de domaine de l’administrateur de cluster HPC. Le travail est soumis, et l’ID du travail apparaît dans la page **Mes travaux** .

6. Pour afficher les résultats de la tâche que vous avez envoyé, cliquez sur l’ID du travail, puis cliquez sur **Afficher les tâches** pour afficher le résultat de la commande (sous **sortie**).

## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez également soumettre les tâches au cluster Azure avec l' [API REST de Pack HPC](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).

* Si vous voulez envoyer les travaux du cluster à partir d’un client Linux, consultez l’exemple Python dans les [exemples de Code et HPC Pack 2012 R2 SDK](https://www.microsoft.com/download/details.aspx?id=41633).


<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png
