<properties
    pageTitle="Préparer un disque dur virtuel Windows pour télécharger dans Azure | Microsoft Azure"
    description="Pratiques recommandées pour la préparation d’un disque dur virtuel Windows avant de le télécharger vers Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="genlin"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/18/2016"
    ms.author="glimoli;genli"/>

# <a name="prepare-a-windows-vhd-to-upload-to-azure"></a>Préparer un disque dur virtuel Windows pour la télécharger vers Azure
Pour télécharger une machine virtuelle Windows locales vers Azure, vous devez préparer correctement le disque dur (disque dur virtuel). Il existe plusieurs étapes recommandées pour pouvoir effectuer avant que vous téléchargez un disque dur virtuel sur Azure. Cet article vous explique comment préparer un disque dur virtuel Windows pour télécharger sur Microsoft Azure et explique [quand et comment utiliser Sysprep](#step23).

## <a name="prepare-the-virtual-disk"></a>Préparer le disque virtuel

>[AZURE.NOTE] 
> Azure prend uniquement en charge [machines virtuelles génération 1](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx) qui se trouvent dans le format de fichier du disque dur virtuel. Le nouveau format de VHDX n’est pas pris en charge dans Azure. 
>
> Le disque dur virtuel doit avoir une taille fixe, non dynamique. Si nécessaire, les instructions ci-dessous en détail la conversion de VHDX ou dynamique disques. La taille maximale autorisée pour le disque dur virtuel est 1023 go.


Vérifiez que le disque dur virtuel Windows fonctionne correctement sur le serveur local. Résoudre les erreurs au sein de la machine virtuelle elle-même avant d’essayer de convertir ou télécharger dans Azure.

Si vous devez convertir votre disque virtuel au format requis pour Azure, utilisez une des méthodes indiquées dans les sections suivantes. Sauvegarder la machine virtuelle avant d’exécuter un processus de conversion de disque virtuel ou Sysprep.

### <a name="convert-using-hyper-v-manager"></a>Convertir à l’aide du Gestionnaire Hyper-V
- Ouvrez le Gestionnaire Hyper-V et sélectionnez votre ordinateur local sur la gauche. Dans le menu au-dessus, cliquez sur **Action** > **Disque modifier**.
    - Dans l’écran **recherche des disque dur virtuel** , recherchez et sélectionnez votre disque virtuel.
    - Sélectionnez **convertir** dans l’écran suivant
        - Si vous avez besoin convertir des VHDX, sélectionnez le **disque dur virtuel** et cliquez sur **suivant**
        - Si vous avez besoin convertir à partir du disque dynamique, sélectionnez **taille fixe** , puis cliquez sur **suivant**

    - Recherchez et sélectionnez **chemin d’accès pour le nouveau fichier de disque dur virtuel**.
    - Cliquez sur **Terminer** pour fermer.

### <a name="convert-using-powershell"></a>Convertir à l’aide de PowerShell
Vous pouvez convertir un disque virtuel à l’aide de l' [applet de commande PowerShell disque dur virtuel convertir](http://technet.microsoft.com/library/hh848454.aspx). Dans l’exemple suivant, nous allons la conversion d’un VHDX au disque dur virtuel et la conversion d’un dynamique au type fixe :

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

### <a name="convert-from-vmware-vmdk-disk-format"></a>Convertir du format de disque VMware VMDK
Si vous avez une image de la machine virtuelle Windows dans le [format de fichier VMDK](https://en.wikipedia.org/wiki/VMDK), convertissez-le en un disque dur virtuel à l’aide du [Convertisseur de Machine virtuelle Microsoft](https://www.microsoft.com/download/details.aspx?id=42497). Lisez le blog [comment convertir un VMDK VMware sur disque dur virtuel Hyper-V](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) pour plus d’informations.

## <a name="prepare-windows-configuration-for-upload"></a>Préparer la configuration de Windows pour téléchargement

> [AZURE.NOTE] Exécuter toutes les commandes suivantes avec [des privilèges d’administrateur](https://technet.microsoft.com/library/cc947813.aspx).

1. Supprimer un itinéraire statique permanent sur la table de routage :

    - Pour afficher la table de routage, exécutez `route print`.
    - Vérifier les sections **Persistance itinéraires** . S’il existe un itinéraire permanent, utilisez [itinéraire supprimer](https://technet.microsoft.com/library/cc739598.apx) pour le supprimer.

2. Supprimer le proxy WinHTTP :

    ```
    netsh winhttp reset proxy
    ```

3. Configurer le disque stratégie SAN à [Onlineall](https://technet.microsoft.com/library/gg252636.aspx):

    ```
    diskpart san policy=onlineall
    ```

4. Utiliser le temps universel coordonné (UTC) pour Windows et définir le type de démarrage du service de temps Windows (w32time) pour **automatiquement**:

    ```
    REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    sc config w32time start= auto
    ```


## <a name="configure-windows-services"></a>Configurer les services de Windows
5. Assurez-vous que chacun des services Windows suivants est défini sur les **valeurs par défaut de Windows**. Ils sont configurés avec les paramètres de démarrage près répertoriées dans la liste suivante. Vous pouvez exécuter ces commandes réinitialiser les paramètres de démarrage :

    ```
    sc config bfe start= auto

    sc config dcomlaunch start= auto

    sc config dhcp start= auto

    sc config dnscache start= auto

    sc config IKEEXT start= auto

    sc config iphlpsvc start= auto

    sc config PolicyAgent start= demand

    sc config LSM start= auto

    sc config netlogon start= demand

    sc config netman start= demand

    sc config NcaSvc start= demand

    sc config netprofm start= demand

    sc config NlaSvc start= auto

    sc config nsi start= auto

    sc config RpcSs start= auto

    sc config RpcEptMapper start= auto

    sc config termService start= demand

    sc config MpsSvc start= auto

    sc config WinHttpAutoProxySvc start= demand

    sc config LanmanWorkstation start= auto

    sc config RemoteRegistry start= auto
    ```


## <a name="configure-remote-desktop-configuration"></a>Configurer la configuration du Bureau à distance
6. S’il y a aucun certificat auto-signé liés au récepteur de protocole RDP (Remote Desktop), supprimez-les :

    ```
    REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
    ```

    Pour plus d’informations sur la configuration de certificats pour écoute RDP, voir [Configurations de certificat récepteur dans Windows Server](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/)

7. Configurer les valeurs de [conservation](https://technet.microsoft.com/library/cc957549.aspx) de service RDP :

    ```
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
    ```

8. Configurer le mode d’authentification pour le service RDP :

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
    ```

9. Activer le service RDP en ajoutant les sous-clés suivantes dans le Registre :

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
    ```


## <a name="configure-windows-firewall-rules"></a>Configurer des règles de pare-feu Windows
10. Autoriser WinRM via les profils de pare-feu trois (domaine, privé et Public) et activer le service PowerShell Remote :

    ```
    Enable-PSRemoting -force
    ```

11. Vérifiez que les règles de pare-feu du système d’exploitation invité suivantes sont en place :

    - Entrant

    ```
    netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
    ```

    - Entrant et sortant

    ```
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes

    netsh advfirewall firewall set rule group="Core Networking" new enable=yes
    ```

    - Sortant

    ```
    netsh advfirewall firewall set rule dir=out name="Network Discovery (LLMNR-UDP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Datagram-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Name-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (Pub-WSD-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (SSDP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnPHost-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD Events-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD EventsSecure-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD-Out)" new enable=yes
    ```


## <a name="additional-windows-configuration-steps"></a>Étapes de configuration Windows supplémentaires
12. Exécuter `winmgmt /verifyrepository` pour confirmer que le référentiel Windows Management Instrumentation (WMI) est cohérent. Si le référentiel est endommagé, voir [ce billet de blog](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

13. Assurez-vous que les paramètres de données de Configuration de démarrage (BCD) correspondent aux opérations suivantes :

    ```
    bcdedit /set {bootmgr} integrityservices enable

    bcdedit /set {default} device partition=C:

    bcdedit /set {default} integrityservices enable

    bcdedit /set {default} recoveryenabled Off

    bcdedit /set {default} osdevice partition=C:

    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
    ```

14. Supprimer tous les filtres Transport Driver Interface supplémentaires, tel qu’un logiciel qui analyse les paquets TCP.
15. Pour vérifier que le disque est correct et cohérentes, exécutez la `CHKDSK /f` commande.
16. Désinstallez les autres logiciels tiers et pilote liés aux composants physiques ou toute autre technologie de virtualisation.
17. Assurez-vous qu’une application tierce n’utilise pas Port 3389. Ce port est utilisé pour le service RDP dans Azure. Vous pouvez utiliser la `netstat -anob` commande pour vérifier les ports utilisé par les applications.
18. Si le disque dur virtuel Windows que vous voulez télécharger est un contrôleur de domaine, suivez [ces étapes supplémentaires](https://support.microsoft.com/kb/2904015) pour préparer le disque.
19. Redémarrez la machine virtuelle pour vous assurer que Windows est toujours exact est accessible à l’aide de la connexion RDP.
20. Réinitialiser le mot de passe administrateur local et vérifiez que vous pouvez utiliser ce compte pour vous connecter à Windows via une connexion RDP.  Cette autorisation d’accès est contrôlée par l’objet de stratégie « Autoriser le journal par les Services Bureau à distance ». Cet objet se trouve sous « Ordinateur configuration Windows\Paramètres sécurité\Stratégies Locales\attribution des droits. »


## <a name="install-windows-updates"></a>Installer les mises à jour Windows
22. Installer les dernières mises à jour pour Windows. Si cela n’est pas possible, vérifiez que les mises à jour suivants sont installés :

    - [KB3137061](https://support.microsoft.com/kb/3137061) Machines virtuelles Microsoft Azure ne récupérer d’une panne de réseau et se produisent des problèmes d’altération des données

    - [KB3115224](https://support.microsoft.com/kb/3115224) Améliorations de la fiabilité pour les machines virtuelles qui sont exécutent sur un hôte Windows Server 2012 R2 ou Windows Server 2012

    - [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031 : Mise à jour pour Microsoft Windows élévation adresse de privilèges : 8 mars 2016

    - [KB3063075](https://support.microsoft.com/kb/3063075) Grand nombre d’événements ID 129 est enregistré lorsque vous exécutez une machine virtuelle Windows Server 2012 R2 dans Microsoft Azure

    - [KB3137061](https://support.microsoft.com/kb/3137061) Machines virtuelles Microsoft Azure ne récupérer d’une panne de réseau et se produisent des problèmes d’altération des données

    - [KB3114025](https://support.microsoft.com/kb/3114025) Baisse des performances lorsque vous accédez à stockage de fichiers Azure à partir de Windows 8.1 ou Server 2012 R2

    - [KB3033930](https://support.microsoft.com/kb/3033930) Correctif augmente la limite de 64 Ko mémoire tampon RIO par processus pour service Windows Azure

    - [KB3004545](https://support.microsoft.com/kb/3004545) Impossible d’accéder aux machines virtuelles qui sont hébergés sur des services d’hébergement via une connexion VPN dans Windows Azure

    - [KB3082343](https://support.microsoft.com/kb/3082343) Connectivité VPN entre local est perdue lorsque Azure tunnel VPN de site à utiliser Windows Server 2012 R2 routage

    - [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031 : Mise à jour pour Microsoft Windows élévation adresse de privilèges : 8 mars 2016

    - [KB3146723](https://support.microsoft.com/kb/3146723) MS16-048 : Description de la mise à jour de sécurité pour CSRSS : 12 avril 2016
    - [KB2904100](https://support.microsoft.com/kb/2904100) Système se bloque pendant disque e/s dans Windows<a id="step23"></a>
23. Si vous voulez créer une image pour déployer plusieurs ordinateurs à partir de celui-ci, vous devez généraliser l’image en exécutant `sysprep` avant de télécharger le disque dur virtuel sur Azure. Vous n’avez pas besoin exécuter `sysprep` pour l’utilisation d’un disque dur virtuel spécialisé. Pour plus d’informations sur la création d’une image GRG, voir les articles suivants :

    - [Créer une image de la machine virtuelle à partir d’un ordinateur de virtuel Azure existant à l’aide du modèle de déploiement Gestionnaire de ressources](virtual-machines-windows-create-vm-generalized.md)
    - [Créer une image de la machine virtuelle à partir d’un virtuelle Azure existant en utilisant le modem déploiement classique](virtual-machines-windows-classic-capture-image.md)
    - [Prise en charge Sysprep des rôles de serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)



## <a name="suggested-extra-configurations"></a>Configurations supplémentaires proposées

Les paramètres suivants ne concernent pas le téléchargement de disque dur virtuel. Toutefois, nous vous recommandons vivement que vous les avez configurés.

- Installez l' [Agent Azure Machines virtuelles](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Après avoir installé l’agent, vous pouvez activer les extensions machine virtuelle. Les extensions de machine virtuelle mettre en œuvre la plupart des fonctionnalités critiques que vous voulez utiliser avec vos ordinateurs virtuels comme la réinitialisation des mots de passe, configuration RDP et nombre d’autres personnes.

- Le journal vidage peut être utile de résoudre les problèmes de blocage de Windows. Activer la collecte de journal vidage :

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f

    sc config wer start= auto
    ```

- Une fois la machine virtuelle est créée dans Azure, configurer la taille du fichier d’échange définies par le système sur le lecteur d :

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
    ```


## <a name="next-steps"></a>Étapes suivantes

- [Télécharger une image de machine virtuelle Windows Azure pour les déploiements du Gestionnaire de ressources](virtual-machines-windows-upload-image.md)
