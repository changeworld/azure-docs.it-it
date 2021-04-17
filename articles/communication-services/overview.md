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
ms.openlocfilehash: 8c2559315e3bfffc41c138be6826adae95dd7b07
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588106"
---
# <a name="what-is-azure-communication-services"></a>Che cos'è l'offerta Servizi di comunicazione di Azure?

Servizi di comunicazione di Azure consente di aggiungere facilmente comunicazioni vocali, video e telefoniche in tempo reale alle applicazioni. Gli SDK di Servizi di comunicazione consentono anche di aggiungere funzionalità SMS alle soluzioni di comunicazione. Servizi di comunicazione di Azure è indipendente dall'identità e si ha il controllo completo sul modo in cui gli utenti finali vengono identificati e autenticati. È possibile connettere gli utenti al piano dati di comunicazione o ai servizi (bot).

Le applicazioni includono:

- **Business to Consumer (B2C).** I dipendenti e i servizi di un'azienda possono interagire con i consumer usando chat vocali, video e testo rtf in un browser personalizzato o in un'applicazione per dispositivi mobili. Un'organizzazione può inviare e ricevere messaggi SMS o usare un sistema IVR (Interactive Voice Response System) usando un numero di telefono acquisito tramite Azure. [L'integrazione con Microsoft Teams](./quickstarts/voice-video-calling/get-started-teams-interop.md) consente agli utenti di partecipare alle riunioni di Teams ospitate dai dipendenti; ideale per scenari di assistenza sanitaria remota, bancaria e di supporto dei prodotti in cui i dipendenti potrebbero avere già familiarità con Teams.
- **Da consumer a consumer.** Creare spazi social coinvolgenti per l'interazione consumer-consumer con voce, video e chat di testo rtf. È possibile creare qualsiasi tipo di interfaccia utente Servizi di comunicazione di Azure SDK, ma sono disponibili esempi completi di applicazioni e asset dell'interfaccia utente per iniziare rapidamente.

Per altre informazioni, vedere il [video di Microsoft Mechanics](https://www.youtube.com/watch?v=apBX7ASurgM) o le risorse collegate di seguito.

## <a name="common-scenarios"></a>Scenari comuni

<br>

| Risorsa                               |Descrizione                           |
|---                                    |---                                   |
|**[Creare una risorsa di Servizi di comunicazione](./quickstarts/create-communication-resource.md)**|Iniziare a Servizi di comunicazione di Azure usando portale di Azure o Communication Services SDK per effettuare il provisioning della prima risorsa di Servizi di comunicazione. Dopo aver ottenuto la stringa di connessione della risorsa di Servizi di comunicazione, è possibile effettuare il provisioning dei primi token di accesso utente.|
|**[Ottenere un numero di telefono](./quickstarts/telephony-sms/get-phone-number.md)**|È possibile usare Servizi di comunicazione di Azure per effettuare il provisioning e rilasciare i numeri di telefono. Questi numeri di telefono possono essere usati per avviare o ricevere chiamate telefoniche e compilare soluzioni SMS.|
|**[Inviare un SMS dall'app](./quickstarts/telephony-sms/send.md)**|Il Servizi di comunicazione di Azure SMS SDK viene usato per inviare e ricevere messaggi SMS dalle applicazioni di servizio.|

Dopo aver creato una risorsa di Servizi di comunicazione, è possibile iniziare a creare scenari client, ad esempio chiamate vocali e video o sms:

| Risorsa                               |Descrizione                           |
|---                                    |---                                   |
|**[Creare i primi token di accesso utente](./quickstarts/access-tokens.md)**|I token di accesso utente vengono usati per autenticare i client nella Servizi di comunicazione di Azure risorsa. Il provisioning e la nuova emissione di questi token vengono evasi tramite l'SDK di Servizi di comunicazione.|
|**[Introduzione alle chiamate vocali e video](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Servizi di comunicazione di Azure consente di aggiungere chiamate vocali e video al browser o alle app native usando Calling SDK. |
|**[Aggiungere l'app di chiamata a una riunione di Teams](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|È possibile usare Servizi di comunicazione di Azure per creare esperienze personalizzate di riunioni che interagiscono con Microsoft Teams. Gli utenti delle soluzioni di Servizi di comunicazione possono interagire con i partecipanti di Teams tramite voce, video, chat e condivisione dello schermo.|
|**[Introduzione alle chat](./quickstarts/chat/get-started.md)**|L Servizi di comunicazione di Azure Chat SDK viene usato per aggiungere chat di testo in tempo reale alle applicazioni.|

## <a name="samples"></a>Esempi

Gli esempi seguenti illustrano l'utilizzo end-to-end del Servizi di comunicazione di Azure. Usare questi esempi per avviare le proprie soluzioni di Servizi di comunicazione.
<br>

| Nome esempio                               | Descrizione                           |
|---                                    |---                                   |
|**[Esempio hero di chiamate di gruppo](./samples/calling-hero-sample.md)**| Scaricare un esempio di applicazione progettata per le chiamate di gruppo per browser, dispositivi iOS e Android. |
|**[Esempio hero di chat di gruppo](./samples/chat-hero-sample.md)**| Scaricare un esempio di applicazione progettata per la chat di testo di gruppo per i browser. |


## <a name="platforms-and-sdk-libraries"></a>Piattaforme e librerie SDK

Altre informazioni sugli SDK Servizi di comunicazione di Azure con le risorse seguenti. Le API REST sono disponibili per la maggior parte delle funzionalità se si vogliono creare client personalizzati o accedere in altro modo al servizio tramite Internet.

| Risorsa                               | Descrizione                           |
|---                                    |---                                   |
|**[Librerie SDK e API REST](./concepts/sdk-options.md)**|Servizi di comunicazione di Azure funzionalità sono organizzate concettualmente in sei aree, ognuna rappresentata da un SDK. È possibile decidere quali librerie SDK usare in base alle esigenze di comunicazione in tempo reale.|
|**[Panoramica delle chiamate all'SDK](./concepts/voice-video-calling/calling-sdk-features.md)**|Esaminare la panoramica dell'SDK per le chiamate di Servizi di comunicazione.|
|**[Panoramica di Chat SDK](./concepts/chat/sdk-features.md)**|Esaminare la panoramica di Communication Services Chat SDK.|
|**[Panoramica di SMS SDK](./concepts/telephony-sms/sdk-features.md)**|Esaminare la panoramica di Communication Services SMS SDK.|

## <a name="other-microsoft-communication-services"></a>Altri servizi di comunicazione Microsoft

Esistono altri due prodotti di comunicazione Microsoft che è possibile usare che non sono direttamente interoperabili con Servizi di comunicazione in questo momento:

 - [Microsoft Graph le API di comunicazione](/graph/cloud-communications-concept-overview) cloud consentono alle organizzazioni di creare esperienze di comunicazione associate Azure Active Directory utenti con Microsoft 365 licenze. Questa soluzione è ideale per le applicazioni associate ad Azure Active Directory o in cui si vuole estendere le esperienze di produttività di Microsoft Teams. Sono anche disponibili API per la creazione di applicazioni e la personalizzazione all'interno dell'esperienza [Teams](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest).

 - [Azure PlayFab Party](/gaming/playfab/features/multiplayer/networking/), che semplifica l'aggiunta di comunicazioni di dati e chat a bassa latenza ai giochi. Anche se è possibile usare sistemi di rete e chat per il gioco con Servizi di comunicazione, PlayFab è un'opzione personalizzata e gratuita su Xbox.


## <a name="next-steps"></a>Passaggi successivi

 - [Creare una risorsa di Servizi di comunicazione](./quickstarts/create-communication-resource.md)
