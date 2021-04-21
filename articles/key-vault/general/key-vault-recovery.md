---
title: Azure Key Vault panoramica del ripristino | Microsoft Docs
description: Key Vault funzionalità di ripristino sono progettate per impedire l'eliminazione accidentale o dannosa dell'insieme di credenziali delle chiavi e dei segreti, delle chiavi e del certificato archiviati all'interno dell'insieme di credenziali delle chiavi.
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.author: mbaldwin
author: msmbaldwin
ms.date: 09/30/2020
ms.openlocfilehash: c3ffbba9546ada54a42c3f2c2aa5d98da599b353
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749733"
---
# <a name="azure-key-vault-recovery-management-with-soft-delete-and-purge-protection"></a>Azure Key Vault gestione del ripristino con protezione da eliminazione e ripulitura soft

Questo articolo illustra due funzionalità di ripristino Azure Key Vault, l'eliminazione soft e la protezione dall'eliminazione. Questo documento offre una panoramica di queste funzionalità e illustra come gestirle tramite portale di Azure, l'interfaccia della riga di comando di Azure e Azure PowerShell.

Per altre informazioni sui Key Vault, vedere
- [Key Vault panoramica](overview.md)
- [Azure Key Vault di chiavi, segreti e certificati](about-keys-secrets-certificates.md)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/dotnet)
* [Modulo di PowerShell](/powershell/azure/install-az-ps).
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)
* Un insieme di credenziali delle chiavi. È possibile crearne uno con il [portale di Azure](../general/quick-create-portal.md), l'[interfaccia della riga di comando di Azure](../general/quick-create-cli.md) o [Azure PowerShell](../general/quick-create-powershell.md).
* L'utente avrà bisogno delle autorizzazioni seguenti (a livello di sottoscrizione) per eseguire operazioni sugli insiemi di credenziali eliminati in modo soft:

  | Autorizzazione | Descrizione |
  |---|---|
  |Microsoft.KeyVault/locations/deletedVaults/read|Visualizza le proprietà di un Key Vault eliminato temporaneamente|
  |Microsoft.KeyVault/locations/deletedVaults/purge/action|Ripulisce un Key Vault eliminato temporaneamente|


## <a name="what-are-soft-delete-and-purge-protection"></a>Informazioni sulla protezione da eliminazione e ripulitura

[La protezione da eliminazione](soft-delete-overview.md) e ripulitura soft è due diverse funzionalità di ripristino dell'insieme di credenziali delle chiavi.

> [!IMPORTANT]
> L'attivazione dell'eliminazione soft è fondamentale per garantire che gli insiemi di credenziali delle chiavi e le credenziali siano protetti da eliminazioni accidentali. Tuttavia, l'attivazione dell'eliminazione software è considerata una modifica di rilievo perché potrebbe essere necessario modificare la logica dell'applicazione o fornire autorizzazioni aggiuntive alle entità servizio. Prima di attivare l'eliminazione software usando le istruzioni riportate di seguito, assicurarsi che l'applicazione sia compatibile con la modifica usando questo [ **documento qui.**](soft-delete-change.md)

**L'eliminazione** soft è progettata per impedire l'eliminazione accidentale dell'insieme di credenziali delle chiavi e delle chiavi, dei segreti e dei certificati archiviati all'interno dell'insieme di credenziali delle chiavi. Si pensi all'eliminazione soft come a un cestino. Quando si elimina un insieme di credenziali delle chiavi o un oggetto dell'insieme di credenziali delle chiavi, questo rimarrà recuperabile per un periodo di conservazione configurabile dall'utente o per un valore predefinito di 90 giorni. Gli insiemi di credenziali delle chiavi nello stato eliminato temporaneamente possono anche essere **eliminati** definitivamente. In questo modo è possibile ricreare gli insiemi di credenziali delle chiavi e gli oggetti dell'insieme di credenziali delle chiavi con lo stesso nome. Sia il ripristino che l'eliminazione di insiemi di credenziali delle chiavi e oggetti richiedono autorizzazioni elevate per i criteri di accesso. **Una volta abilitata, l'eliminazione soft non può essere disabilitata.**

È importante notare che i nomi degli insiemi di credenziali delle chiavi sono univoci a livello **globale,** quindi non sarà possibile creare un insieme di credenziali delle chiavi con lo stesso nome di un insieme di credenziali delle chiavi nello stato eliminato temporaneamente. Analogamente, i nomi delle chiavi, dei segreti e dei certificati sono univoci all'interno di un insieme di credenziali delle chiavi. Non sarà possibile creare un segreto, una chiave o un certificato con lo stesso nome di un altro nello stato di eliminazione soft.

**La protezione dall'eliminazione** è progettata per impedire l'eliminazione dell'insieme di credenziali delle chiavi, delle chiavi, dei segreti e dei certificati da parte di utenti malintenzionati. Si pensi a questo come a un Cestino con un blocco basato sul tempo. È possibile recuperare gli elementi in qualsiasi momento durante il periodo di conservazione configurabile. **Non sarà possibile eliminare o ripulire definitivamente un insieme di credenziali delle chiavi fino alla scadenza del periodo di conservazione.** Una volta trascorso il periodo di conservazione, l'insieme di credenziali delle chiavi o l'oggetto dell'insieme di credenziali delle chiavi verrà ripulito automaticamente.

> [!NOTE]
> La protezione dall'eliminazione è progettata in modo che nessun ruolo o autorizzazione amministratore possa eseguire l'override, disabilitare o aggirare la protezione dall'eliminazione. **Una volta abilitata, la protezione dall'eliminazione non può essere disabilitata o sostituita da chiunque, inclusa Microsoft.** Ciò significa che è necessario ripristinare un insieme di credenziali delle chiavi eliminato o attendere la scadenza del periodo di conservazione prima di riutilizzare il nome dell'insieme di credenziali delle chiavi.

Per altre informazioni sull'eliminazione soft, vedere Azure Key Vault [panoramica dell'eliminazione soft](soft-delete-overview.md)

# <a name="azure-portal"></a>[Portale di Azure](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>Verificare se l'eliminazione soft è abilitata in un insieme di credenziali delle chiavi e abilitare l'eliminazione soft

1. Accedere al portale di Azure.
1. Selezionare l'insieme di credenziali delle chiavi.
1. Fare clic sul pannello "Proprietà".
1. Verificare se il pulsante di opzione accanto all'eliminazione soft è impostato su "Abilita ripristino".
1. Se l'eliminazione soft non è abilitata nell'insieme di credenziali delle chiavi, fare clic sul pulsante di opzione per abilitare l'eliminazione soft e fare clic su "Salva".

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="In Proprietà è evidenziata l'eliminazione soft, così come il valore per abilitarla.":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>Concedere l'accesso a un'entità servizio per ripulire e recuperare i segreti eliminati

1. Accedere al portale di Azure.
1. Selezionare l'insieme di credenziali delle chiavi.
1. Fare clic sul pannello "Criteri di accesso".
1. Nella tabella trovare la riga dell'entità di sicurezza a cui si vuole concedere l'accesso o aggiungere una nuova entità di sicurezza.
1. Fare clic sull'elenco a discesa per chiavi, certificati e segreti.
1. Scorrere fino alla fine dell'elenco a discesa e fare clic su "Ripristina" ed "Elimina"
1. Anche le entità di sicurezza dovranno ottenere ed elencare le funzionalità per eseguire la maggior parte delle operazioni.

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="Nel riquadro di spostamento sinistro è evidenziato Criteri di accesso. Nei criteri di accesso viene visualizzato l'elenco a discesa Posizioni segrete e sono selezionati quattro elementi: Get, List, Recover ed Purge.":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>Elencare, ripristinare o ripulire un insieme di credenziali delle chiavi eliminato definitivamente

1. Accedere al portale di Azure.
1. Fare clic sulla barra di ricerca nella parte superiore della pagina.
1. In "Servizi recenti" fare clic su "Key Vault". Non fare clic su un singolo insieme di credenziali delle chiavi.
1. Nella parte superiore della schermata fare clic sull'opzione "Gestisci insiemi di credenziali eliminati"
1. Sul lato destro dello schermo verrà aperto un riquadro di contesto.
1. Selezionare la propria sottoscrizione.
1. Se l'insieme di credenziali delle chiavi è stato eliminato in modo soft, verrà visualizzato nel riquadro di contesto a destra.
1. Se sono presenti troppi insiemi di credenziali, è possibile fare clic su "Carica altro" nella parte inferiore del riquadro del contesto oppure usare l'interfaccia della riga di comando o PowerShell per ottenere i risultati.
1. Dopo aver trovato l'insieme di credenziali da ripristinare o ripulire, selezionare la casella di controllo accanto.
1. Selezionare l'opzione di ripristino nella parte inferiore del riquadro del contesto se si vuole ripristinare l'insieme di credenziali delle chiavi.
1. Selezionare l'opzione di eliminazione se si vuole eliminare definitivamente l'insieme di credenziali delle chiavi.

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="In Insiemi di credenziali delle chiavi è evidenziata l'opzione Gestisci insiemi di credenziali eliminati.":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="In Gestisci insiemi di credenziali delle chiavi eliminati l'unico insieme di credenziali delle chiavi elencato è evidenziato e selezionato e il pulsante Ripristina è evidenziato.":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>Elencare, ripristinare o ripulire segreti, chiavi e certificati eliminati in modo soft

1. Accedere al portale di Azure.
1. Selezionare l'insieme di credenziali delle chiavi.
1. Selezionare il pannello corrispondente al tipo di segreto che si vuole gestire (chiavi, segreti o certificati).
1. Nella parte superiore della schermata fare clic su "Gestisci elementi eliminati (chiavi, segreti o certificati)
1. Sul lato destro dello schermo verrà visualizzato un riquadro contestuale.
1. Se il segreto, la chiave o il certificato non viene visualizzato nell'elenco, non si trova nello stato di eliminazione soft.
1. Selezionare il segreto, la chiave o il certificato che si vuole gestire.
1. Selezionare l'opzione per il ripristino o l'eliminazione nella parte inferiore del riquadro del contesto.

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="In Chiavi è evidenziata l'opzione Gestisci chiavi eliminate.":::

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

## <a name="key-vault-cli"></a>Key Vault (interfaccia della riga di comando)

* Verificare se per un insieme di credenziali delle chiavi è abilitata l'eliminazione soft

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Abilitare l'eliminazione soft nell'insieme di credenziali delle chiavi

    Per tutti i nuovi insiemi di credenziali delle chiavi l'eliminazione soft è abilitata per impostazione predefinita. Se è attualmente disponibile un insieme di credenziali delle chiavi per cui non è abilitata l'eliminazione soft, usare il comando seguente per abilitare l'eliminazione soft.

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* Eliminare l'insieme di credenziali delle chiavi (recuperabile se è abilitata l'eliminazione soft)

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Elencare tutti gli insiemi di credenziali delle chiavi eliminati automaticamente

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* Ripristinare un insieme di credenziali delle chiavi eliminato automaticamente

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Ripulire l'insieme di credenziali delle chiavi **eliminato automaticamente (AVVISO! QUESTA OPERAZIONE ELIMINERÀ DEFINITIVAMENTE L'INSIEME DI CREDENZIALI DELLE CHIAVI.**

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Abilitare la protezione dall'eliminazione nell'insieme di credenziali delle chiavi

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a>Certificati (interfaccia della riga di comando)

* Concedere l'accesso per ripulire e ripristinare i certificati

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* Eliminazione di un certificato

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Elencare i certificati eliminati

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Recuperare il certificato eliminato

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Ripulire il certificato eliminato in modo soft **(AVVISO! QUESTA OPERAZIONE ELIMINERÀ DEFINITIVAMENTE IL CERTIFICATO)**

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>Chiavi (interfaccia della riga di comando)

* Concedere l'accesso per ripulire e ripristinare le chiavi

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* Tasto CANC

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Elencare le chiavi eliminate

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Ripristinare la chiave eliminata

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Elimina chiave eliminata in modo soft **(AVVISO! QUESTA OPERAZIONE ELIMINERÀ DEFINITIVAMENTE LA CHIAVE)**

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a>Segreti (interfaccia della riga di comando)

* Concedere l'accesso per ripulire e ripristinare i segreti

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* Eliminare un segreto

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Elencare i segreti eliminati

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Recuperare il segreto eliminato

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Ripulire il segreto eliminato in modo soft **(AVVISO! QUESTA OPERAZIONE ELIMINERÀ DEFINITIVAMENTE IL SEGRETO)**

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>Key Vault (PowerShell)

* Verificare se per un insieme di credenziali delle chiavi è abilitata l'eliminazione soft

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* Eliminare l'insieme di credenziali delle chiavi

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* Elencare tutti gli insiemi di credenziali delle chiavi eliminati definitivamente

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* Ripristinare l'insieme di credenziali delle chiavi eliminato in modo soft

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* Ripulire l'insieme di credenziali delle chiavi eliminato in modo **soft (AVVISO! QUESTA OPERAZIONE ELIMINERÀ DEFINITIVAMENTE L'INSIEME DI CREDENZIALI DELLE CHIAVI)**

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* Abilitare la protezione dall'eliminazione nell'insieme di credenziali delle chiavi

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a>Certificati (PowerShell)

* Concedere le autorizzazioni per ripristinare ed eliminare i certificati

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* Eliminare un certificato

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* Elencare tutti i certificati eliminati in un insieme di credenziali delle chiavi

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* Ripristinare un certificato nello stato eliminato

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* Ripulire un certificato eliminato **automaticamente (AVVISO! QUESTA OPERAZIONE ELIMINERÀ DEFINITIVAMENTE IL CERTIFICATO.**

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a>Chiavi (PowerShell)

* Concedere le autorizzazioni per recuperare ed eliminare le chiavi

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* Eliminare una chiave

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* Elencare tutti i certificati eliminati in un insieme di credenziali delle chiavi

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* Per ripristinare una chiave eliminata automaticamente

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* Ripulire una chiave eliminata **automaticamente (AVVISO! QUESTA OPERAZIONE ELIMINERÀ DEFINITIVAMENTE LA CHIAVE.**

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a>Segreti (PowerShell)

* Concedere le autorizzazioni per recuperare ed eliminare i segreti

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* Eliminare un segreto denominato SQLPassword

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* Elencare tutti i segreti eliminati in un insieme di credenziali delle chiavi

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* Ripristinare un segreto nello stato eliminato

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* Ripulire un segreto in stato **eliminato (AVVISO! QUESTA OPERAZIONE ELIMINERÀ DEFINITIVAMENTE LA CHIAVE.**

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
---

## <a name="next-steps"></a>Passaggi successivi

- [Azure Key Vault cmdlet di PowerShell](/powershell/module/az.keyvault)
- [Key Vault dell'interfaccia della riga di comando di Azure](/cli/azure/keyvault)
- [Backup di Azure Key Vault](backup.md)
- [Come abilitare la registrazione di Key Vault](howto-logging.md)
- [Proteggere l'accesso a un insieme di credenziali delle chiavi](security-overview.md)
- [Guida per gli sviluppatori per Azure Key Vault](developers-guide.md)
- [Procedure consigliate per l'uso di un insieme di credenziali delle chiavi](security-overview.md)