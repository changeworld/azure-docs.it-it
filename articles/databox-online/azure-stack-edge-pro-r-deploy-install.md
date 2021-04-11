---
title: Esercitazione sull'installazione del dispositivo fisico Azure Stack Edge Pro R | Microsoft Docs
description: La seconda esercitazione sull'installazione del dispositivo Azure Stack Edge Pro R include le informazioni su come cablare il dispositivo fisico per l'alimentazione e la rete.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/18/2020
ms.author: alkohli
ms.openlocfilehash: c751c1d9caa06973171d35d5e6bd1f945f9d7a77
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059905"
---
# <a name="tutorial-install-azure-stack-edge-pro-r"></a>Esercitazione: Installare Azure Stack Edge Pro R

Questa esercitazione descrive come installare un dispositivo fisico Azure Stack Edge Pro R. La procedura di installazione comporta il cablaggio del dispositivo.

Il completamento dell'installazione può richiedere circa 30 minuti.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Esaminare il dispositivo
> * Cablare il dispositivo

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti per installare un dispositivo fisico sono i seguenti:

### <a name="for-the-azure-stack-edge-resource"></a>Per la risorsa Azure Stack Edge

Prima di iniziare, verificare che:

* Siano stati completati tutti i passaggi descritti in [Preparare la distribuzione di Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md).
    * Sia stata creata una risorsa Azure Stack Edge per distribuire il dispositivo.
    * È stata generata la chiave di attivazione per attivare il dispositivo con la risorsa Azure Stack Edge.

 
### <a name="for-the-azure-stack-edge-pro-r-physical-device"></a>Per il dispositivo fisico Azure Stack Edge Pro R

Prima di distribuire un dispositivo:

- Assicurarsi che il dispositivo sia collocato in sicurezza su una superficie di lavoro orizzontale, stabile e piana.
- Verificare che il sito in cui si intende installare abbia:
    - Alimentazione CA standard da una fonte di energia indipendente

        -OPPURE-
    - Un'unità PDU (Power Distribution Unit) in rack. Il dispositivo viene fornito con un gruppo di continuità (UPS)
    

### <a name="for-the-network-in-the-datacenter"></a>Per la rete nel datacenter

Prima di iniziare:

- Verificare i requisiti di rete per la distribuzione di Azure Stack Edge Pro R e configurare la rete del data center in base ai requisiti. Per altre informazioni, vedere i [requisiti di rete di Azure Stack Edge Pro R](azure-stack-edge-pro-r-system-requirements.md#networking-port-requirements).

- Assicurarsi che la larghezza di banda Internet sia di almeno 20 Mbps per garantire il funzionamento ottimale del dispositivo.


## <a name="inspect-the-device"></a>Esaminare il dispositivo

Questo dispositivo viene fornito come unità singola. Completare la procedura seguente per disimballare il dispositivo.

1. Posizionare la scatola su una superficie piana e orizzontale.
2. Esaminare la custodia del dispositivo per rilevare eventuali danni. Aprire la custodia ed esaminare il dispositivo. Se la custodia del dispositivo risulta danneggiata, contattare il supporto tecnico Microsoft per istruzioni su come verificare il corretto funzionamento del dispositivo.
3. Una volta aperta la custodia, verificare di avere:
    - Un dispositivo Azure Stack Edge Pro R ad alloggiamento singolo
    - Un gruppo di continuità (UPS)
    - 2 cavi di alimentazione corti per collegare il dispositivo all'UPS
    - 1 cavo di alimentazione per collegare l'UPS all'alimentazione

In caso di mancato ricevimento di uno o più degli elementi sopra elencati, contattare il supporto tecnico di Azure Stack Edge Pro R. Il passaggio successivo consiste nel cablare il dispositivo.


## <a name="cable-the-device"></a>Cablare il dispositivo

Le procedure seguenti illustrano come cablare il dispositivo Azure Stack Edge Pro R per l'alimentazione e la rete.

Prima di iniziare a collegare il dispositivo, sono necessari gli elementi seguenti:

- Il dispositivo fisico Azure Stack Edge Pro R nel sito di installazione.
- Un cavo di alimentazione.
- Almeno un cavo di rete 1-GbE RJ-45 per il collegamento all'interfaccia di gestione. Nel dispositivo sono presenti due interfacce di rete 1-GbE, una per la gestione e una per i dati.
- Un cavo di rame 10/25-GbE SFP+ per ogni interfaccia di rete dati da configurare. Almeno una delle interfacce di rete dati tra PORT 3 o PORT 4 deve essere connessa a Internet (con connettività ad Azure).  
- Accesso a un'unità PDU (consigliato).

> [!NOTE]
> - Se si connette una sola interfaccia di rete dati, per inviare dati ad Azure è consigliabile usare un'interfaccia di rete 25/10-GbE come PORT 3 o PORT 4. 
> - Per ottenere prestazioni ottimali e per gestire volumi elevati di dati, prendere in considerazione il collegamento di tutte le porte dati.
> - Per poter inserire dati dai server di origine dati, il dispositivo Azure Stack Edge Pro R deve essere connesso alla rete del data center.

Nel dispositivo Azure Stack Edge Pro R:

- Il pannello anteriore include unità disco e un pulsante di alimentazione.

    - Nella parte anteriore del dispositivo sono presenti 8 slot disco.
    - All'interno del dispositivo sono presenti anche 2 dischi M.2 SATA che fungono da dischi del sistema operativo. 

- Il backplane include alimentatori ridondanti.
- Il backplane ha quattro interfacce di rete:

    - Due interfacce da 1 Gbps.
    - Due interfacce da 25 Gbps che possono anche essere usate come interfacce da 10 Gbps.
    - Un controller BMC (Baseboard Management Controller).

<!--- The back plane has two network cards corresponding to the 4 ports:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

For a full list of supported cables, switches, and transceivers for these network cards, go to [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).-->
 
Per collegare il dispositivo per l'alimentazione e la rete, completare i passaggi seguenti.

1. Identificare le varie porte sul backplane del dispositivo.

    ![Backplane di un dispositivo cablato](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)

2. Individuare gli slot disco e il pulsante di accensione sul pannello anteriore del dispositivo.

    ![Pannello anteriore di un dispositivo](./media/azure-stack-edge-pro-r-deploy-install/device-front-plane-labeled-1.png)

3. Collegare un'estremità del cavo di alimentazione all'UPS. Collegare l'altra estremità del cavo di alimentazione all'unità PDU (Power Distribution Unit) del rack. 
5. Premere il pulsante di alimentazione per accendere il dispositivo.
6. Collegare l'interfaccia di rete PORT 1 a 1 GbE al computer usato per configurare il dispositivo fisico. PORT 1 è l'interfaccia di gestione dedicata.
7. Collegare una o più delle interfacce di rete PORT 2, PORT 3 o PORT 4 alla rete del data center/Internet.

    - Se si collega l'interfaccia di rete PORT 2, usare il cavo di rete RJ-45.
    - Per le interfacce di rete 10/25-GbE, usare i cavi di rame SFP+.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Stack Edge Pro R, ad esempio:

> [!div class="checklist"]
> * Disimballare il dispositivo
> * Cablare il dispositivo

Per informazioni su come connettersi al dispositivo, procedere con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Connettersi ad Azure Stack Edge Pro R](./azure-stack-edge-pro-r-deploy-connect.md)
