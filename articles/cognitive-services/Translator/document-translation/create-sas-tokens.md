---
title: Creare token di firma di accesso condiviso (SAS) per contenitori e BLOB con Microsoft Storage Explorer
description: Come creare un token di accesso condiviso (SAS) per contenitori e BLOB con Microsoft Storage Explorer e il portale di Azure
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: 104329928c507c288537704fbbef4e35b65cb445
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739437"
---
# <a name="create-sas-tokens-for-document-translation"></a>Creare token SAS per la traduzione dei documenti

In questo articolo si apprenderà come creare token di firma di accesso condiviso (SAS) usando il Azure Storage Explorer o l'portale di Azure. Un token di firma di accesso condiviso fornisce l'accesso sicuro e delegato alle risorse nell'account di archiviazione di Azure.

## <a name="create-sas-tokens-with-azure-storage-explorer"></a>Creare token SAS con Azure Storage Explorer

### <a name="prerequisites"></a>Prerequisiti

* È necessaria un'app [**Azure Storage Explorer**](../../../vs-azure-tools-storage-manage-with-storage-explorer.md) installata nell'ambiente di sviluppo Windows, MacOS o Linux. Azure Storage Explorer è uno strumento gratuito che consente di gestire facilmente le risorse di archiviazione cloud di Azure.
* Dopo aver installato l'app Azure Storage Explorer, [connetterla all'account di archiviazione](../../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#connect-to-a-storage-account-or-service) usato per la traduzione dei documenti.

### <a name="create-your-tokens"></a>Creare i token

### <a name="sas-tokens-for-containers"></a>[Token SAS per i contenitori](#tab/Containers)

1. Aprire l'app Azure Storage Explorer nel computer locale e passare agli **account di archiviazione** connessi.
1. Espandere il nodo account di archiviazione e selezionare **contenitori BLOB**.
1. Espandere il nodo contenitori BLOB e fare clic con il pulsante destro del mouse su un nodo del **contenitore** di archiviazione o visualizzare il menu opzioni.
1. Selezionare **Ottieni firma di accesso condiviso** dal menu opzioni.
1. Nella finestra **firma di accesso condiviso** effettuare le selezioni seguenti:
    * Selezionare i **criteri di accesso** (il valore predefinito è None).
    * Specificare la data e l'ora di **inizio** e di **scadenza** della chiave firmata. È consigliabile usare una durata breve perché, una volta generata, una firma di accesso condiviso non può essere revocata.
    * Selezionare il **fuso orario** per la data e l'ora di inizio e di scadenza (l'impostazione predefinita è local).
    * Definire le **autorizzazioni** del contenitore controllando e/o deselezionando la casella di controllo appropriata.
    * Esaminare e selezionare **Crea**.

1. Verrà visualizzata una nuova finestra con il nome del **contenitore** , l' **URI** e la **stringa di query** per il contenitore.  
1. **Copiare e incollare i valori del contenitore, dell'URI e della stringa di query in un percorso sicuro. Verranno visualizzati una sola volta e non potranno essere recuperati dopo la chiusura della finestra.**
1. Per costruire un URL di firma di accesso condiviso, aggiungere il token di firma di accesso condiviso (URI) all'URL per un servizio di archiviazione.

### <a name="sas-tokens-for-blobs"></a>[Token SAS per i BLOB](#tab/blobs)

1. Aprire l'app Azure Storage Explorer nel computer locale e passare agli **account di archiviazione** connessi.
1. Espandere il nodo archiviazione e selezionare **contenitori BLOB**.
1. Espandere il nodo contenitori BLOB e selezionare un nodo **contenitore** per visualizzare il contenuto nella finestra principale.
1. Selezionare il BLOB a cui si vuole delegare l'accesso SAS e fare clic con il pulsante destro del mouse per visualizzare il menu opzioni.
1. Selezionare **Ottieni firma di accesso condiviso** dal menu opzioni.
1. Nella finestra **firma di accesso condiviso** effettuare le selezioni seguenti:
    * Selezionare i **criteri di accesso** (il valore predefinito è None).
    * Specificare la data e l'ora di **inizio** e di **scadenza** della chiave firmata. È consigliabile usare una durata breve perché, una volta generata, una firma di accesso condiviso non può essere revocata.
    * Selezionare il **fuso orario** per la data e l'ora di inizio e di scadenza (l'impostazione predefinita è local).
    * Definire le **autorizzazioni** del contenitore controllando e/o deselezionando la casella di controllo appropriata.
    * Esaminare e selezionare **Crea**.
1. Verrà visualizzata una nuova finestra con il nome del **BLOB** , l' **URI** e la **stringa di query** per il BLOB.  
1. **Copiare e incollare i valori di stringa di query, URI e BLOB in un percorso sicuro. Verranno visualizzati una sola volta e non potranno essere recuperati dopo la chiusura della finestra.**
1. Per costruire un URL di firma di accesso condiviso, aggiungere il token di firma di accesso condiviso (URI) all'URL per un servizio di archiviazione.

---

## <a name="create-sas-tokens-for-blobs-in-the-azure-portal"></a>Creare token SAS per i BLOB nel portale di Azure

> [!NOTE]
> La creazione di token SAS per i contenitori direttamente nel portale di Azure non è attualmente supportata. Tuttavia, è possibile creare un token SAS con [**Azure Storage Explorer**](#create-sas-tokens-with-azure-storage-explorer) o completare l'attività [a livello di codice](../../../storage/blobs/sas-service-create.md).

<!-- markdownlint-disable MD024 -->
### <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

* Un [**account Azure**](https://azure.microsoft.com/free/cognitive-services/)attivo.  Se non si dispone di un account, è possibile [**creare un account gratuito**](https://azure.microsoft.com/free/).
* Una risorsa del servizio di [**conversione**](https://ms.portal.azure.com/#create/Microsoft) (**non** una risorsa multiservizio Servizi cognitivi).  *Vedere* [creare una nuova risorsa di Azure](../../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource).  
* Un [**account di archiviazione BLOB di Azure**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). L'accesso ad Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione.

### <a name="create-your-tokens"></a>Creare i token

Passare alla [portale di Azure](https://ms.portal.azure.com/#home) e spostarsi come segue:  

 **Account di archiviazione** → **contenitori** → **contenitore** → **BLOB**

1. Selezionare **genera SAS** dal menu nella parte superiore della pagina.

1. Selezionare **metodo di firma** → **chiave di delega utente**.

1. Definire le **autorizzazioni** controllando e/o deselezionando la casella di controllo appropriata.

1. Specificare l'ora di **inizio** e di **scadenza** della chiave firmata.

1. Il campo **indirizzi IP consentiti** è facoltativo e specifica un indirizzo IP o un intervallo di indirizzi IP da cui accettare le richieste. Se l'indirizzo IP della richiesta non corrisponde all'indirizzo IP o all'intervallo di indirizzi specificato nel token SAS, non sarà autorizzato.

1. Il campo **protocolli consentiti** è facoltativo e specifica il protocollo consentito per una richiesta effettuata con la firma di accesso condiviso. Il valore predefinito è HTTPS.

1. Vedere quindi selezionare **genera token SAS e URL**.

1. La stringa di query del **token SAS BLOB** e l'URL di firma di accesso condiviso **BLOB** verranno visualizzati nell'area inferiore della finestra.  

1. **Copiare e incollare il token SAS BLOB e i valori URL in una posizione sicura. Verranno visualizzati una sola volta e non potranno essere recuperati dopo la chiusura della finestra.**

1. Per costruire un URL di firma di accesso condiviso, aggiungere il token di firma di accesso condiviso (URI) all'URL per un servizio di archiviazione.

## <a name="learn-more"></a>Altre informazioni

* [Creare token SAS per BLOB o contenitori a livello di codice](../../../storage/blobs/sas-service-create.md)
* [Autorizzazioni per una directory, un contenitore o un BLOB](/rest/api/storageservices/create-service-sas#permissions-for-a-directory-container-or-blob)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione alla traduzione dei documenti](get-started-with-document-translation.md)
>
>