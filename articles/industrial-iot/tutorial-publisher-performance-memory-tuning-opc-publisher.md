---
title: Ottimizzazione delle prestazioni e della memoria di Microsoft OPC Publisher
description: Questa esercitazione illustra come ottimizzare le prestazioni e la memoria del server di pubblicazione OPC.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 89e288d1186efd405019d6474dcbd332e7925d67
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787842"
---
# <a name="tutorial-tune-the-opc-publisher-performance-and-memory"></a>Esercitazione: ottimizzare le prestazioni e la memoria del server di pubblicazione OPC

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Regolazione delle prestazioni
> * Modificare il flusso dei messaggi nelle risorse di memoria

Quando si esegue il server di pubblicazione OPC nelle configurazioni di produzione, è necessario considerare i requisiti delle prestazioni di rete (velocità effettiva e latenza) e le risorse di memoria. Il server di pubblicazione OPC espone i seguenti parametri della riga di comando per soddisfare questi requisiti:

* Capacità della coda di messaggi ( `mq` per la versione 2,5 e versioni precedenti, non disponibile nella versione 2,6, `om` per la versione 2,7)
* Intervallo di invio dell'hub Internet ( `si` )
* Dimensioni messaggi dell'hub Internet ( `ms` )

## <a name="adjusting-iot-hub-send-interval-and-iot-hub-message-size"></a>Regolazione dell'intervallo di invio e delle dimensioni del messaggio dell'hub Internet

Il `mq/om` parametro controlla il limite massimo della capacità della coda di messaggi interna. Questa coda memorizza nel buffer tutti i messaggi prima che vengano inviati all'hub Internet. Le dimensioni predefinite della coda sono pari a 2 MB per il server di pubblicazione OPC versione 2,5 e al di sotto e 4000 messaggi dell'hub Internet per la versione 2,7 (ovvero se l'impostazione per la dimensione del messaggio dell'hub Internet è di 256 KB, le dimensioni della coda saranno fino a 1 GB). Se il server di pubblicazione OPC non è in grado di inviare messaggi all'hub di tutto abbastanza rapidamente, il numero di elementi in questa coda aumenta. Se questo errore si verifica durante le esecuzioni dei test, è possibile eseguire una o entrambe le operazioni seguenti per attenuare:

* ridurre l'intervallo di invio dell'hub Internet ( `si` )

* aumentare le dimensioni del messaggio dell'hub Internet ( `ms` , il valore massimo che può essere impostato è 256 KB)

Se la coda continua a crescere anche se `si` i `ms` parametri e sono stati modificati, alla fine verrà raggiunta la capacità massima della coda e i messaggi andranno persi. Questo è dovuto al fatto che il `si` `ms` parametro e ha limiti fisici e che la connessione Internet tra il server di pubblicazione OPC e l'hub Internet non è sufficientemente veloce per il numero di messaggi che devono essere inviati in uno scenario specifico. In tal caso, è possibile configurare solo diversi editori OPC paralleli. Il `mq/om` parametro ha anche il maggiore effetto sull'utilizzo della memoria da parte del server di pubblicazione OPC. 

Il parametro `si` impone a OPC Publisher di inviare messaggi all'hub IoT all'intervallo specificato. Un messaggio viene inviato quando è disponibile la dimensione massima del messaggio dell'hub di 256 KB di dati (che attiva l'intervallo di invio per la reimpostazione) o quando è trascorso l'intervallo di tempo specificato.

Il `ms` parametro consente la suddivisione in batch dei messaggi inviati all'hub Internet. Nella maggior parte delle configurazioni di rete, la latenza di invio di un singolo messaggio all'hub Internet è elevata rispetto al tempo necessario per trasmettere il payload. Ciò è dovuto principalmente ai requisiti di qualità del servizio (QoS), dal momento che i messaggi vengono riconosciuti solo dopo essere stati elaborati dall'hub. Se pertanto si accetta un ritardo per l'arrivo dei dati nell'hub degli spessori, OPC Publisher deve essere configurato in modo da usare la dimensione massima del messaggio di 256 KB impostando il `ms` parametro su 0. È anche il modo più conveniente per usare il server di pubblicazione OPC.

La configurazione predefinita Invia i dati all'hub delle cose ogni 10 secondi ( `si=10` ) o quando sono disponibili 256 KB di dati dei messaggi dell'hub Internet ( `ms=0` ). In questo modo viene aggiunto un ritardo massimo di 10 secondi, ma la probabilità di perdita dei dati è ridotta a causa della dimensione del messaggio di grandi dimensioni. La metrica `monitored item notifications enqueue failure`  in OPC Publisher versione 2,5 e versioni precedenti e `messages lost` in OPC publisher versione 2,7 indica il numero di messaggi persi.

Quando entrambi `si` i `ms` parametri e sono impostati su 0, il server di pubblicazione OPC Invia un messaggio all'hub delle cose non appena i dati sono disponibili. In questo modo si ottiene una dimensione media dei messaggi dell'hub Internet di circa 200 byte. Tuttavia, il vantaggio di questa configurazione è che il server di pubblicazione OPC invia i dati dall'asset connesso senza ritardo. Il numero di messaggi persi sarà elevato per i casi d'uso in cui è necessario pubblicare una grande quantità di dati e di conseguenza questa operazione non è consigliata per questi scenari.

Per misurare le prestazioni del server di pubblicazione OPC, `di` è possibile usare il parametro per stampare le metriche delle prestazioni nel log di traccia nell'intervallo specificato (in secondi).

## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come ottimizzare le prestazioni e la memoria del server di pubblicazione OPC, è possibile consultare il repository GitHub del server di pubblicazione OPC per altre risorse:

> [!div class="nextstepaction"]
> [Repository GitHub del server di pubblicazione OPC](https://github.com/Azure/Industrial-IoT)