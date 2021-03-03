---
title: Connettere Azure front door Premium a un'origine app Web con collegamento privato
titleSuffix: Azure Private Link
description: Informazioni su come connettere Azure front door Premium a una webapp privata.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: f3250101449971ffd985fcfcce400870e2a3d50f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745365"
---
# <a name="connect-azure-front-door-premium-to-a-web-app-origin-with-private-link"></a>Connettere Azure front door Premium a un'origine app Web con collegamento privato

Questo articolo illustra come configurare lo SKU di Azure front door Premium per connettersi all'app Web privatamente usando il servizio di collegamento privato di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Creare un servizio di [collegamento privato](../../private-link/create-private-link-service-portal.md) per i server Web di origine.

> [!Note]
> L'endpoint privato è disponibile nelle aree pubbliche per le app Web Windows del livello PremiumV2, PremiumV3, le app Web Linux e il piano Premium di Funzioni di Azure (talvolta definito piano elastico Premium).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="enable-private-link-to-a-web-app-in-azure-front-door-premium"></a>Abilitare il collegamento privato a un'app Web in Azure front door Premium
 
In questa sezione si eseguirà il mapping del servizio di collegamento privato a un endpoint privato creato nella rete privata di Azure front door. 

1. Nel profilo Premium di Azure front door selezionare **gruppi di origine** in *Impostazioni*.

1. Selezionare il gruppo di origine che contiene l'origine dell'app Web per cui si vuole abilitare il collegamento privato.

1. Selezionare **+ Aggiungi un'origine** per aggiungere una nuova origine app Web oppure selezionare un'origine app Web creata in precedenza dall'elenco.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-web-app.png" alt-text="Screenshot dell'abilitazione del collegamento privato a un'app Web.":::

1. Per **selezionare una risorsa di Azure**, selezionare **nella directory**. Selezionare o immettere le impostazioni seguenti per configurare il sito a cui si vuole connettere Azure front door Premium con privato.

    | Impostazione | Valore |
    | ------- | ----- |
    | Region | Selezionare l'area che è uguale o più vicina all'origine. |
    | Tipo di risorsa | Selezionare **Microsoft.Web/sites**. |
    | Risorsa | Selezionare **myPrivateLinkService**. |
    | Risorsa secondaria di destinazione | siti |
    | Messaggio di richiesta | Personalizzare il messaggio o scegliere il valore predefinito. |

1. Quindi selezionare **Aggiungi** per salvare la configurazione.

## <a name="approve-azure-front-door-premium-private-endpoint-connection-from-web-app"></a>Approva la connessione all'endpoint privato di Azure front door Premium dall'app Web

1. Passare all'app Web per cui si configura il collegamento privato nell'ultima sezione. Selezionare **rete** in **Impostazioni**.

1. In **Rete** selezionare **Configurare le connessioni endpoint privato**.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/web-app-configure-endpoint.png" alt-text="Screenshot delle impostazioni di rete in un'app Web.":::

1. Selezionare la richiesta di endpoint privato *in sospeso* da Azure front door Premium e quindi scegliere **approva**.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-pending-approval.png" alt-text="Screenshot della richiesta di endpoint privato in sospeso.":::

1. Una volta approvata, dovrebbe essere simile alla schermata seguente. Il completamento della connessione può richiedere alcuni minuti. È ora possibile accedere all'app Web da Azure front door Premium. L'accesso diretto all'app Web dalla rete Internet pubblica viene disabilitato dopo l'abilitazione dell'endpoint privato.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-approved.png" alt-text="Screenshot della richiesta dell'endpoint approvato.":::

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul [servizio di collegamento privato con app Web di Azure](../../app-service/networking/private-endpoint).
