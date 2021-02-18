---
title: Migliorare le prestazioni con la compressione dei file in Azure front door standard/Premium (anteprima)
description: Informazioni su come migliorare la velocità di trasferimento dei file e migliorare le prestazioni di caricamento delle pagine comprimendo i file in ingresso di Azure.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4b526d82465862b1c0d27aed6443c6d7199bfb5b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099678"
---
# <a name="improve-performance-by-compressing-files-in-azure-front-door-standardpremium-preview"></a>Migliorare le prestazioni con la compressione dei file in Azure front door standard/Premium (anteprima)

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

La compressione dei file è un metodo efficace per migliorare la velocità di trasferimento dei file e migliorare le prestazioni di caricamento delle pagine. La compressione riduce le dimensioni del file prima che venga inviato dal server. La compressione dei file può ridurre i costi di larghezza di banda e offrire un'esperienza migliore per gli utenti.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
Esistono due modi per abilitare la compressione dei file:

- Abilitazione della compressione nel server di origine. La porta anteriore di Azure passa i file compressi e li recapita ai client che li richiedono.
- Abilitazione della compressione direttamente nei server POP di Azure front door (*compressione immediata*). In questo caso, la porta anteriore di Azure comprime i file e li invia agli utenti finali.

> [!IMPORTANT]
> Per la propagazione delle modifiche alla configurazione di Azure front door in tutta la rete. Se si sta configurando la compressione per la prima volta per l'endpoint della rete CDN, è consigliabile attendere 1-2 ore prima di risolvere i problemi per assicurarsi che le impostazioni di compressione siano propagate a tutti i pop.

## <a name="enabling-compression"></a>Abilitare la compressione

> [!Note]
> Nel portale di Azure, la compressione fa parte dell' **Abilitazione della memorizzazione nella cache** in Route. Solo quando si **Abilita la memorizzazione nella cache**, è possibile sfruttare la compressione in front door di Azure.

È possibile abilitare la compressione nei modi seguenti:
* Durante la creazione rapida: quando si Abilita la memorizzazione nella cache, è possibile abilitare la compressione.
* Durante la creazione personalizzata, abilitare la memorizzazione nella cache e la compressione quando si aggiunge una route. 
* In Endpoint Manager route.
* Nella pagina ottimizzazione.

### <a name="enable-compression-in-endpoint-manager"></a>Abilitare la compressione in Gestione endpoint

1. Dalla pagina del profilo di Azure front door standard/Premium passare a **Gestione endpoint** e selezionare l'endpoint per cui si vuole abilitare la compressione.

1. Selezionare **Modifica endpoint**, quindi selezionare la **Route** per la quale si vuole abilitare la compressione. 

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-1.png" alt-text="Screenshot della pagina di destinazione di Gestione endpoint." lightbox="../media/how-to-compression/front-door-compression-endpoint-manager-1-expanded.png":::   

1. Verificare che l'opzione **Abilita Caching** sia selezionata, quindi selezionare la casella di controllo **Abilita compressione**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Abilitare la compressione in Gestione endpoint.":::   

1. Selezionare **Aggiorna** per salvare la configurazione.

### <a name="enable-compression-in-optimization"></a>Abilitare la compressione nell'ottimizzazione

1. Dalla pagina del profilo di Azure front door standard/Premium passare a **ottimizzazioni** in impostazioni. Espandere l'endpoint per visualizzare l'elenco delle route. 

1. Selezionare i tre puntini accanto alla **Route** in cui è *disabilitata* la compressione. Quindi selezionare **configura route**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-optimization-1.png" alt-text="Schermata di Abilita compressione nella pagina ottimizzazione." lightbox="../media/how-to-compression/front-door-compression-optimization-1-expanded.png"::: 

1. Verificare che l'opzione **Abilita Caching** sia selezionata, quindi selezionare la casella di controllo **Abilita compressione**.

     :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Cattura di schermata dell'abilitazione della compressione in Gestione endpoint."::: 

1. Fare clic su **Update**.

## <a name="modify-compression-content-type"></a>Modificare il tipo di contenuto della compressione

È possibile modificare l'elenco predefinito dei tipi MIME nella pagina ottimizzazioni.

1. Dalla pagina del profilo di Azure front door standard/Premium passare a **ottimizzazioni** in impostazioni. Selezionare quindi la **Route** con la compressione *abilitata*.

1. Selezionare i tre puntini accanto alla **Route** con la compressione *abilitata*. Quindi selezionare **Visualizza tipi di file compressi**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type.png" alt-text="Screenshot della pagina di ottimizzazione." lightbox="../media/how-to-compression/front-door-compression-edit-content-type-expanded.png"::: 

1. Eliminare i formati predefiniti oppure selezionare **Aggiungi** per aggiungere nuovi tipi di contenuto.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type-2.png" alt-text="Screenshot della pagina personalizzare la compressione dei file."::: 

1. Selezionare **Salva** per aggiornare la configurazione della compressione.

## <a name="disabling-compression"></a>Disabilitazione della compressione

È possibile disabilitare la compressione nei modi seguenti:
* Disabilitare la compressione nella route di Gestione endpoint.
* Disabilitare la compressione nella pagina ottimizzazione.

### <a name="disable-compression-in-endpoint-manager"></a>Disabilitare la compressione in Gestione endpoint

1. Dalla pagina del profilo di Azure front door standard/Premium passare a **Gestione endpoint** in impostazioni. Selezionare l'endpoint per il quale si desidera disabilitare la compressione.

1. Selezionare **Modifica endpoint** e quindi selezionare la **Route** per la quale si desidera disabilitare la compressione. Deselezionare la casella **Abilita compressione** .

1. Selezionare **Aggiorna** per salvare la configurazione.

### <a name="disable-compression-in-optimizations"></a>Disabilitare la compressione nelle ottimizzazioni

1. Dalla pagina del profilo di Azure front door standard/Premium passare a **ottimizzazioni** in impostazioni. Selezionare quindi la **Route** con la compressione *abilitata*.

1. Selezionare i tre puntini accanto alla **Route** con la compressione *abilitata*, quindi selezionare *configura route*.

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization.png" alt-text="Screenshot della pagina Disabilita compressione nella pagina ottimizzazione."::: 

1. Deselezionare la casella **Abilita compressione** .

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization-2.png" alt-text="Screenshot della pagina Aggiorna route per la disabilitazione della compressione."::: 

1. Selezionare **Aggiorna** per salvare la configurazione.

## <a name="compression-rules"></a>Regole di compressione

Nel portale di Azure, vengono compressi solo i file idonei. Per essere idoneo per la compressione, un file deve essere:
* Essere di un tipo MIME 
* Maggiore di 1 KB
* Essere inferiore a 8 MB

Questi profili supportano le codifiche di compressione seguenti:
* gzip (GNU Zip)
* brotli 

Se la richiesta supporta più di un tipo di compressione, la compressione brotli ha la precedenza.

Quando una richiesta di un asset specifica la compressione gzip e la richiesta genera un mancato riscontro nella cache, lo sportello principale di Azure esegue la compressione gzip dell'asset direttamente nel server POP. In seguito, il file compresso viene gestito nella cache.

Se l'origine usa la codifica di trasferimento in blocchi (CTE) per inviare i dati compressi al POP di Azure front door, le dimensioni della risposta maggiori di 8 MB non sono supportate. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come configurare il primo [set di regole](how-to-configure-rule-set.md)
- Altre informazioni sulle [condizioni di corrispondenza del set di regole](concept-rule-set-match-conditions.md)
- Altre informazioni sul [set di regole di Azure front door](concept-rule-set.md)
