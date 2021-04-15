---
title: Domini eventi in Griglia di eventi di Azure
description: Questo articolo descrive come usare i domini eventi per gestire il flusso di eventi personalizzati per vari clienti, organizzazioni o applicazioni aziendali.
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 32c06ac55f667ec9807c7952127c2cf0f0384024
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374710"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Informazioni sui domini eventi per la gestione di argomenti di Griglia di eventi

Questo articolo descrive come usare i domini eventi per gestire il flusso di eventi personalizzati per vari clienti, organizzazioni o applicazioni aziendali. Usare i domini eventi per:

* Gestire architetture di gestione degli eventi su larga scala.
* Gestire l'autenticazione e l'autorizzazione.
* Partizionare gli argomenti senza doverli gestire singolarmente.
* Evitare di dover pubblicare singolarmente in ogni endpoint degli argomenti.

## <a name="event-domain-overview"></a>Panoramica del dominio eventi

Un dominio eventi è uno strumento per la gestione di un numero elevato di argomenti di Griglia di eventi correlati alla stessa applicazione. Si può paragonare a un meta-argomento che può avere migliaia di singoli argomenti.

I domini eventi offrono la stessa architettura usata dai servizi di Azure, ad esempio Archiviazione e Hub IoT, per pubblicare gli eventi. Consentono di pubblicare eventi in migliaia di argomenti. I domini offrono anche il controllo dell'autenticazione e dell'autorizzazione su ogni argomento, consentendo di partizionare i tenant.

## <a name="example-use-case"></a>Esempio di caso d'uso
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="access-management"></a>Gestione degli accessi

Con un dominio è possibile ottenere un controllo granulare dell'autorizzazione e dell'autenticazione su ogni argomento tramite il controllo degli accessi in base al ruolo di Azure. È possibile usare questi ruoli per limitare ogni tenant nell'applicazione ai soli argomenti a cui si vuole concedere l'accesso.

Il controllo degli accessi in base al ruolo di Azure nei domini [eventi](security-authorization.md) funziona allo stesso modo del controllo di accesso gestito nel resto di Griglia di eventi e azure. Usare il controllo degli accessi in base al ruolo di Azure per creare e applicare definizioni di ruolo personalizzate nei domini eventi.

### <a name="built-in-roles"></a>Ruoli predefiniti

Griglia di eventi include due definizioni di ruolo incorporate per semplificare l'uso dei domini eventi in Controllo degli accessi in base al ruolo di Azure. Questi ruoli sono **EventSubscription EventGrid Contributor (Preview)** e **EventGrid EventSubscription Reader (Preview)** e vengono assegnati a utenti che necessitano di sottoscrivere argomenti del dominio eventi del proprietario. L'ambito dell'assegnazione di ruolo si applica solo all'argomento che gli utenti devono sottoscrivere.

Per informazioni su questi ruoli, vedere [Ruoli predefiniti per Griglia di eventi](security-authorization.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Sottoscrizione di argomenti

La sottoscrizione di eventi su un argomento all'interno di un dominio eventi equivale alla [creazione di una sottoscrizione di eventi su un argomento personalizzato](./custom-event-quickstart.md) o alla sottoscrizione di un evento da un servizio di Azure.

> [!IMPORTANT]
> L'argomento del dominio è **considerato una risorsa gestita automaticamente** in Griglia di eventi. È possibile creare una sottoscrizione di eventi nell'ambito dell'argomento del dominio senza creare l'argomento del dominio. In questo caso, Griglia di eventi crea automaticamente l'argomento di dominio per conto dell'utente. Naturalmente, è comunque possibile scegliere di creare manualmente l'argomento del dominio. Questo comportamento consente di preoccuparsi di una risorsa in meno quando si gestiscono un numero elevato di argomenti di dominio. Quando viene eliminata l'ultima sottoscrizione a un argomento di dominio, viene eliminato anche l'argomento del dominio indipendentemente dal fatto che l'argomento di dominio sia stato creato manualmente o creato automaticamente. 

### <a name="domain-scope-subscriptions"></a>Sottoscrizioni di ambiti di dominio

I domini eventi consentono anche di sottoscrivere ambiti di dominio. Una sottoscrizione di eventi in un dominio eventi riceverà tutti gli eventi inviati al dominio, indipendentemente dall'argomento a cui gli eventi vengono inviati. Le sottoscrizioni di ambiti di dominio possono essere utili a scopi di gestione e controllo.

## <a name="publishing-to-an-event-domain"></a>Pubblicazione in un dominio eventi

Quando si crea un dominio eventi, si ottiene un endpoint di pubblicazione come se si fosse creato un argomento in Griglia di eventi. 

Per pubblicare eventi in qualsiasi argomento in un dominio eventi, eseguire il push degli eventi nell'endpoint del dominio come per [un argomento personalizzato](./post-to-custom-topic.md). L'unica differenza è che è necessario specificare l'argomento al quale si vuole recapitare l'evento.

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
Ecco i limiti e le quote correlati ai domini eventi:

- 100.000 argomenti per dominio eventi 
- 100 domini eventi per sottoscrizione di Azure 
- 500 sottoscrizioni di eventi per argomento in un dominio eventi
- 50 sottoscrizioni con ambito di dominio 
- Frequenza di inserimento di 5.000 eventi al secondo (in un dominio)

Se questi limiti non sono adatti, aprire un ticket di supporto o inviare un messaggio di posta elettronica a [askgrid@microsoft.com](mailto:askgrid@microsoft.com) . 

## <a name="pricing"></a>Prezzi
I domini eventi usano gli stessi [prezzi delle](https://azure.microsoft.com/pricing/details/event-grid/) operazioni usate da tutte le altre funzionalità di Griglia di eventi.

Nei domini eventi le operazioni funzionano come negli argomenti personalizzati. Ogni ingresso di un evento in un dominio eventi è un'operazione e ogni tentativo di recapito di un evento è un'operazione.



## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla configurazione di domini eventi, sulla creazione di argomenti, sulla creazione di sottoscrizioni di eventi e sulla pubblicazione di eventi, vedere [Gestire i domini eventi](./how-to-event-domains.md).
