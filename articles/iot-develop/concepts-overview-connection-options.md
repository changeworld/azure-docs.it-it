---
title: Informazioni sulle opzioni di connessione per gli sviluppatori Azure IoT dispositivi
description: Informazioni sugli strumenti e sulle opzioni di connessione dei dispositivi di uso comune per Azure IoT sviluppatori di dispositivi.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 8669919192b1e6394043842d7d23f8829ec7c71e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589551"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>Panoramica: Opzioni di connessione per Azure IoT sviluppatori di dispositivi
Gli sviluppatori che lavorano con i dispositivi possono scegliere tra diverse opzioni per la connessione e la gestione Azure IoT dispositivi. Questo articolo fornisce una panoramica delle opzioni e degli strumenti usati più di frequente che consentono di connettere e gestire i dispositivi.

Quando si valutano Azure IoT di connessione per i dispositivi, è utile confrontare gli elementi seguenti:
- Azure IoT di applicazioni per dispositivi
- Strumenti per la connessione e la gestione dei dispositivi

## <a name="application-platforms-iot-central-and-iot-hub"></a>Piattaforme applicative: IoT Central e hub IoT
Azure IoT contiene due servizi che sono piattaforme per le applicazioni cloud abilitate per i dispositivi: Azure IoT Central e hub IoT di Azure. È possibile usare una delle due per ospitare un'applicazione IoT e connettere i dispositivi.
- [IoT Central](../iot-central/core/overview-iot-central.md) è progettato per ridurre la complessità e i costi di utilizzo delle soluzioni IoT. Si tratta di un'applicazione SaaS (Software-as-a-Service) che offre una piattaforma completa per l'hosting di applicazioni IoT. È possibile usare l'interfaccia IoT Central'interfaccia utente Web per semplificare l'intero ciclo di vita della creazione e della gestione di applicazioni IoT. L'interfaccia utente Web semplifica le attività di creazione di applicazioni e connessione e gestione da pochi fino a milioni di dispositivi. IoT Central usa l'hub IoT per creare e gestire le applicazioni, ma mantiene i dettagli trasparenti per l'utente. In generale, IoT Central complessità e attività di sviluppo ridotte e una gestione semplificata dei dispositivi tramite l'interfaccia utente Web.
- [L'hub IoT](../iot-hub/about-iot-hub.md) funge da hub messaggi centrale per la comunicazione bidirezionale tra le applicazioni IoT e i dispositivi connessi. Si tratta di un'applicazione PaaS (Platform-as-a-Service) che fornisce anche una piattaforma per l'hosting di applicazioni IoT. Come IoT Central, può essere ridimensionato per supportare milioni di dispositivi. In generale, l'hub IoT offre maggiore controllo e personalizzazione sulla progettazione dell'applicazione e più opzioni per gli strumenti di sviluppo per l'uso del servizio, a costo di un certo aumento della complessità di sviluppo e gestione.

## <a name="tools-to-connect-and-manage-devices"></a>Strumenti per connettere e gestire i dispositivi
Dopo aver selezionato l'hub IoT o IoT Central per ospitare l'applicazione IoT, sono disponibili diverse opzioni di strumenti di sviluppo. È possibile usare questi strumenti per connettersi alla piattaforma di applicazioni IoT selezionata e per creare e gestire applicazioni e dispositivi. Nella tabella seguente sono riepilogate le opzioni comuni degli strumenti. 

> [!NOTE]
> Oltre agli strumenti seguenti, è possibile creare e gestire applicazioni IoT a livello di codice usando i modelli di Azure Resource Manager SDK di Azure. Per altre informazioni, vedere la documentazione [dell'hub IoT](../iot-hub/about-iot-hub.md) [IoT Central](../iot-central/core/overview-iot-central.md) servizio.

|Strumento  |Supporta la piattaforma IoT &nbsp; &nbsp; &nbsp;&nbsp; |Documentazione  |Descrizione  |
|---------|---------|---------|---------|
|Interfaccia utente Web centrale     | Centro | [Guida introduttiva a Central](../iot-central/core/quick-deploy-iot-central.md) | Portale basato su browser per IoT Central. |
|Portale di Azure     | Hub, centrale      | [Creare un hub IoT con portale di Azure](../iot-hub/iot-hub-create-through-portal.md), [Gestire IoT Central dal portale di Azure](../iot-central/core/howto-manage-iot-central-from-portal.md)| Portale basato su browser per l'hub IoT e i dispositivi. Funziona anche con altre risorse di Azure, tra cui IoT Central. |
|Azure IoT Explorer     | Hub | [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer#azure-iot-explorer-preview) | Impossibile creare hub IoT. Si connette a un hub IoT esistente per gestire i dispositivi. Spesso usato con l'interfaccia della riga di comando o il portale.|
|Interfaccia della riga di comando di Azure     | Hub, Centrale          | [Creare un hub IoT con l'interfaccia della riga di comando,](../iot-hub/iot-hub-create-using-cli.md) [gestire IoT Central dall'interfaccia della riga di comando di Azure](../iot-central/core/howto-manage-iot-central-from-cli.md) | Interfaccia della riga di comando per la creazione e la gestione di applicazioni IoT. |
|Azure PowerShell     | Hub, centrale   | [Creare un hub IoT con PowerShell](../iot-hub/iot-hub-create-using-powershell.md), [gestire IoT Central da Azure PowerShell](../iot-central/core/howto-manage-iot-central-from-powershell.md) | Interfaccia di PowerShell per la creazione e la gestione di applicazioni IoT |
|Azure IoT Tools per VS Code  | Hub | [Creare un hub IoT con Strumenti per VS Code](../iot-hub/iot-hub-create-use-iot-toolkit.md) | VS Code per le applicazioni dell'hub IoT. |

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle opzioni per la connessione dei dispositivi Azure IoT, vedere le guide introduttive seguenti:
- IoT Central: Creare [un'Azure IoT Central applicazione](../iot-central/core/quick-deploy-iot-central.md)
- Hub IoT: inviare [dati di telemetria da un dispositivo a un hub IoT e monitorarlo con l'interfaccia della riga di comando di Azure](../iot-hub/quickstart-send-telemetry-cli.md)