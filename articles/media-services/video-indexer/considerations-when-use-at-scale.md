---
title: Aspetti da considerare quando si usa Video Indexer su larga scala - Azure
titleSuffix: Azure Media Services
description: Questo argomento illustra gli aspetti da considerare quando si usa Video Indexer su larga scala.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: f941d81df670f017d24a7c5011c55fcc4f082605
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531566"
---
# <a name="things-to-consider-when-using-video-indexer-at-scale"></a>Aspetti da considerare quando si usa Video Indexer su larga scala

Quando si usa Servizi multimediali di Azure'indicizzatore video per indicizzare i video e l'archivio dei video è in crescita, prendere in considerazione la scalabilità. 

Questo articolo risponde a domande come:

* Sono presenti vincoli tecnologici da prendere in considerazione?
* Esiste un modo intelligente ed efficiente per farlo?
* È possibile evitare la spesa in eccesso nel processo?

L'articolo illustra sei procedure consigliate per l'uso Video Indexer su larga scala.

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>Quando si caricano video, è consigliabile usare un URL su una matrice di byte

Video Indexer consente di caricare video dall'URL o direttamente inviando il file come matrice di byte, quest'ultimo presenta alcuni vincoli. Per altre informazioni, vedere [Considerazioni e limitazioni sul caricamento)](upload-index-videos.md#uploading-considerations-and-limitations)

In primo luogo, presenta limitazioni relative alle dimensioni dei file. Le dimensioni del file di matrice di byte sono limitate a 2 GB rispetto alla limitazione delle dimensioni di caricamento di 30 GB durante l'uso dell'URL.

In secondo piano, prendere in considerazione solo alcuni dei problemi che possono influire sulle prestazioni e quindi la possibilità di ridimensionare:

* L'invio di file con più parti implica un'elevata dipendenza dalla rete, 
* affidabilità del servizio, 
* Connettività 
* velocità di caricamento, 
* pacchetti persi da qualche parte nel World Wide Web.

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="Prima considerazione per l'uso Video Indexer su larga scala":::

Quando si caricano video usando l'URL, è sufficiente specificare un percorso per il percorso di un file multimediale e Video Indexer si occupa del resto (vedere il campo `videoUrl` nell'API [carica video).](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)

> [!TIP]
> Usare il `videoUrl` parametro facoltativo dell'API carica video.

Per un esempio di come caricare video usando l'URL, vedere [questo esempio.](upload-index-videos.md#code-sample) In caso contrario, è possibile usare [AzCopy](../../storage/common/storage-use-azcopy-v10.md) per ottenere il contenuto in modo rapido e affidabile in un account di archiviazione da cui è possibile inviarlo a Video Indexer usando l'URL di firma di [accesso condiviso.](../../storage/common/storage-sas-overview.md)

## <a name="increase-media-reserved-units-if-needed"></a>Aumentare le unità riservate dei supporti, se necessario

In genere nella fase del modello di verifica quando si inizia a usare Video Indexer, non è necessaria una grande quantità di potenza di calcolo. Quando si inizia a disporre di un archivio più ampio di video da indicizzare e si vuole che il processo si adatti al caso d'uso, è necessario aumentare l'utilizzo di Video Indexer. È quindi consigliabile aumentare il numero di risorse di calcolo usate se la quantità corrente di potenza di calcolo non è sufficiente.

In Servizi multimediali di Azure, quando si vuole aumentare la potenza di calcolo e la parallelizzazione, è necessario prestare attenzione alle unità riservate [dei](../latest/concept-media-reserved-units.md)supporti. Le unità di calcolo sono le unità di calcolo che determinano i parametri per le attività di elaborazione dei supporti. Il numero di unità richiesta influisce sul numero di attività multimediali che possono essere elaborate contemporaneamente in ogni account e il relativo tipo determina la velocità di elaborazione e un video potrebbe richiedere più di un'unità richiesta se l'indicizzazione è complessa. Quando le unità richiesta sono occupate, le nuove attività verranno mantenute in una coda fino a quando non sarà disponibile un'altra risorsa.

Per operare in modo efficiente ed evitare che le risorse rimangano inattive parte del tempo, Video Indexer offre un sistema di scalabilità automatica che ruota le unità richiesta quando è necessaria una minore elaborazione e fa aumentare le unità richiesta durante le ore di punta (fino a usare completamente tutte le unità richiesta). È possibile abilitare questa funzionalità [attivando](manage-account-connected-to-azure.md#autoscale-reserved-units) la scalabilità automatica nelle impostazioni dell'account o usando [l'API Update-Paid-Account-Azure-Media-Services](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services).

:::image type="content" source="./media/considerations-when-use-at-scale/second-consideration.jpg" alt-text="Seconda considerazione per l'uso Video Indexer su larga scala":::

:::image type="content" source="./media/considerations-when-use-at-scale/reserved-units.jpg" alt-text="Unità riservate AMS":::

Per ridurre al minimo la durata dell'indicizzazione e la velocità effettiva bassa, è consigliabile iniziare con 10 UNITÀ RICHIESTA di tipo S3. In un secondo momento, se si scala l'utente per supportare più contenuto o una concorrenza superiore e sono necessarie più risorse per eseguire questa operazione, è possibile contattare Microsoft usando il sistema di supporto [(solo](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) per gli account a pagamento) per richiedere altre allocazioni di unità richiesta.

## <a name="respect-throttling"></a>Rispettare la limitazione

Video Indexer è progettato per gestire l'indicizzazione su larga scala e, quando si vuole ottenere il massimo, è necessario anche essere consapevoli delle funzionalità del sistema e progettare l'integrazione di conseguenza. Non si vuole inviare una richiesta di caricamento per un batch di video solo per scoprire che alcuni film non sono stati caricati e si riceve un codice di risposta HTTP 429 (troppe richieste). Può verificarsi a causa del fatto che sono stati inviati più richieste rispetto al [limite di film al minuto supportati.](upload-index-videos.md#uploading-considerations-and-limitations) Video Indexer aggiunge un'intestazione nella risposta HTTP, l'intestazione specifica quando tentare `retry-after` il successivo tentativo. Assicurarsi di rispettarlo prima di provare la richiesta successiva.

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="Progettare bene l'integrazione, rispettare la limitazione":::

## <a name="use-callback-url"></a>Usare l'URL di callback

È consigliabile aggiungere un URL di [callback](upload-index-videos.md#callbackurl)anziché eseguire costantemente il polling dello stato della richiesta dal momento in cui è stata inviata la richiesta di caricamento e attendere Video Indexer'aggiornamento. Non appena viene apportata una modifica dello stato nella richiesta di caricamento, si ottiene una notifica POST all'URL specificato.

È possibile aggiungere un URL di callback come uno dei parametri [dell'API di caricamento video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video). Vedere gli esempi di codice nel [repository GitHub.](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/) 

Per l'URL di callback è anche possibile usare Funzioni di Azure, una piattaforma serverless basata su eventi che può essere attivata da HTTP e implementare un flusso seguente.

### <a name="callback-url-definition"></a>definizione dell'URL callBack

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>Usare i parametri di indicizzazione corretti

Quando si prendono decisioni relative all'Video Indexer su larga scala, vedere come ottenere il massimo con i parametri corretti per le proprie esigenze. Si pensi al caso d'uso, definendo parametri diversi è possibile risparmiare denaro e velocizzare il processo di indicizzazione per i video.

Prima di caricare e indicizzare il video, leggere questa breve [documentazione,](upload-index-videos.md)vedere [indexingPreset](upload-index-videos.md#indexingpreset) e [streamingPreset](upload-index-videos.md#streamingpreset) per avere un'idea più chiara delle opzioni disponibili.

Ad esempio, non impostare il set di impostazioni per lo streaming se non si prevede di guardare il video, non indicizzare le informazioni dettagliate sui video se sono necessarie solo informazioni dettagliate sull'audio.

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>Indice con risoluzione ottimale, non con risoluzione massima

Potrebbe essere necessario sapere quale qualità video è necessaria per l'indicizzazione dei video? 

In molti casi, le prestazioni di indicizzazione non hanno quasi alcuna differenza tra i video HD (720P) e i video 4K. Alla fine, si otterrà quasi le stesse informazioni dettagliate con la stessa attendibilità. Maggiore è la qualità del film caricato, maggiore sarà la dimensione del file, con una maggiore potenza di calcolo e il tempo necessario per caricare il video.

Ad esempio, per la funzionalità di rilevamento viso, una risoluzione più elevata può essere utile nello scenario in cui sono presenti molti visi piccoli ma contestualmente importanti. Tuttavia, ciò si verifica con un aumento quadratico del runtime e un maggiore rischio di falsi positivi.

È quindi consigliabile verificare di ottenere i risultati corretti per il caso d'uso e di testarlo prima in locale. Caricare lo stesso video in 720P e in 4K e confrontare le informazioni dettagliate che si ottengono.

## <a name="next-steps"></a>Passaggi successivi

[Esaminare l'output Video Indexer azure generato dall'API](video-indexer-output-json-v2.md)