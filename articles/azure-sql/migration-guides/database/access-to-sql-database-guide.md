---
title: 'Accesso al database SQL di Azure: Guida alla migrazione'
description: Questa guida illustra come eseguire la migrazione dei database di Microsoft Access a un database SQL di Azure usando SQL Server Migration Assistant per l'accesso (SSMA per l'accesso).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 48fe734b382d661f96a86ede181a1258e38120a1
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626538"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Guida alla migrazione: accesso al database SQL di Azure

Questa guida illustra come eseguire la migrazione del database di Microsoft Access a un database SQL di Azure usando SQL Server Migration Assistant per l'accesso (SSMA per l'accesso).

Per altre guide alla migrazione, vedere [Guida alla migrazione del database di Azure](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare la migrazione del database di Access a un database SQL, eseguire le operazioni seguenti:

- Verificare che l'ambiente di origine sia supportato. 
- Scaricare e installare [SQL Server Migration Assistant per l'accesso](https://www.microsoft.com/download/details.aspx?id=54255).
- Assicurarsi di disporre di connettività e di autorizzazioni sufficienti per accedere sia all'origine che alla destinazione.

## <a name="pre-migration"></a>Pre-migrazione

Dopo aver soddisfatto i prerequisiti, si è pronti per individuare la topologia dell'ambiente e valutare la fattibilità della migrazione.


### <a name="assess"></a>Valutare 

Usare SSMA per l'accesso per esaminare i dati e gli oggetti di database e valutare i database per la migrazione. 

Per creare una valutazione, procedere come segue: 

1. Aprire [SSMA per Access](https://www.microsoft.com/download/details.aspx?id=54255). 
1. Selezionare **file**, quindi fare clic su **nuovo progetto**. 
1. Specificare un nome di progetto e un percorso per il progetto e quindi nell'elenco a discesa selezionare **database SQL di Azure** come destinazione della migrazione. 
1. Selezionare **OK**. 

   ![Screenshot del riquadro "nuovo progetto" per immettere il nome e il percorso del progetto di migrazione.](./media/access-to-sql-database-guide/new-project.png)

1. Selezionare **Aggiungi database**, quindi selezionare i database da aggiungere al nuovo progetto. 

   ![Screenshot della scheda "Aggiungi database" in SSMA per l'accesso.](./media/access-to-sql-database-guide/add-databases.png)

1. Nel riquadro **Access Metadata Explorer** fare clic con il pulsante destro del mouse su un database e quindi scegliere **Crea report**. In alternativa, è possibile selezionare la scheda **Crea report** in alto a destra.

   ![Screenshot del comando "Crea report" in Esplora metadati di Access.](./media/access-to-sql-database-guide/create-report.png)

1. Esaminare il report HTML per comprendere le statistiche di conversione e gli eventuali errori o avvisi. È inoltre possibile aprire il report in Excel per ottenere un inventario degli oggetti di accesso e comprendere lo sforzo necessario per eseguire le conversioni dello schema. La posizione predefinita del report è la cartella report all'interno di SSMAProjects. Ad esempio:

   `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![Screenshot di un esempio di valutazione del report del database in SSMA.](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-the-data-types"></a>Convalidare i tipi di dati

Verificare i mapping dei tipi di dati predefiniti e modificarli in base ai requisiti, se necessario. A tale scopo, procedere nel seguente modo:

1. In SSMA per Access selezionare **strumenti**, quindi selezionare **Impostazioni progetto**. 
1. Selezionare la scheda **mapping dei tipi** . 

   ![Screenshot del riquadro "mapping dei tipi" in SSMA per l'accesso.](./media/access-to-sql-database-guide/type-mappings.png)

1. È possibile modificare il mapping dei tipi per ogni tabella selezionando il nome della tabella nel riquadro di **esplorazione dei metadati di accesso** .


### <a name="convert-the-schema"></a>Convertire lo schema

Per convertire gli oggetti di database, eseguire le operazioni seguenti: 

1. Selezionare la scheda **Connetti a database SQL di Azure** e quindi eseguire le operazioni seguenti:

   a. Immettere i dettagli per la connessione al database SQL.  
   b. Nell'elenco a discesa selezionare il database SQL di destinazione. In alternativa, è possibile immettere un nuovo nome, nel qual caso verrà creato un database nel server di destinazione.  
   c. Fornire i dettagli di autenticazione.   
   d. Selezionare **Connetti**.

   ![Screenshot del riquadro "Connetti al database SQL di Azure" per l'immissione dei dettagli della connessione.](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. Nel riquadro **Accedi a Esplora metadati** fare clic con il pulsante destro del mouse sul database e quindi scegliere **Converti schema**. In alternativa, è possibile selezionare il database e quindi selezionare la scheda **Converti schema** .

   ![Screenshot del comando "Converti schema" nel riquadro "accedi a Esplora metadati".](./media/access-to-sql-database-guide/convert-schema.png)

1. Al termine della conversione, confrontare gli oggetti convertiti con quelli originali per identificare potenziali problemi e risolvere i problemi in base alle raccomandazioni.

   ![Screenshot che mostra un confronto tra gli oggetti convertiti e gli oggetti di origine.](./media/access-to-sql-database-guide/table-comparison.png)

    Confrontare il testo Transact-SQL convertito con il codice originale ed esaminare le indicazioni.

   ![Screenshot che mostra un confronto tra le query convertite e il codice sorgente.](./media/access-to-sql-database-guide/query-comparison.png) 

1. Opzionale Per convertire un singolo oggetto, fare clic con il pulsante destro del mouse sull'oggetto, quindi scegliere **Converti schema**. Gli oggetti convertiti vengono visualizzati in grassetto in **Esplora metadati di Access**: 

   ![Screenshot che mostra la conversione degli oggetti in Esplora metadati di Access.](./media/access-to-sql-database-guide/converted-items.png)
 
1. Nel riquadro **output** selezionare l'icona **Verifica risultati** ed esaminare gli errori nel riquadro **Elenco errori** . 
1. Salvare il progetto in locale per un esercizio di correzione dello schema offline. A tale scopo, selezionare **file**  >  **Salva progetto**. In questo modo è possibile valutare gli schemi di origine e di destinazione offline ed eseguire la correzione prima di pubblicarli nel database SQL.

## <a name="migrate-the-databases"></a>Eseguire la migrazione dei database

Dopo aver valutato i database e risolto eventuali discrepanze, è possibile eseguire il processo di migrazione. La migrazione dei dati è un'operazione di caricamento bulk che sposta le righe di dati in un database SQL di Azure nelle transazioni. Il numero di righe da caricare nel database SQL in ogni transazione viene configurato nelle impostazioni del progetto.

Per pubblicare lo schema ed eseguire la migrazione dei dati usando SSMA per l'accesso, seguire questa procedura: 

1. Se non è già stato fatto, selezionare **Connetti al database SQL di Azure** e specificare i dettagli della connessione. 

1. Pubblicare lo schema. Nel riquadro **Esplora metadati del database SQL di Azure** , fare clic con il pulsante destro del mouse sul database che si sta utilizzando, quindi selezionare **Sincronizza con database**. Questa azione pubblica lo schema MySQL nel database SQL.

1. Nel riquadro **Sincronizza con il database** esaminare il mapping tra il progetto di origine e la destinazione:

   ![Screenshot del riquadro "Sincronizza con il database" per la revisione della sincronizzazione con il database.](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. Nel riquadro **Access Metadata Explorer** selezionare le caselle di controllo accanto agli elementi di cui si vuole eseguire la migrazione. Per eseguire la migrazione dell'intero database, selezionare la casella di controllo accanto al database. 

1. Eseguire la migrazione dei dati. Fare clic con il pulsante destro del mouse sul database o sull'oggetto di cui si desidera eseguire la migrazione, quindi selezionare **Migrate data**. In alternativa, è possibile selezionare la scheda **migrazione dati** in alto a destra.  

   Per eseguire la migrazione dei dati per un intero database, selezionare la casella di controllo accanto al nome del database. Per eseguire la migrazione dei dati da singole tabelle, espandere il database, espandere **tabelle**, quindi selezionare la casella di controllo accanto alla tabella. Per omettere i dati dalle singole tabelle, deselezionare la casella di controllo.

    ![Screenshot del comando "migrare i dati" nel riquadro "accedi a Esplora metadati".](./media/access-to-sql-database-guide/migrate-data.png)

1. Al termine della migrazione, visualizzare il **report di migrazione dei dati**.  

    ![Screenshot del riquadro "migrazione report dati" che mostra un report di esempio per la revisione.](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Connettersi al database SQL di Azure usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)e convalidare la migrazione riesaminando i dati e lo schema.

   ![Screenshot della Esplora oggetti SQL Server Management Studio per la convalida della migrazione in SSMA.](./media/access-to-sql-database-guide/validate-data.png)

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

Il team di progettazione di data SQL ha sviluppato questa risorsa. La carta di base del team consente di sbloccare e accelerare la modernizzazione complessa per i progetti di migrazione della piattaforma dati alla piattaforma dati di Microsoft Azure.

## <a name="next-steps"></a>Passaggi successivi

- Per una matrice di servizi e strumenti di Microsoft e di terze parti disponibili per agevolare diversi scenari di migrazione di database e dati e attività speciali, vedere [servizio e strumenti per la migrazione dei dati](../../../dms/dms-tools-matrix.md).

- Per altre informazioni sul database SQL di Azure, vedere:
   - [Panoramica del database SQL](../../database/sql-database-paas-overview.md)
   - [Calcolatore costo totale di proprietà di Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Per ulteriori informazioni sul Framework e il ciclo di adozione per le migrazioni cloud, vedere:
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per il costo e il dimensionamento dei carichi di lavoro per la migrazione ad Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Per valutare il livello di accesso dell'applicazione, vedere [Data Access Migration Toolkit (anteprima)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Per informazioni sull'esecuzione di test A/B a livello di accesso ai dati, vedere [Panoramica di database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
