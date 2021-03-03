---
ms.openlocfilehash: 0fd97fe0eebb23130513409698b75d2ee7b98a6f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657630"
---
## <a name="add-managed-identity-to-your-communication-services-solution-js"></a>Aggiungere l'identità gestita alla soluzione di comunicazione dei servizi (JS)

### <a name="install-the-client-library-packages"></a>Installare i pacchetti della libreria client

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="use-the-client-library-packages"></a>Usare i pacchetti della libreria client

Aggiungere le `import` direttive seguenti al codice per usare le librerie di identità di Azure e client di archiviazione di Azure.

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { SmsClient } from "@azure/communication-sms";
```

Gli esempi seguenti usano [DefaultAzureCredential](/javascript/api/azure.identity.defaultazurecredential). Questa credenziale è adatta per ambienti di produzione e di sviluppo.

Per registrare l'applicazione nell'ambiente di sviluppo e configurare le variabili di ambiente, vedere [autorizzare l'accesso con l'identità gestita](../managed-identity-from-cli.md)  

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Creare un'identità ed emettere un token con identità gestita

Nell'esempio di codice seguente viene illustrato come creare un oggetto client del servizio con identità gestita, quindi utilizzare il client per emettere un token per un nuovo utente:

```JavaScript
export async function createIdentityAndIssueToken(resourceEndpoint: string): Promise<CommunicationUserToken> {
     let credential = new DefaultAzureCredential();
     const client = new CommunicationIdentityClient(resourceEndpoint, credential);
     return await client.createUserWithToken(["chat"]);
}
```

### <a name="send-an-sms-with-managed-identity"></a>Inviare un SMS con identità gestita

Nell'esempio di codice seguente viene illustrato come creare un oggetto client del servizio con identità gestita, quindi utilizzare il client per inviare un messaggio SMS:

```JavaScript
export async function sendSms(resourceEndpoint: string, fromNumber: any, toNumber: any, message: string) {
     let credential = new DefaultAzureCredential();
     const smsClient = new SmsClient(resourceEndpoint, credential);
     const sendRequest: SendRequest = { 
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

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sull'autenticazione](../concepts/authentication.md)

Può essere utile vedere anche gli articoli seguenti:

- [Altre informazioni sul controllo degli accessi in base al ruolo di Azure](../../../../articles/role-based-access-control/index.yml)
- [Altre informazioni su Azure Identity Library per JS](/javascript/api/overview/azure/identity-readme)
- [Creazione dei token di accesso utente](../../quickstarts/access-tokens.md)
- [Inviare SMS](../../quickstarts/telephony-sms/send.md)
- [Altre informazioni sugli SMS](../../concepts/telephony-sms/concepts.md)

