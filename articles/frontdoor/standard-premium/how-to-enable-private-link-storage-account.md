---
title: Connettere Azure front door Premium a un account di archiviazione Origin con collegamento privato
titleSuffix: Azure Private Link
description: Informazioni su come connettere Azure front door Premium a un account di archiviazione privatamente.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/04/2021
ms.author: tyao
ms.openlocfilehash: 885b4d132208ab6f8b470d147438e26a5fd4bab7
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201669"
---
# <a name="connect-azure-front-door-premium-to-a-storage-account-origin-with-private-link"></a>Connettere Azure front door Premium a un account di archiviazione Origin con collegamento privato

Questo articolo illustra come configurare lo SKU di Azure front door Premium per connettersi all'origine dell'account di archiviazione privatamente usando il servizio di collegamento privato di Azure.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="enable-private-link-to-a-storage-account"></a>Abilita collegamento privato a un account di archiviazione
 
In questa sezione si eseguirà il mapping del servizio di collegamento privato a un endpoint privato creato nella rete privata di Azure front door. 

1. Nel profilo Premium di Azure front door selezionare **gruppi di origine** in *Impostazioni*.

1. Selezionare il gruppo di origine che contiene l'origine dell'account di archiviazione per cui si vuole abilitare il collegamento privato.

1. Selezionare **+ Aggiungi un'origine** per aggiungere una nuova origine dell'account di archiviazione oppure selezionare un'origine dell'account di archiviazione creata in precedenza dall'elenco.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-storage-account.png" alt-text="Screenshot dell'abilitazione del collegamento privato a un account di archiviazione.":::

1. Per **selezionare una risorsa di Azure**, selezionare **nella directory**. Selezionare o immettere le impostazioni seguenti per configurare il sito a cui si vuole connettere Azure front door Premium con privato.

    | Impostazione | Valore |
    | ------- | ----- |
    | Region | Selezionare l'area che è uguale o più vicina all'origine. |
    | Tipo di risorsa | Selezionare **Microsoft. storage/storageAccounts**. |
    | Risorsa | Selezionare l'account di archiviazione. |
    | Risorsa secondaria di destinazione | È possibile selezionare *BLOB* o *Web*. |
    | Messaggio di richiesta | Personalizzare il messaggio o scegliere il valore predefinito. |

1. Quindi selezionare **Aggiungi** per salvare la configurazione.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>Approva la connessione all'endpoint privato dall'account di archiviazione

1. Passare all'account di archiviazione per cui si configura il collegamento privato nell'ultima sezione. Selezionare **rete** in **Impostazioni**.

1. In **rete** selezionare **connessioni endpoint privato**. 

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/storage-account-configure-endpoint.png" alt-text="Screenshot delle impostazioni di rete in un'app Web.":::

1. Selezionare la richiesta di endpoint privato *in sospeso* da Azure front door Premium e quindi scegliere **approva**.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-pending-approval.png" alt-text="Screenshot della richiesta di endpoint privato di archiviazione in sospeso.":::

1. Una volta approvata, dovrebbe essere simile alla schermata seguente. Il completamento della connessione può richiedere alcuni minuti. È ora possibile accedere all'account di archiviazione da Azure front door Premium.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="Screenshot della richiesta dell'endpoint di archiviazione approvato.":::

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul [servizio di collegamento privato con l'account di archiviazione](../../storage/common/storage-private-endpoints.md).
