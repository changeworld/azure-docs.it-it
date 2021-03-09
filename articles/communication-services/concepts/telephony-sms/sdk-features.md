---
title: Panoramica della libreria client per gli SMS per Servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Fornisce una panoramica della libreria client per gli SMS e delle relative offerte.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ec468f070b5dd418772aeea8760b5d0b270e093a
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487509"
---
# <a name="sms-client-library-overview"></a>Panoramica della libreria client per gli SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Le librerie client per gli SMS di Servizi di comunicazione di Azure possono essere usate per aggiungere messaggistica SMS alle applicazioni.

## <a name="sms-client-library-capabilities"></a>Funzionalità della libreria client per gli SMS

L'elenco seguente presenta il set di funzionalità attualmente disponibili nelle librerie client.

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
