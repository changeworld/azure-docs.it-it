---
title: 'Esercitazione: usare Azure Time Series Insights per archiviare e analizzare i dati di telemetria del dispositivo Plug and Play Azure'
description: "Esercitazione: configurare un ambiente Time Series Insights e connettere l'hub Internet per visualizzare e analizzare i dati di telemetria dai dispositivi Plug and Play."
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 08ae21c2cd0859b7c361756a4f0380d3ab322a28
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834358"
---
# <a name="tutorial-create-and-configure-a-time-series-insights-gen2-environment"></a>Esercitazione: creare e configurare un ambiente Gen2 Time Series Insights

Questa esercitazione illustra come creare e configurare un ambiente [Azure Time Series Insights Gen2](../time-series-insights/overview-what-is-tsi.md) per l'integrazione con la soluzione Plug and Play IoT. Con Time Series Insights è possibile eseguire operazioni di raccolta, archiviazione, query e visualizzazione di dati di serie temporali a livello di Internet delle cose (IoT).

In questa esercitazione si è appreso come

> [!div class="checklist"]
> * Eseguire il provisioning di un ambiente di Time Series Insights e connettere l'hub Internet come origine evento di streaming.
> * Usare la sincronizzazione dei modelli per creare il [modello Time Series](../time-series-insights/concepts-model-overview.md).
> * Usare i file del modello di esempio [DTDL (Digital Gemini Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) usati per il controller di temperatura e i dispositivi del termostato.

> [!NOTE]
> Questa integrazione tra Time Series Insights e Plug and Play IoT è in anteprima. Il modo in cui i modelli di dispositivo DTDL vengono mappati al modello Time Series di Time Series Insights potrebbe cambiare. 

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

A questo punto, saranno disponibili:

* Un hub IoT di Azure.
* Un'istanza del servizio Device Provisioning (DPS) collegata all'hub IoT. L'istanza di DPS deve avere una singola registrazione per il dispositivo Plug and Play IoT.
* Una connessione all'hub IoT da un dispositivo singolo o a più componenti che esegue lo streaming di dati simulati.

Per evitare la necessità di installare l'interfaccia della riga di comando di Azure in locale, è possibile usare la Azure Cloud Shell per configurare i servizi cloud.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>Preparare l'origine eventi

L'hub IoT creato in precedenza diventerà l'[origine evento](../time-series-insights/concepts-streaming-ingestion-event-sources.md) dell'ambiente Time Series Insights.

> [!IMPORTANT]
> Disabilitare eventuali route esistenti dell'hub IoT. Si verifica un problema noto relativo all'uso di un hub IoT con [routing](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints) configurato. Disabilitare temporaneamente eventuali endpoint di routing. Quando l'hub IoT sarà connesso a Time Series Insights sarà possibile riabilitarli.

Nell'hub IoT, creare un gruppo di consumer univoco per l'utilizzo da parte di Time Series Insights. Nell'esempio seguente, sostituire `my-pnp-hub` con il nome dell'hub IoT usato in precedenza.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-a-time-series-id"></a>Scegliere un ID serie temporale

Quando si effettua il provisioning dell'ambiente Time Series Insights, è necessario selezionare un *ID serie temporale*. È importante selezionare l'ID serie temporale appropriato. Questa proprietà è invariabile e non può essere modificata dopo che è stata impostata. L'ID serie temporale è paragonabile a una chiave di partizione del database. L'ID serie temporale funge da chiave primaria per il modello Time Series. Per altre informazioni, leggere [Best practices for choosing a Time Series ID](../time-series-insights/how-to-select-tsid.md) (Procedure consigliate per la scelta di un ID Time Series).

Come utente di Plug and Play IoT, specificare una _chiave composta_ costituita da `iothub-connection-device-id` e `dt-subject` come ID serie temporale. L'hub IoT aggiunge queste proprietà di sistema che contengono rispettivamente l'ID dispositivo Plug and Play IoT e i nomi dei componenti del dispositivo.

Anche se i propri modelli di dispositivo Plug and Play IoT attualmente non usano componenti, è consigliabile includere `dt-subject` come parte di una chiave composta in modo da poterli usare in futuro. Poiché l'ID serie temporale non è modificabile, Microsoft consiglia di abilitare questa opzione nel caso sia necessaria in futuro.

> [!NOTE]
> Gli esempi in questo articolo sono relativi al dispositivo `TemperatureController` con più componenti. I concetti sono tuttavia identici per il dispositivo `Thermostat` senza componenti.

## <a name="provision-your-time-series-insights-environment"></a>Effettuare il provisioning dell'ambiente Time Series Insights

Questa sezione descrive come effettuare il provisioning dell'ambiente Azure Time Series Insights Gen2.

Eseguire il comando seguente per:

* Creare un account di archiviazione di Azure per l'[archiviazione offline sicura](../time-series-insights/concepts-storage.md#cold-store) dell'ambiente. Questo account è progettato per la conservazione a lungo termine e l'analisi dei dati cronologici.
  * Nel codice, sostituire `mytsicoldstore` con un nome univoco per l'account di archiviazione offline sicura.
* Creare un ambiente Azure Time Series Insights Gen2. L'ambiente verrà creato con archiviazione ad accesso frequente con un periodo di conservazione di sette giorni. L'account di archiviazione offline sicura verrà allegato per un periodo di conservazione infinito.
  * Nel codice, sostituire `my-tsi-env` con un nome univoco per l'ambiente Time Series Insights.
  * Nel codice, sostituire `my-pnp-resourcegroup` con il nome del gruppo di risorse usato durante la configurazione.
  * La proprietà dell'ID serie temporale è `iothub-connection-device-id, dt-subject`.

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties iothub-connection-device-id, dt-subject --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

Connettersi all'origine evento dell'hub IoT. Sostituire `my-pnp-resourcegroup`, `my-pnp-hub` e `my-tsi-env` con i valori scelti. Il comando seguente fa riferimento al gruppo di consumer per l'ambiente Time Series Insights creato in precedenza:

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```

Nel [portale di Azure](https://portal.azure.com) passare al gruppo di risorse e selezionare il nuovo ambiente Time Series Insights. Visitare lo **strumento di esplorazione di Time Series Insights** mostrato nella panoramica dell'istanza:

![Screenshot della pagina di panoramica del portale.](./media/tutorial-configure-tsi/view-environment.png)

In Explorer sono disponibili tre istanze:

* &lt;ID dispositivo PnP&gt;, thermostat1
* &lt;ID dispositivo PnP&gt;, thermostat2
* &lt;ID dispositivo PnP&gt;, `null`

> [!NOTE]
> Il terzo tag rappresenta i tag di telemetria del dispositivo `TemperatureController` stesso, ad esempio il working set della memoria. Poiché si tratta di una proprietà di primo livello, il valore per il nome del componente è Null. In un passaggio successivo questo valore sarà modificato con un nome più descrittivo.

![Screenshot che mostra tre istanze in Explorer.](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>Configurare la conversione del modello

A questo punto, il modello di dispositivo DTDL verrà convertito nel modello di asset in Azure Time Series Insights. In Time Series Insights, il modello Time Series è uno strumento di modellazione semantica per la contestualizzazione dei dati. Il modello include tre componenti principali:

* Le [istanze del modello Time Series](../time-series-insights/concepts-model-overview.md#time-series-model-instances) sono rappresentazioni virtuali delle serie temporali stesse. Le istanze vengono identificate in modo univoco dall'ID serie temporale.
* Le [gerarchie del modello Time Series](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies) organizzano le istanze specificando i nomi e le relazioni delle proprietà.
* I [tipi di modello Time Series](../time-series-insights/concepts-model-overview.md#time-series-model-types) consentono di definire [variabili](../time-series-insights/concepts-variables.md) o formule per eseguire calcoli. I tipi sono associati a un'istanza specifica.

### <a name="define-your-types"></a>Definire i tipi

È possibile iniziare a inserire i dati in Azure Time Series Insights Gen2 senza avere un modello predefinito. Quando riceve i dati di telemetria, Time Series Insights proverà a risolvere automaticamente le istanze di serie temporali in base al valore della proprietà ID serie temporale. A tutte le istanze verrà assegnato il *tipo predefinito*. È necessario creare manualmente un nuovo tipo per categorizzare correttamente le istanze. 

Le informazioni seguenti delineano il metodo più semplice per sincronizzare i modelli DTDL del dispositivo con i tipi di modello Time Series:

* L'identificatore DTMI (Digital Twin Model Identifier) diventa l'ID tipo.
* Il nome del tipo può essere il nome del modello o il nome visualizzato.
* La descrizione del modello diventa la descrizione del tipo.
* Per ogni dato di telemetria con uno schema numerico viene creata almeno una variabile di tipo.
  * Per le variabili è possibile usare solo tipi di dati numerici, ma se un valore viene inviato come un altro tipo convertibile, ad esempio `"0"`, è possibile usare una funzione di [conversione](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) come `toDouble`.
* Il nome della variabile può essere il nome dei dati di telemetria o il nome visualizzato.
* Quando si definisce la variabile di Time Series Expression, fare riferimento al nome dei dati di telemetria in transito e al relativo tipo di dati.

| JSON DTDL | JSON del tipo di modello Time Series | Valore di esempio |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |  
|`contents` (matrice)| `variables` (oggetto)  | Vedere l'esempio seguente.

![Screenshot che mostra il tipo di modello da D T D L a Time Series.](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> Questo esempio mostra tre variabili, ma ogni tipo può averne fino a 100. Variabili diverse possono fare riferimento allo stesso valore di telemetria per eseguire calcoli differenti in base alle esigenze. Per l'elenco completo di filtri, aggregati e funzioni scalari, vedere la documentazione della [sintassi dell'espressione Time Series di Time Series Insights Gen2](/rest/api/time-series-insights/reference-time-series-expression-syntax).

Aprire un editor di testo e salvare il codice JSON seguente nell'unità locale.

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:TemperatureController;1",
      "name": "Temperature Controller",
      "description": "Device with two thermostats and remote reboot.",
      "variables": {
        "workingSet": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.workingSet.Long, toLong($event.workingSet.Double))"
          }, 
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.temperature.Long, toLong($event.temperature.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "eventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        }
      }
    }
  ]
}
```

Nello strumento di esplorazione di Time Series Insights, selezionare l'icona del modello a sinistra per aprire la scheda **Modello**. Selezionare **Tipi** e quindi **Carica JSON**:

![Screenshot che illustra come caricare JSON.](./media/tutorial-configure-tsi/upload-type.png)

Selezionare **Scegli file**, selezionare il file JSON salvato in precedenza, quindi selezionare **Carica**.

Verrà visualizzato il tipo **Temperature Controller** appena definito.

### <a name="create-a-hierarchy"></a>Creare una gerarchia

Creare una gerarchia per organizzare i tag sotto l'elemento padre `TemperatureController`. L'esempio seguente ha un singolo livello, ma le soluzioni IoT in genere presentano molti livelli di annidamento per contestualizzare i tag all'interno della rispettiva posizione fisica e semantica in un'organizzazione.

Selezionare **Gerarchie** e quindi **Aggiungi gerarchia**. Per il nome, immettere *Device Fleet*. Creare un livello denominato *Device Name*. Selezionare quindi **Salva**.

![Screenshot che mostra come aggiungere una gerarchia.](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Assegnare le istanze al tipo corretto

Cambiare quindi il tipo delle istanze e inserirle nella gerarchia.

Selezionare la scheda **Istanze**. Trovare l'istanza che rappresenta il working set del dispositivo, quindi selezionare l'icona **Modifica** all'estrema destra.

![Screenshot che mostra come modificare un'istanza.](./media/tutorial-configure-tsi/edit-instance.png)

Aprire l'elenco a discesa **Tipo**, quindi selezionare **Temperature Controller**. Immettere *defaultComponent, <your device name>* per aggiornare il nome dell'istanza che rappresenta tutti i tag di primo livello associati al dispositivo.

![Screenshot che mostra come modificare un tipo di istanza.](./media/tutorial-configure-tsi/change-type.png)

Prima di selezionare **Salva**, selezionare la scheda **Campi di istanza** e quindi selezionare **Device Fleet**. Per raggruppare i dati di telemetria, immettere *\<your device name> - Temp Controller*. Selezionare quindi **Salva**.

![Screenshot che mostra come assegnare un'istanza a una gerarchia](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Ripetere i passaggi precedenti per assegnare ai tag del termostato il tipo e la gerarchia corretti.

## <a name="view-your-data"></a>Visualizzare i dati

Tornare nel riquadro del grafico ed espandere **Device Fleet > dispositivo**. Selezionare **thermostat1**, la variabile **Temperature** e quindi **Aggiungi** per tracciare il valore sul grafico. Eseguire la stessa operazione per **thermostat2** e per il valore **defaultComponent** **workingSet**.

![Screenshot che mostra come modificare il tipo di istanza per thermostat2.](./media/tutorial-configure-tsi/charting-values.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> Per altre informazioni sulle varie opzioni di creazione di grafici, inclusi i controlli di dimensionamento intervallo e asse Y, vedere lo [strumento di esplorazione di Time Series Insights](../time-series-insights/concepts-ux-panels.md).
