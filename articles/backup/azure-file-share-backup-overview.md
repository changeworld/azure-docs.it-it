---
title: Informazioni sul backup di condivisione file di Azure
description: Informazioni su come eseguire il backup di condivisioni file di Azure nell'insieme di credenziali di Servizi di ripristino
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: c4f9dd816ace2c9aec8f48207fbce88acf34e24a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516816"
---
# <a name="about-azure-file-share-backup"></a>Informazioni sul backup di condivisione file di Azure

Il backup di condivisione file di Azure è una soluzione di backup nativa basata sul cloud che protegge i dati nel cloud ed elimina i sovraccariche di manutenzione aggiuntivi coinvolti nelle soluzioni di backup locali. Il Backup di Azure si integra perfettamente con Sincronizzazione file di Azure e consente di centralizzare i dati della condivisione file e i backup. Questa soluzione semplice, affidabile e sicura consente di configurare la protezione per le condivisioni file aziendali in pochi semplici passaggi, con la certezza che è possibile ripristinare i dati in qualsiasi scenario di emergenza.

## <a name="key-benefits-of-azure-file-share-backup"></a>Vantaggi principali del backup di condivisione file di Azure

* **Nessuna infrastruttura:** non è necessaria alcuna distribuzione per configurare la protezione per le condivisioni file.
* **Conservazione personalizzata:** è possibile configurare i backup con conservazione giornaliera/settimanale/mensile/annuale in base alle esigenze.
* **Funzionalità di gestione incorporate:** è possibile pianificare i backup e specificare il periodo di conservazione desiderato senza il sovraccarico aggiuntivo dell'eliminazione dei dati.
* **Ripristino istantaneo:** il backup di condivisione file di Azure usa snapshot di condivisione file, quindi è possibile selezionare solo i file da ripristinare immediatamente.
* **Avvisi e creazione di report:** è possibile configurare avvisi per gli errori di backup e ripristino e usare la soluzione di creazione di report fornita da Backup di Azure per ottenere informazioni dettagliate sui backup nelle condivisioni file.
* **Protezione dall'eliminazione accidentale** di condivisioni [](../storage/files/storage-files-prevent-file-share-deletion.md) file: Backup di Azure abilita la funzionalità di eliminazione automatica a livello di account di archiviazione con un periodo di conservazione di 14 giorni. Anche se un utente malintenzionato elimina la condivisione file, il contenuto e i punti di ripristino (snapshot) della condivisione file vengono mantenuti per un periodo di conservazione configurabile, consentendo il recupero corretto e completo del contenuto di origine e degli snapshot senza perdita di dati.

## <a name="architecture"></a>Architettura

![Architettura del backup della condivisione file di Azure](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Funzionamento del processo di backup

1. Il primo passaggio per la configurazione del backup per le condivisioni file di Azure consiste nella creazione di un insieme di credenziali di Servizi di ripristino. L'insieme di credenziali offre una visualizzazione consolidata dei backup configurati in carichi di lavoro diversi.

2. Dopo aver creato un insieme di credenziali, il Backup di Azure individua gli account di archiviazione che possono essere registrati con l'insieme di credenziali. È possibile selezionare l'account di archiviazione che ospita le condivisioni file da proteggere.

3. Dopo aver selezionato l'account di archiviazione, il servizio Backup di Azure elenca il set di condivisioni file presenti nell'account di archiviazione e ne archivia i nomi nel catalogo del livello di gestione.

4. Configurare quindi i criteri di backup (pianificazione e conservazione) in base alle esigenze e selezionare le condivisioni file di cui eseguire il backup. Il Backup di Azure registra le pianificazioni nel piano di controllo per eseguire backup pianificati.

5. In base ai criteri specificati, l'utilità Backup di Azure di pianificazione attiva i backup all'ora pianificata. Come parte di tale processo, lo snapshot di condivisione file viene creato usando l'API di condivisione file. Nell'archivio dei metadati viene archiviato solo l'URL dello snapshot.

    >[!NOTE]
    >I dati della condivisione file non vengono trasferiti al servizio Backup, perché il servizio Backup crea e gestisce gli snapshot che fanno parte dell'account di archiviazione e i backup non vengono trasferiti nell'insieme di credenziali.

6. È possibile ripristinare il contenuto della condivisione file di Azure (singoli file o la condivisione completa) dagli snapshot disponibili nella condivisione file di origine. Dopo l'attivazione dell'operazione, l'URL dello snapshot viene recuperato dall'archivio dei metadati e i dati vengono elencati e trasferiti dallo snapshot di origine alla condivisione file di destinazione scelta.

7. Se si usa Sincronizzazione file di Azure, il servizio Backup indica al servizio Sincronizzazione file di Azure i percorsi dei file da ripristinare, che quindi attiva un processo di rilevamento delle modifiche in background su questi file. Tutti i file modificati vengono sincronizzati con l'endpoint server. Questo processo si verifica in parallelo con il ripristino originale nella condivisione file di Azure.

8. Viene eseguito il push dei dati di monitoraggio dei processi di backup e ripristino nel Backup di Azure monitoraggio. In questo modo è possibile monitorare i backup cloud per le condivisioni file in un singolo dashboard. Inoltre, è anche possibile configurare avvisi o notifiche tramite posta elettronica quando l'integrità del backup è interessata. I messaggi di posta elettronica vengono inviati tramite il servizio di posta elettronica di Azure.

## <a name="backup-costs"></a>Costi di backup

Alla soluzione di backup della condivisione file di Azure sono associati due costi:

1. **Costo di archiviazione snapshot:** gli addebiti di archiviazione sostenuti per gli snapshot vengono fatturati File di Azure'utilizzo in base ai dettagli dei prezzi indicati [qui](https://azure.microsoft.com/pricing/details/storage/files/)

2. **Tariffa dell'istanza** protetta: a partire dal 1° settembre 2020, ai clienti verrà addebitata una tariffa per l'istanza protetta in base ai dettagli dei prezzi indicati [qui.](https://azure.microsoft.com/pricing/details/backup/) La tariffa dell'istanza protetta dipende dalle dimensioni totali delle condivisioni file protette in un account di archiviazione.

Per ottenere stime dettagliate per il backup di condivisioni file di Azure, è possibile scaricare la stima dettagliata Backup di Azure [prezzi.](https://aka.ms/AzureBackupCostEstimates)  

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [Eseguire il backup di condivisioni file di Azure](backup-afs.md)
* Risposte alle [domande sul backup di File di Azure](backup-azure-files-faq.yml)
