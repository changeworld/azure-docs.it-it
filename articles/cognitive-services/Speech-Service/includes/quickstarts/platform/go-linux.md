---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: f674edd15b86f49d60450a53f5df5852b32f95a4
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906437"
---
Questa guida spiega come installare [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per Linux

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Requisiti di sistema

Linux: vedere l'elenco di [distribuzioni Linux e architetture di destinazione supportate](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questo argomento di avvio rapido, sono necessari gli elementi seguenti:

* gcc
* [Go Binary (1.13 o versione successiva)](https://golang.org/dl/)

* Le piattaforme Linux supportate richiedono l'installazione di alcune librerie (`libssl` per il supporto di Secure Sockets Layer e `libasound2` per il supporto audio). Per i comandi necessari per installare le versioni corrette di queste librerie, identificare la propria distribuzione di seguito.

   * In Ubuntu/Debian:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

     Se libssl1.0.0 non è disponibile, installare libssl1.0.x (dove x è maggiore di 0) o libssl1.1.

   * In RHEL/CentOS:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - In RHEL/CentOS 7 seguire le istruzioni riportate in [Come configurare RHEL/CentOS 7 per Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - In RHEL/CentOS 8 seguire le istruzioni riportate in [Come configurare OpenSSL per Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]


## <a name="configure-go-environment"></a>Configurare l'ambiente Go

Eseguire la procedura seguente per configurare l'ambiente Go e trovare Speech SDK. In entrambi i passaggi sostituire `<architecture>` con l'architettura di processore della propria CPU. Può essere `x86`, `x64`, `arm32` o `arm64`.

1. Poiché i binding si basano su `cgo`, è necessario impostare le variabili di ambiente in modo che Go trovi l'SDK:

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib/<architecture> -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. Per eseguire le applicazioni, incluso l'SDK, è necessario indicare al sistema operativo dove trovare le librerie:

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<architecture>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [windows](../quickstart-list-go.md)]
