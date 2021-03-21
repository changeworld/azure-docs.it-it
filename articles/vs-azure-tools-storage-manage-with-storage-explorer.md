---
title: Introduzione a Storage Explorer | Microsoft Docs
description: Iniziare a gestire le risorse di archiviazione di Azure con Storage Explorer. Scaricare e installare Azure Storage Explorer, connettersi a un account di archiviazione o a un servizio e altro ancora.
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 3a8fe3ded6608059cc6ad50901ffe6df5dcf1b08
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102441589"
---
# <a name="get-started-with-storage-explorer"></a>Introduzione a Storage Explorer

## <a name="overview"></a>Panoramica

Microsoft Azure Storage Explorer è un'app autonoma che facilita l'uso dei dati con Archiviazione di Azure in Windows, macOS e Linux.

Questo articolo illustra diversi modi per connettersi e gestire gli account di archiviazione di Azure.

:::image type="content" alt-text="Microsoft Azure Storage Explorer" source="./vs-storage-explorer-overview.png":::

## <a name="prerequisites"></a>Prerequisiti

# <a name="windows"></a>[Windows](#tab/windows)

Le seguenti versioni di Windows supportano Storage Explorer:

* Windows 10 (scelta consigliata)
* Windows 8
* Windows 7

Per tutte le versioni di Windows, Storage Explorer richiede almeno .NET Framework 4.7.2.

# <a name="macos"></a>[macOS](#tab/macos)

Le versioni seguenti del supporto macOS Storage Explorer:

* macOS 10,12 Sierra e versioni successive

# <a name="linux"></a>[Linux](#tab/linux)

Storage Explorer è disponibile nell' [Archivio di snap](https://snapcraft.io/storage-explorer) per le distribuzioni più comuni di Linux. Si consiglia di usare l'archivio di snap per questa installazione. Lo snap Storage Explorer installa tutte le relative dipendenze e gli aggiornamenti quando le nuove versioni vengono pubblicate nell'archivio di snap.

Per le distribuzioni supportate, vedere la [ `snapd` pagina installazione](https://snapcraft.io/docs/installing-snapd).

Storage Explorer richiede l'uso di un gestore delle password. Potrebbe essere necessario connettersi manualmente a un gestore delle password. È possibile connettere Storage Explorer al gestore delle password del sistema eseguendo il comando seguente:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer è disponibile anche come download di *. tar. gz* . Se si usa il *. tar. gz*, è necessario installare le dipendenze manualmente. Di seguito sono riportate le distribuzioni di Linux supportate dall'installazione di *. tar. gz* :

* Ubuntu 20,04 x64
* Ubuntu 18,04 x64
* Ubuntu 16,04 x64

L'installazione di *. tar. gz* potrebbe funzionare in altre distribuzioni, ma solo queste sono ufficialmente supportate.

Per ulteriori informazioni sull'installazione di Storage Explorer in Linux, vedere [dipendenze Linux](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) nella Guida alla risoluzione dei problemi Azure Storage Explorer.

---

## <a name="download-and-install"></a>Scaricare e installare

Per scaricare e installare Storage Explorer, vedere [Azure Storage Explorer](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Connettersi a un account o a un servizio di archiviazione

Storage Explorer offre diversi modi per connettersi alle risorse di Azure:

* [Accedere ad Azure per accedere alle sottoscrizioni e alle relative risorse](#sign-in-to-azure)
* [Connettersi a una singola risorsa di archiviazione di Azure](#attach-to-an-individual-resource)
* [Connettersi a una risorsa CosmosDB](#connect-to-azure-cosmos-db)

### <a name="sign-in-to-azure"></a>Accedere ad Azure

> [!NOTE]
> Per accedere completamente alle risorse dopo l'accesso, Storage Explorer richiede le autorizzazioni sia per la gestione (Azure Resource Manager) che per il livello dati. Ciò significa che sono necessarie le autorizzazioni Azure Active Directory (Azure AD) per accedere all'account di archiviazione, ai contenitori nell'account e ai dati nei contenitori. Se si hanno le autorizzazioni solo a livello di dati, è consigliabile scegliere l'opzione **Accedi con Azure Active Directory (Azure ad)** quando si esegue la connessione a una risorsa. Per ulteriori informazioni sulle autorizzazioni specifiche Storage Explorer richieste, vedere la [Guida alla risoluzione dei problemi Azure Storage Explorer](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues).

1. In Storage Explorer selezionare **Visualizza**  >  **Gestione account** oppure selezionare il pulsante **Gestisci account** .

    :::image type="content" alt-text="Gestisci account" source ="./vs-storage-explorer-manage-accounts.png":::

1. La **gestione degli account** ora Visualizza tutti gli account Azure a cui è stato effettuato l'accesso. Per connettersi a un altro account, selezionare **Aggiungi un account..**..

1. Verrà visualizzata la finestra **di dialogo Connetti ad archiviazione di Azure** . Nel pannello **Seleziona risorsa** selezionare **sottoscrizione**.

    :::image type="content" alt-text="Finestra di dialogo Connetti" source="./vs-storage-explorer-connect-dialog.png":::

1. Nel pannello **Seleziona ambiente Azure** selezionare un ambiente Azure a cui accedere. È possibile accedere a Global Azure, a un cloud nazionale o a un'istanza di Azure Stack. Selezionare quindi **Avanti**.

    :::image type="content" alt-text="Opzione per l'accesso" source="./vs-storage-explorer-connect-environment.png":::

    > [!TIP]
    > Per altre informazioni su Azure Stack, vedere [connettere Storage Explorer a una sottoscrizione Azure stack o un account di archiviazione](/azure-stack/user/azure-stack-storage-connect-se).

1. Storage Explorer aprirà una pagina Web per l'accesso.

1. Dopo aver eseguito l'accesso con un account Azure, l'account e le sottoscrizioni di Azure associate a tale account vengono visualizzati in **Gestione account**. Selezionare le sottoscrizioni di Azure da usare e quindi selezionare **Applica**.

    :::image type="content" alt-text="Selezionare le sottoscrizioni di Azure" source="./vs-storage-explorer-account-panel.png":::

1. **Explorer** Visualizza gli account di archiviazione associati alle sottoscrizioni di Azure selezionate.

    :::image type="content" alt-text="Sottoscrizioni di Azure selezionate" source="./vs-storage-explorer-subscription-node.png":::

### <a name="attach-to-an-individual-resource"></a>Connettersi a una singola risorsa

Storage Explorer consente di connettersi a singole risorse, ad esempio un contenitore di Azure Data Lake Storage Gen2, usando vari metodi di autenticazione. Alcuni metodi di autenticazione sono supportati solo per determinati tipi di risorse.

| Tipo di risorsa    | Azure AD | Nome e chiave dell'account | Firma di accesso condiviso  | Pubblico (anonimo) |
|------------------|----------|----------------------|--------------------------------|--------------------|
| Account di archiviazione | Sì      | Sì                  | Sì (stringa di connessione o URL) | No                 |
| Contenitori BLOB  | Sì      | No                   | Sì (URL)                      | Sì                |
| Contenitori Gen2  | Sì      | No                   | Sì (URL)                      | Sì                |
| Directory Gen2 | Sì      | No                   | Sì (URL)                      | Sì                |
| Condivisioni file      | No       | No                   | Sì (URL)                      | No                 |
| Code           | Sì      | No                   | Sì (URL)                      | No                 |
| Tabelle           | No       | No                   | Sì (URL)                      | No                 |
 
Storage Explorer possibile connettersi anche a un [emulatore di archiviazione locale](#local-storage-emulator) usando le porte configurate dell'emulatore.

Per connettersi a una singola risorsa, selezionare il pulsante **Connetti** sulla barra degli strumenti a sinistra. Quindi seguire le istruzioni per il tipo di risorsa a cui si vuole connettersi.

:::image type="content" alt-text="Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)" source="./vs-storage-explorer-connect-button.png":::

Quando viene aggiunta correttamente una connessione a un account di archiviazione, viene visualizzato un nuovo nodo della struttura ad albero in account di archiviazione **locali & collegati**  >  .

Per altri tipi di risorse, viene aggiunto un nuovo nodo in **local &**  >  **account di archiviazione** collegati  >  **(contenitori collegati)**. Il nodo verrà visualizzato in un nodo di gruppo corrispondente al tipo. Ad esempio, una nuova connessione a un contenitore Azure Data Lake Storage Gen2 verrà visualizzata in **contenitori BLOB**.

Se Storage Explorer non è riuscito ad aggiungere la connessione o se non è possibile accedere ai dati dopo aver aggiunto correttamente la connessione, vedere la [Guida alla risoluzione dei problemi Azure Storage Explorer](./storage/common/storage-explorer-troubleshooting.md).

Le sezioni seguenti descrivono i diversi metodi di autenticazione che è possibile usare per connettersi a singole risorse.

#### <a name="azure-ad"></a>Azure AD

Storage Explorer possibile usare l'account Azure per connettersi ai tipi di risorse seguenti:
* Contenitori BLOB
* Contenitori di Azure Data Lake Storage Gen2
* Directory Azure Data Lake Storage Gen2
* Code
 
Azure AD è l'opzione consigliata se si dispone dell'accesso a livello di dati alla risorsa, ma non di accesso al livello di gestione.

1. Accedere ad almeno un account Azure usando la [procedura descritta in precedenza](#sign-in-to-azure).
1. Nel pannello **Seleziona risorsa** della finestra di dialogo **Connetti ad archiviazione di Azure** Selezionare **contenitore BLOB**, **contenitore ADLS Gen2** o **coda**.
1. Selezionare **Accedi con Azure Active Directory (Azure ad)** e fare clic su **Avanti**.
1. Selezionare un account e un tenant di Azure. L'account e il tenant devono avere accesso alla risorsa di archiviazione a cui si vuole connettersi. Selezionare **Avanti**.
1. Immettere un nome visualizzato per la connessione e l'URL della risorsa. Selezionare **Avanti**.
1. Esaminare le informazioni di connessione nel pannello **Riepilogo** . Se le informazioni di connessione sono corrette, selezionare **Connetti**.

#### <a name="account-name-and-key"></a>Nome e chiave dell'account

Storage Explorer possibile connettersi a un account di archiviazione usando il nome e la chiave dell'account di archiviazione.

È possibile trovare le chiavi dell'account nella [portale di Azure](https://portal.azure.com). Aprire la pagina dell'account di archiviazione e selezionare **Impostazioni**  >  **chiavi di accesso**.

1. Nel pannello **Seleziona risorsa** della finestra di dialogo **Connetti ad archiviazione di Azure** selezionare **account di archiviazione**.
1. Selezionare il **nome e la chiave dell'account** e fare clic su **Avanti**.
1. Immettere un nome visualizzato per la connessione, il nome dell'account e una delle chiavi dell'account. Selezionare l'ambiente Azure appropriato. Selezionare **Avanti**.
1. Esaminare le informazioni di connessione nel pannello **Riepilogo** . Se le informazioni di connessione sono corrette, selezionare **Connetti**.

#### <a name="shared-access-signature-sas-connection-string"></a>Stringa di connessione della firma di accesso condiviso

Storage Explorer possibile connettersi a un account di archiviazione usando una stringa di connessione con una firma di accesso condiviso (SAS). Una stringa di connessione SAS ha un aspetto simile al seguente:

```text
SharedAccessSignature=sv=2020-04-08&ss=btqf&srt=sco&st=2021-03-02T00%3A22%3A19Z&se=2020-03-03T00%3A22%3A19Z&sp=rl&sig=fFFpX%2F5tzqmmFFaL0wRffHlhfFFLn6zJuylT6yhOo%2FY%3F;
BlobEndpoint=https://contoso.blob.core.windows.net/;
FileEndpoint=https://contoso.file.core.windows.net/;
QueueEndpoint=https://contoso.queue.core.windows.net/;
TableEndpoint=https://contoso.table.core.windows.net/;
```

1. Nel pannello **Seleziona risorsa** della finestra di dialogo **Connetti ad archiviazione di Azure** selezionare **account di archiviazione**.
1. Selezionare **firma di accesso condiviso (SAS)** e fare clic su **Avanti**.
1. Immettere un nome visualizzato per la connessione e la stringa di connessione SAS per l'account di archiviazione. Selezionare **Avanti**.
1. Esaminare le informazioni di connessione nel pannello **Riepilogo** . Se le informazioni di connessione sono corrette, selezionare **Connetti**.

#### <a name="shared-access-signature-sas-url"></a>URL di firma di accesso condiviso (SAS)

Storage Explorer possibile connettersi ai tipi di risorse seguenti utilizzando un URI di firma di accesso condiviso:
* Contenitore BLOB
* Azure Data Lake Storage Gen2 contenitore o directory
* Condivisione file
* Coda
* Tabella

Un URI di firma di accesso condiviso è simile al seguente:

```text
https://contoso.blob.core.windows.net/container01?sv=2020-04-08&st=2021-03-02T00%3A30%3A33Z&se=2020-03-03T00%3A30%3A33Z&sr=c&sp=rl&sig=z9VFdWffrV6FXU51T8b8HVfipZPOpYOFLXuQw6wfkFY%3F
```

1. Nel pannello **Seleziona risorsa** della finestra di dialogo **Connetti ad archiviazione di Azure** Selezionare la risorsa a cui si vuole connettersi.
1. Selezionare **firma di accesso condiviso (SAS)** e fare clic su **Avanti**.
1. Immettere un nome visualizzato per la connessione e l'URI SAS per la risorsa. Selezionare **Avanti**.
1. Esaminare le informazioni di connessione nel pannello **Riepilogo** . Se le informazioni di connessione sono corrette, selezionare **Connetti**.

#### <a name="local-storage-emulator"></a>Emulatore di archiviazione locale

Storage Explorer possibile connettersi a un emulatore di archiviazione di Azure. Attualmente sono disponibili due emulatori supportati:

* [Emulatore di archiviazione di Azure](storage/common/storage-use-emulator.md) (solo Windows)
* [Azzurrite](https://github.com/azure/azurite) (Windows, MacOS o Linux)

Se l'emulatore è in ascolto sulle porte predefinite, è possibile usare il nodo **local & Attached**  >  **Storage Accounts**  >  **Emulator-default Ports** per accedere all'emulatore.

Se si vuole usare un nome diverso per la connessione o se l'emulatore non è in esecuzione sulle porte predefinite:

1. Avviare l'emulatore.

   > [!IMPORTANT]
   > Storage Explorer non avvia automaticamente l'emulatore. È necessario avviarlo manualmente.

1. Nel pannello **Seleziona risorsa** della finestra di dialogo **Connetti ad archiviazione di Azure** Selezionare **emulatore di archiviazione locale**.
1. Immettere un nome visualizzato per la connessione e il numero di porta per ogni servizio emulato che si vuole usare. Se non si desidera utilizzare in un servizio, lasciare vuota la porta corrispondente. Selezionare **Avanti**.
1. Esaminare le informazioni di connessione nel pannello **Riepilogo** . Se le informazioni di connessione sono corrette, selezionare **Connetti**.

### <a name="connect-to-azure-cosmos-db"></a>Connettersi ad Azure Cosmos DB

Storage Explorer supporta anche la connessione a risorse Azure Cosmos DB.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Connettersi a un account Azure Cosmos DB usando una stringa di connessione

Anziché gestire gli account Azure Cosmos DB tramite una sottoscrizione di Azure, è possibile connettersi a Azure Cosmos DB usando una stringa di connessione. Per eseguire la connessione, seguire questa procedura:

1. In **Esplora**, espandere **& locali collegati**, fare clic con il pulsante destro del mouse su **account Cosmos DB** e selezionare **Connetti a Azure Cosmos DB**.

    ![Connettersi ad Azure Cosmos DB usando una stringa di connessione][21]

1. Selezionare l'API Azure Cosmos DB, immettere i dati della **stringa di connessione** e quindi fare clic su **OK** per connettere l'account Azure Cosmos DB. Per informazioni su come recuperare la stringa di connessione, vedere [gestire un account Azure Cosmos](./cosmos-db/how-to-manage-database-account.md).

    ![Stringa di connessione][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Connettersi a Azure Data Lake Store mediante URI

È possibile accedere a una risorsa che non si trova nella sottoscrizione. È necessario un utente che abbia accesso a tale risorsa per fornire l'URI della risorsa. Dopo aver eseguito l'accesso, connettersi a Data Lake Store usando l'URI. Per eseguire la connessione, seguire questa procedura:

1. In **Esplora risorse** espandere **& locali collegati**.

1. Fare clic con il pulsante destro del mouse su **Data Lake storage Gen1** e scegliere **Connetti a data Lake storage Gen1**.

    ![Menu di scelta rapida Connetti a Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Immettere l'URI, quindi fare clic su **OK**. Il Data Lake Store viene visualizzato sotto **Data Lake storage**.

    ![Connetti a risultato Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

In questo esempio viene usato Data Lake Storage Gen1. Azure Data Lake Storage Gen2 è ora disponibile. Per ulteriori informazioni, vedere [che cos'è Azure Data Lake storage Gen1](./data-lake-store/data-lake-store-overview.md).

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Generare una firma di accesso condiviso in Storage Explorer<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Firma di accesso condiviso a livello di account

1. Fare clic con il pulsante destro del mouse sull'account di archiviazione che si vuole condividere e quindi scegliere **Ottieni firma di accesso condiviso**.

    ![Ottenere l'opzione del menu di scelta rapida firma di accesso condiviso][14]

1. In **firma di accesso condiviso** specificare l'intervallo di tempo e le autorizzazioni desiderate per l'account, quindi selezionare **Crea**.

    ![Ottenere una firma di accesso condiviso][15]

1. Copiare la **stringa di connessione** o la **stringa di query** non elaborata negli Appunti.

### <a name="service-level-shared-access-signature"></a>Firma di accesso condiviso a livello di servizio

È possibile ottenere una firma di accesso condiviso a livello di servizio. Per altre informazioni, vedere [ottenere la firma di accesso condiviso per un contenitore BLOB](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Cercare gli account di archiviazione

Per trovare una risorsa di archiviazione, è possibile cercare nel riquadro di **esplorazione** .

Quando si immette testo nella casella di ricerca, Storage Explorer Visualizza tutte le risorse che corrispondono al valore di ricerca immesso fino a quel momento. Questo esempio mostra una ricerca di **endpoint**:

![Ricerca dell'account di archiviazione][23]

> [!NOTE]
> Per velocizzare la ricerca, usare **Gestione account** per deselezionare le sottoscrizioni che non contengono l'elemento che si sta cercando. È anche possibile fare clic con il pulsante destro del mouse su un nodo e selezionare **Cerca da qui** per avviare la ricerca da un nodo specifico.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire le risorse di archiviazione BLOB di Azure con Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Gestire i dati con Azure Storage Explorer](./cosmos-db/storage-explorer.md)
* [Gestire le risorse di Azure Data Lake Store con Storage Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
