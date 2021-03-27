---
title: 'Da SAP ASE a database SQL di Azure: Guida alla migrazione'
description: Questa guida illustra come eseguire la migrazione dei database SAP ASE a un database SQL di Azure usando SQL Server Migration Assistant per SAP Adapter Server Enterprise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 138a23b610ab96194424bb0f88cf94f516c2d223
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626453"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Guida alla migrazione: SAP ASE nel database SQL di Azure

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Questa guida illustra come eseguire la migrazione dei database di SAP Adapter Server Enterprise (ASE) a un database SQL di Azure usando SQL Server Migration Assistant per SAP Adapter Server Enterprise.

Per altre guide alla migrazione, vedere [Guida alla migrazione del database di Azure](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Prerequisiti 

Prima di iniziare la migrazione del database SAP SE al database SQL, procedere come segue:

- Verificare che l'ambiente di origine sia supportato. 
- Scaricare e installare [SQL Server Migration Assistant per SAP Adaptive Server Enterprise (in precedenza SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256).
- Assicurarsi di disporre di connettività e di autorizzazioni sufficienti per accedere sia all'origine che alla destinazione.

## <a name="pre-migration"></a>Pre-migrazione

Dopo aver soddisfatto i prerequisiti, si è pronti per individuare la topologia dell'ambiente e valutare la fattibilità della migrazione.

### <a name="assess"></a>Valutare

Usando [SQL Server Migration Assistant (SSMA) per SAP Adaptive Server Enterprise (formalmente SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256), è possibile esaminare i dati e gli oggetti di database, valutare i database per la migrazione, migrare gli oggetti di database Sybase nel database SQL e quindi migrare i dati nel database SQL. Per ulteriori informazioni, vedere [SQL Server Migration Assistant per Sybase (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql).

Per creare una valutazione, procedere come segue: 

1. Aprire SSMA per Sybase. 
1. Selezionare **file**, quindi fare clic su **nuovo progetto**. 
1. Nel riquadro **nuovo progetto** immettere un nome e un percorso per il progetto e quindi nell'elenco a discesa **migra a** selezionare **database SQL di Azure**. 
1. Selezionare **OK**.
1. Nel riquadro **Connetti a Sybase** immettere i dettagli della connessione SAP. 
1. Fare clic con il pulsante destro del mouse sul database SAP di cui si desidera eseguire la migrazione e quindi scegliere **Crea report**. Verrà generato un report HTML. In alternativa, è possibile selezionare la scheda **Crea report** in alto a destra.
1. Esaminare il report HTML per comprendere le statistiche di conversione e gli eventuali errori o avvisi. È anche possibile aprire il report in Excel per ottenere un inventario degli oggetti di SAP ASE e lo sforzo necessario per eseguire le conversioni dello schema. La posizione predefinita del report è la cartella report all'interno di SSMAProjects. Ad esempio:

   `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>` 

### <a name="validate-the-type-mappings"></a>Convalidare i mapping dei tipi

Prima di eseguire la conversione dello schema, convalidare i mapping del tipo di dati predefiniti o modificarli in base ai requisiti. Questa operazione può essere eseguita selezionando **strumenti**  >  **Impostazioni progetto** oppure è possibile modificare il mapping dei tipi per ogni tabella selezionando la tabella in **Esplora metadati di SAP ASE**.

### <a name="convert-the-schema"></a>Convertire lo schema

Per convertire lo schema, procedere come segue:

1. Opzionale Per convertire query dinamiche o specializzate, fare clic con il pulsante destro del mouse sul nodo, quindi scegliere **Aggiungi istruzione**. 
1. Selezionare la scheda **Connetti a database SQL di Azure** e quindi immettere i dettagli per il database SQL. È possibile scegliere di connettersi a un database esistente o specificare un nuovo nome, nel qual caso verrà creato un database nel server di destinazione.
1. Nel riquadro **Sybase Metadata Explorer** fare clic con il pulsante destro del mouse sullo schema SAP ASE che si sta utilizzando, quindi selezionare **Convert schema**. 
1. Dopo la conversione dello schema, confrontare ed esaminare la struttura convertita nella struttura originale per identificare i potenziali problemi. 

   Dopo la conversione dello schema, è possibile salvare il progetto localmente per un esercizio di correzione dello schema offline. A tale scopo, selezionare **file**  >  **Salva progetto**. In questo modo è possibile valutare gli schemi di origine e di destinazione offline ed eseguire la correzione prima di pubblicare lo schema nel database SQL.

1. Nel riquadro **output** selezionare **Verifica risultati** ed esaminare gli eventuali errori nel riquadro **Elenco errori** . 
1. Salvare il progetto in locale per un esercizio di correzione dello schema offline. A tale scopo, selezionare **file**  >  **Salva progetto**. In questo modo è possibile valutare gli schemi di origine e di destinazione offline ed eseguire la correzione prima di pubblicare lo schema nel database SQL.

## <a name="migrate-the-databases"></a>Eseguire la migrazione dei database 

Una volta soddisfatti i prerequisiti necessari e aver completato le attività associate alla fase di *pre-migrazione* , è possibile eseguire lo schema e la migrazione dei dati.

Per pubblicare lo schema ed eseguire la migrazione dei dati, eseguire le operazioni seguenti: 

1. Pubblicare lo schema. Nel riquadro **Esplora metadati del database SQL di Azure** , fare clic con il pulsante destro del mouse sul database, quindi scegliere **Sincronizza con database**. Questa azione pubblica lo schema di SAP ASE nel database SQL.

1. Eseguire la migrazione dei dati. Nel riquadro di **Esplora metadati di SAP ASE** , fare clic con il pulsante destro del mouse sul database o sull'oggetto SAP ASE di cui si vuole eseguire la migrazione, quindi selezionare **Migrate data**. In alternativa, è possibile selezionare la scheda **migrazione dati** in alto a destra. 

   Per eseguire la migrazione dei dati per un intero database, selezionare la casella di controllo accanto al nome del database. Per eseguire la migrazione dei dati da singole tabelle, espandere il database, espandere **tabelle**, quindi selezionare la casella di controllo accanto alla tabella. Per omettere i dati dalle singole tabelle, deselezionare la casella di controllo. 
1. Al termine della migrazione, visualizzare il **report di migrazione dei dati**. 
1. Convalidare la migrazione riesaminando i dati e lo schema. A tale scopo, connettersi al database SQL utilizzando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

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


## <a name="next-steps"></a>Passaggi successivi

- Per una matrice di servizi e strumenti di Microsoft e di terze parti disponibili per agevolare diversi scenari di migrazione di database e dati e attività speciali, vedere [servizio e strumenti per la migrazione dei dati](../../../dms/dms-tools-matrix.md).

- Per ulteriori informazioni sul database SQL di Azure, vedere:
   - [Panoramica del database SQL](../../database/sql-database-paas-overview.md)
   - [Calcolatore costo totale di proprietà di Azure](https://azure.microsoft.com/pricing/tco/calculator/)  

- Per ulteriori informazioni sul Framework e il ciclo di adozione per le migrazioni cloud, vedere:
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per il costo e il dimensionamento dei carichi di lavoro per la migrazione ad Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Per valutare il livello di accesso dell'applicazione, vedere [Data Access Migration Toolkit (anteprima)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Per informazioni dettagliate su come eseguire i test A/B di livello di accesso ai dati, vedere [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
