---
title: Configurare nel dashboard Azure IoT Central di | Microsoft Docs
description: Come generatore, informazioni su come configurare il dashboard dell'applicazione Azure IoT Central predefinito con riquadri.
author: philmea
ms.author: philmea
ms.date: 12/19/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 8a8ba765a966409c06dbba636932f7777624f6d4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864254"
---
# <a name="configure-the-application-dashboard"></a>Configurare il dashboard dell'applicazione

Il **dashboard** è la prima pagina visualizzata quando ci si connette a un'IoT Central applicazione. Se si crea l'applicazione da uno dei modelli di applicazione incentrati sul [settore,](./concepts-app-templates.md)l'applicazione dispone di un dashboard predefinito da avviare. Se si crea l'applicazione da un modello [di applicazione personalizzato,](./concepts-app-templates.md)il dashboard mostra alcuni suggerimenti per iniziare.

> [!TIP]
> Gli utenti possono [creare più dashboard](howto-create-personal-dashboards.md) oltre al dashboard dell'applicazione predefinito. Questi dashboard possono essere personali e riservati solo agli utenti che li hanno creati o condivisi tra tutti gli utenti dell'applicazione.  

## <a name="add-tiles"></a>Aggiunta di riquadri

Lo screenshot seguente mostra il dashboard in un'applicazione creata dal **modello applicazione** personalizzata. Per personalizzare il dashboard corrente, selezionare **Modifica**, per aggiungere un dashboard personalizzato personale o condiviso, selezionare **Nuovo**:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="Dashboard per le applicazioni basate sul modello di applicazione personalizzato":::

Dopo aver selezionato **Modifica** o **Nuovo,** il dashboard è in *modalità di* modifica. È possibile usare gli strumenti nel pannello Modifica **dashboard** per aggiungere riquadri al dashboard e personalizzare e rimuovere riquadri nel dashboard stesso. Ad esempio, per aggiungere un riquadro **Telemetria** per visualizzare la temperatura corrente segnalata da uno o più dispositivi:

1. Selezionare un **gruppo di dispositivi** e quindi scegliere i dispositivi nell'elenco a discesa Dispositivi da visualizzare nel riquadro.  Vengono ora visualizzati i dati di telemetria, le proprietà e i comandi disponibili dai dispositivi.

1. Se necessario, usare l'elenco a discesa per selezionare un valore di telemetria da visualizzare nel riquadro. È possibile aggiungere altri elementi al riquadro selezionando **+ Telemetria**, **+ Proprietà** o + **Proprietà cloud**.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="Aggiungere un riquadro di telemetria della temperatura al dashboard":::

Dopo aver selezionato tutti i valori da visualizzare nel riquadro, fare clic **su Aggiungi riquadro.** Il riquadro viene ora visualizzato nel dashboard in cui è possibile modificare la visualizzazione, ridimensionarla, spostarla e configurarla.

Al termine dell'aggiunta e della personalizzazione  dei riquadri nel dashboard, selezionare Salva per salvare le modifiche nel dashboard, in modo da uscire dalla modalità di modifica.

## <a name="customize-tiles"></a>Personalizzare i riquadri

Per modificare un riquadro, è necessario essere in modalità di modifica.  Le opzioni di personalizzazione disponibili dipendono dal [tipo di riquadro](#tile-types):

* L'icona del righello in un riquadro consente di modificare la visualizzazione. Le visualizzazioni includono grafici a linee, grafici a barre, grafici a torta, ultimi valori noti, indicatori di prestazioni chiave (o indicatori KPI), mappe termiche e mappe.

* L'icona quadrata consente di ridimensionare il riquadro.

* L'icona a forma di ingranaggio consente di configurare la visualizzazione. Ad esempio, per una visualizzazione grafico a linee è possibile scegliere di visualizzare la legenda e gli assi e scegliere l'intervallo di tempo da tracciare.


## <a name="tile-types"></a>Tipi di riquadro

La tabella seguente descrive i diversi tipi di riquadro che è possibile aggiungere a un dashboard:

| Tile             | Descrizione |
| ---------------- | ----------- |
| Markdown         | I riquadri Markdown sono riquadri selezionabili che visualizzano un testo di intestazione e descrizione formattato tramite markdown. L'URL può essere un collegamento relativo a un'altra pagina nell'applicazione o un collegamento assoluto a un sito esterno.|
| Immagine            | I riquadri immagine visualizzano un'immagine personalizzata e possono essere selezionabili. L'URL può essere un collegamento relativo a un'altra pagina nell'applicazione o un collegamento assoluto a un sito esterno.|
| Label            | I riquadri etichetta visualizzano testo personalizzato in un dashboard. È possibile scegliere la dimensione del testo. Usare un riquadro etichetta per aggiungere informazioni pertinenti al dashboard, ad esempio descrizioni, dettagli di contatto o informazioni della Guida.|
| Conteggio            | I riquadri conteggio visualizzano il numero di dispositivi in un gruppo di dispositivi.|
| Mappa              | Le tessere mappa visualizzano la posizione di uno o più dispositivi su una mappa. È anche possibile visualizzare fino a 100 punti della cronologia della posizione di un dispositivo. Ad esempio, è possibile visualizzare l'itinerario campionato in cui si trova un dispositivo la settimana precedente.|
| Indicatore KPI              |  I riquadri KPI visualizzano i valori di telemetria aggregati per uno o più dispositivi in un periodo di tempo. Ad esempio, è possibile usarlo per visualizzare la temperatura massima e la pressione raggiunte per uno o più dispositivi nell'ultima ora.|
| Grafico a linee       | I riquadri del grafico a linee tracciano uno o più valori di telemetria aggregati per uno o più dispositivi per un periodo di tempo. Ad esempio, è possibile visualizzare un grafico a linee per tracciare la temperatura media e la pressione di uno o più dispositivi nell'ultima ora.|
| Grafico a barre        | I riquadri del grafico a barre tracciano uno o più valori di telemetria aggregati per uno o più dispositivi per un periodo di tempo. Ad esempio, è possibile visualizzare un grafico a barre per visualizzare la temperatura media e la pressione di uno o più dispositivi nell'ultima ora.|
| Grafico a torta        | I riquadri del grafico a torta visualizzano uno o più valori di telemetria aggregati per uno o più dispositivi per un periodo di tempo.|
| Mappa termica         | I riquadri mappa termica visualizzano informazioni su uno o più dispositivi, rappresentati come colori.|
| Ultimo valore noto | I riquadri degli ultimi valori noti visualizzano i valori di telemetria più recenti per uno o più dispositivi. Ad esempio, è possibile usare questo riquadro per visualizzare i valori più recenti di temperatura, pressione e umidità per uno o più dispositivi. |
| Cronologia eventi    | I riquadri cronologia eventi visualizzano gli eventi per un dispositivo per un periodo di tempo specifico. Ad esempio, è possibile usarlo per visualizzare tutti gli eventi di apertura e chiusura della valvola per uno o più dispositivi nell'ultima ora.|
| Proprietà         |  I riquadri delle proprietà visualizzano il valore corrente per le proprietà e le proprietà cloud di uno o più dispositivi. Ad esempio, è possibile usare questo riquadro per visualizzare le proprietà del dispositivo, ad esempio il produttore o la versione del firmware per un dispositivo. |

Attualmente è possibile aggiungere fino a 10 dispositivi ai riquadri che supportano più dispositivi.

### <a name="customizing-visualizations"></a>Personalizzazione delle visualizzazioni

Per impostazione predefinita, i grafici a linee mostrano i dati in un intervallo di tempo. L'intervallo di tempo selezionato viene suddiviso in 50 bucket di dimensioni uguali e i dati del dispositivo vengono quindi aggregati per bucket per assegnare 50 punti dati nell'intervallo di tempo selezionato. Per visualizzare i dati non elaborati, è possibile modificare la selezione per visualizzare gli ultimi 100 valori. Per modificare l'intervallo di tempo o selezionare la visualizzazione dei dati non elaborati, usare l'elenco a discesa Intervallo di visualizzazione nel **pannello Configura** grafico.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/display-range.png" alt-text="Modificare l'intervallo di visualizzazione di un grafico a linee":::

Per i riquadri che visualizzano valori aggregati, selezionare l'icona a forma di ingranaggio accanto al tipo di telemetria nel pannello **Configura** grafico per scegliere l'aggregazione. È possibile scegliere tra media, somma, massimo, minimo e conteggio.

Per grafici a linee, grafici a barre e grafici a torta, è possibile personalizzare il colore dei diversi valori di telemetria. Selezionare l'icona del riquadro accanto ai dati di telemetria da personalizzare:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="Modificare il colore di un valore di telemetria":::

Per i riquadri che mostrano proprietà stringa o valori di telemetria, è possibile scegliere come visualizzare il testo. Ad esempio, se il dispositivo archivia un URL in una proprietà stringa, è possibile visualizzarlo come collegamento selezionabile. Se l'URL fa riferimento a un'immagine, è possibile eseguire il rendering dell'immagine in un riquadro ultimo valore noto o proprietà. Per modificare la modalità di visualizzazione di una stringa, nella configurazione del riquadro selezionare l'icona a forma di ingranaggio accanto al tipo o alla proprietà di telemetria:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="Modificare la modalità di visualizzazione di una stringa in un riquadro":::

Per i **riquadri numerici KPI**, Last **Known Value** e **Property** è possibile usare la formattazione condizionale per personalizzare il colore del riquadro in base al valore corrente. Per aggiungere la formattazione condizionale, **selezionare** Configura nel riquadro e quindi selezionare l'icona **Formattazione** condizionale accanto al valore da personalizzare:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-1.png" alt-text="Screenshot che mostra come trovare l'opzione di configurazione per un riquadro e quindi l'icona di formattazione condizionale":::

Aggiungere le regole di formattazione condizionale:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-2.png" alt-text="Screenshot che mostra le regole di formattazione condizionale per il flusso medio. Sono disponibili tre regole: meno di 20 è verde, meno di 50 è giallo e qualsiasi valore maggiore di 50 è rosso":::
   
Lo screenshot seguente mostra l'effetto della regola di formattazione condizionale:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-3.png" alt-text="Screenshot che mostra il colore di sfondo rosso nel riquadro Flusso acqua medio. Il numero nel riquadro è 50,54":::

### <a name="tile-formatting"></a>Formattazione "riquadro"
Questa funzionalità, disponibile nei riquadri KPI, LKV e Proprietà, consente agli utenti di modificare le dimensioni del carattere, scegliere la precisione decimale, abbreviare i valori numerici (ad esempio formato 1.700 come 1,7 KB) o eseguire il wrapping dei valori stringa nei riquadri.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/tile-format.png" alt-text="Formato riquadro":::

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come configurare il dashboard dell'applicazione predefinito di Azure IoT Central, è possibile [acquisire informazioni su come creare un dashboard personale](howto-create-personal-dashboards.md).
