Vos machines virtuelles IaaS (machines virtuelles) et les instances de rôle PaaS dans un réseau virtuel recevoir automatiquement une adresse IP privée à partir d’une plage que vous spécifiez, basée sur le sous-réseau qu’ils sont connectés à. Cette adresse est conservée par les machines virtuelles et les instances de rôle, jusqu'à ce qu’ils sont désactivés. Vous désaffectez une instance machine virtuelle ou rôle par l’arrêt de PowerShell, l’infrastructure du langage commun Azure ou le portail Azure. Dans ce cas, une fois que l’instance machine virtuelle ou rôle démarre à nouveau, il reçoit une adresse IP disponible à partir de l’infrastructure Azure, qui peut-être ne pas être la même auparavant. Si vous arrêtez l’instance machine virtuelle ou un rôle à partir du système d’exploitation invité, elle conserve l’adresse IP qu’il avait.  

Dans certains cas, vous souhaitez une instance machine virtuelle ou le rôle d’avoir une adresse IP, par exemple, si votre ordinateur virtuel d’exécuter DNS ou sera un contrôleur de domaine. Vous pouvez le faire en définissant une adresse IP privée.