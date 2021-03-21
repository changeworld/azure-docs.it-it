---
title: Informazioni su Hub di notifica
description: Informazioni su come aggiungere le funzionalità di notifica push con Hub di notifica di Azure.
author: sethmanheim
manager: femila
editor: tjsomasundaram
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 02/12/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: ba5a329d12735fbddc86ff2e3725a1e7de6d9d89
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100546466"
---
# <a name="what-is-azure-notification-hubs"></a>Informazioni su Hub di notifica

Hub di notifica di Azure offre un motore push di facile utilizzo e con scalabilità orizzontale che consente di inviare notifiche a qualsiasi piattaforma (iOS, Android, Windows e così via) da qualsiasi back-end (cloud o locale). Hub di notifica funziona perfettamente per scenari aziendali e di consumo. Di seguito vengono forniti alcuni scenari di esempio:

- Invio di notifiche sulle ultime notizie a milioni di utenti con bassa latenza.
- Invio di coupon in base alla posizione a segmenti di utenti interessati.
- Invio di notifiche sugli eventi a utenti o gruppi per applicazioni di media, sport, finanza e gioco.
- Push di contenuti promozionali alle applicazioni per coinvolgere e offrire prodotti ai clienti.
- Inviare notifiche agli utenti di eventi aziendali, ad esempio nuovi messaggi ed elementi di lavoro.
- Invio di codici per l'autenticazione a più fattori.

## <a name="what-are-push-notifications"></a>Informazioni sulle notifiche push

Le notifiche push sono una forma di comunicazione da app a utente in cui gli utenti delle app per dispositivi mobili ricevono notifiche di determinate informazioni desiderate, in genere in una finestra popup o in una finestra di dialogo in un dispositivo mobile. Gli utenti scelgono in genere di visualizzare o chiudere il messaggio; la prima opzione consente di aprire l'app per dispositivi mobili che ha comunicato la notifica. Alcune notifiche vengono recapitate automaticamente dietro le quinte per l'esecuzione dell'app e decidono cosa fare.

Le notifiche push sono fondamentali per le app di consumo perché aumentano l'interesse e l'uso delle app, mentre per le app aziendali favoriscono la comunicazione di informazioni aziendali aggiornate. Sono la migliore forma di comunicazione tra app e utente perché consentono un notevole risparmio energetico per i dispositivi mobili, sono flessibili per i mittenti di notifiche e disponibili anche quando le applicazioni corrispondenti non sono attive.

> [!NOTE]
> Hub di notifica di Azure non supporta ufficialmente le notifiche push Voice over Internet Protocol (VOIP); [questo articolo descrive tuttavia come usare le notifiche APNs VoIP](voip-apns.md) tramite hub di notifica di Azure.

Per altre informazioni sulle notifiche push per alcune piattaforme più comuni, vedere gli argomenti seguenti:

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](/previous-versions/windows/apps/hh779725(v=win.10))

## <a name="how-do-push-notifications-work"></a>Come funzionano le notifiche push?

Le notifiche push vengono recapitate attraverso infrastrutture specifiche della piattaforma denominate *Platform Notification System* (PNS). Offrono funzionalità di base di push per recapitare un messaggio a un dispositivo con un handle fornito e non hanno un'interfaccia comune. Per inviare una notifica a tutti i clienti nelle versioni di Android, iOS e Windows di un'app, lo sviluppatore deve lavorare separatamente con Apple Push Notification Service (APNS), Firebase Cloud Messaging (FCM) e Windows Notification Service (WNS).

In particolare, ecco come funziona un push:

1. Un'applicazione vuole ricevere una notifica, quindi contatta il PNS per la piattaforma di destinazione in cui è in esecuzione l'app e richiede un handle di push univoco e temporaneo. Il tipo di handle dipende dal sistema, ad esempio WNS usa URI mentre per APNS usa i token.
2. L'app client archivia l'handle nel provider o nel back-end dell'app.
3. Per inviare una notifica push, il back-end dell'app contatta il PNS usando l'handle per individuare una specifica app client.
4. Il PNS inoltra quindi la notifica al dispositivo specificato dall'handle.

![Flusso di lavoro delle notifiche push](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>Difficoltà associate alle notifiche push

I PNS sono potenti. Richiedono tuttavia un grande impegno da parte degli sviluppatori di app per implementare anche i più comuni scenari di notifica push, come la trasmissione di notifiche push a utenti segmentati.

L'invio di notifiche push richiede un'infrastruttura complessa che non è correlata alla logica di business principale dell'applicazione. Di seguito sono riportate alcune delle problemi di infrastruttura:

- **Dipendenza della piattaforma**
  - Il back-end richiede una logica complessa e difficile da gestire per inviare notifiche ai dispositivi su varie piattaforme, perché PNS non sono unificate.
- **Ridimensiona**
  - Per le linee guida del PNS, è necessario aggiornare i token di dispositivo a ogni avvio dell'app. Il back-end si occupa di una grande quantità di traffico e dell'accesso al database solo per mantenere aggiornati i token. Se il numero di dispositivi cresce fino a raggiungere centinaia, migliaia o milioni di unità, i costi per la creazione e la gestione dell'infrastruttura diventano significativi.
  - La maggior parte dei sistemi PNS non supporta la trasmissione a più dispositivi. Una semplice trasmissione a un milione di dispositivi causa un milione di chiamate ai PNS. La scalabilità di questa quantità di traffico con latenza minima è molto complessa.
- **Routing**
  - Sebbene PNS fornisca un modo per inviare messaggi ai dispositivi, la maggior parte delle notifiche dell'app è destinata a utenti o gruppi di interesse. Il back-end deve mantenere un registro per associare i dispositivi a gruppi di interesse, utenti, proprietà e così via. Questo overhead aggiunge al tempo per il mercato e i costi di manutenzione di un'app.

## <a name="why-use-azure-notification-hubs"></a>Vantaggi di Hub di notifica di Azure

Hub di notifica Elimina tutte le complessità associate all'invio di notifiche push autonomamente dal back-end dell'app. La sua infrastruttura di notifiche push multipiattaforma e con scalabilità orizzontale riduce la codifica correlata al push e semplifica il back-end. Con Hub di notifica, i dispositivi devono eseguire soltanto la registrazione dei propri handle PNS su un hub, mentre il back-end è responsabile dell'invio di messaggi a utenti o gruppi di interesse, come mostrato nella figura seguente:

![Diagramma di Hub di notifica](./media/notification-hubs-overview/notification-hub-diagram.png)

Hub di notifica è il motore push pronto per l'uso con i vantaggi seguenti:

- **Multipiattaforma**
  - Supporto per tutte le principali piattaforme push.
  - È dotato di un'interfaccia comune per effettuare il push a tutte le piattaforme in formati specifici o indipendenti dalla piattaforma senza un processo specifico per la piattaforma.
  - Consente di gestire l'handle di dispositivo in un solo posto.
- **Multi back-end**
  - Cloud o in locale.
  - .NET, Node.js, Java, Python e così via
- **Set completo di modelli di distribuzione**
  - Trasmissione a una o più piattaforme: è possibile trasmettere immediatamente a milioni di dispositivi su più piattaforme con una singola chiamata API.
  - Push al dispositivo: è possibile inviare notifiche ai singoli dispositivi.
  - Push all'utente: i tag e i modelli consentono di raggiungere tutti i dispositivi multipiattaforma per un utente.
  - Push a segmento con tag dinamici: la funzionalità tag consente di segmentare i dispositivi e di eseguirne il push in base alle proprie esigenze, che si tratti di inviare a un segmento o un'espressione di segmenti (ad esempio, attivo e abitato a Seattle non nuovo utente). Anziché essere limitati alla pubblicazione-sottoscrizione, è possibile aggiornare i tag del dispositivo ovunque e in qualsiasi momento.
  - Push localizzato: la funzionalità modelli consente di ottenere la localizzazione senza influire sul codice back-end.
  - Push non interattivo: consente di abilitare il modello push-to-pull inviando notifiche automatiche ai dispositivi, attivandoli in modo da completare determinati pull o azioni.
  - Push pianificato: è possibile pianificare le notifiche da inviare in qualsiasi momento.
  - Push diretto: è possibile ignorare la registrazione dei dispositivi nel servizio Hub di notifica ed eseguire direttamente push in batch per un elenco di handle di dispositivo.
  - Push personalizzato: le variabili push del dispositivo consentono di inviare notifiche push personalizzate specifiche del dispositivo con coppie chiave-valore personalizzate.
- **Telemetria avanzata**
  - I dati di telemetria relativi a push, dispositivi, errori e operazioni generali sono disponibili sia nel portale di Azure che a livello di codice.
  - I dati di telemetria per messaggio tengono traccia di ogni push dalla chiamata di richiesta iniziale al servizio Hub di notifica per inviare correttamente i push.
  - Platform Notification System feedback comunica tutti i commenti e suggerimenti di PNS per facilitare il debug.
- **Scalabilità**
  - Invia messaggi veloci a milioni di dispositivi senza riprogettazione o partizionamento orizzontale del dispositivo.
- **Sicurezza**
  - Firma di accesso condiviso (SAS) o autenticazione federata.

## <a name="next-steps"></a>Passaggi successivi

Introduzione alla creazione e all'uso di un hub di notifica con l'esercitazione [Eseguire il push delle notifiche alle applicazioni per dispositivi mobili](notification-hubs-android-push-notification-google-fcm-get-started.md).

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png

[How customers are using Notification Hubs]: https://azure.microsoft.com/services/notification-hubs
[Notification Hubs tutorials and guides]: https://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: ./notification-hubs-push-notification-fixer.md
[Android]: ./notification-hubs-android-push-notification-google-gcm-get-started.md
[Windows Universal]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Windows Phone]: ./notification-hubs-windows-mobile-push-notifications-mpns.md
[Kindle]: ./notification-hubs-android-push-notification-google-fcm-get-started.md
[Xamarin.iOS]: ./xamarin-notification-hubs-ios-push-notification-apns-get-started.md
[Xamarin.Android]: ./xamarin-notification-hubs-push-notifications-android-gcm.md
[Microsoft.WindowsAzure.Messaging.NotificationHub]: /previous-versions/azure/reference/dn339221(v=azure.100)
[Microsoft.ServiceBus.Notifications]: /previous-versions/azure/
[App Service Mobile Apps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure portal]: https://portal.azure.com
[tags]: (https://msdn.microsoft.com/library/azure/dn530749.aspx)
