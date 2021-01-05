---
title: Visualizzare le informazioni negli avvisi
description: Selezionare un avviso dalla finestra avvisi per esaminare i dettagli.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/03/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 804cdbd6266f2e77b5562d914bac089fce80f645
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97840653"
---
# <a name="view-information-in-alerts"></a>Visualizzare le informazioni negli avvisi

Selezionare un avviso dalla finestra **avvisi** per esaminare i dettagli dell'avviso. I dettagli includono le seguenti informazioni:

- Metadati degli avvisi

- Informazioni sul traffico, i dispositivi e l'evento

- Collegamenti a dispositivi connessi nella mappa del dispositivo

- Commenti definiti dagli analisti e dagli amministratori della sicurezza

- Suggerimenti per l'analisi dell'evento

## <a name="alert-metadata"></a>Metadati degli avvisi

I dettagli dell'avviso includono i metadati degli avvisi seguenti:

  - ID avviso

  - Motore dei criteri che ha attivato l'avviso

  - Data e ora in cui è stato attivato l'avviso

:::image type="content" source="media/how-to-work-with-alerts-sensor/internet-connectivity-detection-unauthorized.png" alt-text="È stata rilevata una connettività Internet non autorizzata.":::

## <a name="information-about-devices-traffic-and-the-event"></a>Informazioni sui dispositivi, il traffico e l'evento

Il messaggio di avviso fornisce informazioni su:

  - Dispositivi rilevati.

  - Traffico rilevato tra i dispositivi, ad esempio i protocolli e i codici di funzione.

  - Informazioni dettagliate sulle implicazioni dell'evento.

È possibile utilizzare queste informazioni per decidere come gestire l'evento di avviso.

## <a name="links-to-connected-devices-in-the-device-map"></a>Collegamenti a dispositivi connessi nella mappa del dispositivo

Per altre informazioni sui dispositivi connessi ai dispositivi rilevati, è possibile selezionare un'immagine del dispositivo nell'avviso e visualizzare i dispositivi connessi nella mappa.

:::image type="content" source="media/how-to-work-with-alerts-sensor/rcp-operation-failed.png" alt-text="Operazione RCP non riuscita.":::

:::image type="content" source="media/how-to-work-with-alerts-sensor/devices-screen-populated.png" alt-text="Screenshot dei dispositivi.":::

La mappa filtra al dispositivo selezionato e ad altri dispositivi connessi. Nella mappa viene inoltre visualizzata la finestra di dialogo **Proprietà rapide** per i dispositivi rilevati negli avvisi.

## <a name="comments-defined-by-security-analysts-and-administrators"></a>Commenti definiti dagli analisti e dagli amministratori della sicurezza 

Gli avvisi possono includere un elenco di commenti predefiniti. Ad esempio, i commenti possono essere istruzioni per le azioni di mitigazione da intraprendere o nomi di singoli utenti da contattare sull'evento.

Quando si gestisce un evento di avviso, è possibile scegliere il commento o i commenti che meglio riflettono lo stato dell'evento o i passaggi eseguiti per esaminare l'avviso.

I commenti selezionati vengono salvati nel messaggio di avviso. L'utilizzo di commenti migliora la comunicazione tra singoli utenti e team durante l'analisi di un evento di avviso. Di conseguenza, i commenti possono accelerare il tempo di risposta agli eventi imprevisti.

Un amministratore o un analista di sicurezza predefinisce i commenti. I commenti selezionati non vengono trasmessi ai sistemi partner definiti nelle regole di invio.

Dopo aver esaminato le informazioni in un avviso, è possibile eseguire diverse operazioni forensi per gestire l'evento di avviso. Ad esempio:

- Analizzare l'attività recente del dispositivo (report di data mining). 

- Analizzare altri eventi che si sono verificati contemporaneamente (sequenza temporale Eventi). 

- Analizza il traffico completo degli eventi (file PCAP).

## <a name="pcap-files"></a>File PCAP

In alcuni casi, è possibile accedere a un file PCAP dal messaggio di avviso. Questo potrebbe essere utile se si desiderano informazioni più dettagliate sul traffico di rete associato.

Per scaricare un file PCAP, selezionare l' :::image type="content" source="media/how-to-work-with-alerts-sensor/download-pcap.png" alt-text="icona di download."::: nella parte superiore destra della finestra di dialogo **Dettagli avviso** .

## <a name="recommendations-for-investigating-an-event"></a>Suggerimenti per l'analisi di un evento 

Nell'area **consigliata** di un avviso vengono visualizzate informazioni che possono aiutare a comprendere meglio un evento. Esaminare queste informazioni prima di gestire l'evento di avviso o agire sul dispositivo o sulla rete.

## <a name="see-also"></a>Vedere anche

[Accelerare i flussi di lavoro degli avvisi](how-to-accelerate-alert-incident-response.md)

[Gestire l'evento di avviso](how-to-manage-the-alert-event.md)
