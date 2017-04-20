<properties
    pageTitle="Configurer des clusters à un domaine HDInsight | Microsoft Azure"
    description="Découvrez comment installer et configurer clusters HDInsight à un domaine"
    services="hdinsight"
    documentationCenter=""
    authors="saurinsh"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/26/2016"
    ms.author="saurinsh"/>

# <a name="configure-domain-joined-hdinsight-clusters-preview"></a>Configurer des clusters HDInsight à un domaine (Preview)

Découvrez comment configurer un cluster Azure HDInsight avec Azure Active Directory (AD Azure) et [Apache Ranger](http://hortonworks.com/apache/ranger/) pour tirer parti de l’authentification renforcée et stratégies de contrôle d’accès rôle enrichi.  HDInsight à un domaine peut uniquement être configuré sur clusters basés sur Linux. Pour plus d’informations, voir [clusters HDInsight introduire à un domaine](hdinsight-domain-joined-introduction.md).

Cet article est le premier didacticiel d’une série :

- Créer un cluster HDInsight connecté à Azure AD (via la fonctionnalité Azure Directory Domain Services) avec Ranger Apache activé.
- Créer et appliquer des stratégies de Hive par le biais Ranger Apache et permettre aux utilisateurs (par exemple, scientifiques données) pour vous connecter à Hive à l’aide d’outils basés sur ODBC, par exemple Excel, etc. de Tableau. Microsoft travaille sur l’ajout d’autres charges de travail, tels que HBase et vague de, explosion à HDInsight à un domaine plus rapidement.

Exemple de la topologie final se présente comme suit :

![Topologie de HDInsight à un domaine](.\media\hdinsight-domain-joined-configure\hdinsight-domain-joined-topology.png)

Étant donné que Azure AD actuellement prend uniquement en charge les réseaux virtuels classiques (VNets) et HDInsight basé sur Linux clusters prise en charge uniquement Azure le Gestionnaire de ressources en fonction de VNets, intégration HDInsight Azure AD requiert deux VNets et un peering entre elles. Pour plus d’informations de comparaison entre les modèles de deux déploiement, voir [Azure le Gestionnaire de ressources et déploiement classique : comprendre les modèles de déploiement et l’état de vos ressources](../resource-manager-deployment-model.md). Les deux VNets doit être placé dans la même région en tant que le service d’annuaire Active Directory Azure.

Les noms de service Azure doivent être globalement uniques. Les noms suivants sont utilisés dans ce didacticiel. Contoso est un nom fictif. Lorsque vous utilisez le didacticiel, vous devez remplacer *contoso* sous un autre nom. 
    
**Noms :**

|Propriété|Valeur|
|--------|-----|
| Azure AD VNet|contosoaadvnet|
| Azure AD Machine virtuelle)|contosoaadadmin. Cet ordinateur virtuel est utilisé pour configurer l’unité d’organisation et inverser la zone DNS.|
| Azure annuaire Active Directory|contosoaaddirectory|
| Nom de domaine Active Directory Azure|Contoso (contoso.onmicrosoft.com)|
| HDInsight VNet|contosohdivnet|
| Groupe de ressources HDInsight VNet|contosohdirg|
| Cluster HDInsight|contosohdicluster|

Ce didacticiel fournit les étapes de configuration d’un cluster HDInsight à un domaine. Chaque section comporte des liens vers d’autres articles avec plus d’informations.

## <a name="prerequisite"></a>Au préalable :

- Se familiariser avec les [Services de domaine Active Directory Azure](https://azure.microsoft.com/services/active-directory-ds/) sa structure [tarifs](https://azure.microsoft.com/pricing/details/active-directory-ds/) .
- Vérifiez que votre abonnement est autorisés pour cette version d’évaluation. Vous pouvez le faire en envoyant un courrier électronique à hdipreview@microsoft.com avec votre ID d’abonnement.
- Un certificat SSL est signé par une autorité de signature pour votre domaine. Le certificat est requis en configurant LDAP sécurisé. Certificats auto-signé ne peuvent pas être utilisés.

## <a name="procedures"></a>Procédures

1. Créer un VNet classique Azure pour votre annonce Azure.  
2. Créez et configurez Azure AD et Azure AD DS.
3. Ajouter une machine virtuelle à la VNet classique de création d’unité d’organisation. 
4. Créer une unité d’organisation pour Azure AD DS.
5. Créer un HDInsight VNet dans le mode de gestion des ressources Azure.
6. Configurer les zones DNS inverse pour la Azure AD DS.
6. VNets égal les deux.
7. Créer un cluster HDInsight.

> [AZURE.NOTE] Ce didacticiel suppose que vous n’avez pas une annonce Azure. Si vous en avez, vous pouvez ignorer la partie à l’étape 2.
    
## <a name="create-an-azure-classic-vnet"></a>Créer un VNet classique Azure

Dans cette section, vous créez un VNet classique à l’aide du portail Azure. Dans la section suivante, vous activez le service d’annuaire Active Directory Azure pour votre annonce Azure dans la VNet classique. Pour plus d’informations sur la procédure suivante et l’utilisation d’autres méthodes de création de VNet, voir [créer un réseau virtuel (classique) à l’aide du portail Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md).

**Pour créer un VNet classique**

1. Ouverture de session sur le [portail Azure](https://portal.azure.com). 
2. Cliquez sur **Nouveau** > **réseau** > **réseau virtuel**.
3. Dans la zone **Sélectionner un modèle de déploiement**, sélectionnez **classique**, puis cliquez sur **créer**.
4. Entrez ou sélectionnez les valeurs suivantes :

    - **Nom**: contosoaadvnet
    - **Espace d’adressage**: 10.1.0.0/16
    - **Nom du sous-réseau**: Subnet1
    - **Plage d’adresses sous-réseau**: 10.1.0.0/24
    - **Abonnement**: (sélectionnez un abonnement utilisé pour la création de ce VNet.)
    - **ResourceGroup**:
    - **Emplacement**: (Sélectionner une région pour votre cluster HDInsight.)

        > [AZURE.IMPORTANT] Vous devez choisir un emplacement qui prend en charge Azure AD DS. Pour plus d’informations, voir [produits disponibles par région](https://azure.microsoft.com/en-us/regions/services/). 
        >
        > La VNet classique et la VNet de groupe de ressources doivent être placé dans la même région en tant que le service d’annuaire Active Directory Azure.

5. Cliquez sur **créer** pour créer la VNet.

## <a name="create-and-configure-azure-ad-ds-for-your-azure-ad"></a>Créez et configurez Azure AD DS pour votre annonce Azure

Dans cette section, vous allez :

1. Créer une annonce Azure.
2. Créer des utilisateurs Azure AD. Ces utilisateurs sont des utilisateurs de domaine. Vous utilisez le premier utilisateur pour configurer le cluster HDInsight avec Azure Active Directory.  Les deux autres utilisateurs sont facultatives pour ce didacticiel. Ils seront utilisées dans les [stratégies de configurer la ruche pour les clusters HDInsight à un domaine](hdinsight-domain-joined-run-hive.md) lorsque vous configurez les stratégies Apache Ranger.
3. Créer le groupe Administrateurs de DC AAD et ajoutez l’utilisateur Azure AD au groupe. Cet utilisateur vous permet de créer l’unité d’organisation.
4. Activer les Services de domaine Active Directory Azure (Azure AD DS) pour la publicité Azure.
7. Configurer LDAPS pour Azure Active Directory. L’accès protocole LDAP (Lightweight Directory) est utilisé pour lire et écrire à Azure Active Directory.

Si vous préférez utiliser une annonce Azure existante, vous pouvez ignorer les étapes 1 et 2.

**Pour créer une annonce Azure**

1. À partir du [portail classique Azure](https://manage.windowsazure.com), cliquez sur **Nouveau** > **Application Services** > **Active Directory** > **répertoire** > **Créer personnalisé**. 
3. Entrez ou sélectionnez les valeurs suivantes :

    - **Nom**: contosoaaddirectory
    - **Nom de domaine**: contoso.  Ce nom doit être unique.
    - **Pays ou région**: sélectionnez votre pays ou région.
4. Cliquez sur **terminé**.


**Créer un utilisateur Azure AD**

1. À partir du [portail classique Azure](https://manage.windowsazure.com), cliquez sur **Active Directory** -> **contosoaaddirectory**. 
3. Cliquez sur **utilisateurs** dans le menu supérieur.
4. Cliquez sur **Ajouter un utilisateur**.
4. Entrez le **Nom d’utilisateur**, puis cliquez sur **suivant**. 
5. Configurer les profils utilisateur ; Dans le **rôle**, sélectionnez **Administrateur Global**; puis sur **suivant**.  Le rôle Administrateur général est nécessaires pour créer des unités d’organisation.
6. Cliquez sur **créer** pour obtenir un mot de passe temporaire.
7. Faire une copie du mot de passe, puis cliquez sur **Terminer**. Plus loin dans ce didacticiel, vous utiliserez cet utilisateur d’administrateur général pour vous connecter à l’administrateur machine virtuelle pour créer une unité d’organisation et configurer DNS inverse.


Suivez la procédure pour créer deux davantage d’utilisateurs avec le rôle **utilisateur** , hiveuser1 et hiveuser2. Les utilisateurs suivants seront utilisées dans les [stratégies de configurer la ruche pour les clusters HDInsight à un domaine](hdinsight-domain-joined-run-hive.md).

**Pour créer le AAD DC groupe Administrateurs et ajouter un utilisateur Azure AD**

1. À partir du [portail classique Azure](https://manage.windowsazure.com), cliquez sur **Active Directory** > **contosoaaddirectory**. 
3. Dans le menu supérieur, cliquez sur **groupes** .
4. Cliquez sur **Ajouter un groupe** ou **Ajouter un groupe**.
5. Entrez ou sélectionnez les valeurs suivantes :

    - **Nom**: les administrateurs AAD contrôleur de domaine.  Ne modifiez pas le nom du groupe.
    - **Type de groupe**: sécurité.
6. Cliquez sur **terminé**.
7. Cliquez sur **Administrateurs de DC AAD** pour ouvrir le groupe.
8. Cliquez sur **Ajouter des membres**.
9. Sélectionnez le premier utilisateur que vous avez créé à l’étape précédente, puis cliquez sur **Terminer**.
10. Répétez ces étapes pour créer un autre groupe appelé **HiveUsers**et ajoutez les deux utilisateurs Hive au groupe.

Pour plus d’informations, voir [Azure Active Directory Domain Services (Preview) - créer un groupe « AAD DC administrateurs »](../active-directory-domain-services/active-directory-ds-getting-started.md).

**Pour activer Azure AD DS pour votre annonce Azure**

1. À partir du [portail classique Azure](https://manage.windowsazure.com), cliquez sur **Active Directory** > **contosoaaddirectory**. 
3. Cliquez sur **configurer** dans le menu supérieur.
4. Faites défiler vers le bas jusqu'à **Les Services de domaine**, puis définissez les valeurs suivantes :

    - **Activer les services de domaine pour ce répertoire**: Oui.
    - **Nom de domaine DNS des services de domaine**: affiche le nom de DNS par défaut du répertoire Azure. Par exemple, contoso.onmicrosoft.com.
    - **Services de domaine de se connecter à ce réseau virtuel**: sélectionnez le réseau virtuel classique que vous avez créée, c'est-à-dire **contosoaadvnet**.
    
6. Cliquez sur **Enregistrer** à partir du bas de la page. Vous verrez **en attente...** en regard de **Activer les services de domaine pour ce répertoire**.  
7. Attendez que **en attente...** a disparu, et **Adresse IP** est remplie. Deux adresses IP ne seront remplis. Voici les adresses IP contrôleur de domaine configuré par les Services de domaine. Chaque adresse IP seront visible une fois que le contrôleur de domaine correspondant est généré et prêt. Notez les deux adresses IP. Vous en aurez besoin ultérieurement.

Pour plus d’informations, voir [Azure Active Directory Domain Services (Preview) - activer Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-enableaadds.md).

**Pour synchroniser votre mot de passe**

Si vous utilisez votre propre domaine, vous devez synchroniser le mot de passe. Voir [Activer la synchronisation de mot de passe pour les services de domaine Azure AD pour un Azure exclusivement le nuage, annuaire AD](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).


**Pour configurer LDAPS pour Azure Active Directory**

1. Obtenez un certificat SSL signé par une autorité de signature de votre domaine. Certificats auto-signé ne peuvent pas être utilisés. Si vous ne pouvez pas obtenir un certificat SSL, veuillez contacter hdipreview@microsoft.com pour une exception.
1. À partir du [portail classique Azure](https://manage.windowsazure.com), cliquez sur **Active Directory** > **contosoaaddirectory**. 
3. Cliquez sur **configurer** dans le menu supérieur.
4. Faites défiler pour **les services de domaine**.
5. Cliquez sur **configurer le certificat**.
6. Suivez les instructions pour spécifier le fichier de certificat et le mot de passe. Vous verrez **en attente...** en regard de **Activer les services de domaine pour ce répertoire**.  
7. Attendez que **en attente...** a disparu, et **Sécuriser le certificat LDAP** est remplie.  Cela peut prendre jusqu'à 10 minutes ou plus.
 
>[AZURE.NOTE] Si certaines tâches en arrière-plan sont exécutés sur le service d’annuaire Active Directory Azure, vous pouvez observer une erreur lors du téléchargement du certificat : <i>il est une opération en cours d’exécution pour ce client. Réessayez plus tard</i>.  Si vous rencontrez cette erreur, essayez à nouveau après un certain temps. La deuxième IP de contrôleur de domaine peut prendre jusqu'à 3 heures à mettre en service.

Pour plus d’informations, voir [Configurer Secure LDAP (LDAPS) pour un domaine de Services de domaine Active Directory Azure gérées](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="configure-an-organizational-unit-and-reverse-dns"></a>Configurer une unité organisationnelle et DNS inverse

Dans cette section, vous ajoutez une machine virtuelle à la AD VNet Azure et installez les outils d’administration sur cet ordinateur virtuel afin de pouvoir configurer une unité organisationnelle et DNS inverse. Recherche DNS inversée est requise pour l’authentification Kerberos.

**Pour créer une machine virtuelle dans le réseau virtuel**

1. À partir du [portail classique Azure](https://manage.windowsazure.com), cliquez sur **Nouveau** > **Calculer** > **Machine virtuelle** > **De la galerie**.
3. Sélectionnez une image, puis cliquez sur **suivant**.  Si vous ne savez pas celui que vous souhaitez utiliser, sélectionnez la valeur par défaut, **Windows Server 2012 R2 centre de données**.
4. Entrez ou sélectionnez les valeurs suivantes :

    - Nom de la Machine virtuelle : **contosoaadadmin**
    - Niveau : **base**
    - Nouveau nom d’utilisateur : (Entrez un nom d’utilisateur)
    - Mot de passe : (Entrez un mot de passe)
    
    Notez le nom d’utilisateur et le mot de passe est l’administrateur local.
    
5. Cliquez sur **suivant**
6. Dans la **Région/virtuelle réseau**, sélectionnez le réseau virtuel que vous avez créé dans la dernière étape (contosoaadvnet) et puis cliquez sur **suivant**.
7. Cliquez sur **terminé**.

**Pour RDP de la machine virtuelle**

1. À partir du [portail classique Azure](https://manage.windowsazure.com), cliquez sur **Machines virtuelles** > **contosoaadadmin**.
3. Cliquez sur **tableau de bord** dans le menu supérieur.
4. Cliquez sur **se connecter** à partir du bas de la page.
5. Suivez les instructions et utilisez le nom d’utilisateur administrateur local et le mot de passe pour vous connecter.

**Pour participer à une machine virtuelle au domaine Azure AD**

1. À partir de la session RDP, cliquez sur **Démarrer**, puis cliquez sur **Gestionnaire de serveur**.
2. Dans le menu de gauche, cliquez sur **Serveur Local** .
3. Dans le groupe de travail, cliquez sur **groupe de travail**.
4. Cliquez sur **Modifier**.
5. Cliquez sur **un domaine**, entrez **contoso.onmicrosoft.com**, puis cliquez sur **OK**.
6. Entrez les informations d’identification utilisateur de domaine, puis cliquez sur **OK**.
7. Cliquez sur **OK**.
8. Cliquez sur **OK** pour accepter redémarrer l’ordinateur.
9. Cliquez sur **Fermer**.
10. Cliquez sur **Redémarrer maintenant**.

Pour plus d’informations, voir [participer à une machine virtuelle Windows Server pour un domaine géré](../active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm.md).

**Pour installer les outils d’administration Active Directory et DNS**

1. RDP dans **contosoaadadmin** en utilisant le compte d’utilisateur Azure AD.
2. Cliquez sur **Démarrer**, puis cliquez sur **Gestionnaire de serveur**.
3. Dans le menu de gauche, cliquez sur **tableau de bord** .
4. Cliquez sur **Gérer**, puis cliquez sur **Ajouter des rôles et les fonctionnalités**.
5. Cliquez sur **suivant**.
6. Sélectionnez **l’installation basée sur une fonctionnalité ou rôle**, puis cliquez sur **suivant**.
7. Sélectionnez la machine virtuelle courante dans le pool du serveur, puis cliquez sur **suivant**.
8. Cliquez sur **suivant** pour ignorer des rôles.
9. Développez **Outils d’Administration de serveur distant**, développez **Outils d’Administration de rôle**, sélectionnez **AD DS et outils ADAM** et les **Outils du serveur DNS**, puis sur **suivant**. 
10. Cliquez sur **suivant**
10. Cliquez sur **installer**.

Pour plus d’informations, voir [Outils d’administration installer Active Directory sur l’ordinateur virtuel](../active-directory-domain-services/active-directory-ds-admin-guide-administer-domain.md#task-2---install-active-directory-administration-tools-on-the-virtual-machine).


**Pour configurer le système DNS inverse**

1. RDP pour contosoaadadmin en utilisant le compte d’utilisateur Azure AD.
2. Cliquez sur **Démarrer**, cliquez sur **Outils d’administration**, puis cliquez sur **DNS**. 
3. Cliquez sur **non** pour ignorer l’ajout de ContosoAADAdmin.
4. Sélectionnez **l’ordinateur suivant**, entrez l’adresse IP du premier serveur DNS que vous avez configuré précédemment, puis cliquez sur **OK**.  Vous doit afficher que le contrôleur de domaine/DNS est ajouté à la partie gauche.
3. Développez le serveur DNS/DC, avec le bouton droit de **Zones de recherche inversée**, puis cliquez sur **Nouvelle Zone**. L’Assistant Nouvelle Zone s’ouvre.
4. Cliquez sur **suivant**.
5. Sélectionnez **zone principale**, puis cliquez sur **suivant**.
6. Sélectionnez cette option **pour tous les serveurs DNS s’exécutant sur contrôleurs de domaine dans ce domaine**, puis cliquez sur **suivant**.
6. Sélectionnez **Zone de recherche inversée IPv4**, puis cliquez sur **suivant**.
7. Dans **Numéro d’identification réseau**, entrez le préfixe de la plage de réseau HDInsight VNET, puis cliquez sur **suivant**. Vous allez créer la HDInsight VNet dans la section suivante.
8. Cliquez sur **suivant**.
9. Cliquez sur **suivant**.
10. Cliquez sur **Terminer**.



L’unité d’organisation que vous créez sera ensuite utilisée lors de la création du cluster HDInsight. Les utilisateurs de système Hadoop et les comptes d’ordinateur seront placés dans cette unité d’organisation.

**Créer une unité d’organisation (OU) sur un domaine géré Azure Active Directory Domain Services**

1. RDP dans **contosoaadadmin** en utilisant le compte de domaine qui se trouve dans le groupe **Administrateurs de DC AAD** .
2. Cliquez sur **Démarrer**, cliquez sur **Outils d’administration**, puis cliquez sur **Centre d’administration Active Directory**.
5. Cliquez sur le nom de domaine dans le volet gauche. Par exemple, contoso.
6. Cliquez sur **Nouveau** sous le nom de domaine dans le volet **Office** , puis cliquez sur **Unité d’organisation**.
7. Entrez un nom, par exemple **HDInsightOU**, puis sur **OK**. 


Pour plus d’informations, voir [créer une unité d’organisation (OU) sur un domaine de Services de domaine Active Directory Azure gérées](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md).


## <a name="create-a-resource-manager-vnet-for-hdinsight-cluster"></a>Créer un VNet Gestionnaire de ressources pour cluster HDInsight

Dans cette section, vous allez créer un VNet Gestionnaire de ressources Azure qui sera utilisé pour le cluster HDInsight. Pour plus d’informations sur la création de VNET Azure à l’aide d’autres méthodes, voir [créer un réseau virtuel](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)

Après avoir créé le VNet, vous allez configurer le Gestionnaire de ressources VNet pour utiliser les mêmes serveurs DNS en ce qui concerne la AD VNet Azure. Si vous avez suivi les étapes décrites dans ce didacticiel pour créer la VNet classique et Azure Active Directory, les serveurs DNS sont 10.1.0.4 et 10.1.0.5.

**Pour créer un VNet Gestionnaire de ressources**

1. Ouverture de session sur le [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau**, **mise en réseau**, puis **réseau virtuel**. 
3. Dans la zone **Sélectionner un modèle de déploiement**, sélectionnez **Le Gestionnaire de ressources**, puis cliquez sur **créer**.
4. Tapez ou sélectionnez les valeurs suivantes :

    - **Nom**: contosohdivnet
    - **Espace d’adressage**: 10.2.0.0/16. Vérifiez que la plage d’adresses ne peut pas recouvrir la plage d’adresses IP de le VNet classique.
    - **Nom du sous-réseau**: Subnet1
    - **Plage d’adresses sous-réseau**: 10.2.0.0/24
    - **Abonnement**: (sélectionnez votre abonnement Azure.)
    - **Groupe de ressources**: contosohdirg
    - **Emplacement**: (sélectionné au même emplacement comme le AD VNet Azure, c'est-à-dire contosoaadvnet).

5. Cliquez sur **créer**.


**Pour configurer le système DNS pour le Gestionnaire de ressources VNet**

1. À partir du [portail Azure](https://portal.azure.com), cliquez sur **plusieurs services de** -> **réseaux virtuels**. Vérifiez que ne pas pour cliquer sur **les réseaux virtuels (classiques)**.
2. Cliquez sur **contosohdivnet**.
4. Cliquez sur **les serveurs DNS** du côté gauche de la nouvelle cuillère.
6. Cliquez sur **personnalisée**et entrez les valeurs suivantes :

    - 10.1.0.4
    - 10.1.0.5

    Ces adresses IP du serveur DNS doivent correspondre aux serveurs DNS dans la VNet de AD Azure (VNet classique).
7. Cliquez sur **Enregistrer**.

























## <a name="peer-the-azure-ad-vnet-and-the-hdinsight-vnet"></a>Homologue la VNet Azure AD et la HDInsight VNet

**Pour examiner les deux VNet**

1. Ouverture de session sur le [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, cliquez sur **plusieurs services** .
3. Cliquez sur **les réseaux virtuels**. Ne cliquez pas sur **les réseaux virtuels (classiques)**.
4. Cliquez sur **contosohdivnet**.  Il s’agit de la HDInsight VNet.
5. Cliquez sur **Peerings** dans le menu de gauche de la cuillère.
6. Dans le menu supérieur, cliquez sur **Ajouter** . Il s’ouvre la carte **Ajouter peering** .
7. Sur la carte **Ajouter peering** , définissez ou sélectionnez les valeurs suivantes :

    - **Nom**: ContosoAADHDIVNetPeering
    - **Modèle de déploiement réseau virtuelle**: classique
    - **Abonnement**: sélectionnez le nom de votre abonnement utilisé pour la vnet classique (Azure AD).
    - **Réseau virtuel**: contosoaadvnet.
    - **Autoriser l’accès réseau virtuel**: (vérification)
    - **Autoriser le trafic transféré**: (vérification). Laissez deux autres cases à cocher désactivée.

8. Cliquez sur **OK**.



## <a name="create-hdinsight-cluster"></a>Créer cluster HDInsight


Dans cette section, vous créez un cluster Hadoop basé sur Linux dans HDInsight via le portail Azure ou [modèle Azure le Gestionnaire de ressources](../resource-group-template-deploy.md). Pour les autres méthodes de création de cluster et comprendre les paramètres, voir [créer HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md). Pour plus d’informations sur l’utilisation de modèle du Gestionnaire de ressources pour créer des clusters Hadoop dans HDInsight, voir [Hadoop créer des groupes dans un HDInsight à l’aide de modèles de gestionnaire de ressources](hdinsight-hadoop-create-windows-clusters-arm-templates.md)


**Pour créer un cluster de HDInsight à un domaine à l’aide du portail Azure**

1. Ouverture de session sur le [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau**, **Intelligence + analytique**, puis **HDInsight**.
3. À partir de la carte **cluster nouveau HDInsight** , entrez ou sélectionnez les valeurs suivantes :

    - **Nom de cluster**: entrez un nouveau nom de cluster pour le cluster HDInsight à un domaine.
    - **Abonnement**: sélectionnez un abonnement Azure utilisé pour la création de ce groupe.
    - **Configuration de cluster**:

        - **Type de cluster**: Hadoop. HDInsight à un domaine n’est actuellement pris en charge sur Hadoop uniquement clusters.
        - **Système d’exploitation**: Linux.  HDInsight à un domaine est uniquement prise en charge sur les clusters basés sur Linux HDInsight.
        - **Version**: Hadoop 2.7.3 (HDI 3.5). HDInsight à un domaine est uniquement prise en charge la version de cluster HDInsight 3.5.
        - **Type de cluster**: PREMIUM

        Cliquez sur **Sélectionner** pour enregistrer les modifications.

    - **Informations d’identification**: configurer les informations d’identification pour l’utilisateur cluster et l’utilisateur SSH.
    - **Source de données**: créer un nouveau compte de stockage ou utiliser un compte de stockage existant en tant que le compte de stockage par défaut pour le cluster HDInsight. L’emplacement doit être identique aux deux VNets.  L’emplacement est également l’emplacement du cluster HDInsight.
    - **Tarification**: sélectionnez le nombre de nœuds de travail de votre cluster.
    - **Les configurations avancées**: 

        - **Rejoindre domaine & Vnet/sous-réseau**: 

            - **Paramètres de domaine**: 

                - **Nom de domaine**: contoso.onmicrosoft.com
                - **Nom d’utilisateur de domaine**: entrez un nom de domaine. Ce domaine doit disposer des autorisations suivantes : joindre des ordinateurs au domaine et placez-les dans l’unité d’organisation que vous avez configuré précédemment ; Créer des identités de service au sein de l’unité d’organisation que vous avez configuré précédemment ; Créer des entrées DNS inverses. Cet utilisateur du domaine devient l’administrateur de ce cluster HDInsight à un domaine.
                - **Mot de passe de domaine**: entrez le mot de passe utilisateur.
                - **Unité d’organisation**: entrez le nom unique de l’énoncer unité d’organisation que vous avez configuré précédemment. Par exemple : unité d’organisation = HDInsightOU, DC = contoso, DC = onmicrosoft, DC = com
                - **URL LDAPS**: ldaps://contoso.onmicrosoft.com:636
                - **Groupe d’utilisateurs Access**: spécifier la sécurité groupe dont les utilisateurs que vous voulez synchroniser avec le cluster. Par exemple, HiveUsers.

                Cliquez sur **Sélectionner** pour enregistrer les modifications.

                ![Portail de HDInsight à un domaine configurer les paramètres du domaine](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
            - **Réseau virtuel**: contosohdivnet
            - **Sous-réseau**: Subnet1

            Cliquez sur **Sélectionner** pour enregistrer les modifications.       
        Cliquez sur **Sélectionner** pour enregistrer les modifications.
    - **Groupe de ressources**: sélectionnez le groupe de ressources utilisé pour le HDInsight VNet (contosohdirg).

4. Cliquez sur **créer**.  

Une autre option pour la création de cluster HDInsight à un domaine consiste à utiliser le modèle de gestion des ressources Azure. La procédure suivante vous explique comment procéder :

**Pour créer un cluster de HDInsight à un domaine à l’aide d’un modèle de gestion des ressources**

1. Cliquez sur l’image suivante pour ouvrir un modèle de gestionnaire de ressources dans le portail Azure. Le modèle de gestionnaire de ressources se trouve dans un conteneur blob public. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. À partir de la carte de **paramètres** , entrez les valeurs suivantes :

    - **Abonnement**: (sélectionnez votre abonnement Azure).
    - **Groupe de ressources**: cliquez sur **utiliser existant**, et spécifiez le même groupe de ressources que vous avez utilisé.  Par exemple contosohdirg. 
    - **Emplacement**: indiquez un emplacement de groupe de ressources.
    - **Nom de cluster**: entrez un nom pour le cluster Hadoop que vous allez créer. Par exemple contosohdicluster.
    - **Type de cluster**: sélectionnez un type de cluster.  La valeur par défaut est **hadoop**.
    - **Emplacement**: sélectionnez un emplacement pour le cluster.  Le compte de stockage par défaut utilise le même emplacement.
    - **Nombre de nœuds de travail cluster**: sélectionnez le nombre de nœuds de travail.
    - **Mot de passe et le nom de connexion cluster**: le nom de connexion par défaut est **admin**.
    - **Mot de passe et nom d’utilisateur SSH**: le nom d’utilisateur par défaut est **sshuser**.  Vous pouvez la renommer. 
    - **Id de réseau virtuel**: /subscriptions/&lt;SubscriptionID > /resourceGroups/&lt;ResourceGroupName > /providers/Microsoft.Network/virtualNetworks/&lt;VNetName >
    - **Sous-réseau virtuel**: /subscriptions/&lt;SubscriptionID > /resourceGroups/&lt;ResourceGroupName > /providers/Microsoft.Network/virtualNetworks/&lt;VNetName >/sous-réseaux/Subnet1
    - **Nom de domaine**: contoso.onmicrosoft.com
    - **Nom unique unité organisation**: unité d’organisation = HDInsightOU, DC = contoso, DC = onmicrosoft, DC = com
    - **Cluster utilisateurs groupe D Ns**: «\"CN = HiveUsers, unité d’organisation = AADDC utilisateurs, DC =<DomainName>, DC = onmicrosoft, DC = com\"»
    - **LDAPUrls**: [« ldaps://contoso.onmicrosoft.com:636 »]
    - **DomainAdminUserName**: (entrez le nom d’utilisateur domaine)
    - **DomainAdminPassword**: (entrez le mot de passe domain admin)
    - **J’accepte les termes et conditions indiquées ci-dessus**: (vérification)
    - **Épingler au tableau de bord**: (vérification)

6. Cliquez sur **acheter**. Vous verrez une vignette intitulée **déploiement de déploiement d’un modèle**. Il est nécessaire à environ 20 minutes pour créer un cluster. Une fois que le cluster est créé, vous pouvez cliquer sur la carte cluster dans le portail pour l’ouvrir.

Une fois que vous avez terminé le didacticiel, vous souhaiterez peut-être supprimer le cluster. Avec HDInsight, vos données sont stockées dans le stockage Azure, afin de supprimer en toute sécurité un cluster lorsqu’elle n’est pas en cours d’utilisation. Vous êtes également chargé pour un cluster de HDInsight, même lorsqu’elle n’est pas en cours d’utilisation. Dans la mesure où les frais pour le cluster sont plus autant de fois que les frais de stockage, il est préférable économique de supprimer clusters lorsqu’ils ne sont pas en cours d’utilisation. Pour obtenir des instructions de la suppression d’un cluster, voir [Hadoop gérer les groupes dans un HDInsight à l’aide du portail Azure](hdinsight-administer-use-management-portal.md#delete-clusters).





## <a name="next-steps"></a>Étapes suivantes

- Pour configurer des stratégies Hive et exécuter des requêtes Hive, consultez [configurer ruche stratégies pour les clusters HDInsight à un domaine](hdinsight-domain-joined-run-hive.md).
- Pour l’exécution des requêtes Hive à l’aide de SSH sur clusters HDInsight à un domaine, voir [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-domain-joined-hdinsight-cluster).
