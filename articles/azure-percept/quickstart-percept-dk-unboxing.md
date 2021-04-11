---
title: Eseguire l'unboxing e assemblare i componenti di Azure Percept DK
description: Informazioni su come eseguire l'unboxing, connettersi e accendere Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/16/2021
ms.custom: template-quickstart
ms.openlocfilehash: efa255ba38f7e00785335bf458ecc0ed91da646b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608181"
---
# <a name="quickstart-unbox-and-assemble-your-azure-percept-dk-components"></a>Guida introduttiva: eseguire l'unboxing e assemblare i componenti di Azure Percept DK

Dopo aver ricevuto Azure Percept DK, fare riferimento a questa guida per informazioni su come connettere i componenti e accendere il dispositivo.

## <a name="prerequisites"></a>Prerequisiti

- Azure Percept DK (DevKit)
- Cacciavite P7 (facoltativo, usato per la protezione del connettore del cavo di alimentazione alla lavagna del gestore)

## <a name="unbox-and-assemble-your-device"></a>Unboxing e assemblaggio del dispositivo

1. Eseguire l'unboxing dei componenti di Azure Percept DK.

    Il DevKit contiene una lavagna del vettore, Azure Percept Vision SoM, accessori box che contiene antenne e cavi e una carta di benvenuto con una chiave esadecimale.

1. Connettere i componenti di devkit.

    > [!NOTE]
    > La porta dell'adattatore di alimentazione si trova sul lato destro della lavagna del vettore. Le porte rimanenti (2x USB-A, 1x USB-C e 1x Ethernet) e il pulsante di alimentazione si trovano sul lato sinistro della lavagna del vettore.

    1. Avvitare entrambe Wi-Fi antenne nella lavagna del vettore.

    1. Connettere il modello Vision SoM alla porta USB-C della lavagna del vettore con il cavo USB-C.

    1. Collegare il cavo di alimentazione alla scheda di alimentazione.

    1. Rimuovere la confezione di plastica rimanente dai dispositivi.

    1. Connettere il cavo/adattatore alla lavagna del vettore e una presa a muro. Per proteggere completamente il connettore del cavo di alimentazione alla lavagna del vettore, usare un cacciavite P7 (non incluso in DevKit) per rafforzare le viti del connettore.

    1. Dopo aver collegato il cavo di alimentazione a una presa a muro, il dispositivo si accende automaticamente. Il pulsante di alimentazione sul lato sinistro della lavagna del vettore verrà illuminato. Attendere un po' di tempo per l'avvio del dispositivo.

        > [!NOTE]
        > Il pulsante di alimentazione è per spegnere o riavviare il dispositivo quando si è connessi a un'alimentazione. In caso di interruzione dell'alimentazione, il dispositivo verrà riavviato automaticamente.

Per una dimostrazione visiva dell'assembly DevKit, vedere 0:00-0:50 del video seguente:

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Passaggi successivi

Ora che la DevKit è connessa e accesa, vedere la [procedura dettagliata sull'esperienza di installazione di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md) per completare la configurazione del dispositivo. L'esperienza di installazione consente di connettere la DevKit a una rete Wi-Fi, configurare un account di accesso SSH, creare un hub delle cose ed effettuare il provisioning della DevKit nel proprio account Azure. Dopo aver completato l'installazione del dispositivo, sarà possibile iniziare a creare i prototipi.