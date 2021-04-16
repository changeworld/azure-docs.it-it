---
title: Eseguire l'aggiornamento a un account di archiviazione per utilizzo generico v2
titleSuffix: Azure Storage
description: Eseguire l'aggiornamento agli account di archiviazione per utilizzo generico v2 usando portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Specificare un livello di accesso per i dati BLOB.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: d6d94efea52d920b8bfe69be2ee07c8b829792d8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484099"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Eseguire l'aggiornamento a un account di archiviazione per utilizzo generico v2

Gli account di archiviazione per utilizzo generico v2 supportano le funzionalità di archiviazione di Azure più recenti e incorporano tutte le funzionalità degli account di archiviazione BLOB e per utilizzo generico v1. Gli account per utilizzo generico v2 rappresentano la scelta consigliata per la maggior parte degli scenari. Gli account per utilizzo generico v2 offrono i prezzi per gigabyte più bassi per Archiviazione di Azure, oltre a prezzi per transazione competitivi a livello di settore. Gli account per utilizzo generico v2 supportano i livelli di accesso agli account predefiniti con livelli di accesso ad accesso ad accesso rapido o ad accesso ad accesso elevato e a livello di BLOB tra accesso ad accesso rapido, ad accesso ad accesso limitato o archivio.

L'aggiornamento a un account di archiviazione per utilizzo generico v2 dagli account di archiviazione per utilizzo generico v1 o BLOB è semplice. È possibile eseguire l'aggiornamento tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Non sono presenti tempi di inattività o rischi di perdita di dati associati all'aggiornamento a un account di archiviazione per utilizzo generico v2. L'aggiornamento dell'account avviene tramite una semplice Azure Resource Manager che modifica il tipo di account.

> [!IMPORTANT]
> L'aggiornamento di un account di archiviazione BLOB o per utilizzo generico v1 alla versione 2 per utilizzo generico è permanente e non può essere annullato.

> [!NOTE]
> Anche se Microsoft consiglia gli account per utilizzo generico v2 per la maggior parte degli scenari, Microsoft continuerà a supportare gli account per utilizzo generico v1 per i clienti nuovi ed esistenti. È possibile creare account di archiviazione per utilizzo generico v1 in nuove aree ogni volta Archiviazione di Azure disponibili in tali aree. Microsoft attualmente non ha un piano per deprecare il supporto per gli account per utilizzo generico v1 e fornirà almeno un anno di preavviso prima di deprecare qualsiasi funzionalità Archiviazione di Azure. Microsoft continuerà a fornire aggiornamenti della sicurezza per gli account per utilizzo generico v1, ma non è previsto alcun nuovo sviluppo di funzionalità per questo tipo di account.
>
> Per le nuove aree di Azure che sono state online dopo il 1° ottobre 2020, i prezzi per gli account per utilizzo generico v1 sono stati modificati ed è equivalente ai prezzi per gli account per utilizzo generico v2 in tali aree. I prezzi per gli account per utilizzo generico v1 nelle aree di Azure esistenti prima del 1° ottobre 2020 non sono stati modificati. Per informazioni dettagliate sui prezzi per gli account per utilizzo generico v1 in un'area specifica, vedere la pagina Archiviazione di Azure prezzi. Scegliere l'area e quindi accanto a **Offerte per i prezzi** selezionare **Altro**.

## <a name="upgrade-an-account"></a>Aggiornare un account

Per aggiornare un account di archiviazione BLOB o per utilizzo generico v1 a un account per utilizzo generico v2, usare portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare all'account di archiviazione.
3. Nella sezione **Impostazioni** fare clic su **Configurazione**.
4. In **Account kind (Tipo di account)** fare clic **su Upgrade (Aggiorna).**
5. In **Conferma aggiornamento** digitare il nome dell'account.
6. Fare **clic su** Aggiorna nella parte inferiore del pannello.

    ![Tipo di account di aggiornamento](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per aggiornare un account per utilizzo generico v1 a un account per utilizzo generico v2 con PowerShell, aggiornare prima PowerShell in modo che usi l'ultima versione del modulo **Az.Storage**. Per informazioni su come installare PowerShell, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps).

Chiamare quindi il comando seguente per aggiornare l'account, sostituendo il nome del gruppo di risorse, il nome dell'account di archiviazione e il livello di accesso dell'account desiderato.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per aggiornare un account per utilizzo generico v1 a un account per utilizzo generico v2 con l'interfaccia della riga di comando di Azure, installare prima l'ultima versione dell'interfaccia della riga di comando di Azure. Per informazioni sull'installazione dell'interfaccia della riga di comando, vedere [Install the Azure CLI 2.0](/cli/azure/install-azure-cli) (Installare l'interfaccia della riga di comando di Azure 2.0).

Chiamare quindi il comando seguente per aggiornare l'account, sostituendo il nome del gruppo di risorse, il nome dell'account di archiviazione e il livello di accesso dell'account desiderato.

```azurecli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Specificare un livello di accesso per i dati BLOB

Gli account per utilizzo generico v2 supportano tutti i servizi e gli oggetti dati di Archiviazione di Azure, ma i livelli di accesso sono disponibili solo per i BLOB in blocchi all'interno dell'archiviazione BLOB. Quando si esegue l'aggiornamento a un account di archiviazione per utilizzo generico v2, è possibile specificare un livello di accesso all'account predefinito ad accesso rapido o ad accesso limitato, che indica il livello predefinito in cui verranno caricati i dati BLOB come se non fosse specificato il singolo parametro del livello di accesso al BLOB.

I livelli di accesso BLOB consentono di scegliere l'archiviazione più conveniente in base ai modelli di utilizzo previsti. I BLOB in blocchi possono essere archiviati in livelli di accesso ad accesso più caldo, ad accesso ad accesso archivio o archivio. Per altre informazioni sui livelli di accesso, vedere Archiviazione BLOB di Azure: livelli di archiviazione ad accesso rapido, ad accesso [limitato e archivio.](../blobs/storage-blob-storage-tiers.md)

Per impostazione predefinita, viene creato un nuovo account di archiviazione nel livello di accesso ad accesso rapido e un account di archiviazione per utilizzo generico v1 può essere aggiornato al livello di account ad accesso ad accesso rapido o ad accesso ad accesso rapido. Se durante l'aggiornamento non viene specificato un livello di accesso dell'account, per impostazione predefinita verrà aggiornato all'accesso ad accesso più rapido. Se si sta valutando il livello di accesso da usare per l'aggiornamento, prendere in considerazione lo scenario di utilizzo dei dati corrente. Esistono due tipici scenari utente per la migrazione a un account per utilizzo generico v2:

* È disponibile un account di archiviazione per utilizzo generico v1 esistente e si vuole valutare un aggiornamento per passare a un account di archiviazione per utilizzo generico v2 con il livello di accesso all'archiviazione corretto per i dati BLOB.
* Si è deciso di usare un account di archiviazione per utilizzo generico v2 o se ne è già disponibile uno e si vuole valutare se è consigliabile usare il livello di accesso ad accesso rapido o ad accesso ad accesso cool per i dati BLOB.

In entrambi i casi, è prioritario stimare il costo di archiviazione, accesso e gestione dei dati archiviati in un account di archiviazione per utilizzo generico v2 e confrontarlo con i costi attuali.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

L'aggiornamento di un account di archiviazione v1 a un account per utilizzo generico v2 è gratuito. È possibile specificare il livello dell'account desiderato durante il processo di aggiornamento. Se non viene specificato un livello account durante l'aggiornamento, il livello account predefinito dell'account aggiornato sarà `Hot` . Tuttavia, la modifica del livello di accesso all'archiviazione dopo l'aggiornamento può comportare modifiche alla fattura, pertanto è consigliabile specificare il nuovo livello di account durante l'aggiornamento.

Tutti gli account di archiviazione usano per l'archivio BLOB un modello di determinazione prezzi basato sul livello di ogni BLOB. Quando si usa un account di archiviazione, tenere conto delle considerazioni seguenti relative alla fatturazione:

* **Costi di archiviazione:** oltre alla quantità di dati archiviati, il costo di archiviazione dei dati varia a seconda del livello di accesso all'archiviazione. Il costo per gigabyte diminuisce passando a un livello ad accesso più sporadico.

* **Costi di accesso ai dati**: i costi di accesso ai dati aumentano passando a un livello ad accesso più sporadico. Per i dati nei livelli di archiviazione ad accesso sporadico e archivio vengono addebitati i costi per l'accesso ai dati per gigabyte per le operazioni di lettura.

* **Costi delle transazioni**: sono previsti costi per transazione per tutti i livelli e tali costi aumentano passando a un livello ad accesso più sporadico.

* **Costi di trasferimento dati con la replica geografica**: si applicano solo agli account per cui è configurata la replica geografica, incluse l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza geografica e accesso in lettura. Il trasferimento dati con la replica geografica comporta un addebito per gigabyte.

* **Costi di trasferimento dati in uscita**: i trasferimenti dati in uscita (dati che vengono trasferiti al di fuori di un'area di Azure) vengono fatturati in base all'utilizzo di larghezza di banda per singolo gigabyte, come per gli account di archiviazione di uso generico.

* **Modifica del livello di accesso** all'archiviazione: la modifica del livello di accesso all'archiviazione dell'account da ad accesso ad accesso rapido comporta un addebito pari alla lettura di tutti i dati esistenti nell'account di archiviazione. Il passaggio dell'account dal livello di accesso all'archiviazione ad accesso frequente a quello ad accesso sporadico comporta invece un addebito corrispondente a quello per la scrittura di tutti i dati nel livello ad accesso sporadico (solo per account per utilizzo generico v2).

> [!NOTE]
> Per altre informazioni sul modello di determinazione prezzi per gli account di archiviazione, vedere la pagina [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/). Per altre informazioni sugli addebiti per i trasferimenti dati in uscita, vedere la pagina [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Stimare i costi per i modelli di utilizzo correnti

Per stimare il costo di archiviazione e accesso ai dati BLOB in un account di archiviazione per utilizzo generico v2 a un particolare livello, è necessario valutare il modello di utilizzo esistente o definire approssimativamente il modello di utilizzo previsto. In genere, è consigliabile conoscere quanto segue:

* Uso dell'archiviazione BLOB, in gigabyte, inclusi:
  * Quantità di dati archiviata nell'account di archiviazione
  * Variazione nel volume di dati su base mensile e se i dati precedenti vengono costantemente sostituiti da nuovi dati

* Modello di accesso primario per i dati di archiviazione BLOB, inclusi:
  * Quantità di dati letti e scritti nell'account di archiviazione
  * Numero di operazioni di lettura e di scrittura eseguite sui dati nell'account di archiviazione

Per scegliere il miglior livello di accesso per le proprie esigenze, può essere utile determinare la capacità di dati del BLOB e la modalità d'uso di tali dati. Questa scelta può essere fatta al meglio osservando le metriche di monitoraggio per l'account.

### <a name="monitoring-existing-storage-accounts"></a>Monitoraggio degli account di archiviazione esistenti

Per monitorare gli account di archiviazione esistenti e raccoglierne i dati, è possibile usare Analisi archiviazione di Azure, che esegue la registrazione e fornisce i dati delle metriche per un account di archiviazione. Analisi archiviazione può archiviare metriche che includono le statistiche delle transazioni aggregate e i dati sulla capacità relativi alle richieste a un servizio di archiviazione per i tipi di account di archiviazione BLOB, per utilizzo generico v1 e per utilizzo generico v2. I dati vengono archiviati in tabelle note nello stesso account di archiviazione.

Per altre informazioni, vedere [About Storage Analytics Metrics](../blobs/monitor-blob-storage.md) (Informazioni sulle metriche di Analisi archiviazione) e [Storage Analytics Metrics Table Schema](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) (Schema di tabella della metrica di Analisi archiviazione)

> [!NOTE]
> Gli account di archiviazione BLOB espongono l'endpoint di servizio tabelle solo per l'archiviazione e l'accesso ai dati delle metriche per l'account specifico.

Per monitorare l'utilizzo delle risorse di archiviazione per l'archiviazione BLOB, è necessario abilitare la metrica della capacità.
Con questa impostazione abilitata, i dati sulla capacità vengono registrati ogni giorno per il servizio BLOB di un account di archiviazione e registrati come una voce di tabella che viene scritta nella tabella *$MetricsCapacityBlob* nello stesso account di archiviazione.

Per monitorare i modelli di accesso ai dati per l'archiviazione BLOB, è necessario abilitare la metrica delle transazioni orarie dall'API. Quando è abilitata questa metrica, le transazioni vengono aggregate ogni ora per ogni API e registrate come una voce della tabella che viene scritta nella tabella *$MetricsHourPrimaryTransactionsBlob* nello stesso account di archiviazione. La tabella *$MetricsHourSecondaryTransactionsBlob* registra le transazioni nell'endpoint secondario quando si usano account di archiviazione con ridondanza geografica e accesso in lettura.

> [!NOTE]
> Se si ha un account di archiviazione per utilizzo generico in cui sono archiviati BLOB di pagine e dischi di macchine virtuali oppure code, file o tabelle insieme a dati di BLOB in blocchi e di aggiunta, questo processo di stima non è applicabile. I dati sulla capacità non distinguono i BLOB in blocchi dagli altri tipi, di conseguenza non offrono dati sulla capacità per altri tipi di dati. Se si usano questi tipi, una metodologia alternativa consiste nell'esaminare le quantità nella fattura più recente.

Per ottenere una buona approssimazione del modello di accesso e di utilizzo dei dati, è consigliabile scegliere un periodo di conservazione per le metriche che rappresenti l'utilizzo regolare ed estrapolarne i dati. Una possibilità consiste nel conservare i dati delle metriche per sette giorni e raccoglierli ogni settimana per analizzarli alla fine del mese. Un'altra possibilità è conservare i dati di metrica per gli ultimi 30 giorni e raccogliere e analizzare i dati alla fine del periodo di 30 giorni.

Per informazioni dettagliate sull'abilitazione, la raccolta e la visualizzazione dei dati delle metriche, vedere [Metriche di Analisi archiviazione](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Anche l'archiviazione, l'accesso e il download dei dati di analisi vengono addebitati come avviene per i dati utente normali.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Uso della metrica di utilizzo per stimare i costi

#### <a name="capacity-costs"></a>Costi di capacità

L'ultima voce nella tabella della metrica della capacità *$MetricsCapacityBlob* con la chiave di riga *'data'* mostra la capacità di archiviazione utilizzata dai dati utente. L'ultima voce nella tabella della metrica della capacità *$MetricsCapacityBlob* con la chiave di riga *'analytics'* mostra la capacità di archiviazione utilizzata dai log di analisi.

Questa capacità totale utilizzata sia dai dati utente che dai log di analisi, se abilitati, può quindi essere usata per stimare i costi di archiviazione dei dati nell'account di archiviazione. Lo stesso metodo può essere usato anche per stimare i costi di archiviazione negli account di archiviazione per utilizzo generico v1.

#### <a name="transaction-costs"></a>Costi di transazione

La somma di *'TotalBillableRequests'* in tutte le voci relative a un'API nella tabella della metrica delle transazioni indica il numero totale di transazioni per quell'API specifica. *Ad esempio*, il numero totale di transazioni *'GetBlob'* in un dato periodo può essere calcolato dalla somma delle richieste fatturabili totali per tutte le voci con la chiave di riga *'user;GetBlob'*.

Per stimare i costi delle transazioni per gli account di archiviazione BLOB, è necessario suddividere le transazioni in tre gruppi, perché i prezzi vengono determinati in modo diverso.

* Transazioni di scrittura, ad esempio *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* e *'CopyBlob'*.
* Transazioni di eliminazione, ad esempio *'DeleteBlob'* e *'DeleteContainer'*.
* Tutte le altre transazioni.

Per stimare i costi delle transazioni per gli account di archiviazione per utilizzo generico v1, è necessario aggregare tutte le transazioni indipendentemente dall'operazione o dall'API.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Costi di trasferimento dati con replica geografica e di accesso ai dati

Mentre l'analisi dell'archiviazione non fornisce la quantità di dati in lettura e scrittura in un account di archiviazione, è possibile effettuare una stima approssimativa esaminando la tabella della metrica delle transazioni. La somma di *'TotalIngress'* in tutte le voci relative a un'API nella tabella della metrica delle transazione indica la quantità totale di dati in ingresso, espressa in byte, per quell'API specifica. In modo analogo, la somma di *'TotalEgress'* indica la quantità totale di dati in uscita, in byte.

Per stimare i costi di accesso ai dati per gli account di archiviazione BLOB, è necessario suddividere le transazioni in due gruppi.

* La quantità di dati recuperata dall'account di archiviazione può essere stimata esaminando la somma di *'TotalEgress'* principalmente per le operazioni *'GetBlob'* e *'CopyBlob'*.

* La quantità di dati scritta nell'account di archiviazione può essere stimata esaminando la somma di *'TotalIngress'* principalmente per le operazioni *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* e *'AppendBlock'*.

I costi di trasferimento dati con replica geografica per gli account di archiviazione BLOB possono anche essere calcolati usando la stima per la quantità di dati scritti quando si usa un account di archiviazione con ridondanza geografica o con ridondanza geografica e accesso in lettura.

> [!NOTE]
> Per un esempio più dettagliato relativo al calcolo dei costi per l'uso del livello di accesso all'archiviazione ad accesso frequente o sporadico, vedere la domanda frequente *"Che cosa sono i livelli di archiviazione ad accesso frequente e accesso sporadico e come si determina quale usare?"* nella [pagina Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica dell'account di archiviazione](storage-account-overview.md)
* [Creare un account di archiviazione](storage-account-create.md)
* [Spostare un account Archiviazione di Azure in un'altra area](storage-account-move.md)
* [consente di ripristinare un account di archiviazione eliminato](storage-account-recover.md)
