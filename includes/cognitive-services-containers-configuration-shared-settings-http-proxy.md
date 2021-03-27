---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7f51b7fe95445cbd3a8aff530f89ce55b5abfb1e
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630281"
---
Se è necessario configurare un proxy HTTP per eseguire le richieste in uscita, usare questi due argomenti:

| Nome | Tipo di dati | Descrizione |
|--|--|--|
|HTTP_PROXY|string|Il proxy da usare, ad esempio, `http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|string|Tutte le credenziali necessarie per l'autenticazione nel proxy, ad esempio `username:password` . Questo valore **deve essere in lettere minuscole**. |
|`<proxy-user>`|string|L'utente per il proxy.|
|`<proxy-password>`|string|La password associata a `<proxy-user>` per il proxy.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
