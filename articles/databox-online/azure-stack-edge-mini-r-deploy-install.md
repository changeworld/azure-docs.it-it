---
title: Esercitazione sull'installazione del dispositivo fisico Azure Stack Edge Mini R | Microsoft Docs
description: La seconda esercitazione sull'installazione del dispositivo Azure Stack Edge Mini R include le informazioni su come cablare il dispositivo fisico per l'alimentazione e la rete.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Mini R device in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 4f7656337b12cf477c5c71d861d031919e0d55d6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464977"
---
# <a name="tutorial-install-azure-stack-edge-mini-r"></a>Esercitazione: Installare Azure Stack Edge Mini R

Questa esercitazione descrive come installare un dispositivo fisico Azure Stack Edge Mini R. La procedura di installazione comporta il cablaggio del dispositivo.

Il completamento dell'installazione può richiedere circa 30 minuti.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Esaminare il dispositivo
> * Cablare il dispositivo

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti per installare un dispositivo fisico sono i seguenti:

### <a name="for-the-azure-stack-edge-resource"></a>Per la risorsa Azure Stack Edge

Prima di iniziare, verificare che:

* Siano stati completati tutti i passaggi descritti in [Preparare la distribuzione di Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-prep.md).
    * Sia stata creata una risorsa Azure Stack Edge per distribuire il dispositivo.
    * È stata generata la chiave di attivazione per attivare il dispositivo con la risorsa Azure Stack Edge.

 
### <a name="for-the-azure-stack-edge-mini-r-physical-device"></a>Per il dispositivo fisico Azure Stack Edge Mini R

Prima di distribuire un dispositivo:

- Assicurarsi che il dispositivo sia collocato in sicurezza su una superficie di lavoro orizzontale, stabile e piana.
- Verificare che il sito in cui si intende installare abbia:
    - Alimentazione CA standard da una fonte di energia indipendente - OPPURE
    - Un'unità PDU (Power Distribution Unit) in rack. 
    

### <a name="for-the-network-in-the-datacenter"></a>Per la rete nel datacenter

Prima di iniziare:

- Verificare i requisiti di rete per la distribuzione di Azure Stack Edge Mini R e configurare la rete del data center in base ai requisiti. Per altre informazioni, vedere i [requisiti di rete di Azure Stack Edge](azure-stack-edge-mini-r-system-requirements.md#networking-port-requirements).

- Assicurarsi che la larghezza di banda Internet sia di almeno 20 Mbps per garantire un funzionamento ottimale del dispositivo. <!-- engg TBC -->


## <a name="inspect-the-device"></a>Esaminare il dispositivo

Questo dispositivo viene fornito come unità singola. Completare la procedura seguente per disimballare il dispositivo.

1. Posizionare la scatola su una superficie piana e orizzontale.
2. Esaminare la custodia del dispositivo per rilevare eventuali danni. Aprire la custodia ed esaminare il dispositivo. Se la custodia del dispositivo risulta danneggiata, contattare il supporto tecnico Microsoft per istruzioni su come verificare il corretto funzionamento del dispositivo.
3. Una volta aperta la custodia, verificare di avere:
    - Un dispositivo Azure Stack Edge Mini R portatile con paracolpi fissati ai lati.
    - Una batteria e la copertura posteriore fissata al dispositivo. 
    - Un cavo di alimentazione per collegare la batteria all'alimentazione. 

In caso di mancato ricevimento di uno o più degli elementi elencati sopra, contattare il supporto tecnico di Azure Stack Edge. Il passaggio successivo consiste nel cablare il dispositivo.


## <a name="cable-the-device"></a>Cablare il dispositivo

Le procedure seguenti illustrano come cablare il dispositivo Azure Stack Edge per l'alimentazione e la rete.

Prima di iniziare a collegare il dispositivo, sono necessari gli elementi seguenti:

- Il dispositivo fisico Azure Stack Edge Mini R nel sito di installazione.
- Un cavo di alimentazione.
- Almeno un cavo di rete 1-GbE RJ-45 per il collegamento all'interfaccia di gestione. Nel dispositivo sono presenti due interfacce di rete 1-GbE, una per la gestione e una per i dati.
- Un cavo di rame 10-GbE SFP+ per ogni interfaccia di rete dati da configurare. Almeno una delle interfacce di rete dati tra PORT 3 o PORT 4 deve essere connessa a Internet (con connettività ad Azure).  
- Accesso a un'unità PDU (consigliato).

> [!NOTE]
> - Se si connette una sola interfaccia di rete dati, per inviare dati ad Azure è consigliabile usare un'interfaccia di rete 10-GbE come PORT 3 o PORT 4. 
> - Per ottenere prestazioni ottimali e per gestire volumi elevati di dati, prendere in considerazione il collegamento di tutte le porte dati.
> - Per poter inserire dati dai server di origine dati, il dispositivo Azure Stack Edge deve essere connesso alla rete del data center. <!-- engg TBC -->

Nel Dispositivo Azure Stack Edge:

- Il pannello anteriore dispone di un porta-SSD. 

    - Il dispositivo ha 1 disco SSD nello slot. 
    - Il dispositivo ha anche una scheda CFx che funge da archivio per il disco del sistema operativo.
    
- Nel pannello anteriore sono presenti interfacce di rete e accesso al Wi-Fi.

    - 2 interfacce di rete da 1 GbE RJ 45. Si tratta di PORT 1 e PORT 2 nell'interfaccia utente locale del dispositivo.
    - 2 interfacce di rete da 10 GbE SFP+. Si tratta di PORT 3 e PORT 4 nell'interfaccia utente locale del dispositivo. 
    - Una porta Wi-Fi con ricetrasmettitore Wi-Fi collegato.

- Nel pannello anteriore è presente anche un pulsante di accensione. 

- Il pannello posteriore include una batteria e un coperchio installati nel dispositivo. 


Per collegare il dispositivo per l'alimentazione e la rete, completare i passaggi seguenti.

1. Identificare i vari componenti di rete e archiviazione sul pannello anteriore del dispositivo.

    ![Interfacce di rete e archiviazione nel dispositivo](./media/azure-stack-edge-mini-r-deploy-install/ports-front-plane.png)

2. Individuare il pulsante di accensione nell'angolo in basso a sinistra sul pannello anteriore del dispositivo. 

    ![Pannello anteriore di un dispositivo con il pulsante di accensione sul dispositivo](./media/azure-stack-edge-mini-r-deploy-install/device-power-button.png)

3. La batteria è collegata al backplane del dispositivo. Identificare il secondo pulsante di accensione collocato sulla batteria. 

    ![Pannello anteriore di un dispositivo con il pulsante di accensione sulla batteria](./media/azure-stack-edge-mini-r-deploy-install/battery-power-button.png)


    Collegare un'estremità del cavo di alimentazione alla batteria e l'altra alla presa elettrica. 

    ![Collegamento del cavo di alimentazione](./media/azure-stack-edge-mini-r-deploy-install/power-cord-connector-1.png) 

    Quando si usa solo la batteria (la batteria non è collegata all'alimentazione), sia l'interruttore di alimentazione sul pannello anteriore che l'interruttore sulla batteria devono essere nella posizione ON. Quando la batteria è collegata all'alimentazione, solo il pulsante di accensione sul pannello anteriore del dispositivo deve essere nella posizione ON. 

4. Premere il pulsante di accensione sul pannello anteriore per accendere il dispositivo. 
    
    > [!NOTE]
    > Per attivare o disattivare l'alimentazione del dispositivo, è necessario premere il pulsante nero sopra il pulsante di accensione e quindi posizionare il pulsante di accensione su ON o OFF. 

5. Per configurare il Wi-Fi su questo dispositivo, usare il diagramma di cablaggio seguente:

    ![Cablaggio per la rete Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)  

    - Collegare l'interfaccia di rete PORT 1 a 1 GbE al computer usato per configurare il dispositivo fisico. PORT 1 è l'interfaccia di gestione dedicata.


    Se si usa una configurazione cablata per il dispositivo, usare il diagramma seguente:
     
    ![Cablaggio per la rete cablata](./media/azure-stack-edge-mini-r-deploy-install/wired-cabled.png)     
    - Collegare l'interfaccia di rete PORT 1 a 1 GbE al computer usato per configurare il dispositivo fisico. PORT 1 è l'interfaccia di gestione dedicata.
    - Collegare una o più delle interfacce di rete PORT 2, PORT 3 o PORT 4 alla rete del data center/Internet.
    
        - Se si collega l'interfaccia di rete PORT 2, usare il cavo di rete RJ-45.
        - Per le interfacce di rete 10-GbE, usare i cavi di rame SFP+.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Stack Edge, ad esempio:

> [!div class="checklist"]
> * Disimballare il dispositivo
> * Cablare il dispositivo

Passare all'esercitazione successiva per informazioni su come connettere, configurare e attivare il dispositivo.

> [!div class="nextstepaction"]
> [Connettere e configurare Azure Stack Edge](./azure-stack-edge-mini-r-deploy-connect.md)
