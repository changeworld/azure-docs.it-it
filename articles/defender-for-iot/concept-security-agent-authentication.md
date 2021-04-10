---
title: Autenticazione dell'agente di sicurezza (anteprima)
description: Eseguire l'autenticazione di micro Agent con due metodi possibili.
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: 8dd8abaedaaababf4d84330c5bf8cb030bac55bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779240"
---
# <a name="micro-agent-authentication-methods-preview"></a>Metodi di autenticazione di micro Agent (anteprima)

Sono disponibili due opzioni per l'autenticazione con Defender per l'agente Micro-Internet: 

- Stringa di connessione 

- Certificato 

## <a name="authentication-using-a-connection-string"></a>Autenticazione tramite una stringa di connessione 

Per utilizzare una stringa di connessione, è necessario aggiungere un file che utilizza la stringa di connessione codificata in UTF-8 nella directory dell'agente Defender in un file denominato `connection_string.txt` . Ad esempio,

```azurecli
echo “<connection string>” > connection_string.txt 
```

Una volta completata questa operazione, è necessario riavviare il servizio utilizzando questo comando.

```azurecli
sudo systemctl restart defender-iot-micro-agent.service
``` 

## <a name="authentication-using-a-certificate"></a>Autenticazione tramite un certificato 


Per eseguire l'autenticazione usando un certificato: 

1. Inserire la parte pubblica con codifica PEM di un certificato nella directory dell'agente Defender, in un file denominato `certificate_public.pem` .
1. Inserire la chiave privata con codifica PEM nella directory dell'agente Defender, in un file denominato `certificate_private.pem` .
1. Inserire la stringa di connessione appropriata in un file denominato `connection_string.txt` . Ad esempio,

    ```azurecli
    HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true 
    ```

    Questa azione indica che l'agente Defender prevede che venga fornito un certificato per l'autenticazione. 

1. riavviare il servizio utilizzando il codice seguente: 

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

## <a name="ensure-the-micro-agent-is-running-correctly"></a>Verificare che l'agente micro venga eseguito correttamente 

1. Eseguire il comando seguente: 
    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```
1. Verificare che il servizio sia stabile assicurandosi che sia **attivo** e che il tempo di attività del processo sia appropriato: 

    :::image type="content" source="media/concept-security-agent-authentication/active.png" alt-text="Verificare che il servizio sia stabile assicurandosi che sia attivo.":::

## <a name="next-steps"></a>Passaggi successivi

Verificare il comportamento di [sicurezza: benchmark CIS](concept-security-posture.md).
