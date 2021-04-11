---
title: Usare Azure Storage Explorer con Azure Data Lake Storage Gen2
description: Usare il Azure Storage Explorer per gestire directory e elenchi di controllo di accesso (ACL) di file e directory in account di archiviazione con spazio dei nomi gerarchico (HNS) abilitato.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e6147918e7cd56aed5b5b333a8e9825a34d60fd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652276"
---
# <a name="use-azure-storage-explorer-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Usare Azure Storage Explorer per gestire directory e file in Azure Data Lake Storage Gen2

Questo articolo illustra come usare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per creare e gestire directory e file negli account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico (HNS).

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico. Per crearne uno, seguire [queste](../common/storage-account-create.md) istruzioni.

- Azure Storage Explorer installato nel computer locale. Per installare Azure Storage Explorer per Windows, Macintosh o Linux, vedere [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

> [!NOTE]
> Storage Explorer usa entrambi gli [endpoint](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage) BLOB (blob) & data Lake storage Gen2 (DFS) quando si lavora con Azure Data Lake storage Gen2. Se l'accesso a Azure Data Lake Storage Gen2 viene configurato usando endpoint privati, verificare che siano stati creati due endpoint privati per l'account di archiviazione: uno con la risorsa secondaria di destinazione `blob` e l'altro con la sottorisorsa di destinazione `dfs` .

## <a name="sign-in-to-storage-explorer"></a>Accedere a Storage Explorer

Al primo avvio di Storage Explorer, viene visualizzata la finestra **Microsoft Azure Storage Explorer - Connetti**. Sebbene Storage Explorer offra numerosi modi per connettersi agli account di archiviazione, per la gestione degli elenchi di controllo di accesso è attualmente supportata una sola modalità.

|Attività|Scopo|
|---|---|
|Aggiungere un account Azure | Consente il reindirizzamento alla pagina di accesso dell'organizzazione per l'autenticazione in Azure. Attualmente questo è l'unico metodo di autenticazione supportato per la gestione e l'impostazione degli elenchi di controllo di accesso.|
|Usare una stringa di connessione o un URI di firma di accesso condiviso | Consente di accedere direttamente a un contenitore o a un account di archiviazione con un token di firma di accesso condiviso o una stringa di connessione condivisa. |
|Usare un nome e una chiave dell'account di archiviazione| Consente di usare un nome e una chiave dell'account di archiviazione per la connessione ad Archiviazione di Azure.|

Selezionare **Add an Azure Account** (Aggiungi un account Azure) e fare clic su **Accedi**. Attenersi alle richiesta visualizzate per accedere all'account Azure.

![Screenshot che Mostra Microsoft Azure Storage Explorer ed evidenzia l'opzione Aggiungi un account Azure e il pulsante Accedi.](media/storage-quickstart-blobs-storage-explorer/connect.png)

Al termine della connessione, Azure Storage Explorer viene caricato con la scheda **Explorer** visualizzata. Questa visualizzazione include informazioni dettagliate per tutti gli account di archiviazione di Azure e per le risorse di archiviazione locali configurate tramite gli account dell'[emulatore di archiviazione Azurite](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) o gli ambienti di [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

![Microsoft Azure Storage Explorer - Finestra Connetti](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Creare un contenitore

Un contenitore include le directory e i file. Per crearne uno, espandere l'account di archiviazione creato nel passaggio che segue. Selezionare **contenitori BLOB**, fare clic con il pulsante destro del mouse e scegliere **Crea contenitore BLOB**. Immettere il nome del contenitore. Vedere la sezione [creare un contenitore](storage-quickstart-blobs-dotnet.md#create-a-container) per un elenco di regole e restrizioni relative alla denominazione dei contenitori. Al termine, premere **invio** per creare il contenitore. Una volta creato correttamente, il contenitore viene visualizzato nella cartella **contenitori BLOB** per l'account di archiviazione selezionato.

![Microsoft Azure Storage Explorer-creazione di un contenitore](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Creare una directory

Per creare una directory, selezionare il contenitore creato nel passaggio che segue. Nella barra multifunzione del contenitore scegliere il pulsante **nuova cartella** . Immettere il nome per la directory. Al termine, premere **invio** per creare la directory. Una volta creata correttamente, la directory viene visualizzata nella finestra dell'editor.

![Microsoft Azure Storage Explorer-creazione di una directory](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Caricare i BLOB nella directory

Sulla barra multifunzione Directory scegliere il pulsante **carica** . Questa operazione consente di caricare una cartella o un file.

Scegliere i file o le cartelle da caricare.

![Microsoft Azure Storage Explorer - Caricare un BLOB](media/data-lake-storage-explorer/upload-file.png)

Quando si seleziona **OK**, i file selezionati vengono accodati per il caricamento e viene caricato ogni file. Al termine del caricamento, i risultati vengono visualizzati nella finestra **Attività**.

## <a name="view-blobs-in-a-directory"></a>Visualizzare BLOB in una directory

Nell'applicazione **Azure Storage Explorer**, selezionare una directory in un account di archiviazione. Il riquadro principale mostra un elenco dei BLOB disponibili nella directory selezionata.

![Microsoft Azure Storage Explorer - Elencare i BLOB in una directory](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Scaricare BLOB

Per scaricare i file tramite **Azure Storage Explorer**, con un file selezionato, selezionare **Scarica** dalla barra multifunzione. Viene visualizzata una finestra di dialogo File, che consente di immettere un nome di file. Selezionare **Save (Salva** ) per avviare il download di un file nel percorso locale.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come gestire le autorizzazioni per file e directory impostando elenchi di controllo di accesso (ACL)

> [!div class="nextstepaction"]
> [Utilizzare Azure Storage Explorer per gestire gli ACL in Azure Data Lake Storage Gen2](./data-lake-storage-explorer-acl.md)
