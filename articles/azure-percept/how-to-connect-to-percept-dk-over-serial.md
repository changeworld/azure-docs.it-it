---
title: Connettersi ad Azure Percept DK tramite seriale
description: Informazioni su come configurare una connessione seriale ad Azure Percept DK con PuTTy e un cavo seriale da USB a TTL
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 93b8ab0ce53202402e86b059abe3c600590d549e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662923"
---
# <a name="connect-to-your-azure-percept-dk-over-serial"></a>Connettersi ad Azure Percept DK tramite seriale

Attenersi alla procedura seguente per configurare una connessione seriale ad Azure Percept DK tramite [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

> [!WARNING]
> **Non** tentare di connettere la DevKit tramite seriale, tranne nei casi di errore estremo (ad esempio, il dispositivo è stato Brick). Separare l'enclosure della lavagna del vettore per collegare il cavo seriale è molto difficile e interromperà i cavi dell'antenna Wi-Fi.

## <a name="prerequisites"></a>Prerequisiti

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- COMPUTER host
- Azure Percept DK
- [Cavo seriale da USB a TTL](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="Cavo seriale da USB a TTL.":::

## <a name="initiate-the-serial-connection"></a>Avviare la connessione seriale

1. Se la lavagna del vettore è connessa a una guida 80/20, rimuoverla dalla barra con la chiave esadecimale (inclusa nella scheda di benvenuto DevKit).

1. Rimuovere le viti sulla parte inferiore dell'enclosure della lavagna del vettore ed estrarre la scheda madre.

    > [!WARNING]
    > Rimuovendo la scheda madre si interrompe il Wi-Fi cavi dell'antenna. Non **procedere con** la connessione seriale, a meno che non si tratti dell'ultima risorsa per ripristinare il dispositivo.

1. Rimuovere il dissipatore.

1. Rimuovere il ponticello dalla scheda GPIO pin.

    > [!TIP]
    > Prendere nota dell'orientamento della lavagna dei jumper prima di rimuoverla. Ad esempio, creare una freccia su o allineare un adesivo alla scheda del ponticello che punta verso il circuito di riferimento. Il jumper Board non è con chiave e può essere accidentalmente connesso all'indietro quando si riassembla la lavagna del vettore.

1. Connettere il [cavo seriale da USB a TTL](https://www.adafruit.com/product/954) ai pin GPIO sulla scheda madre, come illustrato di seguito. Si noti che la rete rossa non è connessa.

    - Connettere il cavo nero (GND) al pin 6.
    - Connettere il cavo bianco (RX) al pin 8.
    - Connettere il cavo verde (TX) al pin 10.

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/serial-connection-carrier-board.png" alt-text="Connessioni pin seriale della lavagna del vettore.":::

1. Accendere il DevKit e connettere il lato USB del cavo seriale al PC.

1. In Windows passare a **Start**  ->  **Windows Update settings**  ->  **Visualizza aggiornamenti facoltativi**  ->  **driver aggiornamenti**. Cercare un aggiornamento da seriale a USB nell'elenco, selezionare la casella accanto, quindi fare clic su **Scarica e installa**.  

1. Aprire quindi Windows gestione dispositivi (**Avvia**  ->  **Gestione dispositivi**). Passare a **porte** e fare clic su **USB per UART** per aprire **proprietà**. Prendere nota della porta COM a cui è connesso il dispositivo.

1. Fare clic sulla scheda **Impostazioni porta** . Assicurarsi che **bits al secondo** sia impostato su 115200.

1. Aprire PuTTY. Immettere quanto segue e fare clic su **Apri** per connettersi alla DevKit tramite seriale:

    1. Linea seriale: COM [porta #]
    1. Velocità: 115200
    1. Tipo di connessione: seriale

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="Finestra di sessione PuTTy con parametri seriali selezionati.":::

## <a name="next-steps"></a>Passaggi successivi

Per aggiornare un dispositivo non avviabile tramite seriale con il [cavo seriale da USB a TTL](https://www.adafruit.com/product/954), vedere la Guida all'aggiornamento USB per le situazioni non standard.

[comment]: # (Aggiungere un collegamento alla guida all'aggiornamento USB se disponibile.)