---
title: Introduzione ai componenti di base del Framework dell'interfaccia utente di servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services quickstart
description: In questa Guida introduttiva si apprenderà come iniziare a usare i componenti di base di UI Framework
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 336059007cfca40a74ad5a4395c6f9a59215bb58
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105825782"
---
# <a name="quickstart-get-started-with-ui-framework-base-components"></a>Guida introduttiva: Introduzione ai componenti di base del Framework dell'interfaccia utente

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Inizia a usare i servizi di comunicazione di Azure usando il Framework dell'interfaccia utente per integrare rapidamente le esperienze di comunicazione nelle tue applicazioni. In questa Guida introduttiva si apprenderà come integrare i componenti di base di UI Framework nell'applicazione per creare esperienze di comunicazione. 

I componenti del Framework dell'interfaccia utente sono disponibili in due tipi: base e composito.

- I **componenti di base** rappresentano funzionalità di comunicazione discrete; si tratta dei blocchi predefiniti di base che possono essere usati per creare esperienze di comunicazione complesse. 
- I **componenti compositi** sono esperienze chiave per scenari di comunicazione comuni creati usando componenti di **base** come blocchi predefiniti e inclusi in un pacchetto per essere facilmente integrati nelle applicazioni.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Versioni LTS attive e manutenzione LTS (nodo 12 consigliato).
- Una risorsa attiva di Servizi di comunicazione. [Creare una risorsa di Servizi di comunicazione](./../create-communication-resource.md).
- Un token di accesso utente per creare un'istanza del client di chiamata. Vedere come [creare e gestire i token di accesso utente](./../access-tokens.md).

## <a name="setting-up"></a>Configurazione

Il Framework dell'interfaccia utente richiede l'installazione di un ambiente React. Questa operazione verrà eseguita in seguito. Se si dispone già di un'app REACT, è possibile ignorare questa sezione.

### <a name="set-up-react-app"></a>Configurare l'app React

Per questa Guida introduttiva verrà usato il modello create-React-app. Per altre informazioni, vedere [Introduzione a React](https://reactjs.org/docs/create-a-new-react-app.html) .

```console

npx create-react-app my-app

cd my-app

```

Al termine di questo processo, è necessario disporre di un'applicazione completa all'interno della cartella `my-app` . Per questa Guida introduttiva verranno modificati i file all'interno della `src` cartella.

### <a name="install-the-package"></a>Installare il pacchetto

Usare il `npm install` comando per installare i servizi di comunicazione di Azure che chiamano SDK per JavaScript. Spostare il tarball fornito (anteprima privata) nella directory My-App.

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

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità di Azure Communication Services UI SDK:

| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| Provider| Provider interfaccia utente Fluent che consente agli sviluppatori di modificare i componenti dell'interfaccia utente Fluent sottostanti|
| CallingProvider| Chiamata del provider per creare un'istanza di una chiamata. Obbligatorio per aggiungere componenti aggiuntivi|
| ChatProvider | Provider di chat per creare un'istanza di un thread di chat. Obbligatorio per aggiungere componenti aggiuntivi|
| MediaGallery | Componente di base che mostra i partecipanti alle chiamate e i relativi flussi video remoti |
| MediaControls | Componente di base per il controllo della chiamata, inclusa la schermata mute, video, share |
| ChatThread | Componente di base che esegue il rendering di un thread di chat con indicatori di digitazione, conferme di lettura e così via. |
| SendBox | Componente di base che consente all'utente di immettere i messaggi che verranno inviati al thread Unito|

## <a name="initialize-calling-and-chat-providers-using-azure-communication-services-credentials"></a>Inizializzare i provider di chiamate e chat usando le credenziali di servizi di comunicazione di Azure

Passare alla `src` cartella all'interno di `my-app` e cercare il file `app.js` . Qui verrà rilasciato il codice seguente per inizializzare i provider di chiamate e di chat. Questi provider sono responsabili della gestione del contesto delle chiamate e delle esperienze di chat. È possibile scegliere quello da usare a seconda del tipo di esperienza di comunicazione che si sta creando. Se necessario, è possibile usare entrambe contemporaneamente. Per inizializzare i componenti, è necessario un token di accesso recuperato dai servizi di comunicazione di Azure. Per informazioni dettagliate su come ottenere i token di accesso, vedere: [creare e gestire i token di accesso](./../access-tokens.md).

> [!NOTE]
> I componenti non generano token di accesso, ID gruppo o ID thread. Questi elementi provengono da servizi che passano attraverso i passaggi appropriati per generare questi ID e passarli all'applicazione client. Per ulteriori informazioni, vedere: [architettura del server client](./../../concepts/client-and-server-architecture.md).
> 
> Ad esempio, il provider di chat prevede che l'oggetto `userId` associato all'oggetto `token` utilizzato per inizializzarlo sia già stato aggiunto all'oggetto `threadId` fornito. Se il token non è stato aggiunto all'ID del thread, il provider di chat avrà esito negativo. Per ulteriori informazioni sulla chat, vedere: [Introduzione con chat](./../chat/get-started.md)

Verrà usato un tema dell'interfaccia utente Fluent per migliorare l'aspetto dell'applicazione:

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import { mergeThemes, teamsTheme } from '@fluentui/react-northstar';
import { Provider } from '@fluentui/react-northstar/dist/commonjs/components/Provider/Provider';
import { svgIconStyles } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconStyles';
import { svgIconVariables } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconVariables';
import * as siteVariables from '@fluentui/react-northstar/dist/es/themes/teams/siteVariables';

const iconTheme = {
  componentStyles: {
    SvgIcon: svgIconStyles
  },
  componentVariables: {
    SvgIcon: svgIconVariables
  },
  siteVariables
};

function App(props) {

  return (
    <Provider theme={mergeThemes(iconTheme, teamsTheme)}>
        <CallingProvider
        displayName={/*Insert Display Name to be used for the user*/}
        groupId={/*Insert GUID for group call to be joined*/}
        token={/*Insert the Azure Communication Services access token*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            // Add Calling Components Here
        </CallingProvider>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}

        <ChatProvider
        token={/*Insert the Azure Communication Services access token*/}
        displayName={/*Insert Display Name to be used for the user*/}
        threadId={/*Insert id for group chat thread to be joined*/}
        endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            //  Add Chat Components Here
        </ChatProvider>
    </Provider>
  );
}

export default App;

```

Una volta inizializzato, questo provider consente di creare un layout personalizzato usando i componenti di base del Framework dell'interfaccia utente e qualsiasi logica di layout aggiuntiva. Il provider si occupa dell'inizializzazione di tutta la logica sottostante e della corretta connessione dei diversi componenti. Verranno quindi usati diversi componenti di base forniti dal framework dell'interfaccia utente per creare esperienze di comunicazione. È possibile personalizzare il layout di questi componenti e aggiungere tutti gli altri componenti personalizzati di cui si desidera eseguire il rendering. 

## <a name="build-ui-framework-calling-component-experiences"></a>Creazione del Framework dell'interfaccia utente per la chiamata di componenti

Per chiamare, verranno usati i `MediaGallery` componenti e `MediaControls` . Per altre informazioni, vedere funzionalità del [Framework dell'interfaccia utente](./../../concepts/ui-framework/ui-sdk-features.md). Per iniziare, nella `src` cartella creare un nuovo file denominato `CallingComponents.js` . Qui verrà inizializzato un componente della funzione che conterrà i componenti di base per l'importazione in `app.js` . È possibile aggiungere layout e stili aggiuntivi intorno ai componenti. 

`CallingComponents.js`
```javascript

import {MediaGallery, MediaControls, MapToCallConfigurationProps, connectFuncsToContext} from "@azure/acs-ui-sdk"

function CallingComponents(props) {

  if (props.isCallInitialized) {props.joinCall()}

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <MediaGallery/>
        <MediaControls/>
    </div>
  );
}

export default connectFuncsToContext(CallingComponents, MapToCallConfigurationProps);

```

Alla fine di questo file sono stati esportati i componenti di chiamata usando il  `connectFuncsToContext` metodo del Framework dell'interfaccia utente per connettere i componenti dell'interfaccia utente chiamante allo stato sottostante usando la funzione di mapping `MapToCallingSetupProps` . Questo metodo produce il componente con i relativi oggetti Props popolati, che vengono quindi usati per controllare lo stato e partecipare alla chiamata. Utilizzando la `isCallInitialized` proprietà per verificare se `CallAgent` è pronto, utilizzare il `joinCall` metodo per eseguire l'Unione. Il Framework dell'interfaccia utente supporta le funzioni di mapping personalizzate da usare per gli scenari in cui gli sviluppatori vogliono controllare la modalità di push dei dati nei componenti.

## <a name="build-ui-framework-chat-component-experiences"></a>Compilazione del componente di chat del Framework dell'interfaccia utente

Per la chat, si useranno `ChatThread` i `SendBox` componenti e. Per altre informazioni su questi componenti, vedere [funzionalità del Framework dell'interfaccia utente](./../../concepts/ui-framework/ui-sdk-features.md). Per iniziare, nella `src` cartella creare un nuovo file denominato `ChatComponents.js` . Qui verrà inizializzato un componente della funzione che conterrà i componenti di base per l'importazione in `app.js` .

`ChatComponents.js`
```javascript

import {ChatThread, SendBox} from '@azure/acs-ui-sdk'

function ChatComponents() {

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <ChatThread />
        <SendBox />
    </div >
  );
}

export default ChatComponents;

```

## <a name="add-calling-and-chat-components-to-the-main-application"></a>Aggiungere componenti per la chiamata e la chat all'applicazione principale

Tornando al `app.js` file, i componenti verranno ora aggiunti a `CallingProvider` e `ChatProvider` come mostrato di seguito.

`App.js`
```javascript

import ChatComponents from './ChatComponents';
import CallingComponents from './CallingComponents';

<Provider ... >
    <CallingProvider .... >
        <CallingComponents/>
    </CallingProvider>

    <ChatProvider .... >
        <ChatComponents />
    </ChatProvider>
</Provider>

```

## <a name="run-quickstart"></a>Esegui avvio rapido

Per eseguire il codice precedente, usare il comando:

```console

npm run start   

```

Per eseguire il test completo delle funzionalità, è necessario un secondo client con la funzionalità chiamata e chat per aggiungere la chiamata e il thread di chat. Vedere l'esempio di [chiamata di Hero](./../../samples/calling-hero-sample.md) e [chat Hero](./../../samples/chat-hero-sample.md) come possibili opzioni.

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire e rimuovere una sottoscrizione di Servizi di comunicazione, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate. Vedere altre informazioni sulla [pulizia delle risorse](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Prova componenti compositi del Framework dell'interfaccia utente](./get-started-with-composites.md)

Per altre informazioni, vedere le risorse seguenti:
- [Panoramica del Framework dell'interfaccia utente](../../concepts/ui-framework/ui-sdk-overview.md)
- [Funzionalità del Framework dell'interfaccia utente](./../../concepts/ui-framework/ui-sdk-features.md)
