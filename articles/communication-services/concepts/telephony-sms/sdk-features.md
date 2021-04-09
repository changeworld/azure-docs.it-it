---
title: Panoramica di SMS SDK per i servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Viene fornita una panoramica di SMS SDK e delle relative offerte.
author: mikben
manager: jken
services: azure-communication-services
ms.author: prakulka
ms.date: 03/26/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: c25dfea077510580daf2c1aab584ab9ff5caa7fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105930431"
---
# <a name="sms-sdk-overview"></a>Panoramica di SMS SDK

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-phone-numbers.md)]

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Gli SDK SMS di servizi di comunicazione di Azure possono essere usati per aggiungere la messaggistica SMS alle applicazioni.

## <a name="sms-sdk-capabilities"></a>Funzionalità di SMS SDK

L'elenco seguente presenta il set di funzionalità attualmente disponibili negli SDK.

| Gruppo di funzionalità | Funzionalità                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Funzionalità principali | Inviare e ricevere messaggi SMS                                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Abilita report recapito per i messaggi inviati                                             | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Tutti i set di caratteri (supporto per lingua/Unicode)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Supporto per messaggi lunghi (fino a 2048 byte)                                          | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Concatenazione automatica di messaggi lunghi                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Inviare messaggi a più destinatari alla volta                                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Supporto per idempotenza                                                               | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Tag personalizzati per i messaggi.                                                             | ✔️   | ✔️    | ✔️    | ✔️      |
| Eventi            | Usare Griglia di eventi per configurare i webhook per la ricezione di messaggi in ingresso e report di recapito | ✔️   | ✔️    | ✔️    | ✔️      |
| Numero di telefono      | Numeri verdi                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Chiamata PSTN      | Aggiungere funzionalità per chiamate PSTN al numero verde abilitato per SMS                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione all'invio di SMS](../../quickstarts/telephony-sms/send.md)

I documenti seguenti possono essere interessanti:

- Acquisire familiarità con i [concetti generali relativi agli SMS](../telephony-sms/concepts.md)
- Ottenere un [numero di telefono](../../quickstarts/telephony-sms/get-phone-number.md) con supporto per gli SMS
- [Tipi di numeri di telefono in Servizi di comunicazione di Azure](../telephony-sms/plan-solution.md)
