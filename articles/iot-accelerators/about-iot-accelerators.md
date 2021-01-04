---
title: Introduzione agli acceleratori di soluzioni IoT - Azure | Microsoft Docs
description: Informazioni sugli acceleratori di soluzioni di Azure IoT. Gli acceleratori di soluzioni IoT sono soluzioni complete, end-to-end, pronte per la distribuzione.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: 5012383e64a85ee025273f5339b828f5338e1d4f
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629069"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Informazioni sugli acceleratori di soluzioni Azure IoT

Una soluzione IoT basata su cloud usa in genere codice personalizzato e servizi cloud per gestire la connettività dei dispositivi, l'elaborazione e l'analisi dei dati e la presentazione.

Gli acceleratori di soluzioni IoT sono soluzioni IoT complete pronte per la distribuzione che implementano scenari IoT comuni. Gli scenari includono la fabbrica connessa e la simulazione di dispositivi. Quando si implementa un acceleratore di soluzioni, la distribuzione include tutti i servizi basati su cloud necessari insieme a qualsiasi codice applicativo richiesto.

Gli acceleratori di soluzioni sono punti di partenza per le soluzioni IoT. Il codice sorgente per tutti gli acceleratori di soluzioni è open source ed è disponibile in GitHub. Si consiglia di scaricare e personalizzare acceleratori di soluzioni adatti alle proprie esigenze.

Gli acceleratori di soluzioni possono essere usati anche come strumenti di apprendimento prima di compilare una soluzione IoT personalizzata ex novo. Gli acceleratori di soluzioni implementano pratiche collaudate per le soluzioni IoT basate su cloud da seguire.

Il codice dell'applicazione in ogni acceleratore di soluzioni include un'app Web che consente di gestire l'acceleratore di soluzioni.

> [!NOTE]
> Le soluzioni di monitoraggio remoto e manutenzione predittiva sono state rimosse dal sito degli [acceleratori di soluzione Azure IoT](https://www.azureiotsolutions.com/Accelerators). Per altre informazioni, vedere [Panoramica degli acceleratori di soluzione di Azure IoT (versione precedente)](/previous-versions/azure/iot-accelerators/about-iot-accelerators).

## <a name="supported-iot-scenarios"></a>Scenari IoT supportati

Attualmente sono disponibili per la distribuzione due acceleratori di soluzione:

### <a name="connected-factory"></a>Connected Factory

Usare l'[acceleratore di soluzione Connected Factory](iot-accelerators-connected-factory-features.md) per raccogliere i dati di telemetria da asset industriali con un'interfaccia [OPC Unified Architecture](https://opcfoundation.org/about/opc-technologies/opc-ua/) e per controllarli. Gli asset industriali possono includere stazioni di assemblaggio e prova su una linea di produzione factory.

È possibile usare il dashboard di fabbrica connessa per monitorare e gestire i dispositivi industriali:

:::image type="content" source="./media/about-iot-accelerators/cf-dashboard-inline.png" alt-text="Screenshot che mostra il dashboard della soluzione Connected Factory." lightbox="./media/about-iot-accelerators/cf-dashboard-expanded.png":::

### <a name="device-simulation"></a>Simulazione dei dispositivi

Usare l'[acceleratore di soluzione Simulazione dispositivi](iot-accelerators-device-simulation-overview.md) per eseguire dispositivi simulati che generano dati di telemetria realistici. È possibile usare questo acceleratore di soluzioni per testare il comportamento degli altri acceleratori di soluzioni o per testare le proprie soluzioni IoT personalizzate.

È possibile usare l'app Web di simulazione dei dispositivi per configurare ed eseguire simulazioni:

:::image type="content" source="./media/about-iot-accelerators/ds-dashboard-inline.png" alt-text="Screenshot che mostra il dashboard della soluzione Simulazione dispositivi." lightbox="./media/about-iot-accelerators/ds-dashboard-expanded.png":::

## <a name="design-principles"></a>Principi di progettazione

Tutti gli acceleratori di soluzioni seguono gli stessi principi e obiettivi di progettazione. Sono progettati per essere:

* **Scalabili**, ovvero consentono di connettere e gestire milioni di dispositivi connessi.
* **Estendibili**, in quanto consentono di personalizzarli in modo da adattarli alle proprie esigenze.
* **Comprensibili**, in quanto consentono di capire come funzionano e come vengono implementati.
* **Modulari**, perché consentono di scambiare servizi con alternative.
* **Sicuri**, in quanto combinano la sicurezza di Azure con le funzioni di connettività integrata e di sicurezza del dispositivo.

## <a name="architectures-and-languages"></a>Architetture e lingue

Gli acceleratori di soluzioni originali sono stati scritti tramite .NET e un'architettura MVC (model-view-controller). Microsoft sta aggiornando gli acceleratori di soluzioni preconfigurati a una nuova architettura di microservizi. La tabella seguente illustra lo stato corrente degli acceleratori di soluzioni con collegamenti ai repository di GitHub:

| Acceleratore di soluzioni   | Architecture  | Languages     |
| ---------------------- | ------------- | ------------- |
| Connected Factory      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| Simulazione dei dispositivi      | Microservizi | [.NET](https://github.com/Azure/device-simulation-dotnet)          |

Per altre informazioni sull'architettura di microservizi, vedere [Introduction to the Azure IoT reference architecture](/azure/architecture/reference-architectures/iot/) (Introduzione all'architettura di riferimento di Azure IoT).

## <a name="deployment-options"></a>Opzioni di distribuzione

È possibile distribuire gli acceleratori di soluzioni dal sito [Acceleratori di soluzioni Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#) o tramite la riga di comando.

Il costo di esecuzione di un acceleratore di soluzione è un'aggregazione del [costo di esecuzione dei servizi di Azure sottostanti](https://azure.microsoft.com/pricing). Vengono visualizzati i dettagli dei servizi Azure usati quando si scelgono le opzioni di distribuzione.

## <a name="next-steps"></a>Passaggi successivi

Per provare uno degli acceleratori di soluzioni IoT, vedere la guida di avvio rapido [Provare una soluzione di fabbrica connessa](quickstart-connected-factory-deploy.md).
