<properties
    pageTitle="Résolution des problèmes est détérioré état dans le Gestionnaire de trafic Azure"
    description="Comment résoudre les problèmes de profils Manager le trafic lorsqu’il affiche sous forme de dégradé état."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Résolution des problèmes est détérioré état dans le Gestionnaire de trafic Azure

Cet article décrit comment résoudre les problèmes d’un profil Azure le trafic Manager qui s’affiche un état dégradé. Dans ce scénario, tenez compte que vous avez configuré un profil de trafic Manager en pointant sur certains de vos services cloudapp.net hébergé. Lorsque vous vérifiez l’état de votre gestionnaire de trafic, vous voyez que l’état est détériorée.

![état dégradé](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degraded.png)

Si vous accédez à l’onglet points de terminaison de ce profil, vous voyez une ou plusieurs des points de terminaison avec un état en mode hors connexion :

![en mode hors connexion](./media/traffic-manager-troubleshooting-degraded/traffic-manager-offline.png)

## <a name="understanding-traffic-manager-probes"></a>Présentation du Gestionnaire de trafic teste

- Gestionnaire de trafic considère qu’un point de terminaison pour être en ligne uniquement quand la sonde reçoit une réponse HTTP 200 par le chemin d’accès sonde. Toute autre réponse non-200 est une défaillance.
- Une redirection 30 x échoue, même si l’URL redirigé renvoie un 200.
- Pour les sondes HTTPs, les erreurs de certificat sont ignorées.
- Le contenu effectif du chemin sonde n’a aucune importance, dans la mesure où un 200 est renvoyée. Détection une URL vers du contenu statique, tel que « / favicon.ico » est une technique courante. Contenu dynamique, comme les pages ASP, ne retourne pas toujours 200, même lorsque l’application est correcte.
- Une meilleure solution consiste à définir le chemin d’accès sonde à un élément qui dispose d’une logique suffisamment pour déterminer que le site est vers le haut ou vers le bas. Dans l’exemple précédent, en définissant le chemin d’accès « / favicon.ico », vous n’êtes test que w3wp.exe répond. Cette sonde n’indique pas que votre application web est correcte. Une meilleure option serait pour définir un chemin d’accès un élément tel que « / Probe.aspx » qui contient une logique pour déterminer l’état du site. Par exemple, vous pourriez utiliser des compteurs de performance à l’utilisation du processeur ou mesurer le nombre d’échecs de requêtes. Ou vous pourriez tenter d’accéder aux ressources de base de données ou l’état de session pour vous assurer que l’application web fonctionne.
- Si tous les points de terminaison dans un profil sont dégradées, puis le trafic Manager considère tous les points de terminaison comme exact et route le trafic vers tous les points de terminaison. Ce comportement garantit que les problèmes avec le mécanisme de détection n’entraînent pas une panne complète de votre service.

## <a name="troubleshooting"></a>Résolution des problèmes

Pour résoudre une défaillance sonde, vous avez besoin d’un outil qui affiche le code d’état HTTP retour à partir de l’URL de sonde. Il existe plusieurs outils qui vous montrent la réponse HTTP brute.

* [Fiddler](http://www.telerik.com/fiddler)
* [coin](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

En outre, vous pouvez utiliser l’onglet réseau des outils de débogage F12 dans Internet Explorer pour afficher les réponses HTTP.

Dans cet exemple que vous souhaitez voir la réponse à partir de notre URL sonde : http://watestsdp2008r2.cloudapp.net:80/sonde. L’exemple de PowerShell suivant illustre le problème.

```powershell
    Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Exemple de résultat :

```text
    StatusCode StatusDescription
    ---------- -----------------
            301 Moved Permanently
```

Notez que nous avons reçu une réponse de redirection. Comme indiqué précédemment, n’importe quel StatusCode autre que 200 est considéré comme un échec. Le trafic remplace l’état de point de terminaison sur hors connexion. Pour résoudre le problème, vérifiez la configuration du site Web pour vous assurer que la bonne StatusCode peut être renvoyée par le chemin d’accès sonde. Reconfigurer la sonde gestionnaire le trafic vers un chemin d’accès qui retourne une 200.

Si votre sonde utilise le protocole HTTPS, vous devrez peut-être désactiver la vérification pour éviter les erreurs SSL/TLS pendant votre test de certificats. Les instructions suivantes PowerShell désactiver la validation de certificat pour la session PowerShell :

```powershell
    add-type @"
    using System.Net;
    using System.Security.Cryptography.X509Certificates;
    public class TrustAllCertsPolicy : ICertificatePolicy {
        public bool CheckValidationResult(
        ServicePoint srvPoint, X509Certificate certificate,
        WebRequest request, int certificateProblem) {
        return true;
        }
    }
    "@
    [System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Étapes suivantes

[À propos du Gestionnaire de trafic du trafic méthodes de routage](traffic-manager-routing-methods.md)

[Quel est le trafic Manager](traffic-manager-overview.md)

[Services en nuage](http://go.microsoft.com/fwlink/?LinkId=314074)

[Applications Web Azure](https://azure.microsoft.com/documentation/services/app-service/web/)

[Opérations sur le trafic Manager (référence des API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Applets de commande Gestionnaire de trafic Azure][1]

[1]: https://msdn.microsoft.com/library/mt125941(v=azure.200).aspx
