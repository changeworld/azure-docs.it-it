---
title: Creare un input del processo da un URL HTTPS
description: Questo argomento illustra come creare un input del processo di servizi multimediali di Azure da un URL HTTPS.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 1073a13f477894e1b35d732fd1cd6191747a62a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955988"
---
# <a name="create-a-job-input-from-an-https-url"></a>Creare un input del processo da un URL HTTPS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In Servizi multimediali v3, quando si inviano processi per elaborare i video, è necessario indicare a Servizi multimediali dove trovare il video di input. Una delle opzioni consiste nello specificare un URL HTTPS come input del processo, come illustrato in questo esempio. Attualmente AMS v3 non supporta la codifica di trasferimenti in blocchi su URL HTTPS. Per un esempio completo, vedere questo [esempio di GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

> [!TIP]
> Prima di iniziare lo sviluppo, vedere [sviluppo con le API di servizi multimediali V3](media-services-apis-overview.md) (include informazioni sull'accesso alle API, alle convenzioni di denominazione e così via).

## <a name="net-sample"></a>Esempio .NET

Il codice seguente mostra come creare un processo da un input URL HTTP(s).

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Codici di errore dei processi

Vedere i [codici di errore](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Passaggi successivi

[Creare un input del processo da un file locale](job-input-from-local-file-how-to.md).
