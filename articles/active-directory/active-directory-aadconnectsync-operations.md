<properties
   pageTitle="Azure AD Connect synchronisation : tâches opérationnelles et considérations | Microsoft Azure"
   description="Cette rubrique décrit les tâches opérationnelles de synchronisation Azure AD Connect et le préparer pour faire fonctionner ce composant."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/01/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Azure AD Connect synchronisation : tâches opérationnelles et examen
L’objectif de cette rubrique est pour décrire les tâches opérationnelles pour la synchronisation Azure AD Connect.

## <a name="staging-mode"></a>Mode de mise en attente
Mode de mise en attente peut être utilisé pour plusieurs scénarios, notamment :

-   Disponibilité.
-   Tester et déployer la modification de configuration.
-   Présentez un nouveau serveur et désaffecter l’ancienne.

Avec un serveur en mode mise en attente, vous pouvez apporter des modifications à la configuration et prévisualiser les modifications avant de vous rendre le serveur actif. Il vous permet également d’exécuter une importation intégrale et synchronisation complète pour vérifier que toutes les modifications sont susceptibles d’avant d’apporter ces modifications dans votre environnement de production.

Pendant l’installation, vous pouvez sélectionner le serveur en **mode de mise en attente**. Cette action rend le serveur actif pour l’importation et la synchronisation, mais il ne s’exécute pas des exportations. Un serveur en mode intermédiaire ne fonctionne pas synchroniser de mot de passe ou écriture différée mot de passe, même si vous avez sélectionné ces fonctionnalités lors de l’installation. Lorsque vous désactivez le mode mise en attente, le serveur lance l’exportation, permet de synchronisation de mot de passe et permet d’écriture différée de mot de passe.

Vous pouvez toujours forcer une exportation à l’aide du Gestionnaire de service de synchronisation.

Un serveur en mode mise en attente continue à recevoir des modifications d’Active Directory et Azure AD. Il est toujours une copie des dernières modifications et peuvent durer très rapides sur les responsabilités d’un autre serveur. Si vous apportez des modifications à votre serveur principal, il est votre responsabilité d’apporter les modifications mêmes au serveur en mode de mise en attente.

Pour ceux qui vous avec connaissance des technologies de synchronisation plus anciens, le mode intermédiaire est différent dans la mesure où il dispose de sa propre base de données SQL. Cette architecture permet au serveur en mode mise en attente de se trouver dans un centre de données différent.

### <a name="verify-the-configuration-of-a-server"></a>Vérifier la configuration d’un serveur
Pour appliquer cette méthode, procédez comme suit :

1. [Préparer](#prepare)
2. [Importer et synchroniser](#import-and-synchronize)
3. [Vérifier](#verify)
4. [Serveur actif commutateur](#switch-active-server)

#### <a name="prepare"></a>Préparer

1. Installer Azure AD Connect, sélectionnez **le mode de mise en attente**et désélectionner **lancer une synchronisation** sur la dernière page de l’Assistant installation. Ce mode vous permet d’exécuter le moteur de synchronisation manuellement.
![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Signe désactive/nom d’utilisateur et dans le menu Démarrer, sélectionnez **Le Service de synchronisation**.

#### <a name="import-and-synchronize"></a>Importer et synchroniser

1. Sélectionnez les **connecteurs**, puis sélectionnez le premier connecteur avec le type de **Services de domaine Active Directory**. Cliquez sur **exécuter**, sélectionnez **importation complète**, puis **OK**. Procédez comme suit pour tous les connecteurs de ce type.
2. Sélectionnez le connecteur à type **Azure Active Directory (Microsoft)**. Cliquez sur **exécuter**, sélectionnez **importation complète**, puis **OK**.
3. Vérifiez que l’onglet connecteurs est toujours sélectionnée. Pour chaque connecteur à type de **Services de domaine Active Directory**, cliquez sur **exécuter**, sélectionnez **Synchronisation Delta**, puis **OK**.
4. Sélectionnez le connecteur à type **Azure Active Directory (Microsoft)**. Cliquez sur **exécuter**, sélectionnez **Synchronisation Delta**, puis **OK**.

Vous avez maintenant transférées Exporter passe à Azure AD et locaux AD (si vous utilisez un déploiement hybride Exchange). Étapes suivantes permettent à inspecter que doit modifier avant de commencer l’exportation vers les répertoires réellement.

#### <a name="verify"></a>Vérifier

1. Démarrer une invite de commandes et accédez à`%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Exécuter :`csexport "Name of Connector" %temp%\export.xml /f:x`  
Le nom du connecteur sont accessibles dans le Service de synchronisation. Il comporte un nom similaire à « contoso.com – AAD » pour Azure AD.
3. Exécuter :`CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Vous avez un fichier dans % temp% nommé export.csv qui peut être examiné dans Microsoft Excel. Ce fichier contient toutes les modifications qui doivent être exportés.
5. Apportez les modifications nécessaires à la configuration ou de données et exécuter ces étapes à nouveau (importer et synchroniser et vérifier) jusqu'à ce que les modifications qui doivent être exportés sont prévues.

**Présentation du fichier export.csv**

La plupart du fichier est explicite. Certaines abréviations à comprendre le contenu :

- OMODT – Type de Modification d’objet. Indique si l’opération au niveau de l’objet est d’ajout, la mise à jour ou la supprimer.
- AMODT – Type de Modification attribut. Indique si l’opération au niveau de l’attribut est d’ajout, la mise à jour ou la supprimer.

Si la valeur de l’attribut à valeurs multiples, pas toutes les modifications sont affiche. Uniquement le nombre de valeurs ajoutés ou supprimés est visible.

#### <a name="switch-active-server"></a>Serveur actif commutateur

1. Sur le serveur actif, désactiver le serveur (DirSync/FIM/Azure AD Sync) afin qu’il n’est pas l’exportation vers Azure Active Directory ou définir dans les intermédiaires mode (Azure AD Connect).
2. Exécutez l’Assistant installation sur le serveur dans **le mode de mise en attente** et désactiver **le mode de mise en attente**.
![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>Récupération d’urgence
Partie de la conception de l’implémentation consiste à prévoir la procédure à suivre en cas de sinistre dans lequel vous perdez le serveur de synchronisation. Il existe différents modèles à utiliser et celle à utiliser dépend de plusieurs facteurs, notamment :

-   Quelle est votre tolérance pour n’est pas en mesure d’apporter des modifications aux objets dans Azure AD pendant l’interruption ?
-   Si vous utilisez la synchronisation de mot de passe, les utilisateurs accepter qu’ils aient à utiliser l’ancien mot de passe dans Azure AD au cas où ils modifiez-le en local ?
-   Vous avez une dépendance sur les opérations en temps réel, telles que votre mot de passe d’écriture différée ?

Selon les réponses à ces questions et stratégie de votre organisation, une des stratégies suivantes peut être implémentée :

-   Reconstruire lorsque cela est nécessaire.
-   Disposer d’un serveur de secours rechange, connu sous le **mode de mise en attente**.
-   Utiliser des machines virtuelles.

Si vous n’utilisez pas la base de données SQL Express intégrée, vous devez également vérifier la section [Disponibilité SQL](#sql-high-availability) .

### <a name="rebuild-when-needed"></a>Reconstruire lorsque cela est nécessaire
Une stratégie viable consiste à planifier une reconstruction de serveur lorsque cela est nécessaire. En règle générale, installez le moteur de synchronisation, puis effectuez que l’importation initiale et la synchronisation peuvent en effectuer quelques heures. Si il n’est pas un serveur de réserve disponible, il est possible d’utiliser temporairement un contrôleur de domaine pour héberger le moteur de synchronisation.

Le serveur de moteur de synchronisation ne stocke pas tout état sur les objets afin que la base de données peut être reconstruit à partir des données dans Active Directory et Azure AD. L’attribut **sourceAnchor** permet de joindre les objets en local et le nuage. Si vous reconstruisez le serveur avec existant objets en local et le cloud, puis le moteur de synchronisation correspond à ces objets ensemble à nouveau sur la réinstallation. Les choses à documenter et enregistrer sont les modifications apportées au serveur, telles que des règles de filtrage et de synchronisation configuration. Ces configurations personnalisées doivent être appliquée à nouveau avant de commencer la synchronisation.

### <a name="have-a-spare-standby-server---staging-mode"></a>Disposer d’un serveur de secours rechange - mode de mise en attente
Si vous disposez d’un environnement plus complexe, il est recommandé de puis ayant une ou plusieurs des serveurs en attente. Pendant l’installation, vous pouvez activer un serveur en **mode de mise en attente**.

Pour plus d’informations, voir [le mode de test](#staging-mode).

### <a name="use-virtual-machines"></a>Utiliser des machines virtuelles
Une méthode courantes et pris en charge consiste à exécuter le moteur de synchronisation dans une machine virtuelle. Au cas où l’hôte rencontre un problème, l’image avec le serveur de moteur de synchronisation peut être migré vers un autre serveur.

### <a name="sql-high-availability"></a>Disponibilité SQL
Si vous n’utilisez pas SQL Server Express qui est fourni avec Azure AD Connect, haute disponibilité pour SQL Server doit également être considérée. La solution de disponibilité uniquement pris en charge est SQL cluster. Les solutions non prises en charge inclure la mise en miroir et toujours sur.

## <a name="next-steps"></a>Étapes suivantes

**Rubriques de présentation**  

- [Azure AD Connect synchronisation : comprendre et personnaliser la synchronisation](active-directory-aadconnectsync-whatis.md)  
- [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)  
