---
title: Informazioni sul supporto per l'aggiornamento dei dispositivi disconnessi tramite Microsoft cache connessa | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Informazioni sul supporto per l'aggiornamento dei dispositivi disconnessi tramite Microsoft cache connessa
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e216d42ff1f279d87e657126514fcfb50960f806
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811905"
---
# <a name="understand-support-for-disconnected-device-updates"></a>Informazioni sul supporto per gli aggiornamenti dei dispositivi disconnessi

In uno scenario di gateway trasparente, uno o più dispositivi possono passare i messaggi tramite un singolo dispositivo gateway che mantiene la connessione hub IoT di Azure. In questi casi, i dispositivi figlio potrebbero non avere connettività Internet o non essere autorizzati a scaricare contenuto da Internet. Il modulo Microsoft cache connessa Preview IoT Edge fornirà ai clienti di hub IoT di Azure la funzionalità di una cache intelligente in rete, che consente aggiornamenti basati su immagini e basati su pacchetti di dispositivi basati su sistema operativo Linux dietro e gateway IoT Edge (dispositivi IoT downstream) e consente anche di risparmiare larghezza di banda per l'aggiornamento dei dispositivi per i clienti hub IoT di Azure.

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>Come funziona Microsoft cache connessa preview for Device Update for hub IoT di Azure?

Microsoft cache connessa Preview è una cache intelligente e trasparente per il contenuto pubblicato per l'aggiornamento del dispositivo per il contenuto hub IoT di Azure e può essere personalizzata per memorizzare nella cache anche il contenuto di altre origini, ad esempio i repository di pacchetti. Microsoft cache connessa è una cache a freddo che viene riscaldata dalle richieste client per gli intervalli di file esatti richiesti dal client Ottimizzazione recapito e non esegue il pre-seed del contenuto. Il diagramma e la descrizione dettagliata seguenti illustrano il funzionamento di Microsoft cache connessa all'interno dell'infrastruttura di aggiornamento hub IoT di Azure dispositivo.

>[!Note]
>Nella definizione di questo flusso si presuppone che il gateway IoT Edge abbia connettività Internet. Per lo scenario downstream IoT Edge gateway (Nested Edge) la "rete per la distribuzione di contenuti" (CDN) può essere considerata la rete MCC ospitata nel gateway IoT Edge padre.

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Aggiornamento del dispositivo disconnesso" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Microsoft cache connessa viene distribuito come modulo IoT Edge nel server locale.
2. L'aggiornamento del dispositivo per i client hub IoT di Azure è configurato per scaricare contenuto da Microsoft cache connessa in virtù dell'attributo GatewayHostName della stringa di connessione del dispositivo per i dispositivi foglia IoT **O** parent_hostname impostato nel file config.toml per i dispositivi figlio IoT Edge.
3. Aggiornamento dei dispositivi per hub IoT di Azure client in entrambi i casi ricevono i comandi di download del contenuto di aggiornamento dal servizio Aggiornamento dispositivi per hub IoT di Azure e richiedono il contenuto di aggiornamento a Microsoft cache connessa anziché alla rete CDN. Microsoft cache connessa per impostazione predefinita è configurato per l'ascolto sulla porta HTTP 80 e il client Ottimizzazione recapito effettua la richiesta di contenuto sulla porta 80, quindi l'elemento padre deve essere configurato per l'ascolto su questa porta.  Al momento è supportato solo il protocollo HTTP.
4. Il server Microsoft cache connessa scarica il contenuto dalla rete CDN, esegue il seed della cache locale archiviata su disco e recapita il contenuto all'aggiornamento del dispositivo per hub IoT di Azure client.
   
>[!Note]
>Quando si usano gli aggiornamenti basati su pacchetto, il server microsoft cache connessa verrà configurato dall'amministratore con il nome host del pacchetto richiesto.

5. Le richieste successive da altri client di aggiornamento del dispositivo hub IoT di Azure per lo stesso contenuto di aggiornamento verranno ora inviate dalla cache e Microsoft cache connessa non inserirà richieste alla rete CDN per lo stesso contenuto.

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>Supporto di Industrial IoT (IIoT) con scenari di hosting padre/figlio

Quando un gateway IoT Edge downstream o figlio ospita il server Microsoft cache connessa, verrà configurato per richiedere il contenuto di aggiornamento dal gateway IoT Edge padre, ospitando il server Microsoft cache connessa. Questa operazione è necessaria per tutti i livelli necessari prima di raggiungere il gateway IoT Edge che ospita un server Microsoft cache connessa con accesso a Internet. Dal server connesso a Internet, il contenuto viene richiesto dalla rete CDN a quel punto il contenuto viene recapitato al gateway IoT Edge figlio che ha originariamente richiesto il contenuto. Il contenuto verrà archiviato su disco a ogni livello.

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>Accesso all'anteprima di Microsoft cache connessa per l'aggiornamento dei dispositivi per hub IoT di Azure

Il modulo Microsoft cache connessa IoT Edge viene rilasciato come anteprima per i clienti che distribuiscono soluzioni usando Aggiornamento dispositivi per hub IoT di Azure. L'accesso all'anteprima è su invito. [Richiedere l'accesso](https://aka.ms/MCCForDeviceUpdateForIoT) a Microsoft cache connessa Preview per l'aggiornamento dei dispositivi per IoT e fornire le informazioni richieste se si vuole accedere al modulo.
