---
title: 'Da MySQL a database SQL di Azure: Guida alla migrazione'
description: Questa guida illustra come eseguire la migrazione dei database MySQL a un database SQL di Azure usando SQL Server Migration Assistant per MySQL (SSMA per MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 32c56df5bafa9439fc559edf137c1080920cfb32
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284375"
---
# <a name="migration-guide-mysql-to-azure-sql-database"></a>Guida alla migrazione: MySQL per il database SQL di Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Questa guida illustra [come eseguire la migrazione](https://azure.microsoft.com/migration/migration-journey) del database MySQL a un database SQL di Azure usando [SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant per MySQL (SSMA per MySQL). 

Per altre guide alla migrazione, vedere [Guida alla migrazione del database di Azure](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare la migrazione del database MySQL a un database SQL, eseguire le operazioni seguenti:

- Verificare che l'ambiente di origine sia supportato. Attualmente sono supportati MySQL 5,6 e 5,7. 
- Scaricare e installare [SQL Server Migration Assistant per MySQL](https://www.microsoft.com/download/details.aspx?id=54257).
- Assicurarsi di disporre di connettività e di autorizzazioni sufficienti per accedere sia all'origine che alla destinazione.

## <a name="pre-migration"></a>Pre-migrazione 

Dopo aver soddisfatto i prerequisiti, si è pronti per individuare la topologia dell'ambiente e valutare la fattibilità della [migrazione del cloud di Azure](https://azure.microsoft.com/migration).

### <a name="assess"></a>Valutare 

Usare SQL Server Migration Assistant (SSMA) per MySQL per esaminare i dati e gli oggetti di database e valutare i database per la migrazione. 

Per creare una valutazione, procedere come segue:

1. Aprire [SSMA per MySQL](https://www.microsoft.com/download/details.aspx?id=54257). 
1. Selezionare **file**, quindi fare clic su **nuovo progetto**. 
1. Nel riquadro **nuovo progetto** immettere un nome e un percorso per il progetto e quindi nell'elenco a discesa **migra a** selezionare **database SQL di Azure**. 
1. Selezionare **OK**.

   ![Screenshot del riquadro "nuovo progetto" per immettere il nome, la posizione e la destinazione del progetto di migrazione.](./media/mysql-to-sql-database-guide/new-project.png)

1. Selezionare la scheda **Connetti a MySQL** e quindi specificare i dettagli per la connessione al server MySQL. 

   ![Screenshot del riquadro "Connetti a MySQL" per specificare le connessioni all'origine.](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. Nel riquadro **MySQL Metadata Explorer** fare clic con il pulsante destro del mouse sullo schema MySQL, quindi scegliere **Crea report**. In alternativa, è possibile selezionare la scheda **Crea report** in alto a destra.

   ![Screenshot dei collegamenti per la creazione di report in SSMA per MySQL.](./media/mysql-to-sql-database-guide/create-report.png)

1. Esaminare il report HTML per comprendere le statistiche di conversione, gli errori e gli avvisi. Analizzarlo per comprendere i problemi di conversione e le risoluzioni. 
   È anche possibile aprire il report in Excel per ottenere un inventario degli oggetti MySQL e comprendere il lavoro richiesto per eseguire le conversioni dello schema. La posizione predefinita del report è la cartella report all'interno di SSMAProjects. Ad esempio: 
   
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Screenshot di un report di conversione di esempio in SSMA.](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-the-data-types"></a>Convalidare i tipi di dati

Verificare i mapping dei tipi di dati predefiniti e modificarli in base ai requisiti, se necessario. A tale scopo, procedere nel seguente modo: 

1. Selezionare **strumenti**, quindi selezionare **Impostazioni progetto**.  
1. Selezionare la scheda **mapping dei tipi** . 

   ![Screenshot del riquadro "mapping dei tipi" in SSMA per MySQL.](./media/mysql-to-sql-database-guide/type-mappings.png)

1. È possibile modificare il mapping dei tipi per ogni tabella selezionando il nome della tabella nel riquadro di **Esplora metadati di MySQL** . 

### <a name="convert-the-schema"></a>Convertire lo schema 

Per convertire lo schema, procedere come segue: 

1. Opzionale Per convertire query dinamiche o specializzate, fare clic con il pulsante destro del mouse sul nodo, quindi scegliere **Aggiungi istruzione**. 

1. Selezionare la scheda **Connetti a database SQL di Azure** e quindi eseguire le operazioni seguenti:

   a. Immettere i dettagli per la connessione al database SQL.  
   b. Nell'elenco a discesa selezionare il database SQL di destinazione. In alternativa, è possibile specificare un nuovo nome, nel qual caso verrà creato un database nel server di destinazione.  
   c. Fornire i dettagli di autenticazione.  
   d. Selezionare **Connetti**.

   ![Screenshot del riquadro "Connetti al database SQL di Azure" in SSMA per MySQL.](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Fare clic con il pulsante destro del mouse sullo schema che si sta utilizzando, quindi scegliere **Converti schema**. In alternativa, è possibile selezionare la scheda **Converti schema** in alto a destra.

   ![Screenshot del comando "Convert schema" nel riquadro "MySQL Metadata Explorer".](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Al termine della conversione, esaminare e confrontare gli oggetti convertiti con quelli originali per identificare i potenziali problemi e risolverli in base alle indicazioni. 

   ![Screenshot che mostra un confronto tra gli oggetti convertiti e gli oggetti originali.](./media/mysql-to-sql-database-guide/table-comparison.png)

   Confrontare il testo Transact-SQL convertito con il codice originale ed esaminare le indicazioni.

   ![Screenshot che mostra un confronto tra le query convertite e il codice sorgente.](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. Nel riquadro **output** selezionare **Verifica risultati**, quindi esaminare gli eventuali errori nel riquadro **Elenco errori** . 
1. Salvare il progetto in locale per un esercizio di correzione dello schema offline. A tale scopo, selezionare **file**  >  **Salva progetto**. In questo modo è possibile valutare gli schemi di origine e di destinazione offline ed eseguire la correzione prima di pubblicare lo schema nel database SQL.

   Confrontare le procedure convertite con le procedure originali, come illustrato di seguito: 

   ![Screenshot che mostra un confronto tra le procedure convertite e le procedure originali.](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate-the-databases"></a>Eseguire la migrazione dei database 

Dopo aver valutato i database e risolto eventuali discrepanze, è possibile eseguire il processo di migrazione. La migrazione prevede due passaggi: la pubblicazione dello schema e la migrazione dei dati. 

Per pubblicare lo schema ed eseguire la migrazione dei dati, eseguire le operazioni seguenti: 

1. Pubblicare lo schema. Nel riquadro **Esplora metadati del database SQL di Azure** , fare clic con il pulsante destro del mouse sul database, quindi scegliere **Sincronizza con database**. Questa azione pubblica lo schema MySQL nel database SQL.

   ![Screenshot del riquadro "Sincronizza con il database" per la revisione del mapping del database.](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Eseguire la migrazione dei dati. Nel riquadro **MySQL Metadata Explorer** fare clic con il pulsante destro del mouse sullo schema MySQL di cui si vuole eseguire la migrazione e quindi selezionare **Migrate data**. In alternativa, è possibile selezionare la scheda **migrazione dati** in alto a destra.

   Per eseguire la migrazione dei dati per un intero database, selezionare la casella di controllo accanto al nome del database. Per eseguire la migrazione dei dati da singole tabelle, espandere il database, espandere **tabelle**, quindi selezionare la casella di controllo accanto alla tabella. Per omettere i dati dalle singole tabelle, deselezionare la casella di controllo.

   ![Screenshot del comando "Migrate data" nel riquadro "MySQL Metadata Explorer".](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Al termine della migrazione, visualizzare il **report di migrazione dei dati**.
   
   ![Screenshot del report di migrazione dei dati.](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Connettersi al database SQL utilizzando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e convalidare la migrazione riesaminando i dati e lo schema.

   ![Screenshot del SQL Server Management Studio.](./media/mysql-to-sql-database-guide/validate-in-ssms.png)

## <a name="post-migration"></a>Post-migrazione 

Dopo aver completato la fase di *migrazione* , è necessario completare una serie di attività post-migrazione per assicurarsi che tutto funzioni correttamente nel modo più semplice ed efficiente possibile.

### <a name="remediate-applications"></a>Correggere le applicazioni

Dopo la migrazione dei dati nell'ambiente di destinazione, tutte le applicazioni che in precedenza usavano l'origine devono iniziare a usare la destinazione. Per ottenere questo risultato, in alcuni casi sarà necessario apportare modifiche alle applicazioni.

### <a name="perform-tests"></a>Eseguire test

L'approccio di test alla migrazione del database è costituito dalle attività seguenti:

1. **Sviluppare i test di convalida**: per testare la migrazione del database, è necessario usare le query SQL. È necessario creare le query di convalida per l'esecuzione nei database di origine e di destinazione. Le query di convalida devono coprire l'ambito definito.

1. **Configurare un ambiente di test**: l'ambiente di test deve contenere una copia del database di origine e del database di destinazione. Assicurarsi di isolare l'ambiente di test.

1. **Eseguire i test di convalida**: eseguire i test di convalida sull'origine e sulla destinazione, quindi analizzare i risultati.

1. **Eseguire test delle prestazioni**: eseguire test delle prestazioni sull'origine e sulla destinazione, quindi analizzare e confrontare i risultati.

### <a name="optimize"></a>Ottimizzazione

La fase post-migrazione è fondamentale per riconciliare eventuali problemi di accuratezza dei dati, verificare la completezza e risolvere i problemi relativi alle prestazioni con il carico di lavoro.

Per ulteriori informazioni su questi problemi e sui passaggi per attenuarli, vedere la [Guida alla convalida e all'ottimizzazione post-migrazione](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Risorse per la migrazione

Per ulteriori informazioni sul completamento di questo scenario di migrazione, vedere la seguente risorsa. È stata sviluppata in supporto di un impegno di progetto di migrazione reale.

| Titolo | Descrizione |
| --- | --- |
| [Strumento e modello di valutazione dei carichi di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Fornisce le piattaforme di destinazione "Best Fit" suggerite, la preparazione per il cloud e i livelli di monitoraggio e aggiornamento dell'applicazione/database per i carichi di lavoro specificati. Offre un semplice calcolo con un solo clic e una generazione di report che consente di accelerare le valutazioni di grandi dimensioni fornendo un processo decisionale automatico e uniforme per la piattaforma di destinazione. |

Le risorse sono state sviluppate dal team di progettazione di SQL Data. La carta di base di questo team consente di sbloccare e accelerare la modernizzazione complessa per i progetti di migrazione della piattaforma dati alla piattaforma dati di Microsoft Azure.

## <a name="next-steps"></a>Passaggi successivi 

- Per stimare i risparmi che è possibile ottenere grazie alla migrazione dei carichi di lavoro in Azure, vedere il [calcolatore costo totale di proprietà di Azure](https://aka.ms/azure-tco).

- Per una matrice di servizi e strumenti di Microsoft e di terze parti disponibili per agevolare diversi scenari di migrazione di database e dati e attività speciali, vedere [servizio e strumenti per la migrazione dei dati](../../../dms/dms-tools-matrix.md).

- Per altre guide alla migrazione, vedere [Guida alla migrazione del database di Azure](https://datamigration.microsoft.com/). 

- Per i video sulla migrazione, vedere [la panoramica del percorso di migrazione e gli strumenti e i servizi consigliati per la migrazione e la valutazione](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).

- Per altre [risorse di migrazione cloud](https://azure.microsoft.com/migration/resources/), vedere soluzioni per la [migrazione](https://azure.microsoft.com/migration)cloud.

