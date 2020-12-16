---
title: Come usare l'archiviazione di Accodamento di Azure da PowerShell-archiviazione di Azure
description: Eseguire operazioni nell'archivio code di Azure tramite PowerShell. Con archiviazione code di Azure è possibile archiviare un numero elevato di messaggi accessibili da HTTP/HTTPS.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 05/15/2019
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fba288f76377e744b1fe21a52e03a43409c505bf
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585616"
---
# <a name="how-to-use-azure-queue-storage-from-powershell"></a>Come usare l'archiviazione di Accodamento di Azure da PowerShell

Archiviazione code di Azure è un servizio per l'archiviazione di grandi quantità di messaggi a cui è possibile accedere da qualsiasi parte del mondo tramite HTTP o HTTPS. Per informazioni dettagliate, vedere [Introduzione all'archiviazione code di Azure](storage-queues-introduction.md). Questo articolo illustra le procedure comuni per l'archiviazione delle code. Si apprenderà come:

> [!div class="checklist"]
>
> - Creare una coda
> - Recuperare una coda
> - Aggiungere un messaggio
> - Leggere un messaggio
> - Eliminare un messaggio
> - Eliminare una coda

Questa guida dettagliata richiede il modulo Azure PowerShell ( `Az` ) v 0.7 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione attualmente installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps).

Non sono disponibili cmdlet di PowerShell per il piano dati per le code. Per eseguire le operazioni del piano dati come aggiungere, leggere ed eliminare un messaggio, è necessario usare la libreria client di dell'archivio .NET come mostrato in PowerShell. Si crea un oggetto messaggio e quindi si possono usare comandi come `AddMessage` per eseguire operazioni su tale messaggio. Questo articolo mostra come eseguire questa operazione.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Recuperare un elenco di posizioni

Se non si sa quale posizione usare, è possibile elencare le posizioni disponibili. Nell'elenco visualizzato trovare la posizione da usare. Questo esercizio utilizzerà `eastus` . Archiviarlo nella variabile `location` per un uso futuro.

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Archiviare il nome del gruppo di risorse in una variabile per uso futuro. In questo esempio viene creato un gruppo di risorse denominato `howtoqueuesrg` nell'area `eastus`.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Crea account di archiviazione

Creare un account di archiviazione standard per utilizzo generico con archiviazione con ridondanza locale mediante [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Ottenere il contesto che definisce l'account di archiviazione da usare. Quando si usa un account di archiviazione, si può fare riferimento al contesto anziché fornire ripetutamente le credenziali.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Creare una coda

Innanzitutto viene stabilita una connessione ad Archiviazione di Azure usando il contesto dell'account di archiviazione che include il nome dell'account di archiviazione e la relativa chiave di accesso. Successivamente, viene chiamato il cmdlet [New-AzStorageQueue](/powershell/module/az.storage/new-azstoragequeue) per creare una coda denominata `howtoqueue` .

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Per informazioni sulle convenzioni di denominazione per l'archiviazione code di Azure, vedere [denominazione di code e metadati](/rest/api/storageservices/naming-queues-and-metadata).

## <a name="retrieve-a-queue"></a>Recuperare una coda

È possibile eseguire una query e recuperare una coda specifica o un elenco di tutte le code in un account di archiviazione. Gli esempi seguenti illustrano come recuperare tutte le code nell'account di archiviazione e una coda specifica. Entrambi i comandi usano il cmdlet [Get-AzStorageQueue](/powershell/module/az.storage/get-azstoragequeue).

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>Aggiungere un messaggio a una coda

Le operazioni che influiscono sui messaggi effettivi nella coda usano la libreria client di archiviazione .NET come illustrato in PowerShell. Per aggiungere un messaggio a una coda, creare una nuova istanza dell'oggetto messaggio, [`Microsoft.Azure.Storage.Queue.CloudQueueMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueuemessage) classe. Chiamare quindi il [`AddMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueue.addmessage) metodo. `CloudQueueMessage`È possibile creare un oggetto da una stringa (in formato UTF-8) o da una matrice di byte.

L'esempio seguente mostra come aggiungere un messaggio alla coda.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 1")

# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 2")
$queue.CloudQueue.AddMessageAsync($QueueMessage)

$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 3")
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

Se si usa [Azure Storage Explorer](https://storageexplorer.com), è possibile connettersi al proprio account Azure, visualizzare le code nell'account di archiviazione ed eseguire il drill-down in una di esse per visualizzare i messaggi contenuti nella coda.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Leggere un messaggio dalla coda e quindi eliminarlo

I messaggi vengono letti nell'ordine first in, first out, ma senza alcuna garanzia. Quando il messaggio viene letto dalla coda, diventa invisibile a tutti gli altri processi che esaminano la coda. Ciò garantisce che, qualora il codice non riesca a elaborare il messaggio a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare.

Questo **periodo di invisibilità** definisce per quanto tempo il messaggio rimane invisibile prima di diventare di nuovo disponibile per l'elaborazione. Il valore predefinito è 30 secondi.

Il codice legge il messaggio dalla coda in due passaggi. Quando si chiama il [`Microsoft.Azure.Storage.Queue.CloudQueue.GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) metodo, si ottiene il messaggio successivo nella coda. Un messaggio restituito da `GetMessage` diventa invisibile a qualsiasi altro elemento di codice che legge i messaggi di questa coda. Per completare la rimozione del messaggio dalla coda, chiamare il [`Microsoft.Azure.Storage.Queue.CloudQueue.DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage) metodo.

Nell'esempio seguente vengono letti i tre messaggi in coda, seguiti da 10 secondi di attesa, il periodo di invisibilità. I tre messaggi vengono quindi letti nuovamente, eliminando i messaggi dopo la lettura chiamando `DeleteMessage` . Se si tenta di leggere la coda dopo l'eliminazione dei messaggi, `$queueMessage` verrà restituito come `$null` .

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue.
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>Eliminare una coda

Per eliminare una coda e tutti i messaggi che contiene, chiamare il `Remove-AzStorageQueue` cmdlet. Nell'esempio seguente viene illustrato come eliminare la coda specifica utilizzata in questo esercizio utilizzando il `Remove-AzStorageQueue` cmdlet.

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare tutti gli asset creati in questo esercizio, rimuovere il gruppo di risorse. Così facendo vengono eliminate anche tutte le risorse in esso contenute. In questo caso, rimuove l'account di archiviazione creato e il gruppo di risorse stesso.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sulle procedure sono state illustrate le procedure di base per la gestione dell'archiviazione delle code con PowerShell, tra cui:

> [!div class="checklist"]
>
> - Creare una coda
> - Recuperare una coda
> - Aggiungere un messaggio
> - Leggere il messaggio successivo
> - Eliminare un messaggio
> - Eliminare una coda

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Cmdlet di archiviazione Microsoft Azure PowerShell

- [Cmdlet di PowerShell per l'archiviazione](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

- [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) è un'app autonoma gratuita di Microsoft che consente di rappresentare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.
