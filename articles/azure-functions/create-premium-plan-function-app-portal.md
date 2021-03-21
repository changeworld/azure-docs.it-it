---
title: Creare un piano Premium di funzioni di Azure nel portale
description: Informazioni su come usare la portale di Azure per creare un'app per le funzioni che viene eseguita nel piano Premium.
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 9cab67f096665c9333fa40bcb790896fcbebd8d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98676587"
---
# <a name="create-a-premium-plan-function-app-in-the-azure-portal"></a>Creare un'app per le funzioni di piano Premium nella portale di Azure

Funzioni di Azure offre un piano Premium scalabile che offre connettività di rete virtuale, nessun avvio a freddo e hardware Premium. Per altre informazioni, vedere [piano Premium di funzioni di Azure](functions-premium-plan.md). 

Questo articolo illustra come usare la portale di Azure per creare un'app per le funzioni in un piano Premium. 

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com) con il proprio account Azure.

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

Per ospitare l'esecuzione delle funzioni è necessaria un'app per le funzioni. Un'app per le funzioni consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione, il ridimensionamento e la condivisione delle risorse.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

A questo punto, è possibile creare funzioni nella nuova app per le funzioni. Queste funzioni possono sfruttare i vantaggi del [piano Premium](functions-premium-plan.md).

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere una funzione attivata tramite HTTP] (./functions-get-started.md
