---
title: Concetti relativi all'integrazione della telefonia PSTN per i servizi di comunicazione di Azure
description: Informazioni su come integrare le funzionalità di chiamata PSTN nell'applicazione Servizi di comunicazione di Azure.
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 7aebb10fb3855936d930685f1a8eef2588140ad1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105930456"
---
# <a name="telephony-concepts"></a>Concetti relativi alla telefonia

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-phone-numbers.md)]

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

I servizi di comunicazione di Azure che chiamano gli SDK possono essere usati per aggiungere telefonia e PSTN alle applicazioni. Questa pagina riepiloga i concetti e le funzionalità principali di telefonia. Per ulteriori informazioni su linguaggi e funzionalità SDK specifici, vedere la [libreria chiamante](../../quickstarts/voice-video-calling/calling-client-samples.md) .

## <a name="overview-of-telephony"></a>Panoramica della telefonia
Ogni volta che gli utenti interagiscono con un numero di telefono tradizionale, le chiamate sono facilitate dalla chiamata vocale PSTN (Public Switched Telephone Network). Per effettuare e ricevere chiamate PSTN, è necessario aggiungere funzionalità di telefonia alla risorsa di Servizi di comunicazione di Azure. In questo caso, per connettere gli utenti, la segnalazione e i flussi multimediali usano una combinazione di tecnologie basate su IP e PSTN. Servizi di comunicazione fornisce due modi discreti per raggiungere la rete PSTN, ovvero la chiamata al cloud di Azure e l'interfaccia SIP.

### <a name="azure-cloud-calling"></a>Chiamata al cloud di Azure

Un modo semplice per aggiungere la connettività PSTN all'app o al servizio, in questo caso Microsoft è il provider Telco. È possibile acquistare numeri direttamente da Microsoft. La chiamata al cloud di Azure è una soluzione di telefonia all-in-the-cloud per i servizi di comunicazione. Questa è l'opzione più semplice che connette ACS alla rete telefonica pubblica commutata (PSTN) per consentire le chiamate a dispositivi fissi e telefoni cellulari in tutto il mondo. Con questa opzione, Microsoft funge da gestore PSTN, come illustrato nel diagramma seguente:

![Diagramma di chiamata del cloud di Azure.](../media/telephony-concept/azure-calling-diagram.png)

Se la risposta è "Sì", il cloud di Azure che chiama è la soluzione ideale:
- La chiamata al cloud di Azure è disponibile nella tua area.
- Non è necessario mantenere il gestore PSTN corrente.
- Si vuole usare l'accesso gestito da Microsoft alla rete PSTN.

Con questa opzione:
- È possibile ottenere numeri direttamente da Microsoft e chiamare telefoni in tutto il mondo.
- Non è necessaria la distribuzione o la manutenzione di una distribuzione locale, perché la chiamata al cloud di Azure opera da servizi di comunicazione di Azure.
- Nota: se necessario, è possibile scegliere di connettere un controller di bordo della sessione (SBC) supportato tramite l'interfaccia SIP per l'interoperabilità con PBX di terze parti, dispositivi analoghi e altre apparecchiature di telefonia di terze parti supportate da SBC.

Questa opzione richiede una connessione senza interruzioni ai servizi di comunicazione di Azure.

### <a name="sip-interface"></a>Interfaccia SIP

Con questa opzione è possibile connettere la telefonia locale legacy e il vettore scelto ai servizi di comunicazione di Azure. Fornisce funzionalità di chiamata PSTN alle applicazioni ACS anche se la chiamata cloud di Azure non è disponibile nel proprio paese/area geografica. 

![Diagramma dell'interfaccia SIP.](../media/telephony-concept/sip-interface-diagram.png)

Se si risponde a "Sì" per una delle domande seguenti, l'interfaccia SIP è la soluzione ideale:

- Si desidera utilizzare ACS con funzionalità di chiamata PSTN.
- È necessario mantenere il gestore PSTN corrente.
- Si vuole combinare il routing, con alcune chiamate che passano attraverso la chiamata al cloud di Azure, alcune tramite il vettore.
- È necessario interoperare con PBX e/o apparecchiature di terze parti, ad esempio pagine di overhead, dispositivi analoghi e così via.

Con questa opzione:

- È possibile connettere SBC supportato ai servizi di comunicazione di Azure senza la necessità di software locale aggiuntivo.
- È possibile utilizzare letteralmente qualsiasi gestore telefonico con ACS.
- È possibile scegliere di configurare e gestire questa opzione oppure può essere configurata e gestita dal gestore o dal partner (richiedere se il gestore o il partner fornisce questa opzione).
- È possibile configurare l'interoperabilità tra le apparecchiature di telefonia, ad esempio un PBX di terze parti e dispositivi analoghi, e ACS.

Questa opzione richiede quanto segue:

- Connessione ininterrotta ad Azure.
- Distribuzione e gestione di un SBC supportato.
- Un contratto con un vettore di terze parti. (A meno che non sia stata distribuita come opzione per fornire una connessione a PBX di terze parti, a dispositivi analoghi o ad altre apparecchiature di telefonia per gli utenti che si trovano nei servizi di comunicazione).

## <a name="next-steps"></a>Passaggi successivi

### <a name="conceptual-documentation"></a>Documentazione concettuale

- [Tipi di numeri di telefono in Servizi di comunicazione di Azure](./plan-solution.md)
- [Pianificare l'interfaccia SIP](./sip-interface-infrastructure.md)
- [Prezzi](../pricing.md)

### <a name="quickstarts"></a>Guide rapide introduttive

- [Ottenere un numero di telefono](../../quickstarts/telephony-sms/get-phone-number.md)
- [Chiamata al telefono](../../quickstarts/voice-video-calling/pstn-call.md)
