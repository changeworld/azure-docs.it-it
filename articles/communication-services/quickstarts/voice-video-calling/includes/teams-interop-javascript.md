---
title: 'Avvio rapido: Partecipare a una riunione di Teams'
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: fa7f279e13e5dbb59af7eb3a7939be1edf0f7d5d
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551386"
---
## <a name="prerequisites"></a>Prerequisiti

- Un'[app per le chiamate di Servizi di comunicazione](../getting-started-with-calling.md) funzionante.
- Una [distribuzione di Teams](/deployoffice/teams-install).


## <a name="add-the-teams-ui-controls"></a>Aggiungere i controlli dell'interfaccia utente di Teams

Sostituire il codice in index.html con il frammento di codice seguente.
La casella di testo verrà usata per accedere al contesto della riunione di Teams e il pulsante per partecipare alla riunione specificata:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - Calling Sample</title>
</head>
<body>
    <h4>Azure Communication Services</h4>
    <h1>Teams meeting join quickstart</h1>
    <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
    <div>
        <button id="join-meeting-button" type="button" disabled="false">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
    </div>
    <script src="./bundle.js"></script>
</body>

</html>
```

## <a name="enable-the-teams-ui-controls"></a>Abilitare i controlli dell'interfaccia utente di Teams

Sostituire il contenuto del file client.js con il frammento di codice seguente.

```javascript
import { CallClient } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'ACS user'});
    teamsMeetingJoinButton.disabled = false;
}
init();

hangUpButton.addEventListener("click", async () => {
    // end the current call
    await call.hangUp();
  
    // toggle button states
    hangUpButton.disabled = true;
    teamsMeetingJoinButton.disabled = false;
    callStateElement.innerText = '-';
  });

teamsMeetingJoinButton.addEventListener("click", () => {    
    // join with meeting link
    call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
    call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
    })
    // toggle button states
    hangUpButton.disabled = false;
    teamsMeetingJoinButton.disabled = true;
});
```

## <a name="get-the-teams-meeting-link"></a>Ottenere il collegamento alla riunione di Teams

Il collegamento alla riunione di Teams può essere recuperato usando le API Graph. Questa operazione è descritta in dettaglio nella [documentazione di Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta).
L'SDK per le chiamate di Servizi di comunicazione accetta un collegamento alla riunione di Teams completo. Questo collegamento viene restituito come parte della risorsa `onlineMeeting`, accessibile nella [proprietà `joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta). È anche possibile ottenere le informazioni sulla riunione necessarie dall'URL di **Partecipa alla riunione** nell'invito alla riunione di Teams stesso.

## <a name="run-the-code"></a>Eseguire il codice

Gli utenti di Webpack possono usare `webpack-dev-server` per compilare ed eseguire l'app. Eseguire il comando seguente per aggregare l'host dell'applicazione in un server Web locale:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Aprire il browser e passare a http://localhost:8080/. Dovrebbe essere visualizzata la seguente schermata:

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="Screenshot dell'applicazione JavaScript completata.":::

Inserire il contesto di Teams nella casella di testo e premere il pulsante *Partecipa alla riunione di Teams* per partecipare alla riunione di Teams dall'interno dell'applicazione di Servizi di comunicazione.
