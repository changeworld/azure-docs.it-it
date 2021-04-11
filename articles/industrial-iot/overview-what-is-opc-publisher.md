---
title: Publisher Microsoft OPC
description: Questo articolo fornisce una panoramica del modulo OPC Publisher Edge.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 3a44bdbadfe6ecd86a1b98fb7002f2d75c23bb6a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800534"
---
# <a name="what-is-the-opc-publisher"></a>Che cos'è il server di pubblicazione OPC?

Publisher OPC è un prodotto Microsoft completamente supportato, sviluppato in modo aperto, che colma il divario tra le risorse industriali e il cloud Microsoft Azure. Questa operazione viene eseguita connettendosi alle risorse abilitate per OPC UA o al software di connettività industriale e pubblica i dati di telemetria nell'hub Azure Internet in diversi formati, tra cui IEC62541 OPC UA PubSub Standard Format (dalla versione 2,6 in poi).

Viene eseguito in Azure IoT Edge come un modulo o in un Docker normale come contenitore. Poiché si avvale del runtime .NET multipiattaforma, viene eseguito anche in modalità nativa in Linux e Windows 10.

OPC Publisher è un'implementazione di riferimento che illustra come:

- Connettersi ai server OPC UA esistenti.
- Pubblicare i dati di telemetria codificati JSON dai server OPC UA (in formato Pub/Sub OPC UA) nell'hub IoT di Azure, usando un payload JSON.

È possibile usare uno dei protocolli di trasporto supportati dall'SDK client dell'hub IoT di Azure: HTTPS, AMQP e MQTT.

L'implementazione di riferimento include:

- Un *client* OPC UA per la connessione ai server OPC UA esistenti nella rete.
- Un *server* OPC UA sulla porta 62222 che consente di gestire i contenuti pubblicati e offre metodi diretti dell'hub IoT per eseguire le stesse operazioni.

È possibile scaricare l'[implementazione di riferimento di OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) da GitHub.

L'applicazione viene implementata usando la tecnologia .NET Core e può essere eseguita su qualsiasi piattaforma supportata da .NET Core.

## <a name="what-does-the-opc-publisher-do"></a>Che cosa fa il server di pubblicazione OPC?

OPC Publisher implementa la logica di ripetizione dei tentativi per stabilire connessioni agli endpoint che non rispondono a un certo numero di richieste keep-alive, ad esempio se un server OPC UA smette di rispondere a causa di un'interruzione dell'alimentazione.

Per ogni singolo intervallo di pubblicazione in un server OPC UA, l'applicazione crea una sottoscrizione separata in cui vengono aggiornati tutti i nodi con questo intervallo di pubblicazione.

Per ridurre il carico di rete, OPC Publisher supporta l'invio in batch dei dati all'hub IoT. Questa tecnica di invio in batch invia un pacchetto all'hub IoT solo se viene raggiunta la dimensione configurata del pacchetto.

Questa applicazione usa lo stack di riferimento OPC UA di OPC Foundation come pacchetti NuGet. Vedere [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) per le condizioni di licenza.

## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come è il server di pubblicazione OPC, è possibile iniziare a distribuirlo:

> [!div class="nextstepaction"]
> [Distribuire il server di pubblicazione OPC in modalità autonoma](tutorial-publisher-deploy-opc-publisher-standalone.md)
