---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: nikuklic
ms.openlocfilehash: 2f884a09e6b51b1c72034a62eaea601a4e69ecd2
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024295"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Una risorsa di Servizi di comunicazione distribuita. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un numero di telefono acquisito nella risorsa di Servizi di comunicazione. [Come ottenere un numero di telefono](../../telephony-sms/get-phone-number.md).
- Un `User Access Token` per abilitare il client di chiamata. Per altre informazioni, vedere [come ottenere un `User Access Token`](../../access-tokens.md)


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
      id="callee-phone-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 230px;"
    />
    <div>
      <button id="call-phone-button" type="button">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-phone-button" type="button" disabled="true">
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
import { AzureCommunicationUserCredential } from '@azure/communication-common';

let call;
let callAgent;

const calleePhoneInput = document.getElementById("callee-phone-input");
const callPhoneButton = document.getElementById("call-phone-button");
const hangUpPhoneButton = document.getElementById("hang-up-phone-button");

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationUserCredential('your-token-here');
    callAgent = await callClient.createCallAgent(tokenCredential);
  //  callButton.disabled = false;
}

init();

```

## <a name="start-a-call-to-phone"></a>Avviare una chiamata al telefono

Specificare il numero di telefono acquisito nella risorsa di Servizi di comunicazione, che verrà usato per avviare la chiamata:
> [!WARNING]
> Si noti che i numeri di telefono devono essere specificati nel formato standard internazionale E.164, ad esempio +12223334444.

Aggiungere un gestore dell'evento per avviare una chiamata al numero di telefono specificato facendo clic su `callPhoneButton`:


```javascript
callPhoneButton.addEventListener("click", () => {
  // start a call to phone
  const phoneToCall = calleePhoneInput.value;
  call = callAgent.call(
    [{phoneNumber: phoneToCall}], { alternateCallerId: {phoneNumber: 'YOUR AZURE REGISTERED PHONE NUMBER HERE: +12223334444'}
  });
  // toggle button states
  hangUpPhoneButton.disabled = false;
  callPhoneButton.disabled = true;
});
```

## <a name="end-a-call-to-phone"></a>Terminare una chiamata al telefono

Aggiungere un listener di eventi per terminare la chiamata corrente quando si fa clic su `hangUpPhoneButton`:

```javascript
hangUpPhoneButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({
    forEveryone: true
  });

  // toggle button states
  hangUpPhoneButton.disabled = true;
  callPhoneButton.disabled = false;
});
```

La proprietà `forEveryone` termina la chiamata per tutti i partecipanti.

## <a name="run-the-code"></a>Eseguire il codice

Usare `webpack-dev-server` per compilare ed eseguire l'app. Eseguire il comando seguente per aggregare l'host dell'applicazione in un server Web locale:


```console
npx webpack-dev-server --entry ./client.js --output bundle.js
```

Aprire il browser e passare a `http://localhost:8080/`. Dovrebbe essere visualizzata la seguente schermata:

:::image type="content" source="../media/javascript/pstn-calling-javascript-app.png" alt-text="Screenshot dell'applicazione JavaScript completata.":::

È possibile effettuare una chiamata a un numero di telefono reale specificandolo nel campo di testo aggiunto e facendo clic sul pulsante **Start Phone Call** (Avvia chiamata telefonica).

> [!WARNING]
> Si noti che i numeri di telefono devono essere specificati nel formato standard internazionale E.164, ad esempio +12223334444.
