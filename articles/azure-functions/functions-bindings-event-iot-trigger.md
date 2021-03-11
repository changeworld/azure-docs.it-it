---
title: Trigger dell'hub Azure per funzioni di Azure
description: Informazioni su come rispondere agli eventi inviati a un flusso di eventi dell'hub Internet in funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 5c9309834b407ee56d29e38afd965ac947fc8a4f
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612287"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Trigger dell'hub Azure per funzioni di Azure

Questo articolo illustra come usare le associazioni di funzioni di Azure per l'hub Internet. Il supporto per hub eventi è basato sull' [associazione di hub eventi di Azure](functions-bindings-event-hubs.md).

Per informazioni sui dettagli di impostazione e configurazione, vedere la [panoramica](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Sebbene gli esempi di codice seguenti usino l'API dell'hub eventi, la sintassi specificata è applicabile alle funzioni dell'hub Internet.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-properties"></a>Proprietà di host.json

Il [host.jsnel](functions-host-json.md#eventhub) file contiene le impostazioni che controllano il comportamento del trigger dell'hub eventi. Per informazioni dettagliate sulle impostazioni disponibili, vedere la sezione [host.jsimpostazioni](functions-bindings-event-iot.md#hostjson-settings) .

## <a name="next-steps"></a>Passaggi successivi

- [Scrivere eventi in un flusso di eventi (associazione di output)](./functions-bindings-event-iot-output.md)
