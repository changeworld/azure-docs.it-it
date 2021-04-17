---
title: Configurare i log di controllo e i log di query lente con l'interfaccia della riga di comando di Azure - Database di Azure per MySQL - Server flessibile
description: Questo articolo descrive come configurare e accedere ai log di query lente nel server flessibile di Database di Azure per MySQL dall'interfaccia della riga di comando di Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: b42aba84dcbff795ee4ca1f8d622527e8039f27a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509142"
---
# <a name="configure-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Configurare log di query lente per Database di Azure per MySQL - Server flessibile usando l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

L'articolo illustra come configurare log di [query lente per](concepts-slow-query-logs.md) il server flessibile MySQL usando l'interfaccia della riga di comando di Azure. 

## <a name="prerequisites"></a>Prerequisiti
- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Installare o aggiornare l'interfaccia della riga di comando di Azure alla versione più recente. Vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
-  Accedere all'account Azure usando [il comando az login.](/cli/azure/reference-index#az-login) Annotare la proprietà **id** che fa riferimento all'**ID sottoscrizione** per l'account Azure.

    ```azurecli-interactive
    az login
    ````

- Se si dispone di più sottoscrizioni, scegliere la sottoscrizione appropriata in cui si vuole creare il server usando il ```az account set``` comando .
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- Creare un server flessibile MySQL se non ne è già stato creato uno usando il ```az mysql flexible-server create``` comando .

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="configure-slow-query-logs"></a>Configurare i log di query lente

>[!IMPORTANT]
> È consigliabile registrare solo i tipi di eventi e gli utenti necessari ai fini del controllo per garantire che le prestazioni del server non siano notevolmente influenzate.

Abilitare e configurare i log di query lente per il server.

```azurecli
# Turn on statement level log
az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 sec
# This setting will log all queries executing for more than 10 sec. Please adjust this threshold based on your definition for slow queries
az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable Slow query logs
az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON

```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sui [log di query lente](concepts-slow-query-logs.md)
