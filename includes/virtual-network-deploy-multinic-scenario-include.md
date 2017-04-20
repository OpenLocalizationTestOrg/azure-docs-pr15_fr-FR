## <a name="scenario"></a>Scénario

Ce document guidera dans un déploiement qui utilise plusieurs cartes réseau dans machines virtuelles dans un scénario spécifique. Dans ce scénario, vous avez deux niveaux IaaS la charge de travail hébergé dans Azure. Chaque niveau est déployé dans son propre sous-réseau dans un réseau virtuel (VNet). Le niveau frontal se compose de plusieurs serveurs web, regroupées dans un équilibrage de charge définir de disponibilité. Le niveau principal se compose de plusieurs serveurs de base de données. Ces serveurs de base de données seront déployées avec deux cartes réseau chaque, un pour l’accès de base de données, l’autre pour la gestion. Le scénario inclut également des groupes de sécurité réseau (NSGs) pour contrôler le trafic autorisé sur chaque sous-réseau et carte réseau dans le déploiement. L’illustration suivante montre l’architecture de base de ce scénario.  

![Scénario MultiNIC](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

