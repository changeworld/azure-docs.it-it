---
title: Esercitazione sulla rotazione per le risorse con due set di credenziali
description: Questa esercitazione illustra come automatizzare la rotazione di un segreto per le risorse che usano due set di credenziali di autenticazione.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 1f656a41b0f447b90f58ec14173e418a2defb72e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484830"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>Automatizzare la rotazione di un segreto per le risorse che hanno due set di credenziali di autenticazione

Sebbene il modo migliore per eseguire l'autenticazione ai servizi di Azure preveda l'utilizzo di un'[identità gestita](../general/authentication.md), esistono alcuni scenari in cui questa alternativa non è disponibile. In questi casi, si usano chiavi di accesso o password. Le chiavi di accesso e le password devono essere ruotate di frequente.

Questa esercitazione illustra come automatizzare la rotazione periodica dei segreti per i database e i servizi che usano due set di credenziali di autenticazione. In particolare, illustra come ruotare le chiavi dell'account di Archiviazione di Azure archiviate in Azure Key Vault come segreti. Si userà una funzione attivata da una notifica di Griglia di eventi di Azure. 

> [!NOTE]
> Le chiavi dell'account di archiviazione possono essere gestite automaticamente in Key Vault fornendo token di firma di accesso condiviso per l'accesso delegato all'account di archiviazione. Esistono servizi che richiedono stringhe di connessione dell'account di archiviazione con chiavi di accesso. Questa soluzione è consigliabile per uno scenario di questo tipo.

Ecco la soluzione di rotazione descritta in questa esercitazione: 

![Diagramma che illustra la soluzione di rotazione.](../media/secrets/rotation-dual/rotation-diagram.png)

In questa soluzione Azure Key Vault archivia le chiavi di accesso individuali dell'account di archiviazione come versioni dello stesso segreto, alternando tra la chiave primaria e quella secondaria nelle versioni successive. Quando una chiave di accesso viene archiviata nella versione più recente del segreto, la chiave alternativa viene rigenerata e aggiunta a Key Vault come nuova versione più recente del segreto. La soluzione fornisce l'intero ciclo di rotazione dell'applicazione da aggiornare alla chiave rigenerata più recente. 

1. Trenta giorni prima della data di scadenza di un segreto, Key Vault pubblica l'evento in scadenza in Griglia di eventi.
1. Griglia di eventi controlla le sottoscrizioni di eventi e, tramite POST HTTP, chiama l'endpoint dell'app per le funzioni che ha sottoscritto l'evento.
1. L'app per le funzioni identifica la chiave alternativa (diversa da quella più recente) e chiama l'account di archiviazione per rigenerarla.
1. L'app per le funzioni aggiunge la nuova chiave rigenerata ad Azure Key Vault come nuova versione del segreto.

## <a name="prerequisites"></a>Prerequisiti
* Una sottoscrizione di Azure. [Crearne una gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure [Cloud Shell](https://shell.azure.com/). Questa esercitazione usa il portale Cloud Shell con l'ambiente PowerShell
* Azure Key Vault.
* Due account di archiviazione di Azure.

Se non si hanno a disposizione un insieme di credenziali delle chiavi e account di archiviazione, è possibile usare questo collegamento di distribuzione:

[![Collegamento Distribuisci in Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. In **Gruppo di risorse** selezionare **Crea nuovo**. Assegnare al gruppo il nome **vaultrotation** e quindi selezionare **OK**.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.

    ![Screenshot che mostra come creare un gruppo di risorse.](../media/secrets/rotation-dual/dual-rotation-1.png)

A questo punto si hanno un insieme di credenziali delle chiavi e due account di archiviazione. È possibile verificare questa configurazione nell'interfaccia della riga di comando di Azure o Azure PowerShell eseguendo questo comando:
# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
az resource list -o table -g vaultrotation
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzResource -Name 'vaultrotation*' | Format-Table
```
---

Il risultato dovrebbe essere simile all'output seguente:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
vaultrotation-kv         vaultrotation      westus      Microsoft.KeyVault/vaults
vaultrotationstorage     vaultrotation      westus      Microsoft.Storage/storageAccounts
vaultrotationstorage2    vaultrotation      westus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>Creare e distribuire la funzione di rotazione delle chiavi

Verrà ora creata un'app per le funzioni con un'identità gestita dal sistema, oltre ad altri componenti necessari. Si distribuirà inoltre la funzione di rotazione per le chiavi degli account di archiviazione.

La funzione di rotazione dell'app per le funzioni richiede i componenti e la configurazione seguenti:
- Un piano di servizio app di Azure
- Un account di archiviazione per gestire i trigger dell'app per le funzioni
- Un criterio di accesso ai segreti in Key Vault
- Il ruolo del servizio dell'operatore della chiave dell'account di archiviazione assegnato all'app per le funzioni in modo che possa accedere alle chiavi di accesso degli account di archiviazione
- Una funzione di rotazione delle chiavi con un trigger di eventi e un trigger HTTP (rotazione su richiesta)
- Una sottoscrizione di eventi di Griglia di eventi per l'evento **SecretNearExpiry**

1. Selezionare il collegamento alla distribuzione modelli di Azure: 

   [![Collegamento alla distribuzione modelli di Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. Nell'elenco **Gruppo di risorse** selezionare **vaultrotation**.
1. Nella casella **Storage Account RG** (Gruppo di risorse dell'account di archiviazione) immettere il nome del gruppo di risorse in cui si trova l'account di archiviazione. Mantenere il valore predefinito **[resourceGroup().name]** se l'account di archiviazione si trova già nello stesso gruppo di risorse in cui si distribuirà la funzione di rotazione delle chiavi.
1. Nella casella **Nome account di archiviazione** immettere il nome dell'account di archiviazione che contiene le chiavi di accesso da ruotare. Mantenere il valore predefinito **[concat(resourceGroup().name, 'storage')]** se si usa l'account di archiviazione creato nei [prerequisiti](#prerequisites).
1. Nella casella **Key Vault RG** (Gruppo di risorse dell'insieme di credenziali delle chiavi) immettere il nome del gruppo di risorse in cui si trova l'insieme di credenziali delle chiavi. Mantenere il valore predefinito **[resourceGroup().name]** se l'insieme di credenziali delle chiavi si trova già nello stesso gruppo di risorse in cui si distribuirà la funzione di rotazione delle chiavi.
1. Nella casella **Nome insieme di credenziali delle chiavi** immettere il nome dell'insieme di credenziali delle chiavi. Mantenere il valore predefinito **[concat(resourceGroup().name, '-kv')]** se si usa l'insieme di credenziali delle chiavi creato nei [prerequisites](#prerequisites).
1. Nella casella **App Service Plan Type** (Tipo di piano del servizio app) selezionare Piano di hosting. L'opzione **Piano Premium** è necessaria solo se l'insieme di credenziali delle chiavi è protetto da firewall.
1. Nella casella **Nome dell'app per le funzioni** immettere il nome dell'app per le funzioni.
1. Nella casella **Nome del segreto** immettere il nome del segreto in cui verranno archiviate le chiavi di accesso.
1. Nella casella **URL del repository** immettere la posizione del codice della funzione in GitHub. In questa esercitazione è possibile usare **https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell.git** .
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.

   ![Screenshot che mostra come creare e distribuire una funzione.](../media/secrets/rotation-dual/dual-rotation-2.png)

Dopo aver completato la procedura precedente, si avranno un account di archiviazione, una server farm, un'app per le funzioni e informazioni dettagliate sull'applicazione. Una volta completata la distribuzione, verrà visualizzata questa pagina:

   ![Screenshot che mostra la pagina La distribuzione è stata completata.](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> In caso di errore, è possibile selezionare **Ridistribuisci** per completare la distribuzione dei componenti.


È possibile trovare modelli di distribuzione e codice per la funzione di rotazione in [Esempi di Azure](https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>Aggiungere le chiavi di accesso dell'account di archiviazione a Key Vault

Impostare prima di tutto il criterio di accesso per concedere all'entità utente le autorizzazioni per **gestire i segreti**:
# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
az keyvault set-policy --upn <email-address-of-user> --name vaultrotation-kv --secret-permissions set delete get list
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Set-AzKeyVaultAccessPolicy -UserPrincipalName <email-address-of-user> --name vaultrotation-kv -PermissionsToSecrets set,delete,get,list
```
---

A questo punto è possibile creare un nuovo segreto con una chiave di accesso dell'account di archiviazione come valore. Saranno anche necessari l'ID della risorsa dell'account di archiviazione, il periodo di validità del segreto e l'ID chiave da aggiungere al segreto, in modo che la funzione di rotazione possa rigenerare la chiave nell'account di archiviazione.

Determinare l'ID risorsa dell'account di archiviazione. Il valore è disponibile nella proprietà `id`.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Elencare le chiavi di accesso dell'account di archiviazione in modo da recuperarne i valori:
# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Aggiungere il segreto all'insieme di credenziali delle chiavi con la data impostata sul giorno successivo, un periodo di validità di 60 giorni e l'ID risorsa dell'account di archiviazione. Eseguire questo comando usando i valori recuperati per `key1Value` e `storageAccountResourceId`:

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
az keyvault secret set --name storageKey --vault-name vaultrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyy-MM-ddTHH:mm:ssZ')
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key1'
    ProviderAddress='<storageAccountResourceId>'
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

Il segreto precedente attiverà l'evento `SecretNearExpiry` entro diversi minuti. Questo evento a sua volta attiverà la funzione per ruotare il segreto con la scadenza impostata su 60 giorni. In tale configurazione, l'evento 'SecretNearExpiry' verrà attivato ogni 30 giorni (30 giorni prima della scadenza) e la funzione di rotazione alternerà la rotazione tra key1 e key2.

È possibile verificare che le chiavi di accesso siano state rigenerate recuperando la chiave dell'account di archiviazione e il segreto di Key Vault e confrontandoli.

Usare questo comando per ottenere le informazioni sul segreto:
# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey -AsPlainText
```
---

Si noti che `CredentialId` è stato aggiornato al valore di `keyName` alternativo e che `value` è stato rigenerato:

![Screenshot che mostra l'output del comando az keyvault secret show per il primo account di archiviazione.](../media/secrets/rotation-dual/dual-rotation-4.png)

Recuperare le chiavi di accesso per confrontare i valori:
# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Si noti che `value` della chiave è uguale al segreto nell'insieme di credenziali delle chiavi:

![Screenshot che mostra l'output del comando a z storage account keys list per il primo account di archiviazione.](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>Aggiungere gli account di archiviazione per la rotazione

È possibile riutilizzare la stessa app per le funzioni per ruotare le chiavi di più account di archiviazione. 

Per aggiungere chiavi di account di archiviazione a una funzione esistente per la rotazione, è necessario:
- Il ruolo del servizio dell'operatore della chiave dell'account di archiviazione assegnato all'app per le funzioni in modo che possa accedere alle chiavi di accesso dell'account di archiviazione.
- Una sottoscrizione di eventi di Griglia di eventi per l'evento **SecretNearExpiry**.

1. Selezionare il collegamento alla distribuzione modelli di Azure: 

   [![Collegamento alla distribuzione modelli di Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FAdd-Event-Subscriptions%2Fazuredeploy.json)

1. Nell'elenco **Gruppo di risorse** selezionare **vaultrotation**.
1. Nella casella **Storage Account RG** (Gruppo di risorse dell'account di archiviazione) immettere il nome del gruppo di risorse in cui si trova l'account di archiviazione. Mantenere il valore predefinito **[resourceGroup().name]** se l'account di archiviazione si trova già nello stesso gruppo di risorse in cui si distribuirà la funzione di rotazione delle chiavi.
1. Nella casella **Nome account di archiviazione** immettere il nome dell'account di archiviazione che contiene le chiavi di accesso da ruotare.
1. Nella casella **Key Vault RG** (Gruppo di risorse dell'insieme di credenziali delle chiavi) immettere il nome del gruppo di risorse in cui si trova l'insieme di credenziali delle chiavi. Mantenere il valore predefinito **[resourceGroup().name]** se l'insieme di credenziali delle chiavi si trova già nello stesso gruppo di risorse in cui si distribuirà la funzione di rotazione delle chiavi.
1. Nella casella **Nome insieme di credenziali delle chiavi** immettere il nome dell'insieme di credenziali delle chiavi.
1. Nella casella **Nome dell'app per le funzioni** immettere il nome dell'app per le funzioni.
1. Nella casella **Nome del segreto** immettere il nome del segreto in cui verranno archiviate le chiavi di accesso.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.

   ![Screenshot che mostra come creare un account di archiviazione aggiuntivo.](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Aggiungere un'altra chiave di accesso dell'account di archiviazione a Key Vault

Determinare l'ID risorsa dell'account di archiviazione. Il valore è disponibile nella proprietà `id`.
# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Elencare le chiavi di accesso dell'account di archiviazione in modo da recuperare i valori della chiave 2:
# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage2 -ResourceGroupName vaultrotation
```
---

Aggiungere il segreto all'insieme di credenziali delle chiavi con la data impostata sul giorno successivo, un periodo di validità di 60 giorni e l'ID risorsa dell'account di archiviazione. Eseguire questo comando usando i valori recuperati per `key2Value` e `storageAccountResourceId`:

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyy-MM-ddTHH:mm:ssZ')
az keyvault secret set --name storageKey2 --vault-name vaultrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key2';
    ProviderAddress='<storageAccountResourceId>';
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey2 -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

Usare questo comando per ottenere le informazioni sul segreto:
# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey2 -AsPlainText
```
---

Si noti che `CredentialId` è stato aggiornato al valore di `keyName` alternativo e che `value` è stato rigenerato:

![Screenshot che mostra l'output del comando az keyvault secret show per il secondo account di archiviazione.](../media/secrets/rotation-dual/dual-rotation-8.png)

Recuperare le chiavi di accesso per confrontare i valori:
# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Si noti che `value` della chiave è uguale al segreto nell'insieme di credenziali delle chiavi:

![Screenshot che mostra l'output del comando a z storage account keys list per il secondo account di archiviazione.](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-rotation-functions-for-two-sets-of-credentials"></a>Funzioni di rotazione di Key Vault per due set di credenziali

Modello di funzioni di rotazione per due set di credenziali e diverse funzioni pronte per l'uso:

- [Modello di progetto](https://serverlesslibrary.net/sample/bc72c6c3-bd8f-4b08-89fb-c5720c1f997f)
- [Cache Redis](https://serverlesslibrary.net/sample/0d42ac45-3db2-4383-86d7-3b92d09bc978)
- [Storage Account](https://serverlesslibrary.net/sample/0e4e6618-a96e-4026-9e3a-74b8412213a4)
- [Cosmos DB](https://serverlesslibrary.net/sample/bcfaee79-4ced-4a5c-969b-0cc3997f47cc)

> [!NOTE]
> Le funzioni di rotazione elencate sopra sono state create da un membro della community e non da Microsoft. Le funzioni di Azure della community non sono supportate in alcun programma o servizio di supporto Microsoft e sono rese disponibili così come sono senza garanzia di alcun tipo.

## <a name="next-steps"></a>Passaggi successivi

- Esercitazione: [Rotazione dei segreti per un set di credenziali](./tutorial-rotation.md)
- Panoramica: [Monitoraggio di Key Vault con Griglia di eventi di Azure](../general/event-grid-overview.md)
- Procedura: [Creare la prima funzione nel portale di Azure](../../azure-functions/functions-get-started.md)
- Procedura: [Ricevere un messaggio di posta elettronica quando viene modificato un segreto dell'insieme di credenziali delle chiavi](../general/event-grid-logicapps.md)
- Informazioni di riferimento: [Schema di eventi di Griglia di eventi di Azure per Azure Key Vault](../../event-grid/event-schema-key-vault.md)
