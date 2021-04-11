---
title: 'Istanza gestita SQL da Oracle ad Azure: Guida alla migrazione'
description: In questa guida si apprenderà come eseguire la migrazione degli schemi Oracle in Azure SQL Istanza gestita usando SQL Server Migration Assistant per Oracle.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 2bb019a692178c5b44c3589d401d3b2b34c3dccb
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553907"
---
# <a name="migration-guide-oracle-to-azure-sql-managed-instance"></a>Guida alla migrazione: Istanza gestita SQL da Oracle ad Azure

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

In questa guida si apprenderà come eseguire la migrazione degli schemi Oracle in Azure SQL Istanza gestita usando SQL Server Migration Assistant per Oracle (SSMA per Oracle).

Per altre guide alla migrazione, vedere [guide alla migrazione del database di Azure](https://docs.microsoft.com/data-migration).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare la migrazione dello schema Oracle a SQL Istanza gestita:

- Verificare che l'ambiente di origine sia supportato.
- Scaricare [SSMA per Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- Avere una destinazione [SQL istanza gestita](../../managed-instance/instance-create-quickstart.md) .
- Ottenere le [autorizzazioni necessarie per SSMA per Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) e [provider](/sql/ssma/oracle/connect-to-oracle-oracletosql).
 
## <a name="pre-migration"></a>Pre-migrazione

Dopo aver soddisfatto i prerequisiti, si è pronti per individuare la topologia dell'ambiente e valutare la fattibilità della migrazione. Questa parte del processo comporta l'esecuzione di un inventario dei database di cui è necessario eseguire la migrazione, la valutazione di tali database per potenziali problemi o blocchi di migrazione e la risoluzione di eventuali elementi che potrebbero essere stati individuati.

### <a name="assess"></a>Valutare

Utilizzando SSMA per Oracle, è possibile esaminare gli oggetti e i dati di database, valutare i database per la migrazione, eseguire la migrazione di oggetti di database a SQL Istanza gestita e infine migrare i dati nel database.

Per creare una valutazione:

1. Aprire [SSMA per Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
1. Selezionare **file**, quindi fare clic su **nuovo progetto**.
1. Immettere un nome di progetto e un percorso per salvare il progetto. Selezionare quindi **istanza gestita SQL di Azure** come destinazione della migrazione dall'elenco a discesa e selezionare **OK**.

   ![Screenshot che mostra il nuovo progetto.](./media/oracle-to-managed-instance-guide/new-project.png)

1. Selezionare **Connetti a Oracle**. Immettere i valori per i dettagli della connessione Oracle nella finestra di dialogo **Connetti a Oracle** .

   ![Screenshot che mostra la connessione a Oracle.](./media/oracle-to-managed-instance-guide/connect-to-oracle.png)

1. Selezionare gli schemi Oracle di cui si desidera eseguire la migrazione.

   ![Screenshot che mostra la selezione dello schema Oracle.](./media/oracle-to-managed-instance-guide/select-schema.png)

1. In **Oracle Metadata Explorer** fare clic con il pulsante destro del mouse sullo schema Oracle di cui si desidera eseguire la migrazione e quindi scegliere **Crea report** per generare un report HTML. In alternativa, è possibile selezionare un database e quindi selezionare la scheda **Crea rapporto** .

   ![Screenshot che mostra la creazione di report.](./media/oracle-to-managed-instance-guide/create-report.png)

1. Leggere il report HTML per esaminare le statistiche di conversione e gli eventuali errori o avvisi. È inoltre possibile aprire il report in Excel per ottenere un inventario degli oggetti Oracle e lo sforzo necessario per eseguire le conversioni dello schema. La posizione predefinita del report è la cartella report all'interno di SSMAProjects.

   Ad esempio, vedere `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`.

   ![Screenshot che mostra un report di valutazione.](./media/oracle-to-managed-instance-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>Convalidare i tipi di dati

Convalidare i mapping dei tipi di dati predefiniti e modificarli in base ai requisiti, se necessario. A questo scopo, attenersi alla procedura seguente:

1. In SSMA per Oracle selezionare **strumenti**, quindi selezionare **Impostazioni progetto**.
1. Selezionare la scheda **mapping dei tipi** .

   ![Screenshot che mostra il mapping dei tipi.](./media/oracle-to-managed-instance-guide/type-mappings.png)

1. È possibile modificare il mapping dei tipi per ogni tabella selezionando la tabella in **Oracle Metadata Explorer**.

### <a name="convert-the-schema"></a>Convertire lo schema

Per convertire lo schema:

1. (Facoltativo) Aggiungere query dinamiche o ad hoc alle istruzioni. Fare clic con il pulsante destro del mouse sul nodo, quindi scegliere **Aggiungi istruzioni**.
1. Selezionare la scheda **Connetti ad Azure SQL istanza gestita** .
    1. Immettere i dettagli della connessione per connettere il database in **istanza gestita di database SQL**.
    1. Selezionare il database di destinazione dall'elenco a discesa oppure immettere un nuovo nome, nel qual caso verrà creato un database nel server di destinazione.
    1. Immettere i dettagli di autenticazione e selezionare **Connetti**.

    ![Screenshot che mostra la connessione al Istanza gestita SQL di Azure.](./media/oracle-to-managed-instance-guide/connect-to-sql-managed-instance.png)

1. In **Oracle Metadata Explorer** fare clic con il pulsante destro del mouse sullo schema Oracle, quindi scegliere **Converti schema**. In alternativa, è possibile selezionare lo schema e quindi selezionare la scheda **Converti schema** .

   ![Screenshot che mostra la conversione dello schema.](./media/oracle-to-managed-instance-guide/convert-schema.png)

1. Al termine della conversione, confrontare ed esaminare gli oggetti convertiti con gli oggetti originali per identificare i potenziali problemi e risolverli in base alle indicazioni.

   ![Screenshot che mostra il confronto tra le raccomandazioni della tabella.](./media/oracle-to-managed-instance-guide/table-comparison.png)

1. Confrontare il testo Transact-SQL convertito con il codice originale ed esaminare le indicazioni.

   ![Screenshot che mostra il confronto tra le procedure consigliate.](./media/oracle-to-managed-instance-guide/procedure-comparison.png)

1. Nel riquadro Output selezionare **Verifica risultati** ed esaminare gli errori nel riquadro **Elenco errori** .
1. Salvare il progetto in locale per un esercizio di correzione dello schema offline. Scegliere **Salva progetto** dal menu **file** . Questo passaggio consente di valutare gli schemi di origine e di destinazione offline ed eseguire la correzione prima di pubblicare lo schema in SQL Istanza gestita.

## <a name="migrate"></a>Migrate

Al termine della valutazione dei database e della risoluzione di eventuali discrepanze, il passaggio successivo consiste nell'eseguire il processo di migrazione. La migrazione prevede due passaggi: la pubblicazione dello schema e la migrazione dei dati.

Per pubblicare lo schema ed eseguire la migrazione dei dati:
1. Pubblicare lo schema facendo clic con il pulsante destro del mouse sul database dal nodo **database** in **Azure SQL istanza gestita Metadata Explorer** e selezionando **Sincronizza con database**.

   ![Screenshot che mostra la sincronizzazione con il database.](./media/oracle-to-managed-instance-guide/synchronize-with-database.png)
   

1. Esaminare il mapping tra il progetto di origine e la destinazione.

   ![Screenshot che mostra la sincronizzazione con la revisione del database.](./media/oracle-to-managed-instance-guide/synchronize-with-database-review.png)

1. Migrare i dati facendo clic con il pulsante destro del mouse sullo schema o sull'oggetto di cui si desidera eseguire la migrazione in **Oracle Metadata Explorer** e selezionando **Migrate data**. In alternativa, è possibile selezionare la scheda **Migrate data** . Per eseguire la migrazione dei dati per un intero database, selezionare la casella di controllo accanto al nome del database. Per eseguire la migrazione dei dati da singole tabelle, espandere il database, espandere **tabelle**, quindi selezionare le caselle di controllo accanto alle tabelle. Per omettere i dati dalle singole tabelle, deselezionare le caselle di controllo.

   ![Screenshot che mostra la migrazione dei dati.](./media/oracle-to-managed-instance-guide/migrate-data.png)

1. Immettere i dettagli della connessione per Oracle e SQL Istanza gestita.
1. Al termine della migrazione, visualizzare il **report di migrazione dei dati**.

   ![Screenshot che mostra il report di migrazione dei dati.](./media/oracle-to-managed-instance-guide/data-migration-report.png)

1. Connettersi all'istanza di SQL Istanza gestita utilizzando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)e convalidare la migrazione riesaminando i dati e lo schema.

   ![Screenshot che mostra la convalida in SSMA per Oracle.](./media/oracle-to-managed-instance-guide/validate-data.png)

In alternativa, è anche possibile usare SQL Server Integration Services per eseguire la migrazione. Per altre informazioni, vedere:

- [Introduzione a SQL Server Integration Services](/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services per Azure e lo spostamento di dati ibridi](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>Post-migrazione

Dopo aver completato la fase di *migrazione* , è necessario completare una serie di attività post-migrazione per assicurarsi che tutto funzioni correttamente nel modo più semplice ed efficiente possibile.

### <a name="remediate-applications"></a>Correggere le applicazioni

Dopo la migrazione dei dati nell'ambiente di destinazione, tutte le applicazioni che in precedenza usavano l'origine devono iniziare a usare la destinazione. In alcuni casi, l'esecuzione di questo passaggio richiede modifiche alle applicazioni.

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) è un'estensione per Visual Studio Code che consente di analizzare il codice sorgente Java e rilevare le chiamate e le query API di accesso ai dati. Il Toolkit fornisce una visualizzazione a un singolo riquadro degli elementi che devono essere risolti per supportare il nuovo back-end del database. Per altre informazioni, vedere il post di Blog [eseguire la migrazione dell'applicazione Java da Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) .

### <a name="perform-tests"></a>Eseguire test

L'approccio di test alla migrazione del database è costituito dalle attività seguenti:

1. **Sviluppare i test di convalida**: per testare la migrazione del database, è necessario usare le query SQL. È necessario creare le query di convalida da eseguire sia sul database di origine che su quello di destinazione. Le query di convalida devono coprire l'ambito definito.
2. **Configurare un ambiente di test**: l'ambiente di test deve contenere una copia del database di origine e del database di destinazione. Assicurarsi di isolare l'ambiente di test.
3. **Eseguire i test di convalida**: eseguire i test di convalida sull'origine e sulla destinazione, quindi analizzare i risultati.
4. **Eseguire test delle prestazioni**: eseguire test delle prestazioni sull'origine e sulla destinazione, quindi analizzare e confrontare i risultati.

### <a name="optimize"></a>Ottimizzazione

La fase post-migrazione è fondamentale per riconciliare eventuali problemi di accuratezza dei dati, verificare la completezza e risolvere i problemi relativi alle prestazioni con il carico di lavoro.

> [!NOTE]
> Per ulteriori informazioni su questi problemi e sui passaggi per attenuarli, vedere la [Guida alla convalida e all'ottimizzazione post-migrazione](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Risorse per la migrazione

Per ulteriori informazioni sul completamento di questo scenario di migrazione, vedere le risorse seguenti. Sono state sviluppate a supporto di un engagement di progetto di migrazione reale.

| **Titolo/collegamento**                                                                                                                                          | **Descrizione**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Strumento e modello di valutazione del carico di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Questo strumento fornisce le piattaforme di destinazione consigliate, la preparazione per il cloud e il livello di monitoraggio e aggiornamento dell'applicazione o del database per un determinato carico di lavoro. Offre semplici calcoli con un solo clic e la generazione di report che consentono di accelerare le valutazioni di grandi dimensioni fornendo un processo decisionale di piattaforma di destinazione automatizzato e uniforme.                                                          |
| [Elementi di script di inventario Oracle](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Questo asset include una query PL/SQL che raggiunge le tabelle di sistema Oracle e fornisce un conteggio degli oggetti in base al tipo di schema, al tipo di oggetto e allo stato. Fornisce inoltre una stima approssimativa dei dati non elaborati in ogni schema e il dimensionamento delle tabelle in ogni schema, con risultati archiviati in formato CSV.                                                                                                               |
| [Automatizzare la raccolta SSMA Oracle Assessment & il consolidamento](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Questo set di risorse utilizza un file CSV come voce (sources.csv nelle cartelle del progetto) per produrre i file XML necessari per eseguire una valutazione SSMA in modalità console. Il source.csv viene fornito dal cliente in base a un inventario delle istanze esistenti di Oracle. I file di output sono AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml e VariableValueFile.xml.|
| [SSMA per errori comuni di Oracle e come risolverli](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Con Oracle è possibile assegnare una condizione non scalare nella clausola WHERE. Tuttavia, SQL Server non supporta questo tipo di condizione. Di conseguenza, SSMA per Oracle non converte le query con una condizione non scalare nella clausola WHERE. Genera invece l'errore O2SS0001. In questa white paper vengono fornite informazioni dettagliate sul problema e su come risolverlo.          |
| [Manuale della migrazione da Oracle a SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Questo documento è incentrato sulle attività associate alla migrazione di uno schema Oracle alla versione più recente di SQL Server database. Se la migrazione richiede modifiche alle funzionalità o alle funzionalità, il possibile effetto di ogni modifica sulle applicazioni che utilizzano il database deve essere considerato attentamente.                                                     |

Le risorse sono state sviluppate dal team di progettazione di SQL Data. La carta di base di questo team consente di sbloccare e accelerare la modernizzazione complessa per i progetti di migrazione della piattaforma dati alla piattaforma dati di Microsoft Azure.

## <a name="next-steps"></a>Passaggi successivi

- Per una matrice di servizi e strumenti di Microsoft e di terze parti disponibili per agevolare diversi scenari di migrazione di database e dati e attività speciali, vedere [Servizi e strumenti per la migrazione dei dati](../../../dms/dms-tools-matrix.md).

- Per ulteriori informazioni su SQL Istanza gestita, vedere:
  - [Panoramica di Istanza gestita SQL di Azure](../../managed-instance/sql-managed-instance-paas-overview.md)
  - [Calcolatore costo totale di proprietà (TCO) di Azure](https://azure.microsoft.com/en-us/pricing/tco/calculator/)

- Per ulteriori informazioni sul Framework e il ciclo di adozione per le migrazioni cloud, vedere:
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per il costo e il dimensionamento dei carichi di lavoro per la migrazione ad Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- Per contenuti video, vedere:
    - [Panoramica del percorso di migrazione e degli strumenti e dei servizi consigliati per eseguire la valutazione e la migrazione](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
