---
title: Servizi di comunicazione di Azure-problemi noti
description: Scopri di più sui servizi di comunicazione di Azure
author: rinarish
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: aa5530dd279e8b45382fe6841b6f193a652c0ba3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566796"
---
# <a name="known-issues-azure-communication-services-client-libraries"></a>Problemi noti: librerie client dei servizi di comunicazione di Azure
Questo articolo fornisce informazioni sulle limitazioni e sui problemi noti relativi alle librerie client dei servizi di comunicazione di Azure.

> [!IMPORTANT]
> Esistono diversi fattori che possono influire sulla qualità dell'esperienza chiamante. Per ulteriori informazioni sulla configurazione di rete dei servizi di comunicazione e sulle procedure consigliate, vedere la documentazione relativa ai **[requisiti di rete](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements)** .


## <a name="javascript-client-library"></a>Libreria client JavaScript

In questa sezione vengono fornite informazioni sui problemi noti associati alle librerie client di chiamata vocale e video di JavaScript nei servizi di comunicazione di Azure.

### <a name="after-refreshing-the-page-user-is-not-removed-from-the-call-immediately"></a>Dopo l'aggiornamento della pagina, l'utente non viene rimosso immediatamente dalla chiamata 
Se l'utente è in una chiamata e decide di aggiornare la pagina, la libreria client dei servizi di comunicazione potrebbe non essere in grado di informare il servizio multimediale dei servizi di comunicazione che sta per essere disconnesso. Il servizio multimediale di servizi di comunicazione non rimuoverà tale utente immediatamente dalla chiamata, ma attenderà che un utente si riunisca presupponendo problemi di connettività di rete. L'utente verrà rimosso dalla chiamata dopo il timeout del servizio multimediale.

Si consiglia agli sviluppatori di creare esperienze che non richiedono agli utenti finali di aggiornare la pagina dell'applicazione partecipando a una chiamata. Se l'utente aggiorna la pagina, il modo migliore per gestirlo per l'app consiste nel riutilizzare lo stesso ID utente di servizi di comunicazione per l'utente dopo il ritorno all'applicazione dopo l'aggiornamento.

Per la prospettiva di altri partecipanti alla chiamata, tale utente rimarrà nella chiamata per un periodo di tempo predefinito (1-2 minuti). Se l'utente si riunirà con lo stesso ID utente di servizi di comunicazione, verrà rappresentato come lo stesso oggetto esistente nella `remoteParticipants` raccolta.
Se in precedenza l'utente stava inviando video, `videoStreams` la raccolta manterrà le informazioni sul flusso precedenti fino al timeout e alla rimozione del servizio, in questo scenario l'applicazione potrebbe decidere di osservare eventuali nuovi flussi aggiunti alla raccolta e ne ne eseguirne il rendering con il valore più elevato `id` . 


### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>Non è possibile eseguire il rendering di più anteprime da più dispositivi sul Web
Si tratta di un limite noto. Per ulteriori informazioni, fare riferimento alla [Panoramica della libreria client chiamante](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) .

### <a name="enumeration-of-the-microphone-and-speaker-devices-is-not-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>L'enumerazione dei dispositivi microfono e speaker non è possibile in Safari quando l'applicazione è in esecuzione in iOS o iPados 
Le applicazioni non possono enumerare/selezionare i dispositivi mic/speaker (ad esempio Bluetooth) su Safari iOS/iPad. Si tratta di una limitazione nota del sistema operativo.

Se si usa Safari in macOS, l'app non sarà in grado di enumerare/selezionare gli altoparlanti tramite i servizi di comunicazione Gestione dispositivi. In questo scenario i dispositivi devono essere selezionati tramite il sistema operativo. Se si usa Chrome in macOS, l'app può enumerare/selezionare i dispositivi tramite i servizi di comunicazione Gestione dispositivi.

### <a name="audio-connectivity-is-lost-when-receiving-sms-messages-or-calls-during-an-ongoing-voip-call"></a>La connettività audio viene persa durante la ricezione di messaggi SMS o chiamate durante una chiamata VoIP in corso
I browser per dispositivi mobili non mantengono la connettività nello stato in background. Questo può comportare un'esperienza di chiamata ridotta se la chiamata VoIP è stata interrotta da un SMS o da una chiamata PSTN in arrivo che effettua il push dell'applicazione in background.

<br/>Libreria client: chiamata a (JavaScript)
<br/>Browser: Safari, Chrome
<br/>Sistema operativo: iOS, Android

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Il cambio continuo dei dispositivi video potrebbe causare l'arresto temporaneo dello streaming video

Il cambio tra dispositivi video può causare la sospensione del flusso video mentre il flusso viene acquisito dal dispositivo selezionato.

#### <a name="possible-causes"></a>Possibili cause
Lo streaming e il cambio tra dispositivi multimediali sono a elevato utilizzo di calcolo. Il cambio frequente può causare un calo delle prestazioni. Gli sviluppatori sono invitati a arrestare un flusso di dispositivi prima di avviarne un altro.

### <a name="bluetooth-headset-microphone-is-not-detected-therefore-is-not-audible-during-the-call-on-safari-on-ios"></a>Il microfono per la cuffia Bluetooth non viene rilevato, pertanto non è udibile durante la chiamata a Safari in iOS
Gli auricolari Bluetooth non sono supportati da Safari in iOS. Il dispositivo Bluetooth non verrà elencato nelle opzioni del microfono disponibili e gli altri partecipanti non saranno in grado di ascoltarli se si prova a usare Bluetooth su Safari.

#### <a name="possible-causes"></a>Possibili cause
Si tratta di una limitazione nota del sistema operativo macOS/iOS/iPados. 

Con Safari in **MacOS** e **iOS/iPad**, non è possibile enumerare/selezionare i dispositivi speaker tramite i servizi di comunicazione gestione dispositivi perché l'enumerazione/selezione degli altoparlanti non è supportata da Safari. In questo scenario, è necessario aggiornare la selezione del dispositivo tramite il sistema operativo.

### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>La rotazione di un dispositivo può creare una qualità del video scadente
Quando i dispositivi vengono ruotati, gli utenti potrebbero riscontrare una riduzione della qualità del video.

<br/>Dispositivi interessati: Google pixel 5, Google pixel 3A, Apple iPad 8 e Apple iPad X
<br/>Libreria client: chiamata a (JavaScript)
<br/>Browser: Safari, Chrome
<br/>Sistema operativo: iOS, Android


### <a name="camera-switching-makes-the-screen-freeze"></a>Lo spostamento della fotocamera rende lo schermo bloccato 
Quando un utente di servizi di comunicazione partecipa a una chiamata usando la libreria client chiamante di JavaScript e quindi raggiunge il pulsante di commutazione della fotocamera, l'interfaccia utente potrebbe essere completamente bloccata fino a quando l'applicazione non viene aggiornata o il browser viene inserito in background dall'utente.

<br/>Dispositivi interessati: Google pixel 4a
<br/>Libreria client: chiamata a (JavaScript)
<br/>Browser: Chrome
<br/>Sistema operativo: iOS, Android


#### <a name="possible-causes"></a>Possibili cause
In fase di analisi.

### <a name="if-the-video-signal-was-stopped-while-the-call-is-in-connecting-state-the-video-will-not-be-sent-after-the-call-started"></a>Se il segnale video è stato interrotto mentre la chiamata è in stato di connessione, il video non verrà inviato dopo l'avvio della chiamata 
Se gli utenti decidono di attivare o disattivare il video rapidamente mentre la chiamata è nello `Connecting` stato, potrebbe verificarsi un problema con il flusso acquisito per la chiamata. Si consiglia agli sviluppatori di creare le proprie app in modo da non richiedere l'accensione o la disattivazione del video mentre la chiamata è nello `Connecting` stato. Questo problema può causare un calo delle prestazioni video negli scenari seguenti:

 - Se l'utente inizia con audio e quindi avvia e arresta video mentre la chiamata è nello `Connecting` stato.
 - Se l'utente inizia con audio e quindi avvia e arresta video mentre la chiamata è nello `Lobby` stato.


#### <a name="possible-causes"></a>Possibili cause
In fase di analisi.

###  <a name="sometimes-it-takes-a-long-time-to-render-remote-participant-videos"></a>In alcuni casi è necessario molto tempo per il rendering di video del partecipante remoto
Durante una chiamata di gruppo in corso, l' _utente a Invia un_ video e quindi l' _utente B_ partecipa alla chiamata. In alcuni casi, l'utente B non Visualizza il video dell'utente A oppure il video dell'utente A inizia il rendering dopo un lungo ritardo. Questo problema potrebbe essere causato da un ambiente di rete che richiede un'ulteriore configurazione. Per indicazioni sulla configurazione di rete, vedere la documentazione relativa ai [requisiti di rete](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements) .
