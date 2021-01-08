---
title: Connettere i processi di analisi di flusso alle risorse in una rete virtuale di Azure (VNET)
description: Questo articolo descrive come connettere un processo di analisi di flusso di Azure con le risorse presenti in una VNET.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: 99563760bf37c4046e7dd81e779fedbe415380bc
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019483"
---
# <a name="connect-stream-analytics-jobs-to-resources-in-an-azure-virtual-network-vnet"></a>Connettere i processi di analisi di flusso alle risorse in una rete virtuale di Azure (VNet)

I processi di analisi di flusso rendono le connessioni in uscita alle risorse di input e di output di Azure per elaborare i dati in tempo reale e produrre risultati. Queste risorse di input e di output (ad esempio, Hub eventi di Azure e database SQL di Azure) potrebbero trovarsi dietro un firewall di Azure o in una rete virtuale di Azure (VNet). Il servizio di analisi di flusso opera da reti che non possono essere incluse direttamente nelle regole di rete.

Tuttavia, esistono due modi per connettere in modo sicuro i processi di analisi di flusso alle risorse di input e output in questi scenari.
* Uso di endpoint privati nei cluster di analisi di flusso.
* Uso della modalità di autenticazione identità gestita abbinata all'impostazione di rete "Consenti servizi attendibili".

Il processo di analisi di flusso non accetta alcuna connessione in ingresso.

## <a name="private-endpoints-in-stream-analytics-clusters"></a>Endpoint privati nei cluster di analisi di flusso.
[Cluster di analisi di flusso](https://docs.microsoft.com/azure/stream-analytics/cluster-overview) è un cluster di calcolo dedicato a singolo tenant in cui è possibile eseguire i processi di analisi di flusso. È possibile creare endpoint privati gestiti nel cluster di analisi di flusso, che consente a tutti i processi in esecuzione nel cluster di eseguire una connessione in uscita sicura alle risorse di input e output.

La creazione di endpoint privati nel cluster di analisi di flusso è un' [operazione in due passaggi](https://docs.microsoft.com/azure/stream-analytics/private-endpoints). Questa opzione è più adatta per i carichi di lavoro di streaming di medie e grandi dimensioni, perché la dimensione minima di un cluster di analisi di flusso è 36 SUs (anche se la versione 36 può essere condivisa da processi diversi in diverse sottoscrizioni o ambienti quali sviluppo, test e produzione).

## <a name="managed-identity-authentication-with-allow-trusted-services-configuration"></a>Autenticazione dell'identità gestita con la configurazione ' Consenti servizi attendibili '
Alcuni servizi di Azure offrono un'impostazione di rete di **Servizi Microsoft attendibile** che, quando abilitata, consente ai processi di analisi di flusso di connettersi in modo sicuro alla risorsa usando l'autenticazione avanzata. Questa opzione consente di connettere i processi alle risorse di input e output senza richiedere un cluster di analisi di flusso e gli endpoint privati. La configurazione del processo per l'utilizzo di questa tecnica è un'operazione in due passaggi:
* Usare la modalità di autenticazione identità gestita quando si configura l'input o l'output nel processo di analisi di flusso.
* Concedere ai processi di analisi di flusso specifici l'accesso esplicito alle risorse di destinazione assegnando un ruolo di Azure all'identità gestita assegnata dal sistema del processo. 

L'abilitazione di **Consenti servizi Microsoft attendibili** non concede l'accesso con copertura a tutti i processi. In questo modo si ottiene il controllo completo dei processi di analisi di flusso specifici che possono accedere alle risorse in modo sicuro. 

I processi possono connettersi ai seguenti servizi di Azure usando questa tecnica:
1. [Archiviazione BLOB o Azure Data Lake storage Gen2](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity) -può essere l'account di archiviazione del processo, l'input o l'output di streaming.
2. [Hub eventi di Azure](https://docs.microsoft.com/azure/stream-analytics/event-hubs-managed-identity) : può essere l'input o l'output del processo di streaming.

Se i processi devono connettersi ad altri tipi di input o di output, è possibile scrivere prima da analisi di flusso all'output di hub eventi e quindi a qualsiasi destinazione di propria scelta tramite funzioni di Azure. Se si vuole scrivere direttamente da analisi di flusso in altri tipi di output protetti in un VNet o in un firewall, l'unica opzione consiste nell'usare endpoint privati nei cluster di analisi di flusso.

## <a name="next-steps"></a>Passaggi successivi

* [Creare e rimuovere endpoint privati nei cluster di analisi di flusso](https://docs.microsoft.com/azure/stream-analytics/private-endpoints)
* [Connettersi a hub eventi in una VNet usando l'autenticazione di identità gestita](https://docs.microsoft.com/azure/stream-analytics/event-hubs-managed-identity)
* [Connettersi all'archiviazione BLOB e ADLS Gen2 in una VNet usando l'autenticazione di identità gestita](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity)
