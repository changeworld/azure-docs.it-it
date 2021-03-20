---
title: Arrestare/avviare portale di Azure-database di Azure per il server MySQL
description: Questo articolo descrive come arrestare/avviare le operazioni nel database di Azure per MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: d297d215d4b0edfdd67b603ba4707bf02057ad78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516873"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Arrestare/avviare un database di Azure per MySQL

> [!IMPORTANT]
>  Quando si **Arresta** il server, rimane in tale stato per i 7 giorni successivi in un'estensione. Se non viene **avviata** manualmente durante questo periodo di tempo, il server verrà avviato automaticamente alla fine dei 7 giorni. Se non si utilizza il server, è possibile scegliere di **arrestarlo** di nuovo.

In questo articolo viene fornita una procedura dettagliata per eseguire l'arresto e l'avvio del singolo server.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

-   È necessario disporre di un server singolo database di Azure per MySQL.

> [!NOTE]
> Vedere la limitazione dell'uso di [Stop/Start](concepts-servers.md#limitations-of-stopstart-operation)

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>Come arrestare/avviare il database di Azure per MySQL usando portale di Azure

### <a name="stop-a-running-server"></a>Arrestare un server in esecuzione

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere il server MySQL che si desidera arrestare.

2.  Nella pagina **Panoramica** fare clic sul pulsante **Arresta** sulla barra degli strumenti.

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Server di arresto per database di Azure per MySQL":::

    > [!NOTE]
    > Una volta arrestato il server, le altre operazioni di gestione non sono disponibili per il singolo server.

### <a name="start-a-stopped-server"></a>Avviare un server arrestato

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere il singolo server che si desidera avviare.

2.  Nella pagina **Panoramica** fare clic sul pulsante **Avvia** sulla barra degli strumenti.

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Server di avvio del database di Azure per MySQL":::

    > [!NOTE]
    > Una volta avviato il server, tutte le operazioni di gestione sono ora disponibili per il singolo server.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>Come arrestare/avviare il database di Azure per MySQL tramite l'interfaccia della riga di comando

### <a name="stop-a-running-server"></a>Arrestare un server in esecuzione

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere il server MySQL che si desidera arrestare.

2.  Nella pagina **Panoramica** fare clic sul pulsante **Arresta** sulla barra degli strumenti.

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Una volta arrestato il server, le altre operazioni di gestione non sono disponibili per il singolo server.

### <a name="start-a-stopped-server"></a>Avviare un server arrestato

1.  Nella [portale di Azure](https://portal.azure.com/)scegliere il singolo server che si desidera avviare.

2.  Nella pagina **Panoramica** fare clic sul pulsante **Avvia** sulla barra degli strumenti.

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Una volta avviato il server, tutte le operazioni di gestione sono ora disponibili per il singolo server.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su [come creare avvisi per le metriche](howto-alert-on-metric.md).
