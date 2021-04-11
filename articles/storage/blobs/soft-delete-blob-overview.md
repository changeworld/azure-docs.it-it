---
title: Eliminazione temporanea per i BLOB
titleSuffix: Azure Storage
description: L'eliminazione temporanea per i BLOB protegge i dati in modo da poter ripristinare più facilmente i dati quando vengono erroneamente modificati o eliminati da un'applicazione o da un altro utente dell'account di archiviazione.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: ef1ed584a609b2e4baa27111e47343df99146f5a
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257501"
---
# <a name="soft-delete-for-blobs"></a>Eliminazione temporanea per i BLOB

L'eliminazione temporanea BLOB protegge un singolo BLOB, snapshot o versione da eliminazioni accidentali o sovrascritture mantenendo i dati eliminati nel sistema per un periodo di tempo specificato. Durante il periodo di memorizzazione, è possibile ripristinare un oggetto eliminato temporaneamente al relativo stato al momento dell'eliminazione. Una volta scaduto il periodo di conservazione, l'oggetto viene eliminato definitivamente.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="recommended-data-protection-configuration"></a>Configurazione della protezione dati consigliata

L'eliminazione temporanea dei BLOB fa parte di una strategia di protezione dei dati completa per i dati BLOB. Per una protezione ottimale per i dati BLOB, Microsoft consiglia di abilitare tutte le funzionalità di protezione dei dati seguenti:

- Elimina temporaneamente il contenitore per ripristinare un contenitore che è stato eliminato. Per informazioni su come abilitare l'eliminazione temporanea del contenitore, vedere [Enable and Manage soft delete for Containers](soft-delete-container-enable.md).
- Controllo delle versioni dei BLOB, per gestire automaticamente le versioni precedenti di un BLOB. Quando è abilitata la funzionalità di controllo delle versioni dei BLOB, è possibile ripristinare una versione precedente di un BLOB per ripristinare i dati se vengono erroneamente modificati o eliminati. Per informazioni su come abilitare il controllo delle versioni dei BLOB, vedere [abilitare e gestire il controllo delle versioni dei BLOB](versioning-enable.md).
- Eliminazione temporanea BLOB, per ripristinare un BLOB, uno snapshot o una versione che è stata eliminata. Per informazioni su come abilitare l'eliminazione temporanea del BLOB, vedere [abilitare e gestire l'eliminazione temporanea per i BLOB](soft-delete-blob-enable.md).

Per ulteriori informazioni sulle raccomandazioni di Microsoft per la protezione dei dati, vedere [panoramica sulla protezione dei dati](data-protection-overview.md).

## <a name="how-blob-soft-delete-works"></a>Funzionamento dell'eliminazione temporanea BLOB

Quando si Abilita l'eliminazione temporanea BLOB per un account di archiviazione, è necessario specificare un periodo di conservazione per gli oggetti eliminati da 1 a 365 giorni. Il periodo di memorizzazione indica per quanto tempo i dati rimangono disponibili dopo l'eliminazione o la sovrascrittura. L'orologio inizia con il periodo di memorizzazione non appena un oggetto viene eliminato o sovrascritto.

Mentre il periodo di memorizzazione è attivo, è possibile ripristinare un BLOB eliminato, insieme ai relativi snapshot, o una versione eliminata chiamando l'operazione di [annullamento dell'eliminazione del BLOB](/rest/api/storageservices/undelete-blob) . Il diagramma seguente mostra come è possibile ripristinare un oggetto eliminato quando è abilitata l'eliminazione temporanea BLOB:

:::image type="content" source="media/soft-delete-blob-overview/blob-soft-delete-diagram.png" alt-text="Diagramma che illustra come è possibile ripristinare un BLOB eliminato temporaneamente":::

È possibile modificare il periodo di conservazione dell'eliminazione temporanea in qualsiasi momento. Un periodo di memorizzazione aggiornato si applica solo ai dati che sono stati eliminati dopo la modifica del periodo di conservazione. Qualsiasi dato eliminato prima del periodo di memorizzazione è stato modificato è soggetto al periodo di conservazione che era attivo al momento dell'eliminazione.

Il tentativo di eliminare un oggetto eliminato temporaneamente non influisce sull'ora di scadenza.

Se si disabilita l'eliminazione temporanea BLOB, è possibile continuare ad accedere e ripristinare gli oggetti eliminati temporaneamente nell'account di archiviazione fino a quando non è trascorso il periodo di memorizzazione dell'eliminazione temporanea.

Il controllo delle versioni dei BLOB è disponibile per gli account per utilizzo generico V2, BLOB in blocchi e archiviazione BLOB. Gli account di archiviazione con uno spazio dei nomi gerarchico abilitato per l'uso con Azure Data Lake Storage Gen2 non sono attualmente supportati.

La versione 2017-07-29 e successive dell'API REST di archiviazione di Azure supportano l'eliminazione temporanea BLOB.

> [!IMPORTANT]
> È possibile usare l'eliminazione temporanea BLOB solo per ripristinare un singolo BLOB, snapshot o versione. Per ripristinare un contenitore e il relativo contenuto, è necessario abilitare l'eliminazione temporanea del contenitore anche per l'account di archiviazione. Microsoft consiglia di abilitare l'eliminazione temporanea del contenitore e il controllo delle versioni dei BLOB insieme all'eliminazione temporanea BLOB per garantire la protezione completa dei dati BLOB. Per altre informazioni, vedere [panoramica sulla protezione dei dati](data-protection-overview.md).
>
> L'eliminazione temporanea del BLOB non protegge dall'eliminazione di un account di archiviazione. Per proteggere un account di archiviazione dall'eliminazione, configurare un blocco sulla risorsa dell'account di archiviazione. Per altre informazioni sul blocco di un account di archiviazione, vedere [applicare un blocco Azure Resource Manager a un account di archiviazione](../common/lock-account-resource.md).

### <a name="how-deletions-are-handled-when-soft-delete-is-enabled"></a>Modalità di gestione delle eliminazioni quando l'eliminazione temporanea è abilitata

Quando l'eliminazione temporanea BLOB è abilitata, l'eliminazione di un BLOB contrassegna tale BLOB come eliminata temporaneamente. Non viene creato alcuno snapshot. Al termine del periodo di memorizzazione, il BLOB eliminato temporaneamente viene eliminato definitivamente.

Se un BLOB contiene snapshot, non è possibile eliminare il BLOB a meno che non vengano eliminati anche gli snapshot. Quando si elimina un BLOB e i relativi snapshot, i BLOB e gli snapshot vengono contrassegnati come eliminati temporaneamente. Non viene creato alcun nuovo snapshot.

È anche possibile eliminare uno o più snapshot attivi senza eliminare il BLOB di base. In questo caso, lo snapshot viene eliminato temporaneamente.

Gli oggetti eliminati temporaneamente sono invisibili a meno che non vengano visualizzati o elencati in modo esplicito. Per altre informazioni su come elencare gli oggetti eliminati temporaneamente, vedere [gestire e ripristinare i BLOB eliminati temporaneamente](soft-delete-blob-manage.md).

### <a name="how-overwrites-are-handled-when-soft-delete-is-enabled"></a>Come vengono gestite le sovrascritture quando l'eliminazione temporanea è abilitata

La chiamata a un'operazione, ad esempio [Put Blob](/rest/api/storageservices/put-blob), [Put Block List](/rest/api/storageservices/put-block-list)o [Copy Blob](/rest/api/storageservices/copy-blob) , sovrascrive i dati in un BLOB. Quando l'eliminazione temporanea BLOB è abilitata, sovrascrivendo un BLOB viene creato automaticamente uno snapshot eliminato temporaneamente dello stato del BLOB prima dell'operazione di scrittura. Al termine del periodo di memorizzazione, lo snapshot eliminato temporaneamente viene eliminato definitivamente.

Gli snapshot eliminati temporaneamente sono invisibili a meno che gli oggetti eliminati temporaneamente non vengano visualizzati o elencati in modo esplicito. Per altre informazioni su come elencare gli oggetti eliminati temporaneamente, vedere [gestire e ripristinare i BLOB eliminati temporaneamente](soft-delete-blob-manage.md).

Per proteggere un'operazione di copia, è necessario abilitare l'eliminazione temporanea del BLOB per l'account di archiviazione di destinazione.

L'eliminazione temporanea del BLOB non protegge da operazioni per la scrittura di metadati o proprietà del BLOB. Quando vengono aggiornati i metadati o le proprietà di un BLOB, non viene creato alcuno snapshot eliminato temporaneamente.

L'eliminazione temporanea BLOB non offre la protezione da sovrascrittura per i BLOB nel livello archivio. Se un BLOB nel livello archivio viene sovrascritto con un nuovo BLOB in qualsiasi livello, il BLOB sovrascritto viene eliminato definitivamente.

Per gli account di archiviazione Premium, gli snapshot eliminati temporaneamente non vengono conteggiati per il limite di 100 snapshot per BLOB.

### <a name="restoring-soft-deleted-objects"></a>Ripristino di oggetti eliminati temporaneamente

È possibile ripristinare i BLOB eliminati temporaneamente chiamando l'operazione di [annullamento dell'eliminazione del BLOB](/rest/api/storageservices/undelete-blob) entro il periodo di memorizzazione. L'operazione **Undelete BLOB** ripristina un BLOB e tutti gli snapshot eliminati temporaneamente associati. Qualsiasi snapshot eliminato durante il periodo di memorizzazione viene ripristinato.

Se si chiama **Undelete BLOB** su un BLOB che non viene eliminato temporaneamente, verranno ripristinati gli snapshot eliminati temporaneamente associati al BLOB. Se il BLOB non ha snapshot e non è eliminato temporaneamente, la chiamata a **Undelete BLOB** non ha alcun effetto.

Per innalzare di livello uno snapshot eliminato temporaneamente al BLOB di base, chiamare prima di tutto **Undelete BLOB** sul BLOB di base per ripristinare il BLOB e i relativi snapshot. Quindi, copiare lo snapshot desiderato sul BLOB di base. È anche possibile copiare lo snapshot in un nuovo BLOB.

Non è possibile leggere i dati di un BLOB o di uno snapshot eliminato temporaneamente finché l'oggetto non è stato ripristinato.

Per altre informazioni su come ripristinare oggetti eliminati temporaneamente, vedere [gestire e ripristinare i BLOB eliminati temporaneamente](soft-delete-blob-manage.md).

## <a name="blob-soft-delete-and-versioning"></a>Eliminazione temporanea e controllo delle versioni dei BLOB

Se per un account di archiviazione sono abilitate le funzionalità di controllo delle versioni BLOB e eliminazione temporanea BLOB, la sovrascrittura di un BLOB crea automaticamente una nuova versione. La nuova versione non viene eliminata temporaneamente e non viene rimossa al termine del periodo di memorizzazione dell'eliminazione temporanea. Non vengono creati snapshot eliminati temporaneamente. Quando si elimina un BLOB, la versione corrente del BLOB diventa una versione precedente e non è più disponibile una versione corrente. Non viene creata alcuna nuova versione e non vengono creati snapshot eliminati temporaneamente.

L'abilitazione dell'eliminazione temporanea e del controllo delle versioni consente di proteggere le versioni BLOB dall'eliminazione. Quando l'eliminazione temporanea è abilitata, l'eliminazione di una versione crea una versione eliminata temporaneamente. È possibile usare l'operazione **Annulla eliminazione BLOB** per ripristinare le versioni eliminate temporaneamente durante il periodo di memorizzazione dell'eliminazione temporanea. L'operazione di **annullamento dell'eliminazione del BLOB** ripristina sempre tutte le versioni eliminate temporaneamente del BLOB. Non è possibile ripristinare una sola versione eliminata temporaneamente.

Una volta trascorso il periodo di memorizzazione dell'eliminazione temporanea, eventuali versioni di BLOB eliminate temporaneamente vengono eliminate definitivamente.

> [!NOTE]
> La chiamata all'operazione **Undelete BLOB** su un BLOB eliminato quando è abilitato il controllo delle versioni consente di ripristinare le versioni o gli snapshot eliminati temporaneamente, ma non il ripristino della versione corrente. Per ripristinare la versione corrente, alzare di livello una versione precedente copiando il valore nella versione corrente.

Microsoft consiglia di abilitare il controllo delle versioni e l'eliminazione temporanea dei BLOB per gli account di archiviazione per la protezione dei dati ottimale. Per altre informazioni sull'uso combinato del controllo delle versioni e dell'eliminazione temporanea, vedere [controllo delle versioni dei BLOB e eliminazione](versioning-overview.md#blob-versioning-and-soft-delete)temporanea.

## <a name="blob-soft-delete-protection-by-operation"></a>Protezione dell'eliminazione temporanea BLOB per operazione

La tabella seguente descrive il comportamento previsto per le operazioni di eliminazione e scrittura quando l'eliminazione temporanea BLOB è abilitata, con o senza il controllo delle versioni dei BLOB:

| Operazioni dell'API REST | Eliminazione temporanea abilitata | Eliminazione temporanea e controllo delle versioni abilitato |
|--|--|--|
| [Elimina account di archiviazione](/rest/api/storagerp/storageaccounts/delete) | Nessuna modifica. I contenitori e i BLOB contenuti nell'account eliminato non sono recuperabili. | Nessuna modifica. I contenitori e i BLOB contenuti nell'account eliminato non sono recuperabili. |
| [Delete Container](/rest/api/storageservices/delete-container) | Nessuna modifica. I BLOB contenuti nell'account eliminato non sono recuperabili. | Nessuna modifica. I BLOB contenuti nell'account eliminato non sono recuperabili. |
| [Eliminare un BLOB](/rest/api/storageservices/delete-blob) | Se usata per eliminare un BLOB, tale BLOB viene contrassegnato come eliminato temporaneamente. <br /><br /> Se usato per eliminare uno snapshot BLOB, lo snapshot viene contrassegnato come eliminato temporaneamente. | Se usato per eliminare un BLOB, la versione corrente diventa una versione precedente e la versione corrente viene eliminata. Non viene creata alcuna nuova versione e non vengono creati snapshot eliminati temporaneamente.<br /><br /> Se usato per eliminare una versione BLOB, la versione viene contrassegnata come eliminata temporaneamente. |
| [Annulla l'eliminazione del BLOB](/rest/api/storageservices/delete-blob) | Ripristina un BLOB e tutti gli snapshot eliminati entro il periodo di memorizzazione. | Ripristina un BLOB e tutte le versioni eliminate entro il periodo di memorizzazione. |
| [Put Blob](/rest/api/storageservices/put-blob)<br />[Put Block List](/rest/api/storageservices/put-block-list)<br />[Copy Blob](/rest/api/storageservices/copy-blob)<br />[Copia BLOB da URL](/rest/api/storageservices/copy-blob) | Se viene chiamato su un BLOB attivo, viene generato automaticamente uno snapshot dello stato del BLOB prima dell'operazione. <br /><br /> Se viene chiamato su un BLOB eliminato temporaneamente, viene generato uno snapshot dello stato precedente del BLOB solo se viene sostituito da un BLOB dello stesso tipo. Se il BLOB è di un tipo diverso, tutti i dati eliminati temporaneamente esistenti verranno eliminati definitivamente. | Una nuova versione che acquisisce lo stato del BLOB prima dell'operazione viene generata automaticamente. |
| [Put Block](/rest/api/storageservices/put-block) | Se utilizzata per eseguire il commit di un blocco in un BLOB attivo, non viene apportata alcuna modifica.<br /><br />Se usato per eseguire il commit di un blocco in un BLOB eliminato temporaneamente, viene creato un nuovo BLOB e viene generato automaticamente uno snapshot per acquisire lo stato del BLOB eliminato temporaneamente. | Nessuna modifica. |
| [Put Page](/rest/api/storageservices/put-page)<br />[Put Page from URL](/rest/api/storageservices/put-page-from-url) | Nessuna modifica. I dati BLOB di pagine sovrascritti o cancellati con questa operazione non vengono salvati e non sono recuperabili. | Nessuna modifica. I dati BLOB di pagine sovrascritti o cancellati con questa operazione non vengono salvati e non sono recuperabili. |
| [Append Block](/rest/api/storageservices/append-block)<br />[Append Block from URL](/rest/api/storageservices/append-block-from-url) | Nessuna modifica. | Nessuna modifica. |
| [Set Blob Properties](/rest/api/storageservices/set-blob-properties) | Nessuna modifica. Le proprietà del BLOB sovrascritto non sono recuperabili. | Nessuna modifica. Le proprietà del BLOB sovrascritto non sono recuperabili. |
| [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata) | Nessuna modifica. I metadati del BLOB sovrascritto non sono recuperabili. | Una nuova versione che acquisisce lo stato del BLOB prima dell'operazione viene generata automaticamente. |
| [Set Blob Tier](/rest/api/storageservices/set-blob-tier) | Il BLOB di base viene spostato nel nuovo livello. Eventuali snapshot attivi o eliminati temporaneamente rimangono nel livello originale. Non viene creato alcuno snapshot eliminato temporaneamente. | Il BLOB di base viene spostato nel nuovo livello. Tutte le versioni attive o eliminate temporaneamente rimangono nel livello originale. Non viene creata alcuna nuova versione. |

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Tutti i dati eliminati temporaneamente vengono fatturati alla stessa tariffa dei dati attivi. Non verranno addebitati costi per i dati che vengono eliminati definitivamente al termine del periodo di conservazione.

Quando si Abilita l'eliminazione temporanea, Microsoft consiglia di usare un periodo di conservazione breve per comprendere meglio il modo in cui la funzionalità influirà sulla fattura. Il periodo di memorizzazione minimo consigliato è di sette giorni.

L'abilitazione dell'eliminazione temporanea per i dati sovrascritti frequentemente può comportare un aumento degli addebiti per la capacità di archiviazione e una latenza maggiore È possibile mitigare questo costo aggiuntivo e la latenza archiviando i dati sovrascritti di frequente in un account di archiviazione separato in cui l'eliminazione temporanea è disabilitata.

Non vengono addebitate le transazioni relative alla generazione automatica di snapshot o versioni quando un BLOB viene sovrascritto o eliminato. Vengono addebitate le chiamate all'operazione di **annullamento dell'eliminazione del BLOB** al tasso delle transazioni per le operazioni di scrittura.

Per altre informazioni sui prezzi per l'archiviazione BLOB, vedere la pagina [prezzi di archiviazione BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/) .

## <a name="blob-soft-delete-and-virtual-machine-disks"></a>Eliminazione temporanea BLOB e dischi delle macchine virtuali  

L'eliminazione temporanea dei BLOB è disponibile per i dischi non gestiti sia Premium che standard, che sono BLOB di pagine dietro le quinte. L'eliminazione temporanea consente di ripristinare i dati eliminati o sovrascritti dall'elenco **Elimina BLOB**, **Put Blob**, **Put Block** e copy solo le operazioni **BLOB** .

I dati sovrascritti da una chiamata alla **pagina put** non sono recuperabili. Una macchina virtuale di Azure scrive in un disco non gestito usando le chiamate a **Put Page**, quindi l'uso dell'eliminazione temporanea per annullare le Scritture in un disco non gestito da una macchina virtuale di Azure non è uno scenario supportato.

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare l'eliminazione temporanea per i BLOB](./soft-delete-blob-enable.md)
- [Gestire e ripristinare BLOB eliminati temporaneamente](soft-delete-blob-manage.md)
- [Controllo delle versioni dei BLOB](versioning-overview.md)