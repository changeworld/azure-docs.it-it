---
title: Trigger di hub eventi di Azure per funzioni di Azure
description: Informazioni su come usare il trigger di hub eventi di Azure in funzioni di Azure.
author: craigshoemaker
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: c2b8302e64f7dcc657fd20ed5d918ed6816d750d
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608913"
---
# <a name="azure-event-hubs-trigger-for-azure-functions"></a>Trigger di hub eventi di Azure per funzioni di Azure

Questo articolo illustra come usare il trigger di [Hub eventi di Azure](../event-hubs/event-hubs-about.md) per funzioni di Azure. Funzioni di Azure supporta il trigger e le [associazioni di output](functions-bindings-event-hubs-output.md) per hub eventi.

Per informazioni sui dettagli di impostazione e configurazione, vedere la [panoramica](functions-bindings-event-hubs.md).

[!INCLUDE [functions-bindings-event-hubs-trigger](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-settings"></a>impostazioni host.json

Il [host.jsnel](functions-host-json.md#eventhub) file contiene le impostazioni che controllano il comportamento del trigger dell'hub eventi. Per informazioni dettagliate sulle impostazioni disponibili, vedere la sezione [host.jsimpostazioni](functions-bindings-event-hubs.md#hostjson-settings) .

## <a name="next-steps"></a>Passaggi successivi

- [Scrivere eventi in un flusso di eventi (associazione di output)](./functions-bindings-event-hubs-output.md)
