---
title: Configurare un ambiente Gen2 usando il Gen2 portale di Azure-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come configurare un ambiente in Azure Time Series Insights Gen2 usando portale di Azure.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 09068d966df871d4b6804978a543db50bccbee37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952848"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-portal"></a>Creare un ambiente Gen2 Azure Time Series Insights usando il portale di Azure

Questo articolo descrive come creare un ambiente Gen2 Azure Time Series Insights usando il [portale di Azure](https://portal.azure.com/).

Nell'esercitazione sul provisioning dell'ambiente viene illustrato il processo. Si apprenderà come selezionare l'ID della serie temporale corretto e visualizzare gli esempi di due payload JSON.</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]

## <a name="overview"></a>Panoramica

Quando si esegue il provisioning di un ambiente Azure Time Series Insights Gen2, è necessario creare le risorse di Azure seguenti:

* Un ambiente Azure Time Series Insights Gen2 che segue il modello di determinazione prezzi con pagamento in base al consumo
* Un account di archiviazione Azure
* Archivio caldo facoltativo per query più veloci e illimitate

> [!TIP]
>
> * Vedere [come pianificare l'ambiente](./how-to-plan-your-environment.md).
> * Leggere le informazioni su come [aggiungere un'origine Hub eventi](./how-to-ingest-data-event-hub.md) o su come [aggiungere un'origine Hub](./how-to-ingest-data-iot-hub.md)Internet.

Si apprenderà come:

1. Associare ogni ambiente Azure Time Series Insights generazione 2 a un'origine evento. Si forniranno anche una proprietà ID timestamp e un gruppo di consumer univoco per garantire che l'ambiente possa accedere agli eventi appropriati.

1. Al termine del provisioning, è possibile modificare i criteri di accesso e altri attributi dell'ambiente in base alle esigenze aziendali.

   > [!NOTE]
   > Il primo passaggio è facoltativo quando si esegue il provisioning di un ambiente. Se si ignora questo passaggio, è necessario aggiungere un'origine evento all'ambiente in un secondo momento, in modo che i dati possano essere avviati nel proprio ambiente ed è possibile accedervi tramite query.

## <a name="create-the-environment"></a>Creare l'ambiente

Per creare un ambiente Azure Time Series Insights generazione 2:

1. Creare una risorsa Azure Time Series Insights in *Internet delle cose* [portale di Azure](https://portal.azure.com/).

1. Selezionare **Gen2 (L1)** come **livello**. Specificare un nome per l'ambiente e scegliere il gruppo di sottoscrizioni e il gruppo di risorse da usare. Selezionare quindi un percorso supportato per ospitare l'ambiente.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration.png" alt-text="Creare un'istanza di Azure Time Series Insights." lightbox="media/how-to-create-environment-using-portal/environment-configuration.png":::

1. Immettere un ID serie temporale.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration-2.png" alt-text="Creare una configurazione dell'ambiente Azure Time Series Insights, continuare." lightbox="media/how-to-create-environment-using-portal/environment-configuration-2.png":::

   > [!NOTE]
   >
   > * L'ID della serie temporale fa *distinzione tra maiuscole* e minuscole e non è *modificabile*. Non può essere modificato dopo averlo impostato.
   > * Gli ID delle serie temporali possono contenere fino a *tre* chiavi. Considerarlo come chiave primaria in un database, che rappresenta in modo univoco ogni sensore di dispositivo che invia i dati all'ambiente. Potrebbe trattarsi di una proprietà o di una combinazione di un massimo di tre proprietà.
   > * Scopri di più su [come scegliere un ID Time Series](./how-to-select-tsid.md)

1. Creare un account di archiviazione di Azure selezionando il nome dell'account di archiviazione, il tipo di account e designando una scelta di [replica](../storage/common/redundancy-migration.md?tabs=portal) . In questo modo viene creato automaticamente un account di archiviazione di Azure. Per impostazione predefinita, verrà creato un account per [utilizzo generico V2](../storage/common/storage-account-overview.md) . L'account viene creato nella stessa area dell'ambiente Azure Time Series Insights Gen2 selezionato in precedenza.
In alternativa, è anche possibile usare l'archiviazione (BYOS) tramite un [modello di Azure Resource Manager](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) quando si crea un nuovo ambiente Gen2 di Azure Time Series.

1. **(Facoltativo)** Abilitare l'archivio a caldo per l'ambiente se si desiderano query più veloci e illimitate sui dati più recenti nell'ambiente in uso. È anche possibile creare o eliminare un archivio a caldo tramite l'opzione di **Configurazione archiviazione** nel riquadro di spostamento a sinistra, dopo aver creato un ambiente di Azure Time Series Insights Gen2.

1. **(Facoltativo)** È ora possibile aggiungere un'origine evento. È anche possibile attendere fino al termine del provisioning dell'istanza.

   * Azure Time Series Insights supporta l' [Hub Azure](./how-to-ingest-data-iot-hub.md) e gli hub [eventi di Azure](./how-to-ingest-data-event-hub.md) come opzioni di origine eventi. Sebbene sia possibile aggiungere una sola origine evento quando si crea l'ambiente, è possibile aggiungere un'altra origine evento in un secondo momento.

     È possibile selezionare un gruppo di consumer esistente o creare un nuovo gruppo di consumer quando si aggiunge l'origine evento. Assicurarsi che l'origine eventi usi un gruppo di consumer univoco per l'ambiente in cui leggere i dati.

   * Scegliere la proprietà timestamp appropriata. Per impostazione predefinita, Azure Time Series Insights utilizza l'ora accodata dal messaggio per ogni origine evento.

     > [!TIP]
     > Il tempo di Accodamento dei messaggi potrebbe non essere l'impostazione migliore configurata per l'utilizzo in scenari di eventi batch o scenari di caricamento cronologico dei dati. In questi casi, assicurarsi di verificare la decisione di utilizzare o meno una proprietà timestamp.

   :::image type="content" source="media/how-to-create-environment-using-portal/configure-event-source.png" alt-text="Scheda configurazione origine evento" lightbox="media/how-to-create-environment-using-portal/configure-event-source.png":::

1. Selezionare **Verifica + crea** per verificare che l'ambiente sia stato sottoposto a provisioning e configurato nel modo desiderato.

    :::image type="content" source="media/how-to-create-environment-using-portal/environment-confirmation.png" alt-text="Verifica + scheda Crea" lightbox="media/how-to-create-environment-using-portal/environment-confirmation.png":::

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su Azure Time Series Insights ambienti disponibili a livello generale e sugli ambienti Gen2, vedere [pianificare l'ambiente](./how-to-plan-your-environment.md).
* Informazioni sulle [origini eventi](./concepts-streaming-ingestion-event-sources.md) di inserimento del flusso per l'ambiente di Azure Time Series Insights Gen2.
* Altre informazioni su [come gestire l'ambiente](./how-to-provision-manage.md).
