---
title: Considerazioni sull'archiviazione per Funzioni di Azure
description: Informazioni sui requisiti di archiviazione di Funzioni di Azure e sulla crittografia dei dati archiviati.
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: 5faa85a4fac9fc0b8639f33c475283f4f043c627
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779256"
---
# <a name="storage-considerations-for-azure-functions"></a>Considerazioni sull'archiviazione per Funzioni di Azure

Funzioni di Azure richiede un account di Archiviazione di Azure quando si crea un'istanza dell'app per le funzioni. I servizi di archiviazione seguenti possono essere usati dall'app per le funzioni:


|Servizio di archiviazione  | Utilizzo delle funzioni  |
|---------|---------|
| [Archivio BLOB di Azure](../storage/blobs/storage-blobs-introduction.md)     | Gestire lo stato delle associazioni e i tasti funzione.  <br/>Usato anche dagli [hub attività in Durable Functions](durable/durable-functions-task-hubs.md). |
| [File di Azure](../storage/files/storage-files-introduction.md)  | Condivisione file usata per archiviare ed eseguire il codice dell'app per le funzioni in un piano a [consumo](consumption-plan.md) e [in un piano Premium.](functions-premium-plan.md) |
| [Archiviazione code di Azure](../storage/queues/storage-queues-introduction.md)     | Usato dagli [hub attività in Durable Functions](durable/durable-functions-task-hubs.md).   |
| [Archivio tabelle di Azure](../storage/tables/table-storage-overview.md)  |  Usato dagli [hub attività in Durable Functions](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> Quando si usa il piano di hosting Consumption/Premium, il codice della funzione e i file di configurazione dell'associazione vengono archiviati in Archiviazione file di Azure nell'account di archiviazione principale. Quando si elimina l'account di archiviazione principale, il contenuto viene eliminato e non può essere ripristinato.

## <a name="storage-account-requirements"></a>Requisiti dell'account di archiviazione

Quando si crea un'app per le funzioni, è necessario creare o collegare un account di Archiviazione di Azure di uso generico che supporti l'archiviazione BLOB, code e tabelle. Questo perché Funzioni di Azure si basa internamente su Archiviazione di Azure per operazioni come la gestione dei trigger e la registrazione dell'esecuzione delle funzioni. Alcuni account di archiviazione non supportano code e tabelle. Questi account includono account di archiviazione solo BLOB e azure Archiviazione Premium.

Per altre informazioni sui tipi di account di archiviazione, vedere l'[introduzione ai servizi di Archiviazione di Azure](../storage/common/storage-introduction.md#core-storage-services). 

Sebbene sia possibile usare un account di archiviazione esistente con l'app per le funzioni, è necessario assicurarsi che soddisfi questi requisiti. Gli account di archiviazione creati come parte del flusso di creazione dell'app per le funzioni portale di Azure sono garantiti per soddisfare questi requisiti dell'account di archiviazione. Nel portale gli account non supportati vengono filtrati quando si sceglie un account di archiviazione esistente durante la creazione di un'app per le funzioni. In questo flusso è possibile scegliere solo gli account di archiviazione esistenti nella stessa area dell'app per le funzioni che si sta creando. Per altre informazioni, vedere [Percorso dell'account di archiviazione.](#storage-account-location)

<!-- JH: Does using a Premium Storage account improve perf? -->

## <a name="storage-account-guidance"></a>Linee guida sugli account di archiviazione

Ogni app per le funzioni richiede un account di archiviazione per funzionare. Se l'account viene eliminato, l'app per le funzioni non sarà eseguibile. Per risolvere i problemi correlati all'archiviazione, vedere [Come risolvere i problemi relativi all'archiviazione](functions-recover-storage-account.md). Le considerazioni aggiuntive seguenti si applicano all'account di archiviazione usato dalle app per le funzioni.

### <a name="storage-account-location"></a>Posizione dell'account di archiviazione

Per prestazioni ottimali, l'app per le funzioni deve usare un account di archiviazione nella stessa area, riducendo la latenza. Il portale di Azure applica questa procedura consigliata. Se, per qualche motivo, è necessario usare un account di archiviazione in un'area diversa dall'app per le funzioni, è necessario creare l'app per le funzioni all'esterno del portale. 

### <a name="storage-account-connection-setting"></a>Impostazione di connessione dell'account di archiviazione

La connessione dell'account di archiviazione viene mantenuta nell'impostazione dell'applicazione [AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

Se si rigenerano le chiavi di archiviazione, è necessario aggiornare le stringhe di connessione dell'account di archiviazione precedente. [Altre informazioni sulla gestione delle chiavi archiviazione qui](../storage/common/storage-account-create.md).

### <a name="shared-storage-accounts"></a>Account di archiviazione condivisi

È possibile che più app per le funzioni condividano lo stesso account di archiviazione senza problemi. Ad esempio, in Visual Studio è possibile sviluppare più app usando l'Emulatore di Archiviazione di Azure. In questo caso, l'emulatore funge da singolo account di archiviazione. È anche possibile usare lo stesso account di archiviazione dell'app per le funzioni per archiviare i dati dell'applicazione. Tuttavia, questo approccio non è sempre una scelta ideale in un ambiente di produzione.

### <a name="optimize-storage-performance"></a>Ottimizzare le prestazioni di archiviazione

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Crittografia dei dati di archiviazione

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

### <a name="in-region-data-residency"></a>Residenza dei dati nell'area geografica

Quando tutti i dati dei clienti devono rimanere all'interno di una singola area, l'account di archiviazione associato all'app per le funzioni deve essere uno con ridondanza [nell'area](../storage/common/storage-redundancy.md). Un account di archiviazione con ridondanza dell'area deve essere usato anche con [Azure Durable Functions](./durable/durable-functions-perf-and-scale.md#storage-account-selection).

Altri dati dei clienti gestiti dalla piattaforma vengono archiviati nell'area solo quando vengono ospitati in un ambiente del servizio ambiente del servizio app (ASE). Per altre informazioni, vedere [Ridondanza della zona dell'app.](../app-service/environment/zone-redundancy.md#in-region-data-residency)

## <a name="mount-file-shares"></a>Montare condivisioni file

_Questa funzionalità è attualmente disponibile solo quando è in esecuzione in Linux._ 

È possibile montare condivisioni di File di Azure esistenti nelle app per le funzioni di Linux. Montando una condivisione nell'app per le funzioni di Linux è possibile sfruttare i modelli di machine learning esistenti o altri dati nelle funzioni. È possibile usare il comando [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) per montare una condivisione esistente nell'app per le funzioni di Linux. 

In questo comando `share-name` è il nome della condivisione File di Azure esistente e `custom-id` può essere qualsiasi stringa che definisca in modo univoco la condivisione quando viene montata nell'app per le funzioni. `mount-path`, invece, è il percorso da cui viene eseguito l'accesso alla condivisione nell'app per le funzioni. `mount-path` deve essere nel formato `/dir-name` e non può iniziare con `/home`.

Per un esempio completo, vedere gli script in [Creare un'app per le funzioni Python e montare una condivisione File di Azure](scripts/functions-cli-mount-files-storage-linux.md). 

Attualmente è supportato solo un `storage-type` di `AzureFiles`. È possibile montare solo cinque condivisioni in una determinata app per le funzioni. Il montaggio di una condivisione file può aumentare i tempi di avvio a freddo di almeno 200-300 ms o anche oltre se l'account di archiviazione si trova in un'area diversa.

La condivisione montata è disponibile per il codice della funzione nel `mount-path` specificato. Ad esempio, quando `mount-path` è `/path/to/mount`, è possibile accedere alla directory di destinazione tramite API del file system, come nell'esempio Python seguente:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle opzioni di hosting di Funzioni di Azure.

> [!div class="nextstepaction"]
> [Ridimensionamento e hosting di Funzioni di Azure](functions-scale.md)
