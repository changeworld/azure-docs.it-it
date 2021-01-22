---
title: Confronto tra gli esempi di migrazione di servizi multimediali da V2 a V3 | Microsoft Docs
description: Set di esempi che consentono di confrontare le differenze del codice tra servizi multimediali di Azure V2 e V3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2021
ms.author: inhenkel
ms.openlocfilehash: 7705443bbe810866cd5b52946b310b5b79bfa072
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690372"
---
# <a name="media-services-migration-code-sample-comparison"></a>Confronto degli esempi di codice di migrazione di servizi multimediali

![logo della Guida alla migrazione](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

È possibile usare alcuni esempi di codice per confrontare il modo in cui vengono eseguite le operazioni tra gli SDK.

## <a name="samples-for-comparison"></a>Esempi per il confronto

La tabella seguente è un elenco di esempi per il confronto tra V2 e V3 per gli scenari comuni.

|Scenario|API v2|API V3|
|---|---|---|
|Creare un asset e caricare un file |[Esempio .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[Esempio .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Inviare un processo|[Esempio .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[Esempio .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Mostra come creare una trasformazione e quindi inviare un processo.|
|Pubblicare un asset con crittografia AES |1. crea `ContentKeyAuthorizationPolicyOption`<br/>2. crea `ContentKeyAuthorizationPolicy`<br/>3. creazione `AssetDeliveryPolicy`<br/>4. creazione `Asset` e caricamento di contenuto o invio `Job` e utilizzo `OutputAsset`<br/>5. associa `AssetDeliveryPolicy` a `Asset`<br/>6. creazione `ContentKey`<br/>7. Connetti `ContentKey` a `Asset`<br/>8. creazione `AccessPolicy`<br/>9. creazione `Locator`<br/><br/>[Esempio .NET v2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. crea `ContentKeyPolicy`<br/>2. crea `Asset`<br/>3. caricare il contenuto o usare `Asset` come `JobOutput`<br/>4. creazione `StreamingLocator`<br/><br/>[Esempio .NET v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Ottenere i dettagli del processo e gestire i processi |[Gestire i processi con V2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Gestire i processi con V3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
