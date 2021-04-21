---
title: 'Esercitazione: Eseguire la migrazione offline di MySQL a Database di Azure per MySQL con Servizio Migrazione del database'
titleSuffix: Azure Database Migration Service
description: Informazioni su come eseguire una migrazione offline da MySQL locale a Database di Azure per MySQL usando Servizio Migrazione del database di Azure.
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: 71363771359ffef0ff165bd4a6744d864ea1856c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819647"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-offline-using-dms"></a>Esercitazione: Eseguire la migrazione offline di MySQL a Database di Azure per MySQL con Servizio Migrazione del database

È possibile usare Servizio Migrazione del database di Azure per eseguire una sola volta la migrazione completa del database dall'istanza locale di MySQL a Database di Azure per [MySQL](../mysql/index.yml) con funzionalità di migrazione dei dati ad alta velocità. In questa esercitazione verrà eseguita la migrazione di un database di esempio da un'istanza locale di MySQL 5.7 a Database di Azure per MySQL (v5.7) usando un'attività di migrazione offline in Servizio Migrazione del database di Azure. Anche se gli articoli presuppongono che l'origine sia un'istanza di database MySQL e che la destinazione sia Database di Azure per MySQL, può essere usata per eseguire la migrazione da un Database di Azure per MySQL a un altro semplicemente modificando il nome e le credenziali del server di origine. È supportata anche la migrazione da server MySQL di versione precedente (v5.6 e versioni successive) a versioni successive.

> [!IMPORTANT]
> Per le migrazioni online, è possibile usare questa nuova offerta insieme alla [replica dei dati in ingresso.](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) In alternativa, usare strumenti open source come [MyDumper/MyLoader](https://centminmod.com/mydumper.html) con la replica dei dati in ingresso per le migrazioni online. 

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]


In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Eseguire la migrazione dello schema del database usando l'utilità mysqldump.
> * Creare un'istanza del servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione tramite il Servizio Migrazione del database di Azure.
> * Eseguire la migrazione.
> * Monitorare la migrazione.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

* Avere a disposizione un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free).
* Avere un database MySQL locale con la versione 5.7. In caso contrario, scaricare e installare [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.7.
* [Creare un'istanza in Database di Azure per MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Per informazioni dettagliate su come connettersi e creare un database usando l'applicazione Workbench, vedere l'articolo Usare [MySQL Workbench](../mysql/connect-workbench.md) per connettersi ai dati ed eseguire query. La versione di Database di Azure per MySQL deve essere uguale o superiore alla versione locale di MySQL . Ad esempio, MySQL 5.7 può eseguire la migrazione a Database di Azure per MySQL 5.7 o essere aggiornato a 8. 
* Creare una rete virtuale di Microsoft Azure per il Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che offre la connettività da sito a sito per i server di origine locali con [ExpressRoute](../expressroute/expressroute-introduction.md) o [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Per altre informazioni sulla creazione di una rete virtuale, vedere la [documentazione sulla rete virtuale](../virtual-network/index.yml) e in particolare gli articoli di avvio rapido con istruzioni dettagliate.

    > [!NOTE]
    > Durante la configurazione della rete virtuale, se si usa ExpressRoute con il peering di rete a Microsoft, aggiungere gli [endpoint](../virtual-network/virtual-network-service-endpoints-overview.md) servizio seguenti alla subnet in cui verrà effettuato il provisioning del servizio:
    >
    > * Endpoint del database di destinazione (ad esempio endpoint SQL, endpoint Cosmos DB e così via)
    > * Endpoint di archiviazione
    > * Endpoint bus di servizio
    >
    > Questa configurazione è necessaria perché il Servizio Migrazione del database di Azure non ha connettività Internet.

* Assicurarsi che le regole del gruppo di sicurezza di rete della rete virtuale non blocchino la porta in uscita 443 di ServiceTag per ServiceBus, Archiviazione e AzureMonitor. Per informazioni dettagliate sul filtro del traffico dei gruppi di sicurezza di rete della rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Aprire Windows Firewall per consentire alle connessioni dalla rete virtuale Servizio Migrazione del database di Azure accedere al server MySQL di origine, che per impostazione predefinita è la porta TCP 3306.
* Quando si usa un'appliance firewall davanti ai database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire alle connessioni dalla rete virtuale per Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.
* Creare una regola del firewall a livello di [server](../azure-sql/database/firewall-configure.md) o configurare gli [endpoint](../mysql/howto-manage-vnet-using-portal.md) di servizio della rete virtuale per Database di Azure per MySQL di destinazione per consentire alla rete virtuale Servizio Migrazione del database di Azure l'accesso ai database di destinazione.
* L'origine MySQL deve essere in un'Edizione MySQL Community supportata. Per determinare la versione dell'istanza di MySQL, eseguire il comando seguente nell'utilità MySQL o in MySQL Workbench:

    ```
    SELECT @@version;
    ```

* Database di Azure per MySQL supporta solo le tabelle InnoDB. Per convertire le tabelle MyISAM in InnoDB, vedere l'articolo [Converting Tables from MyISAM to InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) (Conversione di tabelle da MyISAM a InnoDB).
* L'utente deve avere i privilegi per leggere i dati nel database di origine.

## <a name="migrate-database-schema"></a>Eseguire la migrazione dello schema del database

Per trasferire tutti gli oggetti di database come schemi di tabella, indici e stored procedure, è necessario estrarre lo schema dal database di origine e applicarlo al database di destinazione. Per estrarre lo schema, è possibile usare mysqldump con il parametro `--no-data`. A questo scopo è necessario un computer in grado di connettersi sia al database MySQL di origine che al database di Azure di destinazione per MySQL.

Per esportare lo schema usando mysqldump, eseguire il comando seguente:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Ad esempio:

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

Per importare lo schema nel database di Azure per MySQL di destinazione, eseguire il comando seguente:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Ad esempio:

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

Se nello schema sono presenti chiavi esterne, il caricamento parallelo dei dati durante la migrazione verrà gestito dall'attività di migrazione. Non è necessario eliminare le chiavi esterne durante la migrazione dello schema.

Se sono presenti trigger nel database, verrà applicata l'integrità dei dati nella destinazione prima della migrazione completa dei dati dall'origine. È consigliabile disabilitare i trigger in tutte le tabelle nella destinazione durante la migrazione e quindi abilitare i trigger al termine della migrazione.

Eseguire lo script seguente in MySQL Workbench nel database di destinazione per estrarre lo script del trigger di rilascio e aggiungere lo script di trigger.

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

Eseguire la query del trigger di rilascio (colonna DropQuery) generata nel risultato per eliminare i trigger nel database di destinazione. È possibile salvare la query di aggiunta trigger da usare dopo il completamento della migrazione dei dati.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

La registrazione del provider di risorse deve essere eseguita in ogni sottoscrizione di Azure una sola volta. Senza la registrazione, non sarà possibile creare un'istanza di **Servizio Migrazione del database di Azure**.

1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

   ![Mostra le sottoscrizioni del portale](media/tutorial-mysql-to-azure-mysql-offline-portal/01-dms-portal-select-subscription.png)

2. Selezionare la sottoscrizione in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

3. Ricercare la migrazione e quindi a destra del **Microsoft.DataMigration** selezionare **Registro**.

    ![Registrare il provider di risorse](media/tutorial-mysql-to-azure-mysql-offline-portal/02-dms-portal-register-rp.png)

## <a name="create-a-database-migration-service-instance"></a>Creare un'istanza del Servizio Migrazione del database

1. Nel portale di Azure selezionare **+ Crea una risorsa,** cercare Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-offline-portal/03-dms-portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-mysql-to-azure-mysql-offline-portal/04-dms-portal-marketplace-create.png)
  
3. Nella schermata **Crea servizio Migrazione** specificare un nome per il servizio, la sottoscrizione e un gruppo di risorse nuovo o esistente.

4. Selezionare un piano tariffario e passare alla schermata di rete. La funzionalità di migrazione offline è disponibile nel piano tariffario Standard e Premium.

    Per altre informazioni sui costi e i piani tariffari, vedere la [pagina relativa ai prezzi](https://aka.ms/dms-pricing).

    ![Configurare le Servizio Migrazione del database di Azure di base](media/tutorial-mysql-to-azure-mysql-offline-portal/05-dms-portal-create-basic.png)

5. Selezionare una rete virtuale esistente nell'elenco o specificare il nome della nuova rete virtuale da creare. Passare alla schermata rivedi e crea. Facoltativamente, è possibile aggiungere tag al servizio usando la schermata dei tag.

    La rete virtuale consente al Servizio Migrazione del database di Azure di accedere all'istanza di SQL Server di origine e all'istanza di destinazione di Database SQL di Azure.

    ![Configurare le Servizio Migrazione del database di Azure di rete](media/tutorial-mysql-to-azure-mysql-offline-portal/06-dms-portal-create-networking.png)

    Per altre informazioni su come creare una rete virtuale nel portale di Azure, vedere l'articolo [Creare una rete virtuale con il portale di Azure](../virtual-network/quick-create-portal.md).

6. Esaminare le configurazioni e **selezionare Crea** per creare il servizio.
    
    ![Servizio Migrazione del database di Azure creazione](media/tutorial-mysql-to-azure-mysql-offline-portal/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato il servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.  

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

    ![Individuare tutte le istanze di Servizio Migrazione del database di Azure](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. Selezionare l'istanza del servizio di migrazione nei risultati della ricerca e selezionare + **Nuovo progetto di migrazione.**
    
    ![Creare un nuovo progetto di migrazione](media/tutorial-mysql-to-azure-mysql-offline-portal/08-02-dms-portal-new-project.png)

3. Nella  schermata Nuovo progetto di migrazione specificare un nome per il progetto, nella casella  di selezione Tipo di **server** di origine  selezionare **MySQL**, nella casella di selezione Tipo server di destinazione selezionare Database di Azure per **MySQL** **\[ \]** e nella casella di selezione Tipo di attività di migrazione selezionare Anteprima migrazione dati . Selezionare **Crea ed esegui attività**.

    ![Creare il progetto del Servizio Migrazione del database](media/tutorial-mysql-to-azure-mysql-offline-portal/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > In alternativa, è possibile scegliere **Crea solo il progetto** per creare subito il progetto di migrazione ed eseguire la migrazione in un secondo momento.

## <a name="configure-migration-project"></a>Configurare il progetto di migrazione

1. Nella schermata **Seleziona origine** specificare i dettagli di connessione per l'istanza di MySQL di origine e selezionare **Avanti: Selezionare** l'origine>>

    ![Schermata Aggiungi dettagli origine](media/tutorial-mysql-to-azure-mysql-offline-portal/10-dms-portal-project-mysql-source.png)

2. Nella schermata **Seleziona destinazione specificare i** dettagli di connessione per l'istanza di Database di Azure per MySQL di destinazione e selezionare **Avanti: Selezionare** i database>>

    ![Schermata Aggiungi dettagli destinazione](media/tutorial-mysql-to-azure-mysql-offline-portal/11-dms-portal-project-mysql-target.png)

3. Nella schermata **Seleziona database eseguire** il mapping del database di origine e del database di destinazione per la migrazione e selezionare Avanti: Configurare le impostazioni di migrazione **>>**. È possibile  selezionare l'opzione Rendi sola lettura server di origine per rendere l'origine di sola lettura, ma è importante che si tratta di un'impostazione a livello di server. Se selezionata, imposta l'intero server su sola lettura, non solo i database selezionati.
    
    Se il database di destinazione contiene lo stesso nome del database di origine, il Servizio Migrazione del database di Azure seleziona il database di destinazione per impostazione predefinita.
    ![Schermata Dei dettagli del database](media/tutorial-mysql-to-azure-mysql-offline-portal/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
    > Sebbene sia possibile selezionare più database in questo passaggio, esistono limiti al numero e alla velocità di migrazione dei database in questo modo, poiché ogni database condividerà il calcolo. Con la configurazione predefinita dello SKU Premium, ogni attività di migrazione tenterà di eseguire la migrazione di due tabelle in parallelo. Queste tabelle possono essere provenienti da uno dei database selezionati. Se non è abbastanza veloce, è possibile suddividere le attività di migrazione del database in attività di migrazione diverse e scalare tra più servizi. È anche previsto un limite di 10 istanze del Servizio Migrazione del database di Azure per sottoscrizione e per area.
    > Per un controllo più granulare sulla velocità effettiva e sulla parallelizzazione della migrazione, vedere l'articolo [PowerShell: Eseguire](./migrate-mysql-to-azure-mysql-powershell.md) la migrazione offline dal database MySQL al database di Azure per MySQL usando il Servizio Migrazione del database

4. Nella schermata **Configura impostazioni di migrazione** selezionare le tabelle da parte della migrazione e selezionare Avanti : **Riepilogo**>>. Se le tabelle di destinazione hanno dati, non vengono selezionate per impostazione predefinita, ma è possibile selezionarle in modo esplicito e verranno troncate prima di avviare la migrazione.

    ![Schermata Seleziona tabelle](media/tutorial-mysql-to-azure-mysql-offline-portal/13-dms-portal-project-mysql-select-tbl.png)

5. Nella casella **di** testo  Nome attività della schermata Riepilogo specificare un nome per l'attività di migrazione ed esaminare il riepilogo per assicurarsi che i dettagli di origine e destinazione corrispondano a quanto specificato in precedenza.

    ![Riepilogo del progetto di migrazione](media/tutorial-mysql-to-azure-mysql-offline-portal/14-dms-portal-project-mysql-activity-summary.png)

6. Selezionare **Avvia migrazione**. Verrà visualizzata la finestra dell'attività di migrazione con il campo **Stato** dell'attività impostato su **Inizializzazione in corso**. Lo **stato cambia** in In **esecuzione** all'avvio delle migrazioni della tabella.
 
     ![Esecuzione della migrazione](media/tutorial-mysql-to-azure-mysql-offline-portal/15-dms-portal-project-mysql-running.png)

## <a name="monitor-the-migration"></a>Monitorare la migrazione

1. Nella schermata dell'attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione e visualizzare lo stato di avanzamento sul numero di tabelle completate. 

2. È possibile fare clic sul nome del database nella schermata dell'attività per visualizzare lo stato di ogni tabella durante la migrazione. Selezionare **Aggiorna** per aggiornare la visualizzazione. 

     ![Monitoraggio della migrazione](media/tutorial-mysql-to-azure-mysql-offline-portal/16-dms-portal-project-mysql-monitor.png)

## <a name="complete-the-migration"></a>Completare la migrazione

1. Nella schermata dell'attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione finché nel campo **Stato** della migrazione non viene indicato **Completata**.

    ![Completare la migrazione](media/tutorial-mysql-to-azure-mysql-offline-portal/17-dms-portal-project-mysql-complete.png)

## <a name="post-migration-activities"></a>Attività post-migrazione

Il cutover della migrazione in una migrazione offline è un processo dipendente dall'applicazione che non è in ambito per questo documento, ma sono prescritte le attività post-migrazione seguenti:

1. Creare account di accesso, ruoli e autorizzazioni in base ai requisiti dell'applicazione.
2. Ricreare tutti i trigger nel database di destinazione come estratti durante il passaggio di pre-migrazione.
3. Eseguire test di sanità dell'applicazione sul database di destinazione per certificare la migrazione. 

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si continuerà a usare il Servizio Migrazione del database, è possibile eliminare il servizio seguendo questa procedura:

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

    ![Individuare tutte le istanze del Servizio Gestione dei dati](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. Selezionare l'istanza del servizio di migrazione nei risultati della ricerca e selezionare **Elimina servizio.**
    
    ![Eliminare il servizio di migrazione](media/tutorial-mysql-to-azure-mysql-offline-portal/18-dms-portal-delete.png)

3. Nella finestra di dialogo di conferma digitare il nome del servizio nella casella di testo **DIGITARE IL NOME** DEL SERVIZIO MIGRAZIONE DEL DATABASE e selezionare **Elimina**

    ![Confermare l'eliminazione del servizio di migrazione](media/tutorial-mysql-to-azure-mysql-offline-portal/19-dms-portal-deleteconfirm.png)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sui problemi noti e sulle limitazioni durante l'esecuzione di migrazioni con servizio Migrazione del database, vedere l'articolo [Problemi comuni - Servizio Migrazione del database di Azure](./known-issues-troubleshooting-dms.md).
* Per la risoluzione dei problemi di connettività del database di origine durante l'uso del Servizio Gestione dei dati, vedere l'articolo [Problemi di connessione dei database di origine.](./known-issues-troubleshooting-dms-source-connectivity.md)
* Per informazioni su Servizio Migrazione del database di Azure, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](./dms-overview.md).
* Per informazioni sul Database di Azure per MySQL, vedere l'articolo [Database di Azure per MySQL](../mysql/overview.md).
* Per indicazioni sull'uso del Servizio Migrazione del database tramite PowerShell, vedere l'articolo [PowerShell:](./migrate-mysql-to-azure-mysql-powershell.md) Eseguire la migrazione offline dal database MySQL a Database di Azure per MySQL usando servizio Migrazione del database