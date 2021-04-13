---
title: Chiave gestita dal cliente di Monitoraggio di Azure
description: Informazioni e passaggi per configurare la chiave gestita dal cliente per crittografare i dati nelle aree di lavoro Log Analytics usando Azure Key Vault chiave.
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/10/2021
ms.openlocfilehash: 4033421095ead47e2bd1e97c4f2f42672644d7df
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364856"
---
# <a name="azure-monitor-customer-managed-key"></a>Chiave gestita dal cliente di Monitoraggio di Azure 

I dati Monitoraggio di Azure crittografati con chiavi gestite da Microsoft. È possibile usare la propria chiave di crittografia per proteggere i dati e le query salvate nelle aree di lavoro. Quando si specifica una chiave gestita dal cliente, tale chiave viene usata per proteggere e controllare l'accesso ai dati e, dopo la configurazione, tutti i dati inviati alle aree di lavoro vengono crittografati con la chiave Azure Key Vault dati. Le chiavi gestite dal cliente offrono maggiore flessibilità per gestire i controlli di accesso.

È consigliabile esaminare [limiti e vincoli](#limitationsandconstraints) di seguito prima di procedere alla configurazione.

## <a name="customer-managed-key-overview"></a>Panoramica delle chiavi gestite dal cliente

[La crittografia in pausa](../../security/fundamentals/encryption-atrest.md) è un requisito comune per la privacy e la sicurezza nelle organizzazioni. È possibile consentire ad Azure di gestire completamente la crittografia in stato di inquieto, mentre sono disponibili diverse opzioni per gestire da vicino la crittografia e le chiavi di crittografia.

Monitoraggio di Azure garantisce che tutti i dati e le query salvate siano crittografati in stato di inquieto usando chiavi gestite da Microsoft (MMK). Monitoraggio di Azure offre anche un'opzione per la crittografia usando la propria chiave archiviata nel [Azure Key Vault](../../key-vault/general/overview.md), che consente di revocare l'accesso ai dati in qualsiasi momento. Monitoraggio di Azure'uso della crittografia è identico a quello Archiviazione di Azure [la](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) crittografia.

La chiave gestita dal cliente viene recapitata in [cluster dedicati che](./logs-dedicated-clusters.md) forniscono un livello di protezione e un controllo più elevati. I dati inseriti in cluster dedicati vengono crittografati due volte, una volta a livello di servizio usando chiavi gestite da Microsoft o chiavi gestite dal cliente e una volta a livello di infrastruttura usando due diversi algoritmi di crittografia e due chiavi diverse. [La doppia](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) crittografia protegge da uno scenario in cui uno degli algoritmi o delle chiavi di crittografia può essere compromesso. In questo caso, il livello aggiuntivo di crittografia continua a proteggere i dati. Il cluster dedicato consente anche di proteggere i dati con [il controllo Lockbox.](#customer-lockbox-preview)

I dati inseriti negli ultimi 14 giorni vengono anche mantenuti nella cache ad accesso frequente (con supporto SSD) per un efficace funzionamento del motore di query. Questi dati rimangono crittografati con le chiavi Microsoft indipendentemente dalla configurazione della chiave gestita dal cliente, ma il controllo sui dati SSD è conforme alla [revoca delle chiavi.](#key-revocation) I dati SSD verranno crittografati con la chiave gestita dal cliente nella prima metà del 2021.

I cluster dedicati di Log Analytics usano un modello di prezzi per la prenotazione [della capacità](./logs-dedicated-clusters.md#cluster-pricing-model) a partire da 1000 GB al giorno.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Funzionamento della chiave gestita dal cliente in Monitoraggio di Azure

Monitoraggio di Azure usa l'identità gestita per concedere l'accesso al Azure Key Vault. L'identità del cluster di Log Analytics è supportata a livello di cluster. Per consentire la protezione con chiave gestita dal cliente in più aree di lavoro, una nuova risorsa *cluster* di Log Analytics viene eseguita come connessione di identità intermedia tra il Key Vault e le aree di lavoro Log Analytics. L'archiviazione del cluster usa l'identità gestita associata alla risorsa Cluster per eseguire l'autenticazione al \' Azure Key Vault tramite Azure Active Directory.  

Dopo la configurazione della chiave gestita dal cliente, i nuovi dati inseriti nelle aree di lavoro collegate al cluster dedicato vengono crittografati con la chiave. È possibile scollegare le aree di lavoro dal cluster in qualsiasi momento. I nuovi dati vengono quindi inseriti nell'archiviazione di Log Analytics e crittografati con la chiave Microsoft, mentre è possibile eseguire facilmente query sui dati nuovi e vecchi.

> [!IMPORTANT]
> La funzionalità della chiave gestita dal cliente è a livello di regione. Le Azure Key Vault, il cluster e le aree di lavoro Log Analytics collegate devono essere nella stessa area, ma possono essere in sottoscrizioni diverse.

![Panoramica delle chiavi gestite dal cliente](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. La risorsa *Cluster* di Log Analytics con identità gestita con le autorizzazioni per Key Vault: l'identità viene propagata all'archivio cluster sottostante dedicato Log Analytics
3. Cluster Log Analytics dedicato
4. Aree di lavoro collegate alla *risorsa* Cluster 

### <a name="encryption-keys-operation"></a>Funzionamento delle chiavi di crittografia

La crittografia dei dati di archiviazione include tre tipi di chiavi:

- **KEK** - Chiave di crittografia della chiave (chiave gestita dal cliente)
- **AEK** - chiave di crittografia dell'account
- **DEK** - chiave di crittografia dei dati

Sono applicabili le regole seguenti:

- Gli account di archiviazione del cluster di Log Analytics generano una chiave di crittografia univoca per ogni account di archiviazione, noto come AEK.
- L'AEK viene usato per derivare le chiavi DEK, ovvero le chiavi usate per crittografare ogni blocco di dati scritti su disco.
- Quando si configura la chiave in Key Vault e vi si fa riferimento nel cluster, Archiviazione di Azure invia richieste al Azure Key Vault per eseguire il wrapping e annullare il wrapping della chiave AEK per eseguire operazioni di crittografia e decrittografia dei dati.
- La kek non lascia mai il Key Vault.
- Archiviazione di Azure usa l'identità gestita associata alla risorsa *Cluster* per l'autenticazione e l'accesso Azure Key Vault tramite Azure Active Directory.

### <a name="customer-managed-key-provisioning-steps"></a>Customer-Managed di provisioning delle chiavi

1. Creazione di Azure Key Vault e archiviazione della chiave
1. Creazione di un cluster
1. Concessione delle autorizzazioni a Key Vault
1. Aggiornamento del cluster con i dettagli dell'identificatore di chiave
1. Collegamento di aree di lavoro di Log Analytics

La configurazione della chiave gestita dal cliente non è attualmente supportata portale di Azure [](/cli/azure/monitor/log-analytics) il provisioning può essere eseguito tramite [PowerShell,](/powershell/module/az.operationalinsights/)l'interfaccia della riga di comando [o le richieste REST.](/rest/api/loganalytics/)

### <a name="asynchronous-operations-and-status-check"></a>Operazioni asincrone e controllo dello stato

Alcuni passaggi di configurazione vengono eseguiti in modo asincrono perché non possono essere completati rapidamente. L'oggetto in risposta può essere uno dei `status` seguenti: 'InProgress', 'Updating', 'Deleting', 'Succeeded o Failed' con codice di errore.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/D

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

N/D

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/D

# <a name="rest"></a>[REST](#tab/rest)

Quando si usa REST, la risposta restituisce inizialmente un codice di stato HTTP 202 (accettato) e un'intestazione con la proprietà *Azure-AsyncOperation:*
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

È possibile controllare lo stato dell'operazione asincrona inviando una richiesta GET all'endpoint *nell'intestazione Azure-AsyncOperation:*
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

---

## <a name="storing-encryption-key-kek"></a>Archiviazione della chiave di crittografia (KEK)

Creare o usare un'istanza di Azure Key Vault già disponibile per generare o importare una chiave da usare per la crittografia dei dati. L'istanza di Azure Key Vault deve essere configurata come recuperabile per proteggere la chiave e l'accesso ai dati in Monitoraggio di Azure. È possibile verificare questa configurazione nelle proprietà dell'istanza di Key Vault. Devono essere abilitate sia *Eliminazione temporanea* che *Protezione dall'eliminazione*.

![Impostazioni Eliminazione temporanea e Protezione dall'eliminazione](media/customer-managed-keys/soft-purge-protection.png)

Queste impostazioni possono essere aggiornate in Key Vault tramite l'interfaccia della riga di comando e PowerShell:

- [eliminazione temporanea](../../key-vault/general/soft-delete-overview.md)
- [Protezione dall'eliminazione](../../key-vault/general/soft-delete-overview.md#purge-protection) protegge dall'eliminazione forzata del segreto/insieme di credenziali anche dopo l'eliminazione temporanea

## <a name="create-cluster"></a>Creare cluster

I cluster [supportano](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)due tipi di identità gestite: assegnato dal sistema e assegnato dall'utente, mentre una singola identità può essere definita in un cluster a seconda dello scenario. 
- L'identità gestita assegnata dal sistema è più semplice e viene generata automaticamente con la creazione del cluster quando l'identità `type` è impostata su "*SystemAssigned*". Questa identità può essere usata in un secondo momento per concedere all'archiviazione l'accesso Key Vault per le operazioni di wrapping e annullamento del wrapping. 
  
  Impostazioni di identità nel cluster per l'identità gestita assegnata dal sistema
  ```json
  {
    "identity": {
      "type": "SystemAssigned"
      }
  }
  ```

- Se si vuole configurare la chiave gestita dal cliente durante la creazione del cluster, è necessario disporre di una chiave e di un'identità assegnata dall'utente concesse in anticipo nel Key Vault, quindi creare il cluster con queste impostazioni: identity `type` come "*UserAssigned*", con l'ID risorsa dell'identità. `UserAssignedIdentities` 

  Impostazioni di identità nel cluster per l'identità gestita assegnata dall'utente
  ```json
  {
  "identity": {
  "type": "UserAssigned",
    "userAssignedIdentities": {
      "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.ManagedIdentity/UserAssignedIdentities/<cluster-assigned-managed-identity>"
      }
  }
  ```

> [!IMPORTANT]
> Non è possibile usare l'identità gestita assegnata dall'utente se l'Key Vault è in Private-Link (rete virtuale). In questo scenario è possibile usare l'identità gestita assegnata dal sistema.

Seguire la procedura illustrata [nell'articolo Cluster dedicati](./logs-dedicated-clusters.md#creating-a-cluster). 

## <a name="grant-key-vault-permissions"></a>Concedere le autorizzazioni di Key Vault

Creare criteri di accesso Key Vault per concedere le autorizzazioni al cluster. Queste autorizzazioni vengono usate dall'archivio Monitoraggio di Azure sottostante. Aprire il Key Vault in portale di Azure fare clic su *"Criteri* di accesso" e *quindi su "+ Aggiungi* criteri di accesso" per creare un criterio con queste impostazioni:

- Autorizzazioni chiave: selezionare *'Get',* *'Wrap Key'* e *'Unwrap Key'.*
- Selezionare l'entità: a seconda del tipo di identità usato nel cluster (identità gestita assegnata dal sistema o dall'utente), immettere il nome del cluster o l'ID entità cluster per l'identità gestita assegnata dal sistema o il nome dell'identità gestita assegnata dall'utente.

![concedere le autorizzazioni di Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

L'autorizzazione *Recupera* è necessaria per verificare che l'istanza di Key Vault sia configurata come recuperabile per proteggere la chiave e l'accesso ai dati di Monitoraggio di Azure.

## <a name="update-cluster-with-key-identifier-details"></a>Aggiornare il cluster con i dettagli dell'identificatore di chiave

Tutte le operazioni nel cluster richiedono `Microsoft.OperationalInsights/clusters/write` l'autorizzazione per l'azione. Questa autorizzazione può essere concessa tramite il proprietario o il collaboratore che contiene l'azione o tramite il ruolo `*/write` Collaboratore di Log Analytics che contiene l'azione. `Microsoft.OperationalInsights/*`

Questo passaggio aggiorna Monitoraggio di Azure archiviazione con la chiave e la versione da usare per la crittografia dei dati. Quando viene aggiornata, la nuova chiave viene usata per eseguire il wrapping e annullare il wrapping della chiave di archiviazione (AEK).

Selezionare la versione corrente della chiave in Azure Key Vault ottenere i dettagli dell'identificatore di chiave.

![Concedere le autorizzazioni di Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Aggiornare KeyVaultProperties nel cluster con i dettagli dell'identificatore di chiave.

>[!NOTE]
>La rotazione delle chiavi supporta due modalità: rotazione [](#key-rotation) automatica o aggiornamento esplicito della versione della chiave. Vedere Rotazione delle chiavi per determinare l'approccio migliore.

L'operazione è asincrona e il completamento può richiedere del tempo.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/D

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
  },
  "sku": {
    "name": "CapacityReservation",
    "capacity": 1000
  }
}
```

**Risposta**

La propagazione della chiave richiede alcuni minuti. È possibile controllare lo stato di aggiornamento in due modi:
1. Copiare il valore dell'URL di Azure-AsyncOperation dalla risposta e seguire la [verifica dello stato delle operazioni asincrone](#asynchronous-operations-and-status-check).
2. Inviare una richiesta GET nel cluster ed esaminare le *proprietà KeyVaultProperties.* La chiave aggiornata di recente dovrebbe restituire nella risposta.

Una risposta alla richiesta GET dovrebbe essere simile alla seguente al termine dell'aggiornamento della chiave: 202 (Accettato) e intestazione
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

---

## <a name="link-workspace-to-cluster"></a>Collegare l'area di lavoro al cluster

> [!IMPORTANT]
> Questo passaggio deve essere eseguito solo dopo il completamento del provisioning del cluster Log Analytics. Se si collegano aree di lavoro e si importano dati prima del provisioning, i dati inseriti verranno eliminati e non saranno recuperabili.

Per eseguire questa operazione, che includono e , è necessario disporre delle autorizzazioni di "scrittura" sia per l'area di lavoro che per il `Microsoft.OperationalInsights/workspaces/write` `Microsoft.OperationalInsights/clusters/write` cluster.

Seguire la procedura illustrata [nell'articolo Cluster dedicati](./logs-dedicated-clusters.md#link-a-workspace-to-cluster).

## <a name="key-revocation"></a>Revoca della chiave

> [!IMPORTANT]
> - Il modo consigliato per revocare l'accesso ai dati è disabilitare la chiave o eliminare i criteri di accesso nel Key Vault.
> - L'impostazione di del cluster su "None" revoca anche l'accesso ai dati, ma questo approccio non è consigliato perché non è possibile ripristinare la revoca quando si ripristina nel cluster senza aprire la richiesta di `identity` `type` `identity` supporto.

L'archiviazione cluster rispetterà sempre le modifiche apportate alle autorizzazioni chiave entro un'ora o prima e l'archiviazione non sarà più disponibile. I nuovi dati inseriti nelle aree di lavoro collegate al cluster vengono eliminati e non saranno recuperabili, i dati diventano inaccessibili e le query su queste aree di lavoro hanno esito negativo. I dati inseriti in precedenza rimangono nell'archiviazione purché il cluster e le aree di lavoro non siano eliminati. I dati inaccessibili sono regolati dai criteri di conservazione dei dati e verranno eliminati al raggiungimento della scadenza della conservazione. I dati inseriti negli ultimi 14 giorni vengono anche mantenuti nella cache ad accesso frequente (con supporto SSD) per un efficace funzionamento del motore di query. Questa operazione viene eliminata durante l'operazione di revoca della chiave e diventa inaccessibile.

L'archiviazione del cluster controlla periodicamente il Key Vault per tentare di annullare il wrapping della chiave di crittografia e, una volta eseguito l'accesso, l'inserimento dei dati e la query vengono ripresi entro 30 minuti.

## <a name="key-rotation"></a>Rotazione delle chiavi

La rotazione delle chiavi ha due modalità: 
- Rotazione automatica: quando si aggiorna il cluster con ma si omette la proprietà o lo si imposta su , l'archiviazione userà automaticamente ```"keyVaultProperties"``` ```"keyVersion"``` le versioni più ```""``` recenti.
- Aggiornamento esplicito della versione della chiave: quando si aggiorna il cluster e si specifica la versione della chiave nella proprietà , qualsiasi nuova versione della chiave richiede un aggiornamento esplicito nel cluster, vedere Aggiornare il cluster con i dettagli ```"keyVersion"``` ```"keyVaultProperties"``` dell'identificatore di [chiave](#update-cluster-with-key-identifier-details). Se si genera una nuova versione della chiave Key Vault ma non la si aggiorna nel cluster, l'archiviazione cluster di Log Analytics continuerà a usare la chiave precedente. Se si disabilita o si elimina la chiave precedente prima di aggiornare la nuova chiave nel cluster, si entra nello stato [di revoca della](#key-revocation) chiave.

Tutti i dati rimarranno accessibili dopo l'operazione di rotazione della chiave perché i dati vengono sempre crittografati con la chiave di crittografia dell'account (AEK), mentre la chiave AEK viene ora crittografata con la nuova versione della chiave di crittografia della chiave (KEK) in Key Vault.

## <a name="customer-managed-key-for-saved-queries"></a>Chiave gestita dal cliente per le query salvate

Il linguaggio di query usato in Log Analytics è espressivo e può contenere informazioni riservate nei commenti aggiunti alle query o nella sintassi di query. Alcune organizzazioni richiedono che tali informazioni vengono mantenute protette in base ai criteri delle chiavi gestite dal cliente ed è necessario salvare le query crittografate con la chiave. Monitoraggio di Azure consente di archiviare le query *saved-searches* e *log-alerts* crittografate con la chiave nell'account di archiviazione quando si è connessi all'area di lavoro. 

> [!NOTE]
> Le query di Log Analytics possono essere salvate in vari archivi a seconda dello scenario usato. Le query rimangono crittografate con la chiave Microsoft negli scenari seguenti, indipendentemente dalla configurazione della chiave gestita dal cliente: cartelle di lavoro in Monitoraggio di Azure, dashboard di Azure, App per la logica di Azure, Azure Notebooks e runbook di Automazione.

Quando si utilizza Bring Your Own Storage (BYOS) e lo si collega all'area di lavoro, il servizio carica le query *saved-searches* e *log-alerts* nell'account di archiviazione. Ciò significa che è possibile [](../../storage/common/customer-managed-keys-overview.md) controllare l'account di archiviazione e i criteri di crittografia dei dati in pausa usando la stessa chiave che si usa per crittografare i dati nel cluster di Log Analytics o una chiave diversa. Si sarà tuttavia responsabili dei costi associati a tale account di archiviazione. 

**Considerazioni prima di impostare la chiave gestita dal cliente per le query**
* È necessario avere le autorizzazioni di scrittura sia per l'area di lavoro che per l'account di archiviazione
* Assicurarsi di creare l'account di archiviazione nella stessa area in cui si trova l'area di lavoro Log Analytics
* Le *ricerche salvate nell'archiviazione* vengono considerate artefatti del servizio e il relativo formato può cambiare
* Le *ricerche di salvataggio esistenti vengono* rimosse dall'area di lavoro. Copiare *ed eventuali ricerche salvate* necessarie prima della configurazione. È possibile visualizzare le *ricerche salvate* usando  [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch)
* La cronologia delle query non è supportata e non sarà possibile visualizzare le query eseguite
* È possibile collegare un singolo account di archiviazione all'area di lavoro allo scopo di salvare le query, ma è possibile usare sia query *salvate che* query *di log-alerts*
* L'aggiunta al dashboard non è supportata

**Configurare BYOS per le query di ricerca salvate**

Collegare un account di archiviazione per *Query all'area* di lavoro. Le *query di* ricerca salvate vengono salvate nell'account di archiviazione. 

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/D

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Dopo la configurazione, qualsiasi nuova query *di ricerca* salvata verrà salvata nell'archiviazione.

**Configurare BYOS per le query di log-alerts**

Collegare un account di archiviazione per *Avvisi all'area* di lavoro. Le *query di log-alerts* vengono salvate nell'account di archiviazione. 

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/D

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Dopo la configurazione, qualsiasi nuova query di avviso verrà salvata nell'archiviazione.

## <a name="customer-lockbox-preview"></a>Customer Lockbox (anteprima)

Lockbox consente di approvare o rifiutare la richiesta del tecnico Microsoft di accedere ai dati durante una richiesta di supporto.

In Monitoraggio di Azure questo controllo sui dati nelle aree di lavoro collegate al cluster dedicato di Log Analytics. Il controllo Lockbox si applica ai dati archiviati in un cluster dedicato di Log Analytics in cui viene mantenuto isolato negli account di archiviazione del cluster nella sottoscrizione protetta da Lockbox.  

Altre informazioni sui [Customer Lockbox per Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md)

## <a name="customer-managed-key-operations"></a>Customer-Managed operazioni chiave

Customer-Managed chiave viene fornita nel cluster dedicato e queste operazioni sono indicate [nell'articolo cluster dedicato](./logs-dedicated-clusters.md#change-cluster-properties)

- Ottenere tutti i cluster nel gruppo di risorse  
- Ottenere tutti i cluster nella sottoscrizione
- Aggiornare *la prenotazione della* capacità nel cluster
- Aggiornare *billingType* nel cluster
- Scollegare un'area di lavoro dal cluster
- Eliminare il cluster

## <a name="limitations-and-constraints"></a>Limiti e vincoli

- Il numero massimo di cluster per area e sottoscrizione è 2

- Il numero massimo di aree di lavoro che possono essere collegate a un cluster è 1000

- È possibile collegare un'area di lavoro al cluster e quindi scollegarla. Il numero di operazioni di collegamento all'area di lavoro in una determinata area di lavoro è limitato a 2 in un periodo di 30 giorni.

- La crittografia della chiave gestita dal cliente si applica ai dati appena inseriti dopo il tempo di configurazione. I dati inseriti prima della configurazione rimangono crittografati con la chiave Microsoft. È possibile eseguire facilmente query sui dati inseriti prima e dopo la configurazione della chiave gestita dal cliente.

- Il Azure Key Vault deve essere configurato come recuperabile. Queste proprietà non sono abilitate per impostazione predefinita e devono essere configurate tramite l'interfaccia della riga di comando o PowerShell:<br>
  - [eliminazione temporanea](../../key-vault/general/soft-delete-overview.md)
  - [La protezione dall'eliminazione](../../key-vault/general/soft-delete-overview.md#purge-protection) deve essere attivata per proteggersi dall'eliminazione forzata del segreto o dell'insieme di credenziali anche dopo l'eliminazione definitiva.

- Lo spostamento del cluster in un altro gruppo di risorse o sottoscrizione non è attualmente supportato.

- Il Azure Key Vault, il cluster e le aree di lavoro devono essere nella stessa area e nello stesso tenant Azure Active Directory (Azure AD), ma possono essere in sottoscrizioni diverse.

- Lockbox non è attualmente disponibile in Cina. 

- [La crittografia doppia](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) viene configurata automaticamente per i cluster creati a partire da ottobre 2020 nelle aree supportate. È possibile verificare se il cluster è configurato per la crittografia doppia inviando una richiesta GET nel cluster e osservando che il valore è per i cluster con la crittografia `isDoubleEncryptionEnabled` `true` Doppia abilitata. 
  - Se si crea un cluster e viene visualizzato l'errore "<region-name> doesn't support Double Encryption for clusters", è comunque possibile creare il cluster senza crittografia doppia aggiungendo nel corpo della richiesta `"properties": {"isDoubleEncryptionEnabled": false}` REST.
  - Non è possibile modificare l'impostazione di crittografia doppia dopo la creazione del cluster.

  - Se il cluster è impostato con l'identità gestita assegnata dall'utente, l'impostazione con sospende il cluster e impedisce l'accesso ai dati, ma non è possibile ripristinare la revoca e attivare il cluster senza aprire la richiesta di `UserAssignedIdentities` `None` supporto. Questa limitazione viene applicata all'identità gestita assegnata dal sistema.

  - Non è possibile usare la chiave gestita dal cliente con l'identità gestita assegnata dall'utente se l'Key Vault è in Private-Link (rete virtuale). In questo scenario è possibile usare l'identità gestita assegnata dal sistema.

## <a name="troubleshooting"></a>Risoluzione dei problemi

- Comportamento con la disponibilità di Key Vault
  - Nel funzionamento normale: l'archiviazione memorizza la chiave AEK nella cache per brevi periodi di tempo e torna a Key Vault per annullare il wrapping periodicamente.
    
  - Errori di connessione temporanei: l'archiviazione gestisce gli errori temporanei (timeout, errori di connessione, problemi relativi a DNS) consentendo alle chiavi di rimanere nella cache per un po' più di tempo, superando così piccoli problemi di disponibilità. Le funzionalità di query e inserimento proseguono senza interruzioni.
    
  - Sito Live: una indisponibilità di circa 30 minuti comporterà la mancata disponibilità dell'account di archiviazione. La funzionalità di query non sarà disponibile e i dati inseriti verranno memorizzati nella cache per diverse ore usando la chiave Microsoft per evitare la perdita dei dati. Quando l'accesso Key Vault viene ripristinato, la query diventa disponibile e i dati temporanei memorizzati nella cache vengono inseriti nell'archivio dati e crittografati con la chiave gestita dal cliente.

  - Frequenza di accesso a Key Vault: la frequenza con cui l'archivio di Monitoraggio di Azure accede a Key Vault per le operazioni di wrapping e annullamento del wrapping è compresa tra 6 e 60 secondi.

- Se si aggiorna il cluster mentre il cluster è in stato di provisioning o aggiornamento, l'aggiornamento avrà esito negativo.

- Se si verifica un errore di conflitto durante la creazione di un cluster, è possibile che il cluster sia stato eliminato negli ultimi 14 giorni e sia in un periodo di eliminazione soft. Il nome del cluster rimane riservato durante il periodo di eliminazione soft e non è possibile creare un nuovo cluster con tale nome. Il nome viene rilasciato dopo il periodo di eliminazione temporaneamente quando il cluster viene eliminato definitivamente.

- Il collegamento dell'area di lavoro al cluster avrà esito negativo se è collegato a un altro cluster.

- Se si crea un cluster e si specifica keyVaultProperties immediatamente, l'operazione potrebbe non riuscire perché i criteri di accesso non possono essere definiti fino a quando non viene assegnata l'identità di sistema al cluster.

- Se si aggiorna il cluster esistente con KeyVaultProperties e i criteri di accesso alla chiave "Get" non sono presenti Key Vault, l'operazione avrà esito negativo.

- Se non si riesce a distribuire il cluster, verificare che le aree di lavoro Azure Key Vault, cluster e Log Analytics collegate siano nella stessa area. Le sottoscrizioni possono essere diverse.

- Se si aggiorna la versione della chiave in Key Vault e non si aggiornano i dettagli del nuovo identificatore di chiave nel cluster, il cluster di Log Analytics continuerà a usare la chiave precedente e i dati diventeranno inaccessibili. Aggiornare i dettagli del nuovo identificatore di chiave nel cluster per riprendere l'inserimento dei dati e la possibilità di eseguire query sui dati.

- Alcune operazioni sono lunghe e possono richiedere del tempo, ad esempio la creazione del cluster, l'aggiornamento delle chiavi del cluster e l'eliminazione del cluster. È possibile controllare lo stato dell'operazione in due modi:
  1. Quando si usa REST, copiare il Azure-AsyncOperation URL dalla risposta e seguire il controllo dello stato [delle operazioni asincrone](#asynchronous-operations-and-status-check).
  2. Inviare la richiesta GET al cluster o all'area di lavoro e osservare la risposta. Ad esempio, l'area di lavoro non collegata non *includerà clusterResourceId* nelle *funzionalità*.

- messaggi di errore
  
  **Creazione del cluster**
  -  400 -- Il nome del cluster non è valido. Il nome del cluster può contenere caratteri a-z, A-Z, 0-9 e lunghezza di 3-63.
  -  400 -- Il corpo della richiesta è Null o in formato non valido.
  -  400 -- Il nome dello SKU non è valido. Impostare il nome dello SKU su capacityReservation.
  -  400 -- La capacità è stata fornita ma lo SKU non è capacityReservation. Impostare il nome dello SKU su capacityReservation.
  -  400 -- Capacità mancante nello SKU. Impostare Il valore di Capacità su 1000 o su un valore superiore nei passaggi di 100 (GB).
  -  400 -- La capacità nello SKU non è nell'intervallo. Deve essere almeno 1000 e fino alla capacità massima consentita disponibile in "Utilizzo e costo stimato" nell'area di lavoro.
  -  400 -- La capacità è bloccata per 30 giorni. La riduzione della capacità è consentita 30 giorni dopo l'aggiornamento.
  -  400 -- Non è stato impostato alcun SKU. Impostare il nome dello SKU su capacityReservation e il valore capacity su 1000 o su un valore superiore nei passaggi di 100 (GB).
  -  400 -- Identity è null o vuoto. Impostare Identity con il tipo systemAssigned.
  -  400 -- KeyVaultProperties viene impostato al momento della creazione. Aggiornare KeyVaultProperties dopo la creazione del cluster.
  -  400 -- L'operazione non può essere eseguita ora. L'operazione asincrona si trova in uno stato diverso da riuscito. Il cluster deve completare l'operazione prima di eseguire qualsiasi operazione di aggiornamento.

  **Aggiornamento del cluster**
  -  400 -- Il cluster è in stato di eliminazione. Operazione asincrona in corso. Il cluster deve completare l'operazione prima di eseguire qualsiasi operazione di aggiornamento.
  -  400 -- KeyVaultProperties non è vuoto ma ha un formato non valido. Vedere [aggiornamento dell'identificatore di chiave](#update-cluster-with-key-identifier-details).
  -  400 -- Impossibile convalidare la chiave Key Vault. Potrebbe essere dovuto alla mancanza di autorizzazioni o a quando la chiave non esiste. Verificare di aver [impostato i criteri di chiave](#grant-key-vault-permissions) e di accesso Key Vault.
  -  400 -- La chiave non è recuperabile. Key Vault deve essere impostato su Soft-delete e Purge-protection. Vedere [Key Vault documentazione](../../key-vault/general/soft-delete-overview.md)
  -  400 -- L'operazione non può essere eseguita ora. Attendere il completamento dell'operazione asincrona e riprovare.
  -  400 -- Il cluster è in stato di eliminazione. Attendere il completamento dell'operazione asincrona e riprovare.

  **Ottenere il cluster**
    -  404 -- Cluster non trovato. È possibile che il cluster sia stato eliminato. Se si tenta di creare un cluster con tale nome e si verifica un conflitto, il cluster viene eliminato automaticamente per 14 giorni. È possibile contattare il supporto tecnico per ripristinarlo o usare un altro nome per creare un nuovo cluster. 

  **Eliminazione del cluster**
    -  409 - Non è possibile eliminare un cluster mentre è in stato di provisioning. Attendere il completamento dell'operazione asincrona e riprovare.

  **Collegamento all'area di lavoro**
  -  404 -- Area di lavoro non trovata. L'area di lavoro specificata non esiste o è stata eliminata.
  -  409 : operazione di collegamento o scollegamento dell'area di lavoro in corso.
  -  400 -- Cluster non trovato, il cluster specificato non esiste o è stato eliminato. Se si tenta di creare un cluster con tale nome e si verifica un conflitto, il cluster è in modalità di eliminazione soft per 14 giorni. È possibile contattare il supporto tecnico per recuperarlo.

  **Scollegamento dell'area di lavoro**
  -  404 -- Area di lavoro non trovata. L'area di lavoro specificata non esiste o è stata eliminata.
  -  409 : operazione di collegamento o scollegamento dell'area di lavoro in corso.
## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [fatturazione di cluster dedicati di Log Analytics](./manage-cost-storage.md#log-analytics-dedicated-clusters)
- Informazioni sulla [progettazione appropriata delle aree di lavoro Log Analytics](./design-logs-deployment.md)