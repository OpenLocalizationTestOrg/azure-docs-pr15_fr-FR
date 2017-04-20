<properties
    pageTitle="Activer le débogage distant avec remise continue | Microsoft Azure"
    description="Apprenez à activer le débogage distant lors de l’utilisation de la remise continue à déployer Azure"
    services="cloud-services"
    documentationCenter=".net"
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="enable-remote-debugging-when-using-continuous-delivery-to-publish-to-azure"></a>Activer le débogage distant lors de l’utilisation de la remise continue à publier sur Azure

Vous pouvez activer le débogage distant dans Azure, pour les services en nuage ou machines virtuelles, lorsque vous utilisez [remise continue](cloud-services-dotnet-continuous-delivery.md) à publier sur Azure en procédant comme suit.

## <a name="enabling-remote-debugging-for-cloud-services"></a>L’activation du débogage à distance des services en nuage

1. Sur l’agent de build, configurer l’environnement initial pour Azure comme indiqué dans la zone [De ligne de commande génération pour Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. Étant donné que l’exécution du débogage distant (msvsmon.exe) est requise pour le package, installer les [Outils à distance pour Visual Studio 2015](http://www.microsoft.com/en-us/download/details.aspx?id=48155) (ou les [Outils à distance pour Microsoft Visual Studio 2013 mise à jour 5](https://www.microsoft.com/en-us/download/details.aspx?id=48156) si vous utilisez Visual Studio 2013). Comme alternative, vous pouvez copier les fichiers binaires de débogage à distance à partir d’un système qui a installé Visual Studio.
3. Créer un certificat comme indiqué dans la [Vue d’ensemble des certificats pour les Services en nuage Azure](cloud-services-certs-create.md). Conserver la .pfx et l’empreinte numérique du certificat RDP et téléchargez le certificat au service de nuage cible.
4. Utilisez les options suivantes dans la ligne de commande MSBuild pour générer et empaqueter avec débogage distant activé. (Substitue réels chemins d’accès à vos fichiers système et project pour les éléments mis entre crochets angle.)

        msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"

    `VSX64RemoteDebuggerPath`représente le chemin d’accès au dossier contenant msvsmon.exe dans les outils à distance pour Visual Studio.
    `RemoteDebuggerConnectorVersion`est la version Azure SDK dans votre service cloud. Il doit également correspondre à la version installée avec Visual Studio.

5. Publier sur le service en nuage cible en utilisant le fichier de package et .cscfg généré à l’étape précédente.
6. Importer le certificat (fichier .pfx) sur l’ordinateur sur lequel Visual Studio avec Azure SDK pour .NET est installé. Veillez à importer dans la `CurrentUser\My` magasin de certificats, dans le cas contraire en joignant au débogueur dans Visual Studio échouera.

## <a name="enabling-remote-debugging-for-virtual-machines"></a>L’activation du débogage à distance des machines virtuelles

1. Créer une machine virtuelle Azure. Voir [créer une Machine virtuelle exécutant Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ou [créer et gérer des Machines virtuelles Azure dans Visual Studio](../virtual-machines/virtual-machines-windows-classic-manage-visual-studio.md).
2. Dans la [page du portail classique Azure](http://go.microsoft.com/fwlink/p/?LinkID=269851), affichage tableau de bord de la machine virtuelle pour voir la machine virtuelle **Empreinte de certificat RDP**. Cette valeur est utilisée pour la `ServerThumbprint` valeur dans la configuration de l’extension.
3. Créer un certificat client, comme indiqué dans la [Vue d’ensemble des certificats pour les Services en nuage Azure](cloud-services-certs-create.md) (laisser la .pfx et l’empreinte numérique du certificat RDP).
4. Installer Azure Powershell (version 0.7.4 ou version ultérieure) comme indiqué dans [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).
5. Exécutez le script suivant pour activer l’extension RemoteDebug. Remplacez les chemins d’accès et les données personnelles par votre propre, tels que votre nom de l’abonnement, le nom du service et l’empreinte numérique.

    >[AZURE.NOTE] Ce script est configuré pour Visual Studio 2015. Si vous utilisez Visual Studio 2013, modifiez la `$referenceName` et `$extensionName` ci-dessous pour utiliser les affectations `RemoteDebugVS2013` (au lieu de `RemoteDebugVS2015`).

    <pre>
   AzureAccount ajouter

    Sélectionnez-AzureSubscription « Mon abonnement Microsoft »

    $vm = get-AzureVM - ServiceName « mytestvm1 »-mytestvm1 « nom »

    $endpoints = @( ,@{Name="RDConnVS2013"; PublicPort = 30400 ; PrivatePort = 30398} ,@{Name="RDFwdrVS2013"; PublicPort = 31400 ; PrivatePort = 31398})  

    foreach ($endpoint dans $endpoints) {ajouter AzureEndpoint - machine virtuelle $vm-nommer $endpoint. Name - tcp Protocol - PublicPort $endpoint. PublicPort - Port_local $endpoint. PrivatePort}

    $referenceName = « Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015 » $publisher = « Microsoft.VisualStudio.WindowsAzure.RemoteDebug » $extensionName = « RemoteDebugVS2015 » $version = « 1.* » $publicConfiguration = «<PublicConfig>< Connector.Enabled > true < /Connector.Enabled ><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>»

    $vm | Jeu-AzureVMExtension `
    -ReferenceName $referenceName ` 
    -Publisher $publisher `
    -ExtensionName $extensionName ` 
    -Version $version ' - PublicConfiguration $publicConfiguration

    foreach ($extension dans $vm. MACHINE VIRTUELLE. ResourceExtensionReferences) {si (($extension. Nom de la référence - eq $referenceName) `
    -and ($extension.Publisher -eq $publisher) ` 
    - et ($extension. Nommez - eq $extensionName) '- et ($extension. Version - eq $version)) {$extension. ResourceExtensionParameterValues [0]. Clé = 'config.txt' saut}}

    $vm | Mise à jour AzureVM </pre>

6. Importer le certificat (.pfx) sur l’ordinateur sur lequel Visual Studio avec Azure SDK pour .NET est installé.
