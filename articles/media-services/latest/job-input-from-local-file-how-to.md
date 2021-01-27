---
title: Creare un input del processo di servizi multimediali di Azure da un file locale
description: Questo articolo illustra come creare un input del processo di servizi multimediali di Azure da un file locale.
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
ms.openlocfilehash: 2121807405849b74b354d9e638a79948c1501da5
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895340"
---
# <a name="create-a-job-input-from-a-local-file"></a>Creare un input del processo da un file locale

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In Servizi multimediali v3, quando si inviano processi per elaborare i video, è necessario indicare a Servizi multimediali dove trovare il video di input. È possibile archiviare il video di input come asset di Servizi multimediali, nel qual caso si crea un asset di input basato su un file archiviato in locale o nel servizio di Archiviazione BLOB di Azure. Questo argomento illustra come creare un input del processo da un file locale. Per un esempio completo, vedere questo [esempio di GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="prerequisites"></a>Prerequisiti 

* [Creare un account di Servizi multimediali di Azure](./create-account-howto.md).

## <a name="net-sample"></a>Esempio .NET

Il codice seguente mostra come creare un asset di input da usare come input per il processo. La funzione CreateInputAsset esegue le azioni seguenti:

* Crea l'asset
* Ottiene un [URL](../../storage/common/storage-sas-overview.md) di firma di accesso condiviso scrivibile per il contenitore dell'asset [nell'archivio](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container)
* Carica il file nel contenitore nel servizio di archiviazione usando l'URL di firma di accesso condiviso.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Il frammento di codice seguente crea un asset di output se non esiste già:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Il frammento di codice seguente invia un processo di codifica:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Codici di errore dei processi

Vedere i [codici di errore](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Passaggi successivi

[Creare un input del processo da un URL HTTPS](job-input-from-http-how-to.md).
