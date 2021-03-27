---
title: Importazione ed esportazione-database di Azure per MySQL
description: Questo articolo illustra i metodi comuni per importare ed esportare database nel database di Azure per MySQL mediante strumenti come MySQL Workbench.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: 049a0ad45ea82210d8fac28db0fb3d067841bba4
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625144"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrare il database MySQL mediante l'importazione ed esportazione

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

In questo articolo vengono illustrati due approcci comuni per importare ed esportare i dati in un database di Azure per il server MySQL con MySQL Workbench.

Per indicazioni dettagliate e complete sulla migrazione, vedere la [Guida alla migrazione risorse](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide). 

Per altri scenari di migrazione, vedere la [Guida alla migrazione del database](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare la migrazione del database MySQL, è necessario:
- Creare un [database di Azure per il server MySQL usando il portale di Azure](quickstart-create-mysql-server-database-using-azure-portal.md).
- Scaricare e installare [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) o un altro strumento MySQL di terze parti per l'importazione e l'esportazione.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Creare un database nel database di Azure per il server MySQL
Creare un database vuoto nel database di Azure per il server MySQL tramite MySQL Workbench, Toad o Navicat. Il database può avere lo stesso nome del database che contiene i dati di dump; in alternativa, è possibile creare un database con un nome diverso.

Per connettersi, eseguire le operazioni seguenti:

1. Nella portale di Azure cercare le informazioni di connessione nel riquadro **Panoramica** del database di Azure per MySQL.

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Screenshot delle informazioni di connessione del server di database di Azure per MySQL nel portale di Azure.":::

1. Aggiungere le informazioni di connessione a MySQL Workbench.

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="Stringa di connessione MySQL Workbench":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Determinare quando utilizzare le tecniche di importazione ed esportazione

> [!TIP]
> Per gli scenari in cui si desidera eseguire il dump e il ripristino dell'intero database, utilizzare invece l'approccio [dump e Restore](concepts-migrate-dump-restore.md) .

Negli scenari seguenti usare gli strumenti di MySQL per importare ed esportare database nel database MySQL. Per altri strumenti, vedere la sezione "metodi di migrazione" (pagina 22) della [Guida alla migrazione del database da MySQL ad Azure](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1.pdf). 

- Quando è necessario scegliere in modo selettivo alcune tabelle da importare da un database MySQL esistente nel database MySQL di Azure, è preferibile usare la tecnica di importazione ed esportazione.  In questo modo, è possibile omettere tutte le tabelle non necessarie dalla migrazione per risparmiare tempo e risorse. Ad esempio, usare l'istruzione `--include-tables` o `--exclude-tables` con [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) e l'istruzione `--tables` con [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Quando si stanno comportando oggetti di database diversi dalle tabelle, creare in modo esplicito tali oggetti. Includere vincoli (chiave primaria, chiave esterna e indici), viste, funzioni, procedure, trigger e altri oggetti di database di cui si desidera eseguire la migrazione.
- Quando si esegue la migrazione di dati da origini dati esterne diverse da un database MySQL, creare un file flat e importarli usando [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

> [!Important]
> Sia il server singolo che il server flessibile supportano *solo il motore di archiviazione InnoDB*. Assicurarsi che tutte le tabelle nel database usino il motore di archiviazione InnoDB quando si caricano dati nel database di Azure per MySQL.
>
> Se il database di origine usa un altro motore di archiviazione, eseguire la conversione nel motore InnoDB prima di eseguire la migrazione del database. Se ad esempio si possiede un'app Web o WordPress che usa il motore MyISAM, convertire prima le tabelle eseguendo la migrazione dei dati in tabelle InnoDB. Usare la clausola `ENGINE=INNODB` per impostare il motore per la creazione di una tabella e trasferire i dati nella tabella compatibile prima della migrazione.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Consigli relativi alle prestazioni per l'importazione ed esportazione

Per ottimizzare le prestazioni di importazione ed esportazione dei dati, è consigliabile eseguire le operazioni seguenti:
-   Creare indici cluster e chiavi primarie prima di caricare i dati. Caricare i dati in ordine di chiave primaria.
-   Ritardare la creazione di indici secondari fino al caricamento dei dati.
-   Disabilitare i vincoli di chiave esterna prima di caricare i dati. La disabilitazione dei controlli della chiave esterna offre miglioramenti significativi delle prestazioni. Abilitare i vincoli e verificare i dati dopo il caricamento per garantire l'integrità referenziale.
-   Caricare i dati in parallelo. Evitare un eccessivo parallelismo che comporterebbe il raggiungimento del limite di risorse e monitorare le risorse con le metriche offerte nel portale di Azure.
-   Usare le tabelle partizionate quando appropriato.

## <a name="import-and-export-data-by-using-mysql-workbench"></a>Importare ed esportare dati con MySQL Workbench
Esistono due modi per esportare e importare dati in MySQL Workbench: dal menu di scelta rapida Visualizzatore oggetti o dal riquadro strumento di navigazione. Ogni metodo svolge uno scopo diverso.

> [!NOTE]
> Se si sta aggiungendo una connessione a un server MySQL singolo o a un server flessibile (anteprima) in MySQL Workbench, seguire questa procedura:
> - Per MySQL Single Server, verificare che il nome utente sia nel formato *\<username@servername>* .
> - Per MySQL flexible server usare *\<username>* solo. Se si utilizza *\<username@servername>* per la connessione, la connessione avrà esito negativo.

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>Eseguire le procedure guidate di esportazione e importazione dei dati della tabella dal menu di scelta rapida Visualizzatore oggetti

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Screenshot dei comandi di esportazione e importazione guidata di MySQL Workbench nel menu di scelta rapida del Visualizzatore oggetti.":::

Le procedure guidate per i dati delle tabelle supportano le operazioni di importazione ed esportazione tramite file CSV e JSON. Le procedure guidate includono diverse opzioni di configurazione, ad esempio separatori, selezione colonne e selezione codifica. È possibile eseguire ogni procedura guidata su server MySQL locali o connessi in remoto. L'operazione di importazione include tabelle, colonne e mapping dei tipi.

Per accedere a queste procedure guidate dal menu di scelta rapida Visualizzatore oggetti, fare clic con il pulsante destro del mouse su una tabella, quindi scegliere **esportazione guidata dati tabella** o **importazione guidata dati tabella**.

#### <a name="the-table-data-export-wizard"></a>Esportazione guidata dati tabella

Per esportare una tabella in un file CSV:

1. Fare clic con il pulsante destro del mouse sulla tabella del database da esportare.
1. Selezionare **Table Data Export Wizard** (Esportazione guidata di tabelle). Selezionare le colonne da esportare, l'offset di riga (se presente) e il conteggio (se presente).
1. Nel riquadro **selezionare i dati da esportare** fare clic su **Avanti**. Selezionare il percorso del file, il tipo di file CSV o JSON. Selezionare anche separatore di riga, metodo di inclusione delle stringhe e separatore di campo.
1. Nel riquadro **selezionare il percorso del file di output** selezionare **Avanti**.
1. Nel riquadro **Esporta dati** selezionare **Avanti**.

#### <a name="the-table-data-import-wizard"></a>Importazione guidata dati tabella

Per importare una tabella da un file CSV:

1. Fare clic con il pulsante destro del mouse sulla tabella del database da importare.
1. Cercare e selezionare il file CSV da importare, quindi fare clic su **Avanti**.
1. Selezionare la tabella di destinazione (nuova o esistente), selezionare o deselezionare la casella di controllo **Tronca tabella prima dell'importazione** , quindi fare clic su **Avanti**.
1. Selezionare la codifica e le colonne da importare, quindi fare clic su **Avanti**.
1. Nel riquadro **Importa dati** selezionare **Avanti**. I dati vengono importati dalla procedura guidata.

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Eseguire le procedure guidate per l'esportazione e l'importazione di dati SQL dal riquadro strumento di navigazione
Usare una procedura guidata per esportare o importare dati SQL generati da MySQL Workbench o dal comando mysqldump. È possibile accedere alle procedure guidate dal riquadro **strumento di navigazione** oppure selezionare **Server** dal menu principale.

#### <a name="export-data"></a>Esportazione dei dati

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Screenshot dell'uso del riquadro strumento di navigazione per visualizzare il riquadro di esportazione dei dati in MySQL Workbench.":::

È possibile usare il riquadro **dati Export** per esportare i dati di MySQL.

1. Nel riquadro **strumento di navigazione** di MySQL Workbench selezionare **esportazione dati**.

1. Nel riquadro **Esporta dati** selezionare ogni schema che si desidera esportare.
 
   Per ogni schema, è possibile selezionare oggetti dello schema o tabelle specifiche da esportare. Le opzioni di configurazione includono l'esportazione in una cartella di progetto o un file SQL autonomo, il dump di routine e eventi archiviati o l'omissione dei dati della tabella.

   In alternativa, usare **Export a Result Set** (Esporta un set di risultati) per esportare uno specifico set di risultati nell'editor SQL in un altro formato, come ad esempio CSV, JSON, HTML e XML.

1. Selezionare gli oggetti del database da esportare e configurare le opzioni correlate.
1. Selezionare **Aggiorna** per caricare gli oggetti correnti.
1. Facoltativamente, selezionare **Opzioni avanzate** in alto a destra per ridefinire l'operazione di esportazione. Ad esempio, aggiungere blocchi di tabella, usare istruzioni "replace" anziché "insert" e segnalare gli identificatori con l'apice inverso.
1. Selezionare **Avvia esportazione** per avviare il processo di esportazione.


#### <a name="import-data"></a>Importa dati

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Screenshot dell'uso del riquadro strumento di navigazione per visualizzare il riquadro importazione dati in MySQL Workbench.":::

È possibile usare il riquadro **importazione dati** per importare o ripristinare i dati esportati dall'operazione di esportazione dei dati o dal comando mysqldump.

1. Nel riquadro **strumento di navigazione** di MySQL Workbench selezionare **esportazione/ripristino dati**.
1. Selezionare la cartella del progetto o il file SQL autonomo, selezionare lo schema in cui eseguire l'importazione oppure selezionare il pulsante **nuovo** per definire un nuovo schema.
1. Selezionare **Avvia importazione** per avviare il processo di importazione.

## <a name="next-steps"></a>Passaggi successivi
- Per un altro approccio alla migrazione, vedere [eseguire la migrazione del database MySQL a un database di Azure per MySQL usando dump e ripristino](concepts-migrate-dump-restore.md).
- Per ulteriori informazioni sulla migrazione di database a un database di Azure per MySQL, vedere la [Guida alla migrazione del database](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
