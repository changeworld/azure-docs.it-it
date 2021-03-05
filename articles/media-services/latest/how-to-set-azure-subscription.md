---
title: Come trovare la sottoscrizione di Azure
description: Trovare la sottoscrizione di Azure in modo da poter configurare l'ambiente.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: cli,portal, devx-track-azurecli
ms.openlocfilehash: f16cc83257dbf6419fc794f34e4cd6df033d3f8b
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213964"
---
# <a name="find-your-azure-subscription"></a>Trovare la sottoscrizione di Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Portale](#tab/portal/)

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nell'intestazione servizi di Azure selezionare sottoscrizioni. Se non sono elencate sottoscrizioni, potrebbe essere necessario passare Azure AD tenant. Gli ID sottoscrizione sono elencati nella seconda colonna.
1. Copiare l'ID sottoscrizione e incollarlo in un documento di testo di propria scelta per l'uso in un secondo momento.

## <a name="cli"></a>[CLI](#tab/cli/)

## <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the "task-" prepended to the file name. -->

### <a name="list-your-azure-subscriptions-with-cli"></a>Elencare le sottoscrizioni di Azure con CLI

[!INCLUDE [List your Azure subscriptions with CLI](./includes/task-list-set-subscription-cli.md)]

### <a name="see-also"></a>Vedi anche

* [Interfaccia della riga di comando di Azure](/cli/azure/ams)

---

## <a name="next-steps"></a>Passaggi successivi

[Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)
