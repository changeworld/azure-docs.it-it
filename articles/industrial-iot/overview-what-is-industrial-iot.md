---
title: Panoramica di Azure Industrially
description: Questo articolo fornisce una panoramica dell'intero settore. Vengono illustrati i componenti di sicurezza e connettività di shop floor in IIoT.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: 940391d26b5a8455fef01c8094cd08e05ab51290
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787951"
---
# <a name="what-is-industrial-iot-iiot"></a>Che cos'è un sacco industriale (IIoT)?

IIoT (Industrial Internet delle cose) migliora le efficienze industriali attraverso l'applicazione di Internet delle cose nel settore manifatturiero.

![Tutto industriale](media/overview-what-is-Industrial-IoT/icon-255-px.png)

## <a name="improve-industrial-efficiencies"></a>Migliorare l'efficienza industriale
Migliora la produttività operativa e la redditività con Azure Industrial Internet. Ridurre il processo che richiede molto tempo per l'accesso agli asset in sede. Connetti e monitora le apparecchiature e i dispositivi industriali nel cloud, inclusi i computer che già operano in fabbrica. Analizza i dati di Internet delle cose per ottenere informazioni dettagliate che consentono di migliorare le prestazioni dell'intero sito.

## <a name="industrial-iot-components"></a>Componenti dell'it industriale

**Dispositivi IOT Edge** Un dispositivo IoT Edge è costituito da moduli perimetrali e Runtime perimetrale. 
- I *moduli Edge* sono contenitori Docker, ovvero la più piccola unità di calcolo, ad esempio OPC Publisher e OPC gemello. 
- Il *dispositivo perimetrale* viene usato per distribuire tali moduli, che agiscono come Mediator tra il server OPC UA e l'hub Internet nel cloud. Altre informazioni su IoT Edge sono disponibili [qui](https://azure.microsoft.com/services/iot-edge/).

**Hub** Internet delle cose L'hub Internet delle cose funge da Hub messaggi centrale per la comunicazione bidirezionale tra l'applicazione Internet e i dispositivi gestiti. Si tratta di una piattaforma cloud aperta e flessibile distribuita come servizio che supporta SDK open source e più protocolli. Scopri di più [sull'hub Internet](https://azure.microsoft.com/services/iot-hub/).

**Moduli perimetrali industriali**
- *Server di pubblicazione OPC*: il server di pubblicazione OPC viene eseguito all'interno IOT Edge. Si connette ai server OPC UA e pubblica i dati di telemetria codificati JSON da questi server nel formato OPC UA "pubblicazione/sottoscrizione" nell'hub Azure. È possibile usare tutti i protocolli di trasporto supportati dall'SDK del client dell'hub Azure, ad esempio HTTPS, AMQP e MQTT.
- *OPC gemello*: il dispositivo OPC gemello è costituito da microservizi che usano Azure IOT Edge e l'hub Internet per connettere il cloud e la rete aziendale. OPC Twin offre funzionalità di individuazione, registrazione e controllo remoto dei dispositivi industriali tramite API REST. OPC gemello non richiede un SDK OPC UA (OPC Unified Architecture). È indipendente dal linguaggio di programmazione e può essere incluso in un flusso di lavoro senza server.
- *Individuazione*: il modulo di individuazione, rappresentato dall'identità del individuatore, fornisce servizi di individuazione sul perimetro, che includono l'individuazione del server OPC UA. Se l'individuazione è configurata e abilitata, il modulo invierà i risultati di un probe di analisi tramite il percorso di telemetria dell'hub IoT Edge e Internet al servizio di onboarding. Il servizio elabora i risultati e aggiorna tutte le identità correlate nel registro di sistema.


**Individuare, registrare e gestire le risorse industriali con Azure** Azure Industrial l'it consente agli operatori di stabilimento di individuare i server abilitati per OPC UA in una rete aziendale e di registrarli nell'hub Azure. Il personale operativo può sottoscrivere e reagire agli eventi in fabbrica da qualsiasi parte del mondo. Le API REST dei microservizi rispecchiano il lato perimetrale dei servizi OPC UA. Sono protetti tramite l'autenticazione OAUTH e l'autorizzazione supportata da Azure Active Directory (AAD). Questo consente alle applicazioni cloud di esplorare gli spazi di indirizzi del server o le variabili di lettura/scrittura ed eseguire i metodi usando HTTPS e i semplici payload di OPC UA JSON.

## <a name="next-steps"></a>Passaggi successivi
Ora che sono state apprese le informazioni sul settore industriale, è possibile leggere le informazioni sulla piattaforma Industrial Internet e sul server di pubblicazione OPC:

> [!div class="nextstepaction"]
> [Che cos'è la piattaforma Internet delle cose?](overview-what-is-industrial-iot-platform.md)

> [!div class="nextstepaction"]
> [Che cos'è il server di pubblicazione OPC?](overview-what-is-opc-publisher.md)