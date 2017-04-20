<properties 
   pageTitle="Résoudre les problèmes d’un appareil StorSimple déployé | Microsoft Azure"
   description="Décrit comment diagnostiquer et corriger les erreurs qui se produisent sur un appareil StorSimple qui est actuellement déployée et opérationnel."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/16/2016"
   ms.author="v-sharos" />

# <a name="troubleshoot-an-operational-storsimple-device"></a>Résoudre les problèmes d’un périphérique StorSimple opérationnel

## <a name="overview"></a>Vue d’ensemble

Cet article fournit des conseils de dépannage pour la résolution des problèmes de configuration que vous pouvez rencontrer une fois que votre appareil StorSimple est déployée et opérationnel. Il décrit les problèmes courants, les causes possibles et étapes recommandées pour vous aider à résoudre les problèmes que vous pouvez rencontrer lorsque vous exécutez Microsoft Azure StorSimple. Ces informations s’appliquent à la fois le périphérique de physique StorSimple en local et le périphérique virtuel StorSimple.

À la fin de cet article, que vous pouvez rechercher une liste des codes d’erreur que vous pouvez rencontrer lors de Microsoft Azure StorSimple opération, ainsi que les étapes vous pouvez prendre pour résoudre les erreurs. 

## <a name="setup-wizard-process-for-operational-devices"></a>Processus de l’Assistant Configuration pour périphériques opérationnels

Vous utilisez l’Assistant Configuration ([Appeler HcsSetupWizard][1]) pour vérifier la configuration du périphérique et remédier si nécessaire.

Lorsque vous exécutez l’Assistant Configuration sur un appareil précédemment configuré et opérationnel, le flux de processus est différent. Vous pouvez modifier uniquement les entrées suivantes :

- Adresse IP, masque et passerelle
- Serveur DNS principal
- Serveur NTP principal
- Configuration du proxy web facultatif

L’Assistant Configuration n’effectue les opérations relatives à l’enregistrement des collection de sites et dispositif de mot de passe.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Erreurs qui se produisent pendant les exécutions ultérieures de l’Assistant de configuration

Le tableau suivant décrit les erreurs que vous pouvez rencontrer lorsque vous exécutez l’Assistant Configuration sur un appareil opérationnel, les causes possibles des erreurs et les mesures recommandées pour les résoudre. 

| Non. | Message d’erreur ou condition | Causes possibles | Action recommandée |
|:--- |:-------------------------- |:--------------- |:------------------ |
|  1  | Erreur 350032 : Cet appareil a déjà été désactivé. | Vous verrez cette erreur si vous exécutez l’Assistant Configuration sur un appareil est désactivé. | [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour obtenir la procédure suivante. Un périphérique désactivé ne peut pas être mis en service. Une réinitialisation usine peut être nécessaire avant que l’appareil peut être activé à nouveau. |
|  2  | Appeler-HcsSetupWizard : ERROR_INVALID_FUNCTION (Exception à partir de HRESULT : 0 x 80070001) | Échec de la mise à jour du serveur DNS. Paramètres de DNS sont des paramètres globaux et sont appliqués à toutes les interfaces réseau activée. | Activer l’interface et appliquez à nouveau les paramètres DNS. Ceci peut interrompre le réseau pour les autres interfaces activés, car ces paramètres sont globaux. |
|  3  | Le périphérique semble être en ligne dans le portail de service Manager StorSimple, mais lorsque vous essayez de terminer la configuration minimale et enregistrer la configuration, l’opération échoue. | Pendant l’installation initiale, le proxy web n’a pas configuré, même si un serveur proxy réel est survenu en place. | Utiliser l' [applet de commande Test-HcsmConnection] [ 2] pour rechercher l’erreur. [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) si vous ne parvenez pas à résoudre le problème. |
|  4  | Appeler-HcsSetupWizard : Valeur n’est pas comprise dans la plage prévue. | Un masque incorrecte génère cette erreur. Les causes possibles sont : <ul><li> Le masque est manquant ou vide.</li><li>Le format de préfixe Ipv6 est incorrect.</li><li>L’interface est activé pour le cloud, mais la passerelle est manquant ou incorrect.</li></ul>Notez que les données 0 soient automatiquement activé pour le cloud si configuré via l’Assistant Configuration. | Pour déterminer le problème, utilisez sous-réseau 0.0.0.0 ou 256.256.256.256 et consultez ensuite le résultat. Entrez les valeurs correctes pour le masque, la passerelle et préfixe Ipv6, selon vos besoins. |
 
## <a name="error-codes"></a>Codes d’erreur

Erreurs sont répertoriées dans l’ordre numérique.

|Numéro d’erreur|Texte d’erreur ou la description|Action de l’utilisateur recommandés|
|:---|:---|:---|
|10502|Une erreur est survenue pendant l’accès à votre compte de stockage.|Patientez quelques minutes et réessayez. Si l’erreur persiste, veuillez contacter le support technique Microsoft pour obtenir la procédure suivante.|
|40017|L’opération de sauvegarde a échoué car un volume spécifié dans la stratégie de sauvegarde n’a pas été trouvé sur l’appareil.|Recommencez la sauvegarde opération, si l’erreur persiste, contactez le Support Microsoft. Pour obtenir la procédure suivante.|
|40018|L’opération de sauvegarde a échoué car aucun des volumes spécifiés dans la stratégie de sauvegarde ont été trouvés sur l’appareil. |Recommencez la sauvegarde opération, si l’erreur persiste, contactez le Support Microsoft. Pour obtenir la procédure suivante.|
|390061|Le système est occupé ou indisponible.|Patientez quelques minutes et réessayez. Si l’erreur persiste, veuillez contacter le support technique Microsoft pour obtenir la procédure suivante.|
|390143|Une erreur s’est produite avec le code d’erreur 390143. (Erreur inconnue).|Si l’erreur persiste, contactez le Support Microsoft pour obtenir la procédure suivante.|

## <a name="next-steps"></a>Étapes suivantes

Si vous ne parvenez pas à résoudre le problème, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md) pour obtenir une assistance. 


[1]: https://technet.microsoft.com/en-us/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/en-us/%5Clibrary/Dn715782(v=WPS.630).aspx
