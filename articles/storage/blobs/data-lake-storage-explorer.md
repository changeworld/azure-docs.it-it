---
title: Usare Azure Storage Explorer con Azure Data Lake Storage Gen2
description: Usare il Azure Storage Explorer per gestire directory e elenchi di controllo di accesso (ACL) di file e directory in account di archiviazione con spazio dei nomi gerarchico (HNS) abilitato.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 5ccef241a37e63467b681d5fd12c65072cb92e58
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626467"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Usare Azure Storage Explorer per gestire directory, file ed elenchi di controllo di accesso in Azure Data Lake Storage Gen2

Questo articolo illustra come usare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per creare e gestire directory, file ed elenchi di controllo di accesso (ACL) negli account di archiviazione con spazio dei nomi gerarchico (HNS) abilitato.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico. Per crearne uno, seguire [queste](../common/storage-account-create.md) istruzioni.

- Azure Storage Explorer installato nel computer locale. Per installare Azure Storage Explorer per Windows, Macintosh o Linux, vedere [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

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

<a id="managing-access"></a>

## <a name="manage-acls"></a>Gestisci ACL

Fare clic con il pulsante destro del mouse sul contenitore, una directory o un file e quindi fare clic su **Gestisci elenchi di controllo di accesso**.  Lo screenshot seguente mostra il menu visualizzato quando si fa clic con il pulsante destro del mouse su una directory.

> [!div class="mx-imgBorder"]
> ![Facendo clic con il pulsante destro del mouse su una directory Azure Storage Explorer](./media/data-lake-storage-explorer/manage-access-control-list-option.png)

La finestra di dialogo **Gestisci accesso** consente di gestire le autorizzazioni per il proprietario e il gruppo Owners. Consente inoltre di aggiungere nuovi utenti e gruppi all'elenco di controllo di accesso per il quale è possibile quindi gestire le autorizzazioni.

> [!div class="mx-imgBorder"]
> ![Finestra di dialogo Gestisci accesso](./media/data-lake-storage-explorer/manage-access-dialog-box.png)

Per aggiungere un nuovo utente o gruppo all'elenco di controllo di accesso, selezionare il pulsante **Aggiungi** . Immettere quindi la voce di Azure Active Directory corrispondente (AAD) che si desidera aggiungere all'elenco e quindi selezionare **Aggiungi**.  L'utente o il gruppo verranno visualizzati nel campo **Utenti e gruppi:**, che consente di iniziare a gestire le relative autorizzazioni.

> [!NOTE]
> È una procedura consigliata per creare un gruppo di sicurezza in Azure Active Directory e gestire le autorizzazioni per il gruppo anziché per i singoli utenti. Per informazioni dettagliate su questa raccomandazione, oltre ad altre procedure consigliate, vedere il [modello di controllo di accesso in Azure Data Lake storage Gen2](data-lake-storage-access-control-model.md).

Utilizzare i controlli casella di controllo per impostare gli ACL predefiniti e di accesso. Per ulteriori informazioni sulla differenza tra questi tipi di ACL, vedere [tipi di ACL](data-lake-storage-access-control.md#types-of-acls).

<a id="apply-acls-recursively"></a>

## <a name="apply-acls-recursively"></a>Applicare gli ACL in modo ricorsivo

È possibile applicare le voci ACL in modo ricorsivo negli elementi figlio esistenti di una directory padre senza dover apportare queste modifiche singolarmente per ogni elemento figlio.

Per applicare le voci ACL in modo ricorsivo, fare clic con il pulsante destro del mouse sul contenitore o su una directory e quindi scegliere **propaga elenchi di controllo di accesso**.  Lo screenshot seguente mostra il menu visualizzato quando si fa clic con il pulsante destro del mouse su una directory.

> [!div class="mx-imgBorder"]
> ![Fare clic con il pulsante destro del mouse su una directory e scegliere l'impostazione propagate Access Control](./media/data-lake-storage-explorer/propagate-access-control-list-option.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni sugli elenchi di controllo di accesso in Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Controllo di accesso in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md)
