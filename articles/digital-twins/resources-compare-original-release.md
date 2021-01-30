---
title: Differenze rispetto alla versione originale
titleSuffix: Azure Digital Twins
description: Informazioni sulle modifiche apportate alla nuova versione di Gemelli digitali di Azure
author: baanders
ms.author: baanders
ms.date: 1/28/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8010581667354f2e8484bc7341227ec41713d10f
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072636"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>Che cosa è la nuova versione di Gemelli digitali di Azure? In che modo è diverso dalla versione originale (2018)?

La prima versione di anteprima pubblica di Gemelli digitali di Azure è stata rilasciata a ottobre 2018. Sebbene i concetti di base di tale versione originale siano stati trasferiti al servizio corrente, molte delle interfacce e dei dettagli di implementazione sono stati modificati per rendere il servizio più flessibile e accessibile. Queste modifiche sono state motivate dal feedback dei clienti.

> [!IMPORTANT]
> Con la luce delle funzionalità espanse del nuovo servizio, il servizio originale di Azure Digital Twins è stato ritirato. A partire dal 2021 gennaio, le API e i dati associati non sono più disponibili.

Se è stata usata la prima versione dei dispositivi gemelli digitali di Azure durante la prima anteprima pubblica, usare le informazioni e le procedure consigliate in questo articolo per informazioni su come usare il servizio corrente e sfruttare i vantaggi delle funzionalità.

## <a name="differences-by-topic"></a>Differenze per argomento

Il grafico seguente fornisce una visualizzazione affiancata dei concetti modificati tra la versione originale del servizio e il servizio corrente.

| Argomento | Nella versione originale | Nella versione corrente |
| --- | --- | --- | --- |
| **Modellazione**<br>*Maggiore flessibilità* | La versione originale è stata progettata per gli spazi intelligenti, quindi è stata introdotta con un vocabolario integrato per gli edifici. | I dispositivi gemelli digitali di Azure correnti sono indipendenti dal dominio. È possibile definire un vocabolario personalizzato e modelli personalizzati per la soluzione, per rappresentare più tipi di ambienti in modo più flessibile.<br><br>Per altre informazioni, vedere [*Concetti: Modelli personalizzati*](concepts-models.md). |
| **Topologia**<br>*Maggiore flessibilità*| La versione originale supportava una struttura di dati albero, adattata a spazi intelligenti. I gemelli digitali erano collegati con relazioni gerarchiche. | Con la versione corrente, i dispositivi gemelli digitali possono essere connessi a topologie di grafi arbitrarie, organizzati comunque. Ciò offre una maggiore flessibilità per esprimere le complesse relazioni del mondo reale.<br><br>Per altre informazioni, vedere [*Concetti: Gemelli digitali e grafo dei gemelli*](concepts-twins-graph.md). |
| **Calcolo**<br>*Più avanzato, maggiore flessibilità* | Nella versione originale, la logica per l'elaborazione degli eventi e della telemetria è stata definita in funzioni definite dall'utente (UDF) JavaScript. Il debug con le funzioni definite dall'utente era limitato. | La versione corrente include un modello di calcolo aperto: è possibile fornire la logica personalizzata connettendo risorse di calcolo esterne come [funzioni di Azure](../azure-functions/functions-overview.md). In questo modo è possibile usare il linguaggio di programmazione desiderato, accedere a librerie di codice personalizzate senza restrizioni e sfruttare le risorse di sviluppo e di debug che potrebbero essere presenti nel servizio esterno.<br><br>Per altre informazioni, vedere [*Procedura: Configurare una funzione di Azure per l'elaborazione dei dati*](how-to-create-azure-function.md). |
| **Gestione dei dispositivi con l'hub IoT**<br>*Maggiore accessibilità* | I dispositivi di versione originale gestiti con un'istanza dell' [Hub](../iot-hub/about-iot-hub.md) Internet che era interna al servizio Azure Digital Twins. Questo hub integrato non era completamente accessibile agli sviluppatori. | Nella versione corrente, è possibile usare l'hub Internet delle cose, collegando un'istanza dell'hub di Internet delle cose creata in modo indipendente, insieme a tutti i dispositivi già gestiti. In questo modo si ha accesso completo alle funzionalità dell'hub IoT e il controllo della gestione dei dispositivi.<br><br>Per altre informazioni, vedere [*Procedura: Inserire dati di telemetria dall'hub IoT*](how-to-ingest-iot-hub-data.md). |
| **Sicurezza**<br>*Più standard* | La versione originale include ruoli predefiniti che è possibile usare per gestire l'accesso all'istanza di. | La versione corrente si integra con lo stesso servizio back-end di [controllo degli accessi in base al ruolo di Azure (RBAC)](../role-based-access-control/overview.md) usato da altri servizi di Azure. Questo può rendere più semplice l'autenticazione tra gli altri servizi di Azure nella soluzione come l'hub IoT, Funzioni di Azure, Griglia di eventi e altro ancora.<br>Con il controllo degli accessi in base al ruolo, è ancora possibile usare ruoli predefiniti oppure è possibile creare e configurare ruoli personalizzati.<br><br>Per altre informazioni, vedere [*Concetti: Sicurezza per le soluzioni di Gemelli digitali di Azure*](concepts-security.md). |
| **Scalabilità**<br>*Maggiore* | La versione originale presentava limitazioni di scalabilità per dispositivi, messaggi, grafici e unità di scala. Per ogni sottoscrizione era supportata una sola istanza di Gemelli digitali di Azure.  | La versione corrente si basa su una nuova architettura con scalabilità migliorata e offre una maggiore potenza di calcolo. Supporta anche 10 istanze per area, per sottoscrizione.<br><br>Per informazioni dettagliate sui limiti della versione corrente, vedere limiti del servizio dei dispositivi [*digitali gemelli di Azure*](reference-service-limits.md) . |

## <a name="service-limits"></a>Limiti del servizio

Per un elenco dei limiti dei dispositivi gemelli digitali di Azure, vedere [*limiti del servizio Azure Digital Twins*](reference-service-limits.md).

## <a name="next-steps"></a>Passaggi successivi

* Approfondire l'uso della versione corrente nella Guida introduttiva: [*Guida introduttiva: esplorare uno scenario di esempio*](quickstart-adt-explorer.md).

* In alternativa, iniziare a leggere i concetti chiave con i [*concetti seguenti: modelli personalizzati*](concepts-models.md).