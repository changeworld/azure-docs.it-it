---
title: Connettersi a Database di Azure per MySQL usando dbForge Studio per MySQL
description: L'articolo illustra come connettersi al server di Database di Azure per MySQL tramite dbForge Studio per MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 942651aadf4113c1aca32e4e1d2c558b0d764421
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377226"
---
# <a name="connect-to-azure-database-for-mysql-using-dbforge-studio-for-mysql"></a>Connettersi a Database di Azure per MySQL usando dbForge Studio per MySQL

Per connettersi a Database di Azure per MySQL [usando dbForge Studio per MySQL](https://www.devart.com/dbforge/mysql/studio/):

1. Scegliere Nuova connessione dal menu Database.

2. Specificare un nome host e le credenziali di accesso.

3. Selezionare il pulsante Test connessione per controllare la configurazione.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Connessione ad Azure":::

## <a name="migrate-a-database-using-the-backup-and-restore-functionality"></a>Eseguire la migrazione di un database usando la funzionalità Backup e ripristino

Studio consente di eseguire la migrazione dei database in Azure in molti modi, la cui scelta dipende esclusivamente dalle esigenze. Se è necessario spostare l'intero database, è meglio usare la funzionalità Backup e ripristino. In questo esempio viene eseguita la migrazione del database *sakila* che si trova nel server MySQL a Database di Azure per MySQL. La logica alla base del processo di migrazione tramite la funzionalità Backup e ripristino di dbForge Studio per MySQL è creare un backup del database MySQL e quindi ripristinarlo in Database di Azure per MySQL.

### <a name="back-up-the-database"></a>Eseguire il backup del database

1. Scegliere Backup e ripristino dal menu Database e quindi selezionare Backup database. Verrà visualizzata la Procedura guidata Backup del database.

2. Nella scheda Contenuto backup del Backup guidato database selezionare gli oggetti di database di cui si vuole eseguire il backup.

3. Nella scheda Opzioni configurare il processo di backup in base alle esigenze.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="Opzioni della procedura guidata di backup":::

4. Specificare quindi il comportamento di elaborazione degli errori e le opzioni di registrazione.

5. Selezionare Backup.

### <a name="restore-the-database"></a>Ripristinare il database

1. Connettersi ad Azure per Database per MySQL come descritto in precedenza.

2. Fare clic con il pulsante destro Esplora database corpo del database, scegliere Backup e ripristino, quindi selezionare Ripristina database.

3. Nella procedura guidata ripristino database visualizzata selezionare un file con un backup del database.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="Passaggio di ripristino":::

4. Selezionare Ripristina.

5. Controllare il risultato.

## <a name="migrate-a-database-using-the-copy-databases-functionality"></a>Eseguire la migrazione di un database usando la funzionalità Copia database

La funzionalità Copia database è simile a Backup e ripristino, ad eccezione del fatto che non sono necessari due passaggi per eseguire la migrazione di un database. Inoltre, la funzionalità consente il trasferimento di due o più database in una sola volta. La funzionalità Copia database è disponibile solo nell'edizione Enterprise di dbForge Studio per MySQL.
In questo esempio viene eseguita la migrazione del database *world_x* dal server MySQL al Database di Azure per MySQL.
Per eseguire la migrazione di un database tramite la funzionalità Copia database:

1. Scegliere Copia database dal menu Database. 

2. Nella scheda Copia database visualizzata specificare la connessione di origine e di destinazione e selezionare i database di cui eseguire la migrazione. Si immette la connessione MySQL di Azure e si seleziona *world_x* database. Selezionare la freccia verde per avviare il processo.

3. Controllare il risultato.

In seguito alle attività di migrazione del database, il database *world_x* è stato correttamente in Azure MySQL.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="Risultato della copia dei database":::

## <a name="migrate-a-database-using-schema-and-data-compare-tools"></a>Eseguire la migrazione di un database usando gli strumenti Di schema e Confronto dati

dbForge Studio per MySQL incorpora alcuni strumenti che consentono la migrazione di database MySQL, schemi MySQL e/o dati in Azure. La scelta delle funzionalità dipende dalle esigenze e dai requisiti del progetto. Se è necessario spostare in modo selettivo un database, ad esempio eseguire la migrazione di alcune tabelle MySQL ad Azure, è meglio usare la funzionalità Schema e Confronto dati.
In questo esempio viene eseguita la migrazione del database *globale* che risiede nel server MySQL a Database di Azure per MySQL. La logica alla base del processo di migrazione tramite la funzionalità Schema e Confronto dati di dbForge Studio per MySQL è creare un database vuoto in Database di Azure per MySQL, sincronizzarlo con il database MySQL necessario prima usando lo strumento confronto schemi e quindi lo strumento Confronto dati. In questo modo i dati e gli schemi MySQL vengono spostati in Modo accurato in Azure.

### <a name="step-1-connect-to-azure-database-for-mysql-and-create-an-empty-database"></a>Passaggio 1. Connettersi a Database di Azure per MySQL e creare un database vuoto

### <a name="step-2-schema-synchronization"></a>Passaggio 2: Sincronizzazione dello schema

1. Scegliere Nuovo confronto schema dal menu Confronto.
Verrà visualizzata la Creazione guidata nuovo confronto schema.

2. Selezionare Origine e Destinazione, quindi specificare le opzioni di confronto dello schema. Selezionare Confronta.

3. Nella griglia dei risultati del confronto visualizzata selezionare gli oggetti per la sincronizzazione. Selezionare il pulsante freccia verde per aprire la sincronizzazione guidata schema.

4. Seguire i passaggi della procedura guidata per la configurazione della sincronizzazione. Selezionare Sincronizza per distribuire le modifiche.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="Sincronizzazione guidata schema":::

### <a name="step-3-data-comparison"></a>Passaggio 3. Confronto dei dati

1. Scegliere Nuovo confronto dati dal menu Confronto. Verrà visualizzata la Procedura guidata Nuovo confronto dati.

2. Selezionare Origine e Destinazione, quindi specificare le opzioni di confronto dei dati e modificare i mapping, se necessario. Selezionare Confronta.

3. Nella griglia dei risultati del confronto visualizzata selezionare gli oggetti per la sincronizzazione. Selezionare il pulsante freccia verde per aprire la sincronizzazione guidata dati.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="Risultato della compilazione dei dati":::

4. Seguire i passaggi della procedura guidata per la configurazione della sincronizzazione. Selezionare Sincronizza per distribuire le modifiche.

5. Controllare il risultato.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="Risultato della sincronizzazione dei dati":::

## <a name="summary"></a>Riepilogo

Oggi più aziende spostano i database in Database di Azure per MySQL, perché questo servizio di database è facile da configurare, gestire e ridimensionare. Non è necessario che la migrazione sia difficile. dbForge Studio per MySQL offre strumenti di migrazione immacolati che possono semplificare in modo significativo il processo. Studio consente di configurare, salvare, modificare, automatizzare e programmare facilmente il trasferimento di database.

## <a name="next-steps"></a>Passaggi successivi
- [Panoramica di MySQL](overview.md)
