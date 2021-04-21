---
title: Microsoft OPC Publisher
description: Questo articolo offre una panoramica del modulo OPC Publisher Edge.
author: v-condav
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 6df39c93e9bcfca522ac61a863c87269216cc592
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816171"
---
# <a name="what-is-the-opc-publisher"></a>Che cos'è il server di pubblicazione OPC?

OPC Publisher è un prodotto Microsoft completamente supportato che colma il divario tra asset industriali e Microsoft Azure cloud. A tale scopo, connette OPC UA asset abilitati o software di connettività industriale al cloud Microsoft Azure. Pubblica i dati di telemetria raccolti in hub IoT di Azure in vari formati, incluso il formato standard IEC62541 OPC UA PubSub (a partire dalla versione 2.6). OPC Publisher viene eseguito in Azure IoT Edge come modulo o come contenitore in Un normale Docker. Poiché sfrutta il runtime multipiattaforma .NET, viene eseguito in modo nativo sia in Linux che Windows 10.

OPC Publisher è un'implementazione di riferimento che illustra come:

- Connettersi ai server OPC UA esistenti.
- Pubblicare i dati di telemetria con codifica JSON da OPC UA server in formato OPC UA pub/sub, usando un payload JSON, in un hub IoT di Azure.

È possibile usare qualsiasi protocollo di trasporto hub IoT di Azure CLIENT SDK, ad esempio HTTPS, AMQP e MQTT.

L'implementazione di riferimento include quanto segue.

- Un *client* OPC UA per la connessione ai server OPC UA esistenti nella rete.
- Un *server* OPC UA sulla porta 62222 che consente di gestire i contenuti pubblicati e offre metodi diretti dell'hub IoT per eseguire le stesse operazioni.

È possibile scaricare l'[implementazione di riferimento di OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) da GitHub.

L'applicazione viene implementata usando la tecnologia .NET Core e può essere eseguita su qualsiasi piattaforma supportata da .NET Core.

## <a name="what-does-the-opc-publisher-do"></a>Quali sono le funzioni del server di pubblicazione OPC?

OPC Publisher implementa la logica di ripetizione dei tentativi per stabilire connessioni agli endpoint che non rispondono a un certo numero di richieste keep-alive, ad esempio se un server OPC UA smette di rispondere a causa di un'interruzione dell'alimentazione.

Per ogni singolo intervallo di pubblicazione in un server OPC UA, l'applicazione crea una sottoscrizione separata in cui vengono aggiornati tutti i nodi con questo intervallo di pubblicazione.

Per ridurre il carico di rete, OPC Publisher supporta l'invio in batch dei dati all'hub IoT. Questa tecnica di invio in batch invia un pacchetto all'hub IoT solo se viene raggiunta la dimensione configurata del pacchetto.

Questa applicazione usa lo stack di riferimento OPC UA di OPC Foundation come pacchetti NuGet. Vedere [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) per le condizioni di licenza.

## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso che cos'è il server di pubblicazione OPC, è possibile iniziare distribuerlo:

> [!div class="nextstepaction"]
> [Distribuire OPC Publisher in modalità autonoma](tutorial-publisher-deploy-opc-publisher-standalone.md)
