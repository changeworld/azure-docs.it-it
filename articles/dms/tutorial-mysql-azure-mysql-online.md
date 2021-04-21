---
title: 'Esercitazione: Eseguire la migrazione online di MySQL a Database di Azure per MySQL'
titleSuffix: Azure Database Migration Service
description: Informazioni su come eseguire la migrazione online da MySQL locale in Database di Azure per MySQL con Servizio Migrazione del database di Azure.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 754d8cc9e79bc100e87f56c6fc33102963e53e8d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818073"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Esercitazione: Eseguire la migrazione di MySQL in Database di Azure per MySQL online mediante il Servizio Migrazione del database

È possibile usare Servizio Migrazione del database di Azure per eseguire la migrazione dei database da un'istanza di MySQL locale a [Database di Azure per MySQL](../mysql/index.yml) con tempi di inattività minimi. In altre parole, la migrazione può essere eseguita con tempi di inattività minimi per l'applicazione. In questa esercitazione si esegue la migrazione del database di esempio **Employees** da un'istanza locale di MySQL 5.7 a Database di Azure per MySQL usando un'attività di migrazione online in Servizio Migrazione del database di Azure.

> [!IMPORTANT]
> Lo scenario di migrazione online "Da MySQL a Database di Azure per MySQL" viene sostituito con uno scenario di migrazione offline parallelizzato a prestazioni molto performanti il 1° giugno 2021. Per le migrazioni online, è possibile usare questa nuova offerta insieme alla [replica dei dati in ingresso.](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) In alternativa, usare strumenti open source come [MyDumper/MyLoader](https://centminmod.com/mydumper.html) con la replica dei dati in ingresso per le migrazioni online. 

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
>
> * Eseguire la migrazione dello schema di esempio con l'utilità mysqldump.
> * Creare un'istanza del servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione tramite il Servizio Migrazione del database di Azure.
> * Eseguire la migrazione.
> * Monitorare la migrazione.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium.

> [!IMPORTANT]
> Per un'esperienza di migrazione ottimale, Microsoft consiglia di creare un'istanza del Servizio Migrazione del database di Azure nella stessa area di Azure del database di destinazione. Lo spostamento dei dati tra regioni o aree geografiche può rallentare il processo di migrazione e causare errori.

> [!NOTE]
> Comunicazione senza distorsione
>
> Microsoft supporta un ambiente diversificato ed inclusivo. Questo articolo contiene riferimenti alla parola _slave_. La [guida di stile Microsoft per la comunicazione senza pregiudizi](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) la riconosce come parola di esclusione. La parola viene usata in questo articolo per coerenza perché è attualmente la parola usata nel software. Quando il software verrà aggiornato per rimuovere la parola, questo articolo verrà aggiornato di conseguenza.
>


## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

* Scaricare e installare l'[Edizione MySQL Community](https://dev.mysql.com/downloads/mysql/) 5.6 o 5.7. La versione di MySQL locale deve corrispondere alla versione di Database di Azure per MySQL. Ad esempio, per MySQL 5.6 è possibile solo eseguire la migrazione a Database di Azure per MySQL 5.6 e non l'aggiornamento a 5.7. Le migrazioni a o da MySQL 8.0 non sono supportate.
* [Creare un'istanza in Database di Azure per MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Per informazioni dettagliate su come connettersi e creare un database usando l'applicazione Workbench, vedere l'articolo Usare [MySQL Workbench](../mysql/connect-workbench.md) per connettersi ed eseguire query sui dati.  
* Creare una rete virtuale di Microsoft Azure per il Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che offre la connettività da sito a sito per i server di origine locali con [ExpressRoute](../expressroute/expressroute-introduction.md) o [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Per altre informazioni sulla creazione di una rete virtuale, vedere la [documentazione sulla rete virtuale](../virtual-network/index.yml) e in particolare gli articoli di avvio rapido con istruzioni dettagliate.

    > [!NOTE]
    > Durante la configurazione della rete virtuale, se si usa ExpressRoute con il peering di rete a Microsoft, aggiungere gli [endpoint](../virtual-network/virtual-network-service-endpoints-overview.md) servizio seguenti alla subnet in cui verrà effettuato il provisioning del servizio:
    >
    > * Endpoint del database di destinazione (ad esempio endpoint SQL, endpoint Cosmos DB e così via)
    > * Endpoint di archiviazione
    > * Endpoint bus di servizio
    >
    > Questa configurazione è necessaria perché il Servizio Migrazione del database di Azure non ha connettività Internet.

* Assicurarsi che le regole del gruppo di sicurezza di rete della rete virtuale non blocchino la porta in uscita 443 di ServiceTag per ServiceBus, Storage e AzureMonitor. Per informazioni dettagliate sul filtro del traffico dei gruppi di sicurezza di rete della rete virtuale, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Configurare [Windows Firewall per l'accesso al motore di database](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Aprire Windows Firewall per consentire a Servizio Migrazione del database di Azure di accedere al server MySQL di origine (per impostazione predefinita attraverso la porta TCP 3306).
* Quando si usa un'appliance firewall all'ingresso dei database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.
* Creare una [regola del firewall](../azure-sql/database/firewall-configure.md) a livello di server per Database di Azure per MySQL per consentire a Servizio Migrazione del database di Azure di accedere ai database di destinazione. Specificare l'intervallo di subnet della rete virtuale usato per il servizio Migrazione del database di Azure.
* L'origine MySQL deve essere in un'Edizione MySQL Community supportata. Per determinare la versione dell'istanza di MySQL, eseguire il comando seguente nell'utilità MySQL o in MySQL Workbench:

    ```
    SELECT @@version;
    ```

* Database di Azure per MySQL supporta solo le tabelle InnoDB. Per convertire le tabelle MyISAM in InnoDB, vedere l'articolo [Converting Tables from MyISAM to InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) (Conversione di tabelle da MyISAM a InnoDB).

* Abilitare la registrazione binaria nel file my.ini (Windows) o my.cnf (Unix) nel database di origine usando la configurazione seguente:

  * **server_id** = 1 o superiore (rilevante solo per MySQL 5.6)
  * **log-bin** =\<path> (rilevante solo per MySQL 5.6) Ad esempio: log-bin = E:\MySQL_logs\BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (è consigliabile non usare zero, che è pertinente solo per MySQL 5.6)
  * **Binlog_row_image** = full (rilevante solo per MySQL 5.6)
  * **log_slave_updates** = 1

* L'utente deve avere il ruolo ReplicationAdmin con i privilegi seguenti:

  * **REPLICATION CLIENT** : necessario solo per le attività di elaborazione delle modifiche. In altre parole, le attività che eseguono solo il caricamento completo non richiedono questo privilegio.
  * **REPLICATION REPLICA**: necessario solo per le attività di elaborazione delle modifiche. In altre parole, le attività che eseguono solo il caricamento completo non richiedono questo privilegio.
  * **SUPER**: necessario solo nelle versioni precedenti a MySQL 5.6.6.

## <a name="migrate-the-sample-schema"></a>Eseguire la migrazione dello schema di esempio

Per completare tutti gli oggetti di database, ad esempio schemi di tabella, indici e stored procedure, è necessario estrarre lo schema dal database di origine e applicarlo al database. Per estrarre lo schema, è possibile usare mysqldump con il parametro `--no-data`.

Supponendo di avere il database MySQL di esempio **Employees** nel sistema locale, il comando per eseguire la migrazione dello schema con mysqldump è:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Ad esempio:

```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

Per importare lo schema nel Database di Azure per MySQL di destinazione, eseguire il comando seguente:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Ad esempio:

```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

Se nello schema sono presenti chiavi esterne, il caricamento iniziale e la sincronizzazione continua della migrazione avrà esito negativo.  Eseguire lo script seguente in MySQL Workbench per estrarre lo script di eliminazione della chiave esterna e lo script di aggiunta della chiave esterna.

```sql
SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
    KCU.TABLE_NAME,
    KCU.COLUMN_NAME,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
    WHERE
      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
  AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
  GROUP BY SchemaName;
 ```

Eseguire drop foreign key, ovvero la seconda colonna, sul risultato della query per eliminare la chiave esterna.

> [!NOTE]
> Il Servizio Migrazione del database di Azure non supporta l'azione referenziale CASCADE, che consente di eliminare o aggiornare automaticamente una riga corrispondente nella tabella figlio quando viene eliminata o aggiornata una riga nella tabella padre. Per altre informazioni, vedere la sezione Azioni referenziali dell'articolo [Vincoli FOREIGN KEY](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html) nella documentazione di MySQL.
> Il Servizio Migrazione del database di Azure richiede di eliminare i vincoli di chiave esterna nel database di destinazione durante il caricamento iniziale di dati e non è possibile usare azioni referenziali. Se il carico di lavoro dipende dall'aggiornamento di una tabella figlio correlata tramite questa operazione referenziale, è consigliabile eseguire invece un'operazione di [dump e ripristino](../mysql/concepts-migrate-dump-restore.md). 


> [!IMPORTANT]
> Se vengono importati dati tramite backup, rimuovere i comandi CREATE DEFINER manualmente o tramite il comando skip-definer quando si esegue mysqldump. DEFINER richiede privilegi avanzati per la creazione ed è limitato nel Database di Azure per MySQL.

Se sono presenti trigger nel database, verrà applicata l'integrità dei dati nella destinazione prima della migrazione completa dei dati dall'origine. È consigliabile disabilitare i trigger in tutte le tabelle nella destinazione durante la migrazione e quindi abilitare i trigger al termine della migrazione.

Eseguire lo script seguente in MySQL Workbench nel database di destinazione per estrarre lo script del trigger di rilascio e aggiungere lo script del trigger.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

Eseguire la query del trigger di rilascio (colonna DropQuery) generata nel risultato per eliminare i trigger nel database di destinazione. La query di aggiunta trigger può essere salvata, da usare dopo il completamento della migrazione dei dati.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

   ![Mostra le sottoscrizioni del portale](media/tutorial-mysql-to-azure-mysql-online/01-portal-select-subscriptions.png)

2. Selezionare la sottoscrizione in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

    ![Visualizzare i provider di risorse](media/tutorial-mysql-to-azure-mysql-online/02-01-portal-select-resource-provider.png)

3. Ricercare la migrazione e quindi a destra del **Microsoft.DataMigration** selezionare **Registro**.

    ![Registrare il provider di risorse](media/tutorial-mysql-to-azure-mysql-online/02-02-portal-register-resource-provider.png)

## <a name="create-a-database-migration-service-instance"></a>Creare un'istanza del Servizio Migrazione del database

1. Nel portale di Azure selezionare **+ Crea una risorsa,** cercare Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/03-dms-portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-mysql-to-azure-mysql-online/04-dms-portal-marketplace-create.png)
  
3. Nella schermata **Crea servizio Migrazione** specificare un nome per il servizio, la sottoscrizione e un gruppo di risorse nuovo o esistente.

4. Selezionare un piano tariffario e passare alla schermata di rete. La funzionalità di migrazione offline è disponibile sia nel piano tariffario Standard che nel piano tariffario Premium.

    Per altre informazioni sui costi e i piani tariffari, vedere la [pagina relativa ai prezzi](https://aka.ms/dms-pricing).

    ![Configurare Servizio Migrazione del database di Azure di base](media/tutorial-mysql-to-azure-mysql-online/05-dms-portal-create-basic.png)

5. Selezionare una rete virtuale esistente nell'elenco o specificare il nome della nuova rete virtuale da creare. Passare alla schermata di revisione e creazione. Facoltativamente, è possibile aggiungere tag al servizio usando la schermata dei tag.

    La rete virtuale consente al Servizio Migrazione del database di Azure di accedere all'istanza di SQL Server di origine e all'istanza di destinazione di Database SQL di Azure.

    ![Configurare Servizio Migrazione del database di Azure di rete](media/tutorial-mysql-to-azure-mysql-online/06-dms-portal-create-networking.png)

    Per altre informazioni su come creare una rete virtuale nel portale di Azure, vedere l'articolo [Creare una rete virtuale con il portale di Azure](../virtual-network/quick-create-portal.md).

6. Esaminare le configurazioni e **selezionare Crea** per creare il servizio.
    
    ![Servizio Migrazione del database di Azure creare](media/tutorial-mysql-to-azure-mysql-online/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato il servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.  

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

    ![Individuare tutte le istanze di Servizio Migrazione del database di Azure](media/tutorial-mysql-to-azure-mysql-online/08-01-dms-portal-search-service.png)

2. Selezionare l'istanza del servizio di migrazione nei risultati della ricerca e selezionare + **Nuovo progetto di migrazione.**
    
    ![Creare un nuovo progetto di migrazione](media/tutorial-mysql-to-azure-mysql-online/08-02-dms-portal-new-project.png)

3. Nella  schermata Nuovo progetto di migrazione specificare un nome per il progetto, nella casella di selezione Tipo di **server** di origine  selezionare **MySQL**, nella casella di selezione Tipo del **server** di destinazione selezionare Database di Azure per **MySQL** e nella casella di selezione Tipo di attività di migrazione selezionare **Migrazione** dati online . Selezionare **Crea ed esegui attività**.

    ![Creare il progetto del Servizio Migrazione del database](media/tutorial-mysql-to-azure-mysql-online/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > In alternativa, è possibile scegliere **Crea solo il progetto** per creare subito il progetto di migrazione ed eseguire la migrazione in un secondo momento.

## <a name="configure-migration-project"></a>Configurare il progetto di migrazione

1. Nella schermata **Seleziona origine** specificare i dettagli di connessione per l'istanza di MySQL di origine e selezionare **Avanti: Selezionare** l'origine>>

    ![Schermata Aggiungi dettagli origine](media/tutorial-mysql-to-azure-mysql-online/10-dms-portal-project-mysql-source.png)

2. Nella schermata **Seleziona destinazione specificare i** dettagli di connessione per l'istanza di Database di Azure per MySQL di destinazione e selezionare **Avanti: Selezionare** i database>>

    ![Schermata Aggiungi dettagli destinazione](media/tutorial-mysql-to-azure-mysql-online/11-dms-portal-project-mysql-target.png)

3. Nella schermata **Seleziona database eseguire** il mapping del database di origine e del database di destinazione per la migrazione e selezionare Avanti: Configurare le impostazioni di migrazione **>>**. È possibile selezionare **l'opzione** Rendi di sola lettura server di origine per impostare l'origine come di sola lettura, ma è necessario assicurarsi che si tratta di un'impostazione a livello di server. Se selezionata, imposta l'intero server in sola lettura, non solo i database selezionati.
    
    Se il database di destinazione contiene lo stesso nome del database di origine, il Servizio Migrazione del database di Azure seleziona il database di destinazione per impostazione predefinita.
    ![Schermata Dei dettagli del database](media/tutorial-mysql-to-azure-mysql-online/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
   > Sebbene in questo passaggio sia possibile selezionare più database, ogni istanza del Servizio Migrazione del database di Azure supporta fino a quattro database per la migrazione simultanea. È anche previsto un limite di 10 istanze del Servizio Migrazione del database di Azure per sottoscrizione e per area. Ad esempio, se si deve eseguire la migrazione di 80 database, è possibile farlo simultaneamente a gruppi di 40 nella stessa area, ma solo se sono state create 10 istanze del Servizio Migrazione del database di Azure.

4. Nella schermata **Configura impostazioni di** migrazione selezionare le tabelle da far parte della migrazione e selezionare **Avanti: Riepilogo**>>. Se le tabelle di destinazione hanno dati, non vengono selezionate per impostazione predefinita, ma è possibile selezionarle in modo esplicito e verranno troncate prima di avviare la migrazione.

    ![Schermata Seleziona tabelle](media/tutorial-mysql-to-azure-mysql-online/13-dms-portal-project-mysql-select-tbl.png)

5. Nella schermata **Riepilogo** specificare  un nome per l'attività di migrazione nella casella di testo Nome attività ed esaminare il riepilogo per assicurarsi che i dettagli di origine e destinazione corrispondano a quanto specificato in precedenza.

    ![Riepilogo del progetto di migrazione](media/tutorial-mysql-to-azure-mysql-online/14-dms-portal-project-mysql-activity-summary.png)

6. Selezionare **Avvia migrazione**. Verrà visualizzata la finestra dell'attività di migrazione con il campo **Stato** dell'attività impostato su **Inizializzazione in corso**. Lo **stato viene** modificato in In **esecuzione** all'avvio della migrazione della tabella.

## <a name="monitor-the-migration"></a>Monitorare la migrazione

1. Nella schermata dell'attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione finché nel campo **Stato** della migrazione non viene indicato **Completata**.

     ![Stato attività: completata](media/tutorial-mysql-to-azure-mysql-online/15-dms-activity-completed.png)

2. In **Nome database** selezionare un database specifico per ottenere lo stato di migrazione per le operazioni **Caricamento completo** e **Sincronizzazione dei dati incrementale**.

    Caricamento completo indica lo stato di migrazione del carico iniziale, mentre Sincronizzazione dei dati incrementale indica lo stato di Change Data Capture (CDC).

     ![Stato attività: caricamento completo completato](media/tutorial-mysql-to-azure-mysql-online/16-dms-activity-full-load-completed.png)

     ![Stato attività: sincronizzazione dei dati incrementale](media/tutorial-mysql-to-azure-mysql-online/17-dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Eseguire il cutover della migrazione

Al termine del caricamento completo iniziale, i database vengono contrassegnati con **Pronto per il cutover**.

1. Quando si è pronti per completare la migrazione del database, selezionare **Avvia cutover**.

    ![Avvia cutover](media/tutorial-mysql-to-azure-mysql-online/18-dms-start-cutover.png)

2. Assicurarsi di arrestare tutte le transazioni in ingresso nel database di origine. Attendere finché il contatore **Modifiche in sospeso** mostra **0**.
3. Selezionare **Conferma** e quindi **Applica**.
4. Quando lo stato della migrazione del database è **Completata**, connettere le applicazioni al nuovo database SQL di Azure di destinazione.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle limitazioni e i problemi noti quando si eseguono migrazioni online in Database di Azure per MySQL, vedere l'articolo sui [problemi noti e soluzioni alternative per le migrazioni online in Database di Azure per MySQL](known-issues-azure-mysql-online.md).
* Per informazioni su Servizio Migrazione del database di Azure, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](./dms-overview.md).
* Per informazioni sul Database di Azure per MySQL, vedere l'articolo [Database di Azure per MySQL](../mysql/overview.md).
