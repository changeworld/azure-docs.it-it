---
title: Panoramica dei tipi di dispositivi Azure Internet
description: Informazioni sui diversi tipi di dispositivi supportati da Azure e dagli strumenti disponibili.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: aa99594fe3de98635e37d15beebf015f15dc4f64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654373"
---
# <a name="overview-of-azure-iot-device-types"></a>Panoramica dei tipi di dispositivi Azure Internet
I dispositivi Internet sono disponibili in un'ampia gamma di piattaforme hardware. Sono disponibili piccoli MCU a 8 bit fino alle ultime CPU x86 disponibili in un computer desktop. Molte variabili determinano il modo in cui l'hardware scelto per un dispositivo Internet delle cose e questo articolo illustrano alcune delle differenze principali.

## <a name="key-hardware-differentiators"></a>Differenziatori hardware principali
Alcuni fattori importanti quando si sceglie l'hardware sono costo, consumo energetico, rete, input e output disponibili.

* **Costo:** I dispositivi più piccoli più economici vengono in genere usati quando la massa produce il prodotto finale. Tuttavia, il compromesso è che lo sviluppo del dispositivo può essere più costoso in base al dispositivo altamente vincolato. Il costo di sviluppo può essere distribuito in tutti i dispositivi prodotti, quindi il costo di sviluppo per unità sarà basso.

* **Potenza:** La quantità di energia utilizzata da un dispositivo è importante se il dispositivo utilizzerà batterie e non sarà connesso alla rete elettrica. MCU sono spesso progettati per scenari di risparmio energia inferiori e possono essere una scelta migliore per estendere la durata della batteria.

* **Accesso alla rete:** Esistono diversi modi per connettere un dispositivo a un servizio cloud. Ethernet, Wi-Fi e cellulare e alcune delle opzioni disponibili. Il tipo di connessione scelto dipende dalla posizione in cui viene distribuito il dispositivo e dal modo in cui viene usato. Ad esempio, Cellular può essere un'opzione interessante in base alla copertura elevata, tuttavia, per i dispositivi con traffico elevato, può essere costosa. Ethernet cablata consente di ridurre i costi di dati, ma con un aspetto meno portabile.

* **Input e output:** Gli input e gli output disponibili sul dispositivo influiscono direttamente sulle funzionalità di gestione dei dispositivi. Un microcontroller include in genere numerose funzioni di I/O incorporate direttamente nel chip e offre un'ampia gamma di sensori per connettersi direttamente.

## <a name="microcontrollers-vs-microprocessors"></a>Microprocessori vs microprocessori
I dispositivi Internet delle cose possono essere suddivisi in due ampie categorie, microcontroller (MCU) e microprocessori (MPUs).

**MCU** sono meno costosi e più semplici da usare rispetto a MPUs. Una MCU conterrà molte delle funzioni, ad esempio memoria, interfacce e I/O all'interno del chip stesso. Un MPU trarrà questa funzionalità dai componenti nei chip di supporto. Una MCU usa spesso un sistema operativo in tempo reale (RTO) o esegue bare metal (nessun sistema operativo) e fornisce risposte in tempo reale e reazioni altamente deterministiche agli eventi esterni.

**MPUs** eseguirà in genere un sistema operativo generico, ad esempio Windows, Linux o MacOSX, che fornisce una risposta in tempo reale non deterministica. Non esiste in genere alcuna garanzia a quando un'attività verrà completata. 

:::image type="content" border="false" source="media/concepts-iot-device-types/mcu-vs-mpu.png" alt-text="MCU vs MPU":::

Di seguito è riportata una tabella che illustra alcune delle differenze di definizione tra una MCU e un sistema basato su MPU:

||Microcontroller (MCU)|Microprocessore (MPU)|
|-|-|-|
|**CPU**| Minore | Più informazioni |
|**RAM**| Minore | Più informazioni |
|**Flash**| Minore | Più informazioni |
|**Sistema operativo**| No o RTO | Utilizzo generico |
|**Difficoltà di sviluppo**| Più difficile |  Facile |
|**Consumo di energia elettrica**| Minore | Maggiore |
|**Costi**| Minore | Maggiore |
|**Deterministico**| Sì | No-con eccezioni|
|**Dimensioni del dispositivo**| Più piccoli | Maggiore |
