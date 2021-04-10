---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a2a5935079a339e85713e9cbcd0f32c211cabbb5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729871"
---
Le impostazioni `Logging` gestiscono il supporto di registrazione di ASP.NET Core per il contenitore. È possibile usare le stesse impostazioni di configurazione e gli stessi valori per il contenitore che si usano per un'applicazione ASP.NET Core. 

I provider di registrazione seguenti sono supportati dal contenitore:

|Provider|Scopo|
|--|--|
|[Console](/aspnet/core/fundamentals/logging/#console-provider)|Provider di registrazione `Console` di ASP.NET Core. Tutti i valori predefiniti e le impostazioni di configurazione di ASP.NET Core per questo provider di registrazione sono supportati.|
|[Eseguire il debug](/aspnet/core/fundamentals/logging/#debug-provider)|Provider di registrazione `Debug` di ASP.NET Core. Tutti i valori predefiniti e le impostazioni di configurazione di ASP.NET Core per questo provider di registrazione sono supportati.|
|[Disco](#disk-logging)|Provider di registrazione JSON. Questo provider di registrazione scrive i dati di log nel montaggio di output.|

Questo comando del contenitore archivia informazioni di registrazione nel formato JSON al montaggio di output:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

Questo comando del contenitore visualizza informazioni di debug, con il prefisso `dbug`, durante l'esecuzione del contenitore:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Registrazione su disco

Il provider di registrazione `Disk` supporta le impostazioni di configurazione seguenti:

| Nome | Tipo di dati | Descrizione |
|------|-----------|-------------|
| `Format` | string | Formato di output dei file di log.<br/> **Nota:** per abilitare il provider di registrazione, questo valore deve essere impostato su `json`. Se questo valore viene specificato senza specificare anche un montaggio di output durante la creazione di un'istanza di un contenitore, si verifica un errore. |
| `MaxFileSize` | Integer | Dimensione massima, espressa in megabyte (MB), di un file di log. Quando la dimensione del file di log corrente corrisponde a questo valore o lo supera, il provider di registrazione avvia un nuovo file di log. Se viene specificato -1, la dimensione del file di log è limitata solo dalla dimensione massima del file del montaggio di output eventualmente presente. Il valore predefinito è 1. |

Per altre informazioni sulla configurazione del supporto di registrazione di ASP.NET Core, vedere [Registrazione in ASP.NET Core](/aspnet/core/fundamentals/logging/).