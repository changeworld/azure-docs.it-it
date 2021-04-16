---
title: 'Da MySQL a database SQL di Azure: Guida alla migrazione'
description: Questa guida illustra come eseguire la migrazione dei database MySQL a un database Azure SQL usando SQL Server Migration Assistant per MySQL (SSMA per MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: d4510aa5cda61dac88102c89b3e03da231380bd6
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389452"
---
# <a name="migration-guide-mysql-to-azure-sql-database"></a>Guida alla migrazione: Da MySQL a database SQL di Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Questa guida illustra [](https://azure.microsoft.com/migration/migration-journey) come eseguire la migrazione del database MySQL a un database Azure SQL usando [SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant per MySQL (SSMA per MySQL). 

Per altre guide alla migrazione, vedere Guida [alla migrazione del database di Azure](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare la migrazione del database MySQL a un database SQL, eseguire le operazioni seguenti:

- Verificare che l'ambiente di origine sia supportato. Attualmente mySQL 5.6 e 5.7 sono supportati. 
- Scaricare e installare [SQL Server Migration Assistant per MySQL](https://www.microsoft.com/download/details.aspx?id=54257).
- Assicurarsi di avere connettività e autorizzazioni sufficienti per accedere sia all'origine che alla destinazione.

## <a name="pre-migration"></a>Pre-migrazione 

Dopo aver soddisfatto i prerequisiti, è possibile individuare la topologia dell'ambiente e valutare la fattibilità della migrazione [cloud di Azure.](https://azure.microsoft.com/migration)

### <a name="assess"></a>Valutare 

Usare SQL Server Migration Assistant (SSMA) per MySQL per esaminare oggetti e dati di database e valutare i database per la migrazione. 

Per creare una valutazione, eseguire le operazioni seguenti:

1. Aprire [SSMA per MySQL](https://www.microsoft.com/download/details.aspx?id=54257). 
1. Selezionare **File** e quindi Nuovo **progetto.** 
1. Nel riquadro **Nuovo progetto** immettere un nome e un percorso  per il progetto e quindi nell'elenco a discesa Migra a **selezionare** database SQL di Azure . 
1. Selezionare **OK**.

   ![Screenshot del riquadro "Nuovo progetto" per l'immissione del nome, del percorso e della destinazione del progetto di migrazione.](./media/mysql-to-sql-database-guide/new-project.png)

1. Selezionare la **scheda Connetti a MySQL** e quindi specificare i dettagli per la connessione del server MySQL. 

   ![Screenshot del riquadro "Connetti a MySQL" per specificare le connessioni all'origine.](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. Nel riquadro **Esplora metadati MySQL** fare clic con il pulsante destro del mouse sullo schema MySQL e quindi scegliere **Crea report**. In alternativa, è possibile selezionare la **scheda Crea** report in alto a destra.

   ![Screenshot dei collegamenti "Crea report" in SSMA per MySQL.](./media/mysql-to-sql-database-guide/create-report.png)

1. Esaminare il report HTML per comprendere le statistiche di conversione, gli errori e gli avvisi. Analizzarlo per comprendere i problemi di conversione e le soluzioni. 
   È anche possibile aprire il report in Excel per ottenere un inventario degli oggetti MySQL e comprendere il lavoro necessario per eseguire le conversioni dello schema. La posizione predefinita del report è la cartella report all'interno di SSMAProjects. Ad esempio: 
   
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Screenshot di un report di conversione di esempio in SSMA.](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-the-data-types"></a>Convalidare i tipi di dati

Convalidare i mapping dei tipi di dati predefiniti e modificarli in base ai requisiti, se necessario. A tale scopo, procedere nel seguente modo: 

1. Selezionare **Strumenti** e quindi Impostazioni **progetto**.  
1. Selezionare la **scheda Mapping dei** tipi. 

   ![Screenshot del riquadro "Mapping dei tipi" in SSMA per MySQL.](./media/mysql-to-sql-database-guide/type-mappings.png)

1. È possibile modificare il mapping dei tipi per ogni tabella selezionando il nome della tabella nel riquadro Esplora metadati **MySQL.** 

### <a name="convert-the-schema"></a>Convertire lo schema 

Per convertire lo schema, eseguire le operazioni seguenti: 

1. (Facoltativo) Per convertire query dinamiche o specializzate, fare clic con il pulsante destro del mouse sul nodo e quindi scegliere **Aggiungi istruzione**. 

1. Selezionare la **scheda Connetti database SQL di Azure** e quindi eseguire le operazioni seguenti:

   a. Immettere i dettagli per la connessione al database SQL.  
   b. Nell'elenco a discesa selezionare il database SQL di destinazione. In caso contrario, è possibile specificare un nuovo nome, nel qual caso verrà creato un database nel server di destinazione.  
   c. Specificare i dettagli di autenticazione.  
   d. Selezionare **Connetti**.

   ![Screenshot del riquadro "Connetti a database SQL di Azure" in SSMA per MySQL.](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Fare clic con il pulsante destro del mouse sullo schema in uso e quindi **scegliere Converti schema**. In alternativa, è possibile selezionare la **scheda Converti schema** in alto a destra.

   ![Screenshot del comando "Convert Schema" nel riquadro "MySQL Metadata Explorer".](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Al termine della conversione, esaminare e confrontare gli oggetti convertiti con gli oggetti originali per identificare potenziali problemi e risolvere i problemi in base alle raccomandazioni. 

   ![Screenshot che mostra un confronto tra gli oggetti convertiti e gli oggetti originali.](./media/mysql-to-sql-database-guide/table-comparison.png)

   Confrontare il testo Transact-SQL convertito con il codice originale ed esaminare le raccomandazioni.

   ![Screenshot che mostra un confronto tra query convertite e codice sorgente.](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. Nel riquadro **Output** selezionare **Rivedi risultati** e quindi esaminare eventuali errori nel **riquadro Elenco errori.** 
1. Salvare il progetto in locale per un esercizio di correzione dello schema offline. A tale scopo, selezionare **File**  >  **Save Project (Salva progetto).** In questo modo è possibile valutare gli schemi di origine e di destinazione offline ed eseguire la correzione prima di pubblicare lo schema nel database SQL.

   Confrontare le procedure convertite con le procedure originali, come illustrato di seguito: 

   ![Screenshot che mostra un confronto tra le procedure convertite e le procedure originali.](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate-the-databases"></a>Eseguire la migrazione dei database 

Dopo aver valutato i database e aver corretto eventuali discrepanze, è possibile eseguire il processo di migrazione. La migrazione prevede due passaggi: la pubblicazione dello schema e la migrazione dei dati. 

Per pubblicare lo schema ed eseguire la migrazione dei dati, eseguire le operazioni seguenti: 

1. Pubblicare lo schema. Nel riquadro **database SQL di Azure Visualizzatore metadati** fare clic con il pulsante destro del mouse sul database e quindi scegliere **Sincronizza con database**. Questa azione pubblica lo schema MySQL nel database SQL.

   ![Screenshot del riquadro "Sincronizza con il database" per esaminare il mapping del database.](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Eseguire la migrazione dei dati. Nel riquadro **Esplora metadati MySQL** fare clic con il pulsante destro del mouse sullo schema MySQL di cui si vuole eseguire la migrazione e quindi scegliere Esegui migrazione **dati**. In alternativa, è possibile selezionare la **scheda Esegui** migrazione dati in alto a destra.

   Per eseguire la migrazione dei dati per un intero database, selezionare la casella di controllo accanto al nome del database. Per eseguire la migrazione dei dati da singole tabelle, espandere il database, espandere **Tabelle** e quindi selezionare la casella di controllo accanto alla tabella. Per omettere dati da singole tabelle, deselezionare la casella di controllo .

   ![Screenshot del comando "Migrate Data" nel riquadro "MySQL Metadata Explorer".](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Al termine della migrazione, visualizzare il **report di migrazione dei dati**.
   
   ![Screenshot del report di migrazione dei dati.](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Connettersi al database SQL [usando](/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio convalidare la migrazione esaminando i dati e lo schema.

   ![Screenshot della SQL Server Management Studio.](./media/mysql-to-sql-database-guide/validate-in-ssms.png)

## <a name="post-migration"></a>Post-migrazione 

Dopo aver completato la  fase di migrazione, è necessario completare una serie di attività post-migrazione per assicurarsi che tutto funzioni nel modo più uniforme ed efficiente possibile.

### <a name="remediate-applications"></a>Correggere le applicazioni

Dopo la migrazione dei dati nell'ambiente di destinazione, tutte le applicazioni che in precedenza usavano l'origine devono iniziare a usare la destinazione. Per ottenere questo risultato, in alcuni casi sarà necessario apportare modifiche alle applicazioni.

### <a name="perform-tests"></a>Eseguire test

L'approccio di test alla migrazione del database è costituito dalle attività seguenti:

1. **Sviluppare test di convalida:** per testare la migrazione del database, è necessario usare query SQL. È necessario creare le query di convalida da eseguire nei database di origine e di destinazione. Le query di convalida devono coprire l'ambito definito.

1. **Configurare un ambiente di test:** l'ambiente di test deve contenere una copia del database di origine e del database di destinazione. Assicurarsi di isolare l'ambiente di test.

1. **Eseguire test di convalida:** eseguire test di convalida sull'origine e sulla destinazione e quindi analizzare i risultati.

1. **Eseguire test delle prestazioni:** eseguire test delle prestazioni rispetto all'origine e alla destinazione, quindi analizzare e confrontare i risultati.

### <a name="optimize"></a>Ottimizzazione

La fase post-migrazione è fondamentale per la risoluzione di eventuali problemi di accuratezza dei dati, la verifica della completezza e la risoluzione dei problemi di prestazioni con il carico di lavoro.

Per altre informazioni su questi problemi e sui passaggi per attenuarli, vedere la Guida alla convalida e all'ottimizzazione [post-migrazione](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Risorse per la migrazione

Per altre informazioni sul completamento di questo scenario di migrazione, vedere la risorsa seguente. È stato sviluppato a supporto di un impegno di progetto di migrazione reale.

| Titolo | Descrizione |
| --- | --- |
| [Strumento e modello di valutazione dei carichi di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Offre piattaforme di destinazione "best fit" suggerite, conformità al cloud e livelli di correzione di applicazioni/database per carichi di lavoro specificati. Offre una semplice generazione di report e calcolo con un solo clic che consente di accelerare le valutazioni di proprietà di grandi dimensioni fornendo un processo decisionale automatizzato e uniforme della piattaforma di destinazione. |

Il team di data sql engineering ha sviluppato queste risorse. Lo scopo principale di questo team è sbloccare e accelerare la modernizzazione complessa per i progetti di migrazione della piattaforma dati alla piattaforma dati di Azure di Microsoft.

## <a name="next-steps"></a>Passaggi successivi 

- Per stimare i risparmi sui costi che è possibile realizzare eseguendo la migrazione dei carichi di lavoro ad Azure, vedere il calcolatore del costo [totale di proprietà di Azure.](https://aka.ms/azure-tco)

- Per una matrice di servizi e strumenti Microsoft e di terze parti disponibili per facilitare vari scenari di migrazione di database e dati e attività speciali, vedere Service and tools for data migration ( Servizio e strumenti per la migrazione [dei dati).](../../../dms/dms-tools-matrix.md)

- Per altre guide alla migrazione, vedere Guida [alla migrazione del database di Azure](https://datamigration.microsoft.com/). 

- Per i video sulla migrazione, vedere Panoramica del percorso di migrazione e degli strumenti e dei servizi consigliati [per la migrazione e la valutazione.](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)

- Per altre risorse [di migrazione cloud,](https://azure.microsoft.com/migration/resources/)vedere [Soluzioni di migrazione cloud.](https://azure.microsoft.com/migration)

