---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Sottoscrivere un argomento personalizzato | Microsoft Docs
description: Questo articolo include un esempio di script dell'interfaccia della riga di comando di Azure che illustra come sottoscrivere gli eventi di Griglia di eventi per un argomento personalizzato.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: eaec3521c90a0fb6425f5b68425ef608ae6db3c5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871310"
---
# <a name="subscribe-to-events-for-a-custom-topic-with-azure-cli"></a>Sottoscrivere eventi per un argomento personalizzato con l'interfaccia della riga di comando di Azure

Lo script crea una sottoscrizione di Griglia di eventi per gli eventi relativi a un argomento personalizzato.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Lo script di esempio disponibile in anteprima richiede l'estensione Griglia di eventi. Per installarla, eseguire `az extension add --name eventgrid`.

## <a name="sample-script---stable"></a>Script di esempio - stabile

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-custom-topic/subscribe-to-custom-topic.sh "Subscribe to custom topic")]

## <a name="sample-script---preview-extension"></a>Script di esempio - estensione in anteprima

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-custom-topic-preview/subscribe-to-custom-topic-preview.sh "Subscribe to custom topic")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa il comando seguente per creare una sottoscrizione a eventi. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | Creare una sottoscrizione di Griglia di eventi. |
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) - versione dell'estensione | Creare una sottoscrizione di Griglia di eventi. |

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle query sulle sottoscrizioni, vedere [Eseguire query sulle sottoscrizioni di Griglia di eventi](../query-event-subscriptions.md).
* Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).
