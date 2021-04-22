---
title: Gestire e aggiornare i Cache HPC di Azure
description: Come gestire e aggiornare i Cache HPC di Azure usando l'interfaccia della portale di Azure o l'interfaccia della riga di comando di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/08/2021
ms.author: v-erkel
ms.openlocfilehash: a831aa7b2f3b0d438d9db8fefa3d26428fea3680
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862598"
---
# <a name="manage-your-cache"></a>Gestire la cache

La pagina di panoramica della cache portale di Azure i dettagli del progetto, lo stato della cache e le statistiche di base per la cache. Include anche controlli per arrestare o avviare la cache, eliminare la cache, scaricare i dati nell'archiviazione a lungo termine e aggiornare il software.

Questo articolo illustra anche come eseguire queste attività di base con l'interfaccia della riga di comando di Azure.

Per aprire la pagina di panoramica, selezionare la risorsa cache nell'portale di Azure. Ad esempio, caricare la **pagina Tutte le** risorse e fare clic sul nome della cache.

![Screenshot della pagina Panoramica Cache HPC di Azure'istanza di un'istanza](media/hpc-cache-overview.png)

I pulsanti nella parte superiore della pagina consentono di gestire la cache:

* **Avvio** e [**arresto:**](#stop-the-cache) riprende o sospende l'operazione della cache
* [**Scaricamento:**](#flush-cached-data) scrive i dati modificati nelle destinazioni di archiviazione
* [**Aggiornamento**](#upgrade-cache-software) : aggiorna il software della cache
* [**Raccogliere informazioni di diagnostica**](#collect-diagnostics) - Carica le informazioni di debug
* **Aggiorna** : ricarica la pagina di panoramica
* [**Elimina:**](#delete-the-cache) elimina definitivamente la cache

Altre informazioni su queste opzioni sono disponibili di seguito.

Fare clic sull'immagine seguente per guardare un [video che](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) illustra le attività di gestione della cache.

[![anteprima video: Cache HPC di Azure: Gestisci (fare clic per visitare la pagina del video)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>Arrestare la cache

È possibile arrestare la cache per ridurre i costi durante un periodo di inattività. Non vengono addebitati costi per il tempo di attività mentre la cache viene arrestata, ma vengono addebitati i costi per lo spazio di archiviazione su disco allocato della cache. Per informazioni [dettagliate, vedere](https://aka.ms/hpc-cache-pricing) la pagina dei prezzi.

Una cache arrestata non risponde alle richieste del client. È consigliabile smontare i client prima di arrestare la cache.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Il **pulsante Arresta** sospende una cache attiva. Il **pulsante** Arresta è disponibile quando lo stato di una cache è **Integro** **o Danneggiato.**

![screenshot dei pulsanti in alto con l'opzione Arresta evidenziata e un messaggio popup che descrive l'azione di arresto e chiede 'vuoi continuare?' con i pulsanti Sì (impostazione predefinita) e No](media/stop-cache.png)

Dopo aver fatto clic su Sì per confermare l'arresto della cache, il contenuto della cache viene scaricato automaticamente nelle destinazioni di archiviazione. Questo processo può richiedere tempo, ma garantisce la coerenza dei dati. Infine, lo stato della cache cambia in **Arrestato.**

Per riattivare una cache arrestata, fare clic sul **pulsante** Avvia. Non è necessaria alcuna conferma.

![screenshot dei pulsanti in alto con l'opzione Avvia evidenziata](media/start-cache.png)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure Cache HPC di Azure](./az-cli-prerequisites.md).

Sospendere temporaneamente una cache con il [comando az hpc-cache stop.](/cli/azure/hpc-cache#az_hpc_cache_stop) Questa azione è valida solo quando lo stato di una cache è **Integro** **o Danneggiato.**

La cache scarica automaticamente il contenuto nelle destinazioni di archiviazione prima dell'arresto. Questo processo può richiedere tempo, ma garantisce la coerenza dei dati.

Al termine dell'azione, lo stato della cache viene modificato in **Arrestato**.

Riattivare una cache arrestata con [az hpc-cache start](/cli/azure/hpc-cache#az_hpc_cache_start).

Quando si esegue il comando start o stop, la riga di comando visualizza un messaggio di stato "Running" fino al completamento dell'operazione.

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

Al termine, il messaggio viene aggiornato a "Finished" e mostra i codici restituiti e altre informazioni.

```azurecli
$ az hpc-cache start --name doc-cache0629
{- Finished ..
  "endTime": "2020-07-01T18:46:43.6862478+00:00",
  "name": "c48d320f-f5f5-40ab-8b25-0ac065984f62",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-01T18:40:28.5468983+00:00",
  "status": "Succeeded"
}
```

---

## <a name="flush-cached-data"></a>Scaricare i dati memorizzati nella cache

Il **pulsante Scarica** nella pagina di panoramica indica alla cache di scrivere immediatamente tutti i dati modificati archiviati nella cache nelle destinazioni di archiviazione back-end. La cache salva regolarmente i dati nelle destinazioni di archiviazione, quindi non è necessario eseguire questa operazione manualmente a meno che non si voglia assicurarsi che il sistema di archiviazione back-end sia aggiornato. Ad esempio, è possibile usare **Flush prima** di creare uno snapshot di archiviazione o controllare le dimensioni del set di dati.

> [!NOTE]
> Durante il processo di scaricamento, la cache non può gestire le richieste client. L'accesso alla cache viene sospeso e ripreso al termine dell'operazione.

Quando si avvia l'operazione di scaricamento della cache, la cache smette di accettare richieste client e lo stato della cache nella pagina di panoramica cambia in **Scaricamento**.

I dati nella cache vengono salvati nelle destinazioni di archiviazione appropriate. A seconda della quantità di dati da scaricare, il processo può richiedere alcuni minuti o più di un'ora.

Dopo che tutti i dati sono stati salvati nelle destinazioni di archiviazione, la cache inizia automaticamente a eseguire nuovamente le richieste client. Lo stato della cache torna a **Integro.**

### <a name="portal"></a>[Portale](#tab/azure-portal)

Per scaricare la cache, fare clic sul **pulsante Scarica** e quindi su **Sì** per confermare l'azione.

![Screenshot dei pulsanti principali con scaricamento evidenziato e un messaggio popup che descrive l'azione di scaricamento e chiede "vuoi continuare?". con i pulsanti Sì (impostazione predefinita) e No](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure Cache HPC di Azure](./az-cli-prerequisites.md).

Usare [az hpc-cache flush](/cli/azure/hpc-cache#az_hpc_cache_flush) per forzare la cache a scrivere tutti i dati modificati nelle destinazioni di archiviazione.

Esempio:

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

Al termine dello scaricamento, viene restituito un messaggio di operazione riuscita.

```azurecli
{- Finished ..
  "endTime": "2020-07-09T17:26:13.9371983+00:00",
  "name": "c22f8e12-fcf0-49e5-b897-6a6e579b6489",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-09T17:25:21.4278297+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="upgrade-cache-software"></a>Aggiornare il software della cache

Se è disponibile una nuova versione del software, il **pulsante** Aggiorna diventa attivo. Nella parte superiore della pagina dovrebbe essere visualizzato anche un messaggio sull'aggiornamento del software.

![screenshot della riga superiore dei pulsanti con il pulsante Aggiorna abilitato](media/hpc-cache-upgrade-button.png)

L'accesso client non viene interrotto durante un aggiornamento software, ma le prestazioni della cache rallentano. Pianificare l'aggiornamento del software durante le ore di utilizzo non di punta o in un periodo di manutenzione pianificata.

L'aggiornamento software può richiedere diverse ore. L'aggiornamento delle cache configurate con una velocità effettiva superiore richiede più tempo rispetto alle cache con valori di velocità effettiva di picco più piccoli.

Quando è disponibile un aggiornamento software, sarà disponibile una settimana per l'applicazione manuale. La data di fine è elencata nel messaggio di aggiornamento. Se non si esegue l'aggiornamento durante tale periodo, Azure applica automaticamente l'aggiornamento alla cache. La tempistica dell'aggiornamento automatico non è configurabile. Se si è interessati all'impatto sulle prestazioni della cache, è necessario aggiornare il software manualmente prima della scadenza del periodo di tempo.

Se la cache viene arrestata al termine della data di fine, il software verrà aggiornato automaticamente al successivo avvio. L'aggiornamento potrebbe non iniziare immediatamente, ma inizierà alla prima ora.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Fare clic **sul pulsante** Aggiorna per avviare l'aggiornamento software. Lo stato della cache cambia in **Aggiornamento fino** al completamento dell'operazione.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure Cache HPC di Azure](./az-cli-prerequisites.md).

Nell'interfaccia della riga di comando di Azure le nuove informazioni software sono incluse alla fine del report sullo stato della cache. Usare [az hpc-cache show](/cli/azure/hpc-cache#az_hpc_cache_show) per controllare. Cercare la stringa "upgradeStatus" nel messaggio.

Usare [az hpc-cache upgrade-firmware](/cli/azure/hpc-cache#az_hpc_cache_upgrade-firmware) per applicare l'aggiornamento, se presente.

Se non è disponibile alcun aggiornamento, questa operazione non ha alcun effetto.

Questo esempio mostra lo stato della cache (non è disponibile alcun aggiornamento) e i risultati del comando upgrade-firmware.

```azurecli
$ az hpc-cache show --name doc-cache0629
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },

<...>

  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.61",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-06-29T22:18:32.004822+00:00",
    "pendingFirmwareVersion": null
  }
}
$ az hpc-cache upgrade-firmware --name doc-cache0629
$
```

---

## <a name="collect-diagnostics"></a>Raccogliere i dati di diagnostica

Il **pulsante Raccogli diagnostica** avvia manualmente il processo per raccogliere informazioni di sistema e caricarlo nel servizio Microsoft e nel supporto tecnico per la risoluzione dei problemi. La cache raccoglie e carica automaticamente le stesse informazioni di diagnostica se si verifica un grave problema di cache.

Usare questo controllo se il servizio e il supporto tecnico Microsoft lo richiede.

Dopo aver fatto clic sul pulsante, fare **clic su Sì** per confermare il caricamento.

![Screenshot del messaggio di conferma popup "Avvia raccolta diagnostica". Il pulsante predefinito "Sì" è evidenziato.](media/diagnostics-confirm.png)

## <a name="delete-the-cache"></a>Eliminare la cache

Il **pulsante** Elimina elimina definitivamente la cache. Quando si elimina una cache, tutte le relative risorse vengono eliminate definitivamente e non vengono più sostenuti addebiti per l'account.

I volumi di archiviazione back-end usati come destinazioni di archiviazione non sono interessati quando si elimina la cache. È possibile aggiungerli a una cache futura in un secondo momento o rimuovere le autorizzazioni separatamente.

> [!NOTE]
> Cache HPC di Azure non scrive automaticamente i dati modificati dalla cache ai sistemi di archiviazione back-end prima di eliminare la cache.
>
> Per assicurarsi che tutti i dati nella cache siano stati scritti nell'archiviazione a lungo termine, [arrestare](#stop-the-cache) la cache prima di eliminarla. Assicurarsi che venga visualizzato lo stato **Arrestato prima dell'eliminazione.**

### <a name="portal"></a>[Portale](#tab/azure-portal)

Dopo aver interrotto la cache, fare clic **sul pulsante** Elimina per rimuovere definitivamente la cache.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure Cache HPC di Azure](./az-cli-prerequisites.md).

Usare il comando dell'interfaccia della riga di comando di Azure [az hpc-cache delete](/cli/azure/hpc-cache#az_hpc_cache_delete) per rimuovere definitivamente la cache.

Esempio:
```azurecli
$ az hpc-cache delete --name doc-cache0629
 - Running ..

<...>

{- Finished ..
  "endTime": "2020-07-09T22:24:35.1605019+00:00",
  "name": "7d3cd0ba-11b3-4180-8298-d9cafc9f22c1",
  "startTime": "2020-07-09T22:13:32.0732892+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="cache-metrics-and-monitoring"></a>Metriche e monitoraggio della cache

La pagina di panoramica mostra i grafici per alcune statistiche di base della cache: velocità effettiva della cache, operazioni al secondo e latenza.

![screenshot di tre grafici a linee che mostrano le statistiche indicate in precedenza per una cache di esempio](media/hpc-cache-overview-stats.png)

Questi grafici fanno parte degli strumenti di monitoraggio e analisi predefiniti di Azure. Altri strumenti e avvisi sono disponibili nelle pagine sotto **l'intestazione** Monitoraggio nella barra laterale del portale. Per altre informazioni, vedere la sezione del portale della documentazione [di Monitoraggio di Azure.](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-in-the-azure-portal)

## <a name="view-warnings"></a>Visualizza avvisi

Se la cache passa a uno stato non integro, controllare la **pagina** Avvisi. Questa pagina mostra le notifiche del software di cache che potrebbero essere utili per comprenderne lo stato.

Queste notifiche non vengono visualizzate nel log attività perché non sono controllate da portale di Azure. Spesso sono associati a impostazioni personalizzate che potrebbero essere state apportate.

I tipi di avvisi che possono essere visualizzati qui includono:

* La cache non può raggiungere il server NTP
* La cache non è riuscita a scaricare le informazioni sul nome utente dei gruppi estesi
* Le impostazioni DNS personalizzate sono state modificate in una destinazione di archiviazione

![screenshot della pagina Avvisi di > monitoraggio che mostra un messaggio che indica che non è stato possibile scaricare i nomi utente dei gruppi estesi](media/warnings-page.png)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sugli strumenti [per metriche e statistiche di Azure](../azure-monitor/index.yml)
* Ottenere [assistenza per l'Cache HPC di Azure](hpc-cache-support-ticket.md)
