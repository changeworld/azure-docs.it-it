---
title: 'Da MySQL a database SQL di Azure: Guida alla migrazione'
description: Questa guida illustra come eseguire la migrazione dei database MySQL nel database SQL di Azure usando SQL Server Migration Assistant per MySQL (SSMA per MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: ff7b2115b396bf42cdeffa9c58bffb1802e980d1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721886"
---
# <a name="migration-guide--mysql-to-azure-sql-database"></a>Guida alla migrazione: MySQL per il database SQL di Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Questa guida illustra come eseguire la migrazione del database MySQL al database SQL di Azure usando SQL Server Migration Assistant per MySQL (SSMA per MySQL). 

Per altre guide alla migrazione, vedere [Migrazione dei database](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Prerequisiti

Per eseguire la migrazione del database MySQL al database SQL di Azure, è necessario quanto segue:

- Verificare che l'ambiente di origine sia supportato. Attualmente sono supportati MySQL 5,6 e 5,7. 
- [SQL Server Migration Assistant per MySQL](https://www.microsoft.com/download/confirmation.aspx?id=54257)


## <a name="pre-migration"></a>Pre-migrazione 

Una volta soddisfatti i prerequisiti, si è pronti per individuare la topologia dell'ambiente e valutare la fattibilità della migrazione.

### <a name="assess"></a>Valutare 

Usando [SQL Server Migration Assistant per MySQL](https://www.microsoft.com/download/confirmation.aspx?id=54257), è possibile esaminare i dati e gli oggetti di database e valutare i database per la migrazione.

Per creare una valutazione, seguire questa procedura.

1. Aprire SQL Server Migration Assistant per MySQL. 
1. Selezionare **file** dal menu, quindi scegliere **nuovo progetto**. Specificare il nome del progetto, un percorso in cui salvare il progetto. Scegliere il **database SQL di Azure** come destinazione della migrazione. 

   ![Nuovo progetto](./media/mysql-to-sql-database-guide/new-project.png)

1. Scegliere **Connetti a MySQL** e specificare i dettagli della connessione per connettere il server MySQL. 

   ![Connettersi a MySQL](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. Fare clic con il pulsante destro del mouse sullo schema MySQL in **MySQL Metadata Explorer** e scegliere **Crea report**. In alternativa, è possibile selezionare **Crea report** dalla barra di spostamento in alto a linee. 

   ![Creazione di report](./media/mysql-to-sql-database-guide/create-report.png)

1. Esaminare il report HTML per le statistiche di conversione, nonché gli errori e gli avvisi. Analizzarlo per comprendere le soluzioni e i problemi di conversione. 

   È inoltre possibile accedere a questo report dalla cartella SSMA Projects come selezionato nella prima schermata. Dall'esempio precedente, individuare il file di report.xml da:
 
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   e aprirlo in Excel per ottenere un inventario degli oggetti MySQL e lo sforzo necessario per eseguire le conversioni dello schema.

    ![Report di conversione](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-data-types"></a>Convalidare i tipi di dati

Convalidare i mapping dei tipi di dati predefiniti e modificarli in base ai requisiti, se necessario. A questo scopo, attenersi alla procedura seguente: 

1. Selezionare **Tools** (Strumenti) dal menu. 
1. Selezionare **Project Settings** (Impostazioni progetto). 
1. Selezionare la scheda **Type mappings** (Mapping tipi). 

   ![Mapping dei tipi](./media/mysql-to-sql-database-guide/type-mappings.png)

1. È possibile modificare il mapping dei tipi per ogni tabella selezionando la tabella in **MySQL Metadata Explorer**. 

### <a name="convert-schema"></a>Converti schema 

Per convertire lo schema, seguire questa procedura: 

1. Opzionale Per convertire le query dinamiche o ad hoc, fare clic con il pulsante destro del mouse sul nodo e scegliere **Aggiungi istruzione**. 
1. Scegliere **Connetti a database SQL di Azure** dalla barra di spostamento in alto a linee e specificare i dettagli della connessione. È possibile scegliere di connettersi a un database esistente o specificare un nuovo nome, nel qual caso verrà creato un database nel server di destinazione.

   ![Connetti a SQL](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Fare clic con il pulsante destro del mouse sullo schema e scegliere **Converti schema**. 

   ![Converti schema](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Al termine della conversione dello schema, confrontare il codice convertito con il codice originale per identificare i potenziali problemi. 

   Confrontare gli oggetti convertiti con quelli originali: 

   ![ Confrontare ed esaminare l'oggetto ](./media/mysql-to-sql-database-guide/table-comparison.png)

   Confrontare le routine convertite con le procedure originali: 

   ![Confrontare e verificare il codice oggetto](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate"></a>Migrazione 

Dopo aver completato la valutazione dei database e corretto eventuali discrepanze, il passaggio successivo consiste nell'eseguire il processo di migrazione. La migrazione prevede due passaggi: la pubblicazione dello schema e la migrazione dei dati. 

Per pubblicare lo schema ed eseguire la migrazione dei dati, attenersi alla procedura seguente: 

1. Fare clic con il pulsante destro del mouse sul database da **Esplora metadati del database SQL di Azure** e scegliere **Sincronizza con database**. Questa azione pubblica lo schema MySQL nel database SQL di Azure.

   ![Sincronizza con database](./media/mysql-to-sql-database-guide/synchronize-database.png)

   Esaminare il mapping tra il progetto di origine e la destinazione:

   ![Sincronizzare con la revisione del database](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Fare clic con il pulsante destro del mouse sullo schema MySQL da **MySQL Metadata Explorer** e scegliere **Migrate data**. In alternativa, è possibile selezionare **migrare i dati** dall'esplorazione della riga superiore. 

   ![Eseguire la migrazione dei dati](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Al termine della migrazione, visualizzare il report di **migrazione dei dati** : 

   ![Report di migrazione dati](./media/mysql-to-sql-database-guide/data-migration-report.png)

1.  Convalidare la migrazione esaminando i dati e lo schema nel database SQL di Azure usando SQL Server Management Studio (SSMS).

    ![Convalida in SSMA](./media/mysql-to-sql-database-guide/validate-in-ssms.png)



## <a name="post-migration"></a>Post-migrazione 

Dopo aver completato la fase di **migrazione** , è necessario eseguire una serie di attività post-migrazione per assicurarsi che tutto funzioni correttamente nel modo più semplice ed efficiente possibile.

### <a name="remediate-applications"></a>Correggere le applicazioni

Dopo la migrazione dei dati nell'ambiente di destinazione, tutte le applicazioni che in precedenza usavano l'origine devono iniziare a usare la destinazione. Per ottenere questo risultato, in alcuni casi sarà necessario apportare modifiche alle applicazioni.

### <a name="perform-tests"></a>Eseguire test

L'approccio di test per la migrazione del database consiste nell'eseguire le attività seguenti:

1. **Sviluppare i test di convalida**. Per testare la migrazione del database, è necessario usare le query SQL. È necessario creare le query di convalida da eseguire sia sul database di origine che su quello di destinazione. Le query di convalida devono essere estese all'ambito definito.

2. **Configurare l'ambiente di test**. L'ambiente di test deve contenere una copia del database di origine e del database di destinazione. Assicurarsi di isolare l'ambiente di test.

3. **Eseguire i test di convalida**. Eseguire i test di convalida sull'origine e sulla destinazione, quindi analizzare i risultati.

4. **Eseguire test delle prestazioni**. Eseguire test delle prestazioni sull'origine e sulla destinazione, quindi analizzare e confrontare i risultati.

### <a name="optimize"></a>Ottimizzazione

La fase post-migrazione è fondamentale per riconciliare eventuali problemi di accuratezza dei dati e verificare la completezza, nonché per risolvere i problemi di prestazioni del carico di lavoro.

Per ulteriori dettagli su questi problemi e su passaggi specifici per attenuarli, vedere la [Guida alla convalida e all'ottimizzazione post-migrazione](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Risorse per la migrazione

Per ulteriori informazioni sul completamento di questo scenario di migrazione, vedere le risorse seguenti, che sono state sviluppate a supporto di un progetto di migrazione del mondo reale.

| Titolo/collegamento     | Descrizione    |
| ---------------------------------------------- | ---------------------------------------------------- |
| [Strumento e modello di valutazione del carico di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Questo strumento indica le piattaforme di destinazione "più idonee" suggerite, la preparazione per il cloud e il livello di correzione di applicazioni/database per un determinato carico di lavoro. Offre semplici calcoli con un solo clic e generazione di report che consentono di accelerare le valutazioni di grandi dimensioni grazie a un processo decisionale automatizzato e uniforme della piattaforma di destinazione. |

Queste risorse sono state sviluppate come parte del programma Data SQL Ninja, sponsorizzato dal team di progettazione Azure Data Group. Obiettivo principale del programma Data SQL Ninja è rendere disponibili opportunità per velocizzare progetti di modernizzazione complessi e la migrazione delle piattaforme dati alla piattaforma dati di Microsoft Azure. Se si ritiene che l'organizzazione possa essere interessata a partecipare al programma Data SQL Ninja, contattare il team dell'account per richiedere l'invio di una candidatura.

## <a name="next-steps"></a>Passaggi successivi 

- Assicurarsi di consultare il [calcolatore TCO (Total cost of Ownership) di Azure](https://aka.ms/azure-tco) per stimare i risparmi consentiti grazie alla migrazione dei carichi di lavoro in Azure.

- Per una matrice di servizi e strumenti di Microsoft e di terze parti disponibili per supportare diversi scenari di migrazione di database e dati, nonché per le attività speciali, vedere l'articolo [servizio e strumenti per la migrazione dei dati](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

- Per altre guide alla migrazione, vedere [Migrazione dei database](https://datamigration.microsoft.com/). 

Per i video, vedere: 
- [Panoramica del percorso di migrazione e degli strumenti e dei servizi consigliati per eseguire la valutazione e la migrazione](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
