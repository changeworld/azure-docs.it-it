---
title: Installare Defender per gli agenti Internet (anteprima)
titleSuffix: Azure Defender for IoT
description: Informazioni su come installare e autenticare Defender micro Agent.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 3/3/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 4125fc3c2f03104eeca108a73d83f2fad44bebc1
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051753"
---
# <a name="install-defender-for-iot-micro-agent-preview"></a>Installare Defender per gli agenti Internet (anteprima)

Questo articolo fornisce una spiegazione su come installare e autenticare Defender micro Agent.

## <a name="prerequisites"></a>Prerequisiti

Prima di installare il modulo Defender for Internet è necessario creare un'identità del modulo nell'hub Internet delle cose. Per altre informazioni su come creare un'identità del modulo, vedere [creare un modulo gemello di Defender Internet Agent (anteprima)](quickstart-create-micro-agent-module-twin.md).

## <a name="install-the-package"></a>Installare il pacchetto

Installare e configurare il repository dei pacchetti Microsoft seguendo [queste istruzioni](/windows-server/administration/linux-package-repository-for-microsoft-software). 

Per Debian 9, le istruzioni non includono il repository che deve essere aggiunto, usare i comandi seguenti per aggiungere il repository: 

```azurecli
curl -sSL https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add - 

sudo apt-get install software-properties-common

sudo apt-add-repository https://packages.microsoft.com/debian/9/multiarch/prod

sudo apt-get update
```

Per installare il pacchetto Defender micro Agent in Debian e le distribuzioni Linux basate su Ubuntu, usare il comando seguente:

```azurecli
sudo apt-get install defender-iot-micro-agent 
```

## <a name="micro-agent-authentication-methods"></a>Metodi di autenticazione di micro Agent 

Le due opzioni usate per autenticare il Defender per l'agente Internet delle cose sono: 

- Stringa di connessione. 

- Certificato.

### <a name="authenticate-using-a-connection-string"></a>Eseguire l'autenticazione con una stringa di connessione

Per eseguire l'autenticazione usando una stringa di connessione:

1. Inserire un file denominato `connection_string.txt` contenente la stringa di connessione codificata in UTF-8 nel percorso della directory dell'agente Defender `/var/defender_iot_micro_agent` immettendo il comando seguente:

    ```azurecli
    sudo bash -c 'echo "<connection string" > /var/defender_iot_micro_agent/connection_string.txt' 
    ```

    Il `connection_string.txt` dovrebbe ora trovarsi nel percorso seguente `/var/defender_iot_micro_agent/connection_string.txt` .

1. Riavviare il servizio utilizzando questo comando:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

### <a name="authenticate-using-a-certificate"></a>Eseguire l'autenticazione con un certificato

Per eseguire l'autenticazione usando un certificato:

1. Procurarsi un certificato seguendo [queste istruzioni](../iot-hub/iot-hub-security-x509-get-started.md).

1. Inserire la parte pubblica con codifica PEM del certificato e la chiave privata, nella directory Defender Agent nel file denominato `certificate_public.pem` , e `certificate_private.pem` . 

1. Inserire la stringa di connessione appropriata nel `connection_string.txt` file. la stringa di connessione dovrebbe essere simile alla seguente: 

    `HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true` 

    Questa stringa avvisa l'agente Defender, in modo da prevedere che venga fornito un certificato per l'autenticazione. 

1. Riavviare il servizio usando il comando seguente:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service
    ```

### <a name="validate-your-installation"></a>Convalidare l'installazione

Per convalidare l'installazione:

1. Assicurarsi che l'agente micro venga eseguito correttamente con il comando seguente:  

    ```azurecli
    systemctl status defender-iot-micro-agent.service
    ```
1. Verificare che il servizio sia stabile assicurandosi che `active` il tempo di esecuzione del processo sia appropriato.

    :::image type="content" source="media/quickstart-standalone-agent-binary-installation/active-running.png" alt-text="Verificare che il servizio sia stabile e attivo.":::
 
## <a name="testing-the-system-end-to-end"></a>Test del sistema end-to-end 

È possibile testare il sistema da end-to-end creando un file di trigger nel dispositivo. Il file trigger provocherà l'analisi della linea di base nell'agente per rilevare il file come una violazione di base. 

Creare un file nella file system con il comando seguente:

```azurecli
sudo touch /tmp/DefenderForIoTOSBaselineTrigger.txt 
```
Si verificherà un errore di convalida della linea di base nell'hub con una `CceId` di CIS-Debian-9-DEFENDER_FOR_IOT_TEST_CHECKS-0,0: 

:::image type="content" source="media/quickstart-standalone-agent-binary-installation/validation-failure.png" alt-text="Indicazione dell'errore di convalida di base che si verifica nell'hub." lightbox="media/quickstart-standalone-agent-binary-installation/validation-failure-expanded.png":::

Attendere fino a un'ora che la raccomandazione venga visualizzata nell'hub. 

## <a name="micro-agent-versioning"></a>Controllo delle versioni di micro Agent 

Per installare una versione specifica di Defender Internets micro Agent, eseguire il comando seguente: 

```azurecli
sudo apt-get install defender-iot-micro-agent=<version>
```

## <a name="next-steps"></a>Passaggi successivi

[Creazione di Defender micro Agent dal codice sorgente](quickstart-building-the-defender-micro-agent-from-source.md)