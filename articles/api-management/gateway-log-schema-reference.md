---
title: 'Informazioni di riferimento: Log delle risorse di Gestione API di Azure'
description: Informazioni di riferimento sullo schema per il log delle risorse GatewayLogs di Gestione API di Azure
services: api-management
author: dlepow
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: df0018e323bc0c5725c9752b25b77612f035c196
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100582494"
---
# <a name="reference-api-management-resource-log-schema"></a>Informazioni di riferimento: Schema del log delle risorse di Gestione API

Questo articolo fornisce informazioni di riferimento sullo schema per il log delle risorse GatewayLogs di Gestione API di Azure. Le voci di log includono inoltre i campi nello [schema comune di primo livello](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema).

Per abilitare la raccolta del log delle risorse in Gestione API, vedere [Monitorare le API pubblicate](api-management-howto-use-azure-monitor.md#resource-logs).

## <a name="gatewaylogs-schema"></a>Schema di GatewayLogs

Per ogni richiesta dell'API, vengono registrate le proprietà seguenti.

| Proprietà  | Type | Descrizione |
| ------------- | ------------- | ------------- |
| method | string | Metodo HTTP della richiesta in ingresso |
| url | string | URL della richiesta in ingresso |
| responseCode | integer | Codice di stato della risposta HTTP inviata a un client |
| responseSize | integer | Numero di byte inviati a un client durante l'elaborazione della richiesta | 
| cache | string | Stato di intervento della cache di Gestione API nell'elaborazione della richiesta (riscontri, mancati riscontri, nessuno) | 
| apiId | string | Identificatore dell'entità API per la richiesta corrente | 
| operationId | string | Identificatore dell'entità operazione per la richiesta corrente | 
| clientProtocol | string | Versione del protocollo HTTP della richiesta in ingresso |
| clientTime | integer | Numero di millisecondi impiegati complessivamente per le operazioni di I/O del client (connessione, invio e ricezione byte) | 
| apiRevision | string | Revisione dell'API per la richiesta corrente | 
| clientTlsVersion| string | Versione di TLS usata dal client che invia la richiesta |
| lastError | object | Per una richiesta non riuscita, i dettagli sull'ultimo errore di elaborazione della richiesta | 
| backendMethod | string | Metodo HTTP della richiesta inviata a un back-end |
| backendUrl | string | URL della richiesta inviata a un back-end |
| backendResponseCode | integer | Codice della risposta HTTP ricevuta da un back-end |
| backedProtocol | string | Versione del protocollo HTTP della richiesta inviata a un back-end |
| backendTime | integer | Numero di millisecondi impiegati complessivamente per le operazioni di I/O del back-end (connessione, invio e ricezione byte) | 


## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul monitoraggio delle API in Gestione API, vedere [Monitorare le API pubblicate](api-management-howto-use-azure-monitor.md)
* Altre informazioni sullo [Schema comune e specifico del servizio per i log delle risorse di Azure](../azure-monitor/essentials/resource-logs-schema.md)

