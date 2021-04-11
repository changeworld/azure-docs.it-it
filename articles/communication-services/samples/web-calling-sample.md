---
title: Servizi di comunicazione di Azure - Esempio di chiamata Web
titleSuffix: An Azure Communication Services sample
description: Informazioni sull'esempio di chiamata Web di Servizi di comunicazione
author: chriswhilar
manager: mariusu-msft
services: azure-communication-services
ms.author: mariusu
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 7d9e2f02eadec9fac93f14a0fe137c72a35224be
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727910"
---
# <a name="get-started-with-the-web-calling-sample"></a>Introduzione all'esempio di chiamata Web

L'esempio Web Calling è un'applicazione Web che funge da procedura dettagliata per le varie funzionalità offerte dall'SDK per la chiamata Web di servizi di comunicazione.

Questo esempio è stato creato per gli sviluppatori e offre un modo molto semplice per iniziare a familiarizzare con Servizi di comunicazione. La sua interfaccia utente è suddivisa in più sezioni, ognuna con un pulsante "Show code" (Mostra codice) che consente di copiare il codice direttamente dal browser nell'applicazione di Servizi di comunicazione.

## <a name="get-started-with-the-web-calling-sample"></a>Introduzione all'esempio di chiamata Web

> [!IMPORTANT]
> [Questo esempio è disponibile su GitHub](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/).

Seguire le/Project/Readme.MD per configurare il progetto ed eseguirlo localmente nel computer.
Quando l' [esempio di chiamata Web](https://github.com/Azure-Samples/communication-services-web-calling-tutorial) è in esecuzione nel computer, verrà visualizzata la pagina di destinazione seguente:

:::image type="content" source="./media/web-calling-tutorial-page-1.png" alt-text="Esercitazione chiamata Web 1" lightbox="./media/web-calling-tutorial-page-1.png":::

:::image type="content" source="./media/web-calling-tutorial-page-2.png" alt-text="Esercitazione chiamata Web 2" lightbox="./media/web-calling-tutorial-page-2.png":::

## <a name="user-provisioning-and-sdk-initialization"></a>Provisioning degli utenti e inizializzazione dell'SDK

Fare clic su "Provision user and initialize SDK" (Effettua il provisioning dell'utente e inizializza l'SDK) usando un token fornito dal servizio back-end di provisioning dei token. Questo servizio back-end è in `/project/webpack.config.js`.

Fare clic sul pulsante "Show code" (Mostra codice) per visualizzare il codice di esempio che può essere usato nella propria soluzione.

Dopo l'inizializzazione dell'SDK dovrebbe essere visualizzata la schermata seguente:

:::image type="content" source="./media/user-provisioning.png" alt-text="Provisioning utenti" lightbox="./media/user-provisioning.png":::

A questo punto è possibile iniziare a effettuare chiamate usando la risorsa di Servizi di comunicazione.

## <a name="placing-and-receiving-calls"></a>Esecuzione e ricezione di chiamate

L'SDK per la chiamata Web di servizi di comunicazione consente **1:1**, **1: N** e il **gruppo** chiamante.

Per le chiamate in uscita 1:1 o 1: N è possibile specificare più identità utente di Servizi di comunicazione da chiamare usando valori separati da virgole. È anche possibile specificare numeri di telefono tradizionali (PSTN) da chiamare usando valori separati da virgole.

Quando si chiamano numeri di telefono PSTN, specificare l'ID chiamante alternativo. Fare clic sul pulsante "Place call" (Chiama) per effettuare una chiamata in uscita:

:::image type="content" source="./media/place-a-call.png" alt-text="Effettuare una chiamata" lightbox="./media/place-a-call.png":::

Per partecipare a una chiamata di gruppo, immettere il GUID che identifica la chiamata e fare clic sul pulsante "Join group" (Entra nel gruppo):

:::image type="content" source="./media/join-a-group-call.png" alt-text="Partecipare a una chiamata di gruppo" lightbox="./media/join-a-group-call.png":::

Fare clic sul pulsante "Show code" (Mostra codice) per visualizzare il codice di esempio per effettuare chiamate, ricevere chiamate e partecipare a chiamate di gruppo.

Una chiamata attiva ha un aspetto simile al seguente:

:::image type="content" source="./media/group-call.png" alt-text="Chiamata di gruppo" lightbox="./media/group-call.png":::

Questo esempio fornisce anche frammenti di codice per le funzionalità seguenti:

  - Fare clic sull'icona del video per accendere/spegnere la videocamera
  - Fare clic sull'icona del microfono per accendere/spegnere il microfono
  - Fare clic sull'icona Riproduci per mettere in attesa/riprendere la chiamata
  - Fare clic sull'icona dello schermo per avviare/interrompere la condivisione dello schermo
  - Fare clic sull'icona della persona per aggiungere un partecipante alla chiamata
  - Fare clic su "Rimuovi partecipante" nell'elenco partecipanti per rimuovere un partecipante specifico dalla chiamata


## <a name="next-steps"></a>Passaggi successivi

>[!div class="nextstepaction"]
>[È possibile scaricare l'esempio da GitHub](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

Per altre informazioni, vedere gli articoli seguenti:

- Familiarizzare con [l'uso dell'SDK chiamante](../quickstarts/voice-video-calling/calling-client-samples.md)
- Leggere altre informazioni sul [funzionamento delle chiamate](../concepts/voice-video-calling/about-call-types.md)
- Vedere la [documentazione di riferimento sulle API](/javascript/api/azure-communication-services/@azure/communication-calling/)
- Esaminare l'esempio di [app Contoso Med](https://github.com/Azure-Samples/communication-services-contoso-med-app)

## <a name="additional-reading"></a>Altre letture

- [Esempi: trovare](./overview.md) altri esempi ed esempi nella pagina Panoramica degli esempi.
- [Redux](https://redux.js.org/) - Gestione dello stato lato client
- [FluentUI](https://aka.ms/fluent-ui) - Libreria dell'interfaccia utente di Microsoft
- [React](https://reactjs.org/) - Libreria per la compilazione di interfacce utente
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) - Framework per la compilazione di applicazioni Web
