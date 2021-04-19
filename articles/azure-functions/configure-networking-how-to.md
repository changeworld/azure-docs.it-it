---
title: Come configurare le Funzioni di Azure con una rete virtuale
description: Articolo che illustra come eseguire determinate attività di rete virtuale per Funzioni di Azure.
ms.topic: conceptual
ms.date: 3/13/2021
ms.custom: template-how-to
ms.openlocfilehash: c123b20e163731f9a872a969f2f1564479b6e308
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718432"
---
# <a name="how-to-configure-azure-functions-with-a-virtual-network"></a>Come configurare le Funzioni di Azure con una rete virtuale

Questo articolo illustra come eseguire attività correlate alla configurazione dell'app per le funzioni per la connessione e l'esecuzione in una rete virtuale. Per altre informazioni sulle funzionalità Funzioni di Azure rete, vedere Funzioni di Azure [di rete.](functions-networking-options.md)

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Limitare l'account di archiviazione a una rete virtuale 

Quando si crea un'app per le funzioni, è necessario creare o collegare un account di archiviazione di Azure di uso generico che supporta l'archiviazione BLOB, Coda e Tabella. È possibile sostituire questo account di archiviazione con uno protetto con endpoint di servizio o endpoint privato. 

> [!NOTE]  
> Questa funzionalità attualmente funziona per tutti gli SKU supportati dalla rete virtuale Windows nel piano Dedicato (servizio app) e per i piani Premium. Il piano a consumo non è supportato. 

Per configurare una funzione con un account di archiviazione limitato a una rete privata:

1. Creare una funzione con un account di archiviazione in cui gli endpoint di servizio non sono abilitati.

1. Configurare la funzione per la connessione alla rete virtuale.

1. Creare o configurare un account di archiviazione diverso.  Questo sarà l'account di archiviazione protetto con gli endpoint di servizio e connetterà la funzione.

1. [Creare una condivisione file nell'account](../storage/files/storage-how-to-create-file-share.md#create-a-file-share) di archiviazione protetto.

1. Abilitare gli endpoint di servizio o l'endpoint privato per l'account di archiviazione.  
    * Se si usano connessioni endpoint privato, l'account di archiviazione dovrà disporre di un endpoint privato per `file` le risorse `blob` secondarie e .  Se si usano determinate funzionalità come Durable Functions, sarà necessario e accessibile `queue` anche tramite una connessione endpoint `table` privato.
    * Se si usano endpoint di servizio, abilitare la subnet dedicata alle app per le funzioni per gli account di archiviazione.

1. Copiare il contenuto del file e del BLOB dall'account di archiviazione dell'app per le funzioni all'account di archiviazione protetto e alla condivisione file.

1. Copiare la stringa di connessione per questo account di archiviazione.

1. Aggiornare le **impostazioni dell'applicazione** in **Configurazione** per l'app per le funzioni come segue:

    | Nome impostazione | Valore | Commento |
    |----|----|----|
    | `AzureWebJobsStorage`| Stringa di connessione di archiviazione | Si tratta della stringa di connessione per un account di archiviazione protetto. |
    | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` |  Stringa di connessione di archiviazione | Si tratta della stringa di connessione per un account di archiviazione protetto. |
    | `WEBSITE_CONTENTSHARE` | Condivisione file | Nome della condivisione file creata nell'account di archiviazione protetto in cui si trovano i file di distribuzione del progetto. |
    | `WEBSITE_CONTENTOVERVNET` | 1 | Nuova impostazione |
    | `WEBSITE_VNET_ROUTE_ALL` | 1 | Forza tutto il traffico in uscita attraverso la rete virtuale. Obbligatorio quando l'account di archiviazione usa connessioni endpoint private. |
    | `WEBSITE_DNS_SERVER` | `168.63.129.16` | Server DNS usato dall'app. Obbligatorio quando l'account di archiviazione usa connessioni endpoint private. |

1. Selezionare **Salva** per salvare le impostazioni dell'applicazione. Se si modificano le impostazioni dell'app, l'app viene riavviata.  

Dopo il riavvio, l'app per le funzioni è ora connessa a un account di archiviazione protetto.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Opzioni di rete di Funzioni di Azure](functions-networking-options.md)

