---
title: Ripristinare i BLOB di Azure
description: Informazioni su come ripristinare i BLOB di Azure (in anteprima).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 4cbd47ea654115f00095e30f7d5114aec0f2c85a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745518"
---
# <a name="restore-azure-blobs-in-preview"></a>Ripristinare i BLOB di Azure (in anteprima)

I BLOB in blocchi negli account di archiviazione con il backup operativo configurato possono essere ripristinati in qualsiasi punto nel tempo entro il periodo di mantenimento dati. È anche possibile definire l'ambito dei ripristini a tutti i BLOB in blocchi nell'account di archiviazione o a un subset di BLOB.

## <a name="before-you-start"></a>Prima di iniziare

- I BLOB verranno ripristinati nello stesso account di archiviazione. Quindi, i BLOB che sono stati modificati dal momento in cui si esegue il ripristino verranno sovrascritti.
- Come parte di un'operazione di ripristino, è possibile ripristinare solo i BLOB in blocchi in un account di archiviazione standard per utilizzo generico V2. I BLOB di Accodamento, i BLOB di pagine e i BLOB in blocchi Premium non vengono ripristinati.
- Mentre è in corso un processo di ripristino, i BLOB nell'archivio non possono essere letti o scritti.
- Non è possibile ripristinare un BLOB con un lease attivo. Se un BLOB con un lease attivo è incluso nell'intervallo di BLOB da ripristinare, l'operazione di ripristino avrà esito negativo automaticamente. Interrompi tutti i lease attivi prima di avviare l'operazione di ripristino.
- Gli snapshot non vengono creati o eliminati come parte di un'operazione di ripristino. Solo il BLOB di base viene ripristinato allo stato precedente.
- Se si elimina un contenitore dall'account di archiviazione chiamando l'operazione **Delete Container** , il contenitore non può essere ripristinato con un'operazione di ripristino. Anziché eliminare un intero contenitore, eliminare i singoli BLOB se si desidera ripristinarli in un secondo momento. Microsoft consiglia inoltre di abilitare l'eliminazione temporanea per i contenitori oltre al backup operativo per proteggersi da eliminazioni accidentali di contenitori.
- Per tutte le limitazioni e gli scenari supportati, vedere la [matrice di supporto](blob-backup-support-matrix.md) .

## <a name="restore-blobs"></a>Ripristinare i BLOB

È possibile avviare un ripristino tramite il centro di backup.

1. In centro backup passare a **Ripristina** nella barra superiore.

    ![Ripristino nel centro di backup](./media/blob-restore/backup-center-restore.png)

1. Nella scheda **Avvia ripristino** scegliere **BLOB di Azure (archiviazione di Azure)** come tipo di origine dati e selezionare l' **istanza di backup** che si desidera ripristinare. L'istanza di backup è l'account di archiviazione che contiene i BLOB da ripristinare.

     ![Selezionare l'istanza di backup](./media/blob-restore/select-backup-instance.png)

1. Nella scheda **Seleziona punto di ripristino** scegliere la data e l'ora da cui si desidera ripristinare i dati. È anche possibile usare il dispositivo di scorrimento per scegliere il momento in cui eseguire il ripristino. La bolla info accanto alla data Mostra la durata valida dalla quale è possibile ripristinare i dati. Il backup operativo per i BLOB con backup continuo offre un controllo granulare sui punti per il ripristino dei dati.

    >[!NOTE]
    > L'ora rappresentata qui è l'ora locale.

    ![Data e ora per il ripristino](./media/blob-restore/date-and-time.png)

1. Nella scheda **Ripristina parametri** scegliere se si vuole ripristinare tutti i BLOB nell'account di archiviazione, in contenitori specifici o in un subset di BLOB usando la corrispondenza di prefisso. Quando si usa la corrispondenza di prefisso, è possibile specificare fino a 10 intervalli di prefissi o FilePath. Per altri dettagli sull'uso della corrispondenza di prefisso, vedere [qui](#use-prefix-match-for-restoring-blobs).

    ![Parametri di ripristino](./media/blob-restore/restore-parameters.png)

    Scegliere una delle opzioni seguenti:

    - **Ripristinare tutti i BLOB nell'account di archiviazione**: l'uso di questa opzione consente di ripristinare tutti i BLOB in blocchi nell'account di archiviazione eseguendone il rollback al punto nel tempo selezionato. Gli account di archiviazione che contengono grandi quantità di dati o la presenza di una varianza elevata potrebbero richiedere più tempo per il ripristino.

    - **Cerca e ripristina i contenitori selezionati**: l'uso di questa opzione consente di cercare e selezionare fino a 10 contenitori da ripristinare. È necessario disporre di autorizzazioni sufficienti per visualizzare i contenitori nell'account di archiviazione. in caso contrario, potrebbe non essere possibile visualizzare il contenuto dell'account di archiviazione.

    - **Selezionare i BLOB da ripristinare usando la corrispondenza del prefisso**: questa opzione consente di ripristinare un subset di BLOB usando una corrispondenza con prefisso. È possibile specificare fino a 10 intervalli di lessicografico di BLOB all'interno di un singolo contenitore o tra più contenitori per restituire tali BLOB allo stato precedente in un determinato momento. Ecco alcuni aspetti da tenere presente:

        - È possibile usare una barra (/) per delineare il nome del contenitore dal prefisso del BLOB
        - L'inizio dell'intervallo specificato è incluso, tuttavia l'intervallo specificato è esclusivo.

    Per ulteriori informazioni sull'utilizzo dei prefissi per ripristinare gli intervalli di BLOB, vedere [questa sezione](#use-prefix-match-for-restoring-blobs).

1. Al termine della definizione dei BLOB da ripristinare, passare alla scheda **revisione e ripristino** e selezionare **Ripristina** per avviare il ripristino.

1. **Track Restore**: usare la visualizzazione **processi di backup** per tenere traccia dei dettagli e dello stato dei ripristini. A tale scopo, passare a **Backup Center**  >  **Backup Jobs**. Lo stato verrà visualizzato **in corso** durante l'esecuzione del ripristino.

    ![Scheda processi di backup](./media/blob-restore/backup-jobs.png)

    Quando l'operazione di ripristino viene completata correttamente, lo stato cambia in **completato**. Una volta completata l'operazione di ripristino, sarà possibile leggere e scrivere i BLOB nell'account di archiviazione.

## <a name="additional-topics"></a>Argomenti aggiuntivi

### <a name="use-prefix-match-for-restoring-blobs"></a>USA corrispondenza prefisso per il ripristino dei BLOB

Si consideri l'esempio seguente:

![Ripristino con corrispondenza prefisso](./media/blob-restore/prefix-match.png)

L'operazione di ripristino mostrata nell'immagine esegue le azioni seguenti:

- Ripristina il contenuto completo di *container1*.
- Ripristina i BLOB nell'intervallo lessicografico *blob1* tramite *blob5* in *container2*. Questo intervallo consente di ripristinare i BLOB con nomi come *blob1*, *blob11*, *blob100*, *blob2* e così via. Poiché la fine dell'intervallo è esclusiva, ripristina i BLOB i cui nomi iniziano con *blob4*, ma non ripristina i BLOB i cui nomi iniziano con *blob5*.
- Ripristina tutti i BLOB in *container3* e *container4*. Poiché la fine dell'intervallo è esclusiva, questo intervallo non esegue il ripristino di *container5*.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica del backup operativo per i BLOB di Azure (in anteprima)](blob-backup-overview.md)
