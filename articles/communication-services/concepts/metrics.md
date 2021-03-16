---
title: Definizioni delle metriche per Servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Questo documento contiene le definizioni delle metriche disponibili nel portale di Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 129a718175fdda80d4d6852e3d3b4cea609da64d
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492414"
---
# <a name="metrics-overview"></a>Panoramica delle metriche

Servizi di comunicazione di Azure attualmente include le metriche per i servizi Chat e SMS. È possibile usare [Esplora metriche di Azure](../../azure-monitor/essentials/metrics-getting-started.md) per tracciare i propri grafici, analizzare le anomalie nei valori delle metriche e interpretare il traffico API usando i dati delle metriche generati dalle richieste di Chat e SMS.

## <a name="where-to-find-metrics"></a>Dove trovare le metriche

I servizi Chat e SMS di Servizi di comunicazione di Azure generano metriche per richieste API. Queste metriche sono disponibili nel pannello Metriche nella risorsa di Servizi di comunicazione. È anche possibile creare dashboard permanenti usando il pannello Cartelle di lavoro nella risorsa di Servizi di comunicazione.

## <a name="metric-definitions"></a>Definizioni delle metriche

Esistono due tipi di richieste rappresentate nelle metriche di Servizi di comunicazione: **richieste API di Chat** e **richieste API di SMS**.

Entrambe le metriche di richieste API contengono tre dimensioni che è possibile usare per filtrare i dati. Queste dimensioni possono essere aggregate usando il tipo di aggregazione `Count` e supportano tutte le serie temporali di aggregazione standard di Azure, tra cui `Sum`, `Average`, `Min` e `Max`.

Altre informazioni sui tipi di aggregazione supportati e sulle aggregazioni di serie temporali sono disponibili in [Funzionalità avanzate di Esplora metriche di Azure](../../azure-monitor/essentials/metrics-charts.md#aggregation)

- **Operazione**: tutte le operazioni o le route che è possibile chiamare nel gateway Chat di Servizi di comunicazione di Azure.
- **Codice di stato**: la risposta del codice di stato inviata dopo la richiesta.
- **StatusSubClass**: la serie di codici di stato inviati dopo la risposta. 


### <a name="chat-api-request-metric-operations"></a>Operazioni per le metriche delle richieste API di Chat

Per le metriche delle richieste API di Chat sono disponibili le operazioni seguenti:

| Operazione/Route    | Descrizione                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| GetChatMessage       | Ottiene un messaggio in base a ID messaggio. |
| ListChatMessages     | Ottiene un elenco di messaggi di chat da un thread. |
| SendChatMessage      | Invia un messaggio di chat a un thread. |
| UpdateChatMessage    | Aggiorna un messaggio di chat. |
| DeleteChatMessage    | Elimina un messaggio di chat. |
| GetChatThread        | Ottiene un thread di chat. |
| ListChatThreads      | Ottiene l'elenco dei thread di chat di un utente. |
| UpdateChatThread     | Aggiorna le proprietà di un thread di chat. |
| CreateChatThread     | Crea un thread di chat. |
| DeleteChatThread     | Elimina un thread. |
| GetReadReceipts      | Ottiene le conferme di lettura per un thread. |
| SendReadReceipt      | Invia un evento di conferma di lettura a un thread per conto di un utente. |
| SendTypingIndicator           | Pubblica un evento di digitazione in un thread per conto dell'utente. |
| ListChatThreadParticipants    | Ottiene i membri di un thread. |
| AddChatThreadParticipants     | Aggiunge membri a un thread. Se i membri esistono già, non vengono apportate modifiche. |
| RemoveChatThreadParticipant   | Rimuove un membro da un thread. |

:::image type="content" source="./media/chat-metric.png" alt-text="Metriche delle richieste API di Chat.":::

Se viene effettuata una richiesta a un'operazione non riconosciuta, si riceverà una risposta con il valore "Route errata".

### <a name="sms-api-requests"></a>Richieste API di SMS

Per le metriche delle richieste API di SMS sono disponibili le operazioni seguenti:

| Operazione/Route    | Descrizione                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| SMSMessageSent       | Invia un messaggio SMS. |
| SMSDeliveryReportsReceived     | Ottiene i report di recapito di SMS |
| SMSMessagesReceived      | Ottiene i messaggi SMS. |


:::image type="content" source="./media/sms-metric.png" alt-text="Metriche delle richieste API di SMS.":::

### <a name="authentication-api-requests"></a>Richieste API di autenticazione

Per le metriche delle richieste API di autenticazione sono disponibili le operazioni seguenti:

| Operazione/Route    | Descrizione                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| CreateIdentity       | Crea un'identità che rappresenta un singolo utente. |
| DeleteIdentity       | Elimina un'identità. |
| CreateToken          | Crea un token di accesso. |
| RevokeToken          | Revoca tutti i token di accesso creati per un'identità prima di una data o un'ora specificata. |

:::image type="content" source="./media/acs-auth-metrics.png" alt-text="Metrica della richiesta di autenticazione.":::

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [metriche della piattaforma dati](../../azure-monitor/essentials/data-platform-metrics.md)
