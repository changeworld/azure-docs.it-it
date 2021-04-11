---
ms.openlocfilehash: a3771a21914831f249696fc3d733c5ea935c2c7e
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251497"
---
## <a name="add-managed-identity-to-your-communication-services-solution-js"></a>Aggiungere l'identità gestita alla soluzione di comunicazione dei servizi (JS)

### <a name="install-the-sdk-packages"></a>Installare i pacchetti SDK

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="use-the-sdk-packages"></a>Usare i pacchetti SDK

Aggiungere le `import` direttive seguenti al codice per usare l'identità di Azure e gli SDK di archiviazione di Azure.

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CommunicationIdentityClient, CommunicationUserToken } from "@azure/communication-identity";
import { SmsClient, SmsSendRequest } from "@azure/communication-sms";
```

Gli esempi seguenti usano [DefaultAzureCredential](/javascript/api/@azure/identity/defaultazurecredential). Questa credenziale è adatta per ambienti di produzione e di sviluppo.

Per un modo semplice per passare all'uso dell'autenticazione di identità gestita, vedere [autorizzare l'accesso con identità gestita](../managed-identity-from-cli.md)

Per un'analisi più approfondita del funzionamento dell'oggetto DefaultAzureCredential e del modo in cui è possibile usarlo in modi non specificati in questa Guida introduttiva, vedere [libreria client di identità di Azure per JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Creare un'identità ed emettere un token con identità gestita

Nell'esempio di codice seguente viene illustrato come creare un oggetto client del servizio con identità gestita, quindi utilizzare il client per emettere un token per un nuovo utente:

```JavaScript
export async function createIdentityAndIssueToken(resourceEndpoint: string): Promise<CommunicationUserToken> {
     let credential = new DefaultAzureCredential();
     const client = new CommunicationIdentityClient(resourceEndpoint, credential);
     return await client.createUserAndToken(["chat"]);
}
```

### <a name="send-an-sms-with-managed-identity"></a>Inviare un SMS con identità gestita

Nell'esempio di codice seguente viene illustrato come creare un oggetto client del servizio con identità gestita, quindi utilizzare il client per inviare un messaggio SMS:

```JavaScript
export async function sendSms(resourceEndpoint: string, fromNumber: any, toNumber: any, message: string) {
     let credential = new DefaultAzureCredential();
     const smsClient = new SmsClient(resourceEndpoint, credential);
     const sendRequest: SmsSendRequest = {
          from: fromNumber,
          to: [toNumber],
          message: message
     };
     const response = await smsClient.send(
          sendRequest,
          {} //Optional SendOptions
          );
}
```