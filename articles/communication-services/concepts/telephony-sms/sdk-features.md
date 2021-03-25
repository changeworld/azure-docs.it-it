---
title: Panoramica di SMS SDK per i servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Viene fornita una panoramica di SMS SDK e delle relative offerte.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 25a5befab6ef141b059caaac7b1784d47f2f93cf
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108112"
---
# <a name="sms-sdk-overview"></a>Panoramica di SMS SDK

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Gli SDK SMS di servizi di comunicazione di Azure possono essere usati per aggiungere la messaggistica SMS alle applicazioni.

## <a name="sms-sdk-capabilities"></a>Funzionalità di SMS SDK

L'elenco seguente presenta il set di funzionalità attualmente disponibili negli SDK.

| Gruppo di funzionalità | Funzionalità                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Funzionalità principali | Inviare e ricevere messaggi SMS </br> *Emoji Unicode supportate*                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Ricevere report di recapito per i messaggi inviati                                            | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Tutti i set di caratteri (supporto per lingua/Unicode)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Supporto per messaggi lunghi (fino a 2048 caratteri)                                           | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Concatenazione automatica di messaggi lunghi                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
| Eventi            | Usare Griglia di eventi per configurare i webhook per la ricezione di messaggi in ingresso e report di recapito | ✔️   | ✔️    | ✔️    | ✔️      |
| Numero di telefono      | Numeri verdi                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Regolamentazione        | Gestione esplicita                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| Monitoraggio        | Monitorare l'utilizzo dei messaggi inviati e ricevuti                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| Chiamata PSTN      | Aggiungere funzionalità per chiamate PSTN al numero verde abilitato per SMS                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione all'invio di SMS](../../quickstarts/telephony-sms/send.md)

I documenti seguenti possono essere interessanti:

- Acquisire familiarità con i [concetti generali relativi agli SMS](../telephony-sms/concepts.md)
- Ottenere un [numero di telefono](../../quickstarts/telephony-sms/get-phone-number.md) con supporto per gli SMS
- [Tipi di numeri di telefono in Servizi di comunicazione di Azure](../telephony-sms/plan-solution.md)
