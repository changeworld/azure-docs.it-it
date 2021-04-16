---
title: Eseguire il backup di Exchange Server con server di Backup di Azure
description: Informazioni su come eseguire il backup di un server di Exchange in Backup di Azure con il server di Backup di Azure
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: f3a7fae5a1f5ec933c015546ddf2bdb2898e3904
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515499"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Eseguire il backup di un server Exchange in Azure con il server di Backup di Azure

Questo articolo descrive come configurare il server di Backup di Microsoft Azure (MABS) per eseguire il backup di un server Microsoft Exchange in Azure.  

## <a name="prerequisites"></a>Prerequisiti

Prima di continuare, assicurarsi che il Server di Backup di Azure sia [installato e pronto](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>Agente protezione MABS

Per installare l'agente protezione MABS nel server di Exchange, seguire questi passaggi:

1. Assicurarsi che i firewall siano configurati correttamente. Vedere [Configurare le eccezioni del firewall per l'agente](/system-center/dpm/configure-firewall-settings-for-dpm).
2. Installare l'agente nel server Exchange selezionando **Management > Agents > Install** in MABS Console di amministrazione. Per la procedura dettagliata, vedere [Installare l'agente protezione MABS](/system-center/dpm/deploy-dpm-protection-agent) .

## <a name="create-a-protection-group-for-the-exchange-server"></a>Creare un gruppo di protezione per il server di Exchange

1. Nella finestra di dialogo Console di amministrazione mabs selezionare  **Protezione** e quindi selezionare Nuovo sulla barra multifunzione per aprire la procedura guidata Crea **nuovo gruppo protezione** dati.
2. Nella schermata **iniziale** della procedura guidata selezionare **Avanti.**
3. Nella schermata **Selezione tipo di gruppo protezione** dati selezionare Server **e** quindi **Avanti.**
4. Selezionare il database del server Exchange da proteggere e selezionare **Avanti.**

   > [!NOTE]
   > Se si vuole proteggere Exchange 2013, controllare i [Prerequisiti di Exchange 2013](/system-center/dpm/back-up-exchange).
   >
   >

    Nell'esempio seguente è selezionato il database di Exchange 2010.

    ![Seleziona membri del gruppo](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Selezionare il metodo di protezione dati.

    Assegnare un nome al gruppo protezione dati e quindi selezionare entrambe le opzioni seguenti:

   * Protezione dati breve termine tramite: Disco.
   * Protezione dati online.
6. Selezionare **Avanti**.
7. Selezionare l'opzione **Esegui Eseutil per controllare l'integrità dei dati** se si vuole controllare l'integrità dei database di Exchange Server.

    Dopo aver selezionato questa opzione, la verifica della coerenza dei backup verrà eseguita in MABS per evitare il traffico di I/O generato eseguendo il comando **eseutil** nel server Exchange.

   > [!NOTE]
   > Per usare questa opzione, è necessario copiare i file Ese.dll e Eseutil.exe nella directory C:\Programmi\Backup di Microsoft Azure\DPM\DPM\bin nel server maBS. In caso contrario, viene generato l'errore seguente:   
   > ![Errore di Eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Selezionare **Avanti**.
9. Selezionare il database per **Copia backup** e quindi selezionare **Avanti.**

   > [!NOTE]
   > Se non si seleziona "Backup completo" per almeno una copia DAG di un database, i log non verranno troncati.
   >
   >
10. Configurare gli obiettivi per **il backup a breve** termine e quindi selezionare **Avanti.**
11. Esaminare lo spazio disponibile su disco e quindi selezionare **Avanti.**
12. Selezionare l'ora in cui il server mabs creerà la replica iniziale e quindi selezionare **Avanti.**
13. Selezionare le opzioni di verifica coerenza e quindi selezionare **Avanti.**
14. Scegliere il database di cui si vuole eseguire il backup in Azure e quindi selezionare **Avanti.** Ad esempio:

    ![Specifica i dati da proteggere online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definire la pianificazione per **Backup di Azure** e quindi selezionare **Avanti.** Ad esempio:

    ![Specificare la pianificazione dei backup online](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Tenere presente che i punti di ripristino online sono basati sui punti di ripristino di backup completo rapido. Pertanto, è necessario pianificare il punto di ripristino online dopo l'ora specificata per il punto di ripristino completo rapido.
    >
    >
16. Configurare i criteri di conservazione **per Backup di Azure** e quindi selezionare **Avanti.**
17. Scegliere un'opzione di replica online e selezionare **Avanti.**

    Un database di grandi dimensioni potrebbe richiedere molto tempo per creare il backup iniziale in rete. Per evitare questo problema, è possibile creare un backup offline.  

    ![Specificare i criteri di mantenimento online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Confermare le impostazioni e quindi selezionare **Crea gruppo.**
19. Selezionare **Chiudi**.

## <a name="recover-the-exchange-database"></a>Ripristinare il database di Exchange

1. Per ripristinare un database di Exchange, selezionare **Ripristino** nel database MABS Console di amministrazione.
2. Individuare il database di Exchange che si vuole ripristinare.
3. Selezionare un punto di ripristino online dall'elenco a discesa *Ora ripristino* .
4. Selezionare **Ripristina** per avviare il **Ripristino guidato.**

Per i punti di ripristino online sono disponibili cinque tipi:

* **Ripristina nel percorso originale di Exchange Server:** i dati verranno ripristinati nel server di Exchange originale.
* **Ripristina in un altro database in un Server di Exchange:** i dati verranno ripristinati in un altro database in un altro server di Exchange.
* **Ripristina in un database di ripristino:** i dati verranno ripristinati in un database di ripristino di Exchange (RDB).
* **Copia in una cartella di rete:** i dati verranno ripristinati in una cartella di rete.
* **Copia su nastro:** se si ha una libreria di nastri o un'unità nastro autonoma collegata e configurata in MABS, il punto di recupero verrà copiato su un nastro disponibile.

    ![Scegliere la replica online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Passaggi successivi

* [Backup di Azure - Domande frequenti](backup-azure-backup-faq.yml)
