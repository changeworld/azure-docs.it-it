---
title: Usare il dashboard della console dei sensori
description: Il dashboard consente di visualizzare rapidamente lo stato di sicurezza della rete. Fornisce una panoramica generale delle minacce per l'intero sistema in una sequenza temporale, insieme alle informazioni sui dispositivi correlati.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/03/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: c9afc22cd123a782c9ee0247952c78c72ee916c0
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509078"
---
# <a name="the-dashboard"></a>Dashboard

Il dashboard consente di visualizzare rapidamente lo stato di sicurezza della rete. Viene fornita una panoramica generale delle minacce per l'intero sistema in una sequenza temporale, insieme alle informazioni sui dispositivi correlati, ad esempio:

- Avvisi con diversi livelli di gravità:

- Critico

- Principale

- Minorenne

- Avvisi

- I due indicatori al centro della pagina mostrano i pacchetti al secondo (PPS) e gli avvisi non riconosciuti (UA). **PPS** è il numero di pacchetti riconosciuti dal sistema al secondo. **UA** è il numero di avvisi che non sono stati ancora riconosciuti.

- Elenco di avvisi non riconosciuti con la relativa descrizione.

- Sequenza temporale con la descrizione dell'avviso.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/dashboard-alert-screen-v2.png" alt-text="Dashboard":::

## <a name="viewing-the-latest-alerts"></a>Visualizzazione degli avvisi più recenti

Il contatore avvisi non riconosciuti (UA) al centro della pagina indica il numero di tali avvisi. Per visualizzare un elenco di avvisi, selezionare **altri avvisi** nella parte inferiore della pagina del dashboard oppure selezionare **avvisi** dal menu laterale.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unhandled-alerts-list.png" alt-text="Avvisi non riconosciuti":::

## <a name="status-boxes"></a>Caselle di stato

Ogni casella di stato viene descritta in questa sezione.

| Casella di stato e misuratori | Descrizione |
| -------------- | -------------- |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/critical-alert-status-box-v2.png" alt-text="Avvisi critici"::: | **Avvisi critici** : la casella nella parte superiore centrale della pagina indica il numero di avvisi critici. Selezionare questa casella per visualizzare le descrizioni degli avvisi nella sequenza temporale e nell'elenco sotto i misuratori, se presenti.                              |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/major-alert-status-box-v2.png" alt-text="Avvisi principali"::: | **Avvisi principali** : la casella nella parte superiore destra della pagina indica il numero di avvisi principali. Selezionare questa casella per visualizzare le descrizioni degli avvisi nella sequenza temporale e nell'elenco sotto i misuratori, se presenti.                                     |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/minor-alert-status-box-v2.png" alt-text="Avvisi secondari"::: | **Avvisi secondari** : la casella nella parte inferiore sinistra della pagina indica il numero di avvisi secondari. Selezionare questa casella per visualizzare le descrizioni degli avvisi nella sequenza temporale e nell'elenco sotto i misuratori, se presenti.                                   |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/warnings-alert-status-box-v2.png" alt-text="Warning Alerts"::: | **Avvisi di avviso** : la casella nella parte inferiore centrale della pagina indica il numero di avvisi di avviso. Selezionare questa casella per visualizzare le descrizioni degli avvisi nella sequenza temporale e nell'elenco sotto i misuratori, se presenti.                             |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/all-alert-status-box-v2.png" alt-text="Tutti gli avvisi"::: | **Tutti gli avvisi** : la casella nella parte inferiore destra della pagina indica il numero totale di avvisi critici, principali, secondari e di avviso. Selezionare questa casella per visualizzare le descrizioni degli avvisi nella sequenza temporale e nell'elenco sotto i misuratori, se presenti. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/packets-per-second-gauge-v2.png" alt-text="Pacchetti al secondo"::: | **Pacchetti al secondo (PPS)** : la metrica PPS è un indicatore delle prestazioni della rete. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unacknowledged-events-gauge-v2.png" alt-text="Eventi non riconosciuti (UA)"::: | **Eventi non riconosciuti** : questa metrica indica il numero di eventi non riconosciuti.

## <a name="using-the-timeline"></a>Uso della sequenza temporale

Gli avvisi vengono visualizzati lungo una sequenza temporale verticale che include informazioni sulla data e l'ora.

La sequenza temporale Visualizza graficamente:

- Critical Alerts

- Avvisi principali

- Avvisi secondari

- Warning Alerts

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/timeline-of-events.png" alt-text="Grafico della sequenza temporale":::

## <a name="viewing-alerts"></a>Visualizzazione degli avvisi

Selezionare la freccia rivolta verso il basso nella parte inferiore di una casella di avviso per visualizzare le informazioni relative alla **voce di avviso** e ai dispositivi.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/extended-alert-screen.png" alt-text="Informazioni sui dispositivi e sulla voce di avviso":::

- Selezionare il dispositivo per visualizzare la mappa in modalità fisica. I dispositivi soggetti sono evidenziati.

- Fare clic in un punto qualsiasi della casella di avviso per visualizzare ulteriori dettagli relativi all'avviso. Un popup viene visualizzato in modo simile a quello riportato di seguito

- Selezionare :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/excel-icon.png" alt-text="Excel"::: per esportare un file CSV sull'avviso.

- Solo amministratori e analisti della sicurezza: selezionare :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/approve-all-icon.png" alt-text="conferma tutti"::: per **confermare tutti gli** avvisi associati.

- Selezionare :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pdf-icon.png" alt-text="PDF":::per scaricare un report degli avvisi come file PDF.

- Selezionare :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pin-icon.png" alt-text="Aggiungi":::per aggiungere o Rimuovi l'avviso. Se si seleziona il PIN, questo verrà aggiunto alla finestra **avvisi bloccati** nella schermata **avvisi** .

- Selezionare :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/download-icon.png" alt-text="download"::: per esaminare l'avviso scaricando il file PCAP correlato contenente un'analisi del protocollo di rete.

- Selezionare :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/cloud-download-icon.png" alt-text="cloud"::: per scaricare un file PCAP filtrato correlato contenente solo i pacchetti rilevanti per gli avvisi, riducendo così le dimensioni del file di output e consentendo un'analisi più mirata. È possibile visualizzarlo usando [Wireshark](https://www.wireshark.org/).

- Selezionare :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/navigate-icon.png" alt-text="navigazione"::: per passare alla sequenza temporale dell'evento al momento dell'avviso richiesto. In questo modo è possibile valutare altri eventi che potrebbero verificarsi intorno all'avviso specifico.

- Solo amministratori e analisti di sicurezza: modificare lo stato dell'avviso da non riconosciuto a riconosciuto. Selezionare Learn per approvare l'attività rilevata.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unauthorized-internet-connectivity-detection-v3.png" alt-text="È stata rilevata una connettività Internet non autorizzata":::

## <a name="see-also"></a>Vedi anche

[Usare gli avvisi sul sensore](how-to-work-with-alerts-on-your-sensor.md)
