---
title: Ripristinare un server flessibile di Database di Azure per MySQL con l'interfaccia della riga di comando di Azure
description: Questo articolo descrive come eseguire operazioni di ripristino in Database di Azure per MySQL tramite l'interfaccia della riga di comando di Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 61a8439b770d8909e04d1b80a5219810dc72aa34
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509102"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-with-azure-cli"></a>Ripristino tempormente di un server flessibile di Database di Azure per MySQL con l'interfaccia della riga di comando di Azure


> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Questo articolo fornisce una procedura dettagliata per eseguire ripresi tempormente nel server flessibile usando i backup.

## <a name="prerequisites"></a>Prerequisiti
- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Installare o aggiornare l'interfaccia della riga di comando di Azure alla versione più recente. Vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
-  Accedere all'account Azure usando [il comando az login.](/cli/azure/reference-index#az-login) Annotare la proprietà **id** che fa riferimento all'**ID sottoscrizione** per l'account Azure.

    ```azurecli-interactive
    az login
    ````

- Se si hanno più sottoscrizioni, scegliere la sottoscrizione appropriata in cui si vuole creare il server usando il ```az account set``` comando .
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- Creare un server flessibile MySQL se non ne è già stato creato uno usando il ```az mysql flexible-server create``` comando .

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="restore-a-server-from-backup-to-a-new-server"></a>Ripristinare un server da un backup in un nuovo server

È possibile eseguire il comando seguente per ripristinare un server a un backup esistente meno recente.

**Utilizzo**
```azurecli
az mysql flexible-server restore --restore-time
                                 --source-server
                                 [--ids]
                                 [--location]
                                 [--name]
                                 [--no-wait]
                                 [--resource-group]
                                 [--subscription]
```

**Esempio:** Ripristinare un server da questo ```2021-03-03T13:10:00Z``` snapshot di backup.

```azurecli
az mysql server restore \
--name mydemoserver-restored \
--resource-group myresourcegroup \
--restore-point-in-time "2021-03-03T13:10:00Z" \
--source-server mydemoserver
```
Il tempo impiegato per il ripristino dipende dalle dimensioni dei dati archiviati nel server.

## <a name="perform-post-restore-tasks"></a>Eseguire le attività post-ripristino
Al termine del ripristino, è necessario eseguire le attività seguenti per ripristinare e eseguire gli utenti e le applicazioni:

- Se il nuovo server è destinato a sostituire il server originale, reindirizzare i client e le applicazioni client al nuovo server
- Assicurarsi che siano presenti regole di rete virtuale appropriate per consentire agli utenti di connettersi. Queste regole non vengono copiate dal server originale.
- Verificare che siano presenti gli account di accesso e le autorizzazioni a livello di database appropriati
- Configurare gli avvisi in base alle esigenze per il server di ripristino appena creato

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulla [continuità aziendale](concepts-business-continuity.md)

