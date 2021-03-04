---
title: Aggregazione di eventi
titleSuffix: Azure Defender for IoT
description: Defender per gli agenti di sicurezza Internet raccoglie gli eventi di dati e di sistema dal dispositivo locale e invia i dati al cloud di Azure per l'elaborazione e l'analisi.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: azure
ms.openlocfilehash: 53811c20330d96b279eeb24e1aa90248b60fcb32
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120046"
---
# <a name="event-aggregation"></a>Aggregazione di eventi 

Defender per gli agenti di sicurezza Internet raccoglie gli eventi di dati e di sistema dal dispositivo locale e invia i dati al cloud di Azure per l'elaborazione e l'analisi. Il Defender per l'agente del Micro Internet raccoglie molti tipi di eventi del dispositivo, inclusi i nuovi processi e tutti i nuovi eventi di connessione. Sia il nuovo processo che i nuovi eventi di connessione possono verificarsi di frequente in un dispositivo entro un secondo. Questa funzionalità è importante per la sicurezza completa, tuttavia, il numero di messaggi inviati dagli agenti di sicurezza può essere rapidamente soddisfatto o superare i limiti di quota e di costo dell'hub. Tuttavia, questi eventi contengono informazioni di sicurezza estremamente utili, cruciali per la protezione del dispositivo. 

Per ridurre la quota aggiuntiva e i costi mantenendo protetti i dispositivi, il Defender per gli agenti Internet aggrega questi tipi di eventi: 

- ProcessCreate (solo Linux) 

- ConnectionCreate (solo Azure RTO) 

## <a name="how-does-event-aggregation-work"></a>Funzionamento dell'aggregazione di eventi 

Defender per gli agenti degli eventi di aggregazione per il periodo di tempo o per l'intervallo di tempo. Una volta trascorso il periodo di intervallo, l'agente invia gli eventi aggregati al cloud di Azure per un'ulteriore analisi. Gli eventi aggregati vengono archiviati in memoria fino a quando non vengono inviati al cloud di Azure. 

Quando l'agente raccoglie un evento identico a uno già mantenuto in memoria, l'agente aumenta il numero di passaggi di questo evento specifico, in modo da ridurre il footprint di memoria dell'agente. Quando viene passato l'intervallo di tempo di aggregazione, l'agente invia il numero di passaggi di ogni tipo di evento che si è verificato. L'aggregazione di eventi è semplicemente l'aggregazione dei conteggi dei passaggi di ogni tipo di evento raccolto. 

## <a name="process-events"></a>Elaborare eventi 

Gli eventi di elaborazione sono attualmente supportati solo nei sistemi operativi Linux. 

Gli eventi di processo sono considerati identici quando la *riga di comando* e  *userid*   sono identici. 

Il buffer predefinito per gli eventi di elaborazione è 32 processi, dopo i quali verrà eseguito il ciclo del buffer e gli eventi di processo meno recenti verranno eliminati per fare spazio ai nuovi eventi di processo.  

## <a name="network-connection-events"></a>Eventi connessione di rete 

Gli eventi di connessione di rete sono attualmente supportati solo in Azure RTO. 

Gli eventi di connessione di rete sono considerati identici quando la *porta locale*, la  *porta remota*, il  *protocollo di trasporto*, l' *indirizzo locale* e l' *indirizzo remoto* sono identici. 

Il buffer predefinito per gli eventi di connessione di rete è 64. Nessun nuovo evento di rete verrà memorizzato nella cache fino al ciclo di raccolta successivo. Verrà registrato un avviso per aumentare le dimensioni della cache.

## <a name="next-steps"></a>Passaggi successivi

Controllare il [Defender per gli avvisi di sicurezza](concept-security-alerts.md).
