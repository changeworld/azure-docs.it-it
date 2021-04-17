---
title: Differenze rispetto alla versione originale
titleSuffix: Azure Digital Twins
description: Informazioni sulle modifiche apportate alla nuova versione di Gemelli digitali di Azure
author: baanders
ms.author: baanders
ms.date: 1/28/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 2bfd36b79ad800c14a68948aa8488e842d3f4def
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481175"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>Che cosa è la nuova versione di Gemelli digitali di Azure? In che modo è diversa dalla versione originale (2018)?

La prima versione di anteprima pubblica di Gemelli digitali di Azure è stata rilasciata a ottobre 2018. Anche se i concetti di base di tale versione originale sono stati illustrati nel servizio corrente, molte delle interfacce e dei dettagli di implementazione sono state modificate per rendere il servizio più flessibile e accessibile. Queste modifiche sono state motivate dal feedback dei clienti.

> [!IMPORTANT]
> Alla luce delle funzionalità espanse del nuovo servizio, il Gemelli digitali di Azure originale è stato ritirato. A partire da gennaio 2021, le API e i dati associati non sono più disponibili.

Se è stata usata la prima versione di Gemelli digitali di Azure durante la prima anteprima pubblica, usare le informazioni e le procedure consigliate in questo articolo per informazioni su come usare il servizio corrente e sfruttarne le funzionalità.

## <a name="differences-by-topic"></a>Differenze per argomento

Il grafico seguente offre una visualizzazione affiancata dei concetti che sono stati modificati tra la versione originale del servizio e il servizio corrente.

| Argomento | Nella versione originale | Nella versione corrente |
| --- | --- | --- | --- |
| **Modellazione**<br>*Maggiore flessibilità* | La versione originale è stata progettata in base agli spazi intelligenti, quindi è stata fornita con un vocabolario incorporato per gli edifici. | L'Gemelli digitali di Azure corrente è indipendente dal dominio. È possibile definire un vocabolario personalizzato e modelli personalizzati per la soluzione, per rappresentare più tipi di ambienti in modo più flessibile.<br><br>Per altre informazioni, vedere [*Concetti: Modelli personalizzati*](concepts-models.md). |
| **Topologia**<br>*Maggiore flessibilità*| La versione originale supportava una struttura di dati dell'albero, adattata agli spazi intelligenti. I gemelli digitali erano collegati con relazioni gerarchiche. | Con la versione corrente, i gemelli digitali possono essere connessi a topologie a grafo arbitrarie, organizzate come si vuole. Ciò offre una maggiore flessibilità per esprimere le complesse relazioni del mondo reale.<br><br>Per altre informazioni, vedere [*Concetti: Gemelli digitali e grafo dei gemelli*](concepts-twins-graph.md). |
| **Calcolo**<br>*Più avanzato, maggiore flessibilità* | Nella versione originale, la logica per l'elaborazione di eventi e dati di telemetria è stata definita nelle funzioni definite dall'utente (UDF) JavaScript. Il debug con le funzioni definite dall'utente era limitato. | La versione corrente ha un modello di calcolo aperto: è possibile fornire logica personalizzata collegando risorse di calcolo esterne [come Funzioni di Azure](../azure-functions/functions-overview.md). In questo modo è possibile usare il linguaggio di programmazione desiderato, accedere a librerie di codice personalizzate senza restrizioni e sfruttare le risorse di sviluppo e di debug che potrebbero essere presenti nel servizio esterno.<br><br>Per altre informazioni, vedere [*Procedura: Configurare una funzione di Azure per l'elaborazione dei dati*](how-to-create-azure-function.md). |
| **Gestione dei dispositivi con l'hub IoT**<br>*Maggiore accessibilità* | Dispositivi gestiti della versione originale con un'istanza [dell'hub IoT](../iot-hub/about-iot-hub.md) interna al Gemelli digitali di Azure servizio. Questo hub integrato non era completamente accessibile agli sviluppatori. | Nella versione corrente si "porta il proprio" hub IoT, collegando un'istanza dell'hub IoT creata in modo indipendente (insieme a tutti i dispositivi già gestiti). In questo modo si ha accesso completo alle funzionalità dell'hub IoT e il controllo della gestione dei dispositivi.<br><br>Per altre informazioni, vedere [*Procedura: Inserire dati di telemetria dall'hub IoT*](how-to-ingest-iot-hub-data.md). |
| **Sicurezza**<br>*Più standard* | La versione originale aveva ruoli predefiniti che è possibile usare per gestire l'accesso all'istanza. | La versione corrente si integra con lo stesso servizio back-end di controllo degli accessi in base al ruolo di [Azure](../role-based-access-control/overview.md) utilizzato da altri servizi di Azure. Questo può rendere più semplice l'autenticazione tra gli altri servizi di Azure nella soluzione come l'hub IoT, Funzioni di Azure, Griglia di eventi e altro ancora.<br>Con il controllo degli accessi in base al ruolo, è ancora possibile usare ruoli predefiniti oppure è possibile creare e configurare ruoli personalizzati.<br><br>Per altre informazioni, vedere [*Concetti: Sicurezza per le soluzioni di Gemelli digitali di Azure*](concepts-security.md). |
| **Scalabilità**<br>*Maggiore* | La versione originale presentava limitazioni di scalabilità per dispositivi, messaggi, grafici e unità di scala. Per ogni sottoscrizione era supportata una sola istanza di Gemelli digitali di Azure.  | La versione corrente si basa su una nuova architettura con scalabilità migliorata e ha una maggiore potenza di calcolo. Supporta anche 10 istanze per area, per sottoscrizione.<br><br>Vedere [*Gemelli digitali di Azure limiti del*](reference-service-limits.md) servizio per informazioni dettagliate sui limiti nella versione corrente. |

## <a name="service-limits"></a>Limiti del servizio

Per un elenco dei limiti Gemelli digitali di Azure, vedere Limiti [*Gemelli digitali di Azure servizio.*](reference-service-limits.md)

## <a name="next-steps"></a>Passaggi successivi

* Approfondire l'uso della versione corrente nell'avvio rapido: [*Guida introduttiva: Esplorare uno scenario di esempio.*](quickstart-azure-digital-twins-explorer.md)

* In altri termini, è possibile iniziare a leggere i concetti [*chiave con Concetti: Modelli personalizzati.*](concepts-models.md)