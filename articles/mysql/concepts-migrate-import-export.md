---
title: Importare ed esportare - Database di Azure per MySQL
description: Questo articolo illustra i metodi comuni per importare ed esportare database nel database di Azure per MySQL mediante strumenti come MySQL Workbench.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 641dfa2439513138b5dd8f56843e81c31eb38609
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389775"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrare il database MySQL mediante l'importazione ed esportazione

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

In questo articolo vengono illustrati due approcci comuni per importare ed esportare i dati in un database di Azure per il server MySQL con MySQL Workbench.

Per indicazioni dettagliate e complete sulla migrazione, vedere le risorse [della guida alla migrazione](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide). 

Per altri scenari di migrazione, vedere La [Guida alla migrazione del database](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare la migrazione del database MySQL, è necessario:
- Creare un [database di Azure per il server MySQL usando il portale di Azure](quickstart-create-mysql-server-database-using-azure-portal.md).
- Scaricare e installare [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) o un altro strumento MySQL di terze parti per l'importazione e l'esportazione.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Creare un database nel database di Azure per il server MySQL
Creare un database vuoto nel database di Azure per il server MySQL usando MySQL Workbench, Toad o Navicat. Il database può avere lo stesso nome del database che contiene i dati di dump; in alternativa, è possibile creare un database con un nome diverso.

Per ottenere la connessione, eseguire le operazioni seguenti:

1. Nella finestra portale di Azure cercare le informazioni di connessione nel riquadro **Panoramica** del Database di Azure per MySQL.

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Screenshot delle informazioni di connessione al server di Database di Azure per MySQL nel portale di Azure.":::

1. Aggiungere le informazioni di connessione a MySQL Workbench.

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="Screenshot della stringa di connessione di MySQL Workbench.":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Determinare quando usare le tecniche di importazione ed esportazione

> [!TIP]
> Per gli scenari in cui si vuole eseguire il dump e il ripristino dell'intero database, usare invece l'approccio [dump e](concepts-migrate-dump-restore.md) ripristino.

Negli scenari seguenti usare gli strumenti MySQL per importare ed esportare i database nel database MySQL. Per altri strumenti, vedere la sezione "Metodi di migrazione" (pagina 22) della guida alla migrazione da MySQL a [Database di Azure.](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1.pdf) 

- Quando è necessario scegliere in modo selettivo alcune tabelle da importare da un database MySQL esistente nel database MySQL di Azure, è meglio usare la tecnica di importazione ed esportazione. In questo modo, è possibile omettere tutte le tabelle non necessarie dalla migrazione per risparmiare tempo e risorse. Ad esempio, usare `--include-tables` `--exclude-tables` l'opzione o con [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables)e `--tables` l'opzione con [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Quando si spostano oggetti di database diversi da tabelle, creare in modo esplicito tali oggetti. Includere vincoli (chiave primaria, chiave esterna e indici), viste, funzioni, procedure, trigger e qualsiasi altro oggetto di database di cui si vuole eseguire la migrazione.
- Quando si esegue la migrazione di dati da origini dati esterne diverse da un database MySQL, creare un file flat e importarli usando [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

> [!Important]
> Sia il server singolo che il server flessibile supportano solo il motore di archiviazione InnoDB. Assicurarsi che tutte le tabelle nel database usino il motore di archiviazione InnoDB quando si caricano dati nel database di Azure per MySQL.
>
> Se il database di origine usa un altro motore di archiviazione, eseguire la conversione nel motore InnoDB prima di eseguire la migrazione del database. Se ad esempio si possiede un'app Web o WordPress che usa il motore MyISAM, convertire prima le tabelle eseguendo la migrazione dei dati in tabelle InnoDB. Usare la clausola `ENGINE=INNODB` per impostare il motore per la creazione di una tabella e trasferire i dati nella tabella compatibile prima della migrazione.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Consigli relativi alle prestazioni per l'importazione ed esportazione

Per ottenere prestazioni ottimali di importazione ed esportazione dei dati, è consigliabile eseguire le operazioni seguenti:
-   Creare indici cluster e chiavi primarie prima di caricare i dati. Caricare i dati in ordine di chiave primaria.
-   Ritardare la creazione di indici secondari fino a dopo il caricamento dei dati.
-   Disabilitare i vincoli di chiave esterna prima di caricare i dati. La disabilitazione dei controlli della chiave esterna offre miglioramenti significativi delle prestazioni. Abilitare i vincoli e verificare i dati dopo il caricamento per garantire l'integrità referenziale.
-   Caricare i dati in parallelo. Evitare un eccessivo parallelismo che comporterebbe il raggiungimento del limite di risorse e monitorare le risorse con le metriche offerte nel portale di Azure.
-   Usare le tabelle partizionate quando appropriato.

## <a name="import-and-export-data-by-using-mysql-workbench"></a>Importare ed esportare dati usando MySQL Workbench
Esistono due modi per esportare e importare dati in MySQL Workbench: dal menu di scelta rapida del Visualizzatore oggetti o dal riquadro Strumento di navigazione. Ogni metodo ha uno scopo diverso.

> [!NOTE]
> Se si aggiunge una connessione al server singolo o al server flessibile MySQL (anteprima) in MySQL Workbench, eseguire le operazioni seguenti:
> - Per Il server singolo MySQL, assicurarsi che il nome utente sia nel formato *\<username@servername>* .
> - Per il server flessibile MySQL, usare *\<username>* solo . Se si usa *\<username@servername>* per connettersi, la connessione avrà esito negativo.

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>Eseguire le procedure guidate per l'esportazione e l'importazione dei dati della tabella dal menu di scelta rapida del Visualizzatore oggetti

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Screenshot dei comandi della procedura guidata di esportazione e importazione di MySQL Workbench nel menu di scelta rapida del Visualizzatore oggetti.":::

Le procedure guidate dati tabella supportano le operazioni di importazione ed esportazione tramite file CSV e JSON. Le procedure guidate includono diverse opzioni di configurazione, ad esempio separatori, selezione di colonne e selezione della codifica. È possibile eseguire ogni procedura guidata su server MySQL locali o connessi in remoto. L'operazione di importazione include tabelle, colonne e mapping dei tipi.

Per accedere a queste procedure guidate dal menu di scelta  rapida del Visualizzatore **oggetti,** fare clic con il pulsante destro del mouse su una tabella e quindi scegliere Esportazione guidata dati tabella o Importazione guidata dati tabella .

#### <a name="the-table-data-export-wizard"></a>Esportazione guidata dati tabella

Per esportare una tabella in un file CSV:

1. Fare clic con il pulsante destro del mouse sulla tabella del database da esportare.
1. Selezionare **Table Data Export Wizard** (Esportazione guidata di tabelle). Selezionare le colonne da esportare, l'offset di riga (se presente) e il conteggio (se presente).
1. Nel riquadro **Selezione dati per l'esportazione** selezionare **Avanti**. Selezionare il percorso del file, il tipo di file CSV o JSON. Selezionare anche separatore di riga, metodo di inclusione delle stringhe e separatore di campo.
1. Nel riquadro **Seleziona percorso file di output** selezionare **Avanti.**
1. Nel riquadro **Esporta dati** selezionare **Avanti.**

#### <a name="the-table-data-import-wizard"></a>Importazione guidata dati tabella

Per importare una tabella da un file CSV:

1. Fare clic con il pulsante destro del mouse sulla tabella del database da importare.
1. Cercare e selezionare il file CSV da importare e quindi selezionare **Avanti.**
1. Selezionare la tabella di destinazione (nuova o esistente), selezionare o deselezionare la casella di controllo **Tronca tabella** prima dell'importazione e quindi selezionare **Avanti.**
1. Selezionare la codifica e le colonne da importare e quindi selezionare **Avanti.**
1. Nel riquadro **Importa dati** selezionare **Avanti.** La procedura guidata importa i dati.

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Eseguire le procedure guidate per l'esportazione e l'importazione di dati SQL dal riquadro Strumento di navigazione
Usare una procedura guidata per esportare o importare dati SQL generati da MySQL Workbench o dal comando mysqldump. È possibile accedere alle procedure guidate dal **riquadro Strumento di** navigazione oppure selezionare **Server** dal menu principale.

#### <a name="export-data"></a>Esportazione dei dati

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Screenshot dell'uso del riquadro Strumento di navigazione per visualizzare il riquadro Esportazione dati in MySQL Workbench.":::

È possibile usare il **riquadro Esportazione** dati per esportare i dati mySQL.

1. Nel riquadro Strumento di navigazione  di MySQL Workbench selezionare **Esportazione dati**.

1. Nel riquadro **Esportazione dati** selezionare ogni schema da esportare.
 
   Per ogni schema è possibile selezionare oggetti o tabelle dello schema specifici da esportare. Le opzioni di configurazione includono l'esportazione in una cartella di progetto o in un file SQL autonomo, il dump di stored procedure ed eventi o l'ignorare i dati della tabella.

   In alternativa, usare **Export a Result Set** (Esporta un set di risultati) per esportare uno specifico set di risultati nell'editor SQL in un altro formato, come ad esempio CSV, JSON, HTML e XML.

1. Selezionare gli oggetti del database da esportare e configurare le opzioni correlate.
1. Selezionare **Aggiorna** per caricare gli oggetti correnti.
1. Facoltativamente, selezionare **Opzioni avanzate** in alto a destra per perfezionare l'operazione di esportazione. Ad esempio, aggiungere blocchi di tabella, usare invece di istruzioni e identificatori tra `replace` `insert` virgolette con caratteri backtick.
1. Selezionare **Avvia esportazione** per avviare il processo di esportazione.


#### <a name="import-data"></a>Importa dati

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Screenshot dell'uso del riquadro Strumento di navigazione per visualizzare il riquadro Importazione dati in MySQL Workbench.":::

È possibile usare il **riquadro Importazione** dati per importare o ripristinare i dati esportati dall'operazione di esportazione dei dati o dal comando mysqldump.

1. Nel riquadro Strumento di navigazione  di MySQL Workbench selezionare **Esportazione/ripristino dati**.
1. Selezionare la cartella del progetto o il file SQL autonomo, selezionare lo schema in cui importare o selezionare il **pulsante** Nuovo per definire un nuovo schema.
1. Selezionare **Avvia importazione** per avviare il processo di importazione.

## <a name="next-steps"></a>Passaggi successivi
- Per un altro approccio alla migrazione, vedere Eseguire la migrazione del database MySQL a un database di [Azure per MySQL usando dump e ripristino.](concepts-migrate-dump-restore.md)
- Per altre informazioni sulla migrazione di database a un database di Azure per MySQL, vedere la Guida alla [migrazione del database](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
