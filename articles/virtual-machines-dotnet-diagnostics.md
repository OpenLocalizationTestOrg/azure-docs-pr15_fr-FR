<properties
    pageTitle="Comment utiliser les diagnostics de Windows Azure dans des Machines virtuelles | Microsoft Azure"
    description="À l’aide des diagnostics de Windows Azure pour collecter des données à partir d’ordinateurs virtuelle Azure débogage, de mesurer les performances, surveillance, l’analyse du trafic et plus."
    services="virtual-machines"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="virtual-machines"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/20/2016"
    ms.author="robb"/>



# <a name="enabling-diagnostics-in-azure-virtual-machines"></a>Activer les Diagnostics dans Azure Machines virtuelles

Voir [Vue d’ensemble des Diagnostics Azure](azure-diagnostics.md) pour un arrière-plan sur Azure Diagnostics.

## <a name="how-to-enable-diagnostics-in-a-virtual-machine"></a>Comment activer les Diagnostics sur une Machine virtuelle

Cette procédure pas à pas décrit comment installer à distance des Diagnostics pour une machine virtuelle Azure à partir d’un ordinateur de développement. Vous découvrez également comment mettre en œuvre une application qui s’exécute sur cet ordinateur virtuel Azure et émet des données de télémétrie à l’aide de la [Classe source d’événement][]. Diagnostics de Windows Azure est utilisée pour recueillir la télémétrie et stockez-le dans un compte de stockage Azure.

### <a name="pre-requisites"></a>Conditions préalables
Cette procédure suppose que vous avez souscrit un abonnement Azure et utilisez Visual Studio 2013 avec le Kit de développement Azure. Si vous ne disposez pas d’un abonnement Azure, vous pouvez vous inscrire pour [Version d’évaluation gratuite][]. Veillez à [installer et configurer Azure PowerShell version 0.8.7 ou version ultérieure][].

### <a name="step-1-create-a-virtual-machine"></a>Étape 1 : Créer une Machine virtuelle
1.  Sur votre ordinateur de développement, lancez Visual Studio 2013.
2.  Dans **l’Explorateur de serveurs** Visual Studio développez **Azure**, avec le bouton droit **Machines virtuelles** , puis sélectionnez **créer une Machine virtuelle**.
3.  Sélectionnez votre abonnement Azure dans la boîte de dialogue **Choisir un abonnement** , puis cliquez sur **suivant**.
4.  Sélectionnez **Windows Server 2012 R2 Datacenter, novembre 2014** dans la boîte de dialogue **Sélectionner une Image de Machine virtuelle** et cliquez sur **suivant**.
5.  Dans les **Paramètres de base Machine virtuelle**, définissez le nom de la machine virtuelle sur « wadexample ». Définissez votre nom d’utilisateur administrateur et le mot de passe, puis cliquez sur **suivant**.
6.  Dans la boîte de dialogue **Paramètres du Service Cloud** créer un nouveau service cloud nommé « wadexampleVM ». Créer un nouveau compte de stockage nommé « wadexample » et cliquez sur **suivant**.
7.  Cliquez sur **créer**.

### <a name="step-2-create-your-application"></a>Étape 2 : Créer votre Application
1.  Sur votre ordinateur de développement, lancez Visual Studio 2013.
2.  Créer une nouvelle Application Visual c# Console qui cible .NET Framework 4.5. Nommez le projet « WadExampleVM ».
    ![CloudServices_diag_new_project](./media/virtual-machines-dotnet-diagnostics/NewProject.png)
3.  Remplacez le contenu de Program.cs par le code suivant. La classe **SampleEventSourceWriter** mettent en œuvre, quatre méthodes d’enregistrement : **SendEnums**, **MessageMethod**, **SetOther** et **HighFreq**. Le premier paramètre de la méthode WriteEvent définit l’ID de l’événement respective. La méthode Run met en œuvre une boucle qui appelle chacune des méthodes de journalisation implémentées dans la classe **SampleEventSourceWriter** toutes les 10 secondes.

        using System;
        using System.Diagnostics;
        using System.Diagnostics.Tracing;
        using System.Threading;

        namespace WadExampleVM
        {
        sealed class SampleEventSourceWriter : EventSource
        {
            public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
            public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
            public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
            public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
            public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

        }

        enum MyColor
        {
            Red,
            Blue,
            Green
        }

        [Flags]
        enum MyFlags
        {
            Flag1 = 1,
            Flag2 = 2,
            Flag3 = 4
        }

        class Program
        {
        static void Main(string[] args)
        {
            Trace.TraceInformation("My application entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }

        }
        }
        }


4.  Enregistrez le fichier et sélectionnez **Générer la Solution** dans le menu **Générer** pour générer votre code.


### <a name="step-3-deploy-your-application"></a>Étape 3 : Déployer votre Application
1.  Avec le bouton droit sur le projet **WadExampleVM** dans **L’Explorateur de solutions** , puis sélectionnez **Ouvrir le dossier dans l’Explorateur de fichiers**.
2.  Accédez au dossier *bin\Debug* et copiez tous les fichiers (WadExampleVM.*)
3.  Dans **L’Explorateur de serveur** avec le bouton droit sur l’ordinateur virtuel, puis cliquez sur **se connecter à l’aide de bureau à distance**.
4.  Une fois connecté à la machine virtuelle créer un dossier nommé WadExampleVM et coller votre application des fichiers dans le dossier.
5.  Lancez l’application WadExampleVM.exe. Vous devriez voir une fenêtre de console vide.

### <a name="step-4-create-your-diagnostics-configuration-and-install-the-extension"></a>Étape 4 : Créer votre configuration de Diagnostics et installez l’Extension
1.  Téléchargez la définition de schéma de fichier de configuration public sur votre ordinateur de développement en exécutant la commande PowerShell suivante :

        (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'

2.  Ouvrez un nouveau fichier XML dans Visual Studio, soit dans un projet ou est déjà ouvert dans un Visual Studio instance avec aucune projets ouverts. Dans Visual Studio, sélectionnez **Ajouter** -> **Un nouvel élément...**  ->  **Éléments visual c#** -> **données** -> **Fichier XML**. Nommez le fichier « WadExample.xml »
3.  Associer le WadConfig.xsd le fichier de configuration. Vérifiez que la fenêtre de l’éditeur WadExample.xml est la fenêtre active. Appuyez sur **F4** pour ouvrir la fenêtre de **Propriétés** . Cliquez sur la propriété de **schémas** dans la fenêtre **Propriétés** . Cliquez sur le bouton **...** dans la propriété **schémas** . Cliquez sur l' **Ajouter...** bouton et accédez à l’emplacement où vous avez enregistré le fichier XSD et sélectionnez le fichier WadConfig.xsd. Cliquez sur **OK**.
4.  Remplacez le contenu du fichier de configuration WadExample.xml par le code XML suivant et enregistrez le fichier. Ce fichier de configuration définit quelques compteurs de performance pour recueillir : une pour l’utilisation du processeur et une pour l’utilisation de la mémoire. Puis la configuration définit les quatre événements correspondant aux méthodes de la classe SampleEventSourceWriter.

```
        <?xml version="1.0" encoding="utf-8"?>
        <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
            <WadCfg>
                <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
                <PerformanceCounters scheduledTransferPeriod="PT1M">
                    <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
                    <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
                    </PerformanceCounters>
                    <EtwProviders>
                        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
                            <Event id="1" eventDestination="EnumsTable"/>
                            <Event id="2" eventDestination="MessageTable"/>
                            <Event id="3" eventDestination="SetOtherTable"/>
                            <Event id="4" eventDestination="HighFreqTable"/>
                            <DefaultEvents eventDestination="DefaultTable" />
                        </EtwEventSourceProviderConfiguration>
                    </EtwProviders>
                </DiagnosticMonitorConfiguration>
            </WadCfg>
        </PublicConfig>
```

### <a name="step-5-remotely-install-diagnostics-on-your-azure-virtual-machine"></a>Étape 5 : Installer à distance Diagnostics sur votre Machine virtuelle Azure
Les applets de commande PowerShell pour la gestion des Diagnostics sur un ordinateur virtuel sont : Set-AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension et supprimer AzureVMDiagnosticsExtension.

1.  Sur votre ordinateur pour les développeurs, ouvrez la session PowerShell Azure.
2.  Exécuter le script pour installer à distance des Diagnostics sur votre ordinateur virtuel (remplacer *StorageAccountKey* avec la clé de compte de stockage pour votre compte de stockage wadexamplevm) :

        $storage_name = "wadexamplevm"
        $key = "<StorageAccountKey>"
        $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
        $service_name="wadexamplevm"
        $vm_name="WadExample"
        $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
        $VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
        $VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
        $VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM


### <a name="step-6-look-at-your-telemetry-data"></a>Étape 6 : Aperçu de vos données de télémétrie
Dans **l’Explorateur de serveurs** Visual Studio naviguez vers le compte de stockage wadexample. Une fois que la machine virtuelle a été exécuté environ 5 minutes, vous devriez voir les tables **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** et **WADSetOtherTable**. Double-cliquez sur l’une des tables pour afficher la télémétrie qui ont été collectée.

![CloudServices_diag_wadexamplevm_tables](./media/virtual-machines-dotnet-diagnostics/WadExampleVMTables.png)

## <a name="configuration-file-schema"></a>Schéma de fichier de configuration

Le fichier de configuration Diagnostics définit des valeurs qui sont utilisées pour initialisation les paramètres de configuration de diagnostic au démarrage de l’agent de diagnostics. Consultez la [dernière référence de schéma](https://msdn.microsoft.com/library/azure/mt634524.aspx) pour les valeurs valides et des exemples.

## <a name="troubleshooting"></a>Résolution des problèmes

Pour plus d’informations, consultez [Résolution des problèmes de diagnostic Azure](azure-diagnostics-troubleshooting.md) .


## <a name="next-steps"></a>Étapes suivantes
[Pour afficher la liste de machine virtuelle-Diagnostics de Windows Azure articles connexes](azure-diagnostics.md#virtual-machines-using-azure-diagnostics) pour modifier les données que vous collectez, résoudre les problèmes ou en savoir plus sur les diagnostics en général.


[Cours de la source d’événement]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Version d’évaluation gratuite]: http://azure.microsoft.com/pricing/free-trial/
[Installer et configurer Azure PowerShell version 0.8.7 ou version ultérieure]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
