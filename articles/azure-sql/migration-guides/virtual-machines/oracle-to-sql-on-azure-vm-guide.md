---
title: 'Da Oracle a SQL Server in macchine virtuali di Azure: Guida alla migrazione'
description: In questa guida viene illustrato come eseguire la migrazione degli schemi Oracle a SQL Server in macchine virtuali di Azure utilizzando SQL Server Migration Assistant per Oracle.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: d4fb33e8e904d12e242f7eeaf9c2dc50a02eff4d
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961252"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-virtual-machines"></a>Guida alla migrazione: Oracle per SQL Server in macchine virtuali di Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

In questa guida viene illustrato come eseguire la migrazione degli schemi Oracle a SQL Server in macchine virtuali di Azure utilizzando SQL Server Migration Assistant per Oracle. 

Per altre guide alla migrazione, vedere [Migrazione dei database](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Prerequisiti 

Per eseguire la migrazione dello schema Oracle a SQL Server in macchine virtuali di Azure, è necessario quanto segue:

- Ambiente di origine supportato.
- [SQL Server Migration Assistant (SSMA) per Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- Una [macchina virtuale SQL Server](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md)di destinazione.
- Le [autorizzazioni necessarie per SSMA per Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) e il [provider](/sql/ssma/oracle/connect-to-oracle-oracletosql).
- Connettività e autorizzazioni sufficienti per accedere all'origine e alla destinazione. 


## <a name="pre-migration"></a>Pre-migrazione

Per preparare la migrazione al cloud, verificare che l'ambiente di origine sia supportato e che siano stati risolti tutti i prerequisiti. Questa operazione consentirà di garantire una migrazione efficiente e corretta.

Questa parte del processo implica: 
- Esecuzione di un inventario dei database di cui è necessario eseguire la migrazione.
- Valutazione di tali database per potenziali problemi o blocchi di migrazione. 
- Risoluzione di eventuali problemi individuati. 

### <a name="discover"></a>Rilevazione

Usare [MAP Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) per identificare le origini dati esistenti e i dettagli sulle funzionalità usate dall'azienda. Questa operazione consentirà di ottenere una migliore comprensione della migrazione e di consentirne la pianificazione. Questo processo comporta l'analisi della rete per identificare le istanze Oracle dell'organizzazione e le versioni e le funzionalità in uso.

Per usare MAP Toolkit per eseguire un'analisi dell'inventario, seguire questa procedura: 


1. Aprire [MAP Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883).


1. Selezionare **Crea/Seleziona database**:

   ![Screenshot che mostra l'opzione Crea/Seleziona database.](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. Selezionare **Crea un database di inventario**. Immettere un nome per il nuovo database di inventario che si sta creando, fornire una breve descrizione e quindi selezionare **OK**: 

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="Screenshot che mostra l'interfaccia per la creazione di un database di inventario.":::

1. Selezionare **Raccogli dati di inventario** per aprire la **procedura guidata di inventario e valutazione**:

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="Screenshot che mostra il collegamento Raccogli dati di inventario.":::


1. Nella **procedura guidata di inventario e valutazione** selezionare **Oracle** e quindi fare clic su **Avanti**:

   ![Screenshot che mostra la pagina scenari di inventario della procedura guidata di inventario e valutazione.](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. Selezionare l'opzione di ricerca computer più adatta alle esigenze e all'ambiente aziendali, quindi selezionare **Avanti**: 

   ![Screenshot che mostra la pagina metodi di individuazione della procedura guidata di inventario e valutazione.](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. Immettere le credenziali o creare nuove credenziali per i sistemi che si desidera esplorare, quindi selezionare **Avanti**:

    ![Screenshot che mostra la pagina delle credenziali di tutti i computer della procedura guidata di inventario e valutazione.](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)


1. Impostare l'ordine delle credenziali, quindi selezionare **Avanti**: 

   ![Screenshot che mostra la pagina relativa all'ordine delle credenziali della procedura guidata di inventario e valutazione.](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  


1. Immettere le credenziali per ogni computer che si desidera individuare. È possibile usare credenziali univoche per ogni computer o computer oppure è possibile usare l'elenco di credenziali tutti i computer.  

   ![Screenshot che mostra la pagina specificare i computer e le credenziali della procedura guidata di inventario e valutazione.](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. Verificare le selezioni e quindi fare clic su **fine**:

   ![Screenshot che mostra la pagina Riepilogo della procedura guidata di inventario e valutazione.](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)


1. Al termine dell'analisi, visualizzare il riepilogo della **raccolta dati** . L'analisi potrebbe richiedere alcuni minuti, a seconda del numero di database. Al termine, selezionare **Chiudi** : 

   ![Screenshot che mostra il riepilogo della raccolta dei dati.](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. Selezionare le **Opzioni** per generare un report sui dettagli relativi a Oracle assessment e database. Selezionare entrambe le opzioni, una alla volta, per generare il report.


### <a name="assess"></a>Valutare

Dopo aver identificato le origini dati, utilizzare [SQL Server Migration Assistant per Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) per valutare le istanze Oracle che eseguono la migrazione alla VM SQL Server. L'assistente aiuterà a comprendere i gap tra i database di origine e di destinazione. È possibile esaminare gli oggetti e i dati di database, valutare i database per la migrazione, migrare oggetti di database SQL Server e quindi migrare i dati SQL Server.

Per creare una valutazione, seguire questa procedura: 


1. Aprire [SQL Server Migration Assistant per Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Scegliere **Nuovo progetto** dal menu **File**. 
1. Specificare un nome di progetto e un percorso per il progetto, quindi selezionare un SQL Server destinazione della migrazione dall'elenco. Selezionare **OK**: 

   ![Screenshot che mostra la finestra di dialogo nuovo progetto.](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)


1. Selezionare **Connetti a Oracle**. Immettere i valori per la connessione Oracle nella finestra di dialogo **Connetti a Oracle** :

   ![Screenshot che mostra la finestra di dialogo Connetti a Oracle.](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   Selezionare gli schemi Oracle di cui si desidera eseguire la migrazione: 

   ![Screenshot che mostra l'elenco degli schemi Oracle di cui è possibile eseguire la migrazione.](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)


1. In **Oracle Metadata Explorer** fare clic con il pulsante destro del mouse sullo schema Oracle di cui si desidera eseguire la migrazione e quindi scegliere **Crea report**. In questo modo viene generato un report HTML. In alternativa, è possibile selezionare il database e quindi fare clic su **Crea report** nel menu in alto.

   ![Screenshot che illustra come creare un report.](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. Esaminare il report HTML per statistiche di conversione, errori e avvisi. Analizzarlo per comprendere problemi di conversione e soluzioni.

    È inoltre possibile aprire il report in Excel per ottenere un inventario degli oggetti Oracle e lo sforzo necessario per completare le conversioni dello schema. Il percorso predefinito per il report è la cartella report in SSMAProjects. 

   ad esempio `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`


   ![Screenshot che mostra un report di conversione.](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)


### <a name="validate-data-types"></a>Convalidare i tipi di dati

Verificare i mapping dei tipi di dati predefiniti e modificarli in base ai requisiti, se necessario. A questo scopo, attenersi alla procedura seguente: 


1. Scegliere **Impostazioni progetto** dal menu **strumenti** . 
1. Selezionare la scheda **mapping dei tipi** . 

   ![Screenshot che mostra la scheda mapping dei tipi.](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. È possibile modificare il mapping dei tipi per ogni tabella selezionando la tabella in **Oracle Metadata Explorer**. 

### <a name="convert-the-schema"></a>Convertire lo schema

Per convertire lo schema, seguire questa procedura: 

1. Opzionale Per convertire le query dinamiche o ad hoc, fare clic con il pulsante destro del mouse sul nodo e scegliere **Aggiungi istruzione**.

1. Selezionare **Connetti a SQL Server** nel menu in alto. 
     1. Immettere i dettagli della connessione per il SQL Server nella macchina virtuale di Azure. 
     1. Selezionare il database di destinazione dall'elenco o specificare un nuovo nome. Se si specifica un nuovo nome, verrà creato un database nel server di destinazione. 
     1. Fornire i dettagli di autenticazione. 
     1. Selezionare **Connetti**. 


   ![Screenshot che illustra come connettersi a SQL Server.](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. Fare clic con il pulsante destro del mouse sullo schema Oracle in **Oracle Metadata Explorer** e scegliere **Converti schema**. In alternativa, è possibile selezionare **Converti schema** nel menu superiore:

   ![Screenshot che illustra come convertire lo schema.](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)


1. Al termine della conversione dello schema, esaminare gli oggetti convertiti e confrontarli con quelli originali per identificare i potenziali problemi. Usare i consigli per risolvere i problemi:

   ![Screenshot che mostra un confronto tra due schemi.](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   Confrontare il testo Transact-SQL convertito con le stored procedure originali ed esaminare le indicazioni: 

   ![Screenshot che mostra Transact-SQL, stored procedure e un avviso.](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   È possibile salvare il progetto localmente per un esercizio di correzione dello schema offline. A tale scopo, selezionare **Salva progetto** dal menu **file** . Il salvataggio del progetto in locale consente di valutare gli schemi di origine e di destinazione offline ed eseguire la correzione prima di pubblicare lo schema in SQL Server.

1. Selezionare **Verifica risultati** nel riquadro **output** , quindi esaminare gli errori nel riquadro **Elenco errori** . 
1. Salvare il progetto in locale per un esercizio di correzione dello schema offline. Scegliere **Salva progetto** dal menu **file** . In questo modo è possibile valutare gli schemi di origine e di destinazione offline ed eseguire il monitoraggio e l'aggiornamento prima di pubblicare lo schema per SQL Server in macchine virtuali di Azure.


## <a name="migrate"></a>Migrate

Una volta soddisfatti i prerequisiti necessari e aver completato le attività associate alla fase di pre-migrazione, si è pronti per avviare lo schema e la migrazione dei dati. La migrazione prevede due passaggi: la pubblicazione dello schema e la migrazione dei dati. 


Per pubblicare lo schema ed eseguire la migrazione dei dati, attenersi alla procedura seguente: 

1. Pubblicare lo schema: fare clic con il pulsante destro del mouse sul database in **SQL Server Esplora metadati** e selezionare **Sincronizza con database**. In questo modo, lo schema Oracle viene pubblicato per SQL Server in macchine virtuali di Azure. 

   ![Screenshot che mostra il comando Sincronizza con database.](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   Esaminare il mapping tra il progetto di origine e la destinazione:

   ![Screenshot che mostra lo stato di sincronizzazione.](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)



1. Eseguire la migrazione dei dati: fare clic con il pulsante destro del mouse sul database o sull'oggetto di cui si desidera eseguire la migrazione in **Oracle Metadata Explorer** e selezionare **Migrate data**. In alternativa, è possibile selezionare **migrare i dati** nel menu in alto.

   Per eseguire la migrazione dei dati per un intero database, selezionare la casella di controllo accanto al nome del database. Per eseguire la migrazione dei dati da singole tabelle, espandere il database, espandere **tabelle**, quindi selezionare la casella di controllo accanto alla tabella. Per omettere i dati dalle singole tabelle, deselezionare le caselle di controllo appropriate.

   ![Screenshot che mostra il comando Migrate data.](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. Specificare i dettagli di connessione per Oracle e SQL Server in macchine virtuali di Azure nella finestra di dialogo.
1. Al termine della migrazione, visualizzare il **report di migrazione dei dati**:

    ![Screenshot che mostra il report di migrazione dei dati.](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. Connettersi al SQL Server nell'istanza di macchine virtuali di Azure usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Convalidare la migrazione esaminando i dati e lo schema:


   ![Screenshot che mostra un'istanza di SQL Server in SSMA.](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)

Invece di usare SSMA, è possibile usare SQL Server Integration Services (SSIS) per eseguire la migrazione dei dati. Per altre informazioni, vedere: 
- L'articolo [SQL Server Integration Services](https://docs.microsoft.com//sql/integration-services/sql-server-integration-services).
- Il white paper [SSIS per Azure e lo spostamento di dati ibridi](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx).


## <a name="post-migration"></a>Post-migrazione 

Dopo aver completato la fase di migrazione, è necessario completare una serie di attività post-migrazione per assicurarsi che tutto sia in esecuzione nel modo più semplice ed efficiente possibile.

### <a name="remediate-applications"></a>Correggere le applicazioni

Dopo la migrazione dei dati nell'ambiente di destinazione, tutte le applicazioni che in precedenza avevano utilizzato l'origine devono iniziare a utilizzare la destinazione. Per apportare tali modifiche potrebbero essere necessarie modifiche alle applicazioni.

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) è un'estensione per Visual Studio Code. Consente di analizzare il codice sorgente Java e rilevare le query e le chiamate API di accesso ai dati. Il Toolkit fornisce una visualizzazione a un singolo riquadro degli elementi che devono essere risolti per supportare il nuovo back-end del database. Per altre informazioni, vedere [eseguire la migrazione dell'applicazione Java da Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727). 

### <a name="perform-tests"></a>Eseguire test

Per testare la migrazione del database, completare le attività seguenti:

1. **Sviluppare i test di convalida**. Per testare la migrazione del database, è necessario usare le query SQL. Creare le query di convalida per l'esecuzione nei database di origine e di destinazione. Le query di convalida devono coprire l'ambito definito.

2. **Configurare un ambiente di test**. L'ambiente di test deve contenere una copia del database di origine e del database di destinazione. Assicurarsi di isolare l'ambiente di test.

3. **Eseguire i test di convalida**. Eseguire i test di convalida sull'origine e sulla destinazione, quindi analizzare i risultati.

4. **Eseguire test delle prestazioni**. Eseguire test delle prestazioni sull'origine e sulla destinazione, quindi analizzare e confrontare i risultati.

### <a name="optimize"></a>Ottimizzazione

La fase post-migrazione è fondamentale per riconciliare eventuali problemi di accuratezza dei dati e verificare la completezza. È anche fondamentale per risolvere i problemi di prestazioni con il carico di lavoro.

> [!Note]
> Per ulteriori informazioni su questi problemi e sui passaggi specifici per attenuarli, vedere la [Guida alla convalida e all'ottimizzazione post-migrazione](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-resources"></a>Risorse per la migrazione 

Per ulteriori informazioni sul completamento di questo scenario di migrazione, vedere le risorse seguenti, che sono state sviluppate per supportare un progetto di migrazione reale.

| **Titolo/collegamento**                                                                                                                                          | **Descrizione**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Strumento e modello di valutazione del carico di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Questo strumento fornisce le piattaforme di destinazione più adatte, la preparazione per il cloud e i livelli di monitoraggio e aggiornamento dell'applicazione/database per un determinato carico di lavoro. Offre un semplice calcolo con un solo clic e una generazione di report che consente di accelerare le valutazioni di grandi dimensioni fornendo un processo decisionale di piattaforma di destinazione automatizzato e uniforme.                                                          |
| [Elementi di script di inventario Oracle](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Questo asset include una query PL/SQL destinata alle tabelle di sistema Oracle e fornisce un conteggio degli oggetti in base al tipo di schema, al tipo di oggetto e allo stato. Fornisce inoltre una stima approssimativa dei dati non elaborati in ogni schema e il dimensionamento delle tabelle in ogni schema, con risultati archiviati in formato CSV.                                                                                                               |
| [Automatizzare la raccolta SSMA Oracle Assessment & il consolidamento](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Questo set di risorse utilizza un file CSV come voce (sources.csv nelle cartelle del progetto) per produrre i file XML necessari per eseguire una valutazione SSMA in modalità console. Per fornire il file di source.csv, è necessario eseguire un inventario delle istanze Oracle esistenti. I file di output sono AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml e VariableValueFile.xml.|
| [Problemi di SSMA e possibili soluzioni per la migrazione di database Oracle](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Con Oracle è possibile assegnare una condizione non scalare in una clausola WHERE. SQL Server non supporta questo tipo di condizione. Quindi SSMA per Oracle non converte le query con una condizione non scalare nella clausola WHERE. Genera invece un errore: O2SS0001. In questa white paper vengono fornite informazioni dettagliate sul problema e sui modi per risolverlo.          |
| [Manuale della migrazione da Oracle a SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Questo documento è incentrato sulle attività associate alla migrazione di uno schema Oracle alla versione più recente di SQL Server. Se la migrazione richiede modifiche a funzionalità o funzionalità, è necessario valutare con attenzione il possibile effetto di ogni modifica sulle applicazioni che utilizzano il database.                                                     |


Le risorse sono state sviluppate dal team di progettazione di SQL Data. La carta di base di questo team consente di sbloccare e accelerare la modernizzazione complessa per i progetti di migrazione della piattaforma dati alla piattaforma di dati Microsoft Azure.


## <a name="next-steps"></a>Passaggi successivi

- Per verificare la disponibilità dei servizi applicabili ai SQL Server, vedere il [centro di infrastruttura globale di Azure](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database).

- Per una matrice di servizi e strumenti di Microsoft e di terze parti disponibili per semplificare l'uso di diversi scenari di migrazione di database e dati e attività specializzate, vedere [Servizi e strumenti per la migrazione dei dati](../../../dms/dms-tools-matrix.md).

- Per altre informazioni su Azure SQL, vedere:
   - [Opzioni di distribuzione](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server in Macchine virtuali di Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Calcolatore costo totale di proprietà di Azure](https://azure.microsoft.com/pricing/tco/calculator/)


- Per ulteriori informazioni sul Framework e il ciclo di adozione per le migrazioni cloud, vedere:
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per i carichi di lavoro relativi a costi e dimensioni migrati in Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Per informazioni sulle licenze, vedere:
   - [Bring Your Own License con il Vantaggio Azure Hybrid](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Ottieni supporto esteso gratuito per SQL Server 2008 e SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- Per valutare il livello di accesso dell'applicazione, usare [l'anteprima di Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Per informazioni dettagliate su come eseguire il test a/B di livello di accesso ai dati, vedere [Panoramica di database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).


