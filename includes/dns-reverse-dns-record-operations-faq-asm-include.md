<BR> 
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>FAQ - inversée DNS pour votre adresse IP affectée Azure

### <a name="how-much-do-reverse-dns-records-cost"></a>Combien inverser le coût des enregistrements DNS ?
Ils sont gratuits !  Il est sans frais supplémentaires pour les enregistrements DNS inverses ou des requêtes.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Mes enregistrements DNS inverses résoudra à partir d’internet ?
Oui. Une fois que vous définissez la propriété DNS inverse pour votre Service Cloud, Azure gère toutes les zones DNS nécessaires pour s’assurer que l’enregistrement DNS inverse résout pour tous les utilisateurs d’internet et délégation DNS.

### <a name="will-a-default-reverse-dns-record-be-created-for-my-cloud-services"></a>Un enregistrement DNS inverse par défaut est créé pour mes Services Cloud ?
Non. DNS inverse est une fonctionnalité d’inscription. Aucun enregistrement DNS inverse par défaut n’est créées si vous choisissez de ne pas les configurer.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Quel est le format du nom de domaine complet (FQDN) ?
Noms de domaine complets sont spécifiées dans l’ordre chronologique et doivent se terminer par un point (par exemple, « app1.contoso.com. »).

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>Que se passe-t-il si les contrôles de validation pour le DNS inverse que j’ai spécifié échouent ?
Où le contrôle pour DNS inversée vérifie échouer, l’opération de gestion du service échouera. Corrigez la valeur DNS inverse selon les besoins et réessayez.

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>Puis-je gérer DNS inverse pour mon site Web Azure ?
DNS inverse n’est pas prise en charge pour les sites Web Azure. DNS inverse est pris en charge pour les rôles d’Azure PaaS et machines virtuelles IaaS.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-cloud-service"></a>Puis-je configurer plusieurs enregistrements DNS inverses pour mon Service Cloud ?
Non. Azure prend en charge un enregistrement DNS inverse unique pour chaque Service Cloud Azure. Chaque Service Cloud Azure toutefois peut avoir son propre enregistrement DNS inverse.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Puis-je envoyer les messages électroniques vers les domaines externes à partir de mes services Azure calculer ?
Non. [Azure calculer les services ne prennent pas en charge envoi messages électroniques à des domaines externes](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).
