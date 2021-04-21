---
title: Panoramica di Azure Industrial IoT
description: Questo articolo offre una panoramica dell'IoT industriale. Illustra i componenti di connettività e sicurezza dei negozi in IIoT.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: ce582f810f483f2e5d3fdda2c3379ecad3842d51
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813273"
---
# <a name="what-is-industrial-iot-iiot"></a>Che cos'è Industrial IoT (IIoT)?

![Industrial Iot](media/overview-what-is-Industrial-IoT/icon-255-px.png)

Microsoft Azure Industrial Internet delle cose (IIoT) è una suite di moduli e servizi di Azure che integrano la potenza del cloud nei piani industriale e di produzione. Usando interfacce aperte standard del settore, ad esempio l'architettura unificata per le comunicazioni della piattaforma aperta [(OPC UA),](https://opcfoundation.org/about/opc-technologies/opc-ua/)Azure IIoT offre la possibilità di integrare nel cloud di Azure i dati di asset e sensori, inclusi quelli già operativi nella fabbrica. La presenza dei dati nel cloud consente di usare i dati in modo più rapido e flessibile come feedback per lo sviluppo di processi aziendali e industriali trasformativi.

## <a name="discover-register-and-manage-your-industrial-assets-with-azure"></a>Individuare, registrare e gestire gli asset industriali con Azure

Azure Industrial IoT consente agli operatori di impianti di individuare OPC UA server abilitati per l'utilizzo in una rete di fabbrica e di registrarli in hub IoT di Azure. Il personale operativo può sottoscrivere e reagire agli eventi nella fabbrica da qualsiasi parte del mondo, ricevere avvisi e allarmi e reagire in tempo reale.

IIoT fornisce un set di microservizi che implementano OPC UA funzionalità. Le API REST dei microservizi rispecchiano OPC UA sul lato perimetrale dei servizi. Vengono protette usando l'autenticazione OAUTH e l'autorizzazione supportate da Azure Active Directory (AAD). In questo modo le applicazioni cloud possono esplorare gli spazi degli indirizzi del server o le variabili di lettura/scrittura ed eseguire metodi usando HTTPS e payload JSON OPC UA semplici. I servizi perimetrali vengono implementati Azure IoT Edge moduli ed eseguiti in locale. I microservizi cloud vengono implementati come microservizi ASP.NET con un'interfaccia REST ed eseguiti in Servizi Azure Kubernetes gestiti o autonomi Servizio app di Azure. Per i servizi perimetrali e cloud, IIoT fornisce contenitori Docker predefiniti nel Microsoft Container Registry (MCR), rimuovendo questo passaggio per il cliente. I servizi perimetrali e cloud si stanno sfruttando a vicenda e devono essere usati insieme. IIoT fornisce anche script di distribuzione facili da usare che consentono di distribuire l'intera piattaforma con un unico comando.

Inoltre, le API REST possono essere usate con qualsiasi linguaggio di programmazione tramite la specifica Open API (Swagger) esposta. Ciò significa che quando si integrano OPC UA nelle soluzioni di gestione cloud, gli sviluppatori sono liberi di scegliere una tecnologia che corrisponda alle proprie competenze, interessi e scelte di architettura. Ad esempio, uno sviluppatore Web dello stack completo che sviluppa un'applicazione per un dashboard di avvisi ed eventi può scrivere la logica per rispondere agli eventi in JavaScript o TypeScript senza usare OPC UA SDK, C, C++, Java o C#.

## <a name="manage-certificates-and-trust-groups"></a>Gestire certificati e gruppi di attendibilità

Azure Industrial IoT gestisce i certificati OPC UA applicazioni e gli elenchi di attendibilità dei sistemi di controllo e dei macchinari della fabbrica per garantire OPC UA la comunicazione da client a server. Limita il client autorizzato a parlare con quale server. L'archiviazione delle chiavi private e la firma dei certificati sono supportate Azure Key Vault, che supporta la sicurezza basata su hardware.

## <a name="industrial-iot-components"></a>Industrial IoT Components

Le soluzioni IIoT di Azure sono compilate da componenti specifici. Tra questi sono inclusi gli elementi seguenti.

- **Almeno un hub IoT di Azure.**
- **IoT Edge dispositivi.**
- **Moduli perimetrali industriali.**

### <a name="iot-hub"></a>Hub IoT
[hub IoT di Azure]( funge da hub di messaggi centrale per comunicazioni bidirezionali sicure tra qualsiasi applicazione IoT e i https://azure.microsoft.com/services/iot-hub/ dispositivi gestiti. Si tratta di un modello di cloud platform as a service (PaaS) aperto e flessibile che supporta SDK open source e più protocolli. 

La raccolta dei dati industriali e aziendali in un hub IoT consente di archiviare i dati in modo sicuro, eseguire analisi aziendali ed efficienti su di esso e generare report da esso. È anche possibile applicare Microsoft Azure e strumenti di , ad esempio [Power BI](https://powerbi.microsoft.com), sui dati consolidati.

### <a name="iot-edge-devices"></a>IoT Edge dispositivi
I servizi perimetrali vengono implementati Azure IoT Edge moduli ed eseguiti in locale. [](https://azure.microsoft.com/services/iot-edge/) I microservizi cloud vengono implementati come microservizi ASP.NET con un'interfaccia REST ed eseguiti in Servizi Azure Kubernetes gestiti o autonomi Servizio app di Azure. Per i servizi perimetrali e cloud, sono stati forniti contenitori Docker predefiniti in Microsoft Container Registry (MCR), rimuovendo questo passaggio per il cliente. I servizi perimetrali e cloud si stanno sfruttando a vicenda e devono essere usati insieme. Sono stati anche forniti script di distribuzione facili da usare che consentono di distribuire l'intera piattaforma con un singolo comando.

Un IoT Edge dispositivo è costituito da moduli Edge Runtime e Edge.
- **I moduli** perimetrali sono contenitori Docker, che sono l'unità di calcolo più piccola, ad esempio OPC Publisher e OPC Twin. 
- **Il dispositivo edge** viene usato per distribuire tali moduli, che fungono da mediatore tra OPC UA server e l'hub IoT nel cloud.

### <a name="industrial-edge-modules"></a>Moduli perimetrali industriali
- **OPC Publisher:** il server di pubblicazione OPC viene eseguito all'interno IoT Edge. Si connette ai server OPC UA e pubblica i dati di telemetria con codifica JSON da questi server in OPC UA "Pub/Sub" per hub IoT di Azure. È possibile usare tutti i protocolli di trasporto supportati dall'SDK client hub IoT di Azure, ad esempio HTTPS, AMQP e MQTT.
- **OPC Twin:** il dispositivo gemello OPC è costituito da microservizi che usano Azure IoT Edge e l'hub IoT per connettere il cloud e la rete di fabbrica. OPC Twin offre funzionalità di individuazione, registrazione e controllo remoto dei dispositivi industriali tramite API REST. OPC Twin non richiede un SDK OPC Unified Architecture (OPC UA). È indipendente dal linguaggio di programmazione e può essere incluso in un flusso di lavoro serverless.
- **Individuazione:** il modulo di individuazione, rappresentato dall'identità dell'individuazione, fornisce servizi di individuazione sul perimetro, che includono OPC UA individuazione server. Se l'individuazione è configurata e abilitata, il modulo invierà i risultati di un probe di analisi tramite il percorso di telemetria dell'IoT Edge e dell'hub IoT al servizio di onboarding. Il servizio elabora i risultati e aggiorna tutte le identità correlate nel Registro di sistema.

## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso cos'è l'IoT industriale, è possibile leggere le informazioni sulla piattaforma IoT industriale e sull'editore OPC:

> [!div class="nextstepaction"]
> [Che cos'è il server di pubblicazione OPC?](overview-what-is-opc-publisher.md)