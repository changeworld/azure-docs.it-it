---
title: Guida alla migrazione da DB2 a SQL Server in macchine virtuali di Azure
description: Questa guida illustra come eseguire la migrazione del database DB2 per SQL Server in macchine virtuali di Azure con SQL Server Migration Assistant per DB2.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 003448255a82d0062e9abc3c358a47687cd5ae90
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644208"
---
# <a name="migration-guide-db2-to-sql-server-on-azure-vms"></a>Guida alla migrazione: DB2 per SQL Server in macchine virtuali di Azure
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Questa guida alla migrazione illustra come eseguire la migrazione dei database utente da DB2 a SQL Server in macchine virtuali di Azure usando la SQL Server Migration Assistant per DB2. 

Per altre guide alla migrazione, vedere [Migrazione dei database](https://docs.microsoft.com/data-migration). 


## <a name="prerequisites"></a>Prerequisiti

Per eseguire la migrazione del database DB2 a SQL Server, è necessario:

- per verificare che l' [ambiente di origine sia supportato](/sql/ssma/db2/installing-ssma-for-Db2-client-Db2tosql#prerequisites).
- [SQL Server Migration Assistant (SSMA) per DB2](https://www.microsoft.com/download/details.aspx?id=54254).
- [Connettività](../../virtual-machines/windows/ways-to-connect-to-sql.md) tra l'ambiente di origine e la macchina virtuale SQL Server in Azure. 
- SQL Server di destinazione [nella macchina virtuale di Azure](../../virtual-machines/windows/create-sql-vm-portal.md). 



## <a name="pre-migration"></a>Pre-migrazione

Una volta soddisfatti i prerequisiti, si è pronti per individuare la topologia dell'ambiente e valutare la fattibilità della migrazione. 


### <a name="assess"></a>Valutare 

Usare SQL Server Migration Assistant (SSMA) per DB2 per esaminare i dati e gli oggetti di database e valutare i database per la migrazione. 

Per creare una valutazione, seguire questa procedura:

1. Aprire [SQL Server Migration Assistant (SSMA) per DB2](https://www.microsoft.com/download/details.aspx?id=54254). 
1. Selezionare **File** e quindi scegliere **Nuovo progetto**. 
1. Specificare un nome di progetto, una posizione in cui salvare il progetto e quindi selezionare la destinazione della migrazione SQL Server nell'elenco a discesa. Selezionare **OK**:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/new-project.png" alt-text="Specificare i dettagli del progetto e selezionare OK per salvare.":::


1. Immettere i valori per i dettagli della connessione DB2 nella finestra di dialogo **Connetti a DB2** :

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/connect-to-Db2.png" alt-text="Connettersi all'istanza di DB2":::


1. Fare clic con il pulsante destro del mouse sullo schema DB2 di cui si desidera eseguire la migrazione, quindi scegliere **Crea report**. Verrà generato un report HTML. In alternativa, è possibile scegliere **Crea report** dalla barra di spostamento dopo aver selezionato lo schema:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/create-report.png" alt-text="Fare clic con il pulsante destro del mouse sullo schema e scegliere Create report (Crea report)":::

1. Leggere il report HTML per esaminare le statistiche di conversione e gli eventuali errori o avvisi. È inoltre possibile aprire il report in Excel per ottenere un inventario degli oggetti DB2 e lo sforzo necessario per eseguire le conversioni dello schema. La posizione predefinita del report è la cartella report all'interno di SSMAProjects.

   Ad esempio: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/report.png" alt-text="Esaminare il report per identificare eventuali errori o avvisi":::


### <a name="validate-data-types"></a>Convalidare i tipi di dati

Convalidare i mapping dei tipi di dati predefiniti e modificarli in base ai requisiti, se necessario. A questo scopo, attenersi alla procedura seguente: 

1. Selezionare **Tools** (Strumenti) dal menu. 
1. Selezionare **Project Settings** (Impostazioni progetto). 
1. Selezionare la scheda **mapping dei tipi** :

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/type-mapping.png" alt-text="Selezionare lo schema e quindi la scheda dei mapping dei tipi":::

1. È possibile modificare il mapping dei tipi per ogni tabella selezionando la tabella in **DB2 Metadata Explorer**. 

### <a name="convert-schema"></a>Converti schema 

Per convertire lo schema, seguire questa procedura:

1. (Facoltativo) Aggiungere query dinamiche o ad hoc alle istruzioni. Fare clic con il pulsante destro del mouse sul nodo e quindi scegliere **Add statements** (Aggiungi istruzioni). 
1. Selezionare **Connect to SQL Server** (Connetti a SQL Server). 
    1. Immettere i dettagli della connessione per connettersi all'istanza di SQL Server nella macchina virtuale di Azure. 
    1. Scegliere di connettersi a un database esistente nel server di destinazione o specificare un nuovo nome per creare un nuovo database nel server di destinazione. 
    1. Fornire i dettagli di autenticazione. 
    1. Selezionare **Connetti**:

    :::image type="content" source="../../../../includes/media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png" alt-text="Connettersi alla SQL Server in una macchina virtuale di Azure":::

1. Fare clic con il pulsante destro del mouse sullo schema e scegliere **Convert Schema** (Converti schema). In alternativa, è possibile scegliere **Converti schema** dalla barra di spostamento superiore dopo aver selezionato lo schema:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/convert-schema.png" alt-text="Fare clic con il pulsante destro del mouse sullo schema e scegliere Convert Schema (Converti schema)":::

1. Al termine della conversione, confrontare ed esaminare la struttura dello schema per identificare i potenziali problemi e risolverli in base alle indicazioni: 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-review-schema-structure.png" alt-text="Confrontare ed esaminare la struttura dello schema per identificare i potenziali problemi e risolverli in base alle raccomandazioni.":::

1. Selezionare **Verifica risultati** nel riquadro Output ed esaminare gli errori nel riquadro **Elenco errori** . 
1. Salvare il progetto in locale per un esercizio di correzione dello schema offline. Scegliere **Salva progetto** dal menu **File**. In questo modo è possibile valutare gli schemi di origine e di destinazione offline ed eseguire il monitoraggio e l'aggiornamento prima di poter pubblicare lo schema per SQL Server nella macchina virtuale di Azure.


## <a name="migrate"></a>Migrazione

Dopo aver completato la valutazione dei database e corretto eventuali discrepanze, il passaggio successivo consiste nell'eseguire il processo di migrazione.

Per pubblicare lo schema ed eseguire la migrazione dei dati, seguire questa procedura:

1. Pubblicare lo schema: fare clic con il pulsante destro del mouse sul database nel nodo **database** in **Esplora metadati SQL Server** e scegliere **Sincronizza con database**:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/synchronize-with-database.png" alt-text="Fare clic con il pulsante destro del mouse sul database e scegliere Synchronize with Database (Sincronizza con il database)":::

1. Migrare i dati: fare clic con il pulsante destro del mouse sul database o sull'oggetto di cui si vuole eseguire la migrazione in **Esplora metadati DB2** e scegliere **Migrate data**. In alternativa, è possibile selezionare **migrare i dati** dalla barra di spostamento in alto a linea. Per eseguire la migrazione dei dati per un intero database, selezionare la casella di controllo accanto al nome del database. Per eseguire la migrazione dei dati da singole tabelle, espandere il database, espandere tabelle, quindi selezionare la casella di controllo accanto alla tabella. Per omettere i dati dalle singole tabelle, deselezionare la casella di controllo:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/migrate-data.png" alt-text="Fare clic con il pulsante destro del mouse sullo schema e scegliere Migrate Data (Esegui migrazione dati)":::

1. Specificare i dettagli della connessione per le istanze di DB2 e SQL Server. 
1. Al termine della migrazione, visualizzare il **report di migrazione dei dati**:  

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/data-migration-report.png" alt-text="Esaminare il report di migrazione dei dati":::

1.  Connettersi al SQL Server nell'istanza di VM di Azure usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e convalidare la migrazione esaminando i dati e lo schema:

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-schema-in-ssms.png" alt-text="Confrontare lo schema in SSMS":::

## <a name="post-migration"></a>Post-migrazione 

Dopo aver completato la fase di migrazione, è necessario eseguire una serie di attività post-migrazione per assicurarsi che tutto funzioni nel modo più corretto ed efficiente possibile.

### <a name="remediate-applications"></a>Correggere le applicazioni 

Dopo la migrazione dei dati nell'ambiente di destinazione, tutte le applicazioni che in precedenza usavano l'origine devono iniziare a usare la destinazione. Per ottenere questo risultato, in alcuni casi sarà necessario apportare modifiche alle applicazioni.

### <a name="perform-tests"></a>Eseguire test

L'approccio di test per la migrazione del database prevede le attività seguenti:

1. **Sviluppare i test di convalida**: per testare la migrazione del database, è necessario usare query SQL. È necessario creare le query di convalida da eseguire sia sul database di origine che su quello di destinazione. Le query di convalida devono essere estese all'ambito definito.
1. **Configurare un ambiente di test**: l'ambiente di test deve contenere una copia del database di origine e del database di destinazione. Assicurarsi di isolare l'ambiente di test.
1. **Eseguire test di convalida**: eseguire i test di convalida sull'origine e sulla destinazione, quindi analizzare i risultati.
1. **Eseguire test delle prestazioni**: eseguire test delle prestazioni sull'origine e sulla destinazione, quindi analizzare e confrontare i risultati.


## <a name="migration-assets"></a>Risorse per la migrazione 

Per ulteriore assistenza, vedere le risorse seguenti, che sono state sviluppate a supporto di un progetto di migrazione reale:

|Asset  |Descrizione  |
|---------|---------|
|[Strumento e modello di valutazione dei carichi di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Questo strumento indica le piattaforme di destinazione "più idonee" suggerite, la preparazione per il cloud e il livello di correzione di applicazioni/database per un determinato carico di lavoro. Offre funzionalità semplici e accessibili con un solo clic per l'esecuzione di calcoli e la generazione di report, che consentono di accelerare le valutazioni in ambienti estesi grazie a un processo decisionale automatizzato e uniforme per la piattaforma di destinazione.|
|[Pacchetto di individuazione e valutazione degli asset di dati DB2 zOS](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Dopo aver eseguito lo script SQL in un database, è possibile esportare i risultati in un file nel file system. Sono supportati diversi formati di file, incluso il formato CSV, in modo che sia possibile acquisire i risultati in strumenti esterni come i fogli di calcolo. Questo metodo può essere utile se si vogliono condividere facilmente i risultati con i team che non hanno installato il workbench.|
|[Script e artefatti di inventario per IBM DB2 LUW](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20Db2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Questa risorsa include una query SQL che raggiunge le tabelle di sistema IBM DB2 LUW versione 11,1 e fornisce un conteggio degli oggetti in base al tipo di schema e di oggetto, una stima approssimativa dei dati non elaborati in ogni schema e il dimensionamento delle tabelle in ogni schema, con i risultati archiviati in formato CSV.|
|[Guida all'installazione di DB2 LUW pure scale in Azure](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/db2%20PureScale%20on%20Azure.pdf)|Questa guida funge da punto di partenza per un piano di implementazione DB2. I requisiti varieranno da azienda ad azienda, ma lo stesso modello di base vale per tutte. Questo modello di architettura può essere usato anche per le applicazioni OLAP in Azure.|

Le risorse sono state sviluppate dal team di progettazione di SQL Data. La carta di base di questo team consente di sbloccare e accelerare la modernizzazione complessa per i progetti di migrazione della piattaforma dati alla piattaforma dati di Microsoft Azure.

## <a name="next-steps"></a>Passaggi successivi

Dopo la migrazione, vedere [Guida di ottimizzazione e convalida post-migrazione](/sql/relational-databases/post-migration-validation-and-optimization-guide). 

Per un elenco dei servizi e degli strumenti di Microsoft e di terze parti disponibili per agevolare diversi scenari di migrazione di database e dati, nonché per attività speciali, vedere [Strumenti e servizi per la migrazione dei dati](../../../dms/dms-tools-matrix.md).

Per altre guide alla migrazione, vedere [Migrazione dei database](https://datamigration.microsoft.com/). 

Per contenuti video, vedere:
- [Panoramica del percorso di migrazione](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)