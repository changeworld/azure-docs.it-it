---
title: Esercitazione-firmare e inviare richieste all'API SMS di ACS con l'agente di pubblicazione
titleSuffix: An Azure Communication Services tutorial
description: Informazioni su come firmare e inviare richieste per ACS con il post per inviare un messaggio SMS.
author: ProbablePrime
services: azure-communication-services
ms.author: rifox
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: c21d8696c0c4edd743aab6cbe7866d27ce46a314
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492609"
---
# <a name="tutorial-sign-and-make-requests-with-postman"></a>Esercitazione: firmare e creare richieste con il post
In questa esercitazione verrà configurata e usata l'opzione postazione per effettuare una richiesta per i servizi di comunicazione di Azure (ACS) tramite HTTP. Al termine di questa esercitazione, è stato inviato un messaggio SMS usando ACS e l'agente di pubblicazione e sarà possibile usare il post per esplorare altre API all'interno di ACS.

In questa esercitazione verranno illustrate le operazioni seguenti:
> [!div class="checklist"]
> * Download del post
> * Impostazione di post per firmare le richieste HTTP
> * Esecuzione di una richiesta per l'API SMS di ACS per l'invio di un messaggio.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. Per informazioni dettagliate, vedere [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). L'account gratuito ti offre $200 di crediti di Azure per provare qualsiasi combinazione di servizi.
- Una stringa di connessione e una risorsa attiva di Servizi di comunicazione. [Informazioni su come creare una risorsa di servizi di comunicazione](../quickstarts/create-communication-resource.md).
- Un numero di telefono ACS che può inviare messaggi SMS. per ottenere [un numero di telefono](../quickstarts/telephony-sms/get-phone-number.md) , vedere il messaggio.

## <a name="downloading-and-installing-postman"></a>Download e installazione del post

Postazione, è un'applicazione desktop in grado di effettuare richieste API per qualsiasi API HTTP. Viene in genere usato per il test e l'esplorazione delle API. Verrà scaricata la versione più recente del [Desktop dal sito Web del posto](https://www.postman.com/downloads/). Il servizio di pubblicazione dispone di versioni per Windows, Mac e Linux, quindi è necessario scaricare la versione appropriata per il sistema operativo in uso. Una volta scaricato, aprire l'applicazione. Verrà visualizzata una schermata Start in cui viene chiesto di eseguire l'accesso o di creare un account di posta. La creazione di un account è facoltativa e può essere ignorata facendo clic sul collegamento "Ignora e vai all'app". La creazione di un account salverà le impostazioni della richiesta dell'API in postazione, che può quindi consentire all'utente di selezionare le richieste in altri computer.

:::image type="content" source="media/postman/create-account-or-skip.png" alt-text="Schermata iniziale del post che mostra la possibilità di creare un account o di ignorare e passare all'app.":::

Una volta creato un account o se ne è stata ignorata la creazione, verrà visualizzata la finestra principale del post.

## <a name="creating-and-configuring-a-postman-collection"></a>Creazione e configurazione di una raccolta di post

Postazione, può organizzare le richieste in molti modi. Ai fini di questa esercitazione. Verrà creata una raccolta di post. A tale scopo, selezionare il pulsante raccolte sul lato sinistro dell'applicazione:

:::image type="content" source="media/postman/collections-tab.png" alt-text="Schermata principale del posting con la scheda raccolte evidenziata.":::

Una volta selezionato, fare clic su "Crea nuova raccolta" per avviare il processo di creazione della raccolta. Viene aperta una nuova scheda nell'area centrale del post. Assegnare alla raccolta un nome qualsiasi. Qui la raccolta è denominata "ACS":

:::image type="content" source="media/postman/acs-collection.png" alt-text="Viene aperto un post con una raccolta ACS e il nome della raccolta evidenziata.":::

Dopo aver creato e denominato la raccolta, è possibile configurarla.

### <a name="adding-collection-variables"></a>Aggiunta di variabili di raccolta

Per gestire l'autenticazione e semplificare le richieste, verranno specificate due variabili di raccolta all'interno della raccolta ACS appena creata. Queste variabili sono disponibili per tutte le richieste all'interno della raccolta ACS. Per iniziare a creare variabili, visitare la scheda Variable's della raccolta.

:::image type="content" source="media/postman/variable-stab.png" alt-text="Postazione con la scheda variabili di una raccolta ACS.":::

Una volta nella scheda raccolta, creare due variabili:
- chiave: questa variabile deve essere una delle chiavi della pagina chiave dei servizi di comunicazione di Azure all'interno del portale di Azure. Ad esempio: `oW...A==`.
- endpoint: questa variabile deve essere l'endpoint dei servizi di comunicazione di Azure dalla pagina chiave. **Assicurarsi di rimuovere la barra finale**. Ad esempio: `https://contoso.communication.azure.com`.

Immettere questi valori nella colonna "valore iniziale" della schermata variabili. Una volta immesso, premere il pulsante "Mantieni tutto" appena sopra la tabella a destra. Una volta configurata correttamente, la schermata di posting dovrebbe avere un aspetto simile al seguente:

:::image type="content" source="media/postman/acs-variables-set.png" alt-text="La configurazione delle variabili di una raccolta ACS è stata eseguita correttamente.":::

È possibile ottenere altre informazioni sulle variabili leggendo [la documentazione di poster](https://learning.postman.com/docs/sending-requests/variables).

### <a name="creating-a-pre-request-script"></a>Creazione di uno script di pre-richiesta

Il passaggio successivo consiste nel creare uno script di pre-richiesta all'interno di un post. Uno script di pre-richiesta è uno script che viene eseguito prima di ogni richiesta in post e può modificare o modificare i parametri di richiesta per conto dell'utente. Questa operazione verrà usata per firmare le richieste HTTP in modo che possano essere autorizzate dai servizi ACS. Per ulteriori informazioni sui requisiti di firma, è possibile [leggere la guida sull'autenticazione](https://docs.microsoft.com/rest/api/communication/authentication).

Questo script verrà creato all'interno della raccolta, in modo che venga eseguito in qualsiasi richiesta all'interno della raccolta. A tale scopo, nella scheda raccolta fare clic sulla sottoscheda "script pre-richiesta".

:::image type="content" source="media/postman/start-pre-request-script.png" alt-text="Il post con uno script di pre-richiesta della raccolta ACS Sub-Tab selezionato.":::

In questa scheda secondaria è possibile creare uno script di pre-richiesta immettendolo nell'area di testo seguente. Potrebbe essere più facile scrivere questo, all'interno di un editor di codice completo, ad esempio [Visual Studio Code](https://code.visualstudio.com/) prima di incollarlo in al termine dell'operazione. In questa esercitazione verranno esaminate tutte le parti dello script. È possibile passare alla fine se si vuole semplicemente copiarla in un post e iniziare. Iniziamo a scrivere lo script.

### <a name="writing-the-pre-request-script"></a>Scrittura dello script di pre-richiesta

La prima cosa da fare è creare una stringa UTC (Coordinated Universal Time) e aggiungerla all'intestazione HTTP "date". Questa stringa viene inoltre archiviata in una variabile per utilizzarla in un secondo momento durante la firma:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});
```

Verrà quindi eseguito l'hashing del corpo della richiesta usando SHA 256 e lo inserirà nell' `x-ms-content-sha256` intestazione. Il posto include alcune [librerie standard](https://learning.postman.com/docs/writing-scripts/script-references/postman-sandbox-api-reference/#using-external-libraries) per l'hashing e la firma a livello globale, pertanto non è necessario installarle o richiederle:

```JavaScript
// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});
```

A questo punto, verrà usata la variabile endpoint specificata in precedenza per discernere il valore per l'intestazione host HTTP. È necessario eseguire questa operazione perché l'intestazione host non è impostata finché non viene elaborato questo script:

```JavaScript
// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');
```

Con queste informazioni create, è ora possibile creare la stringa che verrà firmata per la richiesta HTTP, che è costituita da diversi valori creati in precedenza:

```JavaScript
// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;
```

Infine, è necessario firmare questa stringa usando la chiave ACS e quindi aggiungerla alla richiesta nell' `Authorization` intestazione:

```JavaScript
// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

### <a name="the-final-pre-request-script"></a>Script di pre-richiesta finale

Lo script di pre-richiesta finale dovrebbe avere un aspetto simile al seguente:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});

// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});

// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');

// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;

// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

Immettere o incollare questo script finale nell'area di testo nella scheda script di pre-richiesta:

:::image type="content" source="media/postman/finish-pre-request.png" alt-text="È stato immesso un post con uno script di pre-richiesta della raccolta ACS.":::

Una volta immesso, premere CTRL + S oppure fare clic sul pulsante Salva per salvare lo script nella raccolta. 

:::image type="content" source="media/postman/save-pre-request-script.png" alt-text="Il pulsante Salva script di pre-richiesta del post.":::

## <a name="creating-a-request-in-postman"></a>Creazione di una richiesta in un post

Ora che tutti gli elementi sono configurati, è possibile creare una richiesta ACS nell'ambito del post. Per iniziare, fare clic sull'icona con il segno più (+) accanto alla raccolta ACS:

:::image type="content" source="media/postman/create-request.png" alt-text="Pulsante più del post.":::

Verrà creata una nuova scheda per la richiesta nell'ambito del post. Con la creazione è necessario configurarla. Verrà inviata una richiesta per l'API di invio SMS. Assicurarsi quindi di fare riferimento alla documentazione relativa a [questa API per ottenere assistenza](https://docs.microsoft.com/rest/api/communication/sms/send). Configurare la richiesta di post.

Per iniziare, impostare il tipo di richiesta su `POST` e immettere `{{endpoint}}/sms?api-version=2021-03-07` nel campo URL richiesta. Questo URL usa la variabile creata in precedenza `endpoint` per inviarla automaticamente alla risorsa ACS.

:::image type="content" source="media/postman/post-request-and-url.png" alt-text="Una richiesta post, con il tipo impostato su POST e l'URL impostato correttamente.":::

A questo punto, selezionare la scheda corpo della richiesta e quindi modificare il pulsante di opzione sotto "Raw". A destra è presente un elenco a discesa con il testo "Text", che viene modificato in JSON:

:::image type="content" source="media/postman/postman-json.png" alt-text="Impostazione del corpo della richiesta su RAW e JSON":::

Questo configurerà il Request to Send e riceverà le informazioni in formato JSON.

Nell'area di testo sottostante è necessario immettere il corpo di una richiesta, che deve avere il formato seguente:

```JSON
{
    "from":"<Your ACS Telephone Number>",
    "message":"<The message you'd like to send>",
    "smsRecipients": [
        {
            "to":"<The number you'd like to send the message to>"
        }
    ]
}
```

Per il valore "da", è necessario [ottenere un numero di telefono](../quickstarts/telephony-sms/get-phone-number.md) nel portale ACS come indicato in precedenza. Immetterlo senza spazi e preceduto dal codice paese. Ad esempio: `+15555551234`. Il "messaggio" può essere quello che si vuole inviare, ma `Hello from ACS` è un esempio valido. Il valore "a" deve essere un telefono a cui si ha accesso che può ricevere messaggi SMS. È consigliabile usare il proprio dispositivo mobile.

Una volta immesso, è necessario salvare la richiesta nella raccolta ACS creata in precedenza. In questo modo si assicurerà che selezioni le variabili e lo script di pre-richiesta creati in precedenza. A tale scopo, fare clic sul pulsante "Save" (Salva) in alto a destra nell'area della richiesta.

:::image type="content" source="media/postman/postman-save.png" alt-text="Pulsante Salva per una richiesta post.":::

Verrà visualizzata una finestra di dialogo in cui viene chiesto, cosa si vuole chiamare la richiesta e dove si desidera salvarla. È possibile assegnare un nome qualsiasi, ma assicurarsi di selezionare la raccolta ACS nella parte inferiore della finestra di dialogo:

:::image type="content" source="media/postman/postman-save-to-acs.png" alt-text="La finestra di dialogo per la richiesta di salvataggio del post con la raccolta ACS selezionata.":::

## <a name="sending-a-request"></a>Invio di una richiesta

Ora che tutto è configurato, dovrebbe essere possibile inviare la richiesta e ricevere un messaggio SMS sul telefono. A tale scopo, verificare che sia selezionata la richiesta creata, quindi premere il pulsante "Send" (Invia) a destra:

:::image type="content" source="media/postman/postman-send.png" alt-text="Una richiesta post, con il pulsante Invia evidenziato.":::

Se tutto è andato bene, dovrebbe essere visualizzata la risposta da ACS, che dovrebbe essere 202 codice di stato:

:::image type="content" source="media/postman/postman-202.png" alt-text="Una richiesta post, inviata correttamente con un codice di stato 202.":::

Il telefono cellulare, che possiede il numero specificato nel valore "a", dovrebbe avere anche ricevuto un messaggio SMS. A questo punto è stato configurato un post di lavoro, che può comunicare con i servizi ACS e inviare messaggi SMS.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esplora le API ACS](https://docs.microsoft.com/rest/api/communication/) 
>  [Altre informazioni sull'autenticazione](https://docs.microsoft.com/rest/api/communication/authentication) 
>  [Scopri di più su postazione](https://learning.postman.com/)

Potrebbe inoltre essere necessario:

- [Aggiungere una chat all'app](../quickstarts/chat/get-started.md)
- [Creare token di accesso utente](../quickstarts/access-tokens.md)
- [Informazioni sull'architettura client e server](../concepts/client-and-server-architecture.md)
- [Informazioni sull'autenticazione](../concepts/authentication.md)