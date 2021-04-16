---
title: 'Accesso a database SQL di Azure: Guida alla migrazione'
description: Questa guida illustra come eseguire la migrazione dei database di Microsoft Access a un database Azure SQL usando SQL Server Migration Assistant for Access (SSMA per Access).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 137adbb045a4c449193f9029b9c72f09ddc439b1
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388466"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Guida alla migrazione: Accesso a database SQL di Azure

Questa guida illustra [](https://azure.microsoft.com/migration/migration-journey) come eseguire la migrazione del database di Microsoft Access a un database Azure SQL usando [SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant for Access (SSMA per Access).

Per altre guide alla migrazione, vedere Guida [alla migrazione del database di Azure](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare la migrazione del database di Access a un database SQL, eseguire le operazioni seguenti:

- Verificare che l'ambiente di origine sia supportato. 
- Scaricare e installare SQL Server Migration Assistant [per Access](https://www.microsoft.com/download/details.aspx?id=54255).
- Assicurarsi di avere connettività e autorizzazioni sufficienti per accedere sia all'origine che alla destinazione.

## <a name="pre-migration"></a>Pre-migrazione

Dopo aver soddisfatto i prerequisiti, è possibile individuare la topologia dell'ambiente e valutare la fattibilità della migrazione [cloud di Azure.](https://azure.microsoft.com/migration)


### <a name="assess"></a>Valutare 

Usare SSMA per Access per esaminare oggetti e dati di database e valutare i database per la migrazione. 

Per creare una valutazione, eseguire le operazioni seguenti: 

1. Aprire [SSMA per l'accesso](https://www.microsoft.com/download/details.aspx?id=54255). 
1. Selezionare **File** e quindi Nuovo **progetto.** 
1. Specificare un nome di progetto e un percorso per il progetto e quindi, nell'elenco a discesa, **selezionare database SQL di Azure** come destinazione della migrazione. 
1. Selezionare **OK**. 

   ![Screenshot del riquadro "Nuovo progetto" per l'immissione del nome e del percorso del progetto di migrazione.](./media/access-to-sql-database-guide/new-project.png)

1. Selezionare **Aggiungi database** e quindi selezionare i database da aggiungere al nuovo progetto. 

   ![Screenshot della scheda "Aggiungi database" in SSMA per l'accesso.](./media/access-to-sql-database-guide/add-databases.png)

1. Nel riquadro **Esplora metadati di Accesso** fare clic con il pulsante destro del mouse su un database e quindi scegliere Crea **report**. In alternativa, è possibile selezionare la **scheda Crea** report in alto a destra.

   ![Screenshot del comando "Crea report" in Esplora metadati di Access.](./media/access-to-sql-database-guide/create-report.png)

1. Esaminare il report HTML per comprendere le statistiche di conversione e gli eventuali errori o avvisi. È anche possibile aprire il report in Excel per ottenere un inventario degli oggetti di Access e comprendere il lavoro richiesto per eseguire le conversioni dello schema. La posizione predefinita del report è la cartella report all'interno di SSMAProjects. Ad esempio:

   `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![Screenshot di una valutazione del report di database di esempio in SSMA.](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-the-data-types"></a>Convalidare i tipi di dati

Convalidare i mapping dei tipi di dati predefiniti e modificarli in base ai requisiti, se necessario. A tale scopo, procedere nel seguente modo:

1. In SSMA per Access selezionare **Strumenti** e quindi Selezionare **Impostazioni progetto.** 
1. Selezionare la **scheda Mapping dei** tipi. 

   ![Screenshot del riquadro "Mapping dei tipi" in SSMA per l'accesso.](./media/access-to-sql-database-guide/type-mappings.png)

1. È possibile modificare il mapping dei tipi per ogni tabella selezionando il nome della tabella nel **riquadro Esplora metadati di** accesso.


### <a name="convert-the-schema"></a>Convertire lo schema

Per convertire gli oggetti di database, eseguire le operazioni seguenti: 

1. Selezionare la **scheda Connetti database SQL di Azure** e quindi eseguire le operazioni seguenti:

   a. Immettere i dettagli per la connessione al database SQL.  
   b. Nell'elenco a discesa selezionare il database SQL di destinazione. Oppure è possibile immettere un nuovo nome, nel qual caso verrà creato un database nel server di destinazione.  
   c. Specificare i dettagli di autenticazione.   
   d. Selezionare **Connetti**.

   ![Screenshot del riquadro "Connetti a database SQL di Azure" per l'immissione dei dettagli della connessione.](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. Nel riquadro **Esplora metadati di accesso** fare clic con il pulsante destro del mouse sul database e quindi scegliere Converti **schema.** In alternativa, è possibile selezionare il database e quindi selezionare la **scheda Converti** schema.

   ![Screenshot del comando "Converti schema" nel riquadro "Esplora metadati di accesso".](./media/access-to-sql-database-guide/convert-schema.png)

1. Al termine della conversione, confrontare gli oggetti convertiti con gli oggetti originali per identificare potenziali problemi e risolvere i problemi in base alle raccomandazioni.

   ![Screenshot che mostra un confronto tra gli oggetti convertiti e gli oggetti di origine.](./media/access-to-sql-database-guide/table-comparison.png)

    Confrontare il testo Transact-SQL convertito con il codice originale ed esaminare le raccomandazioni.

   ![Screenshot che mostra un confronto tra query convertite e codice sorgente.](./media/access-to-sql-database-guide/query-comparison.png) 

1. (Facoltativo) Per convertire un singolo oggetto, fare clic con il pulsante destro del mouse sull'oggetto e quindi **scegliere Converti schema**. Gli oggetti convertiti vengono visualizzati in grassetto in **Esplora metadati di Access:** 

   ![Screenshot che mostra la conversione degli oggetti in Esplora metadati di Access.](./media/access-to-sql-database-guide/converted-items.png)
 
1. Nel riquadro **Output** selezionare l'icona **Rivedi** risultati ed esaminare gli errori nel **riquadro Elenco** errori. 
1. Salvare il progetto in locale per un esercizio di correzione dello schema offline. A tale scopo, selezionare **File**  >  **Save Project (Salva progetto).** In questo modo è possibile valutare gli schemi di origine e di destinazione offline ed eseguire la correzione prima di pubblicarli nel database SQL.

## <a name="migrate-the-databases"></a>Eseguire la migrazione dei database

Dopo aver valutato i database e aver corretto eventuali discrepanze, è possibile eseguire il processo di migrazione. La migrazione dei dati è un'operazione di caricamento bulk che sposta righe di dati in un database Azure SQL nelle transazioni. Il numero di righe da caricare nel database SQL in ogni transazione viene configurato nelle impostazioni del progetto.

Per pubblicare lo schema ed eseguire la migrazione dei dati usando SSMA per Access, eseguire le operazioni seguenti: 

1. Se non è già stato fatto, **selezionare** Connetti a database SQL di Azure e specificare i dettagli della connessione. 

1. Pubblicare lo schema. Nel riquadro **database SQL di Azure Visualizzatore metadati** fare clic con il pulsante destro del mouse sul database in uso e quindi scegliere **Sincronizza con database**. Questa azione pubblica lo schema MySQL nel database SQL.

1. Nel riquadro **Sincronizza con il database** esaminare il mapping tra il progetto di origine e la destinazione:

   ![Screenshot del riquadro "Sincronizza con il database" per esaminare la sincronizzazione con il database.](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. Nel riquadro **Esplora metadati di** accesso selezionare le caselle di controllo accanto agli elementi di cui si vuole eseguire la migrazione. Per eseguire la migrazione dell'intero database, selezionare la casella di controllo accanto al database. 

1. Eseguire la migrazione dei dati. Fare clic con il pulsante destro del mouse sul database o sull'oggetto di cui si vuole eseguire la migrazione e quindi scegliere **Esegui migrazione dati**. In alternativa, è possibile selezionare la **scheda Esegui** migrazione dati in alto a destra.  

   Per eseguire la migrazione dei dati per un intero database, selezionare la casella di controllo accanto al nome del database. Per eseguire la migrazione dei dati da singole tabelle, espandere il database, espandere **Tabelle** e quindi selezionare la casella di controllo accanto alla tabella. Per omettere dati da singole tabelle, deselezionare la casella di controllo .

    ![Screenshot del comando "Migrate Data" nel riquadro "Esplora metadati di accesso".](./media/access-to-sql-database-guide/migrate-data.png)

1. Al termine della migrazione, visualizzare il **report di migrazione dei dati**.  

    ![Screenshot del riquadro "Migrate Data Report" che mostra un report di esempio per la revisione.](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Connettersi al database Azure SQL usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)e convalidare la migrazione esaminando i dati e lo schema.

   ![Screenshot dell SQL Server Management Studio Esplora oggetti per convalidare la migrazione in SSMA.](./media/access-to-sql-database-guide/validate-data.png)

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
| [Strumento e modello di valutazione dei carichi di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Offre piattaforme di destinazione "best fit" suggerite, conformità al cloud e livelli di correzione di applicazioni/database per carichi di lavoro specificati. Offre una semplice generazione di report e calcolo con un solo clic che consente di accelerare le valutazioni di proprietà di grandi dimensioni offrendo un processo decisionale automatizzato e uniforme per la piattaforma di destinazione. |

Il team di data sql engineering ha sviluppato queste risorse. Lo scopo principale di questo team è sbloccare e accelerare la modernizzazione complessa per i progetti di migrazione della piattaforma dati alla piattaforma dati di Azure di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

- Per una matrice di servizi e strumenti Microsoft e di terze parti disponibili per facilitare vari scenari di migrazione di database e dati e attività speciali, vedere Service and tools for data migration (Servizi e strumenti per la migrazione [dei dati).](../../../dms/dms-tools-matrix.md)

- Per altre informazioni sul database SQL di Azure, vedere:
   - [Panoramica del database SQL](../../database/sql-database-paas-overview.md)
   - [Calcolatore del costo totale di proprietà di Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Per altre informazioni sul framework e sul ciclo di adozione per le migrazioni cloud, vedere:
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per la determinazione dei costi e il dimensionamento dei carichi di lavoro per la migrazione ad Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Migrazione cloud risorse](https://azure.microsoft.com/migration/resources)


- Per valutare il livello di accesso all'applicazione, [vedere Data Access Migration Toolkit (anteprima)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Per informazioni su come eseguire test di livello A/B di accesso ai dati, vedere [Panoramica di Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
