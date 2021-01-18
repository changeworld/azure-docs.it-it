---
title: Il monitoraggio di Azure registra i cluster dedicati
description: I clienti che inseriscono più di 1 TB un giorno di dati di monitoraggio possono usare i cluster dedicati anziché quelli condivisi
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: 93b05a5535b80d0e0d1a07c88aa9b19052f1b703
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562676"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Il monitoraggio di Azure registra i cluster dedicati

Il monitoraggio di Azure registra i cluster dedicati è un'opzione di distribuzione che Abilita funzionalità avanzate per i clienti dei log di monitoraggio di Azure. I clienti con cluster dedicati possono scegliere le aree di lavoro da ospitare in questi cluster.

Le funzionalità che richiedono cluster dedicati sono:

- **[Chiavi gestite dal cliente](../platform/customer-managed-keys.md)** : crittografare i dati del cluster usando chiavi fornite e controllate dal cliente.
- **[Archivio](../platform/customer-managed-keys.md#customer-lockbox-preview)** dati: i clienti possono controllare le richieste di accesso ai tecnici del supporto tecnico Microsoft.
- La **[crittografia doppia](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)** protegge da uno scenario in cui uno degli algoritmi o delle chiavi di crittografia può essere compromesso. In questo caso, il livello di crittografia aggiuntivo continua a proteggere i dati.
- Più **[aree di lavoro](../log-query/cross-workspace-query.md)** : se un cliente usa più di un'area di lavoro per la produzione, potrebbe avere senso usare un cluster dedicato. Le query tra aree di lavoro verranno eseguite più velocemente se tutte le aree di lavoro si trovano nello stesso cluster. Potrebbe anche essere più conveniente usare un cluster dedicato, poiché i livelli di prenotazione di capacità assegnati prendono in considerazione tutti gli inserimenti del cluster e si applicano a tutte le aree di lavoro, anche se alcune di esse sono di piccole dimensioni e non sono idonee per lo sconto sulla prenotazione della capacità.

I cluster dedicati richiedono ai clienti di eseguire il commit usando una capacità di almeno 1 TB di inserimento dati al giorno. La migrazione a un cluster dedicato è semplice. Non si verificano perdite di dati o interruzioni del servizio. 

> [!IMPORTANT]
> I cluster dedicati sono approvati e supportati completamente per le distribuzioni di produzione. Tuttavia, a causa dei vincoli temporanei di capacità, per usare la funzionalità è necessario eseguire la registrazione preliminare. Usare i contatti in Microsoft per fornire gli ID delle sottoscrizioni.

## <a name="management"></a>Gestione 

I cluster dedicati vengono gestiti tramite una risorsa di Azure che rappresenta i cluster di log di monitoraggio di Azure. Tutte le operazioni vengono eseguite su questa risorsa usando PowerShell o l'API REST.

Una volta creato, il cluster può essere configurato e le aree di lavoro collegate. Quando un'area di lavoro è collegata a un cluster, i nuovi dati inviati all'area di lavoro si trovano nel cluster. Solo le aree di lavoro che si trovano nella stessa area del cluster possono essere collegate al cluster. Le aree di lavoro possono essere diversamente da un cluster con alcune limitazioni. Altre informazioni su queste limitazioni sono incluse in questo articolo. 

I dati inseriti in cluster dedicati vengono crittografati due volte, una volta a livello di servizio usando chiavi gestite da Microsoft o chiavi [gestite dal cliente](../platform/customer-managed-keys.md)e una volta a livello di infrastruttura usando due algoritmi di crittografia diversi e due chiavi diverse. La [crittografia doppia](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) protegge da uno scenario in cui uno degli algoritmi o delle chiavi di crittografia può essere compromesso. In questo caso, il livello di crittografia aggiuntivo continua a proteggere i dati. Il cluster dedicato consente inoltre di proteggere i dati con il controllo dell' [archivio protetto](../platform/customer-managed-keys.md#customer-lockbox-preview) .

Per tutte le operazioni a livello di cluster è necessaria l' `Microsoft.OperationalInsights/clusters/write` autorizzazione azione per il cluster. Questa autorizzazione può essere concessa tramite il proprietario o il collaboratore che contiene l' `*/write` azione o tramite il ruolo di collaboratore log Analytics che contiene l' `Microsoft.OperationalInsights/*` azione. Per altre informazioni sulle autorizzazioni di Log Analytics, vedere [gestire l'accesso ai dati e alle aree di lavoro di log in monitoraggio di Azure](../platform/manage-access.md). 


## <a name="cluster-pricing-model"></a>Modello di determinazione prezzi cluster

Log Analytics cluster dedicati usano un modello di prezzo per la prenotazione della capacità di almeno 1000 GB/giorno. Qualsiasi utilizzo sopra il livello di prenotazione verrà fatturato in base alla tariffa con pagamento in base al consumo.  Le informazioni sui prezzi per la prenotazione della capacità sono disponibili nella [pagina dei prezzi di monitoraggio di Azure]( https://azure.microsoft.com/pricing/details/monitor/).  

Il livello di prenotazione della capacità del cluster viene configurato tramite a livello di codice con Azure Resource Manager utilizzando il `Capacity` parametro in `Sku` . La `Capacity` viene specificata in unità di GB e può includere valori di 1000 GB/giorno o più in incrementi di 100 GB al giorno.

Sono disponibili due modalità di fatturazione per l'utilizzo in un cluster. Questi possono essere specificati dal `billingType` parametro durante la configurazione del cluster. 

1. **Cluster**: in questo caso (impostazione predefinita), la fatturazione per i dati inseriti viene eseguita a livello di cluster. Le quantità di dati inseriti da ogni area di lavoro associata a un cluster vengono aggregate per calcolare la fattura giornaliera per il cluster. 

2. **Aree di lavoro**: i costi di prenotazione della capacità per il cluster sono attribuiti proporzionalmente alle aree di lavoro del cluster, dopo aver tenuto conto delle allocazioni per nodo dal [Centro sicurezza di Azure](../../security-center/index.yml) per ogni area di lavoro.

Se l'area di lavoro usa il piano tariffario per nodo Legacy, quando è collegato a un cluster, verrà fatturato in base ai dati inseriti rispetto alla prenotazione della capacità del cluster e non più per nodo. Le allocazioni di dati per nodo dal centro sicurezza di Azure continueranno a essere applicate.

Per altri dettagli, vedere la pagina relativa alla fatturazione per i cluster dedicati Log Analytics sono disponibili [qui]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters).

## <a name="asynchronous-operations-and-status-check"></a>Operazioni asincrone e controllo dello stato

Alcuni passaggi di configurazione vengono eseguiti in modo asincrono perché non possono essere completati rapidamente. Lo stato in risposta contiene può essere uno dei seguenti:' InProgress ',' Updating ',' deleting ',' SUCCEEDED o ' failed ', incluso il codice di errore. Quando si usa REST, la risposta restituisce inizialmente un codice di stato HTTP 202 (accettato) e un'intestazione con Azure-AsyncOperation proprietà:

```JSON
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

È possibile controllare lo stato dell'operazione asincrona inviando una richiesta GET al valore dell'intestazione Azure-AsyncOperation:

```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

## <a name="creating-a-cluster"></a>Creazione di un cluster

Creare innanzitutto risorse cluster per iniziare a creare un cluster dedicato.

È necessario specificare le proprietà seguenti:

- **Clustername**: usato a scopo amministrativo. Gli utenti non sono esposti a questo nome.
- **ResourceGroupName**: come per qualsiasi risorsa di Azure, i cluster appartengono a un gruppo di risorse. Si consiglia di usare un gruppo di risorse IT centrale perché i cluster sono in genere condivisi da molti team dell'organizzazione. Per altre considerazioni sulla progettazione, vedere [progettazione della distribuzione dei log di monitoraggio di Azure](../platform/design-logs-deployment.md)
- **Località**: un cluster si trova in una determinata area di Azure. Solo le aree di lavoro situate in questa area possono essere collegate a questo cluster.
- **SkuCapacity**: è necessario specificare il livello di *prenotazione della capacità* (SKU) quando si crea una risorsa *cluster* . Il livello di *prenotazione della capacità* può essere compreso tra 1.000 gb e 3.000 GB al giorno. Se necessario, è possibile aggiornarlo nei passaggi 100 in un secondo momento. Se è necessario un livello di prenotazione di capacità superiore a 3.000 GB al giorno, contattare Microsoft all'indirizzo LAIngestionRate@microsoft.com . Per altre informazioni sui costi del cluster, vedere [gestire i costi per i cluster log Analytics](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)

Dopo aver creato la risorsa *cluster* , è possibile modificare proprietà aggiuntive, ad esempio *SKU*, * keyVaultProperties o *billingType*. Per altri dettagli, vedere di seguito.

> [!WARNING]
> La creazione del cluster attiva l'allocazione delle risorse e il provisioning. Il completamento di questa operazione può richiedere fino a un'ora. È consigliabile eseguirlo in modo asincrono.

L'account utente che crea i cluster deve avere l'autorizzazione standard per la creazione di risorse `Microsoft.Resources/deployments/*` di Azure: e l'autorizzazione di scrittura del cluster `(Microsoft.OperationalInsights/clusters/write)` .

### <a name="create"></a>Create 

**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} -AsJob

# Check when the job is done
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST**

*Chiamare* 
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

*Risposta*

Deve essere 202 (accettato) e un'intestazione.

### <a name="check-cluster-provisioning-status"></a>Verificare lo stato del provisioning del cluster

Il provisioning del cluster Log Analytics richiede del tempo per il completamento. È possibile controllare lo stato di provisioning in diversi modi:

- Eseguire Get-AzOperationalInsightsCluster comando PowerShell con il nome del gruppo di risorse e controllare la proprietà ProvisioningState. Il valore è *ProvisioningAccount* durante il provisioning e il completamento è *riuscito* .
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- Copiare il valore dell'URL di Azure-AsyncOperation dalla risposta e seguire la verifica dello stato delle operazioni asincrone.

- Inviare una richiesta GET alla risorsa *Cluster* e osservare il valore di *provisioningState*. Il valore è *ProvisioningAccount* durante il provisioning e il completamento è *riuscito* .

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
   Authorization: Bearer <token>
   ```

   **Risposta**

   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principal-id"
       },
     "sku": {
       "name": "capacityReservation",
       "capacity": 1000,
       "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
       },
     "properties": {
       "provisioningState": "ProvisioningAccount",
       "billingType": "cluster",
       "clusterId": "cluster-id"
       },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

Il GUID *PrincipalId* viene generato dal servizio di gestione delle identità gestito per la risorsa *cluster* .

## <a name="link-a-workspace-to-cluster"></a>Collegare un'area di lavoro al cluster

Quando un'area di lavoro è collegata a un cluster dedicato, i nuovi dati inseriti nell'area di lavoro vengono indirizzati al nuovo cluster mentre i dati esistenti rimangono nel cluster esistente. Se il cluster dedicato è crittografato con chiavi gestite dal cliente (CMK), solo i nuovi dati vengono crittografati con la chiave. Il sistema astrae questa differenza dagli utenti e gli utenti eseguono solo query nell'area di lavoro come di consueto mentre il sistema esegue query tra cluster sul back-end.

Un cluster può essere collegato a un massimo di 100 aree di lavoro. Le aree di lavoro collegate si trovano nella stessa area del cluster. Per proteggere il back-end del sistema ed evitare la frammentazione dei dati, un'area di lavoro non può essere collegata a un cluster più di due volte al mese.

Per eseguire l'operazione di collegamento, è necessario disporre delle autorizzazioni ' Write ' sia per l'area di lavoro che per la risorsa *cluster* :

- Nell'area di lavoro: *Microsoft. OperationalInsights/Workspaces/Write*
- Nella risorsa *cluster* : *Microsoft. OperationalInsights/Clusters/Write*

Oltre agli aspetti di fatturazione, l'area di lavoro collegata mantiene le proprie impostazioni, ad esempio la durata della conservazione dei dati.
L'area di lavoro e il cluster possono trovarsi in sottoscrizioni diverse. È possibile che l'area di lavoro e il cluster si trovino in tenant diversi se Azure Lighthouse viene usato per eseguire il mapping di entrambi a un singolo tenant.

Come qualsiasi operazione del cluster, il collegamento di un'area di lavoro può essere eseguito solo dopo il completamento del provisioning del cluster Log Analytics.

> [!WARNING]
> Il collegamento di un'area di lavoro a un cluster richiede la sincronizzazione di più componenti back-end e la garanzia dell'idratazione della cache. Il completamento di questa operazione può richiedere fino a due ore. Si consiglia di eseguirlo in modo asincrono.


**PowerShell**

Usare il comando di PowerShell seguente per eseguire il collegamento a un cluster:

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```


**REST**

Usare la chiamata REST seguente per il collegamento a un cluster:

*Invia*

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*Risposta*

202 (accettato) e intestazione.

### <a name="check-workspace-link-status"></a>Verifica stato collegamento area di lavoro
  
Se si usano chiavi gestite dal cliente, i dati inseriti vengono archiviati crittografati con la chiave gestita dopo l'operazione di associazione, che può richiedere fino a 90 minuti per il completamento. 

È possibile controllare lo stato dell'associazione dell'area di lavoro in due modi:

- Copiare il valore dell'URL di Azure-AsyncOperation dalla risposta e seguire la verifica dello stato delle operazioni asincrone.

- Eseguire un'operazione get sull'area di lavoro e osservare se la proprietà *clusterResourceId* è presente nella risposta in *features*.

**CLI**

```azurecli
az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

**REST**

*Chiamare*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2020-08-01
Authorization: Bearer <token>
```

*Risposta*

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="change-cluster-properties"></a>Modificare le proprietà del cluster

Dopo aver creato la risorsa *cluster* ed è stato effettuato il provisioning completo, è possibile modificare le proprietà aggiuntive a livello di cluster usando PowerShell o l'API REST. Oltre alle proprietà disponibili durante la creazione del cluster, è possibile impostare proprietà aggiuntive solo dopo il provisioning del cluster:

- **keyVaultProperties** : aggiorna la chiave in Azure Key Vault. Vedere [Aggiorna cluster con i dettagli dell'identificatore di chiave](../platform/customer-managed-keys.md#update-cluster-with-key-identifier-details). Contiene i parametri seguenti: *KeyVaultUri*, *nome di codice*, *versione della versione*. 
- **billingType** : la proprietà *billingType* determina l'attribuzione della fatturazione per la risorsa *cluster* e i relativi dati:
  - **Cluster** (impostazione predefinita): i costi di prenotazione della capacità per il cluster sono attribuiti alla risorsa *cluster* .
  - **Aree di lavoro** : i costi di prenotazione della capacità per il cluster sono attribuiti proporzionalmente alle aree di lavoro del cluster, con la fatturazione della risorsa *cluster* di parte dell'utilizzo se il totale dei dati inseriti per la giornata è inferiore alla prenotazione della capacità. Per ulteriori informazioni sul modello di determinazione prezzi del cluster, vedere [log Analytics cluster dedicati](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) . 

> [!NOTE]
> La proprietà *billingType* non è supportata in PowerShell.

### <a name="get-all-clusters-in-resource-group"></a>Ottenere tutti i cluster nel gruppo di risorse
  
**CLI**

```azurecli
az monitor log-analytics cluster list --resource-group "resource-group-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
```

**REST**

*Chiamare*

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

*Risposta*
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

### <a name="get-all-clusters-in-subscription"></a>Ottenere tutti i cluster nella sottoscrizione

**CLI**

```azurecli
az monitor log-analytics cluster list
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster
```

**REST**

*Chiamare*

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
Authorization: Bearer <token>
```
    
*Risposta*
    
Uguale a quello per i cluster in un gruppo di risorse, ma nell'ambito della sottoscrizione.



### <a name="update-capacity-reservation-in-cluster"></a>Aggiornare la prenotazione di capacità nel cluster

Quando il volume di dati nelle aree di lavoro collegate cambia nel tempo e si vuole aggiornare il livello di prenotazione di capacità in modo appropriato. La capacità è specificata in unità di GB e può avere valori di 1000 GB/giorno o più in incrementi di 100 GB al giorno. Si noti che non è necessario fornire il corpo della richiesta REST completo, ma deve includere lo SKU.

**CLI**

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity 1000
```

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity 1000
```

**REST**

*Chiamare*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 2000
    }
  }
  ```

### <a name="update-billingtype-in-cluster"></a>Aggiornare billingType nel cluster

La proprietà *billingType* determina l'attribuzione della fatturazione per il cluster e i relativi dati:
- *cluster* (impostazione predefinita): la fatturazione viene attribuita alla sottoscrizione che ospita la risorsa Cluster
- *workspaces*: la fatturazione viene attribuita alle sottoscrizioni che ospitano le aree di lavoro in modo proporzionale

**REST**

*Chiamare*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ```

### <a name="unlink-a-workspace-from-cluster"></a>Scollegare un'area di lavoro dal cluster

È possibile scollegare un'area di lavoro da un cluster. Dopo aver scollegato un'area di lavoro dal cluster, i nuovi dati associati a questa area di lavoro non vengono inviati al cluster dedicato. Inoltre, la fatturazione dell'area di lavoro non viene più eseguita tramite il cluster. I dati obsoleti dell'area di lavoro scollegata potrebbero essere lasciati nel cluster. Se questi dati vengono crittografati con chiavi gestite dal cliente (CMK), i segreti Key Vault vengono conservati. Il sistema estrae questa modifica da Log Analytics utenti. Gli utenti possono semplicemente eseguire query sull'area di lavoro come di consueto. Il sistema esegue query tra cluster sul back-end in base alle esigenze senza alcuna indicazione per gli utenti.  

> [!WARNING] 
> È previsto un limite di due operazioni di collegamento per un'area di lavoro specifica all'interno di un mese. Prendere in considerazione e pianificare lo scollegamento delle azioni di conseguenza.

**CLI**

```azurecli
az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --workspace-name "MyWorkspace" --name cluster
```

**PowerShell**

Usare il comando di PowerShell seguente per scollegare un'area di lavoro dal cluster:

```powershell
# Unlink a workspace from cluster
Remove-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster
```

### <a name="delete-cluster"></a>Eliminare il cluster

È possibile eliminare una risorsa cluster dedicata. Prima di eliminarlo, è necessario scollegare tutte le aree di lavoro dal cluster. Per eseguire questa operazione sono necessarie le autorizzazioni di scrittura per la risorsa *Cluster*. 

Una volta eliminata la risorsa cluster, il cluster fisico entra in un processo di ripulitura ed eliminazione. L'eliminazione di un cluster comporta l'eliminazione di tutti i dati archiviati nel cluster. I dati possono provenire dalle aree di lavoro collegate al cluster nel passato.

Una risorsa *Cluster* eliminata negli ultimi 14 giorni è in stato di eliminazione temporanea e può essere recuperata con i relativi dati. Poiché tutte le aree di lavoro sono state dissociate dalla risorsa *cluster* con l'eliminazione di risorse *cluster* , è necessario riassociare le aree di lavoro dopo il ripristino. L'operazione di ripristino non può essere eseguita dall'utente per contattare il canale Microsoft o il supporto per le richieste di ripristino.

Entro 14 giorni dopo l'eliminazione, il nome della risorsa cluster è riservato e non può essere usato da altre risorse.

> [!WARNING] 
> È previsto un limite di tre cluster per sottoscrizione. I cluster sia attivi che eliminati temporaneamente vengono conteggiati come parte di questo. I clienti non devono creare procedure ricorrenti per la creazione e l'eliminazione di cluster. Ha un impatto significativo sui sistemi back-end Log Analytics.

**PowerShell**

Usare il comando di PowerShell seguente per eliminare un cluster:

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

**REST**

Usare la chiamata REST seguente per eliminare un cluster:

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Risposta**

  200 - OK

## <a name="limits-and-constraints"></a>Limiti e i vincoli

- Il numero massimo di cluster per area e sottoscrizione è 2

- Il numero massimo di aree di lavoro collegate al cluster è 1000

- È possibile collegare un'area di lavoro al cluster e quindi scollegarla. Il numero di operazioni di collegamento dell'area di lavoro su un'area di lavoro specifica è limitato a 2 in un periodo di 30 giorni.

- Il collegamento dell'area di lavoro al cluster deve essere eseguito solo dopo aver verificato il completamento del provisioning del cluster Log Analytics.  I dati inviati all'area di lavoro prima del completamento verranno eliminati e non saranno recuperabili.

- Il passaggio del cluster a un altro gruppo di risorse o a una sottoscrizione non è attualmente supportato.

- Il collegamento dell'area di lavoro al cluster non riuscirà se è collegato a un altro cluster.

- L'archivio protetto non è attualmente disponibile in Cina. 

- La [crittografia doppia](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) viene configurata automaticamente per i cluster creati a partire dal 2020 ottobre nelle aree supportate. È possibile verificare se il cluster è configurato per la crittografia doppia mediante una richiesta GET nel cluster e osservando il `"isDoubleEncryptionEnabled"` valore della proprietà per i `true` cluster con crittografia doppia abilitata. 
  - Se si crea un cluster e si riceve un errore "<Region-Name> non supporta la crittografia doppia per i cluster". è comunque possibile creare il cluster senza crittografia doppia. Aggiungere `"properties": {"isDoubleEncryptionEnabled": false}` la proprietà nel corpo della richiesta REST.
  - Non è possibile modificare l'impostazione di crittografia doppia dopo la creazione del cluster.

## <a name="troubleshooting"></a>Risoluzione dei problemi

- Se si verifica un errore di conflitto durante la creazione di un cluster, è possibile che sia stato eliminato il cluster negli ultimi 14 giorni ed è in uno stato di eliminazione temporanea. Il nome del cluster rimane riservato durante il periodo di eliminazione temporanea e non è possibile creare un nuovo cluster con tale nome. Il nome viene rilasciato dopo il periodo di eliminazione temporanea quando il cluster viene eliminato definitivamente.

- Se si aggiorna il cluster mentre è in corso un'operazione, l'operazione avrà esito negativo.

- Alcune operazioni sono lunghe e possono richiedere del tempo per essere completate, ovvero creazione di cluster, aggiornamento della chiave del cluster e eliminazione del cluster. È possibile controllare lo stato dell'operazione in due modi:
  - Quando si usa REST, copiare il valore di Azure-AsyncOperation URL dalla risposta e seguire la [Verifica dello stato delle operazioni asincrone](#asynchronous-operations-and-status-check).
  - Inviare una richiesta GET a un cluster o a un'area di lavoro e osservare la risposta. Ad esempio, l'area di lavoro scollegata non avrà *clusterResourceId* in *funzionalità*.

- messaggi di errore
  
  Creazione cluster:
  -  400--il nome del cluster non è valido. Il nome del cluster può contenere i caratteri a-z, A-Z, 0-9 e la lunghezza di 3-63.
  -  400: il corpo della richiesta è null o in formato non valido.
  -  400--il nome dello SKU non è valido. Impostare il nome dello SKU su capacityReservation.
  -  400: è stata specificata la capacità, ma lo SKU non è capacityReservation. Impostare il nome dello SKU su capacityReservation.
  -  400: capacità mancante nello SKU. Impostare valore capacità su 1000 o superiore nei passaggi di 100 (GB).
  -  400--la capacità nello SKU non è compresa nell'intervallo. Deve essere minimo 1000 e fino alla capacità massima consentita disponibile in ' utilizzo e costo stimato ' nell'area di lavoro.
  -  400: la capacità è bloccata per 30 giorni. La riduzione della capacità è consentita 30 giorni dopo l'aggiornamento.
  -  400--non è stato impostato alcuno SKU. Impostare il nome dello SKU su capacityReservation e il valore della capacità su 1000 o una versione successiva nei passaggi di 100 (GB).
  -  400--Identity è null o vuoto. Impostare Identity con systemAssigned Type.
  -  400--KeyVaultProperties impostati al momento della creazione. Aggiornare KeyVaultProperties dopo la creazione del cluster.
  -  400: non è possibile eseguire l'operazione ora. L'operazione asincrona è in uno stato diverso da succeeded. Prima di eseguire qualsiasi operazione di aggiornamento, il cluster deve completare l'operazione.

  Aggiornamento cluster
  -  400--il cluster è in stato di eliminazione. L'operazione asincrona è in corso. Prima di eseguire qualsiasi operazione di aggiornamento, il cluster deve completare l'operazione.
  -  400--KeyVaultProperties non è vuoto ma presenta un formato non valido. Vedere [aggiornamento dell'identificatore di chiave](../platform/customer-managed-keys.md#update-cluster-with-key-identifier-details).
  -  400--non è stato possibile convalidare la chiave in Key Vault. La causa potrebbe essere la mancanza di autorizzazioni o la chiave non esiste. Verificare di aver [impostato i criteri di accesso e chiave](../platform/customer-managed-keys.md#grant-key-vault-permissions) in Key Vault.
  -  400--la chiave non è reversibile. Key Vault deve essere impostato su soft-delete ed Purge-Protection. Vedere la [documentazione di Key Vault](../../key-vault/general/soft-delete-overview.md)
  -  400: non è possibile eseguire l'operazione ora. Attendere il completamento dell'operazione asincrona e riprovare.
  -  400--il cluster è in stato di eliminazione. Attendere il completamento dell'operazione asincrona e riprovare.

  Cluster Get:
    -  404: Impossibile trovare il cluster. è possibile che sia stato eliminato. Se si tenta di creare un cluster con tale nome e si verifica un conflitto, il cluster viene eliminato temporaneamente per 14 giorni. È possibile contattare il supporto tecnico per ripristinarlo o usare un altro nome per creare un nuovo cluster. 

  Eliminazione del cluster
    -  409: non è possibile eliminare un cluster in stato di provisioning. Attendere il completamento dell'operazione asincrona e riprovare.

  Collegamento area di lavoro:
  -  404--area di lavoro non trovata. L'area di lavoro specificata non esiste o è stata eliminata.
  -  409--collegamento dell'area di lavoro o operazione di scollegamento del collegamento nel processo.
  -  400--cluster non trovato, il cluster specificato non esiste o è stato eliminato. Se si tenta di creare un cluster con tale nome e si verifica un conflitto, il cluster viene eliminato temporaneamente per 14 giorni. È possibile contattare il supporto tecnico per recuperarlo.

  Scollegamento dell'area di lavoro:
  -  404--area di lavoro non trovata. L'area di lavoro specificata non esiste o è stata eliminata.
  -  409--collegamento dell'area di lavoro o operazione di scollegamento del collegamento nel processo.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [log Analytics la fatturazione del cluster dedicato](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- Informazioni sulla [progettazione corretta delle aree di lavoro log Analytics](../platform/design-logs-deployment.md)
