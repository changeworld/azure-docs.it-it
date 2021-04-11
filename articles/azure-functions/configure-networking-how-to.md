---
title: Come configurare funzioni di Azure con una rete virtuale
description: Articolo che illustra come eseguire alcune attività di rete virtuale per funzioni di Azure.
ms.topic: conceptual
ms.date: 3/13/2021
ms.custom: template-how-to
ms.openlocfilehash: a28a59a0de40bba7914d1920b42034fbbc223ddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609539"
---
# <a name="how-to-configure-azure-functions-with-a-virtual-network"></a>Come configurare funzioni di Azure con una rete virtuale

Questo articolo illustra come eseguire attività correlate alla configurazione dell'app per le funzioni per la connessione e l'esecuzione in una rete virtuale. Per altre informazioni su funzioni di Azure e sulla rete, vedere [Opzioni di rete di funzioni di Azure](functions-networking-options.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Limitare l'account di archiviazione a una rete virtuale 

Quando si crea un'app per le funzioni, è necessario creare o collegare un account di archiviazione di Azure di uso generico che supporta l'archiviazione BLOB, Coda e Tabella. È possibile sostituire questo account di archiviazione con uno protetto con endpoint di servizio o privato. 

> [!NOTE]  
> Questa funzionalità funziona attualmente per tutti gli SKU supportati da rete virtuale Windows nel piano dedicato (servizio app) e per i piani Premium. Il piano a consumo non è supportato. 

Per configurare una funzione con un account di archiviazione limitato a una rete privata:

1. Creare una funzione con un account di archiviazione in cui non sono abilitati gli endpoint di servizio.

1. Configurare la funzione per la connessione alla rete virtuale.

1. Creare o configurare un account di archiviazione diverso.  Si tratta dell'account di archiviazione protetto con gli endpoint di servizio e della connessione alla funzione.

1. [Creare una condivisione file](../storage/files/storage-how-to-create-file-share.md#create-file-share) nell'account di archiviazione protetto.

1. Abilitare gli endpoint servizio o l'endpoint privato per l'account di archiviazione.  
    * Se si usano connessioni a endpoint privati, per l'account di archiviazione è necessario un endpoint privato per le `file` `blob` risorse secondarie e.  Se si usano determinate funzionalità come Durable Functions, sarà necessario `queue` e `table` accessibili anche tramite una connessione all'endpoint privato.
    * Se si usano gli endpoint del servizio, abilitare la subnet dedicata alle app per le funzioni per gli account di archiviazione.

1. Copiare il contenuto del file e del BLOB dall'account di archiviazione dell'app per le funzioni nell'account di archiviazione e nella condivisione file protetti.

1. Copiare la stringa di connessione per questo account di archiviazione.

1. Aggiornare le **impostazioni dell'applicazione** in **configurazione** per l'app per le funzioni come riportato di seguito:

    | Nome impostazione | Valore | Commento |
    |----|----|----|
    | `AzureWebJobsStorage`| Stringa di connessione di archiviazione | Si tratta della stringa di connessione per un account di archiviazione protetto. |
    | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` |  Stringa di connessione di archiviazione | Si tratta della stringa di connessione per un account di archiviazione protetto. |
    | `WEBSITE_CONTENTSHARE` | Condivisione file | Nome della condivisione file creata nell'account di archiviazione protetto in cui risiedono i file di distribuzione del progetto. |
    | `WEBSITE_CONTENTOVERVNET` | 1 | Nuova impostazione |
    | `WEBSITE_VNET_ROUTE_ALL` | 1 | Forza tutto il traffico in uscita attraverso la rete virtuale. Obbligatorio quando l'account di archiviazione usa connessioni a endpoint privati. |
    | `WEBSITE_DNS_SERVER` | `168.63.129.16` | Server DNS usato dall'app. Obbligatorio quando l'account di archiviazione usa connessioni a endpoint privati. |

1. Selezionare **Save (Salva** ) per salvare le impostazioni dell'applicazione. Se si modificano le impostazioni dell'app, l'app viene riavviata.  

Dopo che l'app per le funzioni è stata riavviata, ora è connessa a un account di archiviazione protetto.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Opzioni di rete di Funzioni di Azure](functions-networking-options.md)

