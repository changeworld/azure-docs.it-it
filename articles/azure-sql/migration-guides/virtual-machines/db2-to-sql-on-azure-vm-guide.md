---
title: 'Da Db2 a SQL Server macchina virtuale di Azure: Guida alla migrazione'
description: Questa guida spiega come eseguire la migrazione dei database IBM Db2 SQL Server in una macchina virtuale di Azure usando SQL Server Migration Assistant per Db2.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 4c40617f4e374a696bbc00b7250500c1f1402421
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588616"
---
# <a name="migration-guide-ibm-db2-to-sql-server-on-azure-vm"></a>Guida alla migrazione: da IBM Db2 a SQL Server in una macchina virtuale di Azure
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Questa guida spiega come eseguire la migrazione dei database utente da IBM Db2 a SQL Server in una macchina virtuale di Azure usando il SQL Server Migration Assistant per Db2. 

Per altre guide alla migrazione, vedere Guide [alla migrazione del database di Azure](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Prerequisiti

Per eseguire la migrazione del database Db2 SQL Server, è necessario:

- Per verificare che [l'ambiente di origine sia supportato.](/sql/ssma/db2/installing-ssma-for-Db2-client-Db2tosql#prerequisites)
- [SQL Server Migration Assistant (SSMA) per Db2](https://www.microsoft.com/download/details.aspx?id=54254).
- [Connettività](../../virtual-machines/windows/ways-to-connect-to-sql.md) tra l'ambiente di origine e SQL Server macchina virtuale in Azure. 
- Un'istanza [di SQL Server in una macchina virtuale di Azure.](../../virtual-machines/windows/create-sql-vm-portal.md) 

## <a name="pre-migration"></a>Pre-migrazione

Dopo aver soddisfatto i prerequisiti, è possibile individuare la topologia dell'ambiente e valutare la fattibilità della migrazione. 

### <a name="assess"></a>Valutare 

Usare SSMA per DB2 per esaminare oggetti e dati di database e valutare i database per la migrazione. 

Per creare una valutazione, seguire questa procedura:

1. Aprire [SSMA per Db2.](https://www.microsoft.com/download/details.aspx?id=54254) 
1. Selezionare **File**  >  **Nuovo progetto**. 
1. Specificare un nome di progetto e un percorso per salvare il progetto. Selezionare quindi una destinazione SQL Server migrazione dall'elenco a discesa e selezionare **OK**.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/new-project.png" alt-text="Screenshot che mostra i dettagli del progetto da specificare.":::


1. In **Connetti a Db2** immettere i valori per i dettagli della connessione Db2.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/connect-to-Db2.png" alt-text="Screenshot che mostra le opzioni per connettersi all'istanza di Db2.":::


1. Fare clic con il pulsante destro del mouse sullo schema Db2 di cui si vuole eseguire la migrazione e quindi scegliere **Crea report**. Verrà generato un report HTML. In alternativa, è possibile scegliere **Create report** (Crea report) dalla barra di spostamento dopo aver selezionato lo schema.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/create-report.png" alt-text="Screenshot che mostra come creare un report.":::

1. Leggere il report HTML per esaminare le statistiche di conversione e gli eventuali errori o avvisi. È anche possibile aprire il report in Excel per ottenere un inventario degli oggetti Db2 e il lavoro richiesto per eseguire le conversioni dello schema. Il percorso predefinito per il report si trova nella cartella report all'interno *di SSMAProjects.*

   Ad esempio: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/report.png" alt-text="Screenshot del report esaminato per identificare eventuali errori o avvisi.":::


### <a name="validate-data-types"></a>Convalidare i tipi di dati

Convalidare i mapping dei tipi di dati predefiniti e modificarli in base ai requisiti, se necessario. A questo scopo, attenersi alla procedura seguente: 

1. Selezionare **Tools** (Strumenti) dal menu. 
1. Selezionare **Project Settings** (Impostazioni progetto). 
1. Selezionare la scheda **Type mappings** (Mapping tipi).

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/type-mapping.png" alt-text="Screenshot che mostra la selezione dello schema e del mapping dei tipi.":::

1. È possibile modificare il mapping dei tipi per ogni tabella selezionando la tabella in **Esplora metadati Db2.** 

### <a name="convert-schema"></a>Converti schema 

Per convertire lo schema, seguire questa procedura:

1. (Facoltativo) Aggiungere query dinamiche o ad hoc alle istruzioni . Fare clic con il pulsante destro del mouse sul nodo e quindi scegliere **Add statements** (Aggiungi istruzioni). 
1. Selezionare **Connect to SQL Server** (Connetti a SQL Server). 
    1. Immettere i dettagli di connessione per connettersi all'istanza di SQL Server nella macchina virtuale di Azure. 
    1. Scegliere di connettersi a un database esistente nel server di destinazione o specificare un nuovo nome per creare un nuovo database nel server di destinazione. 
    1. Specificare i dettagli di autenticazione. 
    1. Selezionare **Connetti**.

    :::image type="content" source="../../../../includes/media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png" alt-text="Screenshot che mostra i dettagli necessari per connettersi al SQL Server nella macchina virtuale di Azure.":::

1. Fare clic con il pulsante destro del mouse sullo schema e scegliere **Convert Schema** (Converti schema). In alternativa, è possibile scegliere **Convert Schema** (Converti schema) dalla barra di spostamento superiore dopo aver selezionato lo schema.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/convert-schema.png" alt-text="Screenshot che mostra la selezione e la conversione dello schema.":::

1. Al termine della conversione, confrontare ed esaminare la struttura dello schema per identificare potenziali problemi. Risolvere i problemi in base alle raccomandazioni. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-review-schema-structure.png" alt-text="Screenshot che mostra il confronto e la revisione della struttura dello schema per identificare potenziali problemi.":::

1. Nel riquadro **Output** selezionare **Verifica risultati**. Nel riquadro **Elenco errori** esaminare gli errori. 
1. Salvare il progetto in locale per un esercizio di correzione dello schema offline. Scegliere Salva progetto dal menu **File.** In questo modo è possibile valutare gli schemi di origine e di destinazione offline ed eseguire la correzione prima di poter pubblicare lo schema SQL Server nella macchina virtuale di Azure.

## <a name="migrate"></a>Migrazione

Dopo aver completato la valutazione dei database e corretto eventuali discrepanze, il passaggio successivo consiste nell'eseguire il processo di migrazione.

Per pubblicare lo schema ed eseguire la migrazione dei dati, seguire questa procedura:

1. Pubblicare lo schema. In **SQL Server Visualizzatore metadati**, nel nodo **Database** fare clic con il pulsante destro del mouse sul database. Selezionare quindi **Sincronizza con database**.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/synchronize-with-database.png" alt-text="Screenshot che mostra l'opzione per la sincronizzazione con il database.":::

1. Eseguire la migrazione dei dati. Fare clic con il pulsante destro del mouse sul database o sull'oggetto di cui si vuole eseguire la migrazione in Esplora metadati **db2** e scegliere **Esegui migrazione dati**. In alternativa, è possibile selezionare **Esegui migrazione dati** dalla barra di spostamento. Per eseguire la migrazione dei dati per un intero database, selezionare la casella di controllo accanto al nome del database. Per eseguire la migrazione dei dati da singole tabelle, espandere il database, espandere **Tabelle** e quindi selezionare la casella di controllo accanto alla tabella. Per omettere dati da singole tabelle, deselezionare la casella di controllo .

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/migrate-data.png" alt-text="Screenshot che mostra la selezione dello schema e la scelta di eseguire la migrazione dei dati.":::

1. Specificare i dettagli di connessione per le istanze db2 e SQL Server database. 
1. Al termine della migrazione, visualizzare il **report di migrazione dei dati**:  

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/data-migration-report.png" alt-text="Screenshot che mostra dove esaminare il report di migrazione dei dati.":::

1.  Connettersi all'istanza di SQL Server in una macchina virtuale di Azure [usando SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Convalidare la migrazione esaminando i dati e lo schema.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-schema-in-ssms.png" alt-text="Screenshot che mostra il confronto dello schema in SQL Server Management Studio.":::

## <a name="post-migration"></a>Post-migrazione 

Al termine della migrazione, è necessario eseguire una serie di attività post-migrazione per assicurarsi che tutto funzioni nel modo più uniforme ed efficiente possibile.

### <a name="remediate-applications"></a>Correggere le applicazioni 

Dopo la migrazione dei dati nell'ambiente di destinazione, tutte le applicazioni che in precedenza usavano l'origine devono iniziare a usare la destinazione. Per ottenere questo risultato, in alcuni casi sarà necessario apportare modifiche alle applicazioni.

### <a name="perform-tests"></a>Eseguire test

Il test è costituito dalle attività seguenti:

1. **Sviluppare i test di convalida**: per testare la migrazione del database, è necessario usare query SQL. È necessario creare le query di convalida da eseguire sia sul database di origine che su quello di destinazione. Le query di convalida devono essere estese all'ambito definito.
1. **Configurare l'ambiente di test:** l'ambiente di test deve contenere una copia del database di origine e del database di destinazione. Assicurarsi di isolare l'ambiente di test.
1. **Eseguire test di convalida**: eseguire i test di convalida sull'origine e sulla destinazione, quindi analizzare i risultati.
1. **Eseguire test delle prestazioni:** eseguire test delle prestazioni rispetto all'origine e alla destinazione, quindi analizzare e confrontare i risultati.

## <a name="migration-assets"></a>Risorse per la migrazione 

Per ulteriore assistenza, vedere le risorse seguenti, che sono state sviluppate a supporto di un progetto di migrazione reale:

|Asset  |Descrizione  |
|---------|---------|
|[Strumento e modello di valutazione dei carichi di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Questo strumento indica le piattaforme di destinazione "più idonee" suggerite, la preparazione per il cloud e il livello di correzione di applicazioni/database per un determinato carico di lavoro. Offre funzionalità semplici e accessibili con un solo clic per l'esecuzione di calcoli e la generazione di report, che consentono di accelerare le valutazioni in ambienti estesi grazie a un processo decisionale automatizzato e uniforme per la piattaforma di destinazione.|
|[Pacchetto di individuazione e valutazione degli asset di dati Db2 zOS](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Dopo aver eseguito lo script SQL in un database, è possibile esportare i risultati in un file nel file system. Sono supportati diversi formati di file, incluso il formato CSV, in modo che sia possibile acquisire i risultati in strumenti esterni come i fogli di calcolo. Questo metodo può essere utile se si vogliono condividere facilmente i risultati con i team che non hanno installato il workbench.|

|Script ed elementi di [inventario DI IBM Db2 LUW |](https://github.com/microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts) Questo asset include una query SQL che raggiunge le tabelle di sistema IBM Db2 LUW versione 11.1 e fornisce un conteggio degli oggetti in base allo schema e al tipo di oggetto, una stima approssimativa dei "dati non elaborati" in ogni schema e il ridimensionamento delle tabelle in ogni schema, con i risultati archiviati in formato CSV.| | [Db2 LUW pure scale on Azure - Guida alla](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)configurazione | Questa guida funge da punto di partenza per un piano di implementazione db2. Anche se i requisiti aziendali differiranno, si applica lo stesso modello di base. Questo modello architetturale può essere usato anche per le applicazioni OLAP in Azure.|

Queste risorse sono state sviluppate dal team tecnico di Data SQL. L'elemento principale di questo team è sbloccare e accelerare la modernizzazione complessa per i progetti di migrazione della piattaforma dati alla piattaforma dati di Microsoft Azure.

## <a name="next-steps"></a>Passaggi successivi

Dopo la migrazione, vedere [Guida di ottimizzazione e convalida post-migrazione](/sql/relational-databases/post-migration-validation-and-optimization-guide). 

Per i servizi e gli strumenti Microsoft e di terze parti disponibili per facilitare vari scenari di migrazione di database e dati, vedere Strumenti e servizi [di migrazione dei dati.](../../../dms/dms-tools-matrix.md)

Per il contenuto video, [vedere Panoramica del percorso di migrazione.](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
