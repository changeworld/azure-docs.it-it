---
title: Usare redis-cli con cache di Azure per Redis
description: Informazioni su come usare *redis-cli.exe* come strumento da riga di comando per interagire con un cache di Azure per Redis come client
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 81327bd5fc76d14d60d26bd912da8de054e5308d
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833960"
---
# <a name="use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Usare lo strumento da riga di comando Redis con cache di Azure per Redis

*redis-cli.exe* è uno strumento da riga di comando comune per l'interazione con un'istanza di Cache Redis di Azure come client. Questo strumento è disponibile anche per l'uso con Cache Redis di Azure.

Lo strumento è disponibile per le piattaforme Windows scaricando gli [strumenti da riga di comando Redis per Windows](https://github.com/MSOpenTech/redis/releases/). 

Se si vuole eseguire lo strumento da riga di comando in un'altra piattaforma, scaricare cache di Azure per Redis da [https://redis.io/download](https://redis.io/download) .

## <a name="gather-cache-access-information"></a>Raccogliere le informazioni di accesso alla cache

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

È possibile raccogliere le informazioni necessarie per accedere alla cache nelle tre modalità seguenti:

1. Tramite l'interfaccia della riga di comando di Azure usando [az redis list-keys](/cli/azure/redis#az_redis_list_keys)
2. Tramite Azure PowerShell usando [Get-AzRedisCacheKey](/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Tramite il portale di Azure.

In questa sezione si recupereranno le chiavi dal portale di Azure.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Abilitare l'accesso per redis-cli.exe.

Con cache di Azure per Redis, solo la porta TLS (6380) è abilitata per impostazione predefinita. Lo `redis-cli.exe` strumento da riga di comando non supporta TLS. Per usarlo sono disponibili due opzioni di configurazione:

1. [Abilitare la porta non TLS (6379)](cache-configure.md#access-ports)  -  **Questa configurazione non è consigliata** perché in questa configurazione le chiavi di accesso vengono inviate tramite TCP in testo non crittografato. Questa modifica può compromettere l'accesso alla cache. L'unico scenario in cui è possibile tenere in considerazione questa configurazione è quando si accede a una cache di test.

2. Scaricare e installare [stunnel](https://www.stunnel.org/downloads.html).

    Eseguire **stunnel GUI Start** (Avvio GUI stunnel) per avviare il server.

    Fare clic con il pulsante destro del mouse sull'icona della barra delle applicazioni per il server stunnel e fare clic su **Show Log Window** (Mostra finestra log).

    Nel menu stunnel Log Window (Finestra log stunnel) fare clic **su Configuration** Edit  >  **Configuration (Modifica** configurazione) per aprire il file di configurazione corrente.

    Aggiungere la voce seguente per *redis-cli.exe* nella sezione **Service definitions** (Definizioni di servizio). Inserire il nome effettivo della cache invece di `yourcachename`. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Salvare e chiudere il file di configurazione. 
  
    Nel menu stunnel Log Window (Finestra log dello stunnel) fare clic **su Configuration**  >  **Reload Configuration (Configurazione ricarica configurazione).**


## <a name="connect-using-the-redis-command-line-tool"></a>Connettersi usando lo strumento da riga di comando Redis.

Quando si usa stunnel, eseguire *redis-cli.exe* e passare solo la *porta* e la *chiave di accesso* (primaria o secondaria) per connettersi alla cache.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![Screenshot che mostra che la connessione alla cache ha esito positivo.](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Se si usa una cache di test con la porta **non** TLS non sicura, eseguire e passare il nome host, la porta e la chiave di accesso (primaria o secondaria) per connettersi alla `redis-cli.exe` cache di test.   

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel con redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'uso della [Console Redis](cache-configure.md#redis-console) per inviare i comandi.
