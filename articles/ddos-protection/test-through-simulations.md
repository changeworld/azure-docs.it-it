---
title: Test di simulazione di protezione DDoS di Azure
description: Informazioni su come eseguire il test tramite simulazioni
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 4e6c6b441684d3877a8b85f40b650b257a5159da
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106072"
---
# <a name="test-through-simulations"></a>Testare con le simulazioni

È consigliabile testare le ipotesi su come i servizi risponderanno a un attacco effettuando simulazioni periodiche. Durante il test, verificare che i servizi o le applicazioni continuino a funzionare come previsto e che non ci siano interruzioni nell'esperienza dell'utente finale. Identificare le lacune sia dal punto di vista tecnologico che da quello dei processi e integrarle nella strategia di risposta DDoS. È consigliabile eseguire tali test in ambienti di staging o in ore non di punta per ridurre al minimo l'impatto sull'ambiente di produzione.

Microsoft ha collaborato con [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud), un generatore di traffico self-service, per creare un'interfaccia in cui i clienti di Azure possano generare traffico sugli endpoint pubblici con Protezione DDoS abilitata per le simulazioni. La simulazione può essere usata per:

- Convalidare il modo in cui Protezione DDoS di Azure contribuisce a proteggere le risorse di Azure dagli attacchi DDoS.
- Ottimizzare il processo di risposta agli eventi imprevisti durante un attacco DDoS.
- Documentare la conformità DDoS.
- Eseguire il training dei team di sicurezza di rete.

> [!NOTE]
> BreakingPoint cloud è disponibile solo per il cloud pubblico.

## <a name="prerequisites"></a>Prerequisiti

- Prima di eseguire i passaggi di questa esercitazione, è necessario creare prima di tutto un [piano di protezione standard DDoS di Azure](manage-ddos-protection.md) con indirizzi IP pubblici protetti.
- Per prima cosa, è necessario creare un account con [BreakingPoint cloud](http://breakingpoint.cloud/). 

## <a name="configure-a-ddos-test-attack"></a>Configurare un attacco di test DDoS

1. Immettere o selezionare i valori seguenti e quindi selezionare **Avvia test**:

    |Impostazione        |Valore                                              |
    |---------      |---------                                          |
    |Indirizzo IP di destinazione           | Immettere uno degli indirizzi IP pubblici che si desidera testare.                     |
    |Numero porta   | Immettere _443_.                       |
    |Profilo DDoS | I valori possibili sono `DNS Flood` ,, `NTPv2 Flood` `SSDP Flood` , `TCP SYN Flood` , `UDP 64B Flood` , `UDP 128B Flood` , `UDP 256B Flood` , `UDP 512B Flood` , `UDP 1024B Flood` , `UDP 1514B Flood` , `UDP Fragmentation` , `UDP Memcached` .|
    |Dimensioni test       | I valori possibili includono `100K pps, 50 Mbps and 4 source IPs` ,, `200K pps, 100 Mbps and 8 source IPs` `400K pps, 200Mbps and 16 source IPs` , `800K pps, 400 Mbps and 32 source IPs` .                                  |
    |Durata test | I valori possibili includono `10 Minutes` , `15 Minutes` , `20 Minutes` , `25 Minutes` , `30 Minutes` .|

Il punto dovrebbe essere simile al seguente:

![Esempio di simulazione di attacco DDoS: cloud BreakingPoint](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>Monitorare e convalidare

1. Accedere a https://portal.azure.com e andare alla sottoscrizione.
1. Selezionare l'indirizzo IP pubblico su cui è stato testato l'attacco.
1. In **Monitoraggio** selezionare **Metrica**.
1. Per **metrica** selezionare _sotto attacco DDoS o no_.

Quando la risorsa è sotto attacco, si noterà che il valore cambia da **0** a **1**, come nell'immagine seguente:

![Esempio di simulazione di attacco DDoS: portale](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

### <a name="breakingpoint-cloud-api-script"></a>Script API cloud BreakingPoint

Questo [script API](https://aka.ms/ddosbreakingpoint) può essere usato per automatizzare i test DDoS eseguendo una sola volta o usando cron per pianificare i test normali. Questa operazione è utile per verificare che la registrazione sia configurata correttamente e che le procedure di rilevamento e risposta siano effettive. Gli script richiedono un sistema operativo Linux (testato con Ubuntu 18,04 LTS) e Python 3. Installare i prerequisiti e il client API usando lo script incluso o usando la documentazione nel sito Web [BreakingPoint cloud](http://breakingpoint.cloud/) .

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [visualizzare e configurare la telemetria di protezione DDoS](telemetry.md).
- Informazioni su come [visualizzare e configurare la registrazione diagnostica DDoS](diagnostic-logging.md).
- Informazioni su come [coinvolgere la risposta rapida DDoS](ddos-rapid-response.md).
