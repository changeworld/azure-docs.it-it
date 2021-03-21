---
title: Domini eventi in Griglia di eventi di Azure
description: Questo articolo descrive come usare i domini eventi per gestire il flusso di eventi personalizzati per vari clienti, organizzazioni o applicazioni aziendali.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 46a50a8ecc50bd1b80efcba41228564df1c36c9f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198686"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Informazioni sui domini eventi per la gestione di argomenti di Griglia di eventi

Questo articolo descrive come usare i domini eventi per gestire il flusso di eventi personalizzati per vari clienti, organizzazioni o applicazioni aziendali. Usare i domini eventi per:

* Gestire architetture di gestione degli eventi su larga scala.
* Gestire l'autenticazione e l'autorizzazione.
* Partizionare gli argomenti senza doverli gestire singolarmente.
* Evitare di dover pubblicare singolarmente in ogni endpoint degli argomenti.

## <a name="event-domain-overview"></a>Panoramica del dominio eventi

Un dominio eventi è uno strumento per la gestione di un numero elevato di argomenti di Griglia di eventi correlati alla stessa applicazione. Si può paragonare a un meta-argomento che può avere migliaia di singoli argomenti.

I domini eventi mettono a disposizione la stessa architettura usata dai servizi di Azure, come Archiviazione e hub IoT, per pubblicare i propri eventi. Consentono di pubblicare eventi in migliaia di argomenti. I domini offrono anche il controllo dell'autenticazione e dell'autorizzazione su ogni argomento, consentendo di partizionare i tenant.

## <a name="example-use-case"></a>Esempio di caso d'uso
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="access-management"></a>Gestione degli accessi

Con un dominio si ottengono l'autorizzazione e il controllo dell'autenticazione a granularità fine su ogni argomento tramite il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). È possibile usare questi ruoli per limitare ogni tenant nell'applicazione ai soli argomenti a cui si vuole concedere l'accesso.

Il controllo degli accessi in base al ruolo di Azure nei domini eventi funziona nello stesso modo in cui il [controllo di accesso gestito](security-authorization.md) funziona nel resto della griglia di eventi Usare il controllo degli accessi in base al ruolo di Azure per creare e applicare definizioni di ruolo personalizzate nei domini

### <a name="built-in-roles"></a>Ruoli predefiniti

Griglia di eventi include due definizioni di ruolo predefinite che consentono di semplificare il controllo degli accessi in base al ruolo di Azure. Questi ruoli sono **EventSubscription EventGrid Contributor (Preview)** e **EventGrid EventSubscription Reader (Preview)** e vengono assegnati a utenti che necessitano di sottoscrivere argomenti del dominio eventi del proprietario. È possibile definire l'ambito dell'assegnazione di ruolo solo per l'argomento a cui gli utenti devono sottoscrivere.

Per informazioni su questi ruoli, vedere [Ruoli predefiniti per Griglia di eventi](security-authorization.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Sottoscrizione di argomenti

La sottoscrizione di eventi su un argomento all'interno di un dominio eventi equivale alla [creazione di una sottoscrizione di eventi su un argomento personalizzato](./custom-event-quickstart.md) o alla sottoscrizione di un evento da un servizio di Azure.

### <a name="domain-scope-subscriptions"></a>Sottoscrizioni di ambiti di dominio

I domini eventi consentono anche di sottoscrivere ambiti di dominio. Una sottoscrizione di eventi in un dominio eventi riceverà tutti gli eventi inviati al dominio, indipendentemente dall'argomento a cui gli eventi vengono inviati. Le sottoscrizioni di ambiti di dominio possono essere utili a scopi di gestione e controllo.

## <a name="publishing-to-an-event-domain"></a>Pubblicazione in un dominio eventi

Quando si crea un dominio eventi, si ottiene un endpoint di pubblicazione come se si fosse creato un argomento in Griglia di eventi. 

Per pubblicare gli eventi in un argomento di un dominio di evento, effettuare il push degli eventi nell'endpoint del dominio nello [stesso modo in cui si farebbe per un argomento personalizzato](./post-to-custom-topic.md). L'unica differenza è che è necessario specificare l'argomento al quale si vuole recapitare l'evento.

Ad esempio, pubblicando la matrice di eventi seguente, l'evento con `"id": "1111"` verrebbe inviato all'argomento `foo`, mentre l'evento con `"id": "2222"` verrebbe inviato all'argomento `bar`:

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

I domini eventi gestiscono automaticamente la pubblicazione negli argomenti. Invece di pubblicare gli eventi in ogni argomento gestito individualmente, è possibile pubblicare tutti gli eventi nell'endpoint del dominio. Griglia di eventi assicurerà che ogni evento venga inviato all'argomento corretto.

## <a name="limits-and-quotas"></a>Limiti e quote
Di seguito sono riportati i limiti e le quote correlati ai domini eventi:

- argomenti 100.000 per dominio evento 
- 100 domini eventi per ogni sottoscrizione di Azure 
- 500 sottoscrizioni di eventi per argomento in un dominio di evento
- 50 sottoscrizioni ambito dominio 
- frequenza di inserimento degli eventi 5.000 al secondo (in un dominio)

Se questi limiti non soddisfano l'utente, contattare il team del prodotto aprendo un ticket di supporto o inviando un messaggio di posta elettronica a [askgrid@microsoft.com](mailto:askgrid@microsoft.com) . 

## <a name="pricing"></a>Prezzi
I domini degli eventi utilizzano gli stessi [prezzi delle operazioni](https://azure.microsoft.com/pricing/details/event-grid/) utilizzate da tutte le altre funzionalità di griglia di eventi.

Nei domini eventi le operazioni funzionano come negli argomenti personalizzati. Ogni ingresso di un evento in un dominio eventi è un'operazione e ogni tentativo di recapito di un evento è un'operazione.



## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla configurazione di domini eventi, sulla creazione di argomenti, sulla creazione di sottoscrizioni di eventi e sulla pubblicazione di eventi, vedere [gestire i domini di eventi](./how-to-event-domains.md).
