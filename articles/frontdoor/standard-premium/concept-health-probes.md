---
title: Monitoraggio del probe di integrità standard/Premium (anteprima) di Azure front door
description: Questo articolo consente di comprendere il modo in cui il front-end di Azure monitora l'integrità del back-end.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 30a8208babab2991c9d9e86cc419ac50e1530d7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100090"
---
# <a name="azure-front-door-standardpremium-preview-health-probe-monitoring"></a>Monitoraggio del probe di integrità standard/Premium (anteprima) di Azure front door

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

La porta anteriore di Azure invia periodicamente una richiesta HTTP o HTTPS a ogni back-end. Queste richieste consentono a Azure front door di determinare l'integrità di ogni endpoint nel pool back-end. La porta anteriore usa quindi queste risposte dal probe per determinare le risorse di back-end "migliori" per instradare le richieste del client. 

> [!WARNING]
> Poiché la porta anteriore ha molti ambienti perimetrali a livello globale, il volume del probe di integrità per i backend può essere molto elevato da 25 richieste al minuto fino a 1200 richieste al minuto, a seconda della frequenza di probe di integrità configurata. Con la frequenza Probe predefinita di 30 secondi, il volume probe sul back-end deve essere di circa 200 richieste al minuto.

## <a name="supported-protocols"></a>Protocolli supportati

Frontdoor supporta l'invio di probe tramite i protocolli HTTP o HTTPS. Questi probe vengono inviati tramite le stesse porte TCP configurate per il routing delle richieste client e non è possibile eseguirne l'override.

## <a name="supported-http-methods-for-health-probes"></a>Metodi HTTP supportati per i probe di integrità

Front door supporta i seguenti metodi HTTP per l'invio dei probe di integrità:

* **Ottenere:** Il metodo GET indica di recuperare qualsiasi informazione (sotto forma di entità) identificata dall'URI della richiesta.
* **Intestazione:** Il metodo HEAD è identico a GET ad eccezione del fatto che il server non deve restituire un corpo del messaggio nella risposta. Per i nuovi profili di porte anteriori, per impostazione predefinita, il metodo Probe è impostato come HEAD.

> [!NOTE]
> Per un carico inferiore e un costo sui backend, la porta anteriore consiglia di usare le richieste HEAD per i probe di integrità.

## <a name="health-probe-responses"></a>Risposte del probe di integrità

| Risposte  | Descrizione | 
| ------------- | ------------- |
| Determinazione dell'integrità  |  Il codice di stato 200 OK indica che il back-end è integro. Tutti gli altri codici vengono considerati errori. Se per qualsiasi motivo (incluso l'errore di rete) non viene ricevuta una risposta HTTP valida per un probe, il probe viene considerato come un errore.|
| Misurazione della latenza  | La latenza è il tempo misurato dal momento immediatamente prima dell'invio della richiesta del probe al momento in cui viene ricevuto l'ultimo byte della risposta. Viene usata una nuova connessione TCP per ogni richiesta, quindi questa misurazione non è distorta per i backend con le connessioni calde esistenti.  |

## <a name="how-front-door-determines-backend-health"></a>Come Frontdoor determina l'integrità dei back-end

Il front-end di Azure usa lo stesso processo in tre passaggi riportato di seguito in tutti gli algoritmi per determinare l'integrità.

1. Escludere i back-end disabilitati.

1. Escludere i back-end che presentano errori nei probe di integrità:

    * Questa selezione viene eseguita osservando le ultime _n_ risposte del probe di integrità. Se almeno _x_ sono integre, il back-end viene considerato integro.

    * _n_ viene configurato modificando la proprietà SampleSize nelle impostazioni di bilanciamento del carico.

    * _x_ viene configurato modificando la proprietà SuccessfulSamplesRequired nelle impostazioni di bilanciamento del carico.

1. Per i set di back-end integri nel pool back-end, la porta anteriore misura e mantiene la latenza (tempo di round trip) per ogni back-end.


## <a name="complete-health-probe-failure"></a>Errore di completamento del probe di integrità

Se i probe di integrità hanno esito negativo per ogni back-end in un pool di back-end, Frontdoor considererà tutti i back-end non integri e instraderà il traffico in una distribuzione round robin per tutti i back-end.

Quando un back-end torna in uno stato integro, la porta anteriore riprenderà il normale algoritmo di bilanciamento del carico.

## <a name="disabling-health-probes"></a>Disabilitazione di probe di integrità

Se si dispone di un solo back-end nel pool back-end o solo di un back-end è attivo in un pool back-end, è possibile scegliere di disabilitare i probe di integrità. In questo modo si riduce il carico sul back-end dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare uno standard/Premium per la porta anteriore](create-front-door-portal.md).
