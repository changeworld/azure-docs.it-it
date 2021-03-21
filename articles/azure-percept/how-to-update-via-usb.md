---
title: Aggiornare Azure Percept DK tramite una connessione USB
description: Informazioni su come aggiornare Azure Percept DK tramite una connessione USB
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 7f5e5e4da9fea671fc85a55fc8cc191fa14b720f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101663004"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>Come aggiornare Azure Percept DK tramite una connessione USB

Seguire questa guida per informazioni su come eseguire un aggiornamento USB per la bacheca del vettore di Azure Percept DK.

## <a name="prerequisites"></a>Prerequisiti
- Computer host con una porta USB-C o USB-A disponibile.
- Scheda di supporto di Azure Percept DK (Dev Kit) ed è stato fornito il cavo USB-c al cavo USB-C. Se il computer host dispone di una porta USB-A ma non di una porta USB-C, è possibile usare un cavo USB-C a USB-A (venduto separatamente).
- Installare [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) (accesso amministratore necessario).
- Installare lo strumento NXP UUU. [Scaricare la versione più recente del](https://github.com/NXPmicro/mfgtools/releases) file di uuu.exe (per Windows) o il file UUU (per Linux) nella scheda Asset.
- [Installare 7-zip](https://www.7-zip.org/). Questo software verrà usato per estrarre il file di immagine non elaborato dal relativo file compresso XZ. Scaricare e installare il file con estensione exe appropriato.

## <a name="steps"></a>Passaggi
1.  Scaricare i seguenti [tre file di aggiornamento USB](https://go.microsoft.com/fwlink/?linkid=2155734):
    - pe101-UEFI-***&lt; Version numero &gt;***. Raw. xz
    - emmc_full.txt
    - Fast-hab-FW. Raw
 
1. Estrarre in pe101-UEFI-***&lt; numero &gt; di versione**_. Raw dal file compresso pe101-UEFI_ * _&lt; numero &gt; di versione_* *. Raw. xz. Non si è certi di come estrarre? Scaricare e installare 7-zip, quindi fare clic con il pulsante destro del mouse sul file di immagine **. xz** e selezionare 7-Zip &gt; Extract qui.

1. Copiare i tre file seguenti nella cartella che contiene lo strumento UUU:
    - Estrarre il file pe101-UEFI-***&lt; Version numero &gt;***. Raw (dal passaggio 2).
    - emmc_full.txt (dal passaggio 1).
    - Fast-hab-FW. Raw (dal passaggio 1).
 
1. Power on the dev Kit.
1. [Connettersi a dev kit tramite SSH](./how-to-ssh-into-percept-dk.md)
1. Aprire un prompt dei comandi di Windows (avviare &gt; cmd) o un terminale Linux e passare alla cartella in cui sono archiviati i file di aggiornamento. Eseguire il comando seguente per avviare l'aggiornamento:
    - Windows: ```uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    - Linux: ```sudo ./uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    
Dopo aver eseguito questi comandi, è possibile che venga visualizzato un messaggio che informa che è in attesa del dispositivo... al prompt dei comandi. Questo comportamento è previsto ed è necessario procedere al passaggio successivo.
    
1. Connettere la bacheca del vettore Dev Kit al computer host tramite un cavo USB. Connettersi sempre dalla porta USB-C della lavagna del vettore alla porta USB-C o USB-A del computer host (USB-C a USB-un cavo venduto separatamente), a seconda delle porte disponibili. 
 
1. Nel terminale SSH/PuTTy immettere i comandi seguenti per impostare Dev Kit in modalità USB e quindi riavviare Dev Kit.
    - ```flagutil    -wBfRequestUsbFlash    -v1```
    - ```reboot -f```
 
1. È possibile che venga indicato che il computer host riconosce il dispositivo e il processo di aggiornamento verrà avviato automaticamente. Tornare al prompt dei comandi per visualizzare lo stato. Il processo può richiedere fino a 10 minuti e, quando l'aggiornamento ha esito positivo, verrà visualizzato un messaggio che indica "operazione riuscita 1 errore 0"
 
1. Al termine dell'aggiornamento, spegnere la scheda del vettore. Scollegare il cavo USB dal PC.  Collegare il modulo Azure Percept Vision alla lavagna del gestore usando il cavo USB.

1. Riaccendere la lavagna del vettore.

## <a name="next-steps"></a>Passaggi successivi

Il kit dev è stato aggiornato correttamente. È possibile continuare lo sviluppo e le operazioni con la devkit.