---
title: Piattaforma Azure Industrial Internet
description: Questo articolo fornisce una panoramica della piattaforma Internet delle cose e dei relativi componenti.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: 9908c020fb987e951f150fa27bce7c1eba973f7b
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787918"
---
# <a name="what-is-the-industrial-iot-iiot-platform"></a>Che cos'è la piattaforma IIoT (Industrial tutto)?

La piattaforma Azure Industrial Internet è una suite Microsoft di moduli e servizi distribuiti in Azure. Questi moduli e servizi hanno completamente abbracciato l'apertura. In particolare, viene applicata la piattaforma distribuita come servizio (PaaS) di Azure, il software open source concesso in licenza tramite la licenza MIT, gli standard internazionali aperti per la comunicazione (OPC UA, AMQP, MQTT, HTTP) e le interfacce (Open API) e i modelli di dati industriali aperti (OPC UA) nel perimetro e nel cloud.

## <a name="enabling-shopfloor-connectivity"></a>Abilitazione della connettività produzione 

La piattaforma Azure Industrial Internet è basata sulla connettività industriale degli asset produzione (inclusa l'individuazione delle risorse abilitate per OPC UA), normalizza i dati in formato OPC UA e trasmette i dati di telemetria degli asset ad Azure nel formato PubSub OPC UA. Archivia i dati di telemetria in un database cloud. Inoltre, la piattaforma consente di accedere in modo sicuro alle risorse di produzione tramite OPC UA dal cloud. Anche le funzionalità di gestione dei dispositivi (inclusa la configurazione della sicurezza) sono integrate in. La funzionalità OPC UA è stata creata usando la tecnologia del contenitore Docker per semplificare la distribuzione e la gestione. Per le risorse abilitate per gli UA non OPC, abbiamo collaborato con i principali provider di connettività industriali e abbiamo aiutato a trasferire il software di adapter OPC UA a Azure IoT Edge. Questi adapter sono disponibili in Azure Marketplace.

## <a name="industrial-iot-components-iot-edge-modules-and-cloud-microservices"></a>Componenti dell'area industriale: moduli IoT Edge e microservizi cloud

I servizi Edge sono implementati come moduli Azure IoT Edge ed eseguiti in locale. I microservizi cloud vengono implementati come microservizi ASP.NET con un'interfaccia REST ed eseguiti in Servizi Kubernetes gestiti di Azure o autonomo nel servizio app Azure. Per i servizi perimetrali e cloud, sono stati forniti contenitori Docker predefiniti in Microsoft Container Registry (di Microsoft), rimuovendo questo passaggio per il cliente. I servizi perimetrali e cloud si avvaleno reciprocamente e devono essere usati insieme. Sono anche disponibili script di distribuzione di facile utilizzo che consentono di distribuire l'intera piattaforma con un unico comando.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso quale sia la piattaforma Azure Industrial Internet, è possibile ottenere informazioni sul server di pubblicazione OPC:

> [!div class="nextstepaction"]
> [Che cos'è il server di pubblicazione OPC?](overview-what-is-opc-publisher.md)