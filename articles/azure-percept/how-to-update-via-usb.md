---
title: Aggiornare Azure Percept DK tramite una connessione USB
description: Informazioni su come aggiornare Azure Percept DK tramite una connessione USB
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 12f6acda632b9c0fbee2db570df5293c1daf32ea
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720815"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>Come aggiornare Azure Percept DK tramite una connessione USB

Sebbene l'uso di aggiornamenti in modalità non in linea (OTA) sia il metodo migliore per mantenere aggiornato il sistema operativo e il firmware del kit di sviluppo, esistono scenari in cui è necessario aggiornare (o "lampeggiare") il kit di sviluppo su una connessione USB:

- Non è possibile eseguire un aggiornamento OTA a causa di connettività o altri problemi tecnici
- Il dispositivo deve essere ripristinato allo stato di fabbrica

Questa guida illustra come aggiornare correttamente il sistema operativo e il firmware del kit di sviluppo tramite una connessione USB.

> [!WARNING]
> L'aggiornamento di Dev Kit tramite USB eliminerà tutti i dati esistenti nel dispositivo, inclusi i modelli e i contenitori di intelligenza artificiale.
>
> Seguire tutte le istruzioni nell'ordine. I passaggi ignorati possono mettere il kit di sviluppo in uno stato inutilizzabile.

## <a name="prerequisites"></a>Prerequisiti

- Azure Percept DK
- Un computer host basato su Windows, Linux o OS X con funzionalità Wi-Fi e una porta USB-C o USB-A disponibile
- Un cavo USB-C a USB-A (facoltativo, venduto separatamente)
- Accesso SSH, creato durante l' [esperienza di installazione di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md)

## <a name="download-software-tools-and-update-files"></a>Scarica gli strumenti software e i file di aggiornamento

1. [Strumento NXP UUU](https://github.com/NXPmicro/mfgtools/releases). Scaricare la **versione più recente** del file di uuu.exe (per Windows) o il file UUU (per Linux) nella scheda **Asset** .

1. [7-zip](https://www.7-zip.org/). Questo software verrà usato per estrarre il file di immagine non elaborato dal relativo file compresso XZ. Scaricare e installare il file con estensione exe appropriato.

1. [Scaricare i file di aggiornamento](https://go.microsoft.com/fwlink/?linkid=2155734).

1. Verificare che siano presenti tutti e tre gli artefatti di compilazione:
    - Azure-Percept-DK-*&lt; numero &gt; di versione*. Raw. xz
    - Fast-hab-FW. Raw
    - emmc_full.txt

## <a name="set-up-your-environment"></a>Configura il tuo ambiente

1. Creare una cartella o una directory nel computer host in una posizione facilmente accessibile tramite la riga di comando.

1. Copiare lo strumento UUU (**uuu.exe** o **UUU**) nella nuova cartella.

1. Estrarre il file **Azure-Percept-dk-*&lt; version &gt; Number*. Raw** dal file compresso facendo clic con il pulsante destro del mouse su **Azure-Percept-dk-*&lt; Version Number &gt;*. Raw. xz** e selezionando **7-zip** &gt; **Extract qui**.

1. Spostare il file estratto di **Azure-Percept-dk-*&lt; &gt; Version*. Raw** , **Fast-hab-FW. Raw**, e **emmc_full.txt** nella cartella che contiene lo strumento UUU.

## <a name="update-your-device"></a>Aggiornare il dispositivo

1. Connettersi tramite [SSH a Dev Kit](./how-to-ssh-into-percept-dk.md).

1. Aprire quindi un prompt dei comandi di Windows (**Avvia**  >  **cmd**) o un terminale Linux e passare alla cartella in cui sono archiviati i file di aggiornamento e lo strumento UUU. Immettere il comando seguente nel prompt dei comandi o nel terminale per preparare il computer per la ricezione di un dispositivo con Flashing:

    - Windows:

        ```bash
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - Linux:

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. Disconnettere il dispositivo Azure Percept Vision dalla porta USB-C della lavagna del vettore.

1. Connettere il cavo USB-C fornito alla porta USB-C della lavagna del vettore e alla porta USB-C del computer host. Se il computer dispone solo di una porta USB-A, connettere un cavo USB-C al dispositivo USB-A (venduto separatamente) alla lavagna del vettore e al computer host.

1. Nel prompt del client SSH immettere i comandi seguenti:

    1. Impostare la modalità di aggiornamento USB del dispositivo:

        ```bash
        sudo flagutil    -wBfRequestUsbFlash    -v1
        ```

    1. Riavviare il dispositivo. L'installazione dell'aggiornamento inizierà.

        ```bash
        sudo reboot -f
        ```

1. Tornare al prompt dei comandi o al terminale. Al termine dell'aggiornamento, verrà visualizzato un messaggio con ```Success 1    Failure 0``` :

    > [!NOTE]
    > Dopo l'aggiornamento, il dispositivo verrà reimpostato sulle impostazioni predefinite e si perderanno la connessione Wi-Fi e l'accesso SSH.

1. Al termine dell'aggiornamento, spegnere la scheda del vettore. Scollegare il cavo USB dal PC.  

## <a name="next-steps"></a>Passaggi successivi

Usare l' [esperienza di installazione di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md) per riconfigurare il dispositivo.