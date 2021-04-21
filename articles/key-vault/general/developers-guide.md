---
title: Guida per gli sviluppatori dell'insieme di credenziali delle chiavi di Azure
description: Gli sviluppatori possono utilizzare l'insieme di credenziali chiave di Azure per gestire le chiavi di crittografia all'interno dell'ambiente Microsoft Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 4f9523594c07209d530a143713061be6d0467af8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753381"
---
# <a name="azure-key-vault-developers-guide"></a>Guida per gli sviluppatori dell'insieme di credenziali delle chiavi di Azure

Key Vault consente di accedere in modo sicuro ai dati sensibili dall'interno delle applicazioni:

- Le chiavi, i segreti e i certificati sono protetti senza dover scrivere il codice manualmente e si è in grado di usarli facilmente dalle applicazioni.
- È possibile consentire ai clienti di possedere e gestire le proprie chiavi, segreti e certificati in modo da potersi concentrare sulla fornitura delle funzionalità software di base. In questo modo, le applicazioni non saranno proprietarie della responsabilità o della potenziale responsabilità per chiavi, segreti e certificati del tenant dei clienti.
- L'applicazione può usare le chiavi per la firma e la crittografia ma mantiene la gestione delle chiavi esterna all'applicazione. Per altre informazioni sulle chiavi, vedere [Informazioni sulle chiavi](../keys/about-keys.md)
- È possibile gestire credenziali come password, chiavi di accesso e token sas archiviandole Key Vault come segreti, vedere [Informazioni sui segreti](../secrets/about-secrets.md)
- Gestire i certificati. Per altre informazioni, vedere [Informazioni sui certificati](../certificates/about-certificates.md)

Per altre informazioni generali sull'insieme di credenziali delle chiavi di Azure, vedere l'articolo [Cos'è l'insieme di credenziali chiave di Azure?](overview.md)

## <a name="public-previews"></a>Anteprime pubbliche

Periodicamente, viene rilasciata un'anteprima pubblica di una nuova funzionalità di Key Vault. Provare le funzionalità di anteprima pubblica e inviare un messaggio di posta elettronica all'indirizzo di posta elettronica azurekeyvault@microsoft.com per i commenti e suggerimenti.

## <a name="creating-and-managing-key-vaults"></a>Creazione e gestione di insiemi di credenziali delle chiavi

Key Vault, analogamente ad altri servizi di Azure, viene eseguita tramite Azure Resource Manager servizio. Azure Resource Manager è il servizio di distribuzione e gestione di Azure. Fornisce un livello di gestione che consente di creare, aggiornate ed eliminare risorse nell'account Azure. Per altre informazioni, vedere [Azure Resource Manager](../../azure-resource-manager/management/overview.md)

L'accesso al livello di gestione è controllato dal [controllo degli accessi in base al ruolo di Azure.](../../role-based-access-control/overview.md) In Key Vault, il livello di gestione, noto anche come piano di gestione o di controllo, consente di creare e gestire Gli insiemi di credenziali delle chiavi e i relativi attributi, inclusi i criteri di accesso, ma non chiavi, segreti e certificati, gestiti nel piano dati. È possibile usare un ruolo predefinito per concedere `Key Vault Contributor` l'accesso di gestione Key Vault.     

**API e SDK per la gestione dell'insieme di credenziali delle chiavi:**

| Interfaccia della riga di comando di Azure | PowerShell | API REST | Gestione risorse | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Riferimento](/cli/azure/keyvault)<br>[Guida introduttiva](quick-create-cli.md)|[Riferimento](/powershell/module/az.keyvault)<br>[Guida introduttiva](quick-create-powershell.md)|[Riferimento](/rest/api/keyvault/)|[Riferimento](/azure/templates/microsoft.keyvault/vaults)<br>[Guida introduttiva](./vault-create-template.md)|[Riferimento](/dotnet/api/microsoft.azure.management.keyvault)|[Riferimento](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Riferimento](/java/api/com.microsoft.azure.management.keyvault)|[Riferimento](/javascript/api/@azure/arm-keyvault)|

Vedere [Librerie client per](client-libraries.md) i pacchetti di installazione e il codice sorgente.

Per altre informazioni sul piano Key Vault di gestione, vedere Key Vault [Management Plane](security-overview.md)

## <a name="authenticate-to-key-vault-in-code"></a>Eseguire l'Key Vault nel codice

Key Vault l'autenticazione Azure AD che richiede Azure AD di sicurezza per concedere l'accesso. Un Azure AD di sicurezza può essere un utente, un'entità servizio dell'applicazione, un'identità gestita per le risorse di [Azure](../../active-directory/managed-identities-azure-resources/overview.md)o un gruppo di qualsiasi tipo di entità di sicurezza.

### <a name="authentication-best-practices"></a>Procedure consigliate per l'autenticazione

È consigliabile usare l'identità gestita per le applicazioni distribuite in Azure. Se si usano servizi di Azure che non supportano l'identità gestita o se le applicazioni vengono distribuite in [locale,](../../active-directory/develop/howto-create-service-principal-portal.md) l'entità servizio con un certificato è una possibile alternativa. In questo scenario, il certificato deve essere archiviato in Key Vault e ruotato spesso. L'entità servizio con segreto può essere usata per gli ambienti di sviluppo e test ed è consigliabile usare l'entità utente in locale o in Cloud Shell'entità utente.

Entità di sicurezza consigliate per ambiente:
- **Ambiente di produzione:**
  - Identità gestita o entità servizio con un certificato
- **Ambienti di test e sviluppo:**
  - Identità gestita, entità servizio con certificato o entità servizio con segreto
- **Sviluppo locale:**
  - Entità utente o entità servizio con segreto

Gli scenari di autenticazione precedenti sono supportati **dalla libreria client** di Identità di Azure e integrati con Key Vault SDK. La libreria di identità di Azure può essere usata in ambienti e piattaforme diversi senza modificare il codice. Identità di Azure recupera automaticamente anche il token di autenticazione dall'utente connesso all'utente di Azure con l'interfaccia della riga di comando di Azure, Visual Studio, Visual Studio Code e altri. 

Per altre informazioni sul portale del client di identità di Azure, vedere:

**Librerie client di Identità di Azure**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure Identity SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure Identity SDK Python](/python/api/overview/azure/identity-readme)|[Azure Identity SDK Java](/java/api/overview/azure/identity-readme)|[Azure Identity SDK JavaScript](/javascript/api/overview/azure/identity-readme)|     

>[!Note]
> [Libreria di autenticazione app](/dotnet/api/overview/azure/service-to-service-authentication) consigliata per Key Vault .NET SDK versione 3, attualmente obsoleta. Seguire le indicazioni sulla migrazione da AppAuthentication ad [Azure.Identity](/dotnet/api/overview/azure/app-auth-migration) per eseguire la migrazione Key Vault .NET SDK versione 4.

Per esercitazioni su come eseguire l'autenticazione Key Vault nelle applicazioni, vedere:
- [Eseguire l'Key Vault nell'applicazione ospitata nella macchina virtuale in .NET](./tutorial-net-virtual-machine.md)
- [Eseguire l'Key Vault nell'applicazione ospitata nella macchina virtuale in Python](./tutorial-python-virtual-machine.md)
- [Eseguire l'autenticazione Key Vault con il servizio app](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>Gestire chiavi, certificati e segreti

L'accesso a chiavi, segreti e certificati è controllato dal piano dati. Il controllo di accesso al piano dati può essere eseguito usando i criteri di accesso dell'insieme di credenziali locale o il controllo degli accessi in base al ruolo di Azure.

**API chiavi e SDK**

| Interfaccia della riga di comando di Azure | PowerShell | API REST | Gestione risorse | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Riferimento](/cli/azure/keyvault/key)<br>[Guida introduttiva](../keys/quick-create-cli.md)|[Riferimento](/powershell/module/az.keyvault/)<br>[Guida introduttiva](../keys/quick-create-powershell.md)|[Riferimento](/rest/api/keyvault/#key-operations)|[Riferimento](/azure/templates/microsoft.keyvault/vaults/keys)<br>[Guida introduttiva](../keys/quick-create-template.md)|[Riferimento](/dotnet/api/azure.security.keyvault.keys)<br>[Guida introduttiva](../keys/quick-create-net.md)|[Riferimento](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Guida introduttiva](../keys/quick-create-python.md)|[Riferimento](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[Guida introduttiva](../keys/quick-create-java.md)|[Riferimento](/javascript/api/@azure/keyvault-keys/)<br>[Guida introduttiva](../keys/quick-create-node.md)|

**API certificati e SDK**

| Interfaccia della riga di comando di Azure | PowerShell | API REST | Gestione risorse | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Riferimento](/cli/azure/keyvault/certificate)<br>[Guida introduttiva](../certificates/quick-create-cli.md)|[Riferimento](/powershell/module/az.keyvault)<br>[Guida introduttiva](../certificates/quick-create-powershell.md)|[Riferimento](/rest/api/keyvault/#certificate-operations)|N/D|[Riferimento](/dotnet/api/azure.security.keyvault.certificates)<br>[Guida introduttiva](../certificates/quick-create-net.md)|[Riferimento](/python/api/overview/azure/keyvault-certificates-readme)<br>[Guida introduttiva](../certificates/quick-create-python.md)|[Riferimento](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[Guida introduttiva](../certificates/quick-create-java.md)|[Riferimento](/javascript/api/@azure/keyvault-certificates/)<br>[Guida introduttiva](../certificates/quick-create-node.md)|

**API segreti e SDK**

| Interfaccia della riga di comando di Azure | PowerShell | API REST | Gestione risorse | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Riferimento](/cli/azure/keyvault/secret)<br>[Guida introduttiva](../secrets/quick-create-cli.md)|[Riferimento](/powershell/module/az.keyvault/)<br>[Guida introduttiva](../secrets/quick-create-powershell.md)|[Riferimento](/rest/api/keyvault/#secret-operations)|[Riferimento](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Guida introduttiva](../secrets/quick-create-template.md)|[Riferimento](/dotnet/api/azure.security.keyvault.secrets)<br>[Guida introduttiva](../secrets/quick-create-net.md)|[Riferimento](/python/api/overview/azure/keyvault-secrets-readme)<br>[Guida introduttiva](../secrets/quick-create-python.md)|[Riferimento](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Guida introduttiva](../secrets/quick-create-java.md)|[Riferimento](/javascript/api/@azure/keyvault-secrets/)<br>[Guida introduttiva](../secrets/quick-create-node.md)|

Vedere [Librerie client per](client-libraries.md) i pacchetti di installazione e il codice sorgente.

Per altre informazioni sulla sicurezza Key Vault del piano dati, vedere panoramica [Key Vault sicurezza.](security-overview.md)

### <a name="code-examples"></a>Esempi di codice

Per esempi completi che usano l'insieme di credenziali delle chiavi con le applicazioni, vedere:

- [Esempi di codice di Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault): esempi di codice per Azure Key Vault. 

## <a name="how-tos"></a>Procedure

Gli articoli e gli scenari seguenti offrono indicazioni specifiche su come usare l'insieme di credenziali delle chiavi di Azure:

- [Accesso a un insieme di credenziali delle chiavi protetto da firewall](access-behind-firewall.md): per accedere a un insieme di credenziali delle chiavi, l'applicazione client dell'insieme di credenziali delle chiavi deve poter accedere a più endpoint per varie funzionalità.
- Come distribuire certificati nelle macchine virtuali da Key Vault - [Windows](../../virtual-machines/extensions/key-vault-windows.md), [Linux](../../virtual-machines/extensions/key-vault-linux.md) : un'applicazione cloud in esecuzione in una macchina virtuale in Azure richiede un certificato. Come ottenere oggi il certificato per questa VM?
- [Distribuzione di un certificato dell'app Web di Azure tramite Key Vault](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)
- Assegnare un[](assign-access-policy-cli.md)criterio di accesso (portale  |  [di PowerShell dell'interfaccia della riga di](assign-access-policy-powershell.md)  |  [comando).](assign-access-policy-portal.md) 
- [Come usare l'eliminazione temporanea di Key Vault con l'interfaccia della riga di comando](./key-vault-recovery.md) descrive l'utilizzo e il ciclo di vita di un insieme di credenziali delle chiavi e di vari oggetti dell'insieme di credenziali delle chiavi con l'eliminazione temporanea abilitata.
- [Passare valori protetti come password durante la distribuzione](../../azure-resource-manager/templates/key-vault-parameter.md) : se è necessario passare come parametro durante la distribuzione un valore protetto, ad esempio una password, è possibile archiviare tale valore come chiave privata in un insieme di credenziali delle chiavi di Azure e fare riferimento al valore in altri modelli di Resource Manager.

## <a name="integrated-with-key-vault"></a>Integrazione con l'insieme di credenziali delle chiavi

Questi articoli illustrano altri scenari e servizi che usano o si integrano con Key Vault.

- [La crittografia dei dati](../../security/fundamentals/encryption-atrest.md) in pausa consente la codifica (crittografia) dei dati quando vengono resi persistenti. Le chiavi di crittografia dei dati vengono spesso crittografate con una chiave di crittografia della chiave Azure Key Vault per limitare ulteriormente l'accesso.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) consente di gestire la propria chiave tenant. Ad esempio, anziché affidare a Microsoft la gestione della chiave tenant (impostazione predefinita), l'utente può gestire la propria chiave tenant per garantire la conformità alle normative specifiche che si applicano all'organizzazione. La gestione della propria chiave tenant viene definita Bring Your Own Key o BYOK.
- [collegamento privato di Azure Service](private-link-service.md) consente di accedere ai servizi di Azure (ad esempio, Azure Key Vault, Archiviazione di Azure e Azure Cosmos DB) e ai servizi clienti/partner ospitati in Azure tramite un endpoint privato nella rete virtuale.
- Key Vault'integrazione con [Griglia](../../event-grid/event-schema-key-vault.md)  di eventi consente agli utenti di ricevere una notifica quando lo stato di un segreto archiviato nell'insieme di credenziali delle chiavi è stato modificato. È possibile distribuire una nuova versione dei segreti alle applicazioni o ruotare i segreti in prossimità della scadenza per evitare interruzioni.
- È possibile proteggere i [segreti di Devops](/azure/devops/pipelines/release/azure-key-vault) di Azure da accessi indesiderati in Key Vault.
- [Usare il segreto archiviato in Key Vault in DataBricks per connettersi a Archiviazione di Azure](./integrate-databricks-blob-storage.md)
- Configurare ed eseguire il provider Azure Key Vault per il [driver CSI dell'archivio segreti](./key-vault-integrate-kubernetes.md) in Kubernetes

## <a name="key-vault-overviews-and-concepts"></a>Panoramiche e concetti su Key Vault

- [Comportamento di eliminazione temporanea di Key Vault](soft-delete-overview.md) descrive una funzionalità che consente il recupero di oggetti eliminati, sia che l'eliminazione sia stata accidentale sia che sia stata intenzionale.
- [Limitazione del client di Key Vault](overview-throttling.md) descrive i concetti di base della limitazione e offre un approccio per l'app.
- [Scenari di sicurezza di Key Vault](overview-security-worlds.md) descrive le relazioni tra le aree e le zone di sicurezza.

## <a name="social"></a>Social network

- [Blog sull'insieme di credenziali delle chiavi](/archive/blogs/kv/)
- [Forum sull'insieme di credenziali delle chiavi](https://aka.ms/kvforum)