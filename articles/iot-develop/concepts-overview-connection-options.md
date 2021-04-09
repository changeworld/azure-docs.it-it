---
title: Informazioni sulle opzioni di connessione per gli sviluppatori di dispositivi Azure Internet
description: Informazioni sugli strumenti e sulle opzioni di connessione del dispositivo comunemente usati per gli sviluppatori di dispositivi Azure.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 6bbd7d37418af68065daa194d4ff4bd80f6fd09c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654366"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>Panoramica: opzioni di connessione per gli sviluppatori di dispositivi Azure Internet
Gli sviluppatori che lavorano con i dispositivi sono disponibili diverse opzioni per la connessione e la gestione dei dispositivi Azure. Questo articolo illustra le opzioni e gli strumenti usati più di frequente per semplificare la connessione e la gestione dei dispositivi.

Quando si valutano le opzioni di connessione di Azure per i dispositivi, è utile confrontare gli elementi seguenti:
- Piattaforme per le applicazioni per dispositivi Azure
- Strumenti per la connessione e la gestione dei dispositivi

## <a name="application-platforms-iot-central-and-iot-hub"></a>Piattaforme applicative: IoT Central e hub Internet
Azure Internet è dotato di due servizi che sono piattaforme per le applicazioni cloud abilitate per i dispositivi: Azure IoT Central e l'hub Azure. È possibile usare uno dei due per ospitare un'applicazione Internet e connettere i dispositivi.
- [IOT Central](../iot-central/core/overview-iot-central.md) è progettato per ridurre la complessità e i costi di utilizzo delle soluzioni Internet delle cose. Si tratta di un'applicazione SaaS (software-as-a-Service) che offre una piattaforma completa per l'hosting di applicazioni Internet. È possibile usare l'interfaccia utente Web di IoT Central per semplificare l'intero ciclo di vita della creazione e della gestione delle applicazioni Internet. L'interfaccia utente Web semplifica le attività di creazione delle applicazioni e la connessione e la gestione da pochi a milioni di dispositivi. IoT Central USA l'hub Internet per creare e gestire le applicazioni, ma mantiene i dettagli trasparenti per l'utente. In generale, IoT Central offre una riduzione della complessità e dello sviluppo e semplifica la gestione dei dispositivi tramite l'interfaccia utente Web.
- L' [Hub](../iot-hub/about-iot-hub.md) Internet viene utilizzato come hub di messaggi centrale per la comunicazione bidirezionale tra le applicazioni Internet e i dispositivi connessi. Si tratta di un'applicazione di piattaforma distribuita come servizio (PaaS) che fornisce anche una piattaforma per l'hosting di applicazioni Internet. Come IoT Central, può essere ridimensionato per supportare milioni di dispositivi. In generale, l'hub Internet offre maggiore controllo e personalizzazione sulla progettazione dell'applicazione e più opzioni dello strumento di sviluppo per l'utilizzo del servizio, a scapito di un aumento della complessità di sviluppo e gestione.

## <a name="tools-to-connect-and-manage-devices"></a>Strumenti per la connessione e la gestione dei dispositivi
Dopo aver selezionato l'hub Internet o IoT Central per ospitare l'applicazione Internet delle cose, sono disponibili diverse opzioni per gli strumenti di sviluppo. È possibile usare questi strumenti per connettersi alla piattaforma dell'applicazione Internet e per creare e gestire applicazioni e dispositivi. Nella tabella seguente sono riepilogate le opzioni comuni degli strumenti. 

> [!NOTE]
> Oltre agli strumenti seguenti, è possibile creare e gestire a livello di codice le applicazioni Internet usando API REST, Azure SDK o modelli Azure Resource Manager. Altre informazioni sono disponibili nella documentazione sull' [Hub](../iot-hub/about-iot-hub.md) e [IOT Central](../iot-central/core/overview-iot-central.md) Service.

|Strumento  |Supporta la piattaforma &nbsp; &nbsp; &nbsp; Internet delle cose &nbsp; |Documentazione  |Descrizione  |
|---------|---------|---------|---------|
|Interfaccia utente Web centrale     | Centro | [Guida introduttiva centrale](../iot-central/core/quick-deploy-iot-central.md) | Portale basato su browser per IoT Central. |
|Portale di Azure     | Hub, centrale      | [Creare un hub](../iot-hub/iot-hub-create-through-portal.md)Internet delle cose con portale di Azure, [gestire IOT Central dalla portale di Azure](../iot-central/core/howto-manage-iot-central-from-portal.md)| Portale basato su browser per l'hub e i dispositivi. Funziona anche con altre risorse di Azure, tra cui IoT Central. |
|Interfaccia della riga di comando di Azure     | Hub, centrale          | [Creare un hub Internet con interfaccia](../iot-hub/iot-hub-create-using-cli.md)della riga di comando, [gestire IOT Central dall'interfaccia della riga di](../iot-central/core/howto-manage-iot-central-from-cli.md) comando | Interfaccia della riga di comando per la creazione e la gestione delle applicazioni Internet. |
|Azure PowerShell     | Hub, centrale   | [Creare un hub](../iot-hub/iot-hub-create-using-powershell.md)Internet delle cose con PowerShell, [gestire IOT Central da Azure PowerShell](../iot-central/core/howto-manage-iot-central-from-powershell.md) | Interfaccia di PowerShell per la creazione e la gestione di applicazioni Internet |
|Azure IoT Tools per VS Code  | Hub | [Creare un hub Internet delle cose con gli strumenti per VS Code](../iot-hub/iot-hub-create-use-iot-toolkit.md) | Estensione VS Code per le applicazioni dell'hub Internet. |
|Esplora risorse di Azure     | Hub | [Esplora risorse di Azure](https://github.com/Azure/azure-iot-explorer) | Non è possibile creare hub Internet. Si connette a un hub Internet esistente per gestire i dispositivi. Usato spesso con CLI o portale.|

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulle opzioni per la connessione dei dispositivi ad Azure, vedere le guide introduttive seguenti:
- IoT Central: [creare un'applicazione IoT Central di Azure](../iot-central/core/quick-deploy-iot-central.md)
- Hub Internet: [inviare dati di telemetria da un dispositivo a un hub Internet e monitorarli con l'interfaccia della riga di](../iot-hub/quickstart-send-telemetry-cli.md) comando di Azure