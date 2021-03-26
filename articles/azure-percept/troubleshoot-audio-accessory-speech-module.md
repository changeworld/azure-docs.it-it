---
title: Risolvere i problemi relativi all'audio di Azure Percept e al modulo di riconoscimento vocale
description: Ottenere suggerimenti per la risoluzione dei problemi relativi ad audio e azureearspeechclientmodule di Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c4fc7d7564ecd30326fbec832639b2a81d55e6d5
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605655"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Risoluzione dei problemi del modulo audio e vocale di Azure Percept

Usare le linee guida seguenti per risolvere i problemi dell'applicazione Voice Assistant.

## <a name="collecting-speech-module-logs"></a>Raccolta dei log del modulo vocale

Per eseguire questi comandi, connettersi tramite [SSH a Dev Kit](./how-to-ssh-into-percept-dk.md) e immettere i comandi nel prompt client SSH.

Raccogli log del modulo vocale:

```console
sudo iotedge logs azureearspeechclientmodule
```

Per reindirizzare l'output a un file con estensione txt per un'ulteriore analisi, usare la sintassi seguente:

```console
sudo [command] > [file name].txt
```

Modificare le autorizzazioni del file. txt in modo che sia possibile copiarlo:

```console
sudo chmod 666 [file name].txt
```

Dopo il reindirizzamento dell'output a un file con estensione txt, copiare il file nel PC host tramite SCP:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[percorso file host locale] indica il percorso nel computer host in cui si desidera copiare il file con estensione txt. [nomeutente remoto] è il nome utente SSH scelto durante l' [installazione](./quickstart-percept-dk-set-up.md).

## <a name="checking-runtime-status-of-the-speech-module"></a>Controllo dello stato di runtime del modulo vocale

Verificare che lo stato di runtime di **azureearspeechclientmodule** sia indicato come **in esecuzione**. Per individuare lo stato di runtime dei moduli del dispositivo, aprire il [portale di Azure](https://portal.azure.com/) e passare a **tutte le risorse**  ->  **[your hub Internet]**  ->  **IOT Edge**  ->  **[ID dispositivo]**. Fare clic sulla scheda **moduli** per visualizzare lo stato di runtime di tutti i moduli installati.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Pagina dispositivo perimetrale nell'portale di Azure.":::

Se lo stato di runtime di **azureearspeechclientmodule** non è elencato come **in esecuzione**, fare clic su **imposta moduli**  ->  **azureearspeechclientmodule**. Nella pagina **Impostazioni modulo** impostare **stato desiderato** su **in esecuzione** e fare clic su **Aggiorna**.

## <a name="understanding-ear-som-led-indicators"></a>Informazioni sugli indicatori LED per ear SoM

È possibile usare gli indicatori LED per comprendere lo stato in cui si trova il dispositivo. L'inizializzazione completa del modulo dopo l'accensione del dispositivo richiede in genere circa 2 minuti. Quando si esegue la procedura di inizializzazione, verrà visualizzato quanto segue:

1. LED del centro bianco acceso (statico): il dispositivo è acceso.
2. LED del centro bianco acceso (lampeggiante): l'autenticazione è in corso.
3. Quando il dispositivo viene autenticato e pronto per l'uso, tutti e tre i LED cambieranno in blu.

|LED|Stato del LED|Stato SoM Ear|
|---|---------|--------------|
|L02|1x bianco, statico on|Accendere |
|L02|1x bianco, 0,5 Hz lampeggianti|Autenticazione in corso |
|L01 & L02 & L03|3x blu, statico on|In attesa di una parola chiave|
|L01 & L02 & L03|Matrice LED lampeggiante, 20fps |In ascolto o in conversazione|
|L01 & L02 & L03|LED Array Racing, 20fps|Pensare|
|L01 & L02 & L03|3x rosso, statico |Disattiva audio|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi di Azure Percept DK, vedere la [Guida generale alla risoluzione dei problemi](./troubleshoot-dev-kit.md) .
