---
title: Panoramica della libreria client per le chiamate di Servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Fornisce una panoramica della libreria client per le chiamate.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 110607e9da68cf131a43651b97c599873ce33cb8
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495267"
---
# <a name="calling-client-library-overview"></a>Panoramica della libreria client per le chiamate

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Sono disponibili due famiglie separate di librerie client per le chiamate: per i *client* e per i *servizi*. Le librerie client attualmente disponibili sono destinate all'esperienza degli utenti finali: siti Web e app native.

Le librerie client del servizio non sono ancora disponibili e forniscono l'accesso ai piani dati vocali e video non elaborati, adatti per l'integrazione con i bot e altri servizi.

## <a name="calling-client-library-capabilities"></a>Funzionalità della libreria client per le chiamate

L'elenco seguente presenta il set di funzionalità attualmente disponibili nelle librerie client per le chiamate di Servizi di comunicazione di Azure.

| Gruppo di funzionalità | Funzionalità                                                                                                          | JS  | Java (Android) | Objective-C (iOS)
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Funzionalità principali | Inserire una chiamata uno-a-uno tra due utenti                                                                           | ✔️   | ✔️            | ✔️
|                   | Inserire una chiamata di gruppo con più di due utenti (fino a 350 utenti)                                                       | ✔️   | ✔️            | ✔️
|                   | Alzare di livello una chiamata uno-a-uno con due utenti in una chiamata di gruppo con più di due utenti                                 | ✔️   | ✔️            | ✔️
|                   | Partecipare a una chiamata di gruppo dopo che è stata avviata                                                                              | ✔️   | ✔️            | ✔️
|                   | Invitare un altro utente VoIP a partecipare a una chiamata di gruppo in corso                                                       | ✔️   | ✔️            | ✔️
|  Controllo della chiamata intermedia | Attivare/Disattivare il video                                                                                              | ✔️   | ✔️            | ✔️ 
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
| Gestione dei dispositivi | Richiedi l'autorizzazione per l'uso di audio e/o video                                                                       | ✔️   | ✔️            | ✔️
|                   | Ottenere l'elenco di fotocamere                                                                                                     | ✔️   | ✔️            | ✔️ 
|                   | Imposta fotocamera                                                                                                          | ✔️   | ✔️            | ✔️
|                   | Ottenere la fotocamera selezionata                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Ottenere l'elenco di microfoni                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Imposta microfono                                                                                                      | ✔️   | ✔️            | ✔️
|                   | Ottieni microfono selezionato                                                                                             | ✔️   | ✔️            | ✔️
|                   | Ottenere l'elenco di altoparlanti                                                                                                   | ✔️   | ✔️            | ✔️
|                   | Imposta altoparlante                                                                                                         | ✔️   | ✔️            | ✔️
|                   | Ottieni altoparlante selezionato                                                                                                | ✔️   | ✔️            | ✔️
| Rendering video   | Eseguire il rendering di un singolo video in molte posizioni (fotocamera locale o flusso remoto)                                                  | ✔️   | ✔️            | ✔️
|                   | Imposta/Aggiorna modalità di ridimensionamento                                                                                           | ✔️   | ✔️            | ✔️ 
|                   | Rendering del flusso video remoto                                                                                          | ✔️   | ✔️            | ✔️



## <a name="javascript-calling-client-library-support-by-os-and-browser"></a>Browser e sistemi operativi supportati dalla libreria client per le chiamate JavaScript

La tabella seguente rappresenta il set di browser supportati attualmente disponibili. Sono supportate le ultime tre versioni del browser, se non diversamente specificato.

|                                  | Chrome | Safari  | Bordo (cromo) | 
| -------------------------------- | -------| ------  | --------------  |
| Android                          |  ✔️    | ❌     | ❌             |
| iOS                              |  ❌    | ✔️**** | ❌             |
| macOS * * *                         |  ✔️    | ✔️**   | ❌             |
| Windows * * *                       |  ✔️    | ❌     | ✔️             |
| Ubuntu/Linux                     |  ✔️    | ❌     | ❌             |

* Le versioni di Safari 13.1 + sono supportate, 1:1 le chiamate non sono supportate in Safari. 

* * Safari 14 +/macOS 11 + necessario per il supporto video in uscita. 

La condivisione dello schermo in uscita è supportata solo sulle piattaforme desktop (Windows, macOS e Linux), indipendentemente dalla versione del browser e non è supportata in nessuna piattaforma per dispositivi mobili (Android, iOS, iPad e Tablet).

Un'app iOS in Safari non può enumerare/selezionare i dispositivi MIC e speaker (ad esempio, Bluetooth); si tratta di una limitazione del sistema operativo e c'è sempre un solo dispositivo.


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

## <a name="calling-client-library-streaming-support"></a>Supporto streaming per la libreria client per le chiamate
La libreria client per le chiamate di Servizi di comunicazione supporta le configurazioni di streaming seguenti:

|           |Web | Android/iOS|
|-----------|----|------------|
|**Numero di flussi in uscita che possono essere inviati contemporaneamente** |1 video + 1 condivisione dello schermo | 1 video + 1 condivisione dello schermo|
|**Numero di flussi in entrata di cui è possibile eseguire il rendering simultaneamente** |1 video + 1 condivisione dello schermo| 6 video + 1 condivisione dello schermo |


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione alle chiamate](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Per altre informazioni, vedere gli articoli seguenti:
- Acquisire familiarità con i [flussi di chiamate](../call-flows.md) generali
- Informazioni sui [tipi di chiamate](../voice-video-calling/about-call-types.md)
- [Pianificare la soluzione PSTN](../telephony-sms/plan-solution.md)
