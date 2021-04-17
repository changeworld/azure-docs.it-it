---
title: Configurare lo sviluppo locale per le app Web statiche di Azure
description: Informazioni su come configurare l'ambiente di sviluppo locale per le app Web statiche di Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 04/02/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: e19d39a32d48ec55473bb957595d47ec5148e74b
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588786"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Configurare lo sviluppo locale per App Web statiche di Azure (anteprima)

Quando viene pubblicato nel cloud, un sito App Web statiche di Azure dispone di molti servizi che funzionano insieme come se fossero la stessa applicazione. Tali servizi includono:

- App Web statica
- API Funzioni di Azure
- Servizi di autenticazione e autorizzazione
- Servizi di routing e configurazione

Questi servizi devono comunicare tra loro e App Web statiche di Azure l'integrazione nel cloud.

In esecuzione in locale, tuttavia, questi servizi non sono collegati automaticamente.

Per offrire un'esperienza simile a quella disponibile in Azure, l'interfaccia della [riga di App Web statiche di Azure fornisce](https://github.com/Azure/static-web-apps-cli) i servizi seguenti:

- Un server del sito statico locale
- Un proxy per il server di sviluppo del framework front-end
- Un proxy per gli endpoint API, disponibile tramite Azure Functions Core Tools
- Un server di autenticazione e autorizzazione fittizio
- Imposizione delle route locali e delle impostazioni di configurazione

## <a name="how-it-works"></a>Funzionamento

Il grafico seguente mostra come le richieste vengono gestite in locale.

:::image type="content" source="media/local-development/cli-conceptual.png" alt-text="Flusso di richiesta e risposta dell'interfaccia della riga di comando dell'app Web statica di Azure":::

> [!IMPORTANT]
> Passare a per accedere `http://localhost:4280` all'applicazione servita dall'interfaccia della riga di comando.

- **Le** richieste effettuate alla `4280` porta vengono inoltrate al server appropriato a seconda del tipo di richiesta.

- **Le richieste di** contenuto statico, ad esempio HTML o CSS, vengono gestite dal server di contenuti statici dell'interfaccia della riga di comando interno o dal server del framework front-end per il debug.

- **Le richieste di autenticazione** e autorizzazione vengono gestite da un emulatore, che fornisce un profilo di identità fittizio all'app.

- **Il runtime di Strumenti di base** di Funzioni gestisce le richieste all'API del sito.

- **Le risposte** di tutti i servizi vengono restituite al browser come se fossero tutte una singola applicazione.

## <a name="prerequisites"></a>Prerequisiti

- **Sito App Web statiche di Azure esistente:** se non se ne ha uno, iniziare con l'app di avvio [vanilla-api.](https://github.com/staticwebdev/vanilla-api/generate?return_to=/staticwebdev/vanilla-api/generate)
- **[Node.js](https://nodejs.org) con npm**: eseguire la [Node.js LTS,](https://nodejs.org) che include l'accesso a [npm](https://www.npmjs.com/).
- **[Visual Studio Code](https://code.visualstudio.com/)**: usato per il debug dell'applicazione API, ma non obbligatorio per l'interfaccia della riga di comando.

## <a name="get-started"></a>Introduzione

Aprire un terminale nella cartella radice del sito App Web statiche di Azure esistente.

1. Installare l'interfaccia della riga di comando.

    `npm install -g @azure/static-web-apps-cli`

1. Compilare l'app, se richiesto dall'applicazione.

    Eseguire `npm run build` o il comando equivalente per il progetto.

1. Passare alla directory di output per l'app. Le cartelle di output sono spesso _denominate build_ o qualcosa di simile.

1. Avviare l'interfaccia della riga di comando.

    `swa start`

1. Passare a http://localhost:4280 per visualizzare l'app nel browser.

### <a name="other-ways-to-start-the-cli"></a>Altri modi per avviare l'interfaccia della riga di comando

| Descrizione | Comando |
|--- | --- |
| Gestire una cartella specifica | `swa start ./output-folder` |
| Usare un server di sviluppo framework in esecuzione | `swa start http://localhost:3000` |
| Avviare un'app Per le funzioni in una cartella | `swa start ./output-folder --api ./api` |
| Usare un'app di Funzioni in esecuzione | `swa start ./output-folder --api http://localhost:7071` |

## <a name="authorization-and-authentication-emulation"></a>Autorizzazione ed emulazione dell'autenticazione

L'interfaccia della riga di comando di App Web statiche emula il [flusso di sicurezza](./authentication-authorization.md) implementato in Azure. Quando un utente accede, è possibile definire un profilo di identità falso restituito all'app.

Ad esempio, quando si tenta di passare a , viene restituita una `/.auth/login/github` pagina che consente di definire un profilo di identità.

> [!NOTE]
> L'emulatore funziona con qualsiasi provider di sicurezza, non solo con GitHub.

:::image type="content" source="media/local-development/auth-emulator.png" alt-text="Emulatore di autenticazione ed autorizzazione locale":::

L'emulatore fornisce una pagina che consente di specificare i valori [dell'entità client](./user-information.md#client-principal-data) seguenti:

| Valore | Descrizione |
| --- | --- |
| **Nome utente** | Nome dell'account associato al provider di sicurezza. Questo valore viene visualizzato `userDetails` come proprietà nell'entità client e viene rigenerato automaticamente se non si specifica un valore. |
| **ID utente** | Valore rigenerato automaticamente dall'interfaccia della riga di comando.  |
| **Ruoli** | Elenco di nomi di ruolo, in cui ogni nome si trova in una nuova riga.  |

Dopo aver eseguito l'accesso:

- È possibile usare `/.auth/me` l'endpoint o un endpoint funzione per recuperare l'entità client [dell'utente.](./user-information.md)

- Passando a cancella `./auth/logout` l'entità client e disconnette l'utente fittizio.

## <a name="debugging"></a>Debug

In un'app Web statica sono presenti due contesti di debug. Il primo è per il sito di contenuto statico e il secondo per le funzioni API. Il debug locale è possibile consentendo all'interfaccia della riga di comando di App Web statiche di usare i server di sviluppo per uno o entrambi i contesti.

La procedura seguente illustra uno scenario comune che usa i server di sviluppo per entrambi i contesti di debug.

1. Avviare il server di sviluppo del sito statico. Questo comando è specifico per il framework front-end in uso, ma spesso viene fornito sotto forma di comandi come `npm run build` `npm start` , o `npm run dev` .

1. Aprire la cartella dell'applicazione API in Visual Studio Code avviare una sessione di debug.

1. Passare gli indirizzi per il server statico e il server API al `swa start` comando elencandoli in ordine.

    `swa start http://localhost:<DEV-SERVER-PORT-NUMBER> --api=http://localhost:7071`

Gli screenshot seguenti mostrano i terminali per uno scenario di debug tipico:

Il sito di contenuto statico è in esecuzione tramite `npm run dev` .

:::image type="content" source="media/local-development/run-dev-static-server.png" alt-text="Server di sviluppo di siti statici":::

L Funzioni di Azure'applicazione API esegue una sessione di debug in Visual Studio Code.

:::image type="content" source="media/local-development/visual-studio-code-debugging.png" alt-text="Visual Studio Code debug dell'API":::

L'interfaccia della riga di comando di App Web statiche viene avviata usando entrambi i server di sviluppo.

:::image type="content" source="media/local-development/static-web-apps-cli-terminal.png" alt-text="App Web statiche di Azure terminale dell'interfaccia della riga di comando":::

A questo punto le richieste che passano attraverso la porta vengono indirizzate al server di sviluppo di contenuto `4280` statico o alla sessione di debug dell'API.

Per altre informazioni sui diversi scenari di debug, con indicazioni su come personalizzare porte e indirizzi del server, vedere il repository dell'interfaccia della riga di comando App Web statiche di Azure [.](https://github.com/Azure/static-web-apps-cli)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare l'applicazione](configuration.md)
