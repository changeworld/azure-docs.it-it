---
title: Creare un'entità servizio di Azure
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 2f60759fbbae2c0f712a475ef397ca260e1f0415
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473814"
---
## <a name="create-an-azure-service-principal"></a>Creare un'entità servizio di Azure

Per fare in modo che l'applicazione interagisca con l'account Azure, è necessaria un'entità servizio di Azure per gestire le autorizzazioni. Seguire le istruzioni riportate in [Creare un'entità servizio di Azure](/powershell/azure/create-azure-service-principal-azureps).

Quando si crea un'entità servizio, si noterà che include un valore segreto, un ID e un ID applicazione. Salvare l'ID applicazione e il segreto in un percorso temporaneo per i passaggi successivi.
