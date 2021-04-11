---
title: Risoluzione dei problemi relativi all'agente Micro-Internet Defender (anteprima)
description: Informazioni su come gestire gli errori imprevisti o non spiegati.
ms.date: 4/5/2021
ms.topic: reference
ms.openlocfilehash: 3d52c4093c01d7e449c68b1c8143249b51f7061a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011420"
---
# <a name="defender-iot-micro-agent-troubleshooting-preview"></a>Risoluzione dei problemi relativi all'agente Micro-Internet Defender (anteprima)

Se si verifica un errore imprevisto, è possibile usare questi metodi di risoluzione dei problemi in un tentativo di risolvere il problema. È anche possibile contattare il team del prodotto Azure Defender per l'assistenza in base alle esigenze.   

## <a name="service-status"></a>Stato del servizio 

Per visualizzare lo stato del servizio: 

1. Eseguire il comando seguente

    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```

1. Verificare che il servizio sia stabile assicurandosi che sia `active` e che il tempo di esecuzione del processo sia appropriato.

    :::image type="content" source="media/troubleshooting/active-running.png" alt-text="Verificare che il servizio sia stabile controllando che sia attivo e che il tempo di attività sia appropriato.":::

Se il servizio è elencato come `inactive` , utilizzare il comando seguente per avviare il servizio:

```azurecli
systemctl start defender-iot-micro-agent.service 
```

Si saprà che il servizio si arresta in modo anomalo se, il tempo di esecuzione del processo è inferiore a 2 minuti. Per risolvere questo problema, è necessario [esaminare i log](#review-the-logs).

## <a name="validate-micro-agent-root-privileges"></a>Convalida privilegi radice agente micro

Usare il comando seguente per verificare che il servizio Defender Internet per gli agenti sia in esecuzione con privilegi radice.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Verificare che il servizio Defender per l'agente del Micro Internet sia in esecuzione con privilegi radice.":::
## <a name="review-the-logs"></a>Esaminare i log 

Per esaminare i log, usare il comando seguente:  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

### <a name="quick-log-review"></a>Revisione log veloce

Se si verifica un problema durante l'esecuzione di micro Agent, è possibile eseguire l'agente micro in uno stato temporaneo, che consente di visualizzare i log usando il comando seguente:

```azurecli
sudo systectl stop defender-iot-micro-agent
cd /var/defender_iot_micro_agent/
sudo ./defender_iot_micro_agent
```

## <a name="restart-the-service"></a>Riavviare il servizio.

Per riavviare il servizio, usare il comando seguente: 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>Passaggi successivi

Verificare il [supporto e il ritiro delle funzionalità](edge-security-module-deprecation.md).
