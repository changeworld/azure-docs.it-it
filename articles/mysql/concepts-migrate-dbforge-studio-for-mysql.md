---
title: Connettersi a database di Azure per MySQL usando dbForge Studio per MySQL
description: Questo articolo illustra come connettersi al server di database di Azure per MySQL tramite dbForge Studio per MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 1c85a07a3d61c80f3871f04c399263a8e210254e
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107010792"
---
# <a name="connect-to-azure-database-for-mysql-using-dbforge-studio-for-mysql"></a>Connettersi a database di Azure per MySQL usando dbForge Studio per MySQL

Per connettersi a database di Azure per MySQL usando [DbForge Studio per MySQL](https://www.devart.com/dbforge/mysql/studio/):

1. Scegliere nuova connessione dal menu database.

2. Specificare un nome host e le credenziali di accesso.

3. Selezionare il pulsante Test connessione per verificare la configurazione.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Connessione ad Azure":::

## <a name="migrate-a-database-using-the-backup-and-restore-functionality"></a>Eseguire la migrazione di un database utilizzando la funzionalità di backup e ripristino

Lo Studio consente di migrare i database in Azure in molti modi, la scelta che dipende esclusivamente dalle proprie esigenze. Se è necessario spostare l'intero database, è preferibile utilizzare la funzionalità di backup e ripristino. In questo esempio viene eseguita la migrazione del database *Nicola* che risiede nel server MySQL in database di Azure per MySQL. La logica alla base del processo di migrazione con la funzionalità di backup e ripristino di dbForge Studio per MySQL consiste nel creare un backup del database MySQL e quindi ripristinarlo nel database di Azure per MySQL.

### <a name="back-up-the-database"></a>Eseguire il backup del database

1. Scegliere backup e ripristino dal menu database, quindi fare clic su backup database. Verrà visualizzata la procedura guidata per il backup del database.

2. Nella scheda contenuto di backup di backup guidato database selezionare gli oggetti di database di cui si desidera eseguire il backup.

3. Nella scheda opzioni configurare il processo di backup in base ai requisiti.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="Opzioni di backup guidato":::

4. Specificare quindi il comportamento di elaborazione degli errori e le opzioni di registrazione.

5. Selezionare backup.

### <a name="restore-the-database"></a>Ripristinare il database

1. Connettersi ad Azure per database per MySQL come descritto in precedenza.

2. Fare clic con il pulsante destro del mouse sul corpo del Esplora database, scegliere backup e ripristino, quindi selezionare Ripristina database.

3. Nella procedura guidata di ripristino del database che viene visualizzata selezionare un file con un backup del database.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="Passaggio di ripristino":::

4. Selezionare Ripristina.

5. Verificare il risultato.

## <a name="migrate-a-database-using-the-copy-databases-functionality"></a>Eseguire la migrazione di un database utilizzando la funzionalità copia database

La funzionalità copia database è simile al backup e al ripristino, ad eccezione del fatto che non sono necessari due passaggi per eseguire la migrazione di un database. La funzionalità consente di trasferire due o più database in una sola volta. La funzionalità copia database è disponibile solo nell'edizione Enterprise di dbForge Studio per MySQL.
In questo esempio viene eseguita la migrazione del database di *world_x* dal server MySQL al database di Azure per MySQL.
Per eseguire la migrazione di un database utilizzando la funzionalità copia database:

1. Scegliere Copia database dal menu database. 

2. Nella scheda Copia database visualizzata specificare la connessione di origine e di destinazione e selezionare i database da migrare. Si immette la connessione MySQL di Azure e si seleziona il database *world_x* . Selezionare la freccia verde per avviare il processo.

3. Verificare il risultato.

In seguito alle attività di migrazione del database, il database *world_x* è stato correttamente visualizzato in Azure MySQL.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="Risultato copia database":::

## <a name="migrate-a-database-using-schema-and-data-compare-tools"></a>Eseguire la migrazione di un database usando gli strumenti schema e confronto dati

dbForge Studio per MySQL include alcuni strumenti che consentono di migrare i database MySQL, i dati e\o degli schemi MySQL in Azure. La scelta della funzionalità dipende dalle esigenze e dai requisiti del progetto. Se è necessario spostare un database in modo selettivo, ovvero eseguire la migrazione di alcune tabelle MySQL in Azure, è preferibile usare la funzionalità schema e confronto dati.
In questo esempio viene eseguita la migrazione del database *globale* che risiede nel server MySQL in database di Azure per MySQL. La logica alla base del processo di migrazione con lo schema e la funzionalità di confronto dati di dbForge Studio per MySQL consiste nel creare un database vuoto nel database di Azure per MySQL, sincronizzarlo con il database MySQL necessario prima usando lo strumento confronto schema e quindi usando lo strumento di confronto dei dati. In questo modo, gli schemi e i dati MySQL vengono spostati in modo accurato in Azure.

### <a name="step-1-connect-to-azure-database-for-mysql-and-create-an-empty-database"></a>Passaggio 1. Connettersi a database di Azure per MySQL e creare un database vuoto

### <a name="step-2-schema-synchronization"></a>Passaggio 2: Sincronizzazione dello schema

1. Scegliere nuovo confronto schema dal menu di confronto.
Verrà visualizzata la procedura guidata nuovo confronto schema.

2. Selezionare l'origine e la destinazione, quindi specificare le opzioni di confronto dello schema. Selezionare Confronta.

3. Nella griglia risultati confronto visualizzata selezionare oggetti per la sincronizzazione. Selezionare il pulsante freccia verde per aprire la sincronizzazione guidata schema.

4. Procedura dettagliata per la configurazione della sincronizzazione. Selezionare Sincronizza per distribuire le modifiche.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="Sincronizzazione guidata schema":::

### <a name="data-comparison"></a>Confronto dei dati

1. Scegliere nuovo confronto dati dal menu di confronto. Verrà visualizzata la procedura guidata nuovo confronto dati.

2. Selezionare l'origine e la destinazione, quindi specificare le opzioni di confronto dei dati e modificare i mapping, se necessario. Selezionare Confronta.

3. Nella griglia risultati confronto visualizzata selezionare oggetti per la sincronizzazione. Per aprire la sincronizzazione guidata dati, selezionare il pulsante freccia verde.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="Risultato della conformità dei dati":::

4. Procedura dettagliata per la configurazione della sincronizzazione. Selezionare Sincronizza per distribuire le modifiche.

5. Verificare il risultato.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="Risultato sincronizzazione dati":::

## <a name="summary"></a>Riepilogo

Attualmente, più aziende spostano i database in database di Azure per MySQL, perché questo servizio di database è facile da configurare, gestire e ridimensionare. Non è necessario che la migrazione sia dolorosa. dbForge Studio per MySQL offre strumenti di migrazione Immacolata che possono semplificare notevolmente il processo. Lo Studio consente di configurare, salvare, modificare, automatizzare e pianificare facilmente il trasferimento del database.

## <a name="next-steps"></a>Passaggi successivi
- [Panoramica di MySQL](overview.md)
