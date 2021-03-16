---
title: Aggiornare le destinazioni di archiviazione della cache HPC di Azure
description: Come modificare le destinazioni di archiviazione della cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/10/2021
ms.author: v-erkel
ms.openlocfilehash: 78010ef2d93b23a12fc7f3e988a536b4993b4dd4
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471848"
---
# <a name="edit-storage-targets"></a>Modificare le destinazioni di archiviazione

È possibile rimuovere o modificare le destinazioni di archiviazione con la portale di Azure o usando l'interfaccia della riga di comando di Azure.

A seconda del tipo di archiviazione, è possibile modificare questi valori di destinazione di archiviazione:

* Per le destinazioni di archiviazione BLOB, è possibile modificare il percorso dello spazio dei nomi e i criteri di accesso.

* Per le destinazioni di archiviazione NFS, è possibile modificare i valori seguenti:

  * Percorsi dello spazio dei nomi
  * Criterio di accesso
  * Sottodirectory di esportazione o esportazione dell'archiviazione associata a un percorso dello spazio dei nomi
  * Modello di utilizzo

* Per le destinazioni di archiviazione ADLS-NFS, è possibile modificare il percorso dello spazio dei nomi, i criteri di accesso e il modello di utilizzo.

Non è possibile modificare il nome, il tipo o il sistema di archiviazione back-end di una destinazione di archiviazione (contenitore BLOB o nome host/indirizzo IP NFS). Se è necessario modificare queste proprietà, eliminare la destinazione di archiviazione e creare una sostituzione con il nuovo valore.

> [!TIP]
> Il [video sulla gestione della cache HPC di Azure](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) illustra come modificare una destinazione di archiviazione nel portale di Azure.

## <a name="remove-a-storage-target"></a>Rimuovere una destinazione di archiviazione

### <a name="portal"></a>[Portale](#tab/azure-portal)

Per rimuovere una destinazione di archiviazione, aprire la pagina **destinazioni di archiviazione** . Selezionare la destinazione di archiviazione nell'elenco e fare clic sul pulsante **Elimina** .

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure per la cache HPC di Azure](./az-cli-prerequisites.md).

Usare [AZ HPC-cache storage-target Remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) per eliminare una destinazione di archiviazione dalla cache.

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

L'eliminazione di una destinazione di archiviazione rimuove l'associazione del sistema di archiviazione con questo sistema di cache HPC di Azure, ma non modifica il sistema di archiviazione back-end. Ad esempio, se è stato usato un contenitore di archiviazione BLOB di Azure, il contenitore e il relativo contenuto continuano a esistere dopo averlo eliminato dalla cache. È possibile aggiungere il contenitore a una cache HPC di Azure diversa, aggiungerla nuovamente a questa cache o eliminarla con la portale di Azure.

Tutte le modifiche apportate ai file archiviati nella cache vengono scritte nel sistema di archiviazione back-end prima della rimozione della destinazione di archiviazione. Questo processo può richiedere più di un'ora se nella cache sono presenti numerosi dati modificati.

## <a name="change-a-blob-storage-targets-namespace-path"></a>Modificare il percorso dello spazio dei nomi di una destinazione di archiviazione BLOB

I percorsi dello spazio dei nomi sono i percorsi utilizzati dai client per montare questa destinazione di archiviazione. Per altre informazioni, vedere [pianificare lo spazio dei nomi aggregato](hpc-cache-namespace.md) e [impostare lo spazio dei nomi aggregato](add-namespace-paths.md).

Il percorso dello spazio dei nomi è l'unico aggiornamento che è possibile eseguire in una destinazione di archiviazione BLOB di Azure. Usare il portale di Azure o l'interfaccia della riga di comando di Azure per modificarla.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Usare la pagina **spazio dei nomi** per la cache HPC di Azure. La pagina dello spazio dei nomi è descritta più dettagliatamente nell'articolo [configurare lo spazio dei nomi aggregato](add-namespace-paths.md).

Fare clic sul nome del percorso che si desidera modificare e creare il nuovo percorso nella finestra di modifica visualizzata.

![Screenshot della pagina dello spazio dei nomi dopo aver fatto clic sul percorso di uno spazio dei nomi BLOB. i campi di modifica vengono visualizzati in un riquadro a destra](media/edit-namespace-blob.png)

Dopo aver apportato le modifiche, fare clic su **OK** per aggiornare la destinazione di archiviazione oppure fare clic su **Annulla per annullare** le modifiche.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure per la cache HPC di Azure](./az-cli-prerequisites.md).

Per modificare lo spazio dei nomi della destinazione di archiviazione BLOB con l'interfaccia della riga di comando di Azure, usare il comando [AZ HPC-cache BLOB-Storage-target Update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update). `--virtual-namespace-path`È possibile modificare solo il valore.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>Aggiornare una destinazione di archiviazione NFS

Per le destinazioni di archiviazione NFS, è possibile modificare o aggiungere percorsi dello spazio dei nomi virtuali, modificare i valori di esportazione o sottodirectory NFS a cui punta un percorso dello spazio dei nomi e modificare il modello di utilizzo.

Le destinazioni di archiviazione nelle cache con alcuni tipi di impostazioni DNS personalizzate hanno anche un controllo per l'aggiornamento degli indirizzi IP. (Questo tipo di configurazione è raro).

Di seguito sono riportati i dettagli:

* [Modificare i valori di spazio dei nomi aggregati](#change-aggregated-namespace-values) (percorso dello spazio dei nomi virtuale, criteri di accesso, esportazione ed esportazione sottodirectory)
* [Modificare il modello di utilizzo](#change-the-usage-model)
* [Aggiorna DNS](#update-ip-address-custom-dns-configurations-only)

### <a name="change-aggregated-namespace-values"></a>Modificare i valori di spazio dei nomi aggregati

È possibile usare il portale di Azure o l'interfaccia della riga di comando di Azure per modificare il percorso dello spazio dei nomi per il client, l'esportazione dell'archiviazione e la sottodirectory Export (se utilizzata).

Leggere le linee guida in [aggiungere percorsi dello spazio dei nomi NFS](add-namespace-paths.md#nfs-namespace-paths) se è necessario un promemoria per la creazione di più percorsi validi in una destinazione di archiviazione.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Usare la pagina **dello spazio dei nomi** per la cache HPC di Azure per aggiornare i valori dello spazio dei nomi. Questa pagina è descritta più dettagliatamente nell'articolo [configurare lo spazio dei nomi aggregato](add-namespace-paths.md).

![screenshot della pagina dello spazio dei nomi del portale con la pagina di aggiornamento di NFS aperta a destra](media/update-namespace-nfs.png)

1. Fare clic sul nome del percorso che si desidera modificare.
1. Utilizzare la finestra modifica per digitare nuovi valori per percorso virtuale, Esporta o sottodirectory oppure per selezionare criteri di accesso diversi.
1. Dopo avere apportato le modifiche, fare clic su **OK** per aggiornare la destinazione di archiviazione o su **Annulla** per annullare le modifiche.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure per la cache HPC di Azure](./az-cli-prerequisites.md).

Usare l' ``--junction`` opzione nel comando [AZ HPC-cache NFS-storage-target Update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) per modificare il percorso dello spazio dei nomi, l'esportazione NFS o l'esportazione della sottodirectory.

Il ``--junction`` parametro usa i valori seguenti:

* ``namespace-path`` -Percorso del file virtuale rivolte al client
* ``nfs-export`` -Esportazione del sistema di archiviazione da associare al percorso del client
* ``target-path`` (facoltativo): sottodirectory dell'esportazione, se necessario

Esempio: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

È necessario specificare tutti e tre i valori per ogni percorso nell' ``--junction`` istruzione. Utilizzare i valori esistenti per i valori che non si desidera modificare.

In tutti i comandi Update sono necessari anche il nome della cache, il nome della destinazione di archiviazione e il gruppo di risorse.

Comando di esempio:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>Modificare il modello di utilizzo

Il modello di utilizzo influisce sul modo in cui la cache mantiene i dati. Per altre informazioni, vedere [scegliere un modello di utilizzo](hpc-cache-add-storage.md#choose-a-usage-model) .

Per modificare il modello di utilizzo per una destinazione di archiviazione NFS, usare uno di questi metodi.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Modificare il modello di utilizzo dalla pagina **destinazioni di archiviazione** nel portale di Azure. Fare clic sul nome della destinazione di archiviazione da modificare.

![screenshot della pagina di modifica per una destinazione di archiviazione NFS](media/edit-storage-nfs.png)

Usare il selettore a discesa per scegliere un nuovo modello di utilizzo. Fare clic su **OK** per aggiornare la destinazione di archiviazione oppure fare clic su **Annulla** per annullare le modifiche.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure per la cache HPC di Azure](./az-cli-prerequisites.md).

Usare il comando [AZ HPC-cache NFS-storage-target Update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) .

Il comando Update è quasi identico al comando utilizzato per aggiungere una destinazione di archiviazione NFS. Per informazioni dettagliate ed esempi, vedere [creare una destinazione di archiviazione NFS](hpc-cache-add-storage.md#create-an-nfs-storage-target) .

Per modificare il modello di utilizzo, aggiornare l' ``--nfs3-usage-model`` opzione. Esempio: ``--nfs3-usage-model WRITE_WORKLOAD_15``

Sono necessari anche il nome della cache, il nome della destinazione di archiviazione e i valori del gruppo di risorse.

Se si desidera verificare i nomi dei modelli di utilizzo, utilizzare il comando [AZ HPC-cache Usage-model list](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

Se la cache viene arrestata o non è in uno stato integro, l'aggiornamento verrà applicato dopo l'integrità della cache.

---

### <a name="update-ip-address-custom-dns-configurations-only"></a>Aggiornare l'indirizzo IP (solo per le configurazioni DNS personalizzate)

Se la cache usa una configurazione DNS non predefinita, è possibile che l'indirizzo IP della destinazione di archiviazione NFS cambi a causa delle modifiche del DNS del back-end. Se il server DNS modifica l'indirizzo IP del sistema di archiviazione back-end, la cache HPC di Azure può perdere l'accesso al sistema di archiviazione.

Idealmente, è consigliabile collaborare con il responsabile del sistema DNS personalizzato della cache per pianificare gli aggiornamenti, perché queste modifiche rendono l'archiviazione non disponibile.

Se è necessario aggiornare l'indirizzo IP fornito da DNS di una destinazione di archiviazione, è presente un pulsante nell'elenco destinazioni di archiviazione. Fare clic su **Aggiorna DNS** per eseguire una query sul server DNS personalizzato per un nuovo indirizzo IP.

![Screenshot dell'elenco di destinazioni di archiviazione. Per una destinazione di archiviazione, il "..." il menu nella colonna all'estrema destra è aperto e vengono visualizzate due opzioni: Elimina e Aggiorna DNS.](media/refresh-dns.png)

In caso di esito positivo, l'aggiornamento dovrebbe richiedere meno di due minuti. È possibile aggiornare solo una destinazione di archiviazione alla volta. attendere il completamento dell'operazione precedente prima di provarne un altro.

## <a name="update-an-adls-nfs-storage-target-preview"></a>Aggiornare una destinazione di archiviazione ADLS-NFS (anteprima)

Analogamente alle destinazioni NFS, è possibile modificare il percorso dello spazio dei nomi e il modello di utilizzo per le destinazioni di archiviazione ADLS-NFS.

### <a name="change-an-adls-nfs-namespace-path"></a>Modificare un percorso dello spazio dei nomi ADLS-NFS

Usare la pagina **dello spazio dei nomi** per la cache HPC di Azure per aggiornare i valori dello spazio dei nomi. Questa pagina è descritta più dettagliatamente nell'articolo [configurare lo spazio dei nomi aggregato](add-namespace-paths.md).

![screenshot della pagina dello spazio dei nomi del portale con una pagina di aggiornamento di ADS-NFS aperta a destra](media/update-namespace-adls.png)

1. Fare clic sul nome del percorso che si desidera modificare.
1. Utilizzare la finestra modifica per digitare un nuovo percorso virtuale oppure aggiornare i criteri di accesso.
1. Dopo avere apportato le modifiche, fare clic su **OK** per aggiornare la destinazione di archiviazione o su **Annulla** per annullare le modifiche.

### <a name="change-adls-nfs-usage-models"></a>Modificare i modelli di utilizzo di ADLS-NFS

La configurazione per i modelli di utilizzo di ADLS-NFS è identica alla selezione del modello di utilizzo di NFS. Leggere le istruzioni del portale in [modificare il modello di utilizzo](#change-the-usage-model) nella sezione NFS precedente. Sono in fase di sviluppo altri strumenti per l'aggiornamento degli obiettivi di archiviazione ADLS-NFS.


## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su queste opzioni, vedere [aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md) .
* Per altri suggerimenti sull'uso di percorsi virtuali, vedere [pianificare lo spazio dei nomi aggregato](hpc-cache-namespace.md) .
