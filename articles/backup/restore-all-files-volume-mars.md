---
title: Ripristinare tutti i file in un volume con MARS
description: Informazioni su come ripristinare tutti i file in un volume usando l'agente MARS.
ms.topic: conceptual
ms.date: 01/17/2021
ms.openlocfilehash: 44c12809fc94f78721ab1788cb352076dfebabe4
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613970"
---
# <a name="restore-all-the-files-in-a-volume-using-the-mars-agent"></a>Ripristinare tutti i file in un volume usando l'agente MARS

Questo articolo illustra come ripristinare tutti i file di cui è stato eseguito il backup in un intero volume usando la procedura guidata ripristino dati nell'agente di Servizi di ripristino di Microsoft Azure (MARS). È possibile:

- Ripristinare tutti i file di cui è stato eseguito il backup in un volume nello stesso computer da cui sono stati eseguiti i backup.
- Ripristinare tutti i file di backup di un volume in un computer alternativo.

>[!TIP]
>L'opzione **Volume** consente di ripristinare tutti i dati di cui è stato eseguito il backup in un volume specificato. Questa opzione offre velocità di trasferimento più veloci (fino a 40 MBps) ed è consigliata per il ripristino di dati di grandi dimensioni o interi volumi.
>
>L' **opzione singoli file e cartelle** consente di accedere rapidamente ai dati dei punti di ripristino. È adatto per il ripristino di singoli file ed è consigliato per una dimensione totale inferiore a 80 GB. Offre velocità di trasferimento o copia fino a 6 MBps durante il ripristino.

## <a name="volume-level-restore-to-the-same-machine"></a>Ripristino a livello di volume nello stesso computer

I passaggi seguenti consentiranno di ripristinare tutti i file di backup in un volume:

1. Aprire lo snap-in di **Backup di Microsoft Azure** . Se non si sa dove è stato installato lo snap-in, cercare **Backup di Microsoft Azure** nel computer o nel server. L'applicazione desktop dovrebbe essere visualizzata nei risultati della ricerca.

1. Selezionare **Ripristina dati** per avviare la procedura guidata.

    ![Menu Ripristina dati](./media/restore-all-files-volume-mars/recover.png)

1. Nella pagina **Introduzione** , per ripristinare i dati nello stesso server o computer, selezionare **questo server (nome server)**  >  **Avanti**.

    ![Pagina introduttiva](./media/restore-all-files-volume-mars/same-machine-instant-restore.png)

1. Nella pagina **Seleziona modalità di ripristino** scegliere **volume**  >  **Avanti**.

    ![Selezionare la modalità di ripristino](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. Nella pagina **Seleziona volume e data** selezionare il volume che si desidera ripristinare.

    Nel calendario selezionare un punto di ripristino. Le date in **grassetto** indicano la disponibilità di almeno un punto di ripristino. Se sono disponibili più punti di ripristino, scegliere quello appropriato nell'elenco a discesa **Ora**.

     ![Seleziona volume e data](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Nella pagina **Specifica opzioni di ripristino** configurare il comportamento di ripristino.
    1. Scegliere la destinazione di ripristino:
        - **Percorso originale**: ripristinare i dati nel percorso originale.
        - **Un altro percorso**: specificare un percorso alternativo in cui ripristinare i dati.
    1. Scegliere il comportamento per **quando gli elementi nel backup si trovano già nella destinazione di ripristino**:
        - **Creare copie in modo da disporre di entrambe le versioni**: se esiste già un file con lo stesso nome, i dati nel punto di ripristino verranno ripristinati come copia. La copia disporrà di un prefisso di nome file localizzato utilizzando l'ora del processo di ripristino locale in uno dei formati seguenti:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Sovrascrivi versioni esistenti con le versioni ripristinate**: se esiste già un file con lo stesso nome, il contenuto viene sostituito con i dati nel punto di ripristino.
        - Non **ripristinare gli elementi già presenti nella destinazione di ripristino**: se un file con lo stesso nome esiste già, viene ignorato.
    1. **Abilitare Ripristina le autorizzazioni dell'elenco di controllo di accesso (ACL) per il file o la cartella** da ripristinare se il file deve essere ripristinato con le autorizzazioni originali nel punto di ripristino.
        ![Specifica opzioni di ripristino](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Verificare i dettagli del ripristino nel riquadro **conferma** e selezionare **Ripristina**.

    ![Dettagli conferma](./media/restore-all-files-volume-mars/confirmation-details.png)

1. Nella pagina **Stato ripristino** monitorare lo stato di avanzamento del processo di ripristino. La procedura guidata può anche essere chiusa in modo sicuro e l'operazione di ripristino continuerà in background. È possibile visualizzare di nuovo lo stato di avanzamento facendo doppio clic sul processo di ripristino nel dashboard.

## <a name="volume-level-restore-to-an-alternate-machine"></a>Ripristino a livello di volume in un computer alternativo

La procedura seguente consente di ripristinare tutti i file di backup di un volume in un computer alternativo. È possibile usare questi passaggi per ripristinare i dati da backup di Azure in caso di perdita dell'intero server.

Tali passaggi usano la terminologia seguente:

- *Computer di origine*: il computer di origine da cui è stato eseguito il backup e che non è attualmente disponibile.
- *Computer di destinazione* : il computer in cui i dati vengono ripristinati.
- *Insieme di credenziali di esempio*: l'insieme di credenziali dei servizi di ripristino in cui il computer di origine e il computer di destinazione sono registrati.

> [!NOTE]
> Non è possibile ripristinare i backup in un computer di destinazione in cui è in esecuzione una versione precedente del sistema operativo. Un backup eseguito in un computer con Windows 7, ad esempio, può essere ripristinato in un computer con Windows 7 o versioni successive. Non è possibile ripristinare un backup effettuato da un computer Windows 10 in un computer con Windows 7.

1. Aprire lo snap-in di **Backup di Microsoft Azure** nel computer di destinazione.

1. Assicurarsi che il computer di destinazione e il computer di origine siano registrati nello stesso insieme di credenziali dei servizi di ripristino.

1. Selezionare **Ripristina dati** per aprire il **Ripristino guidato dei dati**.

    ![Screenshot di backup di Azure con i dati di ripristino evidenziati (ripristino in un computer alternativo)](./media/backup-azure-restore-windows-server/recover.png)

1. Nella pagina **Guida introduttiva** selezionare **Another server** (Un altro server).

    ![Screenshot della procedura guidata ripristino dati Introduzione pagina (ripristino nel computer alternativo)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

1. Specificare il file dell'insieme di credenziali che corrisponde all'insieme di credenziali di esempio, quindi fare clic su **Avanti**.

    Se il file dell'insieme di credenziali non è valido (o è scaduto), [scaricare un nuovo file di archivio delle credenziali dall'](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file) insieme di credenziali di esempio nel portale di Azure. Dopo aver specificato un insieme di credenziali valido, viene visualizzato il nome dell'insieme di credenziali di backup corrispondente.

1. Nel riquadro **Seleziona server di backup** selezionare il computer di origine nell'elenco dei computer visualizzati e specificare la passphrase. Fare quindi clic su **Avanti**.

    ![Screenshot della procedura guidata ripristino dati pagina Selezione server di backup (Ripristina computer alternativo)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

1. Nella pagina **Seleziona modalità di ripristino** scegliere **volume**  >  **Avanti**.

    ![Selezionare la modalità di ripristino](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. Nella pagina **Seleziona volume e data** selezionare il volume che si desidera ripristinare.

    Nel calendario selezionare un punto di ripristino. Le date in **grassetto** indicano la disponibilità di almeno un punto di ripristino. Se sono disponibili più punti di ripristino, scegliere quello appropriato nell'elenco a discesa **Ora**.

     ![Seleziona volume e data](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Nella pagina **Specifica opzioni di ripristino** configurare il comportamento di ripristino.
    1. Scegliere la destinazione di ripristino:
        - **Percorso originale**: ripristinare i dati nel percorso originale.
        - **Un altro percorso**: specificare un percorso alternativo in cui ripristinare i dati.
    1. Scegliere il comportamento per **quando gli elementi nel backup si trovano già nella destinazione di ripristino**:
        - **Creare copie in modo da disporre di entrambe le versioni**: se esiste già un file con lo stesso nome, i dati nel punto di ripristino verranno ripristinati come copia. La copia disporrà di un prefisso di nome file localizzato utilizzando l'ora del processo di ripristino locale in uno dei formati seguenti:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Sovrascrivi versioni esistenti con le versioni ripristinate**: se esiste già un file con lo stesso nome, il contenuto viene sostituito con i dati nel punto di ripristino.
        - Non **ripristinare gli elementi già presenti nella destinazione di ripristino**: se un file con lo stesso nome esiste già, viene ignorato.
    1. **Abilitare Ripristina le autorizzazioni dell'elenco di controllo di accesso (ACL) per il file o la cartella** da ripristinare se il file deve essere ripristinato con le autorizzazioni originali nel punto di ripristino.
        ![Specifica opzioni di ripristino](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Verificare i dettagli del ripristino nel riquadro **conferma** e selezionare **Ripristina**.

    ![Dettagli conferma](./media/restore-all-files-volume-mars/confirmation-details.png)

1. Nella pagina **Stato ripristino** monitorare lo stato di avanzamento del processo di ripristino. La procedura guidata può anche essere chiusa in modo sicuro e l'operazione di ripristino continuerà in background. È possibile visualizzare di nuovo lo stato di avanzamento facendo doppio clic sul processo di ripristino nel dashboard.

## <a name="next-steps"></a>Passaggi successivi

- Dopo aver ripristinato i file e le cartelle, è possibile [gestire i backup](backup-azure-manage-windows-server.md).
- Trovare [domande frequenti sul backup di file e cartelle](backup-azure-file-folder-backup-faq.md).
