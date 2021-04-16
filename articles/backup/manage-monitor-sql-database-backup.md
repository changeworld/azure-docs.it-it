---
title: Gestire e monitorare SQL Server database in una macchina virtuale di Azure
description: Questo articolo descrive come gestire e monitorare SQL Server database in esecuzione in una macchina virtuale di Azure.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 3938e26e134f7d823d8a6f6fac631ebf4442e6ab
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519137"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Gestire e monitorare i database SQL Server di cui è stato eseguito il backup

Questo articolo descrive le attività comuni per la gestione e il monitoraggio di database SQL Server in esecuzione in una macchina virtuale di Azure e di cui viene eseguito il backup in un insieme di credenziali di Servizi di ripristino Backup di Azure dal [servizio Backup di Azure.](backup-overview.md) Si apprenderà come monitorare processi e avvisi, arrestare e riprendere la protezione del database, eseguire processi di backup e annullare la registrazione di una macchina virtuale dai backup.

Se i backup per i database SQL Server non sono ancora stati configurati, vedere Eseguire il backup SQL Server [database nelle macchine](backup-azure-sql-database.md) virtuali di Azure

## <a name="monitor-backup-jobs-in-the-portal"></a>Monitorare i processi di backup nel portale

Backup di Azure mostra tutte le operazioni pianificate e su richiesta in Processi di **backup** nel portale, ad eccezione dei backup del log pianificati perché possono essere molto frequenti. I processi visualizzati in questo portale includono l'individuazione e la registrazione del database, la configurazione di backup e le operazioni di backup e ripristino.

![Portale dei processi di backup](./media/backup-azure-sql-database/sql-backup-jobs-list.png)

Per informazioni dettagliate sugli scenari di monitoraggio, vedere [Monitoraggio nella portale di Azure](backup-azure-monitoring-built-in-monitor.md) e monitoraggio usando [Monitoraggio di Azure](backup-azure-monitoring-use-azuremonitor.md).  

## <a name="view-backup-alerts"></a>Visualizzare gli avvisi di backup

Con i backup del log eseguiti ogni 15 minuti, monitorare i processi di backup può essere faticoso. Backup di Azure semplifica il monitoraggio inviando avvisi di posta elettronica. Gli avvisi di posta elettronica sono:

- Attivati per tutti gli errori di backup.
- Consolidati a livello di database in base al codice di errore.
- Inviati solo per il primo errore di backup di un database.

Per monitorare gli avvisi di backup del database:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel dashboard dell'insieme di credenziali, selezionare **Avvisi di backup**.

   ![Selezionare Avvisi di backup](./media/backup-azure-sql-database/sql-backup-alerts-list.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Interrompere la protezione di un database di SQL Server

È possibile interrompere i backup di un database di SQL Server in due modi:

- Interrompere tutti i processi di backup futuri ed eliminare tutti i punti di recupero.
- Arrestare tutti i processi di backup futuri e lasciare invariati i punti di ripristino.

Se si sceglie di lasciare invariati i punti di ripristino, tenere presente quanto segue:

- Tutti i punti di ripristino rimarranno invariati per sempre e tutte le eliminazioni verranno arrestate in caso di arresto della protezione con conservazione dei dati.
- Verranno addebitati i costi per l'istanza protetta e lo spazio di archiviazione utilizzato. Per altre informazioni, vedere [Prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).
- Se si elimina un'origine dati senza arrestare i backup, i nuovi backup avranno esito negativo. I punti di ripristino vecchi scadranno in base ai criteri, ma il punto di ripristino più recente verrà sempre mantenuto fino a quando non si arresteranno i backup e non si elimineranno i dati.

Per interrompere la protezione per un database:

1. Nel dashboard dell'insieme di credenziali, selezionare **Elementi di backup**.

2. In **Tipo di gestione backup** selezionare SQL nella macchina virtuale di **Azure.**

    ![Selezionare SQL nella macchina virtuale di Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Selezionare il database per cui si desidera interrompere la protezione.

    ![Selezionare il database per interrompere la protezione](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. Nel menu del database, selezionare **Interrompi backup**.

    ![Selezionare Interrompi backup](./media/backup-azure-sql-database/stop-db-button.png)

5. Nel menu **Interrompi Backup**, scegliere se mantenere o eliminare i dati. L'aggiunta di un motivo e commento è facoltativa.

    ![Conservare o eliminare dati nel menu Arresta backup](./media/backup-azure-sql-database/stop-backup-button.png)

6. Selezionare **Interrompi backup**.

> [!NOTE]
>
>Per altre informazioni sull'opzione elimina dati, vedere le domande frequenti seguenti:
>
>- [Se si elimina un database da un'istanza autoprotetta, cosa accade ai backup?](faq-backup-sql-server.yml#if-i-delete-a-database-from-an-autoprotected-instance--what-will-happen-to-the-backups-)
>- [Se si arresta l'operazione di backup di un database protetto automaticamente, quale sarà il suo comportamento?](faq-backup-sql-server.yml#if-i-change-the-name-of-the-database-after-it-has-been-protected--what-will-be-the-behavior-)
>
>

## <a name="resume-protection-for-a-sql-database"></a>Riprendere la protezione per un database SQL

Quando si arresta la protezione per il database SQL, se si seleziona l'opzione Mantieni dati **di backup,** è possibile riprendere la protezione in un secondo momento. Se non si mantengono i dati di backup, non è possibile riprendere la protezione.

Per riprendere la protezione per un database SQL:

1. Aprire l'elemento di backup e selezionare **Riprendi backup**.

    ![Selezionare Riprendi backup per riprendere la protezione del database](./media/backup-azure-sql-database/resume-backup-button.png)

2. Selezionare un criterio dal menu **Criteri di backup** e quindi selezionare **Salva**.

## <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta

È possibile eseguire diversi tipi di backup su richiesta:

- Backup completo
- Backup completo solo copia
- Backup differenziale
- Backup dei log

Anche se è necessario specificare la durata della conservazione per il backup completo di sola copia, l'intervallo di conservazione per il backup completo su richiesta verrà impostato automaticamente su 45 giorni dall'ora corrente.

Per altre informazioni, vedere tipi [SQL Server backup](backup-architecture.md#sql-server-backup-types).

## <a name="modify-policy"></a>Modifica dei criteri

Modificare i criteri per modificare la frequenza di backup o l'intervallo di conservazione.

> [!NOTE]
> Eventuali modifiche apportate al periodo di conservazione verranno applicate in modo retrospettivo a tutti i punti di ripristino meno recenti oltre a quelli nuovi.

Nel dashboard dell'insieme di credenziali passare a **Gestisci**  >  **criteri di backup** e scegliere i criteri da modificare.

  ![Gestire i criteri di backup](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Modificare i criteri di backup](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

La modifica dei criteri ha effetto su tutti gli elementi di backup associati e attiva processi **Configura protezione** corrispondenti.

### <a name="inconsistent-policy"></a>Criteri non coerenti

In alcuni casi, un'operazione di modifica dei criteri può causare una versione dei criteri **incoerente** per alcuni elementi di backup. Ciò si verifica quando il criterio **Configura protezione** corrispondente ha esito negativo per l'elemento di backup dopo l'attivazione di un'operazione di modifica dei criteri. Nella vista dell'elemento di backup viene visualizzato quanto segue:

  ![Criteri non coerenti](./media/backup-azure-sql-database/inconsistent-policy.png)

È possibile correggere la versione dei criteri per tutti gli elementi interessati con un solo clic:

  ![Correzione di criteri incoerenti](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="unregister-a-sql-server-instance"></a>Annullare un’istanza SQL &Server

Annullare la registrazione di SQL Server dopo aver disabilitato la protezione, ma prima di eliminare l'insieme di credenziali:

1. Nel dashboard dell'insieme di credenziali, in **Gestisci**, selezionare **Infrastruttura di backup**.  

   ![Selezionare Infrastruttura di backup](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. In **Server di gestione** selezionare **Server protetti**.

   ![Selezionare Server protetti](./media/backup-azure-sql-database/protected-servers.png)

3. In **Server protetti** selezionare il server di cui si vuole annullare la registrazione. Per eliminare l'insieme di credenziali, è necessario annullare la registrazione di tutti i server.

4. Fare clic con il pulsante destro del mouse sul server protetto e **scegliere Annulla registrazione**.

   ![Selezionare Elimina](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Registrare nuovamente l'estensione nella SQL Server virtuale

In alcuni casi, l'estensione del carico di lavoro nella macchina virtuale può essere influenzata per un motivo o un altro. In questi casi, tutte le operazioni attivate nella macchina virtuale inizieranno ad avere esito negativo. Potrà quindi essere necessario registrare nuovamente l'estensione nella macchina virtuale. **L'operazione Di nuovo registrazione** reinstalla l'estensione di backup del carico di lavoro nella macchina virtuale per continuare le operazioni. Questa opzione è disponibile in Infrastruttura **di backup nell'insieme** di credenziali di Servizi di ripristino.

![Server protetti nell'infrastruttura di backup](./media/backup-azure-sql-database/protected-servers-backup-infrastructure.png)

Questa opzione deve essere usata con attenzione. Se attivata in una macchina virtuale con un'estensione già integra, questa operazione causerà il riavvio dell'estensione. Ciò può comportare l'esito negativo di tutti i processi in corso. Prima di attivare l'operazione di ripetizione della registrazione, verificare la presenza di uno o più [sintomi](backup-sql-server-azure-troubleshoot.md#re-registration-failures).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Risolvere i problemi relativi ai backup](backup-sql-server-azure-troubleshoot.md)in un database SQL Server database .
