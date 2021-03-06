---
title: includere file
description: includere file
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: df71f0804b62eb4b17ff8d2f652b076b5c64c959
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "92743806"
---
È possibile accedere ai log della console generati dall'interno del contenitore.

Prima di tutto attivare la registrazione del contenitore eseguendo questo comando:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Sostituire `<app-name>` e `<resource-group-name>` con i valori appropriati per l'app Web.

Dopo che la registrazione del contenitore è attivata, eseguire il comando seguente per visualizzare il flusso di registrazione:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Se i log di console non sono immediatamente visibili, controllare nuovamente dopo 30 secondi.

Per interrompere lo streaming dei log in qualsiasi momento, premere **CTRL**+**C**.

È anche possibile ispezionare i file di log in un browser all'indirizzo `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.
