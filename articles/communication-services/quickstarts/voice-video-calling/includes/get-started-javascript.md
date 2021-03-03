---
title: "Avvio rapido: Aggiungere chiamate VoIP a un'app Web con Servizi di comunicazione di Azure"
description: Questa esercitazione illustra come usare la libreria client Chiamate di Servizi di comunicazione di Azure per JavaScript
author: ddematheu
ms.author: nimag
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: d27a79e180a0219773a3094fb85f842773d75183
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656617"
---
Questa guida di avvio rapido illustra come avviare una chiamata con la libreria client Chiamate di Servizi di comunicazione di Azure per JavaScript.
Questo documento fa riferimento ai tipi nella versione 1.0.0-beta. 5 della libreria chiamante.

> [!NOTE]
> Questo documento usa la versione 1.0.0-beta. 6 della libreria client chiamante.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/), versioni Active LTS e Maintenance LTS (8.11.1 e 10.14.1 consigliate).
- Una risorsa attiva di Servizi di comunicazione. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un token di accesso utente per creare un'istanza del client di chiamata. Vedere come [creare e gestire i token di accesso utente](../../access-tokens.md).


[!INCLUDE [Calling with JavaScript](./get-started-javascript-setup.md)]

Ecco il codice:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Nella directory radice del progetto creare un file denominato **client.js** in cui contenere la logica dell'applicazione per questa guida di avvio rapido. Aggiungere il codice seguente per importare il client delle chiamate e ottenere riferimenti agli elementi DOM in modo da collegare la logica di business. 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client Chiamate di Servizi di comunicazione di Azure:

| Nome                             | Descrizione                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient è il principale punto di ingresso alla libreria client Chiamate.                                                                       |
| CallAgent                        | CallAgent si usa per avviare e gestire le chiamate.                                                                                            |
| AzureCommunicationTokenCredential | La classe AzureCommunicationTokenCredential implementa l'interfaccia CommunicationTokenCredential utilizzata per creare un'istanza di CallAgent. |


## <a name="authenticate-the-client"></a>Autenticare il client

È necessario sostituire `<USER_ACCESS_TOKEN>` con un token di accesso utente valido per la risorsa. Se non è disponibile un token, vedere la documentazione dei [token di accesso utente](../../access-tokens.md). Usando `CallClient`, inizializzare un'istanza di `CallAgent` con `CommunicationTokenCredential` per consentire l'effettuazione e la ricezione di chiamate. Aggiungere il codice seguente a **client.js**:

```javascript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential);
    callButton.disabled = false;
}
init();
```

## <a name="start-a-call"></a>Avviare una chiamata

Aggiungere un gestore eventi per avviare una chiamata quando si fa clic su `callButton`:

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ communicationUserId: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>Terminare una chiamata

Aggiungere un listener di eventi per terminare la chiamata corrente quando si fa clic su `hangUpButton`:

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
});
```

La proprietà `forEveryone` termina la chiamata per tutti i partecipanti.

## <a name="run-the-code"></a>Eseguire il codice

Usare `webpack-dev-server` per compilare ed eseguire l'app. Eseguire il comando seguente per aggregare l'host dell'applicazione in un server Web locale:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Aprire il browser e passare a http://localhost:8080/. Dovrebbe essere visualizzata la seguente schermata:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Screenshot dell'applicazione JavaScript completata.":::

È possibile effettuare una chiamata VoIP in uscita fornendo un ID utente nel campo di testo e facendo clic sul pulsante **Start Call** (Avvia chiamata). Con la chiamata a `8:echo123` ci si connette a un Echo Bot, la scelta ideale per iniziare e per verificare che i dispositivi audio funzionino.
