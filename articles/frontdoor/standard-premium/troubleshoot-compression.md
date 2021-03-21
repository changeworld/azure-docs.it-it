---
title: Risoluzione dei problemi di compressione dei file in Azure front door standard/Premium
description: Informazioni su come risolvere i problemi relativi alla compressione dei file in front door di Azure. Questo articolo illustra diverse cause possibili.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2020
ms.author: qixwang
ms.openlocfilehash: c912ebde5499ec2f9f68e5f5762af60823faefe1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100234"
---
# <a name="troubleshooting-azure-front-door-standardpremium-file-compression"></a>Risoluzione dei problemi di compressione di file standard/Premium di Azure front door

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Questo articolo illustra come risolvere i problemi di compressione di file standard o Premium di Azure front door.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="symptom"></a>Sintomo

La compressione per la route è abilitata, ma i file vengono restituiti non compressi.

> [!TIP]
> Per verificare se i file restituiti sono compressi, è necessario usare uno strumento come[Fiddler](https://www.telerik.com/fiddler) o gli [strumenti di sviluppo](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) del browser.  Verificare le intestazioni della risposta HTTP restituite con il contenuto della rete CDN memorizzato nella cache.  Se è presente un'intestazione denominata `Content-Encoding` con un valore **gzip**, **bzip2** o **deflate**, il contenuto è compresso.
> 
> ![Intestazione Content-Encoding](../media/troubleshoot-compression/content-header.png)
> 

## <a name="cause"></a>Causa

Le cause possono essere diverse, ad esempio:

* Il contenuto richiesto non è idoneo per la compressione.
* La compressione non è abilitata per il tipo di file richiesto.
* La richiesta HTTP non include un'intestazione che richiede un tipo di compressione valido.
* L'origine sta inviando contenuto suddiviso in blocchi.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

> [!TIP]
> Come per la distribuzione di nuovi endpoint, la propagazione delle modifiche della configurazione AFD attraverso la rete è molto tempo.  In genere, le modifiche vengono applicate entro 90 minuti.  Se questa è la prima volta che si configura la compressione per l'endpoint della rete CDN, è consigliabile attendere 1-2 ore per assicurarsi che le impostazioni di compressione impostazioni si siano propagate ai POP. 
> 

### <a name="verify-the-request"></a>Verificare la richiesta

Prima di tutto, è necessario controllare la richiesta. È possibile usare gli strumenti di **[sviluppo](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)** del browser per visualizzare le richieste effettuate.

* Verificare che la richiesta venga inviata all'URL dell'endpoint, `<endpointname>.z01.azurefd.net` , e non all'origine.
* Verificare che la richiesta contenga un'intestazione **Accept-Encoding** e che il valore di tale intestazione contenga **gzip**, **deflate** o **bzip2**.

![Intestazioni di richiesta CDN](../media/troubleshoot-compression/request-headers.png)

### <a name="verify-compression-settings"></a>Verificare le impostazioni di compressione

Passare all'endpoint nel [portale di Azure](https://portal.azure.com) e selezionare il pulsante **Configura** nel pannello route. Verificare che la compressione sia **abilitata**.

![Impostazioni di compressione CDN](../media/troubleshoot-compression/compression-settings.png)

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Cercare nelle richieste nel server di origine un'intestazione **Via**

L'intestazione HTPP **Via** indica al server Web che la richiesta viene passata da un server proxy.  Per impostazione predefinita, i server Web Microsoft IIS non comprimeno le risposte quando la richiesta contiene un'intestazione **via** .  Per eseguire l'override di questo comportamento, effettuare le operazioni seguenti:

* **IIS 6**: impostare HcNoCompressionForProxies = "false" nelle proprietà della metabase IIS. Per informazioni, vedere [compressione di IIS 6](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90)).
* **IIS 7 e su**: impostare **noCompressionForHttp10** e **noCompressionForProxies** su *false* nella configurazione del server. Per ulteriori informazioni, vedere la pagina relativa alla [compressione HTTP](https://www.iis.net/configreference/system.webserver/httpcompression).
