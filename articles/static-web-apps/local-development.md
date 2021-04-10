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
ms.openlocfilehash: 8a45d490d060febc18d77c8487c9f562fd2a914a
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275517"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Configurare lo sviluppo locale per App Web statiche di Azure (anteprima)

Quando viene pubblicata nel cloud, un sito di app Web statiche di Azure ha molti servizi che interagiscono come se fossero la stessa applicazione. Tali servizi includono:

- App Web statica
- API di funzioni di Azure
- Servizi di autenticazione e autorizzazione
- Servizi di routing e configurazione

Questi servizi devono comunicare tra loro e le app Web statiche di Azure gestiscono questa integrazione nel cloud.

In locale, tuttavia, questi servizi non sono collegati automaticamente.

Per offrire un'esperienza simile a quella che si ottiene in Azure, l'interfaccia della riga di comando di [app Web statiche di Azure](https://github.com/Azure/static-web-apps-cli) offre i servizi seguenti:

- Un server del sito statico locale
- Un proxy per il server di sviluppo del Framework front-end
- Un proxy per gli endpoint dell'API, disponibile tramite Azure Functions Core Tools
- Un server di autenticazione e autorizzazione fittizio
- Imposizione di impostazioni di configurazione e route locali

## <a name="how-it-works"></a>Funzionamento

Il grafico seguente mostra in che modo le richieste vengono gestite localmente.

:::image type="content" source="media/local-development/cli-conceptual.png" alt-text="Flusso di richiesta e risposta CLI app Web statica di Azure":::

> [!IMPORTANT]
> Passare a [http://localhost:4280](http://localhost:4280) per accedere all'applicazione servita dall'interfaccia della riga di comando.

- **Le richieste** effettuate alla porta `4280` vengono inviate al server appropriato in base al tipo di richiesta.

- Le richieste di **contenuto statico** , ad esempio HTML o CSS, vengono gestite dal server di contenuti statici dell'interfaccia della riga di comando interna o dal server del Framework front-end per il debug.

- Le richieste di **autenticazione e autorizzazione** vengono gestite da un emulatore, che fornisce un profilo di identità fasullo all'app.

- **Functions Core Tools Runtime** gestisce le richieste all'API del sito.

- Le **risposte** di tutti i servizi vengono restituite al browser come se si trattasse di una singola applicazione.

## <a name="prerequisites"></a>Prerequisiti

- **Sito app Web statiche di Azure esistente**: se non è già stato installato, iniziare con l'app di avvio dell' [API Vanilla](https://github.com/staticwebdev/vanilla-api/generate?return_to=/staticwebdev/vanilla-api/generate) .
- **[Node.js](https://nodejs.org) con NPM**: eseguire la versione [Node.js LTS](https://nodejs.org) , che include l'accesso a [NPM](https://www.npmjs.com/).
- **[Visual Studio Code](https://code.visualstudio.com/)**: usato per il debug dell'applicazione API, ma non è necessario per l'interfaccia della riga di comando.

## <a name="get-started"></a>Introduzione

Aprire un terminale nella cartella radice del sito app Web statiche di Azure esistente.

1. Installare l'interfaccia della riga di comando.

    `npm install -g @azure/static-web-apps-cli`

1. Compilare l'app se richiesta dall'applicazione.

    Eseguire `npm run build` oppure il comando equivalente per il progetto.

1. Passare alla directory di output per l'app. Le cartelle di output sono spesso denominate _Build_ o qualcosa di simile.

1. Avviare l'interfaccia della riga di comando.

    `swa start`

1. Passare a [http://localhost:4280](http://localhost:4280) per visualizzare l'app nel browser.

### <a name="other-ways-to-start-the-cli"></a>Altri modi per avviare l'interfaccia della riga di comando

| Descrizione | Comando |
|--- | --- |
| Gestire una cartella specifica | `swa start ./output-folder` |
| Usare un server di sviluppo di Framework in esecuzione | `swa start http://localhost:3000` |
| Avviare un'app per le funzioni in una cartella | `swa start ./output-folder --api ./api` |
| Usare un'app per le funzioni in esecuzione | `swa start ./output-folder --api http://localhost:7071` |

## <a name="authorization-and-authentication-emulation"></a>Emulazione di autorizzazione e autenticazione

L'interfaccia della riga di comando di app Web statiche emula il [flusso di sicurezza](./authentication-authorization.md) implementato in Azure. Quando un utente esegue l'accesso, è possibile definire un profilo di identità fittizio restituito all'app.

Ad esempio, quando si tenta di passare a `/.auth/login/github` , viene restituita una pagina che consente di definire un profilo di identità.

> [!NOTE]
> L'emulatore funziona con qualsiasi provider di sicurezza, non solo GitHub.

:::image type="content" source="media/local-development/auth-emulator.png" alt-text="Emulatore di autenticazione e autorizzazione locale":::

L'emulatore fornisce una pagina che consente di specificare i valori dell' [entità client](./user-information.md#client-principal-data) seguenti:

| Valore | Descrizione |
| --- | --- |
| **Nome utente** | Nome dell'account associato al provider di sicurezza. Questo valore viene visualizzato come la `userDetails` proprietà nell'entità client e viene generato automaticamente se non si specifica un valore. |
| **ID utente** | Valore generato automaticamente dall'interfaccia della riga di comando.  |
| **Ruoli** | Elenco di nomi di ruoli in cui ogni nome si trova su una nuova riga.  |

Una volta effettuato l'accesso:

- È possibile usare l' `/.auth/me` endpoint o un endpoint di funzione per recuperare l' [entità client](./user-information.md)dell'utente.

- Si sposta per `./auth/logout` cancellare l'entità client e si disconnette l'utente fittizio.

## <a name="debugging"></a>Debug

Sono disponibili due contesti di debug in un'app Web statica. Il primo è per il sito di contenuto statico e il secondo è per le funzioni API. Il debug locale è possibile consentendo all'interfaccia della riga di comando di app Web statiche di usare i server di sviluppo per uno o entrambi i contesti.

Nei passaggi seguenti viene illustrato uno scenario comune in cui vengono utilizzati i server di sviluppo per entrambi i contesti di debug.

1. Avviare il server di sviluppo sito statico. Questo comando è specifico del Framework front-end in uso, ma spesso è costituito da comandi come `npm run build` , `npm start` o `npm run dev` .

1. Aprire la cartella dell'applicazione API in Visual Studio Code e avviare una sessione di debug.

1. Passare gli indirizzi per il server statico e il server API al `swa start` comando elencando tali indirizzi nell'ordine.

    `swa start http://localhost:<DEV-SERVER-PORT-NUMBER> --api=http://localhost:7071`

Gli screenshot seguenti mostrano i terminali per uno scenario di debug tipico:

Il sito di contenuto statico viene eseguito tramite `npm run dev` .

:::image type="content" source="media/local-development/run-dev-static-server.png" alt-text="Server di sviluppo sito statico":::

L'applicazione per le API di funzioni di Azure esegue una sessione di debug in Visual Studio Code.

:::image type="content" source="media/local-development/visual-studio-code-debugging.png" alt-text="Debug API Visual Studio Code":::

L'interfaccia della riga di comando app Web statiche viene avviata con entrambi i server di sviluppo.

:::image type="content" source="media/local-development/static-web-apps-cli-terminal.png" alt-text="Terminale dell'interfaccia della riga di comando app Web statiche di Azure":::

Ora le richieste che passano attraverso `4280` la porta vengono instradate al server di sviluppo del contenuto statico o alla sessione di debug dell'API.

Per altre informazioni sui diversi scenari di debug, con le linee guida su come personalizzare le porte e gli indirizzi del server, vedere il repository dell'interfaccia della riga di comando per [app Web statiche di Azure](https://github.com/Azure/static-web-apps-cli).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare l'applicazione](configuration.md)
