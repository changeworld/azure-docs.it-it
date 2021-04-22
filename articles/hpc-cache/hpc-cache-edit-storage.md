---
title: Aggiornare le Cache HPC di Azure di archiviazione
description: Come modificare le destinazioni Cache HPC di Azure archiviazione
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: ebf68c1eb06984e2de8114c53e1bb55d52aed70a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862634"
---
# <a name="edit-storage-targets"></a>Modificare le destinazioni di archiviazione

È possibile rimuovere o modificare le destinazioni di archiviazione con il portale di Azure o usando l'interfaccia della riga di comando di Azure.

A seconda del tipo di archiviazione, è possibile modificare questi valori di destinazione di archiviazione:

* Per le destinazioni di archiviazione BLOB, è possibile modificare il percorso dello spazio dei nomi e i criteri di accesso.

* Per le destinazioni di archiviazione NFS, è possibile modificare questi valori:

  * Percorsi degli spazi dei nomi
  * Criterio di accesso
  * Sottodirectory di esportazione o esportazione di archiviazione associata a un percorso dello spazio dei nomi
  * Modello di utilizzo

* Per le destinazioni di archiviazione ADLS-NFS, è possibile modificare il percorso dello spazio dei nomi, i criteri di accesso e il modello di utilizzo.

Non è possibile modificare il nome, il tipo o il sistema di archiviazione back-end di una destinazione di archiviazione (contenitore BLOB o nome host/indirizzo IP NFS). Se è necessario modificare queste proprietà, eliminare la destinazione di archiviazione e creare una sostituzione con il nuovo valore.

> [!TIP]
> Il [video Gestione Cache HPC di Azure](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) mostra come modificare una destinazione di archiviazione nel portale di Azure.

## <a name="remove-a-storage-target"></a>Rimuovere una destinazione di archiviazione

### <a name="portal"></a>[Portale](#tab/azure-portal)

Per rimuovere una destinazione di archiviazione, aprire la **pagina Destinazioni di** archiviazione. Selezionare la destinazione di archiviazione dall'elenco e fare clic **sul pulsante** Elimina.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure Cache HPC di Azure](./az-cli-prerequisites.md).

Usare [az hpc-cache storage-target remove](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage_target_remove) per eliminare una destinazione di archiviazione dalla cache.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

L'eliminazione di una destinazione di archiviazione rimuove l'associazione del sistema di archiviazione con questo Cache HPC di Azure, ma non modifica il sistema di archiviazione back-end. Ad esempio, se è stato usato un contenitore di archiviazione BLOB di Azure, il contenitore e il relativo contenuto esistono ancora dopo l'eliminazione dalla cache. È possibile aggiungere il contenitore a un Cache HPC di Azure diverso, aggiungerlo di nuovo a questa cache o eliminarlo con il portale di Azure.

Tutte le modifiche ai file archiviate nella cache vengono scritte nel sistema di archiviazione back-end prima della rimozione della destinazione di archiviazione. Questo processo può richiedere un'ora o più se nella cache sono presenti molti dati modificati.

## <a name="change-a-blob-storage-targets-namespace-path"></a>Modificare il percorso dello spazio dei nomi di una destinazione di archiviazione BLOB

I percorsi dello spazio dei nomi sono i percorsi che i client usano per montare questa destinazione di archiviazione. Per altre informazioni, vedere Pianificare lo spazio dei [nomi aggregato](hpc-cache-namespace.md) e [Configurare lo spazio dei nomi aggregato](add-namespace-paths.md).

Il percorso dello spazio dei nomi è l'unico aggiornamento che è possibile eseguire in una destinazione di archiviazione BLOB di Azure. Usare il portale di Azure o l'interfaccia della riga di comando di Azure per modificarla.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Usare la **pagina Spazio** dei nomi per Cache HPC di Azure. La pagina dello spazio dei nomi è descritta in modo più dettagliato nell'articolo [Configurare lo spazio dei nomi aggregato](add-namespace-paths.md).

Fare clic sul nome del percorso da modificare e creare il nuovo percorso nella finestra di modifica visualizzata.

![Screenshot della pagina dello spazio dei nomi dopo aver fatto clic su un percorso dello spazio dei nomi BLOB: i campi di modifica vengono visualizzati in un riquadro a destra](media/update-namespace-blob.png)

Dopo aver apportato le modifiche, fare clic **su OK** per aggiornare la destinazione di archiviazione oppure fare clic **su Annulla** per annullare le modifiche.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure Cache HPC di Azure](./az-cli-prerequisites.md).

Per modificare lo spazio dei nomi di una destinazione di archiviazione BLOB con l'interfaccia della riga di comando di Azure, usare [il comando az hpc-cache blob-storage-target update](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update). È possibile `--virtual-namespace-path` modificare solo il valore .

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>Aggiornare una destinazione di archiviazione NFS

Per le destinazioni di archiviazione NFS, è possibile modificare o aggiungere percorsi dello spazio dei nomi virtuale, modificare i valori di esportazione o sottodirectory NFS a cui punta un percorso dello spazio dei nomi e modificare il modello di utilizzo.

Le destinazioni di archiviazione nelle cache con alcuni tipi di impostazioni DNS personalizzate hanno anche un controllo per l'aggiornamento degli indirizzi IP. Questo tipo di configurazione è raro.

Di seguito sono riportati i dettagli:

* [Modificare i valori aggregati dello spazio dei](#change-aggregated-namespace-values) nomi (percorso dello spazio dei nomi virtuale, criteri di accesso, esportazione ed esportazione della sottodirectory)
* [Modificare il modello di utilizzo](#change-the-usage-model)
* [Aggiornare IL DNS](#update-ip-address-custom-dns-configurations-only)

### <a name="change-aggregated-namespace-values"></a>Modificare i valori aggregati dello spazio dei nomi

È possibile usare il portale di Azure o l'interfaccia della riga di comando di Azure per modificare il percorso dello spazio dei nomi lato client, l'esportazione di archiviazione e la sottodirectory di esportazione (se usato).

Leggere le linee guida in Aggiungere percorsi dello spazio dei nomi [NFS](add-namespace-paths.md#nfs-namespace-paths) se è necessario ricordare come creare più percorsi validi in una destinazione di archiviazione.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Usare la pagina **Spazio** dei nomi per l'Cache HPC di Azure per aggiornare i valori dello spazio dei nomi. Questa pagina è descritta in modo più dettagliato nell'articolo [Configurare lo spazio dei nomi aggregato](add-namespace-paths.md).

![screenshot della pagina dello spazio dei nomi del portale con la pagina di aggiornamento NFS aperta a destra](media/update-namespace-nfs.png)

1. Fare clic sul nome del percorso che si desidera modificare.
1. Usare la finestra di modifica per digitare nuovi valori di percorso virtuale, esportazione o sottodirectory oppure per selezionare criteri di accesso diversi.
1. Dopo aver apportato le modifiche, fare **clic su OK** per aggiornare la destinazione di archiviazione o su Annulla **per** annullare le modifiche.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure Cache HPC di Azure](./az-cli-prerequisites.md).

Usare ``--junction`` l'opzione nel [comando az hpc-cache nfs-storage-target update](/cli/azure/hpc-cache/nfs-storage-target) per modificare il percorso dello spazio dei nomi, l'esportazione NFS o la sottodirectory export.

Il ``--junction`` parametro usa questi valori:

* ``namespace-path`` - Percorso del file virtuale lato client
* ``nfs-export`` - Esportazione del sistema di archiviazione da associare al percorso lato client
* ``target-path`` (facoltativo): sottodirectory dell'esportazione, se necessario

Esempio: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

È necessario specificare tutti e tre i valori per ogni percorso ``--junction`` nell'istruzione . Usare i valori esistenti per i valori che non si vuole modificare.

In tutti i comandi di aggiornamento sono necessari anche il nome della cache, il nome della destinazione di archiviazione e il gruppo di risorse.

Comando di esempio:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>Modificare il modello di utilizzo

Il modello di utilizzo influisce sul modo in cui la cache conserva i dati. Per [altre informazioni, vedere](cache-usage-models.md) Informazioni sui modelli di utilizzo della cache.

> [!NOTE]
> Se si modificano i modelli di utilizzo, potrebbe essere necessario rimontare i client per evitare errori NLM. Per [informazioni dettagliate, vedere Sapere quando rimontare](cache-usage-models.md#know-when-to-remount-clients-for-nlm) i client.

Per modificare il modello di utilizzo per una destinazione di archiviazione NFS, usare uno di questi metodi.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Modificare il modello di utilizzo dalla **pagina Destinazioni di** archiviazione nel portale di Azure. Fare clic sul nome della destinazione di archiviazione da modificare.

![Screenshot della pagina di modifica per una destinazione di archiviazione NFS](media/edit-storage-nfs.png)

Usare il selettore a discesa per scegliere un nuovo modello di utilizzo. Fare **clic su OK** per aggiornare la destinazione di archiviazione oppure fare clic su **Annulla** per annullare le modifiche.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure Cache HPC di Azure](./az-cli-prerequisites.md).

Usare il [comando az hpc-cache nfs-storage-target update.](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update)

Il comando update è quasi identico al comando utilizzato per aggiungere una destinazione di archiviazione NFS. Per informazioni [dettagliate ed esempi,](hpc-cache-add-storage.md#create-an-nfs-storage-target) vedere Creare una destinazione di archiviazione NFS.

Per modificare il modello di utilizzo, aggiornare ``--nfs3-usage-model`` l'opzione . Esempio: ``--nfs3-usage-model WRITE_WORKLOAD_15``

Sono necessari anche il nome della cache, il nome della destinazione di archiviazione e i valori del gruppo di risorse.

Per verificare i nomi dei modelli di utilizzo, usare il comando [az hpc-cache usage-model list](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage-model-list).

Se la cache viene arrestata o non è in uno stato integro, l'aggiornamento verrà applicato dopo che la cache è integra.

---

### <a name="update-ip-address-custom-dns-configurations-only"></a>Aggiornare l'indirizzo IP (solo configurazioni DNS personalizzate)

Se la cache usa una configurazione DNS non predefinita, è possibile che l'indirizzo IP della destinazione di archiviazione NFS cambi a causa di modifiche al DNS back-end. Se il server DNS modifica l'indirizzo IP del sistema di archiviazione back-end, Cache HPC di Azure può perdere l'accesso al sistema di archiviazione.

Idealmente, è consigliabile collaborare con il gestore del sistema DNS personalizzato della cache per pianificare eventuali aggiornamenti, perché queste modifiche rendono l'archiviazione non disponibile.

Se è necessario aggiornare l'indirizzo IP fornito dal DNS di una destinazione di archiviazione, è presente un pulsante nell'elenco Destinazioni di archiviazione. Fare **clic su Aggiorna DNS** per eseguire una query sul server DNS personalizzato per un nuovo indirizzo IP.

![Screenshot dell'elenco di destinazione di archiviazione. Per una destinazione di archiviazione, "..." il menu nella colonna all'estrema destra è aperto e vengono visualizzate due opzioni: Elimina e Aggiorna DNS.](media/refresh-dns.png)

In caso di esito positivo, l'aggiornamento dovrebbe richiedere meno di due minuti. È possibile aggiornare una sola destinazione di archiviazione alla volta. Attendere il completamento dell'operazione precedente prima di provarne un'altra.

## <a name="update-an-adls-nfs-storage-target-preview"></a>Aggiornare una destinazione di archiviazione ADLS-NFS (ANTEPRIMA)

Analogamente alle destinazioni NFS, è possibile modificare il percorso dello spazio dei nomi e il modello di utilizzo per le destinazioni di archiviazione ADLS-NFS.

### <a name="change-an-adls-nfs-namespace-path"></a>Modificare un percorso dello spazio dei nomi ADLS-NFS

Usare la pagina **Spazio** dei nomi per l'Cache HPC di Azure per aggiornare i valori dello spazio dei nomi. Questa pagina è descritta in modo più dettagliato nell'articolo [Configurare lo spazio dei nomi aggregato](add-namespace-paths.md).

![screenshot della pagina dello spazio dei nomi del portale con una pagina di aggiornamento ad ADS-NFS aperta a destra](media/update-namespace-adls.png)

1. Fare clic sul nome del percorso che si desidera modificare.
1. Usare la finestra di modifica per digitare un nuovo percorso virtuale o aggiornare i criteri di accesso.
1. Dopo aver apportato le modifiche, fare **clic su OK** per aggiornare la destinazione di archiviazione o su Annulla **per** annullare le modifiche.

### <a name="change-adls-nfs-usage-models"></a>Modificare i modelli di utilizzo di ADLS-NFS

La configurazione per i modelli di utilizzo di ADLS-NFS è identica alla selezione del modello di utilizzo NFS. Leggere le istruzioni del portale in [Modificare il modello di utilizzo](#change-the-usage-model) nella sezione NFS precedente. Sono in corso di sviluppo altri strumenti per l'aggiornamento delle destinazioni di archiviazione ADLS-NFS.


## <a name="next-steps"></a>Passaggi successivi

* Per [altre informazioni su queste opzioni,](hpc-cache-add-storage.md) vedere Aggiungere destinazioni di archiviazione.
* Per [altri suggerimenti sull'uso dei percorsi virtuali,](hpc-cache-namespace.md) vedere Pianificare lo spazio dei nomi aggregato.
