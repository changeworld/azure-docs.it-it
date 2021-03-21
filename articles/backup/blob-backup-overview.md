---
title: Panoramica del backup operativo per i BLOB di Azure
description: Informazioni sul backup operativo per i BLOB di Azure (in anteprima).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: b10191c8a01d3cc7a92dee8ca9bf59a506497a60
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745098"
---
# <a name="overview-of-operational-backup-for-azure-blobs-in-preview"></a>Panoramica del backup operativo per i BLOB di Azure (in anteprima)

Il backup operativo per i BLOB è una soluzione gestita di protezione dei dati locale che consente di proteggere i BLOB in blocchi da diversi scenari di perdita di dati, ad esempio danneggiamenti, eliminazioni di BLOB e eliminazione accidentale di account di archiviazione. I dati vengono archiviati in locale all'interno dell'account di archiviazione di origine e possono essere ripristinati in un punto nel tempo selezionato, se necessario. Quindi, fornisce un mezzo semplice, sicuro ed economicamente conveniente per proteggere i BLOB.

Il backup operativo per i BLOB si integra con [Backup Center](backup-center-overview.md), tra le altre funzionalità di gestione dei backup, per fornire un unico riquadro di vetro che consente di gestire, monitorare, operare e analizzare i backup su larga scala.

## <a name="how-operational-backup-works"></a>Funzionamento del backup operativo

Il backup operativo dei BLOB è una soluzione di **backup locale** . Quindi, i dati di backup non vengono trasferiti nell'insieme di credenziali di backup, ma vengono archiviati nell'account di archiviazione di origine. Tuttavia, l'insieme di credenziali per il backup funge anche da unità di gestione dei backup. Si tratta inoltre di una soluzione di **backup continuo** , che significa che non è necessario pianificare alcun backup e tutte le modifiche verranno conservate e ripristinabili dallo stato in un momento specifico.

Il backup operativo usa le funzionalità della piattaforma BLOB per proteggere i dati e consentire il ripristino quando necessario:

- **Ripristino temporizzato**: il [ripristino temporizzato del BLOB](https://docs.microsoft.com/azure/storage/blobs/point-in-time-restore-overview) consente il ripristino dei dati BLOB in uno stato precedente. Questo, a sua volta, usa l' **eliminazione** temporanea, il **feed delle modifiche** e il controllo delle **versioni dei BLOB** per conservare i dati per la durata specificata. Il backup operativo si occupa dell'abilitazione del ripristino temporizzato e delle funzionalità sottostanti per garantire la conservazione dei dati per la durata specificata.

- **Delete Lock**: Delete Lock impedisce l'eliminazione accidentale dell'account di archiviazione o da parte di utenti non autorizzati. Il backup operativo quando configurato consente anche di applicare automaticamente un blocco di eliminazione per ridurre le possibilità di perdita di dati a causa dell'eliminazione dell'account di archiviazione.

Per informazioni sulle limitazioni della soluzione corrente, vedere la [matrice di supporto](blob-backup-support-matrix.md) .

### <a name="protection"></a>Protezione

Il backup operativo è configurato e gestito a livello di **account di archiviazione** e si applica a tutti i BLOB in blocchi nell'account di archiviazione. Il backup operativo usa un **criterio di backup** per gestire la durata di conservazione dei dati di backup (incluse le versioni precedenti e i BLOB eliminati), in modo da definire il periodo in cui è possibile ripristinare i dati. Il criterio di backup può avere un periodo di conservazione massimo di 360 giorni oppure un numero equivalente di settimane complete (51) o mesi (11).

Quando si configura il backup per un account di archiviazione e si assegna un criterio di backup con un periodo di conservazione di ' n'giorni, le proprietà sottostanti vengono impostate come descritto di seguito. È possibile visualizzare queste proprietà nella scheda **protezione dati** del servizio BLOB nell'account di archiviazione.

- **Ripristino temporizzato**: impostare su' n'giorni, come definito nei criteri di backup. Se per l'account di archiviazione è già stato abilitato il temporizzato con un periodo di conservazione, ad indicare ' x ' giorni, prima di configurare il backup, la durata del ripristino temporizzato verrà impostata sul valore maggiore dei due valori, ovvero Max (n, x). Se è già stato abilitato il ripristino temporizzato e si è specificato che la conservazione è maggiore di quella nel criterio di backup, rimarrà invariata.

- **Eliminazione** temporanea: impostare su "n + 5" giorni, ovvero cinque giorni, oltre alla durata specificata nei criteri di backup. Se per l'account di archiviazione configurato per il backup operativo è già stata abilitata l'eliminazione temporanea con un periodo di conservazione di, ad indicare "y" giorni, il periodo di memorizzazione dell'eliminazione temporanea verrà impostato sul valore massimo dei due valori, ovvero Max (n + 5, y). Se è già stata abilitata l'eliminazione temporanea e si è specificato che la conservazione è maggiore di quella conforme ai criteri di backup, rimarrà invariata.

- **Controllo delle versioni per BLOB e feed di modifiche BLOB**: il controllo delle versioni e il feed delle modifiche sono abilitati per gli account di archiviazione configurati per il backup operativo.

- **Delete Lock**: la configurazione del backup operativo in un account di archiviazione applica anche un blocco Delete per l'account di archiviazione. Il blocco di eliminazione applicato dal backup può essere visualizzato nella scheda **blocchi** dell'account di archiviazione.

Per consentire al backup di abilitare queste proprietà negli account di archiviazione da proteggere, all'insieme di credenziali per il backup deve essere concesso il ruolo di **collaboratore backup dell'account di archiviazione** nei rispettivi account di archiviazione.

>[!NOTE]
>Il backup operativo supporta solo le operazioni sui BLOB in blocchi e le operazioni sui contenitori non possono essere ripristinate. Se si elimina un contenitore dall'account di archiviazione chiamando l'operazione **Delete Container** , il contenitore non può essere ripristinato con un'operazione di ripristino. È consigliabile abilitare l'eliminazione temporanea per migliorare la protezione e il ripristino dei dati.

### <a name="management"></a>Gestione

Dopo aver abilitato il backup in un account di archiviazione, viene creata un'istanza di backup corrispondente all'account di archiviazione nell'insieme di credenziali per il backup. È possibile eseguire qualsiasi operazione relativa al backup per un account di archiviazione, ad esempio l'avvio di ripristini, il monitoraggio, l'arresto della protezione e così via, tramite l'istanza di backup corrispondente.

Il backup operativo si integra anche direttamente con backup Center per semplificare la gestione della protezione di tutti gli account di archiviazione in modo centralizzato, insieme a tutti gli altri carichi di lavoro supportati per il backup. Il centro di backup è l'unico riquadro per tutti i requisiti di backup, ad esempio il monitoraggio dei processi e lo stato di backup e ripristini, garantendo conformità e governance, analizzando l'utilizzo del backup ed eseguendo operazioni relative al backup e al ripristino dei dati.

### <a name="restore"></a>Restore

È possibile ripristinare i dati da qualsiasi punto nel tempo per cui esiste un punto di ripristino. Un punto di ripristino viene creato quando un account di archiviazione si trova nello stato protetto e può essere usato per ripristinare i dati a condizione che rientri nel periodo di memorizzazione definito dal criterio di backup (e quindi la funzionalità di ripristino temporizzato del servizio BLOB nell'account di archiviazione). Il backup operativo usa il ripristino temporizzato del BLOB per ripristinare i dati da un punto di ripristino.

Il backup operativo offre la possibilità di ripristinare tutti i BLOB in blocchi nell'account di archiviazione, esplorare e ripristinare contenitori specifici o usare corrispondenze di prefisso per ripristinare un subset di BLOB. Tutti i ripristini possono essere eseguiti solo nell'account di archiviazione di origine.

## <a name="pricing"></a>Prezzi

Quando si usa il backup operativo per i BLOB, non verrà addebitato alcun costo di gestione o di istanza. Si otterranno tuttavia gli addebiti seguenti:

- I ripristini vengono eseguiti tramite il ripristino temporizzato del BLOB e attirano gli addebiti in base alla quantità di dati elaborati. Per altre informazioni, vedere [prezzi di ripristino temporizzato](https://docs.microsoft.com/azure/storage/blobs/point-in-time-restore-overview#pricing-and-billing).

- Conservazione dei dati a causa dell' [eliminazione temporanea per i BLOB](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview), del supporto del feed delle [modifiche nell'archiviazione BLOB di Azure e del controllo delle](https://docs.microsoft.com/azure/storage/blobs/storage-blob-change-feed) [versioni dei BLOB](https://docs.microsoft.com/azure/storage/blobs/versioning-overview).

## <a name="next-steps"></a>Passaggi successivi

- [Configurare e gestire il backup BLOB di Azure](blob-backup-configure-manage.md)
