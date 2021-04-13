---
title: Servizi di comunicazione di Azure panoramica di Calling SDK
titleSuffix: An Azure Communication Services concept document
description: Fornisce una panoramica dell'SDK per chiamate.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 28072184d47beb32dc03e0d6ba52328bfceb5b73
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364873"
---
# <a name="calling-sdk-overview"></a>Panoramica delle chiamate all'SDK

L'SDK per chiamate consente ai dispositivi degli utenti finali di creare esperienze di comunicazione vocale e video. Questa pagina fornisce descrizioni dettagliate delle funzionalità di chiamata, incluse le informazioni sul supporto per piattaforma e browser. Per iniziare subito, vedere [](../../quickstarts/voice-video-calling/getting-started-with-calling.md) Avvio rapido per le chiamate o [Esempio di chiamata hero.](../../samples/calling-hero-sample.md) 

Dopo aver avviato lo sviluppo, vedere la [pagina dei problemi noti](../known-issues.md) per individuare i bug su cui si sta lavorando.

Funzionalità principali dell'SDK per chiamate:

- **Indirizzamento:** Servizi di comunicazione di Azure le [identità generiche](../identity-model.md) usate per risolvere gli endpoint di comunicazione. I client usano queste identità per eseguire l'autenticazione al servizio e comunicare tra loro. Queste identità vengono usate nelle API di chiamata che forniscono ai client visibilità su chi è connesso a una chiamata (elenco).
- **Crittografia:** l'SDK per le chiamate crittografa il traffico e impedisce la manomissione in rete. 
- **Gestione dei** dispositivi e supporti: l'SDK per le chiamate offre funzionalità per l'associazione a dispositivi audio e video, codifica il contenuto per una trasmissione efficiente sul piano dati delle comunicazioni ed esegue il rendering del contenuto nei dispositivi e nelle visualizzazioni di output specificati. Le API vengono fornite anche per la condivisione di schermate e applicazioni.
- **PSTN:** l'SDK per le chiamate può ricevere e avviare chiamate vocali con il tradizionale sistema di telefonia commutato pubblicamente, usando i numeri di telefono acquisiti nel portale di Azure o a [livello](../../quickstarts/telephony-sms/get-phone-number.md) di codice.
- **Teams Meetings:** l'SDK per le chiamate può partecipare [alle riunioni](../../quickstarts/voice-video-calling/get-started-teams-interop.md) di Teams e interagire con il piano dati vocale e video di Teams. 
- **Notifiche:** l'SDK per le chiamate fornisce API che consentono ai client di ricevere una notifica di una chiamata in ingresso. Nelle situazioni in cui l'app non è in esecuzione in primo piano, sono disponibili modelli per l'invio di notifiche popup [("avvisi](../notifications.md) popup") per informare gli utenti finali di una chiamata in ingresso. 

## <a name="detailed-capabilities"></a>Funzionalità dettagliate 

L'elenco seguente presenta il set di funzionalità attualmente disponibili nel Servizi di comunicazione di Azure SDK di chiamata.

| Gruppo di funzionalità | Funzionalità                                                                                                          | JS  | Java (Android) | Objective-C (iOS)
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Funzionalità principali | Inserire una chiamata uno-a-uno tra due utenti                                                                           | ✔️   | ✔️            | ✔️
|                   | Inserire una chiamata di gruppo con più di due utenti (fino a 350 utenti)                                                       | ✔️   | ✔️            | ✔️
|                   | Alzare di livello una chiamata uno-a-uno con due utenti in una chiamata di gruppo con più di due utenti                                 | ✔️   | ✔️            | ✔️
|                   | Partecipare a una chiamata di gruppo dopo che è stata avviata                                                                              | ✔️   | ✔️            | ✔️
|                   | Invitare un altro utente VoIP a partecipare a una chiamata di gruppo in corso                                                       | ✔️   | ✔️            | ✔️
|  Controllo delle chiamate intermedie | Attivare/Disattivare il video                                                                                              | ✔️   | ✔️            | ✔️
|                   | Attivare/Disattivare il microfono                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Passare da una fotocamera all'altra                                                                                              | ✔️   | ✔️            | ✔️
|                   | Bloccare/Sbloccare chiamata locale                                                                                                  | ✔️   | ✔️            | ✔️
|                   | Altoparlante attivo                                                                                                      | ✔️   | ✔️            | ✔️
|                   | Scegliere l'altoparlante per le chiamate                                                                                            | ✔️   | ✔️            | ✔️
|                   | Scegliere il microfono per le chiamate                                                                                         | ✔️   | ✔️            | ✔️
|                   | Mostrare lo stato di un partecipante<br/>*Inattivo, Contenuto multimediale anticipato, Connessione in corso, Connesso, In attesa, In sala di attesa, Disconnesso*         | ✔️   | ✔️            | ✔️
|                   | Mostrare lo stato di una chiamata<br/>*Contenuto multimediale anticipato, In arrivo, Connessione in corso, Chiamata in corso, Connesso, In attesa, Disconnessione in corso, Disconnesso* | ✔️   | ✔️            | ✔️
|                   | Mostrare se un partecipante ha l'audio disattivato                                                                                      | ✔️   | ✔️            | ✔️
|                   | Mostrare il motivo per cui un partecipante ha abbandonato una chiamata                                                                       | ✔️   | ✔️            | ✔️
| Condivisione schermo    | Condividere l'intero schermo dall'applicazione                                                                 | ✔️   | ❌            | ❌
|                   | Condividere un'applicazione specifica (dall'elenco delle applicazioni in esecuzione)                                                | ✔️   | ❌            | ❌
|                   | Condividere una scheda del Web browser dall'elenco delle schede aperte                                                                  | ✔️   | ❌            | ❌
|                   | Il partecipante può visualizzare la condivisione dello schermo remoto                                                                            | ✔️   | ✔️            | ✔️
| Registro            | Elencare i partecipanti                                                                                                   | ✔️   | ✔️            | ✔️
|                   | Rimuovere un partecipante                                                                                                | ✔️   | ✔️            | ✔️
| PSTN              | Inserire una chiamata uno-a-uno con un partecipante PSTN                                                                     | ✔️   | ✔️            | ✔️
|                   | Inserire una chiamata di gruppo con partecipanti PSTN                                                                           | ✔️   | ✔️            | ✔️
|                   | Alzare di livello una chiamata uno-a-uno con un partecipante PSTN in una chiamata di gruppo                                                 | ✔️   | ✔️            | ✔️
|                   | Disconnettersi da una chiamata di gruppo come partecipante PSTN                                                                    | ✔️   | ✔️            | ✔️
| Generale           | Testare microfono, altoparlante e fotocamera con un servizio di test audio (disponibile chiamando 8:echo123)                   | ✔️   | ✔️            | ✔️
| Gestione dei dispositivi | Richiedere l'autorizzazione per l'uso di audio e/o video                                                                       | ✔️   | ✔️            | ✔️
|                   | Ottenere l'elenco delle fotocamere                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Impostare la fotocamera                                                                                                          | ✔️   | ✔️            | ✔️
|                   | Ottenere la fotocamera selezionata                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Ottenere l'elenco dei microfoni                                                                                                 | ✔️   | ❌           |❌  
|                   | Impostare il microfono                                                                                                      | ✔️   | ❌           | ❌  
|                   | Ottenere il microfono selezionato                                                                                             | ✔️   | ❌           | ❌  
|                   | Ottenere l'elenco dei parlanti                                                                                                   | ✔️   | ❌           | ❌  
|                   | Impostare l'altoparlante                                                                                                         | ✔️   | ❌           | ❌  
|                   | Ottenere il parlante selezionato                                                                                                | ✔️   | ❌           | ❌  
| Video Rendering   | Eseguire il rendering di un singolo video in più posizioni (fotocamera locale o flusso remoto)                                                  | ✔️   | ✔️            | ✔️
|                   | Impostare/aggiornare la modalità di ridimensionamento                                                                                           | ✔️   | ✔️            | ✔️
|                   | Eseguire il rendering del flusso video remoto                                                                                          | ✔️   | ✔️            | ✔️

## <a name="calling-sdk-streaming-support"></a>Chiamata del supporto per lo streaming DELL'SDK
Communication Services Calling SDK supporta le configurazioni di streaming seguenti:

| Limite          |Web | Android/iOS|
|-----------|----|------------|
|**Numero di flussi in uscita che possono essere inviati contemporaneamente** |1 video o 1 condivisione dello schermo | 1 video + 1 condivisione dello schermo|
|**Numero di flussi in entrata di cui è possibile eseguire il rendering simultaneamente** |1 video o 1 condivisione dello schermo| 6 video + 1 condivisione dello schermo |

## <a name="calling-sdk-timeouts"></a>Timeout della chiamata all'SDK

I timeout seguenti si applicano agli SDK per chiamate di Servizi di comunicazione:

| Azione           | Timeout in secondi |
| -------------- | ---------- |
| Partecipante di riconnessione/rimozione | 120 |
| Aggiungere o rimuovere una nuova modalità da una chiamata (avvio/arresto di video o condivisione dello schermo) | 40 |
| Timeout dell'operazione di trasferimento delle chiamate | 60 |
| 1:1 Timeout di definizione della chiamata | 85 |
| Timeout di definizione delle chiamate di gruppo | 85 |
| Timeout di definizione delle chiamate PSTN | 115 |
| Alzare di livello la chiamata 1:1 a un timeout di chiamata di gruppo | 115 |

## <a name="javascript-calling-sdk-support-by-os-and-browser"></a>Supporto dell'SDK per chiamate JavaScript da parte del sistema operativo e del browser

La tabella seguente rappresenta il set di browser supportati attualmente disponibili. Sono supportate le tre versioni più recenti del browser, se non diversamente indicato.

| Piattaforma                         | Chrome | Safari*  | Edge (Chromium) |
| -------------------------------- | -------| ------  | --------------  |
| Android                          |  ✔️    | ❌     | ❌             |
| iOS                              |  ❌    | ✔️**** | ❌             |
| macOS***                         |  ✔️    | ✔️**   | ❌             |
| Windows***                       |  ✔️    | ❌     | ✔️             |
| Ubuntu/Linux                     |  ✔️    | ❌     | ❌             |

*Sono supportate le versioni 13.1 e successive di Safari, le chiamate 1:1 non sono supportate in Safari.

**Safari 14+/macOS 11+ necessario per il supporto video in uscita.

La condivisione dello schermo in uscita è supportata solo nelle piattaforme desktop (Windows, macOS e Linux), indipendentemente dalla versione del browser, e non è supportata in alcuna piattaforma per dispositivi mobili (Android, iOS, iPad e tablet).

Un'app iOS in Safari non può enumerare/selezionare i dispositivi microfono e altoparlante (ad esempio Bluetooth); si tratta di una limitazione del sistema operativo ed è sempre presente un solo dispositivo.


## <a name="calling-client---browser-security-model"></a>Client chiamante - modello di sicurezza del browser

### <a name="user-webrtc-over-https"></a>WebRTC utente su HTTPS

Un requisito delle API WebRTC come `getUserMedia` è che l'app che chiama queste API sia servita tramite HTTPS.

Per lo sviluppo locale è possibile usare `http://localhost`.

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>Incorporare l'SDK per le chiamate di Servizi di comunicazione in un iframe

Diversi browser stanno adottando nuovi [criteri di autorizzazioni (detti anche criteri di funzionalità)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute). Questi criteri influiscono sugli scenari di chiamata controllando il modo in cui le applicazioni possono accedere alla fotocamera e al microfono di un dispositivo tramite un elemento iframe tra diverse origini.

Se si vuole usare un iframe per ospitare parte dell'app da un dominio diverso, occorre aggiungere l'attributo `allow` con il valore corretto all'iframe.

Ad esempio, questo iframe consente l'accesso sia alla fotocamera che al microfono:

```html
<iframe allow="camera *; microphone *">
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione alle chiamate](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Per altre informazioni, vedere gli articoli seguenti:
- Acquisire familiarità con i [flussi di chiamate](../call-flows.md) generali
- Informazioni sui [tipi di chiamate](../voice-video-calling/about-call-types.md)
- [Pianificare la soluzione PSTN](../telephony-sms/plan-solution.md)
