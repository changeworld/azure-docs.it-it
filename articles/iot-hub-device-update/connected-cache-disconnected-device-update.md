---
title: Informazioni sul supporto per l'aggiornamento dei dispositivi disconnessi tramite la cache connessa Microsoft | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Informazioni sul supporto per l'aggiornamento dei dispositivi disconnessi tramite la cache connessa Microsoft
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e2b27934f58402ecfb7dabf5560dc43e45f3f7dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101679547"
---
# <a name="understand-support-for-disconnected-device-updates"></a>Informazioni sul supporto per gli aggiornamenti dei dispositivi disconnessi

In uno scenario di gateway trasparente, uno o più dispositivi possono passare i messaggi tramite un singolo dispositivo gateway che mantiene la connessione all'hub di Azure. In questi casi, è possibile che i dispositivi figlio non dispongano di connettività Internet o che non siano autorizzati a scaricare contenuti da Internet. Il modulo Microsoft Connected cache Preview IoT Edge fornirà l'aggiornamento dei dispositivi per i clienti dell'hub Azure Internet con la funzionalità di una cache in rete intelligente, che consente di eseguire aggiornamenti basati su immagine e basati su pacchetti di dispositivi Linux basati su sistema operativo dietro e IoT Edge Gateway (dispositivi dell'intero downstream) e contribuirà anche a risparmiare larghezza di banda per i clienti dell'hub Azure.

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>Come funziona l'anteprima della cache connessa Microsoft per l'aggiornamento dei dispositivi per l'hub Azure?

Microsoft Connected cache è una cache intelligente e trasparente per il contenuto pubblicato per l'aggiornamento del dispositivo per il contenuto dell'hub Azure Internet e può essere personalizzato per memorizzare nella cache il contenuto da altre origini, come anche i repository di pacchetti. Microsoft Connected cache è una cache a freddo riscaldata dalle richieste client per gli intervalli di file esatti richiesti dal client di ottimizzazione recapito e che non esegue il pre-seeding del contenuto. Il diagramma e la descrizione dettagliata riportata di seguito illustrano il funzionamento della cache connessa Microsoft nell'ambito dell'aggiornamento del dispositivo per l'infrastruttura dell'hub Azure.

>[!Note]
>Per definire questo flusso, si presuppone che il gateway IoT Edge disponga della connettività Internet. Per lo scenario downstream IoT Edge Gateway (Edge annidato), la rete per la distribuzione di contenuti (CDN) può essere considerata l'MCC ospitata sul gateway IoT Edge padre.

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Aggiornamento dispositivo disconnesso" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. La cache connessa Microsoft viene distribuita come modulo IoT Edge al server locale.
2. L'aggiornamento del dispositivo per i client dell'hub Internet Azure è configurato per scaricare il contenuto dalla cache connessa a Microsoft in base all'attributo GatewayHostName della stringa di connessione del dispositivo per i dispositivi foglia **o** parent_hostname impostati nel file config. toml per IOT Edge dispositivi figlio.
3. Aggiornamento del dispositivo per i client dell'hub Azure Internet in entrambi i casi ricevere i comandi di download del contenuto di aggiornamento dall'aggiornamento del dispositivo per il servizio Hub Azure Internet e richiedere il contenuto dell'aggiornamento alla cache connessa Microsoft invece che alla rete CDN. Microsoft Connected cache per impostazione predefinita è configurato per l'ascolto sulla porta HTTP 80 e il client di ottimizzazione recapito esegue la richiesta di contenuto sulla porta 80, in modo che l'elemento padre deve essere configurato per l'ascolto su questa porta.  Al momento è supportato solo il protocollo http.
4. Il server della cache connessa Microsoft Scarica il contenuto dalla rete CDN, esegue il seeding della cache locale archiviata su disco e recapita il contenuto all'aggiornamento del dispositivo per il client dell'hub Azure.
   
>[!Note]
>Quando si usano gli aggiornamenti basati su pacchetti, il server della cache connessa Microsoft verrà configurato dall'amministratore con il nome host del pacchetto necessario.

5. Le richieste successive provenienti da altri aggiornamenti del dispositivo per i client dell'hub Azure Internet per lo stesso contenuto di aggiornamento provengono ora dalla cache e la cache connessa Microsoft non effettuerà richieste alla rete CDN per lo stesso contenuto.

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>Supporto di Industrial Internet (IIoT) con scenari di hosting padre/figlio

Quando un gateway downstream o figlio IoT Edge ospita il server della cache connessa Microsoft, verrà configurato per richiedere il contenuto dell'aggiornamento dal gateway IoT Edge padre, ospitando il server della cache connessa Microsoft. Questa operazione è necessaria per il numero di livelli necessario prima di raggiungere il gateway IoT Edge padre che ospita un server di cache connesso Microsoft che dispone di accesso a Internet. Dal server connesso a Internet, il contenuto viene richiesto dalla rete CDN a cui il contenuto viene recapitato al gateway IoT Edge figlio che ha richiesto originariamente il contenuto. Il contenuto verrà archiviato su disco a ogni livello.

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>Accesso all'anteprima della cache connessa Microsoft per l'aggiornamento del dispositivo per l'hub Azure

Il modulo Microsoft Connected cache IoT Edge viene rilasciato come anteprima per i clienti che distribuiscono soluzioni usando l'aggiornamento dei dispositivi per l'hub Azure. L'accesso all'anteprima è tramite invito. [Richiedere l'accesso](https://aka.ms/MCCForDeviceUpdateForIoT) all'anteprima della cache connessa Microsoft per l'aggiornamento del dispositivo per il rifugio Internet e fornire le informazioni richieste se si vuole accedere al modulo.
