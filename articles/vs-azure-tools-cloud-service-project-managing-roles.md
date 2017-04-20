<properties
   pageTitle="Gestion des rôles dans le nuage Azure services projets avec Visual Studio | Microsoft Azure"
   description="Découvrez comment ajouter de nouveaux rôles à votre projet de service cloud Azure ou supprimer des rôles existants à partir de celle-ci à l’aide de Visual Studio."
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

# <a name="managing-roles-in-the-azure-cloud-services-projects-with-visual-studio"></a>Gestion des rôles dans les projets de services de cloud Azure avec Visual Studio

Après avoir créé votre projet de service cloud Azure, vous pouvez ajouter de nouveaux rôles à celui-ci ou supprimer des rôles existants à partir de celui-ci. Vous pouvez également importer un projet existant et convertissez-le à un rôle. Par exemple, vous pouvez importer une application web ASP.NET et désigner comme un rôle web.

## <a name="adding-or-removing-roles"></a>Ajout ou suppression d’un rôle

**Pour ajouter un rôle**

Dans l' **Explorateur de solutions**, ouvrez le menu contextuel pour le nœud **rôles** dans votre projet de service cloud et cliquez sur **Ajouter**. Vous pouvez sélectionner un rôle web existant ou travail à partir de la solution actuelle ou créer un nouveau projet de rôle web ou de travail. Ou vous pouvez sélectionner un projet approprié, par exemple un projet d’application web ASP.NET et l’associer à un projet de rôle.

**Pour supprimer une association de rôle**

Dans le nœud **rôles** du projet de service cloud dans l’Explorateur de solutions, ouvrez le menu contextuel pour le rôle à supprimer, puis cliquez sur **Supprimer**.

## <a name="removing-and-adding-roles-in-your-cloud-service"></a>Suppression et ajout de rôles dans votre service de nuage

Si vous supprimez un rôle à partir de votre projet de service cloud mais décidez ensuite d’ajouter le rôle dans le projet, uniquement la déclaration de rôle et les attributs de base, telles que les informations de points de terminaison et les diagnostics, sont ajoutées. Aucune des ressources supplémentaires ou des références ne sont ajoutés au fichier ServiceDefinition.csdef ou le fichier ServiceConfiguration.cscfg. Si vous voulez ajouter ces informations, vous devrez ajouter manuellement à nouveau à ces fichiers.

Par exemple, vous pouvez supprimer un rôle de service web et que vous décidez d’ajouter ce rôle dans votre solution. Si vous procédez ainsi, une erreur est générée. Pour éviter cette erreur, vous devez ajouter la `<LocalResources>` élément affiché dans le code XML suivant dans le fichier ServiceDefinition.csdef. Utilisez le nom du rôle de service web que vous avez ajouté dans le projet dans le cadre de l’attribut de nom pour le **<LocalStorage>** élément. Dans cet exemple, le nom du rôle de service web est **WCFServiceWebRole1**.

    <WebRole name="WCFServiceWebRole1">
        <Sites>
          <Site name="Web">
            <Bindings>
              <Binding name="Endpoint1" endpointName="Endpoint1" />
            </Bindings>
          </Site>
        </Sites>
        <Endpoints>
          <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
          <Import moduleName="Diagnostics" />
        </Imports>
       <LocalResources>
          <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
       </LocalResources>
    </WebRole>

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment configurer des rôles dans Visual Studio en lisant l’article [configurer les rôles d’un Service Cloud Azure avec Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).
