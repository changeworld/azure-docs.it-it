---
title: Monitorare la connettività dei dispositivi con Azure IoT Central Explorer
description: Monitorare i messaggi dei dispositivi e osservare le modifiche del dispositivo gemello usando l'interfaccia della riga di comando di IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: devx-track-azurecli, device-developer
services: iot-central
manager: corywink
ms.openlocfilehash: 30a823b7e78145224929b427e9e37522a7e29f09
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871292"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Monitorare la connettività dei dispositivi con l'interfaccia della riga di comando di Azure

*Questo argomento si applica agli sviluppatori di dispositivi e ai generatori di soluzioni.*

Usare l'estensione IoT dell'interfaccia della riga di comando di Azure per visualizzare i messaggi inviati ai dispositivi IoT Central e osservare le modifiche nel dispositivo gemello. È possibile usare questo strumento per eseguire il debug e osservare la connettività dei dispositivi e diagnosticare i problemi di messaggi del dispositivo che non raggiungono il cloud o dispositivi che non rispondono alle modifiche dei dispositivi gemelli.

[Per altri dettagli, vedere le informazioni di riferimento sulle estensioni dell'interfaccia della riga di comando di Azure](/cli/azure/iot/central)

## <a name="prerequisites"></a>Prerequisiti

+ L'interfaccia della riga di comando di Azure è installata ed è versione 2.7.0 o successiva. Controllare la versione dell'interfaccia della riga di comando di Azure eseguendo `az --version` . Informazioni su come installare e aggiornare dalla documentazione dell'interfaccia della riga [di comando di Azure](/cli/azure/install-azure-cli)
+ Un account aziendale o dell'istituto di istruzione in Azure, aggiunto come utente in un'applicazione IoT Central aziendale.

## <a name="install-the-iot-central-extension"></a>Installare l'IoT Central predefinita

Eseguire il seguente comando dalla riga di comando per l'installazione:

```azurecli
az extension add --name azure-iot
```

Controllare la versione dell'estensione eseguendo:

```azurecli
az --version
```

L'estensione azure-iot dovrebbe essere 0.9.9 o successiva. In caso contrario, eseguire:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Uso dell'estensione

Le sezioni seguenti descrivono i comandi e le opzioni comuni che è possibile usare quando si esegue `az iot central` . Per visualizzare il set completo di comandi e opzioni, passare `--help` a o a uno dei relativi `az iot central` sottocomandi.

### <a name="login"></a>Accesso

Per iniziare, accedere all'interfaccia della riga di comando di Azure. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Ottenere l'ID applicazione dell'app IoT Central app
In **Amministrazione/Impostazioni applicazione** copiare **l'ID applicazione**. Questo valore verrà utilizzato nei passaggi successivi.

### <a name="monitor-messages"></a>Monitoraggio dei messaggi
Monitorare i messaggi inviati all'app IoT Central dai dispositivi. L'output include tutte le intestazioni e le annotazioni.

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Visualizzare le proprietà del dispositivo
Visualizzare le proprietà correnti dei dispositivi di lettura e lettura/scrittura per un determinato dispositivo.

```azurecli
az iot central device twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Passaggi successivi

Per gli sviluppatori di dispositivi, è consigliabile leggere le informazioni sulla connettività dei dispositivi [in Azure IoT Central](./concepts-get-connected.md).