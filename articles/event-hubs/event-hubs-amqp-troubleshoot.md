---
title: Risolvere gli errori di AMQP in hub eventi di Azure | Microsoft Docs
description: Fornisce un elenco di errori AMQP che possono essere visualizzati quando si usa hub eventi di Azure e si verificano tali errori.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 51b96792f6921bae9364212c6e5f9c987ff05e2a
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466066"
---
# <a name="amqp-errors-in-azure-event-hubs"></a>Errori AMQP in hub eventi di Azure
Questo articolo fornisce alcuni degli errori ricevuti quando si usa AMQP con hub eventi di Azure. Sono tutti comportamenti standard del servizio. È possibile evitarli effettuando chiamate di invio/ricezione sulla connessione o sul collegamento, che ricrea automaticamente la connessione o il collegamento.

## <a name="link-is-closed"></a>Collegamento chiuso 
Quando la connessione e il collegamento AMQP sono attivi, viene visualizzato l'errore seguente, ma non viene effettuata alcuna chiamata (ad esempio, trasmissione o ricezione) utilizzando il collegamento per 30 minuti. Il collegamento viene quindi chiuso. La connessione è ancora aperta.

"AMQP: link: Detach-Forced: il collegamento ' G2:7223832: user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' è forzato scollegato dal broker a causa di errori verificatisi nel server di pubblicazione (link164614). Origine scollegamento: AmqpMessagePublisher. IdleTimerExpired: timeout di inattività: 00:30:00. TrackingId: 00000000000000000000000000000000000000_G2_B3, SystemTracker: MyNamespace: argomento: argomento, timestamp: 2/16/2018 11:10:40 PM "

## <a name="connection-is-closed"></a>Connessione chiusa
Viene visualizzato l'errore seguente nella connessione AMQP quando tutti i collegamenti della connessione sono stati chiusi perché non ci sono attività (inattive) e non è stato creato un nuovo collegamento in 5 minuti.

"Error {condition = AMQP: Connection: Forced, Description =' la connessione è rimasta inattiva per un numero di 300000 millisecondi consentito ed è chiusa dal contenitore ' LinkTracker '. TrackingId: 00000000000000000000000000000000000_G21, SystemTracker: gateway5, timestamp: 2019-03-06T17:32:00', info = null} "

## <a name="link-isnt-created"></a>Il collegamento non è stato creato 
Questo errore viene visualizzato quando viene creata una nuova connessione AMQP, ma non viene creato un collegamento entro 1 minuto dalla creazione della connessione AMQP.

"Error {condition = AMQP: Connection: Forced, Description =' la connessione è rimasta inattiva per un numero di 60000 millisecondi consentito ed è chiusa dal contenitore ' LinkTracker '. TrackingId: 0000000000000000000000000000000000000_G21, SystemTracker: gateway5, timestamp: 2019-03-06T18:41:51', info = null} "

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su AMQP, vedere la [Guida al protocollo AMQP 1,0](../service-bus-messaging/service-bus-amqp-protocol-guide.md).
