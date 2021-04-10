---
title: Che cos'è l'offerta Servizi di comunicazione di Azure?
description: Informazioni su come Servizi di comunicazione di Azure consente di sviluppare esperienze utente avanzate con comunicazioni in tempo reale.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 40fe1e59b7c600e273b677d60bc162c767569c14
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728573"
---
# <a name="what-is-azure-communication-services"></a>Che cos'è l'offerta Servizi di comunicazione di Azure?

> [!IMPORTANT]
> Le applicazioni compilate con i servizi di comunicazione di Azure possono comunicare con Microsoft teams. Per altre informazioni, visita la documentazione di [interoperabilità dei team](./quickstarts/voice-video-calling/get-started-teams-interop.md) .


Servizi di comunicazione di Azure consente di aggiungere in modo semplice funzionalità di comunicazione vocale, video e telefonia tramite IP in tempo reale alle applicazioni. Gli SDK di servizi di comunicazione consentono inoltre di aggiungere funzionalità di chat e SMS alle soluzioni di comunicazione.

<br>

> [!VIDEO https://www.youtube.com/embed/apBX7ASurgM]

<br>
<br>

È possibile usare Servizi di comunicazione per la comunicazione vocale, video, di testo e dati in diversi scenari:

- Comunicazione da browser a browser, da browser ad app e da app ad app
- Interazione dell'utente con bot o altri servizi
- Interazione di utenti e bot tramite PSTN

Sono supportati scenari misti. Ad esempio, in un'applicazione di Servizi di comunicazione possono essere presenti utenti che parlano contemporaneamente da browser e dispositivi tradizionali di telefonia. Servizi di comunicazione può anche combinarsi con il servizio Azure Bot per creare sistemi IVR (Interactive Voice Response) basati su bot.

## <a name="common-scenarios"></a>Scenari comuni

Le risorse seguenti sono un ottimo punto di partenza per iniziare a usare i servizi di comunicazione di Azure. 
<br>

| Risorsa                               |Descrizione                           |
|---                                    |---                                   |
|**[Creare una risorsa di Servizi di comunicazione](./quickstarts/create-communication-resource.md)**|È possibile iniziare a usare i servizi di comunicazione di Azure usando il portale di Azure o Communication Services SDK per eseguire il provisioning della prima risorsa di servizi di comunicazione. Dopo aver ottenuto la stringa di connessione della risorsa di Servizi di comunicazione, è possibile effettuare il provisioning dei primi token di accesso utente.|
|**[Ottenere un numero di telefono](./quickstarts/telephony-sms/get-phone-number.md)**|È possibile usare Servizi di comunicazione di Azure per effettuare il provisioning e rilasciare i numeri di telefono. Questi numeri di telefono possono essere usati per avviare le chiamate in uscita e creare soluzioni di comunicazione SMS.|
|**[Inviare un SMS dall'app](./quickstarts/telephony-sms/send.md)**|Azure Communication Services SMS SDK consente di inviare e ricevere messaggi SMS dalle applicazioni .NET e JavaScript.|

Dopo aver creato una risorsa di servizi di comunicazione è possibile iniziare a creare scenari client, ad esempio la chiamata vocale e video o la chat di testo.

| Risorsa                               |Descrizione                           |
|---                                    |---                                   |
|**[Creare i primi token di accesso utente](./quickstarts/access-tokens.md)**|I token di accesso utente vengono usati per autenticare i servizi con la risorsa di Servizi di comunicazione di Azure. Questi token vengono sottoposti a provisioning e rilasciati tramite Communication Services SDK.|
|**[Introduzione alle chiamate vocali e video](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Servizi di comunicazione di Azure consente di aggiungere chiamate vocali e video alle app usando l'SDK chiamante. Questa libreria si basa su WebRTC e consente di stabilire comunicazioni peer-to-peer, multimediali e in tempo reale all'interno delle applicazioni.|
|**[Aggiungere l'app di chiamata a una riunione di Teams](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|È possibile usare Servizi di comunicazione di Azure per creare esperienze personalizzate di riunioni che interagiscono con Microsoft Teams. Gli utenti delle soluzioni per i servizi di comunicazione possono interagire con i team partecipanti attraverso la voce, il video, la chat e la condivisione dello schermo.|
|**[Introduzione alle chat](./quickstarts/chat/get-started.md)**|Azure Communication Services Chat SDK può essere usato per integrare la chat in tempo reale nelle applicazioni.|

## <a name="samples"></a>Esempi

Gli esempi seguenti illustrano l'uso end-to-end degli SDK di servizi di comunicazione di Azure. È possibile usare questi esempi per il bootstrap di soluzioni di Servizi di comunicazione personalizzate.
<br>

| Nome esempio                               | Descrizione                           |
|---                                    |---                                   |
|**[Esempio hero di chiamate di gruppo](./samples/calling-hero-sample.md)**|Scopri in che modo è possibile usare gli SDK di comunicazione per creare un'esperienza di chiamata di gruppo.|
|**[Esempio hero di chat di gruppo](./samples/chat-hero-sample.md)**|Scopri in che modo è possibile usare gli SDK di Communication Services per creare un'esperienza di chat di gruppo.|


## <a name="platforms-and-sdks"></a>Piattaforme e SDK

Le risorse seguenti consentono di ottenere informazioni sugli SDK di servizi di comunicazione Azure:

| Risorsa                               | Descrizione                           |
|---                                    |---                                   |
|**[SDK e API REST](./concepts/sdk-options.md)**|Le funzionalità dei servizi di comunicazione di Azure sono organizzate concettualmente in sei aree, ciascuna rappresentata da un SDK. È possibile scegliere gli SDK da usare in base alle esigenze di comunicazione in tempo reale.|
|**[Panoramica dell'SDK chiamante](./concepts/voice-video-calling/calling-sdk-features.md)**|Esaminare i servizi di comunicazione Cenni preliminari sull'SDK.|
|**[Panoramica di Chat SDK](./concepts/chat/sdk-features.md)**|Vedere la Panoramica di Communication Services Chat SDK.|
|**[Panoramica di SMS SDK](./concepts/telephony-sms/sdk-features.md)**|Vedere la Panoramica di Communication Services SMS SDK.|

## <a name="compare-azure-communication-services"></a>Confrontare Servizi di comunicazione di Azure

Sono disponibili altri due prodotti Microsoft per la comunicazione che attualmente non sono direttamente interoperabili con Servizi di comunicazione:

 - [Microsoft Graph API di comunicazione cloud](/graph/cloud-communications-concept-overview) consentono alle organizzazioni di creare esperienze di comunicazione associate agli utenti Azure Active Directory con licenze Microsoft 365. Questa soluzione è ideale per le applicazioni associate ad Azure Active Directory o in cui si vuole estendere le esperienze di produttività di Microsoft Teams. Sono anche disponibili API per la creazione di applicazioni e la personalizzazione all'interno dell'esperienza [Teams](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest).

 - [Azure PlayFab Party](/gaming/playfab/features/multiplayer/networking/), che semplifica l'aggiunta di comunicazioni di dati e chat a bassa latenza ai giochi. Anche se è possibile usare sistemi di rete e chat per il gioco con Servizi di comunicazione, PlayFab è un'opzione personalizzata e gratuita su Xbox.


## <a name="next-steps"></a>Passaggi successivi

 - [Creare una risorsa di Servizi di comunicazione](./quickstarts/create-communication-resource.md)
