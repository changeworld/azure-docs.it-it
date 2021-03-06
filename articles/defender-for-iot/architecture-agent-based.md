---
title: Che cos'è l'architettura della soluzione basata su agenti
description: Scopri di più su Azure Defender per l'architettura e il flusso di informazioni basate su agenti.
ms.topic: overview
ms.date: 1/25/2021
ms.openlocfilehash: e08c3f151c3d3bc4fe08e61d960874b07f5b63e8
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383525"
---
# <a name="what-is-agent-based-solution-for-device-builders"></a>Che cos'è la soluzione basata su agenti per i generatori di dispositivi

Questo articolo descrive l'architettura del sistema funzionale del Defender per la soluzione basata su agenti. Azure Defender per Internet delle cose offre due set di funzionalità che soddisfano le esigenze dell'ambiente, la soluzione senza agenti per le organizzazioni e la soluzione basata su agenti per i generatori di dispositivi.

## <a name="iot-hub-built-in-security"></a>Sicurezza predefinita dell'hub Internet

Defender per l'it è abilitato per impostazione predefinita in ogni nuovo hub Internet creato. Defender for Internet include funzionalità di monitoraggio, suggerimenti e avvisi in tempo reale, senza richiedere l'installazione dell'agente su tutti i dispositivi e usa l'analisi avanzata sui metadati degli hub delle cose registrate per l'analisi e la protezione dei dispositivi Field e degli hub Internet. 

## <a name="defender-for-iot-micro-agent"></a>Defender per l'agente del Micro Internet 

Defender for Internet è un agente di protezione per la sicurezza e la visibilità del comportamento del dispositivo. raccoglie, aggrega e analizza gli eventi di sicurezza non elaborati dai dispositivi. Gli eventi di sicurezza non elaborati possono includere connessioni IP, creazione di processi, account di accesso utente e altre informazioni rilevanti per la sicurezza. Il difensore degli agenti del dispositivo Internet gestisce anche l'aggregazione di eventi per evitare una elevata velocità effettiva della rete. Gli agenti sono altamente personalizzabili e possono essere usati per attività specifiche, ad esempio per l'invio di informazioni importanti al contratto di servizio più veloce o per l'aggregazione di informazioni e contesto di sicurezza estese in segmenti più grandi, evitando i costi di servizio più elevati.

Gli agenti dispositivo e altre applicazioni usano **Azure Send Security Message SDK** per inviare informazioni di sicurezza nell'hub Azure. L'hub tutto ottiene queste informazioni e le invia al Defender per il servizio Internet delle cose.

Una volta abilitato il servizio Defender for Internet, oltre ai dati inoltrati, l'hub Internet invia anche tutti i dati interni per l'analisi da parte di Defender. Questi dati includono i log delle operazioni del cloud, le identità del dispositivo e la configurazione dell'hub. Tutte queste informazioni consentono di creare il Defender per la pipeline di analisi Internet delle cose.

Il Defender per la pipeline di analisi del servizio Internet riceve anche altri flussi di intelligence per le minacce provenienti da diverse origini all'interno di partner Microsoft Il Defender per l'intera pipeline di analisi funziona con tutte le configurazioni dei clienti effettuate sul servizio, ad esempio gli avvisi personalizzati e l'uso dell'SDK del messaggio di sicurezza di invio.

Con la pipeline di analisi, Defender for Internet usa tutti i flussi di informazioni per generare raccomandazioni e avvisi di utilità pratica. La pipeline contiene sia le regole personalizzate create dai ricercatori e gli esperti della sicurezza, sia i modelli di apprendimento automatico che cercano la deviazione dal comportamento del dispositivo standard e dall'analisi dei rischi.

Il servizio Defender per gli avvisi e le raccomandazioni per l'it (output della pipeline di analisi) viene scritto nell'area di lavoro Log Analytics di ogni cliente. L'inclusione degli eventi non elaborati nell'area di lavoro e degli avvisi e delle raccomandazioni consente l'analisi approfondita e le query usando i dettagli esatti delle attività sospette rilevate.

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="Architettura di micro Agent.":::

## <a name="next-steps"></a>Passaggi successivi

[Domande frequenti su Defender](resources-frequently-asked-questions.md)

[Prerequisiti di sistema](quickstart-system-prerequisites.md)
