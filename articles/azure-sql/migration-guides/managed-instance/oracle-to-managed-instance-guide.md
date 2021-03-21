---
title: 'Istanza gestita da Oracle a SQL: Guida alla migrazione'
description: Questa guida illustra come eseguire la migrazione degli schemi Oracle in Azure SQL Istanza gestita usando SQL Server Migration Assistant per Oracle.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: c54ec2cc6e17d9693e25f1471922da8c7c023e36
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602865"
---
# <a name="migration-guide-oracle-to-azure-sql-managed-instance"></a>Guida alla migrazione: Istanza gestita SQL da Oracle ad Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Questa guida illustra come eseguire la migrazione degli schemi Oracle in Azure SQL Istanza gestita usando SQL Server Migration Assistant per Oracle. 

Per altri scenari, vedere la [Guida alla migrazione del database](https://datamigration.microsoft.com/).

## <a name="prerequisites"></a>Prerequisiti

Per eseguire la migrazione dello schema Oracle a SQL Istanza gestita è necessario: 

- Per verificare che l'ambiente di origine sia supportato. 
- Per scaricare [SQL Server Migration Assistant (SSMA) per Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
- Un [istanza gestita SQL di Azure](../../managed-instance/instance-create-quickstart.md)di destinazione. 
- Le [autorizzazioni necessarie per SSMA per Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) e [provider](/sql/ssma/oracle/connect-to-oracle-oracletosql).
 

## <a name="pre-migration"></a>Pre-migrazione

Una volta soddisfatti i prerequisiti, si è pronti per individuare la topologia dell'ambiente e valutare la fattibilità della migrazione. Questa parte del processo comporta l'esecuzione di un inventario dei database di cui è necessario eseguire la migrazione, la valutazione di tali database per potenziali problemi o blocchi di migrazione e la risoluzione di eventuali elementi che potrebbero essere stati individuati.



### <a name="assess"></a>Valutare 

Usare il SQL Server Migration Assistant (SSMA) per Oracle per esaminare gli oggetti e i dati di database, valutare i database per la migrazione, migrare gli oggetti di database in Azure SQL Istanza gestita e infine migrare i dati nel database. 

Per creare una valutazione, seguire questa procedura: 


1. Aprire [SQL Server Migration Assistant per Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Selezionare **File** e quindi scegliere **Nuovo progetto**. 
1. Specificare un nome di progetto, un percorso per salvare il progetto e quindi selezionare Azure SQL Istanza gestita come destinazione della migrazione dall'elenco a discesa. Selezionare **OK**.

   ![Nuovo progetto](./media/oracle-to-managed-instance-guide/new-project.png)

1. Selezionare **Connetti a Oracle**. Immettere i valori per i dettagli della connessione Oracle nella finestra di dialogo **Connetti a Oracle** .

   ![Connettersi a Oracle](./media/oracle-to-managed-instance-guide/connect-to-oracle.png)

   Selezionare lo schema o gli schemi Oracle di cui si vuole eseguire la migrazione: 

   ![Scegliere lo schema Oracle](./media/oracle-to-managed-instance-guide/select-schema.png)

1. Fare clic con il pulsante destro del mouse sullo schema Oracle di cui si desidera eseguire la migrazione in **Oracle Metadata Explorer**, quindi scegliere **Crea report**. Verrà generato un report HTML. In alternativa, è possibile scegliere **Crea report** dalla barra di spostamento dopo aver selezionato il database.

   ![Creazione di report](./media/oracle-to-managed-instance-guide/create-report.png)

1. Leggere il report HTML per esaminare le statistiche di conversione e gli eventuali errori o avvisi. È inoltre possibile aprire il report in Excel per ottenere un inventario degli oggetti Oracle e lo sforzo necessario per eseguire le conversioni dello schema. La posizione predefinita del report è la cartella report all'interno di SSMAProjects.

   ad esempio `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`

   ![Report di valutazione](./media/oracle-to-managed-instance-guide/assessment-report.png)


### <a name="validate-data-types"></a>Convalidare i tipi di dati

Convalidare i mapping dei tipi di dati predefiniti e modificarli in base ai requisiti, se necessario. A questo scopo, attenersi alla procedura seguente:

1. Selezionare **Tools** (Strumenti) dal menu. 
1. Selezionare **Project Settings** (Impostazioni progetto). 
1. Selezionare la scheda **Type mappings** (Mapping tipi). 

   ![Mapping dei tipi](./media/oracle-to-managed-instance-guide/type-mappings.png)

1. È possibile modificare il mapping dei tipi per ogni tabella selezionando la tabella in **Esplora metadati Oracle**.

### <a name="convert-schema"></a>Converti schema

Per convertire lo schema, seguire questa procedura: 

1. (Facoltativo) Aggiungere query dinamiche o ad hoc alle istruzioni. Fare clic con il pulsante destro del mouse sul nodo e quindi scegliere **Add statements** (Aggiungi istruzioni).
1. Selezionare **Connetti a istanza gestita SQL di Azure**. 
    1. Immettere i dettagli della connessione per connettere il database in Azure SQL Istanza gestita.
    1. Scegliere il database di destinazione dall'elenco a discesa.
    1. Selezionare **Connetti**.

    ![Connettersi a Istanza gestita di SQL](./media/oracle-to-managed-instance-guide/connect-to-sql-managed-instance.png)

1. Fare clic con il pulsante destro del mouse sullo schema Oracle in **Oracle Metadata Explorer** , quindi scegliere **Converti schema**. In alternativa, è possibile scegliere **Convert Schema** (Converti schema) dalla barra di spostamento superiore dopo aver selezionato lo schema.

   ![Converti schema](./media/oracle-to-managed-instance-guide/convert-schema.png)

1. Al termine della conversione, confrontare ed esaminare gli oggetti convertiti con gli oggetti originali per identificare i potenziali problemi e risolverli in base alle indicazioni.

   ![Confrontare le raccomandazioni della tabella](./media/oracle-to-managed-instance-guide/table-comparison.png)

   Confrontare il testo Transact-SQL convertito con le stored procedure originali ed esaminare le indicazioni: 

   ![Confrontare le procedure consigliate](./media/oracle-to-managed-instance-guide/procedure-comparison.png)

1. Salvare il progetto in locale per un esercizio di correzione dello schema offline. Scegliere **Salva progetto** dal menu **File**.

## <a name="migrate"></a>Migrazione

Dopo aver completato la valutazione dei database e corretto eventuali discrepanze, il passaggio successivo consiste nell'eseguire il processo di migrazione. La migrazione prevede due passaggi: la pubblicazione dello schema e la migrazione dei dati. 

Per pubblicare lo schema ed eseguire la migrazione dei dati, seguire questa procedura:

1. Pubblicare lo schema: fare clic con il pulsante destro del mouse sul database nel nodo **database** in **Esplora metadati di SQL istanza gestita di Azure** e scegliere **Sincronizza con database**.

   ![Sincronizza con database](./media/oracle-to-managed-instance-guide/synchronize-with-database.png)

   Esaminare il mapping tra il progetto di origine e la destinazione:

   ![Sincronizzare con la revisione del database](./media/oracle-to-managed-instance-guide/synchronize-with-database-review.png)

1. Eseguire la migrazione dei dati: fare clic con il pulsante destro del mouse sullo schema da **Oracle Metadata Explorer** e scegliere **Migrate data**. 

   ![Migrazione dei dati](./media/oracle-to-managed-instance-guide/migrate-data.png)

1. Specificare i dettagli di connessione per Oracle e Azure SQL Istanza gestita.
1. Visualizzare il **report di migrazione dei dati**.

   ![Report di migrazione dati](./media/oracle-to-managed-instance-guide/data-migration-report.png)

1. Connettersi al Istanza gestita SQL di Azure usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e convalidare la migrazione riesaminando i dati e lo schema.

   ![Convalida in SSMA](./media/oracle-to-managed-instance-guide/validate-data.png)


In alternativa, è anche possibile usare SQL Server Integration Services (SSIS) per eseguire la migrazione. Per altre informazioni, vedere: 

- [SQL Server Migration Assistant: come valutare ed eseguire la migrazione dei dati da piattaforme dati non Microsoft a SQL Server](https://blogs.msdn.microsoft.com/datamigration/2016/11/16/sql-server-migration-assistant-how-to-assess-and-migrate-databases-from-non-microsoft-data-platforms-to-sql-server/)
- [Introduzione con SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services: SSIS per Azure e lo spostamento di dati ibridi](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

    


## <a name="post-migration"></a>Post-migrazione 

Dopo aver completato la fase di **migrazione** , è necessario eseguire una serie di attività post-migrazione per assicurarsi che tutto funzioni correttamente nel modo più semplice ed efficiente possibile.

### <a name="remediate-applications"></a>Correggere le applicazioni

Dopo la migrazione dei dati nell'ambiente di destinazione, tutte le applicazioni che in precedenza usavano l'origine devono iniziare a usare la destinazione. Per ottenere questo risultato, in alcuni casi sarà necessario apportare modifiche alle applicazioni.

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) è un'estensione per Visual Studio Code che consente di analizzare il codice sorgente Java e rilevare chiamate API di accesso ai dati e query, offrendo una visualizzazione a un singolo riquadro degli elementi che devono essere risolti per supportare il nuovo back-end del database. Per altre informazioni, vedere il Blog [eseguire la migrazione dell'applicazione Java da Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) . 

### <a name="perform-tests"></a>Eseguire test

L'approccio di test per la migrazione del database consiste nell'eseguire le attività seguenti:

1.  **Sviluppare i test di convalida**. Per testare la migrazione del database, è necessario usare le query SQL. È necessario creare le query di convalida da eseguire sia sul database di origine che su quello di destinazione. Le query di convalida devono essere estese all'ambito definito.

2.  **Configurare l'ambiente di test**. L'ambiente di test deve contenere una copia del database di origine e del database di destinazione. Assicurarsi di isolare l'ambiente di test.

3.  **Eseguire i test di convalida**. Eseguire i test di convalida sull'origine e sulla destinazione, quindi analizzare i risultati.

4.  **Eseguire test delle prestazioni**. Eseguire test delle prestazioni sull'origine e sulla destinazione, quindi analizzare e confrontare i risultati.

### <a name="optimize"></a>Ottimizzazione

La fase post-migrazione è fondamentale per riconciliare eventuali problemi di accuratezza dei dati e verificare la completezza, nonché per risolvere i problemi di prestazioni del carico di lavoro.

> [!NOTE]
> Per ulteriori dettagli su questi problemi e su passaggi specifici per attenuarli, vedere la [Guida alla convalida e all'ottimizzazione post-migrazione](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-assets"></a>Risorse per la migrazione 

Per ulteriori informazioni sul completamento di questo scenario di migrazione, vedere le risorse seguenti, che sono state sviluppate a supporto di un progetto di migrazione del mondo reale.

| **Titolo/collegamento**                                                                                                                                          | **Descrizione**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Strumento e modello di valutazione del carico di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Questo strumento fornisce le piattaforme di destinazione "più idonee" suggerite, la preparazione per il cloud e il livello di monitoraggio e aggiornamento dell'applicazione/database per un determinato carico di lavoro. Offre semplici calcoli con un solo clic e generazione di report che consentono di accelerare le valutazioni di grandi dimensioni grazie a un processo decisionale automatizzato e uniforme della piattaforma di destinazione.                                                          |
| [Elementi di script di inventario Oracle](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Questo asset include una query PL/SQL che raggiunge le tabelle di sistema Oracle e fornisce un conteggio degli oggetti in base al tipo di schema, al tipo di oggetto e allo stato. Fornisce inoltre una stima approssimativa dei dati non elaborati in ogni schema e il dimensionamento delle tabelle in ogni schema, con risultati archiviati in formato CSV.                                                                                                               |
| [Automatizzare la raccolta SSMA Oracle Assessment & il consolidamento](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Questo set di risorse utilizza un file con estensione CSV come voce (sources.csv nelle cartelle del progetto) per produrre i file XML necessari per eseguire SSMA assessment in modalità console. Il source.csv viene fornito dal cliente in base a un inventario delle istanze esistenti di Oracle. I file di output sono AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml e VariableValueFile.xml.|
| [SSMA per errori comuni di Oracle e come risolverli](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Con Oracle è possibile assegnare una condizione non scalare nella clausola WHERE. Tuttavia, SQL Server non supporta questo tipo di condizione. Di conseguenza, SQL Server Migration Assistant (SSMA) per Oracle non converte le query con una condizione non scalare nella clausola WHERE, generando invece un errore O2SS0001. In questa white paper vengono fornite informazioni dettagliate sul problema e su come risolverlo.          |
| [Manuale della migrazione da Oracle a SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Questo documento è incentrato sulle attività associate alla migrazione di uno schema Oracle alla versione più recente di SQL serverbase. Se la migrazione richiede modifiche a funzionalità o funzionalità, il possibile effetto di ogni modifica sulle applicazioni che utilizzano il database deve essere considerato attentamente.                                                     |

Queste risorse sono state sviluppate come parte del programma Data SQL Ninja, sponsorizzato dal team di progettazione Azure Data Group. Obiettivo principale del programma Data SQL Ninja è rendere disponibili opportunità per velocizzare progetti di modernizzazione complessi e la migrazione delle piattaforme dati alla piattaforma dati di Microsoft Azure. Se si ritiene che l'organizzazione possa essere interessata a partecipare al programma Data SQL Ninja, contattare il team dell'account per richiedere l'invio di una candidatura.

## <a name="next-steps"></a>Passaggi successivi

- Per una matrice di servizi e strumenti di Microsoft e di terze parti disponibili per supportare diversi scenari di migrazione di database e dati, nonché per le attività speciali, vedere l'articolo [servizio e strumenti per la migrazione dei dati](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

- Per ulteriori informazioni sul Istanza gestita SQL di Azure, vedere: 
  - [Panoramica di Istanza gestita SQL di Azure](../../database/sql-database-paas-overview.md)
  - [Calcolatore costo totale di proprietà (TCO) di Azure](https://azure.microsoft.com/en-us/pricing/tco/calculator/)


- Per ulteriori informazioni sul Framework e il ciclo di adozione per le migrazioni cloud, vedere
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per il costo e il ridimensionamento dei carichi di lavoro migrazione ad Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) s

- Per contenuti video, vedere: 
    - [Panoramica del percorso di migrazione e degli strumenti e dei servizi consigliati per eseguire la valutazione e la migrazione](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)