<properties
    pageTitle="Résolution des problèmes de Diagnostics de Windows Azure"
    description="Résoudre les problèmes lors de l’utilisation des diagnostics de Windows Azure dans Azure Cloud Services, Machines virtuelles et "
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="robb"/>


# <a name="azure-diagnostics-troubleshooting"></a>Résolution des problèmes de Diagnostics de Windows Azure
Dépannage des informations relatives à l’aide des Diagnostics Azure. Pour plus d’informations sur les diagnostics de Windows Azure, voir [Vue d’ensemble des Diagnostics Azure](azure-diagnostics.md#cloud-services).

## <a name="azure-diagnostics-is-not-starting"></a>Diagnostics de Windows Azure ne démarre pas

Diagnostics se compose de deux composants : un plug-in de l’agent invité et l’agent de surveillance. Vous pouvez vérifier les fichiers journaux **DiagnosticsPluginLauncher.log** et **DiagnosticsPlugin.log** pour plus d’informations sur pourquoi diagnostics ne parvient pas à démarrer.  
  
Dans un rôle Service Cloud, les fichiers journaux pour le plug-in de l’agent invité se trouvent dans : 

``` 
C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.6.3.0\ 
``` 

Dans une Machine virtuelle Azure, les fichiers journaux pour le plug-in de l’agent invité sont situés dans : 

``` 
C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.6.3.0\Logs\ 
``` 
 
La dernière ligne des fichiers journaux contiendra le code de sortie.  

``` 
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0 
``` 

Le plug-in renvoie les codes de sortie suivants :

Code de sortie|Description
---|---
0|Opération réussie.
-1|Erreur générique.
-2|Impossible de charger le fichier rcf.<p>Il s’agit d’une erreur interne qui doit avoir lieu uniquement si le Lanceur de plug-in agent invité est appelé manuellement, incorrectement, sur l’ordinateur virtuel.
-3|Impossible de charger le fichier de configuration de Diagnostics.<p><p>Solution : Il s’agit le résultat d’un fichier de configuration ne passe pas la validation de schéma. La solution consiste à fournir un fichier de configuration est conforme avec le schéma.
-4|Une autre instance de l’agent de surveillance Diagnostics se sert déjà de l’annuaire de ressources locales.<p><p>Solution : Spécifiez une valeur différente pour **LocalResourceDirectory**.
-6|Le Lanceur de plug-in invité agent essayé de lancer des Diagnostics avec une ligne de commande non valide.<p><p>Il s’agit d’une erreur interne qui doit avoir lieu uniquement si le Lanceur de plug-in agent invité est appelé manuellement, incorrectement, sur l’ordinateur virtuel.
-10|Le plug-in Diagnostics fermé avec une exception non gérée.
-11|L’agent invité n’a pas pu créer le processus responsable de lancement et contrôle de l’agent de surveillance.<p><p>Solution : Vérifiez que les ressources système suffisantes sont disponibles pour lancer le processus de nouveau.<p>
-101|Arguments non valides lors de l’appel le plug-in Diagnostics.<p><p>Il s’agit d’une erreur interne qui doit avoir lieu uniquement si le Lanceur de plug-in agent invité est appelé manuellement, incorrectement, sur l’ordinateur virtuel.
-102|Le processus de plug-in ne peut pas initialisation elle-même.<p><p>Solution : Vérifiez que les ressources système suffisantes sont disponibles pour lancer le processus de nouveau.
-103|Le processus de plug-in ne peut pas initialisation elle-même. En particulier, il est impossible de créer l’objet de journal.<p><p>Solution : Vérifiez que les ressources système suffisantes sont disponibles pour lancer le processus de nouveau.
-104|Impossible de charger le fichier rcf fourni par l’agent invité.<p><p>Il s’agit d’une erreur interne qui doit avoir lieu uniquement si le Lanceur de plug-in agent invité est appelé manuellement, incorrectement, sur l’ordinateur virtuel.
-105|Le plug-in Diagnostics Impossible d’ouvrir le fichier de configuration de Diagnostics.<p><p>Il s’agit d’une erreur interne qui doit avoir lieu uniquement si le plug-in Diagnostics est appelé manuellement, incorrectement, sur l’ordinateur virtuel.
-106|Impossible de lire le fichier de configuration de Diagnostics.<p><p>Solution : Il s’agit le résultat d’un fichier de configuration ne passe pas la validation de schéma. La solution consiste à fournir un fichier de configuration est conforme avec le schéma. Vous pouvez trouver le code XML qui a remis à l’extension Diagnostics dans le dossier *%SystemDrive%\WindowsAzure\Config* sur l’ordinateur virtuel. Ouvrez le fichier XML et la recherche pour **Microsoft.Azure.Diagnostics**, puis pour le champ **xmlCfg** appropriée. Les données sont en base 64 codé afin que vous devez [décoder](http://www.bing.com/search?q=base64+decoder) afficher le code XML qui a été chargé par Diagnostics.<p>
-107|La réussite répertoire ressources de l’agent de surveillance n’est pas valide.<p><p>Il s’agit d’une erreur interne qui doit avoir lieu uniquement si l’agent de surveillance est appelée manuellement, incorrectement, sur l’ordinateur virtuel.</p>
-108    |Impossible de convertir le fichier de configuration de Diagnostics dans le fichier de configuration de l’agent surveillance.<p><p>Il s’agit d’une erreur interne qui doit avoir lieu uniquement si le plug-in Diagnostics est appelé manuellement avec un fichier de configuration non valides.
-110|Erreur de configuration de général Diagnostics.<p><p>Il s’agit d’une erreur interne qui doit avoir lieu uniquement si le plug-in Diagnostics est appelé manuellement avec un fichier de configuration non valides.
-111|Impossible de démarrer l’agent de surveillance.<p><p>Solution : Vérifiez que les ressources système nécessaires sont disponibles.
-112|Erreur générale


## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Données de diagnostic sont pas connecté au stockage Azure
Diagnostics de Windows Azure stocke toutes les données dans le stockage Azure.

La cause la plus courante d’événement données manquantes est informations de compte de stockage défini de manière incorrecte.

Solution : Corrigez votre fichier de configuration de Diagnostics et réinstaller Diagnostics.
Si le problème persiste après la réinstallation l’extension diagnostics puis vous devrez peut-être déboguer plus en consultant les des erreurs de l’agent de surveillance. Avant que les données d’événement sont téléchargées à votre compte de stockage, il est stocké dans le LocalResourceDirectory.

Rôle de Service Cloud le LocalResourceDirectory est la suivante :

    C:\Resources\Directory\<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

Pour les Machines virtuelles du LocalResourceDirectory est la suivante :

    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

Si aucun fichier dans le dossier LocalResourceDirectory, l’agent de surveillance ne peut pas lancer. Ceci est généralement provoquée par un fichier de configuration non valide, un événement qui doit être indiquée dans la CommandExecution.log.

Si l’Agent de surveillance parvient à collecter les données d’événement, vous verrez les fichiers .tsf pour chaque événement défini dans votre fichier de configuration. L’Agent de surveillance enregistre ses erreurs dans le fichier MaEventTable.tsf. Pour examiner le contenu de ce fichier, vous pouvez utiliser l’application tabel2csv pour convertir le fichier .tsf dans un fichier de values(.csv) séparées par des virgules :

Sur un rôle de Service Cloud :

    %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

*%Systemdrive%* sur un rôle de Service Cloud est généralement d :

Sur une Machine virtuelle :

    C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

Les commandes ci-dessus génère le fichier journal *maeventtable.csv*que vous pouvez ouvrir et inspecter les messages d’échec.    


## <a name="diagnostics-data-tables-not-found"></a>Données de diagnostic Tables introuvables
Les tables dans le stockage Azure contenant les données de diagnostics de Windows Azure sont nommés en utilisant le code ci-dessous :

        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;

Voici un exemple :

        <EtwEventSourceProviderConfiguration provider=”prov1”>
          <Event id=”1” />
          <Event id=”2” eventDestination=”dest1” />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider=”prov2”>
          <DefaultEvents eventDestination=”dest2” />
        </EtwEventSourceProviderConfiguration>

Qui générera 4 tables :

Événement|Nom de la table
---|---
fournisseur = « prov1 » &lt;id de l’événement = « 1 » /&gt;|WADEvent + MD5("prov1") + « 1 »
fournisseur = « prov1 » &lt;id de l’événement = eventDestination « 2 » = « dest1 » /&gt;|WADdest1
fournisseur = « prov1 » &lt;DefaultEvents /&gt;|WADDefault+MD5("prov1")
fournisseur = « prov2 » &lt;DefaultEvents eventDestination = « dest2 » /&gt;|WADdest2
