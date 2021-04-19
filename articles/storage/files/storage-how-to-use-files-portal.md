---
title: Guida introduttiva per la gestione di condivisioni file di Azure con il portale di Azure
description: Informazioni su come creare e gestire condivisioni file di Azure nel portale di Azure. Creare un account di archiviazione e creare e usare una condivisione file di Azure.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d7867a4bd60bb179c18fff49691072d683660f7e
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717843"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-the-azure-portal"></a>Guida introduttiva: Creare e gestire condivisioni file di Azure con il portale di Azure 
[File di Azure](storage-files-introduction.md) è il file system cloud facile da usare di Microsoft. Le condivisioni file di Azure possono essere montate in Windows, Linux e macOS. Questa guida contiene tutte le informazioni essenziali sull'uso delle condivisioni file di Azure con il [portale di Azure](https://portal.azure.com/).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure
Per creare una condivisione file di Azure:

1. Selezionare l'account di archiviazione dal proprio dashboard.
1. Nella sezione **Servizi** della pagina dell'account di archiviazione selezionare **File**.
    
    ![Screenshot della sezione relativa all'archiviazione dei dati dell'account di archiviazione. Selezionare condivisioni file.](media/storage-how-to-use-files-portal/create-file-share-1.png)

1. Nel menu in alto nella pagina **Servizio file** fare clic su **Condivisione file**. Verrà visualizzata la pagina **Nuova condivisione file**.
1. In **Nome** digitare *myshare,* immettere un quoate e lasciare selezionata l'opzione **Transaction optimized** (Ottimizzato per le transazioni) **per Tiers (Livelli).**
1. Selezionare **Crea per** creare la condivisione file di Azure.

I nomi condivisione devono essere costituiti da lettere minuscole, numeri e trattini singoli, ma non possono iniziare con un trattino. Per dettagli su come denominare condivisioni e file, vedere [Denominazione e riferimento a condivisioni, directory, file e metadati](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Usare la condivisione file di Azure
File di Azure offre tre metodi per usare i file e le cartelle all'interno della condivisione file di Azure: il [protocollo SMB (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) standard di settore, il protocollo NFS (Network File System) (anteprima) e il [protocollo REST di File](/rest/api/storageservices/file-service-rest-api). 

Per montare una condivisione file con SMB, vedere il documento riportato di seguito in base al sistema operativo in uso:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-from-the-azure-portal"></a>Uso di una condivisione file di Azure dal portale di Azure
Tutte le richieste effettuate tramite il portale di Azure vengono eseguite con l'API REST di File, che consente di creare, modificare ed eliminare file e directory nei client senza accesso SMB. È possibile usare il protocollo REST di File direttamente, ossia creando personalmente chiamate HTTP REST, ma il modo più comune (oltre al portale di Azure) consiste nell'usare il [modulo di Azure PowerShell](storage-how-to-use-files-powershell.md), l'[interfaccia della riga di comando di Azure](storage-how-to-use-files-cli.md) o Azure Storage SDK, perché offrono un pratico wrapper per il protocollo REST di File nel linguaggio di scripting/programmazione preferito. 

È previsto che la maggior parte degli utenti di File di Azure vorrà usare la propria condivisione file di Azure tramite il protocollo SMB, perché questo consente loro di usare le applicazioni e gli strumenti esistenti con cui possono avere familiarità, ma esistono diversi motivi per cui è vantaggioso usare l'API REST di File invece di SMB, ad esempio:

- È necessario apportare una rapida modifica alla condivisione file di Azure quando si è fuori ufficio, ad esempio da un portatile senza accesso SMB, un tablet o un dispositivo mobile.
- È necessario eseguire uno script o un'applicazione da client in cui non può essere montata una condivisione SMB, ad esempio client locali in cui la porta 445 non è sbloccata.
- Si sfruttano risorse serverless, ad esempio [Funzioni di Azure](../../azure-functions/functions-overview.md). 

Gli esempi seguenti illustrano come usare il portale di Azure per modificare la condivisione file di Azure con il protocollo REST di File. 

Dopo avere creato una condivisione file di Azure, è possibile montare la condivisione file con SMB in [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) o [macOS](storage-how-to-use-files-mac.md). In alternativa, è possibile usare la condivisione file di Azure con il portale di Azure. 

#### <a name="create-a-directory"></a>Creare una directory
Per creare una nuova directory denominata *myDirectory* nella radice della condivisione file di Azure:

1. Nella pagina **Servizio file** selezionare la condivisione file **myshare**. Verrà visualizzata la pagina della condivisione file.
1. Dal menu nella parte superiore della pagina selezionare **Aggiungi directory**. Verrà visualizzata la pagina **Nuova directory**.
1. Digitare *myDirectory* e quindi fare clic su **OK**.

#### <a name="upload-a-file"></a>Caricare un file 
Per illustrare il caricamento di un file, è prima di tutto necessario creare o selezionare un file da caricare. È possibile farlo in qualsiasi modo desiderato. Dopo aver selezionato il file da caricare:

1. Fare clic sulla directory **myDirectory**. Si aprirà il pannello **myDirectory**.
1. Scegliere **Upload** (Carica) dal menu in alto. Si aprirà il pannello **Carica file**.  
    
    ![Screenshot del pannello di caricamento file](media/storage-how-to-use-files-portal/upload-file-1.png)

1. Fare clic sull'icona della cartella per aprire una finestra per esplorare i file locali. 
1. Selezionare un file e fare clic su **Apri**. 
1. Nella pagina **Carica file** verificare il nome del file e quindi fare clic su **Upload** (Carica).
1. Al termine, il file sarà visualizzato nell'elenco nella pagina **myDirectory**.

#### <a name="download-a-file"></a>Scaricare un file
È possibile scaricare una copia del file caricato facendo clic con il pulsante destro del mouse sul file. Dopo aver fatto clic sul pulsante di download, i passaggi successivi varieranno in base al sistema operativo e al browser in uso.

## <a name="clean-up-resources"></a>Pulire le risorse
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Che cos'è File di Azure?](storage-files-introduction.md)