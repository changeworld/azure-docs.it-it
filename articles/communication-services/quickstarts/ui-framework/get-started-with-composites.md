---
title: Introduzione ai componenti compositi SDK del Framework dell'interfaccia utente di servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services quickstart
description: In questa Guida introduttiva si apprenderà come iniziare a usare componenti compositi di Framework dell'interfaccia utente
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 7356fb90914e948b6a74a478ce1e19722b224346
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103488058"
---
# <a name="quickstart-get-started-with-ui-framework-composite-components"></a>Guida introduttiva: Introduzione ai componenti compositi di Framework dell'interfaccia utente

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Inizia a usare i servizi di comunicazione di Azure usando il Framework dell'interfaccia utente per integrare rapidamente le esperienze di comunicazione nelle tue applicazioni. In questa Guida introduttiva si apprenderà come integrare i componenti compositi di Framework dell'interfaccia utente nell'applicazione per creare esperienze di comunicazione.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Versioni LTS attive e manutenzione LTS (nodo 12 consigliato).
- Una risorsa attiva di Servizi di comunicazione. [Creare una risorsa di Servizi di comunicazione](./../create-communication-resource.md).
- Un token di accesso utente per creare un'istanza della chiamata composita. Vedere come [creare e gestire i token di accesso utente](./../access-tokens.md).

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

Usare il comando `npm install` per installare la libreria client Chiamate di Servizi di comunicazione di Azure per JavaScript. Spostare il tarball fornito (anteprima privata) nella directory My-App.

```console

//Private Preview install tarball

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
| GroupCall | Componente composito che esegue il rendering di un'esperienza di chiamata di un gruppo con i controlli e la raccolta partecipanti. |
| GroupChat | Componente composito che esegue il rendering di un'esperienza di chat di gruppo con thread e input di chat |


## <a name="initialize-group-call-and-group-chat-composite-components"></a>Inizializzazione dei componenti compositi di chiamata di gruppo e chat di gruppo

Passare alla `src` cartella all'interno di `my-app` e cercare il file `app.js` . Qui viene eliminato il codice seguente per inizializzare i componenti compositi per la chat di gruppo e chiamare. È possibile scegliere quello da usare a seconda del tipo di esperienza di comunicazione che si sta creando. Se necessario, è possibile usare entrambe contemporaneamente. Per inizializzare i componenti, è necessario un token di accesso recuperato dai servizi di comunicazione di Azure. Per informazioni dettagliate su come ottenere i token di accesso, vedere: [creare e gestire i token di accesso utente](./../access-tokens.md).

> [!NOTE]
> I componenti non generano token di accesso, ID gruppo o ID thread. Questi elementi provengono da servizi che passano attraverso i passaggi appropriati per generare questi ID e passarli all'applicazione client. Per ulteriori informazioni, vedere: [architettura del server client](./../../concepts/client-and-server-architecture.md).
> 
> Ad esempio, la composizione di Group Chat prevede che l'oggetto `userId` associato all'oggetto `token` utilizzato per inizializzarlo sia già stato aggiunto all'oggetto `threadId` fornito. Se il token non è stato aggiunto all'ID del thread, la composizione della chat di gruppo avrà esito negativo. Per ulteriori informazioni sulla chat, vedere: [Introduzione con chat](./../chat/get-started.md)


`App.js`
```javascript

import {GroupCall, GroupChat} from "@azure/acs-ui-sdk"

function App(){

    return(<>
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "50rem", float: "left"}}>
            <GroupCall
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                refreshTokenCallback={CALLBACK} //Optional, Callback to refresh the token in case it expires
                groupId={GROUPID} //Required, Id for group call that will be joined. (GUID)
                onEndCall = { () => {
                    //Optional, Action to be performed when the call ends
                }}
            />
        </div>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "30rem", float: "left"}}>
            <GroupChat 
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                threadId={THREADID} //Required, Id for group chat thread that will be joined.
                endpointUrl={ENDPOINT_URL} //Required, URL for Azure endpoint being used for Azure Communication Services
                onRenderAvatar = { (acsId) => {
                    //Optional, function to override the avatar image on the chat thread. Function receives one parameters for the Azure Communication Services Identity. Must return a React element.
                }}
                refreshToken = { () => {
                    //Optional, function to refresh the access token in case it expires
                }}
                options = {{
                    //Optional, options to define chat behavior
                    sendBoxMaxLength: number | undefined //Optional, Limit the max send box length based on viewport size change.
                }}
            />
        </div>
    </>);
}

export default App;

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
> [Prova i componenti di base del Framework dell'interfaccia utente](./get-started-with-components.md)

Per altre informazioni, vedere le risorse seguenti:
- [Panoramica del Framework dell'interfaccia utente](../../concepts/ui-framework/ui-sdk-overview.md)
- [Funzionalità del Framework dell'interfaccia utente](./../../concepts/ui-framework/ui-sdk-features.md)
