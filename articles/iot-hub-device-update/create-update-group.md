---
title: Creare un gruppo di dispositivi nell'aggiornamento del dispositivo per l'hub Azure Microsoft Docs
description: Creare un gruppo di dispositivi nell'aggiornamento del dispositivo per l'hub Azure
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: a0894047db1ed7687a1a0f5f87fc4020ddf7c694
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679518"
---
# <a name="create-device-groups-in-device-update-for-iot-hub"></a>Creare gruppi di dispositivi nell'aggiornamento del dispositivo per l'hub Internet
L'aggiornamento del dispositivo per l'hub Internet delle cose consente la distribuzione di un aggiornamento in un gruppo di dispositivi Internet.

## <a name="prerequisites"></a>Prerequisiti

* [Accesso a un hub Internet delle cose con l'aggiornamento del dispositivo per l'hub](create-device-update-account.md)Internet. Si consiglia di usare un livello S1 (standard) o superiore per l'hub Internet. 
* Viene eseguito il provisioning di un dispositivo o di un simulatore per l'aggiornamento del dispositivo all'interno dell'hub.
* [È stato importato almeno un aggiornamento per il dispositivo di cui è stato effettuato il provisioning.](import-update.md)

## <a name="add-a-tag-to-your-devices"></a>Aggiungere un tag ai dispositivi  

L'aggiornamento del dispositivo per l'hub Internet delle cose consente la distribuzione di un aggiornamento in un gruppo di dispositivi Internet. Per creare un gruppo, il primo passaggio consiste nell'aggiungere un tag al set di dispositivi di destinazione nell'hub Internet. I tag possono essere aggiunti correttamente al dispositivo solo dopo essere stati connessi all'aggiornamento del dispositivo.

La documentazione seguente descrive come aggiungere e aggiornare un tag.

### <a name="programmatically-update-device-twin"></a>Aggiornare a livello di codice un dispositivo gemello

È possibile aggiornare il dispositivo gemello con il tag appropriato usando RegistryManager dopo aver registrato il dispositivo con l'aggiornamento del dispositivo. 
[Informazioni su come aggiungere tag tramite un'app .NET di esempio.](../iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)  
Informazioni [sulle proprietà dei tag](../iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format).

#### <a name="device-update-tag-format"></a>Formato tag di aggiornamento del dispositivo

```markdown
     "tags": {
              "ADUGroup": "<CustomTagValue>"
             }
```

### <a name="using-jobs"></a>Uso dei processi

È possibile pianificare un processo su più dispositivi per aggiungere o aggiornare un tag di aggiornamento del dispositivo seguendo [questi](../iot-hub/iot-hub-devguide-jobs.md) esempi. [Altre informazioni](../iot-hub/iot-hub-csharp-csharp-schedule-jobs.md)

  > [!NOTE] 
  > Questa azione viene eseguita in base alla quota dei messaggi dell'hub Internet corrente ed è consigliabile modificare solo i tag del dispositivo gemello 50.000 alla volta. in caso contrario, potrebbe essere necessario acquistare altre unità dell'hub cose se si supera la quota di messaggi dell'hub Internet giornaliero. Per informazioni dettagliate, vedere [quote e limitazioni](../iot-hub/iot-hub-devguide-quotas-throttling.md#quotas-and-throttling).

### <a name="direct-twin-updates"></a>Aggiornamenti di dispositivi gemelli diretti

I tag possono anche essere aggiunti o aggiornati direttamente nel dispositivo gemello.

1. Accedere a [portale di Azure](https://portal.azure.com) e passare all'hub Internet.

2. Da "dispositivi Internet o" IoT Edge "nel riquadro di spostamento a sinistra trova il tuo dispositivo e passa al dispositivo gemello.

3. Nel dispositivo gemello eliminare qualsiasi valore del tag di aggiornamento del dispositivo esistente impostando il valore su null.

4. Aggiungere un nuovo valore del tag di aggiornamento del dispositivo come illustrato di seguito. [Esempio di documento JSON del dispositivo gemello con tag.](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

### <a name="limitations"></a>Limitazioni

* È possibile aggiungere qualsiasi valore al tag, ad eccezione di ' Uncategorized ', che è un valore riservato.
* Il valore del tag non può superare i 255 caratteri.
* Il valore del tag può contenere caratteri alfanumerici e i caratteri speciali seguenti: ".", "-", "_", "~".
* I nomi di tag e gruppi fanno distinzione tra maiuscole e minuscole.
* Un dispositivo può avere un solo tag con il nome ADUGroup, qualsiasi aggiunta successiva di un tag con tale nome sovrascriverà il valore esistente per il nome di tag ADUGroup.
* Un dispositivo può appartenere a un solo gruppo.

## <a name="create-a-device-group-by-selecting-an-existing-iot-hub-tag"></a>Creare un gruppo di dispositivi selezionando un tag dell'hub Internet esistente

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare l'hub Internet delle cose precedentemente connesso all'istanza di aggiornamento del dispositivo.

3. Selezionare l'opzione aggiornamenti del dispositivo in gestione automatica dispositivi dalla barra di spostamento a sinistra.

4. Selezionare la scheda gruppi nella parte superiore della pagina. Sarà possibile visualizzare il numero di dispositivi connessi all'aggiornamento del dispositivo che non sono ancora raggruppati.
   :::image type="content" source="media/create-update-group/ungrouped-devices.png" alt-text="Screenshot dei dispositivi non raggruppati." lightbox="media/create-update-group/ungrouped-devices.png":::

5. Selezionare il pulsante Aggiungi per creare un nuovo gruppo.
   :::image type="content" source="media/create-update-group/add-group.png" alt-text="Screenshot dell'aggiunta del gruppo di dispositivi." lightbox="media/create-update-group/add-group.png":::

6. Selezionare un tag dell'hub delle cose dall'elenco e quindi selezionare Crea gruppo di aggiornamento.
   :::image type="content" source="media/create-update-group/select-tag.png" alt-text="Screenshot della selezione dei tag." lightbox="media/create-update-group/select-tag.png":::

7. Una volta creato il gruppo, si noterà che l'elenco dei gruppi e del grafico di conformità degli aggiornamenti è stato aggiornato.  Il grafico conformità aggiornamenti Mostra il numero di dispositivi in diversi Stati di conformità: sull'aggiornamento più recente, i nuovi aggiornamenti disponibili, gli aggiornamenti in corso e i dispositivi non ancora raggruppati. Informazioni [sulla conformità degli aggiornamenti.](device-update-compliance.md) 
    :::image type="content" source="media/create-update-group/updated-view.png" alt-text="Screenshot della visualizzazione conformità aggiornamenti." lightbox="media/create-update-group/updated-view.png":::

8. Il gruppo appena creato verrà visualizzato e gli eventuali aggiornamenti disponibili per i dispositivi nel nuovo gruppo. È possibile distribuire l'aggiornamento al nuovo gruppo da questa visualizzazione facendo clic sul nome dell'aggiornamento. Vedere passaggio successivo: distribuire l'aggiornamento per altri dettagli.

## <a name="view-device-details-for-the-group-you-created"></a>Visualizzare i dettagli del dispositivo per il gruppo creato

1. Passare al gruppo appena creato e fare clic sul nome del gruppo.

2. Verrà visualizzato un elenco di dispositivi che fanno parte del gruppo insieme alle relative proprietà di aggiornamento del dispositivo. In questa visualizzazione è anche possibile visualizzare le informazioni sulla conformità degli aggiornamenti per tutti i dispositivi che sono membri del gruppo. Il grafico conformità aggiornamenti Mostra il numero di dispositivi in diversi Stati di conformità: sull'aggiornamento più recente, i nuovi aggiornamenti disponibili e gli aggiornamenti in corso.
   :::image type="content" source="media/create-update-group/group-details.png" alt-text="Screenshot della visualizzazione dettagli del gruppo di dispositivi." lightbox="media/create-update-group/group-details.png":::

3. È anche possibile fare clic su ogni singolo dispositivo in un gruppo da reindirizzare alla pagina dei dettagli del dispositivo nell'hub.
   :::image type="content" source="media/create-update-group/device-details.png" alt-text="Screenshot della visualizzazione dei dettagli del dispositivo." lightbox="media/create-update-group/device-details.png":::

## <a name="next-steps"></a>Passaggi successivi 

[Distribuisci aggiornamento](deploy-update.md)

[Altre informazioni sui gruppi di dispositivi](device-update-groups.md)

[Informazioni sulla conformità degli aggiornamenti.](device-update-compliance.md)
