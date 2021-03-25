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
ms.openlocfilehash: 14b2c1f98ae977548edb635b8a8a7a956b3f2dd7
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023776"
---
# <a name="migration-guide--mysql-to-azure-sql-database"></a>Guida alla migrazione: MySQL per il database SQL di Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Questa guida illustra come eseguire la migrazione del database MySQL al database SQL di Azure usando SQL Server Migration Assistant per MySQL (SSMA per MySQL). 

Per altre guide alla migrazione, vedere [Migrazione dei database](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Prerequisiti

Per eseguire la migrazione del database MySQL al database SQL di Azure, è necessario quanto segue:

- Per verificare che l'ambiente di origine sia supportato. Attualmente sono supportati MySQL 5,6 e 5,7. 
- [SQL Server Migration Assistant per MySQL](https://www.microsoft.com/download/details.aspx?id=54257)
- Connettività e autorizzazioni sufficienti per accedere sia all'origine che alla destinazione. 


## <a name="pre-migration"></a>Pre-migrazione 

Una volta soddisfatti i prerequisiti, si è pronti per individuare la topologia dell'ambiente e valutare la fattibilità della migrazione.

### <a name="assess"></a>Valutare 

Usare SQL Server Migration Assistant (SSMA) per MySQL per esaminare i dati e gli oggetti di database e valutare i database per la migrazione. 

Per creare una valutazione, seguire questa procedura: 

1. Aprire [SQL Server Migration Assistant per MySQL](https://www.microsoft.com/download/details.aspx?id=54257). 
1. Selezionare **file** dal menu, quindi scegliere **nuovo progetto**. 
1. Specificare il nome del progetto, un percorso in cui salvare il progetto. Scegliere il **database SQL di Azure** come destinazione della migrazione. Selezionare **OK**:

   ![Nuovo progetto](./media/mysql-to-sql-database-guide/new-project.png)

1. Scegliere **Connetti a MySQL** e specificare i dettagli della connessione per connettere il server MySQL:

   ![Connettersi a MySQL](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. Fare clic con il pulsante destro del mouse sullo schema MySQL in **MySQL Metadata Explorer** e scegliere **Crea report**. In alternativa, è possibile selezionare **Crea report** dalla barra di spostamento in alto a linee:

   ![Creazione di report](./media/mysql-to-sql-database-guide/create-report.png)

1. Leggere il report HTML per esaminare le statistiche di conversione e gli eventuali errori o avvisi. È anche possibile aprire il report in Excel per ottenere un inventario degli oggetti MySQL e il lavoro richiesto per eseguire le conversioni dello schema. La posizione predefinita del report è la cartella report all'interno di SSMAProjects.
 
   ad esempio `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Report di conversione](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-data-types"></a>Convalidare i tipi di dati

Convalidare i mapping dei tipi di dati predefiniti e modificarli in base ai requisiti, se necessario. A questo scopo, attenersi alla procedura seguente: 

1. Selezionare **Tools** (Strumenti) dal menu. 
1. Selezionare **Project Settings** (Impostazioni progetto). 
1. Selezionare la scheda **mapping dei tipi** :

   ![Mapping dei tipi](./media/mysql-to-sql-database-guide/type-mappings.png)

1. È possibile modificare il mapping dei tipi per ogni tabella selezionando la tabella in **MySQL Metadata Explorer**. 

### <a name="convert-schema"></a>Converti schema 

Per convertire lo schema, seguire questa procedura: 

1. Opzionale Per convertire le query dinamiche o ad hoc, fare clic con il pulsante destro del mouse sul nodo e scegliere **Aggiungi istruzione**. 
1. Selezionare **Connetti a database SQL di Azure**. 
    1. Immettere i dettagli della connessione per connettere il database nel database SQL di Azure.
    1. Scegliere il database SQL di destinazione dall'elenco a discesa o specificare un nuovo nome, nel qual caso verrà creato un database nel server di destinazione. 
    1. Fornire i dettagli di autenticazione. 
    1. Selezionare **Connetti**:

   ![Connetti a SQL](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Fare clic con il pulsante destro del mouse sullo schema e scegliere **Converti schema**. In alternativa, è possibile scegliere **Converti schema** dalla barra di spostamento della riga superiore dopo aver scelto il database:

   ![Converti schema](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Al termine della conversione, confrontare ed esaminare gli oggetti convertiti con gli oggetti originali per identificare i potenziali problemi e risolverli in base alle indicazioni:

   ![Gli oggetti convertiti possono essere confrontati con l'origine](./media/mysql-to-sql-database-guide/table-comparison.png)

   Confrontare il testo Transact-SQL convertito con il codice originale ed esaminare le indicazioni:

   ![Le query convertite possono essere confrontate con il codice sorgente](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. Selezionare **Verifica risultati** nel riquadro Output ed esaminare gli errori nel riquadro **Elenco errori** . 
1. Salvare il progetto in locale per un esercizio di correzione dello schema offline. Scegliere **Salva progetto** dal menu **File**. In questo modo è possibile valutare gli schemi di origine e di destinazione offline ed eseguire la correzione prima di poter pubblicare lo schema nel database SQL.



## <a name="migrate"></a>Migrazione 

Dopo aver completato la valutazione dei database e corretto eventuali discrepanze, il passaggio successivo consiste nell'eseguire il processo di migrazione. La migrazione prevede due passaggi: la pubblicazione dello schema e la migrazione dei dati. 

Per pubblicare lo schema ed eseguire la migrazione dei dati, attenersi alla procedura seguente: 

1. Pubblicare lo schema: fare clic con il pulsante destro del mouse sul database da **Esplora metadati del database SQL di Azure** e scegliere **Sincronizza con database**. Questa azione pubblica lo schema MySQL nel database SQL di Azure:

   ![Sincronizza con database](./media/mysql-to-sql-database-guide/synchronize-database.png)

   Esaminare il mapping tra il progetto di origine e la destinazione:

   ![Sincronizzare con la revisione del database](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Migrare i dati: fare clic con il pulsante destro del mouse sul database o sull'oggetto di cui si vuole eseguire la migrazione in **MySQL Metadata Explorer** e scegliere **Migrate data**. In alternativa, è possibile selezionare **migrare i dati** dalla barra di spostamento in alto a linea. Per eseguire la migrazione dei dati per un intero database, selezionare la casella di controllo accanto al nome del database. Per eseguire la migrazione dei dati da singole tabelle, espandere il database, espandere tabelle, quindi selezionare la casella di controllo accanto alla tabella. Per omettere i dati dalle singole tabelle, deselezionare la casella di controllo:

   ![Eseguire la migrazione dei dati](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Al termine della migrazione, visualizzare il report di **migrazione dei dati** : 

   ![Report di migrazione dati](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Connettersi al database SQL di Azure usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e convalidare la migrazione riesaminando i dati e lo schema:

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
