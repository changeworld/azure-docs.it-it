---
title: Rete virtuale gestita & endpoint privati gestiti
description: Informazioni sulla rete virtuale gestita e sugli endpoint privati gestiti in Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 07/15/2020
ms.openlocfilehash: 1213d5f7421cc71255f29d013fa47878559110ee
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481583"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure Data Factory rete virtuale gestita (anteprima)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra la rete virtuale gestita e gli endpoint privati gestiti in Azure Data Factory.


## <a name="managed-virtual-network"></a>Rete virtuale gestita

Quando si crea un Azure Integration Runtime (IR) all'interno di una rete virtuale gestita di Azure Data Factory, verrà eseguito il provisioning del runtime di integrazione con la rete virtuale gestita e verranno sfruttati gli endpoint privati per connettersi in modo sicuro agli archivi dati supportati. 

La creazione di Azure IR all'interno di una rete virtuale gestita garantisce che il processo di integrazione dei dati sia isolato e sicuro. 

Vantaggi dell'uso della rete virtuale gestita:

- Con una rete virtuale gestita, è possibile eseguire l'offload del carico di lavoro della gestione della rete virtuale per Azure Data Factory. Non è necessario creare una subnet per un Azure Integration Runtime che potrebbe usare molti indirizzi IP privati dalla rete virtuale e che richiederebbe una pianificazione dell'infrastruttura di rete precedente. 
- Non richiede conoscenze approfondite sulla rete di Azure per eseguire integrazioni dei dati in modo sicuro. L'introduzione all'ETL sicuro è invece molto semplificata per i data engineer. 
- La rete virtuale gestita e gli endpoint privati gestiti proteggono dall'esfiltrazione dei dati. 

> [!IMPORTANT]
>Attualmente, la rete virtuale gestita è supportata solo nella stessa area Azure Data Factory area.
 

![Architettura della rete virtuale gestita di AdF](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>Endpoint privati gestiti

Gli endpoint privati gestiti sono endpoint privati creati nella Azure Data Factory virtuale gestita che stabilisce un collegamento privato alle risorse di Azure. Azure Data Factory gestisce questi endpoint privati per conto dell'utente. 

![Nuovo endpoint privato gestito](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory supporta i collegamenti privati. Il collegamento privato consente di accedere ai servizi di Azure (PaaS), ad esempio Archiviazione di Azure, Azure Cosmos DB, Azure Synapse Analytics.

Quando si usa un collegamento privato, il traffico tra gli archivi dati e la rete virtuale gestita attraversa interamente la rete backbone Microsoft. Il collegamento privato protegge da rischi di esfiltrazione dei dati. Per stabilire un collegamento privato a una risorsa, è necessario creare un endpoint privato.

L'endpoint privato usa un indirizzo IP privato nella rete virtuale gestita per accedere in modo efficace al servizio. Gli endpoint privati sono associati a una risorsa specifica in Azure e non all'intero servizio. I clienti possono limitare la connettività a una risorsa specifica approvata dall'organizzazione. Altre informazioni su [collegamenti ed endpoint privati](../private-link/index.yml).

> [!NOTE]
> È consigliabile creare endpoint privati gestiti per connettersi a tutte le origini dati di Azure. 
 
> [!WARNING]
> Se in un archivio dati PaaS (BLOB, ADLS Gen2, Azure Synapse Analytics) è già stato creato un endpoint privato e anche se consente l'accesso da tutte le reti, Azure Data Framework sarà in grado di accedervi solo usando un endpoint privato gestito. Assicurarsi di creare un endpoint privato in tali scenari. 

Una connessione endpoint privato viene creata in uno stato "In sospeso" quando si crea un endpoint privato gestito in Azure Data Factory. Viene avviato un flusso di lavoro di approvazione. Il proprietario della risorsa del collegamento privato ha la responsabilità di approvare la connessione.

![Endpoint privato gestito](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

Se il proprietario approva la connessione, il collegamento privato viene stabilito. In caso contrario, il collegamento privato non verrà stabilito. In entrambi i casi, l'endpoint privato gestito verrà aggiornato con lo stato della connessione.

![Approvare l'endpoint privato gestito](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

Solo un endpoint privato gestito in uno stato approvato può inviare il traffico a una risorsa del collegamento privato specificata.

## <a name="interactive-authoring"></a>Creazione interattiva
Le funzionalità di creazione interattiva vengono usate per funzionalità come la connessione di test, l'elenco di cartelle di esplorazione e l'elenco di tabelle, il ripristino dello schema e l'anteprima dei dati. È possibile abilitare la creazione interattiva durante la creazione o la modifica di un Azure Integration Runtime che si trova nella rete virtuale gestita da AdF. Il servizio back-end preallocerà le risorse di calcolo per le funzionalità di creazione interattive. In caso contrario, il calcolo verrà allocato ogni volta che viene eseguita un'operazione interattiva che può richiedere più tempo. La durata (TTL) per la creazione interattiva è di 60 minuti, ovvero verrà disabilitata automaticamente dopo 60 minuti dall'ultima operazione di creazione interattiva.

![Creazione interattiva](./media/managed-vnet/interactive-authoring.png)

## <a name="create-managed-virtual-network-via-azure-powershell"></a>Creare una rete virtuale gestita tramite Azure PowerShell
```powershell
$subscriptionId = ""
$resourceGroupName = ""
$factoryName = ""
$managedPrivateEndpointName = ""
$integrationRuntimeName = ""
$apiVersion = "2018-06-01"
$privateLinkResourceId = ""

$vnetResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default"
$privateEndpointResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default/managedprivateendpoints/${managedPrivateEndpointName}"
$integrationRuntimeResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/integrationRuntimes/${integrationRuntimeName}"

# Create managed Virtual Network resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${vnetResourceId}"

# Create managed private endpoint resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${privateEndpointResourceId}" -Properties @{
        privateLinkResourceId = "${privateLinkResourceId}"
        groupId = "blob"
    }

# Create integration runtime resource enabled with VNET
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${integrationRuntimeResourceId}" -Properties @{
        type = "Managed"
        typeProperties = @{
            computeProperties = @{
                location = "AutoResolve"
                dataFlowProperties = @{
                    computeType = "General"
                    coreCount = 8
                    timeToLive = 0
                }
            }
        }
        managedVirtualNetwork = @{
            type = "ManagedVirtualNetworkReference"
            referenceName = "default"
        }
    }

```

## <a name="limitations-and-known-issues"></a>Limitazioni e problemi noti
### <a name="supported-data-sources"></a>Origini dati supportate
Le origini dati seguenti sono supportate per la connessione tramite collegamento privato dalla rete virtuale gestita di AdF.
- Archiviazione BLOB di Azure (senza l'account di archiviazione V1)
- Archiviazione tabelle di Azure (senza l'account di archiviazione V1)
- File di Azure (senza l'account di archiviazione V1)
- Azure Data Lake Gen2
- database SQL di Azure (senza Istanza gestita di SQL di Azure)
- Azure Synapse Analytics
- Azure CosmosDB SQL
- Insieme di credenziali chiave di Azure
- collegamento privato di Azure servizio
- Ricerca di Azure
- Database di Azure per MySQL
- Database di Azure per PostgreSQL
- Database di Azure per MariaDB

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>Azure Data Factory rete virtuale gestita è disponibile nelle aree di Azure seguenti:
- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti centro-occidentali
- Stati Uniti occidentali
- Stati Uniti occidentali 2
- Stati Uniti centro-meridionali
- Stati Uniti centrali
- Europa settentrionale
- Europa occidentale
- Regno Unito meridionale
- Asia sud-orientale
- Australia orientale
- Australia sud-orientale
- Norvegia orientale
- Giappone orientale
- Giappone occidentale
- Corea centrale
- Brasile meridionale
- Francia centrale
- Svizzera settentrionale
- Regno Unito occidentale
- Canada orientale
- Canada centrale

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Comunicazioni in uscita tramite endpoint pubblico dalla rete virtuale gestita di AdF
- Per le comunicazioni in uscita viene aperta solo la porta 443.
- Archiviazione di Azure e Azure Data Lake Gen2 non supportano la connessione tramite un endpoint pubblico dalla rete virtuale gestita di Azure Data Factory.

### <a name="linked-service-creation-of-azure-key-vault"></a>Creazione del servizio collegato di Azure Key Vault 
- Quando si crea un servizio collegato per Azure Key Vault, non esiste alcun riferimento ad Azure Integration Runtime. Non è quindi possibile creare un endpoint privato durante la creazione del servizio collegato di Azure Key Vault. Tuttavia, quando si crea un servizio collegato per gli archivi dati che fa riferimento Azure Key Vault Linked Service e questo servizio collegato fa riferimento Azure Integration Runtime con la rete virtuale gestita abilitata, è possibile creare un endpoint privato per il servizio collegato Azure Key Vault durante la creazione. 
- **L'operazione** di test della connessione per Azure Key Vault convalida solo il formato dell'URL, ma non le operazioni di rete.
- La colonna **Uso dell'endpoint** privato viene sempre visualizzata come vuota anche se si crea l'endpoint privato per Azure Key Vault.
![Endpoint privato per AKV](./media/managed-vnet/akv-pe.png)

## <a name="next-steps"></a>Passaggi successivi

- Esercitazione: [Creare una pipeline di copia usando la rete virtuale gestita e gli endpoint privati](tutorial-copy-data-portal-private.md) 
- Esercitazione: [Compilare la pipeline del flusso di dati di mapping usando la rete virtuale gestita e gli endpoint privati](tutorial-data-flow-private.md)