##<a name="tcp-settings-for-azure-vms"></a>Paramètres TCP pour les machines virtuelles Azure

Machines virtuelles Azure communiquent avec l’Internet public en utilisant [NAT] [ nat] (traduction des adresses réseau). Périphériques NAT attribuer une adresse IP publique et le port à une machine virtuelle Azure, ce qui qui machine virtuelle d’établir un socket pour la communication avec d’autres appareils. Si les paquets interruption du flux à ce socket après une période spécifique, le périphérique NAT supprime le mappage et le socket est gratuit devant être utilisé par d’autres machines virtuelles.

Il s’agit d’un comportement NAT courantes, qui peut entraîner des problèmes de communication sur les applications TCP basé qu’attendez un socket doivent être conservées au-delà du délai d’expiration. Il existe deux paramètres de délai d’inactivité à prendre en compte pour les sessions dans un état *établi connexion* :

- **entrant** via [l’équilibrage de charge Azure][azure-lb-timeout]. Ce délai d’attente par défaut à 4 minutes et peut être ajustée vers le haut à 30 minutes.
- **sortant** à l’aide de [SNAT] [ snat] (NAT Source). Ce délai d’expiration est définie sur 4 minutes et ne peut pas être ajustée.

Pour vous assurer que les connexions ne sont pas perdues au-delà de la limite de délai d’attente, vous devez vous assurer soit votre application conserve la session active, ou vous pouvez configurer le système d’exploitation sous-jacent pour ce faire. Les paramètres à utiliser sont différents pour les systèmes Linux et Windows, comme illustré ci-dessous.

Pour [Linux][linux], vous devez modifier les variables de noyau ci-dessous.
NET.IPv4.tcp_keepalive_time = net.ipv4.tcp_keepalive_intvl 120 = 30 net.ipv4.tcp_keepalive_probes = 8
 
Pour [Windows][windows], vous devez modifier les valeurs de Registre ci-dessous.
KeepAliveInterval = KeepAliveTime 30 = 120 TcpMaxDataRetransmissions = 8


Les paramètres ci-dessus garantissent un paquet d’activité est envoyé après 2 minutes (120 secondes) d’inactivité et ensuite envoyé toutes les 30 secondes. Et si vous ne parvenez pas 8 de ces paquets, la session est supprimée.

<!-- links -->
[nat]: http://computer.howstuffworks.com/nat.htm
[snat]: ../load-balancer/load-balancer-overview.md/#source-nat
[linux]: http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/usingkeepalive.html
[windows]: http://blogs.technet.com/b/nettracer/archive/2010/06/03/things-that-you-may-want-to-know-about-tcp-keepalives.aspx
[azure-lb-timeout]: ../load-balancer/load-balancer-tcp-idle-timeout.md