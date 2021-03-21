---
title: Connetti a Azure Data Factory
description: Questo articolo descrive come connettere Azure Data Factory e competenza Azure per tenere traccia della derivazione dei dati.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/08/2021
ms.openlocfilehash: 8812806e535e8e34ca07fdb13e6223bfa0c91d6b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449612"
---
# <a name="how-to-connect-azure-data-factory-and-azure-purview"></a>Come connettersi Azure Data Factory e Azure

Questo documento illustra i passaggi necessari per connettere un account di Azure Data Factory a un account di Azure per tenere traccia della derivazione dei dati. Il documento entra inoltre nei dettagli dell'ambito di code coverage e dei modelli di derivazione supportati.

## <a name="view-existing-data-factory-connections"></a>Visualizzare le connessioni di Data Factory esistenti

Più data factory di Azure possono connettersi a un'unica Data Catalog di Azure per il push delle informazioni di derivazione. Il limite corrente consente di connettere fino a dieci account Data Factory alla volta dal centro di gestione di competenza. Per visualizzare l'elenco di account Data Factory connessi al Data Catalog di competenza, seguire questa procedura:

1. Selezionare **centro di gestione** nel riquadro di spostamento a sinistra.
2. In **connessioni esterne** selezionare **data factory connessione**.
3. Viene visualizzato l'elenco Data Factory Connection.

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="Screenshot che mostra un elenco di connessioni data factory." lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

4. Si notino i vari valori per **lo stato** della connessione:

    - **Connected**: la data factory è connessa a Data Catalog.
    - **Disconnesso**: il Data Factory ha accesso al catalogo, ma è connesso a un altro catalogo. Di conseguenza, la derivazione dei dati non verrà segnalata automaticamente al catalogo.
    - **CannotAccess**: l'utente corrente non ha accesso alla data factory, quindi lo stato della connessione è sconosciuto.
 >[!Note]
 >Per visualizzare le connessioni di Data Factory, è necessario assegnare uno dei ruoli di competenza:
 >- Collaboratore
 >- Proprietario
 >- Lettore
 >- Amministratore Accesso utenti

## <a name="create-new-data-factory-connection"></a>Crea nuova connessione Data Factory

>[!Note]
>Per aggiungere o rimuovere le connessioni di Data Factory, è necessario essere assegnati a uno dei ruoli di competenza:
>- Proprietario
>- Amministratore accessi utente
>
> Inoltre, è necessario che gli utenti siano il "proprietario" o "collaboratore" del data factory. 

Attenersi alla procedura seguente per connettere un account di Data Factory esistente al Data Catalog di competenza.

1. Selezionare **centro di gestione** nel riquadro di spostamento a sinistra.
2. In **connessioni esterne** selezionare **data factory connessione**.
3. Nella pagina **connessione Data Factory** selezionare **nuovo**.

4. Selezionare l'account Data Factory dall'elenco e fare clic su **OK**. È anche possibile filtrare in base al nome della sottoscrizione per limitare l'elenco.

    :::image type="content" source="./media/how-to-link-azure-data-factory/connect-data-factory.png" alt-text="Screenshot che illustra come connettersi Azure Data Factory." lightbox="./media/how-to-link-azure-data-factory/connect-data-factory.png":::

    Alcune istanze di Data Factory potrebbero essere disabilitate se il data factory è già connesso all'account di competenza corrente o se il data factory non ha un'identità gestita.

    Se una delle Data Factory selezionate è già connessa ad altri account di competenza, verrà visualizzato un messaggio di avviso. Selezionando OK, la connessione Data Factory con l'altro account di competenza verrà disconnessa. Non sono necessarie conferme aggiuntive.


    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Screenshot che mostra l'avviso per disconnettere Azure Data Factory." lightbox="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png":::

>[!Note]
>È ora supportata l'aggiunta di un massimo di 10 Data Factory contemporaneamente. Se si desidera aggiungere più di 10 Data Factory in una sola volta, inviare un ticket di supporto.

### <a name="how-does-the-authentication-work"></a>Funzionamento dell'autenticazione

Quando un utente di competenza registra un Data Factory a cui ha accesso, nel back-end si verifica quanto segue:

1. L' **identità gestita di data factory** viene aggiunta al ruolo controllo degli accessi in base al ruolo: responsabile **dati di competenza**.

    :::image type="content" source="./media/how-to-link-azure-data-factory/adf-msi.png" alt-text="Screenshot che mostra Azure Data Factory MSI." lightbox="./media/how-to-link-azure-data-factory/adf-msi.png":::
     
2. È necessario eseguire di nuovo la pipeline Data Factory in modo che i metadati di derivazione possano essere inseriti nuovamente in ambito.
3. Post-esecuzione i metadati del Data Factory vengono inseriti in ambito.

### <a name="remove-data-factory-connections"></a>Rimuovi connessioni data factory
Per rimuovere una connessione data factory, procedere come segue:

1. Nella pagina **connessione Data Factory** selezionare il pulsante **Rimuovi** accanto a una o più connessioni Data Factory.
2. Selezionare **conferma** nella finestra popup per eliminare le connessioni Data Factory selezionate.

    :::image type="content" source="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png" alt-text="Screenshot che illustra come selezionare le data factory per la rimozione della connessione." lightbox="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png":::

## <a name="configure-a-self-hosted-integration-runtime-to-collect-lineage"></a>Configurare un Integration Runtime self-hosted per raccogliere la derivazione

La derivazione per l'attività di copia Data Factory è disponibile per gli archivi dati locali come i database SQL. Se si esegue il runtime di integrazione self-hosted per lo spostamento dei dati con Azure Data Factory e si vuole acquisire la derivazione in Azure, verificare che la versione sia 5,0 o successiva. Per altre informazioni sul runtime di integrazione self-hosted, vedere [creare e configurare un runtime di integrazione self-hosted](../data-factory/create-self-hosted-integration-runtime.md).

## <a name="supported-azure-data-factory-activities"></a>Attività Azure Data Factory supportate

Azure competenza acquisisce la derivazione di runtime dalle attività Azure Data Factory seguenti:

- [Copia dati](../data-factory/copy-activity-overview.md)
- [Flusso di dati](../data-factory/concepts-data-flow-overview.md)
- [Esecuzione del pacchetto SSIS](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)

> [!IMPORTANT]
> Azure competenza Elimina la derivazione se l'origine o la destinazione usa un sistema di archiviazione dati non supportato.

L'integrazione tra Data Factory e la competenza supporta solo un subset dei sistemi dati supportati da Data Factory, come descritto nelle sezioni seguenti.

### <a name="data-factory-copy-activity-support"></a>Supporto per l'attività di copia Data Factory

| Archivio dati | Supportato | 
| ------------------- | ------------------- | 
| Archiviazione BLOB di Azure | Sì |
| Ricerca cognitiva di Azure | Sì | 
| Azure Cosmos DB (API SQL) \* | Sì | 
| API di Azure Cosmos DB per MongoDB \* | Sì |
| Esplora dati di Azure \* | Sì | 
| Azure Data Lake Storage Gen1 | Sì | 
| Azure Data Lake Storage Gen2 | Sì | 
| Database di Azure per Maria DB \* | Sì | 
| Database di Azure per MySQL \* | Sì | 
| Database di Azure per PostgreSQL \* | Sì |
| Archiviazione file di Azure | Sì | 
| Database SQL di Azure \* | Sì | 
| Istanza gestita SQL di Azure \* | Sì | 
| Analisi delle sinapsi di Azure \* | Sì | 
| Archiviazione tabelle di Azure | Sì |
| Amazon S3 | Sì | 
| Alveare \* | Sì | 
| SAP ECC \* | Sì |
| Tabella SAP | Sì |
| SQL Server \* | Sì | 
| Teradata \* | Sì |

*\* Azure competenze attualmente non supporta la query o la stored procedure per la derivazione o l'analisi. La derivazione è limitata solo alle origini tabella e vista.*

> [!Note]
> La funzionalità di derivazione presenta un certo sovraccarico delle prestazioni in Data Factory attività di copia. Per chi imposta data factory connessioni in ambito, è possibile osservare che alcuni processi di copia richiedono più tempo per essere completati. Per lo più, l'effetto è non trascurabile. Se i processi di copia importano un tempo molto più lungo rispetto al consueto, contattare il supporto tecnico.

#### <a name="known-limitations-on-copy-activity-lineage"></a>Limitazioni note sulla derivazione dell'attività di copia

Attualmente, se si usano le funzionalità dell'attività di copia seguenti, la derivazione non è ancora supportata:

- Copiare i dati in Azure Data Lake Storage Gen1 usando il formato binario.
- Copiare i dati in Azure sinapsi Analytics usando l'istruzione di base o di copia.
- Impostazione di compressione per file binari, delimitati di testo, Excel, JSON e XML.
- Opzioni di partizione di origine per database SQL di Azure, Azure SQL Istanza gestita, Azure sinapsi Analytics, SQL Server e tabella SAP.
- Opzione di individuazione partizione di origine per archivi basati su file.
- Copiare i dati nel sink basato su file con l'impostazione del numero massimo di righe per ogni file.
- Aggiungere altre colonne durante la copia.

### <a name="data-factory-data-flow-support"></a>Supporto del flusso di dati Data Factory

| Archivio dati | Supportato |
| ------------------- | ------------------- | 
| Archiviazione BLOB di Azure | Sì |
| Azure Data Lake Storage Gen1 | Sì |
| Azure Data Lake Storage Gen2 | Sì |
| Database SQL di Azure \* | Sì |
| Analisi delle sinapsi di Azure \* | Sì |

*\* Azure competenze attualmente non supporta la query o la stored procedure per la derivazione o l'analisi. La derivazione è limitata solo alle origini tabella e vista.*

### <a name="data-factory-execute-ssis-package-support"></a>Data Factory eseguire il supporto di pacchetti SSIS

| Archivio dati | Supportato |
| ------------------- | ------------------- |
| Archiviazione BLOB di Azure | Sì |
| Azure Data Lake Storage Gen1 | Sì |
| Azure Data Lake Storage Gen2 | Sì |
| Archiviazione file di Azure | Sì |
| Database SQL di Azure \* | Sì |
| Istanza gestita SQL di Azure \*| Sì |
| Analisi delle sinapsi di Azure \* | Sì |
| SQL Server \* | Sì |

*\* Azure competenze attualmente non supporta la query o la stored procedure per la derivazione o l'analisi. La derivazione è limitata solo alle origini tabella e vista.*

> [!Note]
> Azure Data Lake Storage Gen2 è ora disponibile a livello generale. È consigliabile iniziare a usarlo oggi stesso. Per altre informazioni, vedere la [pagina del prodotto](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="supported-lineage-patterns"></a>Modelli di derivazione supportati

Sono disponibili diversi modelli di derivazione supportati da Azure. I dati di derivazione generati sono basati sul tipo di origine e sink utilizzati nelle attività Data Factory. Sebbene Data Factory supporti più di 80 origine e sink, Azure ambito supporta solo un subset, come elencato in [attività Azure Data Factory supportate](#supported-azure-data-factory-activities).

Per configurare Data Factory per l'invio di informazioni di derivazione, vedere [Introduzione a](catalog-lineage-user-guide.md#get-started-with-lineage)derivazione.

Di seguito sono riportate alcune altre modalità di ricerca delle informazioni nella visualizzazione di derivazione:

- Nella scheda derivazione **passare il mouse** sulle forme per visualizzare in anteprima informazioni aggiuntive sull'asset nella descrizione comando.
- Selezionare il nodo o il bordo per visualizzare il tipo di asset a cui appartiene o per cambiare asset.
- Le colonne di un set di dati vengono visualizzate sul lato sinistro **della scheda di** derivazione. Per altre informazioni sulla derivazione a livello di colonna, vedere [DataSet Column](catalog-lineage-user-guide.md#dataset-column-lineage)Derivation.

### <a name="data-lineage-for-11-operations"></a>Derivazione dei dati per operazioni 1:1

Il modello più comune per l'acquisizione della derivazione dei dati consiste nello trasferire i dati da un singolo set di dati di input a un singolo set di dati di output, con un processo compreso tra.

Un esempio di questo modello è il seguente:

- 1 origine/input: *Customer* (tabella SQL)
- 1 sink/output: *Customer1.csv* (BLOB di Azure)
- 1 processo: *CopyCustomerInfo1 \#Customer1.csv* (Data Factory attività di copia)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage.png" alt-text="Screenshot che mostra la derivazione per un'operazione di copia da uno a uno Data Factory." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage.png":::

### <a name="data-movement-with-11-lineage-and-wildcard-support"></a>Spostamento dei dati con derivazione 1:1 e supporto di caratteri jolly

Un altro scenario comune per l'acquisizione della derivazione è l'uso di un carattere jolly per copiare i file da un singolo set di dati di input a un singolo set di dati Il carattere jolly consente all'attività di copia di trovare la corrispondenza con più file per la copia utilizzando una parte comune del nome file. Azure competenza acquisisce la derivazione a livello di file per ogni singolo file copiato dall'attività di copia corrispondente.

Un esempio di questo modello è il seguente:

* Origine/input: *CustomerCall \* . csv* (percorso ADLS Gen2)
* Sink/output: *CustomerCall \* . csv* (file BLOB di Azure)
* 1 processo: *CopyGen2ToBlob \#CustomerCall.csv* (Data Factory attività di copia)  

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png" alt-text="Screenshot che mostra la derivazione per un'operazione di copia uno-a-uno con supporto jolly." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png":::

### <a name="data-movement-with-n1-lineage"></a>Spostamento dei dati con derivazione n:1

È possibile utilizzare le attività flusso di dati per eseguire operazioni sui dati come merge, join e così via. Per produrre un set di dati di destinazione è possibile usare più di un set di dati di origine. In questo esempio, Azure competenza acquisisce la derivazione a livello di file per i singoli file di input in una tabella SQL che fa parte di un'attività flusso di dati.

Un esempio di questo modello è il seguente:

* 2 origini/input: *Customer.csv*, *Sales. parquet* (percorso ADLS Gen2)
* 1 sink/output: *dati aziendali* (tabella SQL di Azure)
* 1 processo: *DataFlowBlobsToSQL* (Data Factory attività flusso di dati)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png" alt-text="Screenshot che mostra la derivazione di un'operazione del flusso di dati da n a una D F." lightbox="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png":::

### <a name="lineage-for-resource-sets"></a>Derivazione per set di risorse

Un set di risorse è un oggetto logico nel catalogo che rappresenta molti file di partizione nell'archivio sottostante. Per altre informazioni, vedere informazioni sui [set di risorse](concept-resource-sets.md). Quando Azure competenza acquisisce la derivazione dal Azure Data Factory, applica le regole per normalizzare i singoli file di partizione e creare un singolo oggetto logico.

Nell'esempio seguente viene generato un set di risorse Azure Data Lake Gen2 da un BLOB di Azure:

* 1 origine/input: *Employee \_management.csv* (BLOB di Azure)
* 1 sink/output: *Employee \_management.csv* (Azure Data Lake generazione 2)
* 1 processo: *CopyBlobToAdlsGen2 \_ RS* (Data Factory attività di copia)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png" alt-text="Screenshot che mostra la derivazione di un set di risorse." lightbox="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png":::

## <a name="next-steps"></a>Passaggi successivi

- [Guida dell'utente di derivazione del catalogo](catalog-lineage-user-guide.md)
- [Collegamento alla condivisione dati di Azure per la derivazione](how-to-link-azure-data-share.md)
