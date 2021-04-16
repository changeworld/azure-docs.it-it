---
title: 'Sap ASE to database SQL di Azure: Migration guide'
description: In questa guida si apprenderà come eseguire la migrazione dei database dell'ambiente del servizio app SAP a un database Azure SQL usando SQL Server Migration Assistant per SAP Adapter Server Enterprise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 0538071ffb9d244fb8b3493d6b63b27c6b56a726
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388534"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Guida alla migrazione: sap ase to database SQL di Azure

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Questa guida illustra [](https://azure.microsoft.com/migration/migration-journey) come eseguire la migrazione dei database SAP Adapter Server Enterprise (ASE) a un database Azure SQL usando [SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant per SAP Adapter Server Enterprise.

Per altre guide alla migrazione, vedere Guida [alla migrazione del database di Azure](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Prerequisiti 

Prima di iniziare la migrazione del database SAP SE al database SQL, eseguire le operazioni seguenti:

- Verificare che l'ambiente di origine sia supportato. 
- Scaricare e installare SQL Server Migration Assistant [per SAP Adaptive Server Enterprise (in precedenza SAP Sybase ASE).](https://www.microsoft.com/en-us/download/details.aspx?id=54256)
- Assicurarsi di avere connettività e autorizzazioni sufficienti per accedere sia all'origine che alla destinazione.

## <a name="pre-migration"></a>Pre-migrazione

Dopo aver soddisfatto i prerequisiti, è possibile individuare la topologia dell'ambiente e valutare la fattibilità della migrazione [cloud di Azure.](https://azure.microsoft.com/migration)

### <a name="assess"></a>Valutare

Usando [SQL Server Migration Assistant (SSMA) per SAP Adaptive Server Enterprise (formalmente SAP Sybase ASE),](https://www.microsoft.com/en-us/download/details.aspx?id=54256)è possibile esaminare gli oggetti di database e i dati, valutare i database per la migrazione, eseguire la migrazione di oggetti di database Sybase al database SQL e quindi eseguire la migrazione dei dati al database SQL. Per altre informazioni, vedere [SQL Server Migration Assistant per Sybase (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql).

Per creare una valutazione, eseguire le operazioni seguenti: 

1. Aprire SSMA per Sybase. 
1. Selezionare **File** e quindi Nuovo **progetto.** 
1. Nel riquadro **Nuovo progetto** immettere un nome e un percorso  per il progetto e quindi nell'elenco a discesa Migra a **selezionare** database SQL di Azure . 
1. Selezionare **OK**.
1. Nel riquadro **Connetti a Sybase** immettere i dettagli della connessione SAP. 
1. Fare clic con il pulsante destro del mouse sul database SAP di cui si vuole eseguire la migrazione e quindi **scegliere Crea report**. Verrà generato un report HTML. In alternativa, è possibile selezionare la **scheda Crea report** in alto a destra.
1. Esaminare il report HTML per comprendere le statistiche di conversione e gli eventuali errori o avvisi. È anche possibile aprire il report in Excel per ottenere un inventario degli oggetti SAP ASE e il lavoro richiesto per eseguire le conversioni dello schema. La posizione predefinita del report è la cartella report all'interno di SSMAProjects. Ad esempio:

   `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>` 

### <a name="validate-the-type-mappings"></a>Convalidare i mapping dei tipi

Prima di eseguire la conversione dello schema, convalidare i mapping dei tipi di dati predefiniti o modificarli in base ai requisiti. A tale scopo, selezionare Strumenti Impostazioni progetto oppure modificare il mapping dei tipi per ogni tabella selezionando la tabella in Esplora metadati  >  DI **SAP ASE.**

### <a name="convert-the-schema"></a>Convertire lo schema

Per convertire lo schema, eseguire le operazioni seguenti:

1. (Facoltativo) Per convertire query dinamiche o specializzate, fare clic con il pulsante destro del mouse sul nodo e quindi **scegliere Aggiungi istruzione**. 
1. Selezionare la **scheda Connetti database SQL di Azure** e quindi immettere i dettagli per il database SQL. È possibile scegliere di connettersi a un database esistente o specificare un nuovo nome, nel qual caso verrà creato un database nel server di destinazione.
1. Nel riquadro **Sybase Metadata Explorer** fare clic con il pulsante destro del mouse sullo schema SAP ASE in uso e quindi **scegliere Convert Schema (Converti schema).** 
1. Dopo la conversione dello schema, confrontare ed esaminare la struttura convertita nella struttura originale per identificare potenziali problemi. 

   Dopo la conversione dello schema, è possibile salvare il progetto in locale per un esercizio di correzione dello schema offline. A tale scopo, selezionare **File**  >  **Save Project (Salva progetto).** In questo modo è possibile valutare gli schemi di origine e di destinazione offline ed eseguire la correzione prima di pubblicare lo schema nel database SQL.

1. Nel riquadro **Output** selezionare Verifica **risultati** ed esaminare eventuali errori nel **riquadro Elenco** errori. 
1. Salvare il progetto in locale per un esercizio di correzione dello schema offline. A tale scopo, selezionare **File**  >  **Save Project (Salva progetto).** In questo modo è possibile valutare gli schemi di origine e di destinazione offline ed eseguire la correzione prima di pubblicare lo schema nel database SQL.

## <a name="migrate-the-databases"></a>Eseguire la migrazione dei database 

Dopo aver creato i prerequisiti necessari e aver completato le attività associate alla fase *di pre-migrazione,* è possibile eseguire lo schema e la migrazione dei dati.

Per pubblicare lo schema ed eseguire la migrazione dei dati, eseguire le operazioni seguenti: 

1. Pubblicare lo schema. Nel riquadro **database SQL di Azure Visualizzatore metadati** fare clic con il pulsante destro del mouse sul database e quindi scegliere **Sincronizza con database**. Questa azione pubblica lo schema SAP ASE nel database SQL.

1. Eseguire la migrazione dei dati. Nel riquadro **Esplora metadati SAP ASE** fare clic con il pulsante destro del mouse sul database o sull'oggetto SAP ASE di cui si vuole eseguire la migrazione e quindi scegliere Esegui migrazione **dati**. In alternativa, è possibile selezionare la **scheda Esegui** migrazione dati in alto a destra. 

   Per eseguire la migrazione dei dati per un intero database, selezionare la casella di controllo accanto al nome del database. Per eseguire la migrazione dei dati da singole tabelle, espandere il database, espandere **Tabelle** e quindi selezionare la casella di controllo accanto alla tabella. Per omettere dati da singole tabelle, deselezionare la casella di controllo . 
1. Al termine della migrazione, visualizzare il **report di migrazione dei dati**. 
1. Convalidare la migrazione esaminando i dati e lo schema. A tale scopo, connettersi al database SQL [usando](/sql/ssms/download-sql-server-management-studio-ssms)SQL Server Management Studio .

## <a name="post-migration"></a>Post-migrazione 

Dopo aver completato la  fase di migrazione, è necessario completare una serie di attività post-migrazione per garantire che tutto funzioni nel modo più uniforme ed efficiente possibile.

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


## <a name="next-steps"></a>Passaggi successivi

- Per una matrice di servizi e strumenti Microsoft e di terze parti disponibili per facilitare vari scenari di migrazione di database e dati e attività speciali, vedere Service and tools for data migration ( Servizio e strumenti per la migrazione [dei dati).](../../../dms/dms-tools-matrix.md)

- Per altre informazioni sulle database SQL di Azure, vedere:
   - [Panoramica del database SQL](../../database/sql-database-paas-overview.md)
   - [Calcolatore del costo totale di proprietà di Azure](https://azure.microsoft.com/pricing/tco/calculator/)  

- Per altre informazioni sul framework e sul ciclo di adozione per le migrazioni cloud, vedere:
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per la determinazione dei costi e il dimensionamento dei carichi di lavoro per la migrazione ad Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Migrazione cloud risorse](https://azure.microsoft.com/migration/resources)

- Per valutare il livello di accesso all'applicazione, [vedere Data Access Migration Toolkit (anteprima)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Per informazioni dettagliate su come eseguire i test A/B del livello di accesso [ai dati, vedere Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
