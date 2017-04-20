<properties
   pageTitle="Logique d’applications en local connexion de données de passerelle | Microsoft Azure"
   description="Informations sur la façon de créer une connexion à la passerelle de données locale à partir d’une application logique."
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# <a name="connect-to-the-on-premises-data-gateway-for-logic-apps"></a>Se connecter à la passerelle de données locale pour les applications de logique

Logique pris en charge les connecteurs applications permettent de configurer votre connexion pour accéder aux données de local via la passerelle de données locale.  Les étapes suivantes vous guidera tout au long de l’installation et configuration de la passerelle de données locale pour l’utiliser avec une application logique.

## <a name="prerequisites"></a>Conditions préalables

* Vous devez être un volume de travail ou scolaire adresse de messagerie dans Azure à associer la passerelle de données locale à votre compte (compte Azure Active Directory en fonction)
    * Si vous utilisez un Account Microsoft (par exemple, @outlook.com, @live.com) vous pouvez utiliser votre compte Azure pour créer un professionnel ou scolaire adresse de messagerie en [suivant les étapes décrites ici](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)

> [AZURE.WARNING] Il existe actuellement une limitation qui local installation gateway ne se termine que lorsque vous utilisez un compte qui a été enregistré avec Power BI.  En attendant, enregistrez n’importe quel compte avec « Power BI gratuit » pour terminer l’installation avec succès.

* Doit avoir le local données passerelle [installée sur un ordinateur local](app-service-logic-gateway-install.md).
* Passerelle ne doit pas avoir été émanant d’une autre passerelle de données Azure locaux ([réclamer se passe-t-il avec la création de l’étape 2 ci-dessous](#2-create-an-azure-on-premises-data-gateway-resource)) : une installation peut uniquement être associée à une ressource d’une passerelle.

## <a name="installing-and-configuring-the-connection"></a>Installation et configuration de la connexion

### <a name="1-install-the-on-premises-data-gateway"></a>1. Installez la passerelle de données locale

Vous trouverez des informations sur l’installation de la passerelle de données locales [dans cet article](app-service-logic-gateway-install.md).  La passerelle doit être installée sur un ordinateur local avant de pouvoir continuer avec le reste des étapes.

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2. création d’une ressource de passerelle de données Azure en local

Une fois installée, vous devez associer votre abonnement Azure à la passerelle de données locale.

1. Connectez-vous à Azure à l’aide de la même travail ou l’adresse de messagerie de l’école a été utilisée pendant l’installation de la passerelle
1. Cliquez sur le bouton de la ressource de **Nouveau**
1. Rechercher et sélectionner la **passerelle de données locale**
1. Complétez les informations pour associer la passerelle à votre compte - notamment en sélectionnant le **Nom de l’Installation**

    ![Connexion de passerelle de données locale][1]
1. Cliquez sur le bouton **créer** pour créer la ressource

### <a name="3-create-a-logic-app-connection-in-the-designer"></a>3. créer une connexion de l’application logique dans le Concepteur

À présent que votre abonnement Azure est associé à une instance de la passerelle de données locale, vous pouvez créer une connexion pour pouvoir bénéficier au sein d’une application logique.

1. Ouvrez une application logique, puis sélectionnez un connecteur qui prend en charge la connectivité locaux (à ce jour, SQL Server)
1. Activez la case à cocher pour que la **connexion via une passerelle de données locale**

    ![Création d’une logique Application Designer passerelle][2]
1. Sélectionnez la **passerelle** pour se connecter à et procéder à d’autres informations de connexion requises
1. Cliquez sur **créer** pour créer la connexion

La connexion doit maintenant être configurée correctement pour une utilisation dans votre application logique.  

## <a name="next-steps"></a>Étapes suivantes
- [Exemples et des scénarios d’applications logique courants](app-service-logic-examples-and-scenarios.md)
- [Fonctionnalités d’intégration entreprise](app-service-logic-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/app-service-logic-gateway-connection/createblade.PNG
[2]: ./media/app-service-logic-gateway-connection/blankconnection.PNG
[3]: ./media/app-service-logic-gateway-connection/checkbox.PNG