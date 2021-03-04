---
title: Risolvere i problemi relativi ai moduli audio e vocale di Azure Percept
description: Suggerimenti per la risoluzione dei problemi relativi ad alcuni dei problemi più comuni riscontrati durante l'esperienza di onboarding
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: a3877ea680e7b4c705f127c54e0fa10c45d3b51d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097976"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Risoluzione dei problemi del modulo audio e vocale di Azure Percept

Usare le linee guida seguenti per risolvere i problemi dell'applicazione Voice Assistant.

## <a name="collecting-speech-module-logs"></a>Raccolta dei log del modulo vocale

Per eseguire questi comandi, [connettersi al punto di accesso Wi-Fi di Azure PERCEPT dk e connettersi a dev kit tramite SSH](./how-to-ssh-into-percept-dk.md) e immettere i comandi nel terminale SSH.

```console
 iotedge logs azureearspeechclientmodule
```

Per reindirizzare l'output a un file con estensione txt per un'ulteriore analisi, usare la sintassi seguente:

```console
[command] > [file name].txt
```

Dopo il reindirizzamento dell'output a un file con estensione txt, copiare il file nel PC host tramite SCP:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[percorso file host locale] indica il percorso nel computer host in cui si desidera copiare il file con estensione txt. [nomeutente remoto] è il nome utente SSH scelto durante l' [esperienza di onboarding](./quickstart-percept-dk-set-up.md). Se non è stato configurato un accesso SSH durante l'esperienza di onboarding di Azure Percept DK, il nome utente remoto è root.

## <a name="checking-runtime-status-of-the-speech-module"></a>Controllo dello stato di runtime del modulo vocale

Verificare che lo stato di runtime di **azureearspeechclientmodule** sia indicato come **in esecuzione**. Per individuare lo stato di runtime dei moduli del dispositivo, aprire il [portale di Azure](https://portal.azure.com/) e passare a **tutte le risorse**  ->  **\<your IoT hub>**  ->  **IOT Edge**  ->  **\<your device ID>** . Fare clic sulla scheda **moduli** per visualizzare lo stato di runtime di tutti i moduli installati.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Pagina dispositivo perimetrale nell'portale di Azure.":::

Se lo stato di runtime di **azureearspeechclientmodule** non è elencato come **in esecuzione**, fare clic su **imposta moduli**  ->  **azureearspeechclientmodule**. Nella pagina **Impostazioni modulo** impostare **stato desiderato** su **in esecuzione** e fare clic su **Aggiorna**.

## <a name="understanding-ear-som-led-indicators"></a>Informazioni sugli indicatori LED per ear SoM

È possibile usare gli indicatori LED per comprendere lo stato in cui si trova il dispositivo. L'inizializzazione completa del modulo dopo l' *accensione* richiede in genere circa 2 minuti. Quando si esegue la procedura di inizializzazione, verrà visualizzato quanto segue:

1. LED bianco al centro: il dispositivo è acceso. 
2. 1 centro bianco lampeggiante-l'autenticazione è in corso. 
3. Quando il dispositivo viene autenticato e pronto per l'uso, tutti e tre i LED cambieranno in blu.

|LED|   Stato del LED|  Stato SoM Ear|
|---|------------|----------------| 
|L02|   1x bianco, statico on |Accendere |
|L02|   1x bianco, 0,5 Hz lampeggianti|  Autenticazione in corso |
|L01 & L02 & L03|   3x blu, statico on|     In attesa di una parola chiave|
|L01 & L02 & L03|   Matrice LED lampeggiante, 20fps | In ascolto o in conversazione|
|L01 & L02 & L03|   LED Array Racing, 20fps|    Pensare|
|L01 & L02 & L03|   3x rosso, statico | Disattiva audio|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi di Azure Percept DK, vedere la [Guida generale alla risoluzione dei problemi](./troubleshoot-dev-kit.md) .
