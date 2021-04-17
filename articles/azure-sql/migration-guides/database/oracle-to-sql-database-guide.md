---
title: 'Da Oracle a database SQL di Azure: Guida alla migrazione'
description: In questa guida si apprenderà come eseguire la migrazione dello schema Oracle a database SQL di Azure usando SQL Server Migration Assistant per Oracle.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: 45fbc1f85c5d7f66716fbf69deb430ce74575435
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388483"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>Guida alla migrazione: Da Oracle a database SQL di Azure

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

In questa guida [](https://azure.microsoft.com/migration/migration-journey) si apprenderà come eseguire la migrazione degli schemi Oracle a database SQL di Azure usando [SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant per Oracle (SSMA per Oracle).

Per altre guide alla migrazione, vedere Guide [alla migrazione del database di Azure](https://docs.microsoft.com/data-migration).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare la migrazione dello schema Oracle al database SQL:

- Verificare che l'ambiente di origine sia supportato.
- Scaricare [SSMA per Oracle.](https://www.microsoft.com/download/details.aspx?id=54258)
- Disporre di [un'istanza del database SQL di](../../database/single-database-create-quickstart.md) destinazione.
- Ottenere le [autorizzazioni necessarie per SSMA per Oracle e](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) il [provider](/sql/ssma/oracle/connect-to-oracle-oracletosql).
 
## <a name="pre-migration"></a>Pre-migrazione

Dopo aver soddisfatto i prerequisiti, è possibile individuare la topologia dell'ambiente e valutare la fattibilità della migrazione [cloud di Azure.](https://azure.microsoft.com/migration) Questa parte del processo prevede l'esecuzione di un inventario dei database di cui è necessario eseguire la migrazione, la valutazione di tali database per individuare potenziali problemi di migrazione o blocchi e quindi la risoluzione di eventuali elementi scoperti.

### <a name="assess"></a>Valutare

Con SSMA per Oracle è possibile esaminare gli oggetti di database e i dati, valutare i database per la migrazione, eseguire la migrazione di oggetti di database al database SQL e infine eseguire la migrazione dei dati al database.

Per creare una valutazione:

1. Aprire [SSMA per Oracle.](https://www.microsoft.com/download/details.aspx?id=54258)
1. Selezionare **File** e quindi Nuovo **progetto.**
1. Immettere un nome di progetto e un percorso per salvare il progetto. Selezionare quindi **database SQL di Azure** destinazione della migrazione dall'elenco a discesa e selezionare **OK.**

   ![Screenshot che mostra Connettersi a Oracle.](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

1. Selezionare **Connetti a Oracle**. Immettere i valori per i dettagli della connessione Oracle nella **finestra di dialogo Connetti** a Oracle .

1. Selezionare gli schemi Oracle di cui si vuole eseguire la migrazione.

   ![Screenshot che mostra la selezione dello schema Oracle.](./media/oracle-to-sql-database-guide/select-schema.png)

1. In **Oracle Metadata Explorer fare** clic con il pulsante destro del mouse sullo schema Oracle di cui si vuole eseguire la migrazione e quindi scegliere Crea **report** per generare un report HTML. In alternativa, è possibile selezionare un database e quindi selezionare la **scheda Crea** report.

   ![Screenshot che mostra Crea report.](./media/oracle-to-sql-database-guide/create-report.png)

1. Leggere il report HTML per esaminare le statistiche di conversione e gli eventuali errori o avvisi. È anche possibile aprire il report in Excel per ottenere un inventario degli oggetti Oracle e il lavoro richiesto per eseguire le conversioni dello schema. La posizione predefinita del report è la cartella report all'interno di SSMAProjects.

   Ad esempio, vedere `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`.

   ![Screenshot che mostra un report valutazione.](./media/oracle-to-sql-database-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>Convalidare i tipi di dati

Convalidare i mapping dei tipi di dati predefiniti e modificarli in base ai requisiti, se necessario. A questo scopo, attenersi alla procedura seguente:

1. In SSMA per Oracle selezionare **Strumenti** e quindi Selezionare **Impostazioni progetto**.
1. Selezionare la **scheda Mapping dei** tipi.

   ![Screenshot che mostra il mapping dei tipi.](./media/oracle-to-sql-database-guide/type-mappings.png)

1. È possibile modificare il mapping dei tipi per ogni tabella selezionando la tabella in **Esplora metadati Oracle.**

### <a name="convert-the-schema"></a>Convertire lo schema

Per convertire lo schema:

1. (Facoltativo) Aggiungere query dinamiche o ad hoc alle istruzioni. Fare clic con il pulsante destro del mouse sul nodo e **quindi scegliere Aggiungi istruzioni**.
1. Selezionare la **scheda Connetti database SQL di Azure** connessione.
    1. In **Database SQL** immettere i dettagli della connessione per connettere il database.
    1. Selezionare l'istanza del database SQL di destinazione dall'elenco a discesa oppure immettere un nuovo nome, nel qual caso verrà creato un database nel server di destinazione.
    1. Immettere i dettagli di autenticazione e selezionare **Connetti.**

    ![Screenshot che mostra Connetti a database SQL di Azure.](./media/oracle-to-sql-database-guide/connect-to-sql-database.png)

1. In **Oracle Metadata Explorer** fare clic con il pulsante destro del mouse sullo schema Oracle e quindi scegliere Converti **schema**. In alternativa, è possibile selezionare lo schema e quindi selezionare la **scheda Converti** schema.

   ![Screenshot che mostra Converti schema.](./media/oracle-to-sql-database-guide/convert-schema.png)

1. Al termine della conversione, confrontare ed esaminare gli oggetti convertiti con gli oggetti originali per identificare potenziali problemi e risolvere i problemi in base alle raccomandazioni.

   ![Screenshot che mostra lo schema delle raccomandazioni di revisione.](./media/oracle-to-sql-database-guide/table-mapping.png)

1. Confrontare il testo Transact-SQL convertito con le stored procedure originali ed esaminare le raccomandazioni.

   ![Screenshot che mostra le raccomandazioni di revisione.](./media/oracle-to-sql-database-guide/procedure-comparison.png)

1. Nel riquadro di output selezionare **Rivedi risultati** ed esaminare gli errori nel **riquadro Elenco** errori.
1. Salvare il progetto in locale per un esercizio di correzione dello schema offline. Scegliere **Salva** progetto dal menu **File**. Questo passaggio offre la possibilità di valutare gli schemi di origine e di destinazione offline ed eseguire la correzione prima di pubblicare lo schema nel database SQL.

## <a name="migrate"></a>Migrate

Dopo aver valutato i database e aver corretto eventuali discrepanze, il passaggio successivo consiste nell'eseguire il processo di migrazione. La migrazione prevede due passaggi: la pubblicazione dello schema e la migrazione dei dati.

Per pubblicare lo schema ed eseguire la migrazione dei dati:

1. Pubblicare lo schema facendo clic  con il pulsante destro del mouse sul database dal nodo Database in Esplora database SQL di Azure **metadati** e scegliendo **Sincronizza con database**.

   ![Screenshot che mostra La sincronizzazione con il database.](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

1. Esaminare il mapping tra il progetto di origine e la destinazione.

   ![Screenshot che mostra La sincronizzazione con la verifica del database.](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)

1. Eseguire la migrazione dei dati facendo clic con il pulsante destro del mouse sul database o sull'oggetto di cui si vuole eseguire la migrazione in **Oracle Metadata Explorer** e scegliendo Esegui migrazione **dati**. In alternativa, è possibile selezionare la **scheda Esegui migrazione** dati. Per eseguire la migrazione dei dati per un intero database, selezionare la casella di controllo accanto al nome del database. Per eseguire la migrazione dei dati da singole tabelle, espandere il database, espandere **Tabelle** e quindi selezionare le caselle di controllo accanto alle tabelle. Per omettere dati da singole tabelle, deselezionare le caselle di controllo.

   ![Screenshot che mostra Eseguire la migrazione dei dati.](./media/oracle-to-sql-database-guide/migrate-data.png)

1. Immettere i dettagli di connessione sia per Oracle che per il database SQL.
1. Al termine della migrazione, visualizzare il **report di migrazione dei dati**.

   ![Screenshot che mostra il report di migrazione dei dati.](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. Connettersi all'istanza del database SQL [usando SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)e convalidare la migrazione esaminando i dati e lo schema.

   ![Screenshot che mostra la convalida in SQL Server Management Studio.](./media/oracle-to-sql-database-guide/validate-data.png)

In alternativa, è anche possibile usare SQL Server Integration Services per eseguire la migrazione. Per altre informazioni, vedere:

- [Introduzione a SQL Server Integration Services](/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services per lo spostamento di dati ibrido e azure](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>Post-migrazione

Dopo aver completato la  fase di migrazione, è necessario completare una serie di attività post-migrazione per assicurarsi che tutto funzioni nel modo più uniforme ed efficiente possibile.

### <a name="remediate-applications"></a>Correggere le applicazioni

Dopo la migrazione dei dati nell'ambiente di destinazione, tutte le applicazioni che in precedenza usavano l'origine devono iniziare a usare la destinazione. Questa attività richiederà in alcuni casi modifiche alle applicazioni.

Il [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) è un'estensione per Visual Studio Code che consente di analizzare il codice sorgente Java e rilevare query e chiamate API di accesso ai dati. Il toolkit offre una visualizzazione a riquadro singolo di ciò che deve essere indirizzato per supportare il nuovo back-end del database. Per altre informazioni, vedere il post di blog Eseguire la migrazione [di applicazioni Java da Oracle.](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727)

### <a name="perform-tests"></a>Eseguire test

L'approccio di test alla migrazione del database è costituito dalle attività seguenti:

1. **Sviluppare test di convalida:** per testare la migrazione del database, è necessario usare query SQL. È necessario creare le query di convalida da eseguire sia sul database di origine che su quello di destinazione. Le query di convalida devono coprire l'ambito definito.
1. **Configurare un ambiente di test:** l'ambiente di test deve contenere una copia del database di origine e del database di destinazione. Assicurarsi di isolare l'ambiente di test.
1. **Eseguire test di convalida:** eseguire test di convalida sull'origine e sulla destinazione e quindi analizzare i risultati.
1. **Eseguire test delle prestazioni:** eseguire test delle prestazioni rispetto all'origine e alla destinazione, quindi analizzare e confrontare i risultati.

### <a name="optimize"></a>Ottimizzazione

La fase post-migrazione è fondamentale per la risoluzione di eventuali problemi di accuratezza dei dati, la verifica della completezza e la risoluzione dei problemi di prestazioni con il carico di lavoro.

> [!NOTE]
> Per altre informazioni su questi problemi e sui passaggi per attenuarli, vedere la Guida alla convalida e all'ottimizzazione [post-migrazione](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Risorse per la migrazione

Per altre informazioni sul completamento di questo scenario di migrazione, vedere le risorse seguenti. Sono stati sviluppati a supporto di un impegno di progetto di migrazione reale.

| **Titolo/collegamento**                                                                                                                                          | **Descrizione**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Modello e strumento di valutazione del carico di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Questo strumento offre piattaforme di destinazione "best fit" suggerite, conformità al cloud e livello di correzione dell'applicazione o del database per un determinato carico di lavoro. Offre una semplice generazione di report e calcolo con un solo clic che consente di accelerare le valutazioni di proprietà di grandi dimensioni fornendo un processo decisionale automatizzato e uniforme della piattaforma di destinazione.                                                          |
| [Artefatti dello script di inventario Oracle](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Questo asset include una query PL/SQL che raggiunge le tabelle di sistema Oracle e fornisce un conteggio degli oggetti in base al tipo di schema, al tipo di oggetto e allo stato. Fornisce anche una stima approssimativa dei dati non elaborati in ogni schema e il ridimensionamento delle tabelle in ogni schema, con i risultati archiviati in formato CSV.                                                                                                               |
| [Automatizzare la raccolta SSMA Oracle Assessment & consolidamento](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Questo set di risorse usa un file CSV come voce (sources.csv nelle cartelle del progetto) per produrre i file XML necessari per eseguire una valutazione SSMA in modalità console. Il source.csv viene fornito dal cliente in base a un inventario di istanze Oracle esistenti. I file di output AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml e VariableValueFile.xml.|
| [Errori comuni di SSMA per Oracle e come risolverli](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Con Oracle è possibile assegnare una condizione non scalare nella clausola WHERE. Tuttavia, SQL Server non supporta questo tipo di condizione. Di conseguenza, SSMA per Oracle non converte le query con una condizione non scalare nella clausola WHERE. Genera invece l'errore O2SS0001. Questo white paper informazioni dettagliate sul problema e sui modi per risolverlo.          |
| [Manuale sulla migrazione da Oracle a SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Questo documento è in particolare sulle attività associate alla migrazione di uno schema Oracle alla versione più recente di SQL Server Database. Se la migrazione richiede modifiche alle funzionalità, è necessario considerare attentamente il possibile impatto di ogni modifica sulle applicazioni che utilizzano il database.                                                     |

Queste risorse sono state sviluppate dal team tecnico di Data SQL. L'elemento principale di questo team è sbloccare e accelerare la modernizzazione complessa per i progetti di migrazione della piattaforma dati alla piattaforma dati di Microsoft Azure.

## <a name="next-steps"></a>Passaggi successivi

- Per una matrice di servizi e strumenti Microsoft e di terze parti disponibili per facilitare vari scenari di migrazione di database e dati e attività specifiche, vedere Servizi e strumenti per la migrazione [dei dati.](../../../dms/dms-tools-matrix.md)

- Per altre informazioni sul database SQL, vedere:
  - [Panoramica delle database SQL di Azure](../../database/sql-database-paas-overview.md)
  - [Calcolatore del costo totale di proprietà di Azure](https://azure.microsoft.com/pricing/tco/calculator/)

- Per altre informazioni sul framework e sul ciclo di adozione per le migrazioni cloud, vedere:
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per la determinazione dei costi e il ridimensionamento dei carichi di lavoro per la migrazione ad Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
   -  [Migrazione cloud risorse](https://azure.microsoft.com/migration/resources)

- Per contenuti video, vedere:
    - [Panoramica del percorso di migrazione e degli strumenti e dei servizi consigliati per eseguire la valutazione e la migrazione](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
