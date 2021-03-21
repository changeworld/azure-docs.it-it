---
title: Inserire i dati di telemetria dall'hub IoT
titleSuffix: Azure Digital Twins
description: Vedere come inserire i messaggi di telemetria del dispositivo dall'hub Internet.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 9/15/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3223a1c8e20d8b0caced5d940132c32fa0aba97c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149089"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Inserire dati di telemetria dell'hub Internet in dispositivi gemelli digitali di Azure

I dispositivi gemelli digitali di Azure sono basati sui dati dei dispositivi e di altre origini. Un'origine comune per i dati dei dispositivi da usare nei dispositivi gemelli digitali di Azure è l' [Hub](../iot-hub/about-iot-hub.md).

Il processo di inserimento dei dati nei dispositivi gemelli digitali di Azure consiste nel configurare una risorsa di calcolo esterna, ad esempio una funzione eseguita usando [funzioni di Azure](../azure-functions/functions-overview.md). La funzione riceve i dati e usa le [API DigitalTwins](/rest/api/digital-twins/dataplane/twins) per impostare le proprietà o generare eventi di telemetria sui dispositivi [gemelli digitali](concepts-twins-graph.md) di conseguenza. 

Questo documento illustra il processo di scrittura di una funzione che può inserire dati di telemetria dall'hub Internet.

## <a name="prerequisites"></a>Prerequisiti

Prima di continuare con questo esempio, è necessario configurare le risorse seguenti come prerequisiti:
* **Un hub** Internet. Per istruzioni, vedere la sezione *creare un hub* per gli Internet in [questa Guida introduttiva sull'hub](../iot-hub/quickstart-send-telemetry-cli.md).
* **Un'istanza di gemelli digitale di Azure** che riceverà i dati di telemetria del dispositivo. Per istruzioni, vedere [*procedura: configurare un'istanza di dispositivi gemelli digitali di Azure e l'autenticazione*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Scenario di telemetria di esempio

Questa procedura illustra come inviare messaggi dall'hub degli Internet ai dispositivi gemelli di Azure, usando una funzione in Azure. Sono disponibili molte possibili configurazioni e strategie di corrispondenza che è possibile usare per l'invio di messaggi, ma l'esempio per questo articolo contiene le parti seguenti:
* Un dispositivo termostato nell'hub Internet delle cose, con un ID dispositivo noto
* Un dispositivo gemello digitale per rappresentare il dispositivo, con un ID corrispondente

> [!NOTE]
> Questo esempio usa una corrispondenza con ID semplice tra l'ID del dispositivo e un ID del gemello digitale corrispondente, ma è possibile fornire mapping più sofisticati dal dispositivo al relativo gemello, ad esempio con una tabella di mapping.

Ogni volta che un evento di telemetria della temperatura viene inviato dal dispositivo termostato, una funzione elabora i dati di telemetria e la proprietà *temperature* del gemello digitale dovrebbe aggiornare. Questo scenario è illustrato in un diagramma seguente:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Diagramma che mostra un diagramma di flusso. Nel grafico, un dispositivo hub di Internet delle cose invia i dati di telemetria della temperatura attraverso l'hub Internet a una funzione in Azure, che aggiorna una proprietà di temperatura in un gemello in dispositivi gemelli digitali di Azure." border="false":::

## <a name="add-a-model-and-twin"></a>Aggiungere un modello e un gemello

In questa sezione si configurerà un dispositivo [gemello digitale](concepts-twins-graph.md) in Azure Digital gemelli che rappresenterà il dispositivo termostato e verrà aggiornato con le informazioni dell'hub Internet.

Per creare un dispositivo gemello, è necessario innanzitutto caricare il [modello](concepts-models.md) del termostato nell'istanza, che descrive le proprietà di un termostato e verrà usato in un secondo momento per creare il dispositivo gemello. 

Il modello ha un aspetto simile al seguente:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Per **caricare il modello nell'istanza dei dispositivi gemelli**, eseguire il comando dell'interfaccia della riga di comando di Azure seguente, che consente di caricare il modello precedente come JSON inline. È possibile eseguire il comando in [Azure cloud Shell](/cloud-shell/overview.md) nel browser o nel computer se l'interfaccia della riga di comando è [installata localmente](/cli/azure/install-azure-cli.md).

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

Sarà quindi necessario **creare un gemello usando questo modello**. Usare il comando seguente per creare un gemello del termostato denominato **thermostat67** e impostare 0,0 come valore di temperatura iniziale.

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

>[!NOTE]
> Se si usa Cloud Shell nell'ambiente PowerShell, potrebbe essere necessario eseguire l'escape dei caratteri di virgoletta nei campi JSON inline affinché i relativi valori vengano analizzati correttamente. Ecco i comandi per caricare il modello e creare il gemello con questa modifica:
>
> Modello di caricamento:
> ```azurecli-interactive
> az dt model create --models '{  \"@id\": \"dtmi:contosocom:DigitalTwins:Thermostat;1\",  \"@type\": \"Interface\",  \"@context\": \"dtmi:dtdl:context;2\",  \"contents\": [    {      \"@type\": \"Property\",      \"name\": \"Temperature\",      \"schema\": \"double\"    }  ]}' -n {digital_twins_instance_name}
> ```
>
> Creazione di un dispositivo gemello:
> ```azurecli-interactive
> az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{\"Temperature\": 0.0,}' --dt-name {digital_twins_instance_name}
> ```

Quando il gemello viene creato correttamente, l'output dell'interfaccia della riga di comando del comando dovrebbe avere un aspetto simile al seguente:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-a-function"></a>Creare una funzione

In questa sezione si creerà una funzione di Azure per accedere ai dispositivi gemelli digitali di Azure e a aggiornare i dispositivi gemelli in base agli eventi di telemetria che riceve. Per creare e pubblicare la funzione, attenersi alla procedura riportata di seguito.

#### <a name="step-1-create-a-function-app-project"></a>Passaggio 1: creare un progetto di app per le funzioni

Per prima cosa, creare un nuovo progetto di app per le funzioni in Visual Studio. Per istruzioni su come eseguire questa operazione, vedere la sezione [**creare un'app per le funzioni in Visual Studio**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) dell'articolo *procedura: configurare una funzione per l'elaborazione dei dati* .

#### <a name="step-2-fill-in-function-code"></a>Passaggio 2: compilare il codice della funzione

Aggiungere i pacchetti seguenti al progetto:
* [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure. Identity](https://www.nuget.org/packages/Azure.Identity/)
* [Microsoft. Azure. webjobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid/)

Rinominare la funzione di esempio *funzione1. cs* generata da Visual Studio con il nuovo progetto in *IoTHubtoTwins. cs*. Sostituire il codice nel file con il codice seguente:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

Salvare il codice della funzione.

#### <a name="step-3-publish-the-function-app-to-azure"></a>Passaggio 3: pubblicare l'app per le funzioni in Azure

Pubblicare il progetto in un'app per le funzioni in Azure.

Per istruzioni su come eseguire questa operazione, vedere la sezione [**pubblicare l'app per le funzioni in Azure**](how-to-create-azure-function.md#publish-the-function-app-to-azure) dell'articolo *procedura: configurare una funzione per l'elaborazione dei dati* .

#### <a name="step-4-configure-the-function-app"></a>Passaggio 4: configurare l'app per le funzioni

Assegnare quindi **un ruolo di accesso** per la funzione e **configurare le impostazioni dell'applicazione** in modo che possa accedere all'istanza di Azure Digital gemelli. Per istruzioni su come eseguire questa operazione, vedere la sezione [**configurare l'accesso di sicurezza per l'app per le funzioni**](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) dell'articolo *procedura: configurare una funzione per l'elaborazione dei dati* .

## <a name="connect-your-function-to-iot-hub"></a>Connettere la funzione all'hub Internet

In questa sezione verrà configurata la funzione come destinazione dell'evento per i dati del dispositivo dell'hub Internet. In questo modo, i dati del dispositivo termostato nell'hub di gestione delle cose verranno inviati alla funzione di Azure per l'elaborazione.

Nella [portale di Azure](https://portal.azure.com/)passare all'istanza dell'hub Internet delle cose creata nella sezione [*prerequisiti*](#prerequisites) . In **eventi** creare una sottoscrizione per la funzione.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Screenshot del portale di Azure che mostra l'aggiunta di una sottoscrizione di eventi.":::

Nella pagina **Crea sottoscrizione evento** compilare i campi come indicato di seguito:
  1. Per **nome** scegliere il nome desiderato per la sottoscrizione di eventi.
  2. Per **schema evento** scegliere _schema griglia di eventi_.
  3. Per **nome argomento sistema**, scegliere il nome desiderato.
  1. Per **filtrare i tipi di evento**, scegliere la casella di controllo _telemetria del dispositivo_ e deselezionare altri tipi di evento.
  1. Per **tipo di endpoint** selezionare _funzione di Azure_.
  1. Per **endpoint**, usare il collegamento _selezionare un endpoint_ per scegliere la funzione di Azure da usare per l'endpoint.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="Screenshot della portale di Azure per creare i dettagli della sottoscrizione di eventi":::

Nella pagina _Seleziona funzione di Azure_ visualizzata verificare o inserire i dettagli seguenti.
 1. **Sottoscrizione** La sottoscrizione di Azure.
 2. **Gruppo** di risorse: il gruppo di risorse.
 3. **App** per le funzioni: nome dell'app per le funzioni.
 4. **Slot**: _produzione_.
 5. **Funzione**: selezionare la funzione precedente, *IoTHubtoTwins*, dall'elenco a discesa.

Salvare i dettagli con il pulsante _conferma selezione_ .            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="Screenshot del portale di Azure per selezionare la funzione.":::

Selezionare il pulsante _Crea_ per creare la sottoscrizione di eventi.

## <a name="send-simulated-iot-data"></a>Inviare dati dell'Internet delle cose simulati

Per testare la nuova funzione di ingresso, usare il simulatore di dispositivi da [*esercitazione: connettere una soluzione end-to-end*](./tutorial-end-to-end.md). Questa esercitazione è basata su un progetto di esempio scritto in C#. Il codice di esempio è disponibile qui: [esempi end-to-end di Azure Digital gemelli](/samples/azure-samples/digital-twins-samples/digital-twins-samples). Si userà il progetto **DeviceSimulator** in tale repository.

Nell'esercitazione end-to-end completare i passaggi seguenti:
1. [*Registrare il dispositivo simulato con l'hub IoT*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*Configurare ed eseguire la simulazione*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Convalidare i risultati

Quando si esegue il simulatore di dispositivi sopra riportato, il valore della temperatura del gemello digitale verrà modificato. Nell'interfaccia della riga di comando di Azure eseguire il comando seguente per visualizzare il valore della temperatura.

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

L'output dovrebbe contenere un valore di temperatura analogo al seguente:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Per visualizzare la modifica del valore, eseguire ripetutamente il comando di query precedente.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sui dati in ingresso e in uscita con i dispositivi gemelli digitali di Azure:
* [*Concetti: integrazione con altri servizi*](concepts-integration.md)
