---
title: Connettere Azure front door Premium a un'origine del servizio di bilanciamento del carico interno con collegamento privato
titleSuffix: Azure Private Link
description: Informazioni su come connettere il servizio di Azure front door Premium a un servizio di bilanciamento del carico interno.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/16/2021
ms.author: tyao
ms.openlocfilehash: 6876692bcf752570ecdc5d42b65da81992ad3743
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803727"
---
# <a name="connect-azure-front-door-premium-to-an-internal-load-balancer-origin-with-private-link"></a>Connettere Azure front door Premium a un'origine del servizio di bilanciamento del carico interno con collegamento privato

Questo articolo illustra come configurare lo SKU di Azure front door Premium per connettersi all'origine del servizio di bilanciamento del carico interno usando il servizio di collegamento privato di Azure.

## <a name="prerequisites"></a>Prerequisiti

Creare un [servizio di collegamento privato](../../private-link/create-private-link-service-portal.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="enable-private-link-to-an-internal-load-balancer"></a>Abilita collegamento privato a un servizio di bilanciamento del carico interno
 
In questa sezione si eseguirà il mapping del servizio di collegamento privato a un endpoint privato creato nella rete privata di Azure front door. 

1. Nel profilo Premium di Azure front door selezionare **gruppi di origine** in *Impostazioni*.

1. Selezionare il gruppo di origine che si vuole abilitare come collegamento privato per il servizio di bilanciamento del carico interno.

1. Selezionare **+ Aggiungi un'origine** per aggiungere un'origine del servizio di bilanciamento del carico interno.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-internal-load-balancer.png" alt-text="Screenshot dell'abilitazione del collegamento privato a un servizio di bilanciamento del carico interno.":::

1. Per **selezionare una risorsa di Azure**, selezionare **nella directory**. Selezionare o immettere le impostazioni seguenti per configurare il sito a cui si vuole connettere Azure front door Premium con privato.

    | Impostazione | Valore |
    | ------- | ----- |
    | Region | Selezionare l'area che è uguale o più vicina all'origine. |
    | Tipo di risorsa | Selezionare **Microsoft. Network/privateLinkServices**. |
    | Risorsa | Selezionare il collegamento privato associato al servizio di bilanciamento del carico interno. |
    | Risorsa secondaria di destinazione | Lasciare vuoto. |
    | Messaggio di richiesta | Personalizzare il messaggio o scegliere il valore predefinito. |

1. Fare quindi clic su **Aggiungi** e quindi su **Aggiorna** per salvare la configurazione.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>Approva la connessione all'endpoint privato dall'account di archiviazione

1. Accedere al centro collegamenti privati e selezionare **servizi di collegamento privato**. Quindi selezionare il nome del collegamento privato.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/list.png" alt-text="Screenshot dell'elenco di collegamenti privati.":::

1. Selezionare **connessioni endpoint privato** in *Impostazioni*.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/overview.png" alt-text="Screenshot della pagina di panoramica del collegamento privato.":::

1. Selezionare la richiesta di endpoint privato *in sospeso* da Azure front door Premium e quindi scegliere **approva**.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-pending-approval.png" alt-text="Screenshot dell'approvazione in sospeso per il collegamento privato.":::

1. Una volta approvata, dovrebbe essere simile alla schermata seguente. Il completamento della connessione può richiedere alcuni minuti. È ora possibile accedere al servizio di bilanciamento del carico interno da Azure front door Premium.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="Screenshot della richiesta di collegamento privato approvato.":::

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul [servizio di collegamento privato](../../private-link/private-link-service-overview.md).
