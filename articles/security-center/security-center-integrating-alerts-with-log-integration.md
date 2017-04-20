<properties
   pageTitle="Intégration des alertes du centre de sécurité Azure avec l’intégration de journal Azure (Preview) | Microsoft Azure"
   description="Cet article vous permet de commencer à utiliser l’intégration des alertes du centre de sécurité avec l’intégration de journal Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/08/2016"
   ms.author="terrylan"/>

# <a name="integrating-security-center-alerts-with-azure-log-integration-preview"></a>Intégration alertes du centre de sécurité de l’intégration de journal Azure (Preview)

Nombreuses opérations de sécurité et permet aux équipes de réponse à un incident s’appuient sur une solution les informations de sécurité et de gestion des événements (SIEM) en tant que point de départ pour hiérarchiser et analyse des alertes de sécurité. Avec l’intégration des journaux Azure clients peuvent synchroniser alertes du centre de sécurité Azure, ainsi que des événements de sécurité machine virtuelle collectées par Diagnostics Azure et Azure les journaux d’Audit, avec leurs journal analytique ou solution SIEM en temps quasi réel.

Intégration du journal Azure fonctionne avec HP ArcSight, Splunk, IBM QRadar et d’autres personnes.

## <a name="what-logs-can-i-integrate"></a>Quels journaux puis-je intégrer ?

Azure génère journalisation complète pour chaque service. Ces fichiers journaux classés comme :

- **Journaux de contrôle/gestion** qui permettent aux activités Azure Gestionnaire de ressources créer, mettre à jour et supprimer.
- **Les journaux de plan de données** qui donnent une visibilité sur les événements déclenchés lors de l’utilisation d’une ressource Azure. Un exemple est le journal des événements Windows - sécurité et ouvre une application sur une machine virtuelle.

Intégration du journal Azure prend actuellement en charge l’intégration de :

- Journaux machine virtuelle Azure
- Journaux d’Audit Azure
- Alertes du centre de sécurité Azure

## <a name="install-azure-log-integration"></a>Installer l’intégration journal Azure

Téléchargez [Azure s’intégration](https://www.microsoft.com/download/details.aspx?id=53324).

Le service d’intégration journal Azure collecte des données de télémétrie à partir de l’ordinateur sur lequel il est installé.  Données de télémétrie recueillies sont la suivante :

- Exceptions qui se produisent pendant l’exécution de l’intégration de journal Azure
- Indicateurs sur le nombre de requêtes et événements traités
- Statistiques sur les Azlog.exe Utilisation des options de ligne de commande

> [AZURE.NOTE] Vous pouvez désactiver la collecte de données de télémétrie en désactivant cette option.

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Intégrer des journaux d’Audit Azure et le centre de sécurité des alertes

1. Ouvrez l’invite de commandes et le **cd** dans **c:\Program Files\Microsoft Azure journal d’intégration**.

2. Exécutez la commande **azlog createazureid** pour créer une [Entité du Service Azure Active Directory](../active-directory/active-directory-application-objects.md) dans les clients Azure Active Directory (AD) qui hébergent les abonnements Azure.

    Vous demandera votre nom d’utilisateur Azure.

    > [AZURE.NOTE] Vous devez être le propriétaire de l’abonnement ou administrateur de co-création de l’abonnement.

    L’authentification Azure s’effectue via Azure AD.  Création d’un principal de service d’intégration du journal Azure créera l’identité Azure AD qui sera accordée l’accès en lecture à partir des abonnements Azure.

3. Exécuter le **azlog autoriser <SubscriptionID> ** commande pour attribuer des accès en lecture de l’abonnement à l’entité de service créée à l’étape 2. Si vous ne spécifiez pas une **SubscriptionID**, puis l’entité de service sont attribuée au rôle de lecteur à tous les abonnements auxquels vous avez accès.

    > [AZURE.NOTE] Vous pouvez afficher les alertes si vous exécutez la commande **Autoriser** immédiatement après la commande **createazureid** , car il existe une latence entre lorsque le compte Azure AD est créé et lorsque le compte est disponible pour une utilisation. Si vous attendez environ 10 secondes après l’exécution de la commande **createazureid** pour exécuter la commande **Autoriser** , vous ne devriez pas voir ces avertissements.

4. Vérifier les dossiers suivants pour confirmer que les fichiers JSON du journal d’Audit sont :

  - **c:\Users\azlog\AzureResourceManagerJson**
  - **c:\Users\azlog\AzureResourceManagerJsonLD**

5. Vérifier pour confirmer que les alertes du centre de sécurité existent dans les dossiers suivants :

  - **c:\Users\azlog\ AzureSecurityCenterJson**
  - **c:\Users\azlog\AzureSecurityCenterJsonLD**

6. Pointez sur le lien de redirecteurs standard SIEM fichier vers le dossier approprié pour copier les données à l’instance SIEM. Reportez-vous à [configurations SIEM](https://azsiempublicdrops.blob.core.windows.net/drops/ALL.htm) sur la configuration SIEM.

Si vous avez des questions sur l’intégration de journal Azure, envoyez un message électronique à [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les journaux d’Audit de Azure et les définitions de propriétés, voir :

- [Opérations d’audit avec le Gestionnaire de ressources](../resource-group-audit.md)
- [Répertorier les événements de gestion dans un abonnement](https://msdn.microsoft.com/library/azure/dn931934.aspx) - pour récupérer les événements du journal d’audit.

Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md) : Forum aux questions sur l’utilisation du service de recherche.
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) — obtenir les dernières informations de sécurité Azure et des informations.
