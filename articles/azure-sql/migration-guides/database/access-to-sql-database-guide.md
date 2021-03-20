---
title: 'Accesso al database SQL di Azure: Guida alla migrazione'
description: Questa guida illustra come eseguire la migrazione dei database di Microsoft Access al database SQL di Azure usando SQL Server Migration Assistant per l'accesso (SSMA per l'accesso).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: e323b1c15d78da4e8c1a82ae8848df7f59b0dd87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657274"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Guida alla migrazione: accesso al database SQL di Azure

Questa guida alla migrazione illustra come eseguire la migrazione dei database di Microsoft Access al database SQL di Azure usando il SQL Server Migration Assistant per l'accesso.

Per altre guide alla migrazione, vedere [Migrazione dei database](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Prerequisiti

Per eseguire la migrazione del database di Access al database SQL di Azure, è necessario quanto segue:

- Verificare che l'ambiente di origine sia supportato. 
- [SQL Server Migration Assistant per l'accesso](https://www.microsoft.com/download/details.aspx?id=54255). 

## <a name="pre-migration"></a>Pre-migrazione

Una volta soddisfatti i prerequisiti, si è pronti per individuare la topologia dell'ambiente e valutare la fattibilità della migrazione.


### <a name="assess"></a>Valutare 

Creare una valutazione usando [SQL Server Migration Assistant per l'accesso](https://www.microsoft.com/download/details.aspx?id=54255). 

Per creare una valutazione, seguire questa procedura: 

1. Aprire SQL Server Migration Assistant per l'accesso. 
1. Selezionare **File** e quindi scegliere **Nuovo progetto**. Consente di specificare un nome per il progetto di migrazione. 

   ![Scegli nuovo progetto](./media/access-to-sql-database-guide/new-project.png)

1. Selezionare **Aggiungi database** e scegliere i database da aggiungere al nuovo progetto. 

   ![Scegliere Aggiungi database](./media/access-to-sql-database-guide/add-databases.png)

1. In **Esplora metadati di Access** fare clic con il pulsante destro del mouse sul database, quindi scegliere **Crea report**. 

   ![Fare clic con il pulsante destro del mouse sul database e scegliere Crea report](./media/access-to-sql-database-guide/create-report.png)

1. Esaminare la valutazione di esempio. Ad esempio: 

   ![Esaminare la valutazione dei report di esempio](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-data-types"></a>Convalidare i tipi di dati

Convalidare i mapping dei tipi di dati predefiniti e modificarli in base ai requisiti, se necessario. A questo scopo, attenersi alla procedura seguente:

1. Selezionare **Tools** (Strumenti) dal menu. 
1. Selezionare **Project Settings** (Impostazioni progetto). 
1. Selezionare la scheda **Type mappings** (Mapping tipi). 

   ![Mapping dei tipi](./media/access-to-sql-database-guide/type-mappings.png)

1. È possibile modificare il mapping dei tipi per ogni tabella selezionando la tabella in **Esplora metadati di Access**.


### <a name="convert-schema"></a>Converti schema

Per convertire gli oggetti di database, attenersi alla seguente procedura: 

1. Selezionare **Connetti al database SQL di Azure** e specificare i dettagli della connessione.

   ![Connettersi al database SQL di Azure](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. Fare clic con il pulsante destro del mouse sul database in **Accedi a Esplora metadati** e scegliere **Converti schema**. In alternativa, è possibile scegliere **Converti schema** dalla barra di spostamento superiore dopo aver selezionato il database.

   ![Fare clic con il pulsante destro del mouse sul database e scegliere Converti schema](./media/access-to-sql-database-guide/convert-schema.png)

   Confrontare le query convertite con le query originali: 

   ![Le query convertite possono essere confrontate con il codice sorgente](./media/access-to-sql-database-guide/query-comparison.png)

   Confrontare gli oggetti convertiti con quelli originali: 

   ![Gli oggetti convertiti possono essere confrontati con l'origine](./media/access-to-sql-database-guide/table-comparison.png)

1. Opzionale Per convertire un singolo oggetto, fare clic con il pulsante destro del mouse sull'oggetto e scegliere **Converti schema**. Gli oggetti convertiti vengono visualizzati in grassetto in **Esplora metadati di accesso**: 

   ![Gli oggetti in grassetto in Esplora metadati sono stati convertiti](./media/access-to-sql-database-guide/converted-items.png)
 
1. Selezionare **Verifica risultati** nel riquadro Output ed esaminare gli errori nel riquadro **Elenco errori** . 


## <a name="migrate"></a>Migrazione

Dopo aver completato la valutazione dei database e corretto eventuali discrepanze, il passaggio successivo consiste nell'eseguire il processo di migrazione. La migrazione dei dati è un'operazione di caricamento bulk che sposta le righe di dati nel database SQL di Azure nelle transazioni. Il numero di righe da caricare nel database SQL di Azure in ogni transazione viene configurato nelle impostazioni del progetto.

Per eseguire la migrazione dei dati usando SSMA per l'accesso, seguire questa procedura: 

1. Se non è già stato fatto, selezionare **Connetti al database SQL di Azure** e specificare i dettagli della connessione. 
1. Fare clic con il pulsante destro del mouse sul database da **Esplora metadati del database SQL di Azure** e scegliere **Sincronizza con database**. Questa azione pubblica lo schema MySQL nel database SQL di Azure.

   ![Sincronizza con database](./media/access-to-sql-database-guide/synchronize-with-database.png)

   Esaminare il mapping tra il progetto di origine e la destinazione:

   ![Esaminare la sincronizzazione con il database](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. Utilizzare **Esplora metadati di Access** per selezionare le caselle di controllo accanto agli elementi di cui si desidera eseguire la migrazione. Se si desidera eseguire la migrazione dell'intero database, selezionare la casella accanto al database. 
1. Fare clic con il pulsante destro del mouse sul database o sull'oggetto di cui si desidera eseguire la migrazione e scegliere **Migrate data**. 
   Per eseguire la migrazione dei dati per un intero database, selezionare la casella di controllo accanto al nome del database. Per eseguire la migrazione dei dati da singole tabelle, espandere il database, espandere tabelle, quindi selezionare la casella di controllo accanto alla tabella. Per omettere i dati dalle singole tabelle, deselezionare la casella di controllo.

    ![Migrazione dei dati](./media/access-to-sql-database-guide/migrate-data.png)

    Esaminare i dati migrati: 

    ![Esegui migrazione Revisione dati](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Connettersi al database SQL di Azure usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e convalidare la migrazione riesaminando i dati e lo schema.

   ![Convalida in SSMA](./media/access-to-sql-database-guide/validate-data.png)



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

| **Titolo/collegamento**                                                                                                                                          | **Descrizione**                                                                                                                                                                                                                                                                                                                              |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Strumento e modello di valutazione del carico di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Questo strumento fornisce le piattaforme di destinazione "più idonee" suggerite, la preparazione per il cloud e il livello di monitoraggio e aggiornamento dell'applicazione/database per un determinato carico di lavoro. Offre semplici calcoli con un solo clic e generazione di report che consentono di accelerare le valutazioni di grandi dimensioni grazie a un processo decisionale automatizzato e uniforme della piattaforma di destinazione. |


Queste risorse sono state sviluppate come parte del programma Data SQL Ninja, sponsorizzato dal team di progettazione Azure Data Group. Obiettivo principale del programma Data SQL Ninja è rendere disponibili opportunità per velocizzare progetti di modernizzazione complessi e la migrazione delle piattaforme dati alla piattaforma dati di Microsoft Azure. Se si ritiene che l'organizzazione possa essere interessata a partecipare al programma Data SQL Ninja, contattare il team dell'account per richiedere l'invio di una candidatura.

## <a name="next-steps"></a>Passaggi successivi

- Per una matrice di servizi e strumenti di Microsoft e di terze parti disponibili per supportare diversi scenari di migrazione di database e dati, nonché per le attività speciali, vedere [servizio e strumenti per la migrazione dei dati](../../../dms/dms-tools-matrix.md).

- Per altre informazioni sul database SQL di Azure, vedere:
   - [Panoramica del database SQL](../../database/sql-database-paas-overview.md)
   - [Calcolatore costo totale di proprietà di Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Per ulteriori informazioni sul Framework e il ciclo di adozione per le migrazioni cloud, vedere
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per il costo e il ridimensionamento dei carichi di lavoro migrazione ad Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Per valutare il livello di accesso dell'applicazione, vedere [Data Access Migration Toolkit (anteprima)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Per informazioni dettagliate su come eseguire i test A/B di livello di accesso ai dati, vedere [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).

