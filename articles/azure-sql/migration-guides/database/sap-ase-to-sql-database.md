---
title: 'Da SAP ASE a database SQL di Azure: Guida alla migrazione'
description: Questa guida illustra come eseguire la migrazione dei database SAP ASE al database SQL di Azure usando SQL Server Migration Assistant per SAP Adapter Server Enterprise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 81956a16142f314f54afd9d5a1b9055a559e906c
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565081"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Guida alla migrazione: SAP ASE nel database SQL di Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Questa guida illustra come eseguire la migrazione dei database SAP ASE al database SQL di Azure usando SQL Server Migration Assistant per SAP Adapter Server Enterprise.

Per altre guide alla migrazione, vedere [Migrazione dei database](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Prerequisiti 

Per eseguire la migrazione del database SAP SE al database SQL di Azure, è necessario quanto segue:

- Verificare che l'ambiente di origine sia supportato. 
- [SQL Server Migration Assistant per SAP Adaptive Server Enterprise (in precedenza SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256). 

## <a name="pre-migration"></a>Pre-migrazione

Una volta soddisfatti i prerequisiti, si è pronti per individuare la topologia dell'ambiente e valutare la fattibilità della migrazione.

### <a name="assess"></a>Valutare

Usare [SQL Server Migration Assistant (SSMA) per SAP Adaptive Server Enterprise (formalmente SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256) per esaminare gli oggetti e i dati di database, valutare i database per la migrazione, migrare gli oggetti di database Sybase nel database SQL di Azure e quindi migrare i dati nel database SQL di Azure. Per ulteriori informazioni, vedere [SQL Server Migration Assistant per Sybase (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql).

Per creare una valutazione, seguire questa procedura: 

1. Aprire **SSMA per Sybase**. 
1. Selezionare **File** e quindi scegliere **Nuovo progetto**. 
1. Specificare un nome di progetto, un percorso in cui salvare il progetto e quindi selezionare database SQL di Azure come destinazione della migrazione dall'elenco a discesa. Selezionare **OK**.
1. Immettere i valori per i dettagli della connessione SAP nella finestra di dialogo **Connetti a Sybase** . 
1. Fare clic con il pulsante destro del mouse sul database SAP di cui si desidera eseguire la migrazione, quindi scegliere **Crea report**. Verrà generato un report HTML.
1. Leggere il report HTML per esaminare le statistiche di conversione e gli eventuali errori o avvisi. È anche possibile aprire il report in Excel per ottenere un inventario degli oggetti DB2 e dell'impegno necessario per eseguire le conversioni dello schema. La posizione predefinita del report è la cartella report all'interno di SSMAProjects.

   Ad esempio: `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`. 


### <a name="validate-type-mappings"></a>Convalida mapping dei tipi

Prima di eseguire la conversione dello schema, convalidare i mapping del tipo di dati predefiniti o modificarli in base ai requisiti. Questa operazione può essere eseguita passando al menu **strumenti** e scegliendo **Impostazioni progetto** oppure è possibile modificare il mapping dei tipi per ogni tabella selezionando la tabella in **Esplora metadati di SAP ASE**.


### <a name="convert-schema"></a>Converti schema

Per convertire lo schema, seguire questa procedura:

1. Opzionale Per convertire le query dinamiche o ad hoc, fare clic con il pulsante destro del mouse sul nodo e scegliere **Aggiungi istruzione**. 
1. Selezionare **Connetti a database SQL di Azure** nella barra di spostamento in alto a linee e specificare i dettagli del database SQL di Azure. È possibile scegliere di connettersi a un database esistente o specificare un nuovo nome, nel qual caso verrà creato un database nel server di destinazione.
1. Fare clic con il pulsante destro del mouse sullo schema SAP ASE in **Sybase Metadata Explorer** e scegliere **Converti schema**. In alternativa, è possibile selezionare **Converti schema** dalla barra di spostamento in alto a linee. 
1. Confrontare ed esaminare la struttura dello schema per identificare i potenziali problemi. 

   Dopo la conversione dello schema è possibile salvare il progetto localmente per un esercizio di correzione dello schema offline. Scegliere **Salva progetto** dal menu **File**. In questo modo è possibile valutare gli schemi di origine e di destinazione offline ed eseguire la correzione prima di poter pubblicare lo schema nel database SQL di Azure.

Per altre informazioni, vedere [Convert schema](/sql/ssma/sybase/converting-sybase-ase-database-objects-sybasetosql) .


## <a name="migrate"></a>Migrate 

Una volta soddisfatti i prerequisiti necessari e aver completato le attività associate alla fase di **pre-migrazione** , si è pronti per eseguire la migrazione dello schema e dei dati.

Per pubblicare lo schema ed eseguire la migrazione dei dati, attenersi alla procedura seguente: 

1. Fare clic con il pulsante destro del mouse sul database in **Esplora metadati del database SQL di Azure** e scegliere **Sincronizza con database**.  Questa azione pubblica lo schema di SAP ASE nell'istanza del database SQL di Azure.
1. Fare clic con il pulsante destro del mouse sullo schema SAP ASE in **SAP ASE Metadata Explorer** e scegliere **Migrate data**.  In alternativa, è possibile selezionare **migrare i dati** dalla barra di spostamento in alto a linea.  
1. Al termine della migrazione, visualizzare il **report di migrazione dei dati**: 
1. Convalidare la migrazione esaminando i dati e lo schema nell'istanza del database SQL di Azure usando il database SQL di Azure Management Studio (SSMS).


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

> [!NOTE]
> Per ulteriori dettagli su questi problemi e su passaggi specifici per attenuarli, vedere la [Guida alla convalida e all'ottimizzazione post-migrazione](/sql/relational-databases/post-migration-validation-and-optimization-guide).


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
