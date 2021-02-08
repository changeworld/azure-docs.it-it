---
title: Risoluzione dei problemi relativi all'agente Micro-Internet Defender (anteprima)
titleSuffix: Azure Defender for IoT
description: Informazioni su come gestire gli errori imprevisti o non spiegati.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/24/2021
ms.topic: reference
ms.service: azure
ms.openlocfilehash: 07198a5d0ef5d0a6c9eed97523c61826e451b7f5
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809811"
---
# <a name="defender-iot-micro-agent-troubleshooting-preview"></a>Risoluzione dei problemi relativi all'agente Micro-Internet Defender (anteprima)

In caso di errori imprevisti o non spiegati, utilizzare i seguenti metodi di risoluzione dei problemi per tentare di risolvere i problemi. È anche possibile contattare il team del prodotto Azure Defender per l'assistenza in base alle esigenze.   

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

Si saprà che il servizio si arresta in modo anomalo se il tempo di esecuzione del processo è troppo breve. Per risolvere questo problema, è necessario esaminare i log.

## <a name="review-logs"></a>Esaminare i log 

Usare il comando seguente per verificare che il servizio Defender Internet per gli agenti sia in esecuzione con privilegi radice.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Verificare che il servizio Defender per l'agente del Micro Internet sia in esecuzione con privilegi radice.":::

Per visualizzare i log, usare il comando seguente:  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

Per riavviare il servizio, usare il comando seguente: 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>Passaggi successivi

Verificare il [supporto e il ritiro delle funzionalità](edge-security-module-deprecation.md).
