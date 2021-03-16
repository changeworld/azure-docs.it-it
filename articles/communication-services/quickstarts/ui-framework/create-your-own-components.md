---
title: Creare un componente del Framework dell'interfaccia utente personalizzato
titleSuffix: An Azure Communication Services quickstart
description: In questa Guida introduttiva si apprenderà come creare un componente personalizzato compatibile con il Framework dell'interfaccia utente
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 093fcfd95d291d959ed49cc39a227a99f14a0383
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488245"
---
# <a name="quickstart-create-your-own-ui-framework-component"></a>Guida introduttiva: creare un componente del Framework dell'interfaccia utente

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Inizia a usare i servizi di comunicazione di Azure usando il Framework dell'interfaccia utente per integrare rapidamente le esperienze di comunicazione nelle tue applicazioni.

In questa Guida introduttiva si apprenderà come creare componenti personalizzati usando l'interfaccia di stato predefinita offerta da UI Framework. Questo approccio è ideale per gli sviluppatori che necessitano di una maggiore personalizzazione e vogliono usare le proprie risorse di progettazione per l'esperienza. 

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Versioni LTS attive e manutenzione LTS (nodo 12 consigliato).
- Una risorsa attiva di Servizi di comunicazione. [Creare una risorsa di Servizi di comunicazione](./../create-communication-resource.md).
- Un token di accesso utente per creare un'istanza del client di chiamata. Vedere come [creare e gestire i token di accesso utente](./../access-tokens.md).

Il Framework dell'interfaccia utente richiede la configurazione di un ambiente React. Questa operazione verrà eseguita in seguito. Se si dispone già di un'app REACT, è possibile ignorare questa sezione.

### <a name="set-up-react-app"></a>Configurare l'app React

Per questa Guida introduttiva verrà usato il modello create-React-app. Per altre informazioni, vedere [Introduzione a React](https://reactjs.org/docs/create-a-new-react-app.html) .

```console

npx create-react-app my-app

cd my-app

```

Al termine di questo processo, è necessario disporre di un'applicazione completa all'interno della cartella `my-app` . Per questa Guida introduttiva verranno modificati i file all'interno della `src` cartella.

### <a name="install-the-package"></a>Installare il pacchetto

Usare il comando `npm install` per installare la libreria client Chiamate di Servizi di comunicazione di Azure per JavaScript. Spostare il tarball fornito (anteprima privata) nella directory My-App.

```console

//For Private Preview install tarball

npm install --save ./{path for tarball}

```

L'opzione `--save` elenca la libreria come dipendenza nel file **package.json**.

### <a name="run-create-react-app"></a>Esegui Crea app React

Testare l'installazione di Crea app React eseguendo:

```console

npm run start   

```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client dell'interfaccia utente di servizi di comunicazione di Azure:

| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| Provider| Provider interfaccia utente Fluent che consente agli sviluppatori di modificare i componenti dell'interfaccia utente Fluent sottostanti|
| CallingProvider| Chiamata del provider per creare un'istanza di una chiamata. Obbligatorio per aggiungere componenti di base|
| ChatProvider | Provider di chat per creare un'istanza di un thread di chat. Obbligatorio per aggiungere componenti di base|
| connectFuncsToContext | Metodo per connettere i componenti del Framework dell'interfaccia utente ai provider sottostanti tramite Mapper |
| MapToChatMessageProps | Mapper del livello dati del messaggio di chat che fornisce componenti con le prop del messaggio di chat |


## <a name="initialize-chat-providers-using-azure-communication-services-credentials"></a>Inizializzare i provider di chat usando le credenziali di servizi di comunicazione Azure

Per questa Guida introduttiva si userà la chat come esempio, per altre informazioni sulla chiamata a, vedere Guida introduttiva ai [componenti di base](./get-started-with-components.md) e [componenti compositi](./get-started-with-composites.md).

Passare alla `src` cartella all'interno di `my-app` e cercare il file `app.js` . Qui verrà rilasciato il codice seguente per inizializzare il provider di chat. Questo provider è responsabile della gestione del contesto delle chiamate e delle esperienze di chat. Per inizializzare i componenti, è necessario un token di accesso recuperato dai servizi di comunicazione di Azure. Per informazioni dettagliate su come ottenere un token di accesso, vedere: [creare e gestire i token di accesso](./../access-tokens.md).

I componenti del Framework dell'interfaccia utente seguono la stessa architettura generale per il resto del servizio. I componenti non generano token di accesso, ID gruppo o ID thread. Questi elementi provengono da servizi che passano attraverso i passaggi appropriati per generare questi ID e passarli all'applicazione client. Per ulteriori informazioni, vedere [architettura del server client](./../../concepts/client-and-server-architecture.md).

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"

function App(props) {

  return (
    <ChatProvider
    token={/*Insert the Azure Communication Services access token*/}
    userId={/*Insert the Azure Communication Services user id*/}
    displayName={/*Insert Display Name to be used for the user*/}
    threadId={/*Insert id for group chat thread to be joined*/}
    endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
    refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
    >
        //  Add Chat Components Here
    </ChatProvider>
  );
}

export default App;

```

Una volta inizializzato, questo provider consente di creare un layout personalizzato usando il componente del Framework dell'interfaccia utente e qualsiasi logica di layout aggiuntiva. Il provider si occupa dell'inizializzazione di tutta la logica sottostante e della corretta connessione dei diversi componenti. Successivamente, verrà creato un componente personalizzato usando i mapper del Framework dell'interfaccia utente per connettersi al provider di chat.


## <a name="create-a-custom-component-using-mappers"></a>Creare un componente personalizzato usando i Mapper

Si inizierà creando un nuovo file denominato in `SimpleChatThread.js` cui si creerà il componente. Si inizierà importando i componenti del Framework dell'interfaccia utente necessari. In questo caso, si userà il codice HTML predefinito e si reagirà a creare un componente completamente personalizzato per un semplice thread di chat. Utilizzando il `connectFuncsToContext` metodo, si utilizzerà il `MapToChatMessageProps` Mapper per eseguire il mapping delle proprietà ai  `SimpleChatThread` componenti personalizzati. Queste Props consentono di accedere ai messaggi di chat inviati e ricevuti per popolarli nel semplice thread.

`SimpleChatThread.js`
```javascript

import {connectFuncsToContext, MapToChatMessageProps} from "@azure/acs-ui-sdk"

function SimpleChatThread(props) {

    return (
        <div>
            {props.chatMessages?.map((message) => (
                <div key={message.id ?? message.clientMessageId}> {`${message.senderDisplayName}: ${message.content}`}</div>
            ))}
        </div>
    );
}

export default connectFuncsToContext(SimpleChatThread, MapToChatMessageProps);

```

## <a name="add-your-custom-component-to-your-application"></a>Aggiungere il componente personalizzato all'applicazione

Ora che il componente personalizzato è pronto, verrà importato e aggiunto al layout.

```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import SimpleChatThread from "./SimpleChatThread"

function App(props) {

  return (
        <ChatProvider ... >
            <SimpleChatThread />
        </ChatProvider>
  );
}

export default App;

```

## <a name="run-quickstart"></a>Esegui avvio rapido

Per eseguire il codice precedente, usare il comando:

```console

npm run start   

```

Per testare completamente le funzionalità, sarà necessario un secondo client con funzionalità di chat per inviare messaggi che verranno ricevuti dal semplice thread di chat. Vedere l'esempio di [chiamata di Hero](./../../samples/calling-hero-sample.md) e [chat Hero](./../../samples/chat-hero-sample.md) come possibili opzioni.

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire e rimuovere una sottoscrizione di Servizi di comunicazione, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate. Vedere altre informazioni sulla [pulizia delle risorse](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Prova componenti compositi del Framework dell'interfaccia utente](./get-started-with-composites.md)

Per altre informazioni, vedere le risorse seguenti:
- [Panoramica del Framework dell'interfaccia utente](../../concepts/ui-framework/ui-sdk-overview.md)
- [Funzionalità del Framework dell'interfaccia utente](./../../concepts/ui-framework/ui-sdk-features.md)
- [Guida introduttiva ai componenti di base dell'interfaccia utente](./get-started-with-components.md)
