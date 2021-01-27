---
title: Guida alla migrazione basata su scenari di codifica
description: Questo articolo fornisce indicazioni sulla codifica basate sugli scenari che aiuteranno a eseguire la migrazione da servizi multimediali di Azure V2 a V3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: f8d25a14593c3c4a26ecafa33f7cc9fe6c4d580a
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898359"
---
# <a name="encoding-scenario-based-migration-guidance"></a>Guida alla migrazione basata su scenari di codifica

![logo della Guida alla migrazione](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![passaggi della migrazione 2](./media/migration-guide/steps-4.svg)

Questo articolo fornisce indicazioni sulla codifica basate sugli scenari che aiuteranno a eseguire la migrazione da servizi multimediali di Azure V2 a V3.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a modificare il flusso di lavoro di codifica, è consigliabile comprendere le differenze nella modalità di gestione dell'archiviazione.  In AMS V3, l'API di archiviazione di Azure viene usata per gestire gli account di archiviazione associati all'account di servizi multimediali.

> [!NOTE]
> I processi e le attività creati nella versione V2 non vengono visualizzati nella versione V3 perché non sono associati a una trasformazione. Si consiglia di passare a trasformazioni e processi V3.

## <a name="encoding-workflow-comparison"></a>Confronto tra flussi di lavoro di codifica

Per un confronto visivo dei flussi di lavoro di codifica per V2 e V3, è necessario esaminare i diagrammi di flusso seguenti.

### <a name="v2-encoding-workflow"></a>Flusso di lavoro di codifica V2

Fare clic sull'immagine seguente per visualizzare una versione più grande.

[![Flusso di lavoro di codifica per V2 ](./media/migration-guide/V2-pretty.svg)](./media/migration-guide/V2-pretty.svg#lightbox)

1. Configurazione
    1. Creare un asset o usare un asset esistente. Se si usa un nuovo asset, caricare il contenuto nell'asset. Se si usa un asset esistente, è necessario codificare i file già esistenti nell'asset.
    2. Ottenere i valori degli elementi seguenti:
        - ID o oggetto media processor
        - Stringa del codificatore (nome) del codificatore che si vuole usare
        - ID asset del nuovo asset o ID dell'asset esistente
    3. Per il monitoraggio, creare una sottoscrizione di notifica a livello di processo o di attività o un gestore eventi SDK
2. Creare il processo che contiene l'attività o le attività. Ogni attività deve includere gli elementi precedenti e:
    - Direttiva che deve essere creata da un asset di output.  L'asset di output viene creato dal sistema.
    - Nome facoltativo per l'asset di output
3. Inviare il processo.
4. Monitorare il processo.

### <a name="v3-encoding-workflow"></a>Flusso di lavoro di codifica V3

[![Flusso di lavoro di codifica per V3 ](./media/migration-guide/V3-pretty.svg)](./media/migration-guide/V3-pretty.svg#lightbox)

1. Configurazione
    1. Creare un asset o usare un asset esistente. Se si usa un nuovo asset, caricare il contenuto nell'asset. Se si usa un asset esistente, è necessario codificare i file già esistenti nell'asset. Non è necessario *caricare altro contenuto nell'asset.*
    1. Creare un asset di output.  L'asset di output è la posizione in cui verranno archiviati i file codificati e i metadati di input e output.
    1. Ottenere i valori per la trasformazione:
        - Set di impostazioni standard del codificatore
        - Gruppo di risorse AMS
        - Nome dell'account AMS
    1. Creare la trasformazione o utilizzare una trasformazione esistente.  Le trasformazioni sono riutilizzabili. Non è necessario creare una nuova trasformazione ogni volta che si vuole inviare un processo.
1. Creare un processo
    1. Per il processo, ottenere i valori per gli elementi seguenti:
        - Nome trasformazione
        - URI di base per l'URL SAS per l'asset, il percorso di origine HTTPs della condivisione file o il percorso locale dei file. `JobInputAsset`Può anche usare un nome di asset come input.
        - Nome/i file
        - Asset di output
        - Un gruppo di risorse
        - Nome dell'account AMS  
1. Usare [griglia di eventi](monitor-events-portal-how-to.md) per il monitoraggio del processo.
1. Inviare il processo.

## <a name="custom-presets-from-v2-to-v3-encoding"></a>Set di impostazioni personalizzati dalla codifica V2 alla versione V3

Se il codice V2 ha chiamato il codificatore standard con un set di impostazioni personalizzato, è prima di tutto necessario creare una nuova trasformazione con il set di impostazioni del codificatore standard personalizzato prima di inviare un processo.

I set di impostazioni personalizzati sono ora JSON e non sono più basati su XML. Ricreare il set di impostazioni in JSON dopo lo schema del set di impostazioni personalizzato, come definito nella documentazione di [Transform Open API (spavalderia)](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/examples/transforms-create.json) .


<!-- removed because this is covered in the tutorials
Common custom [encoding](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json) scenarios:
        1. Create a custom Single Bitrate MP4 encode
        1. Create a custom [Adaptive Bitrate Encoding Ladder](autogen-bitrate-ladder.md)
        1. Creating Sprite Thumbnails
        1. Creating Thumbnails (see below for your preferred method)
        1. [Sub Clipping](subclip-video-rest-howto.md)
        1. Cropping
-->

## <a name="input-and-output-metadata-files-from-an-encoding-job"></a>File di metadati di input e output da un processo di codifica

Nella versione V2, i file di metadati di input e output XML vengono generati come risultato di un processo di codifica. In V3, il formato dei metadati è stato modificato da XML a JSON. Per altre informazioni sui metadati, vedere metadati di [input](input-metadata-schema.md) e [metadati di output](output-metadata-schema.md).

## <a name="premium-encoder-to-v3-standard-encoder-or-partner-based-solutions"></a>Codificatore Premium per il codificatore standard V3 o soluzioni basate su partner

L'API v2 non supporta più il codificatore Premium. Se in precedenza è stato usato il codificatore Premium basato sul flusso di lavoro per la codifica HEVC, è necessario eseguire la migrazione al nuovo [codificatore v3 standard](media-encoder-standard-formats.md) con supporto della codifica HEVC.

Se sono necessarie le funzionalità avanzate del flusso di lavoro del codificatore Premium, si consiglia di iniziare a usare una soluzione partner di codifica avanzata di Azure da [Imagine Communications](https://imaginecommunications.com), [Telestream](https://www.telestream.net)o [Bitmovin](https://bitmovin.com).

## <a name="jobs-with-inputs-that-are-on-https-hosted-urls"></a>Processi con input presenti in URL ospitati HTTPS

È ora possibile inviare i processi in V3 da file archiviati in archiviazione di Azure, archiviati localmente o server Web esterni usando il [supporto per l'input del processo http (S)](job-input-from-http-how-to.md).

Se in precedenza sono stati usati flussi di lavoro per copiare file da file BLOB di Azure in asset vuoti prima di inviare i processi, è possibile semplificare il flusso di lavoro passando un URL di firma di accesso condiviso per il file nell'archivio BLOB di Azure direttamente nel processo.

## <a name="indexer-v1-audio-transcription-to-the-new-audioanalyzer-basic-mode"></a>Trascrizione audio dell'indicizzatore V1 alla nuova AudioAnalyzer "modalità di base"

Per i clienti che usano il processore Indexer V1 nell'API v2, è necessario creare una trasformazione che richiama il nuovo `AudioAnalyzer` in [modalità di base](how-to-create-basic-audio-transform.md) prima di inviare un processo.

## <a name="encoding-transforms-and-jobs-concepts-tutorials-and-how-to-guides"></a>Concetti relativi a codifica, trasformazioni e processi, esercitazioni e procedure guidate

### <a name="concepts"></a>Concetti

- [Codifica di video e audio con servizi multimediali](encoding-concept.md)
- [Formati e codec standard del codificatore](media-encoder-standard-formats.md)
- [Codifica con una scala a bitrate generata automaticamente](autogen-bitrate-ladder.md)
- [Usare il set di impostazioni di codifica compatibile con il contenuto per trovare il valore di velocità in bit ottimale per una determinata risoluzione](content-aware-encoding.md)
- [Media reserved unit](concept-media-reserved-units.md)
- [Metadati di input](input-metadata-schema.md)
- [Metadati di output](output-metadata-schema.md)
- [Creazione dinamica dei pacchetti in servizi multimediali V3: codec audio](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging)

### <a name="tutorials"></a>Esercitazioni

- [Esercitazione: Codificare un file remoto basato su URL ed eseguire lo streaming del video - .NET](stream-files-dotnet-quickstart.md)
- [Esercitazione: Caricare, codificare ed eseguire lo streaming di video con Servizi multimediali v3](stream-files-tutorial-with-api.md)

### <a name="how-to-guides"></a>Guide pratiche

- [Creare un input del processo da un URL HTTPS](job-input-from-http-how-to.md)
- [Creare un input del processo da un file locale](job-input-from-local-file-how-to.md)
- [Creare una trasformazione audio di base](how-to-create-basic-audio-transform.md)
- Con .NET
  - [Come codificare con una trasformazione personalizzata-.NET](customize-encoder-presets-how-to.md)
  - [Come creare una sovrapposizione con Media Encoder Standard](how-to-create-overlay.md)
  - [Come generare anteprime usando il codificatore standard con .NET](media-services-generate-thumbnails-dotnet.md)
- Con l'interfaccia della riga di comando di Azure
  - [Come codificare con una trasformazione personalizzata-interfaccia della riga di comando di Azure](custom-preset-cli-howto.md)
- Con REST
  - [Come codificare con una trasformazione personalizzata-REST](custom-preset-rest-howto.md)
  - [Come generare anteprime usando il codificatore standard con REST](media-services-generate-thumbnails-rest.md)
- [Sottoclip di un video durante la codifica con servizi multimediali-.NET](subclip-video-dotnet-howto.md)
- [Sottoclip di un video durante la codifica con servizi multimediali-REST](subclip-video-rest-howto.md)

## <a name="samples"></a>Esempi

È anche possibile [confrontare il codice V2 e V3 negli esempi di codice](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
