---
title: Domande frequenti sulla funzionalità di ripristino temporizzato Azure Cosmos DB.
description: Questo articolo elenca le domande frequenti sulla Azure Cosmos DB funzionalità di ripristino temporizzato, che viene eseguita usando la modalità di backup continuo.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1cf94964f420f7a7d4fc0f6ba0b77813b3e75787
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393225"
---
# <a name="frequently-asked-questions-on-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Domande frequenti sulla Azure Cosmos DB funzionalità di ripristino temporizzato (anteprima)
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> La funzionalità di ripristino temporizzato (modalità di backup continuo) per Azure Cosmos DB è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo elenca le domande frequenti sulla Azure Cosmos DB funzionalità di ripristino temporizzato (anteprima) ottenibile usando la modalità di backup continuo.

## <a name="how-much-time-does-it-takes-to-restore"></a>Quanto tempo è necessario per il ripristino?
Durata del ripristino dipendente dalla dimensione dei dati.

### <a name="can-i-submit-the-restore-time-in-local-time"></a>È possibile inviare l'ora di ripristino nell'ora locale?
Il ripristino potrebbe non avvenire a seconda che le risorse chiave come i database o i contenitori esistano in quel momento. È possibile verificare immettendo il tempo ed esaminando il database o il contenitore selezionato per un determinato periodo di tempo. Se non è presente alcuna risorsa da ripristinare, il processo di ripristino non funziona.

### <a name="how-can-i-track-if-an-account-is-being-restored"></a>Come è possibile verificare se è in corso il ripristino di un account?
Dopo aver inviato il comando Restore e dopo aver atteso la stessa pagina, una volta completata l'operazione, la barra di stato indica che il messaggio dell'account è stato ripristinato correttamente. È anche possibile cercare l'account ripristinato e [tenere traccia dello stato dell'account da ripristinare](continuous-backup-restore-portal.md#track-restore-status). Durante l'esecuzione del ripristino, lo stato dell'account verrà *creato*, al termine dell'operazione di ripristino, lo stato dell'account verrà modificato in in *linea*.

Analogamente per PowerShell e l'interfaccia della riga di comando, è possibile tenere traccia dello stato di avanzamento dell'operazione di ripristino eseguendo il `az cosmosdb show` comando come segue:

```azurecli-interactive
az cosmosdb show --name "accountName" --resource-group "resourceGroup"
```

Il provisioningState Mostra l' *esito positivo* quando l'account è online.

```json
{
"virtualNetworkRules": [],
"writeLocations" : [
{
    "documentEndpoint": "https://<accountname>.documents.azure.com:443/", 
    "failoverpriority": 0,
    "id": "accountName" ,
    "isZoneRedundant" : false, 
    "locationName": "West US 2", 
    "provisioningState": "Succeeded"
}
]
}
```

### <a name="how-can-i-find-out-whether-an-account-was-restored-from-another-account"></a>Come è possibile verificare se un account è stato ripristinato da un altro account?
Eseguire il `az cosmosdb show` comando, nell'output, è possibile notare che il valore della `createMode` Proprietà. Se il valore è impostato per il **ripristino**. indica che l'account è stato ripristinato da un altro account. La `restoreParameters` proprietà contiene ulteriori dettagli, ad esempio `restoreSource` , con l'ID dell'account di origine. L'ultimo GUID nel `restoreSource` parametro è il InstanceId dell'account di origine.

Nell'output seguente, ad esempio, l'ID istanza dell'account di origine è *7b4bb-f6a0-430e-ade1-638d781830cc*

```json
"restoreParameters": {
   "databasesToRestore" : [],
   "restoreMode": "PointInTime",
   "restoreSource": "/subscriptions/2a5b-f6a0-430e-ade1-638d781830dd/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/7b4bb-f6a0-430e-ade1-638d781830cc",
   "restoreTimestampInUtc": "2020-06-11T22:05:09Z"
}
```

### <a name="what-happens-when-i-restore-a-shared-throughput-database-or-a-container-within-a-shared-throughput-database"></a>Cosa accade quando si ripristina un database di velocità effettiva condivisa o un contenitore all'interno di un database di velocità effettiva condivisa?
Viene ripristinato l'intero database della velocità effettiva condivisa. Non è possibile scegliere un subset di contenitori in un database di velocità effettiva condivisa per il ripristino.

### <a name="what-is-the-use-of-instanceid-in-the-account-definition"></a>Qual è l'uso di InstanceID nella definizione dell'account?
In un determinato momento, Azure Cosmos DB proprietà dell'account `accountName` è globalmente univoca mentre è attiva. Tuttavia, dopo l'eliminazione dell'account, è possibile creare un altro account con lo stesso nome e quindi "AccountName" non è più sufficiente per identificare un'istanza di un account. 

ID o `instanceId` è una proprietà di un'istanza di un account che viene utilizzata per evitare ambiguità tra più account (in tempo reale ed eliminato) se hanno lo stesso nome per il ripristino. È possibile ottenere l'ID istanza eseguendo i `Get-AzCosmosDBRestorableDatabaseAccount` comandi o  `az cosmosdb restorable-database-account` . Il valore dell'attributo Name indica "InstanceID".

## <a name="next-steps"></a>Passaggi successivi

* Che cos'è la modalità di [backup continuo](continuous-backup-restore-introduction.md) ?
* Configurare e gestire il backup continuo con [portale di Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)o [Azure Resource Manager](continuous-backup-restore-template.md).
* [Gestire le autorizzazioni](continuous-backup-restore-permissions.md) necessarie per ripristinare i dati con la modalità di backup continuo.
* [Modello di risorse della modalità di backup continuo](continuous-backup-restore-resource-model.md)