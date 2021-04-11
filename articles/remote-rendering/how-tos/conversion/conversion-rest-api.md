---
title: API REST per la conversione di asset
description: Viene descritto come convertire un asset tramite l'API REST
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: e3db4b9c9b4a05142f1327f681b067748cb1a2f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951641"
---
# <a name="use-the-model-conversion-rest-api"></a>Usare l'API REST per la conversione di modelli

Il servizio di [conversione del modello](model-conversion.md) viene controllato tramite un' [API REST](https://en.wikipedia.org/wiki/Representational_state_transfer). Questa API può essere usata per creare conversioni, ottenere le proprietà di conversione ed elencare le conversioni esistenti.

## <a name="rest-api-reference"></a>Informazioni di riferimento sulle API REST

La documentazione di riferimento dell'API REST per il rendering remoto è disponibile [qui](/rest/api/mixedreality/2021-01-01/remoterendering)e le definizioni di spavalderia [qui](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality).

Viene fornito uno script di PowerShell nel [repository di esempi arr](https://github.com/Azure/azure-remote-rendering) nella cartella *Scripts* , denominato *Conversion.ps1*, che illustra l'uso del servizio. Lo script e la relativa configurazione sono descritti di seguito: [script di PowerShell di esempio](../../samples/powershell-example-scripts.md). Sono inoltre disponibili SDK per [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/remoterendering/Azure.MixedReality.RemoteRendering/README.md) e [Java]( https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/remoterendering/azure-mixedreality-remoterendering/README.md).

## <a name="next-steps"></a>Passaggi successivi

- [Usare l'archiviazione BLOB di Azure per la conversione dei modelli](blob-storage.md)
- [Conversione di modelli](model-conversion.md)