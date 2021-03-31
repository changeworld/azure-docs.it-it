---
title: Metriche supportate da griglia di eventi di Azure
description: Questo articolo fornisce le metriche di monitoraggio di Azure supportate dal servizio griglia di eventi di Azure.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 321e318f9dab87fde20b33a6a3a906b020ada622
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588733"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Metriche supportate da griglia di eventi di Azure
Questo articolo fornisce elenchi di metriche di griglia di eventi categorizzate in base agli spazi dei nomi. 

## <a name="system-topics"></a>Argomenti di sistema

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Sì|Valutazioni avanzate del filtro|Conteggio|Totale|Totale dei filtri avanzati valutati nelle sottoscrizioni di eventi per questo argomento.|EventSubscriptionName|
|DeadLetteredCount|Sì|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Errore, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Sì|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|EventSubscriptionName|
|DroppedEventCount|Sì|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason, EventSubscriptionName|
|MatchedEventCount|Sì|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|EventSubscriptionName|
|PublishFailCount|Sì|Publish Failed Events (Eventi con pubblicazione non riuscita)|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, Error|
|PublishSuccessCount|Sì|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuna dimensione|
|PublishSuccessLatencyInMs|Sì|Publish Success Latency (Latenza di pubblicazioni riuscite)|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuna dimensione|
|UnmatchedEventCount|Sì|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuna dimensione|


## <a name="custom-topics"></a>Argomenti personalizzati

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Sì|Valutazioni avanzate del filtro|Conteggio|Totale|Totale dei filtri avanzati valutati nelle sottoscrizioni di eventi per questo argomento.|EventSubscriptionName|
|DeadLetteredCount|Sì|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Errore, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Sì|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|EventSubscriptionName|
|DroppedEventCount|Sì|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason, EventSubscriptionName|
|MatchedEventCount|Sì|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|EventSubscriptionName|
|PublishFailCount|Sì|Publish Failed Events (Eventi con pubblicazione non riuscita)|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, Error|
|PublishSuccessCount|Sì|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuna dimensione|
|PublishSuccessLatencyInMs|Sì|Publish Success Latency (Latenza di pubblicazioni riuscite)|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuna dimensione|
|UnmatchedEventCount|Sì|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuna dimensione|

## <a name="domains"></a>Domains

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Sì|Valutazioni avanzate del filtro|Conteggio|Totale|Totale dei filtri avanzati valutati nelle sottoscrizioni di eventi per questo argomento.|Argomento, EventSubscriptionName, DomainEventSubscriptionName|
|DeadLetteredCount|Sì|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|No|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|Sì|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|No|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|Argomento, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Sì|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Sì|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Sì|Publish Failed Events (Eventi con pubblicazione non riuscita)|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|Argomento, ErrorType, Error|
|PublishSuccessCount|Sì|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Argomento|
|PublishSuccessLatencyInMs|Sì|Publish Success Latency (Latenza di pubblicazioni riuscite)|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuna dimensione|

## <a name="event-subscriptions"></a>Sottoscrizioni di eventi

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sì|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason|
|DeliveryAttemptFailCount|No|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Error, ErrorType|
|DeliverySuccessCount|Sì|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|Nessuna dimensione|
|DestinationProcessingDurationInMs|No|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|Nessuna dimensione|
|DroppedEventCount|Sì|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason|
|MatchedEventCount|Sì|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|Nessuna dimensione|


## <a name="extension-topics"></a>Argomenti sull'estensione

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|---|
|PublishFailCount|Sì|Publish Failed Events (Eventi con pubblicazione non riuscita)|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, Error|
|PublishSuccessCount|Sì|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuna dimensione|
|PublishSuccessLatencyInMs|Sì|Publish Success Latency (Latenza di pubblicazioni riuscite)|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuna dimensione|
|UnmatchedEventCount|Sì|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuna dimensione|

## <a name="partner-namespaces"></a>Spazi dei nomi partner

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sì|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Errore, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Sì|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|EventSubscriptionName|
|DroppedEventCount|Sì|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason, EventSubscriptionName|
|MatchedEventCount|Sì|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|EventSubscriptionName|
|PublishFailCount|Sì|Publish Failed Events (Eventi con pubblicazione non riuscita)|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, Error|
|PublishSuccessCount|Sì|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuna dimensione|
|PublishSuccessLatencyInMs|Sì|Publish Success Latency (Latenza di pubblicazioni riuscite)|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuna dimensione|
|UnmatchedEventCount|Sì|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuna dimensione|


## <a name="partner-topics"></a>Argomenti per i partner

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Sì|Valutazioni avanzate del filtro|Conteggio|Totale|Totale dei filtri avanzati valutati nelle sottoscrizioni di eventi per questo argomento.|EventSubscriptionName|
|DeadLetteredCount|Sì|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Errore, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Sì|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|EventSubscriptionName|
|DroppedEventCount|Sì|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason, EventSubscriptionName|
|MatchedEventCount|Sì|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|EventSubscriptionName|
|PublishFailCount|Sì|Publish Failed Events (Eventi con pubblicazione non riuscita)|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, Error|
|PublishSuccessCount|Sì|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuna dimensione|
|UnmatchedEventCount|Sì|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuna dimensione|


## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente: [log di diagnostica](diagnostic-logs.md)
