---
ms.openlocfilehash: 23c64cc91378be605481eb554af2178100df3508
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629363"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Versioni di Active LTS e Maintenance LTS [Node.js](https://nodejs.org/)(versioni consigliate 8.11.1 e 10.14.1).
- Una risorsa attiva di Servizi di comunicazione e una stringa di connessione. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

- In un terminale o una finestra di comando eseguire `node --version` per verificare che Node.js sia installato.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-nodejs-application"></a>Creare una nuova applicazione Node.js

Aprire prima di tutto il terminale o la finestra di comando per creare una nuova directory per l'app e passare a tale directory.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

Eseguire `npm init -y` per creare un file **package.json** con le impostazioni predefinite.

```console
npm init -y
```

Creare un file denominato **phone-numbers-quickstart.js** nella radice della directory appena creata. Aggiungere il frammento di codice seguente:

```javascript
async function main() {
    // quickstart code will here
}

main();
```

### <a name="install-the-package"></a>Installare il pacchetto

Usare il `npm install` comando per installare la libreria client dei numeri di telefono dei servizi di comunicazione di Azure per JavaScript.

```console
npm install @azure/communication-phone-numbers --save
```

L' `--save` opzione aggiunge la libreria come dipendenza nella **package.jssu** file.

## <a name="authenticate-the-client"></a>Autenticare il client

Importare **PhoneNumbersClient** dalla libreria client e crearne un'istanza con la stringa di connessione. Il codice seguente recupera la stringa di connessione per la risorsa da una variabile di ambiente denominata `COMMUNICATION_SERVICES_CONNECTION_STRING`. Vedere come [gestire la stringa di connessione della risorsa](../../create-communication-resource.md#store-your-connection-string).

Aggiungere il codice seguente all'inizio della **phone-numbers-quickstart.js**:

```javascript
const { PhoneNumbersClient } = require('@azure/communication-phone-numbers');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the phone numbers client
const phoneNumbersClient = new PhoneNumbersClient(connectionString);
```

## <a name="manage-phone-numbers"></a>Gestire i numeri di telefono

### <a name="search-for-available-phone-numbers"></a>Cerca i numeri di telefono disponibili

Per acquistare i numeri di telefono, è necessario innanzitutto cercare i numeri di telefono disponibili. Per cercare i numeri di telefono, fornire il prefisso, il tipo di assegnazione, le [funzionalità del numero](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)di telefono, il tipo di [numero di telefono](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)e la quantità. Si noti che per il tipo di numero di telefono con numero verde, l'indicazione del prefisso è facoltativa.

Aggiungere il frammento di codice seguente alla `main` funzione:

```javascript
/**
 * Search for Available Phone Number
 */

// Create search request
const searchRequest = {
    countryCode: "US",
    phoneNumberType: "tollFree",
    assignmentType: "application",
    capabilities: {
      sms: "outbound",
      calling: "none"
    },
    areaCode: "833",
    quantity: 1
  };

const searchPoller = await phoneNumbersClient.beginSearchAvailablePhoneNumbers(searchRequest);

// The search is underway. Wait to receive searchId.
const { searchId, phoneNumbers } = searchPoller.pollUntilDone();
const phoneNumber = phoneNumbers[0];

console.log(`Found phone number: ${phoneNumber}`);
console.log(`searchId: ${searchId}`);
```

### <a name="purchase-phone-number"></a>Numero di telefono dell'acquisto

Il risultato della ricerca dei numeri di telefono è `PhoneNumberSearchResult` . Contiene un oggetto `searchId` che può essere passato all'API dei numeri di acquisto per acquisire i numeri nella ricerca. Si noti che la chiamata all'API per l'acquisto dei numeri di telefono comporta un addebito per l'account Azure.

Aggiungere il frammento di codice seguente alla `main` funzione:

```javascript
/**
 * Purchase Phone Number
 */

const purchasePoller = await phoneNumbersClient.beginPurchasePhoneNumbers(searchId);

// Purchase is underway.
await purchasePoller.pollUntilDone();
console.log(`Successfully purchased ${phoneNumber}`);
```

### <a name="update-phone-number-capabilities"></a>Aggiorna le funzionalità del numero di telefono

Con un numero di telefono ora acquistato, aggiungere il codice seguente per aggiornarne le funzionalità:

```javascript
/**
 * Update Phone Number Capabilities
 */

// Create update request.
// This will update phone number to send and receive sms, but only send calls.
const updateRequest = {
  sms: "inbound+outbound",
  calling: "outbound"
};

const updatePoller = await phoneNumbersClient.beginUpdatePhoneNumberCapabilities(
  phoneNumber,
  updateRequest
);

// Update is underway.
await updatePoller.pollUntilDone();
console.log("Phone number updated successfully.");
```

### <a name="get-purchased-phone-numbers"></a>Ricevi i numeri di telefono acquistati

Dopo un numero di acquisto, è possibile recuperarlo dal client. Aggiungere il codice seguente alla `main` funzione per ottenere il numero di telefono appena acquistato:

```javascript
/**
 * Get Purchased Phone Number
 */

const { capabilities } = await phoneNumbersClient.getPurchasedPhoneNumber(phoneNumber);
console.log(`These capabilities: ${capabilities}, should be the same as these: ${updateRequest}.`);
```

È anche possibile recuperare tutti i numeri di telefono acquistati.

```javascript
const phoneNumbers = await phoneNumbersClient.listPurchasedPhoneNumbers();

for await (const purchasedPhoneNumber of phoneNumbers) {
  console.log(`Phone number: ${purchasedPhoneNumber.phoneNumber}, country code: ${purchasedPhoneNumber.countryCode}.`);
}
```

### <a name="release-phone-number"></a>Numero di telefono versione

È ora possibile rilasciare il numero di telefono acquistato. Aggiungere il frammento di codice seguente alla `main` funzione:

```javascript
/**
 * Release Purchased Phone Number
 */

const releasePoller = await client.beginReleasePhoneNumber(phoneNumber);

// Release is underway.
await releasePoller.pollUntilDone();
console.log("Successfully release phone number.");
```

## <a name="run-the-code"></a>Eseguire il codice

Usare il `node` comando per eseguire il codice aggiunto al file di **phone-numbers-quickstart.js** .

```console
node phone-numbers-quickstart.js
```