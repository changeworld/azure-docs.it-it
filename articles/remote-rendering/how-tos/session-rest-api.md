---
title: API REST di gestione della sessione
description: Viene descritto come gestire le sessioni
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 4323884b2dee3eeccfe71ec7817d92467450e88e
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950026"
---
# <a name="use-the-session-management-rest-api"></a>Usare l'API REST di gestione delle sessioni

Per usare la funzionalità di rendering remoto di Azure, è necessario creare una *sessione*. Ogni sessione corrisponde a un server allocato in Azure a cui un dispositivo client può connettersi. Quando un dispositivo si connette, il server esegue il rendering dei dati richiesti e fornisce il risultato come flusso video. Durante la creazione della sessione, è stato scelto il [tipo di server](../reference/vm-sizes.md) in cui si desidera eseguire, che determina i prezzi. Quando la sessione non è più necessaria, è consigliabile arrestarla. Se non viene arrestato manualmente, viene arrestato automaticamente al termine del *periodo di lease* della sessione.

## <a name="rest-api-reference"></a>Informazioni di riferimento sulle API REST

Le informazioni di riferimento sull'API REST sono disponibili [qui](/rest/api/mixedreality/2021-01-01preview/remoterendering) e le definizioni di spavalderia [qui](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality).
Viene fornito uno script di PowerShell nel [repository di esempi arr](https://github.com/Azure/azure-remote-rendering) nella cartella *Scripts* , denominato *RenderingSession.ps1*, che illustra l'uso del servizio. Lo script e la relativa configurazione sono descritti di seguito: [script di PowerShell di esempio](../samples/powershell-example-scripts.md).
Sono inoltre disponibili SDK per [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/remoterendering/Azure.MixedReality.RemoteRendering/README.md) e [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/remoterendering/azure-mixedreality-remoterendering/README.md).

> [!IMPORTANT]
> La latenza è un fattore importante quando si utilizza il rendering remoto. Per un'esperienza ottimale, creare sessioni nell'area più vicina. Il [test di latenza di Azure](https://www.azurespeed.com/Azure/Latency) può essere usato per determinare quale area è più vicina.

> [!IMPORTANT]
> Un SDK di runtime ARR è necessario affinché un dispositivo client si connetta a una sessione di rendering. Questi SDK sono disponibili in [.NET](/dotnet/api/microsoft.azure.remoterendering) e [C++](/cpp/api/remote-rendering/). Oltre alla connessione al servizio, questi SDK possono essere usati anche per avviare e arrestare le sessioni.

## <a name="next-steps"></a>Passaggi successivi

* [Usare le API front-end di Azure per l'autenticazione](frontend-apis.md)
* [Script di Azure PowerShell di esempio](../samples/powershell-example-scripts.md)