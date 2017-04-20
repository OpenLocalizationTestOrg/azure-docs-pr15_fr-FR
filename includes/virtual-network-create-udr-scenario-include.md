## <a name="scenario"></a>Scénario

Pour mieux illustrer comment créer UDRs, ce document utilisera le scénario ci-dessous.

![DESCRIPTION DE L’IMAGE](./media/virtual-network-create-udr-scenario-include/figure1.png)

Dans ce scénario vous créerez une UDR pour le *sous-réseau de fin de premier plan* et un autre UDR pour le *sous-réseau de fin précédent* , comme décrit ci-dessous : 

- **UDR frontaux**. Le serveur frontal UDR est appliquée au sous-réseau *FrontEnd* et contenir une gamme :  
    - **RouteToBackend**. Cet itinéraire vous envoie tout le trafic au sous-réseau principal pour la machine virtuelle **FW1** .
- **Serveur principal UDR**. Le serveur principal UDR est appliquée au sous-réseau *principal* et contenir une gamme : 
    - **RouteToFrontend**. Cet itinéraire vous envoie tout le trafic vers le sous-réseau frontal à la machine virtuelle **FW1** .

La combinaison de ces itinéraires permet de garantir que tout le trafic destiné à partir d’un sous-réseau à l’autre doivent être routé vers la machine virtuelle **FW1** , qui est utilisée comme un équipement virtuel. Vous devez également activer le transfert IP pour cet ordinateur virtuel, pour vérifier qu’il peut recevoir le trafic destiné aux autres machines virtuelles.
