---
title: Riavvio/arresto/avvio - portale di Azure - Server flessibile di Database di Azure per MySQL
description: Questo articolo descrive come riavviare/arrestare/avviare operazioni in Database di Azure per MySQL tramite l'interfaccia della riga di comando di Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: c9e646ad40c669e51f052ac9888cdd7c6883a848
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509109"
---
# <a name="restartstopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Riavviare/arrestare/avviare un database di Azure per MySQL - Server flessibile (anteprima)

> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Questo articolo illustra come eseguire il riavvio, l'avvio e l'arresto di un server flessibile tramite l'interfaccia della riga di comando di Azure.

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

## <a name="stop-a-running-server"></a>Arrestare un server in esecuzione
Per arrestare un server, eseguire il  ```az mysql flexible-server stop``` comando . Se si usa [il contesto](/cli/azure/config/param-persist)locale, non è necessario specificare argomenti.

**Utilizzo:**
```azurecli
az mysql flexible-server stop  [--name]
                               [--resource-group]
                               [--subscription]
```

**Esempio senza contesto locale:**
```azurecli
az mysql flexible-server stop  --resource-group --name myservername
```

**Esempio con il contesto locale:**
```azurecli
az mysql flexible-server stop
```

## <a name="start-a-stopped-server"></a>Avviare un server arrestato
Per avviare un server, eseguire il  ```az mysql flexible-server start``` comando . Se si usa [il contesto](/cli/azure/config/param-persist)locale, non è necessario specificare argomenti.

**Utilizzo:**
```azurecli
az mysql flexible-server start [--name]
                               [--resource-group]
                               [--subscription]
```

**Esempio senza contesto locale:**
```azurecli
az mysql flexible-server start  --resource-group --name myservername
```

**Esempio con contesto locale:**
```azurecli
az mysql flexible-server start
```

> [!IMPORTANT]
> Dopo il riavvio del server, tutte le operazioni di gestione sono ora disponibili per il server flessibile.

## <a name="restart-a-server"></a>Riavviare un server
Per riavviare un server, eseguire  ```az mysql flexible-server restart``` il comando . Se si usa [il contesto](/cli/azure/config/param-persist)locale, non è necessario specificare alcun argomento.

**Utilizzo:**
```azurecli
az mysql flexible-server restart [--name]
                                 [--resource-group]
                                 [--subscription]
```

**Esempio senza contesto locale:**
```azurecli
az mysql flexible-server restart  --resource-group --name myservername
```

**Esempio con contesto locale:**
```azurecli
az mysql flexible-server restart
```


> [!IMPORTANT]
> Dopo il riavvio del server, tutte le operazioni di gestione sono ora disponibili per il server flessibile.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla rete nel server flessibile di Database di [Azure per MySQL](./concepts-networking.md)
- [Creare e gestire la rete virtuale del server flessibile di Database di Azure per MySQL tramite il portale di Azure](./how-to-manage-virtual-network-portal.md).

