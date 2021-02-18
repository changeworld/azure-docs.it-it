---
title: Connessione di Azure front door Premium all'origine con collegamento privato
description: Informazioni su come connettere Azure front door Premium all'origine con il servizio di collegamento privato usando il portale di Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: 3015a0560171b61aaab05e27b369d9ca531e81c1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099462"
---
# <a name="connect-azure-front-door-premium-to-your-origin-with-private-link"></a>Connessione di Azure front door Premium all'origine con collegamento privato

Questo articolo illustra come configurare lo SKU di Azure front door Premium per connettersi alle applicazioni ospitate in una rete virtuale usando il servizio di collegamento privato di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Creare un servizio di [collegamento privato](../../private-link/create-private-link-service-portal.md) per i server Web di origine.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="enable-private-endpoint-in-azure-front-door-service"></a>Abilitare un endpoint privato nel servizio front door di Azure

In questa sezione si eseguirà il mapping del servizio di collegamento privato di Azure a un endpoint privato creato nella rete privata dello SKU di Azure front door Premium. 

1. Nel profilo Premium di Azure front door selezionare **gruppi di origine** in *Impostazioni*.

1. Selezionare il gruppo di origine che contiene l'origine per cui si vuole abilitare il collegamento privato.

1. Selezionare **+ Aggiungi un'origine** per aggiungere una nuova origine o selezionare un'origine creata in precedenza dall'elenco. Selezionare quindi la casella di controllo per **abilitare il servizio di collegamento privato**.

    :::image type="content" source="../media/how-to-enable-private-link/front-door-private-endpoint-private-link.png" alt-text="Screenshot dell'abilitazione del collegamento privato in aggiungere una pagina di origine.":::

1. Per **selezionare una risorsa di Azure**, selezionare **nella directory**. Selezionare o immettere l'impostazione seguente per configurare la risorsa per cui si vuole connettere Azure front door Premium con privato.
    
    | Impostazione | Valore |
    | ------- | ----- |
    | Region | Selezionare l'area che è uguale o più vicina all'origine. |
    | Tipo di risorsa | Selezionare **Microsoft. Network/privateLinkServices**. |
    | Risorsa | Selezionare **myPrivateLinkService**. |
    | Risorsa secondaria di destinazione | Lasciare vuoto il campo. |
    | Messaggio di richiesta | Personalizzare il messaggio o scegliere il messaggio predefinito. |

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul [collegamento privato di Azure front door Premium](concept-private-link.md).
