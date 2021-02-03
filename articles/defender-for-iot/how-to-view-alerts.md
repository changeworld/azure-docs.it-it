---
title: Visualizzare gli avvisi
description: Consente di visualizzare gli avvisi in base a diverse categorie e di utilizzare le funzionalità di ricerca per individuare gli avvisi di interesse.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: bce83e11a0d4567d37e78388445c108385ca9a61
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509071"
---
# <a name="view-alerts"></a>Visualizzare gli avvisi

Questo articolo descrive come visualizzare gli avvisi generati dal sensore e gestirli con gli strumenti di avviso.

È possibile visualizzare gli avvisi in base a diverse categorie, ad esempio gli avvisi archiviati o aggiunti. In alternativa, è possibile cercare gli avvisi di interesse, ad esempio gli avvisi in base a un indirizzo IP o MAC.  

È anche possibile visualizzare gli avvisi dal dashboard del sensore.

Per visualizzare gli avvisi:

- Selezionare **avvisi** dal menu laterale. La finestra **avvisi** Visualizza gli avvisi rilevati dal sensore.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-screen.png" alt-text="Visualizzazione della schermata avvisi.":::

## <a name="view-alerts-by-category"></a>Visualizza avvisi per categoria

È possibile visualizzare gli avvisi in base a diverse categorie dalla visualizzazione principale degli **avvisi** . Selezionare un avviso per esaminare i dettagli e gestire l'evento.

| Ordina per tipo | Descrizione |
|--|--|
| **Avvisi importanti** | Avvisi ordinati in base alla priorità. |
| **Avvisi bloccati** | Avvisi che l'utente ha aggiunto per un'ulteriore analisi. Gli avvisi aggiunti non vengono archiviati e vengono archiviati per 14 giorni nella cartella bloccata. |
| **Avvisi recenti** | Avvisi ordinati in base all'ora. |
| **Avvisi riconosciuti** | Avvisi che sono stati confermati e non gestiti oppure che sono stati disattivati e non sono stati disattivati. |
| **Avvisi archiviati** | Avvisi che il sistema ha archiviato automaticamente. Solo l'utente amministratore può accedervi. |

## <a name="search-for-alerts-of-interest"></a>Cerca avvisi di interesse

La visualizzazione principale degli **avvisi** fornisce varie funzionalità di ricerca che consentono di trovare gli avvisi di interesse.

:::image type="content" source="media/how-to-work-with-alerts-sensor/main-alerts-view.png" alt-text="Schermata di formazione degli avvisi.":::

### <a name="text-search"></a>Ricerca di testo 

Utilizzare l'opzione di **ricerca gratuita** per cercare gli avvisi in base a testo, numeri o caratteri.

Per eseguire la ricerca:

- Digitare il testo richiesto nel campo **ricerca gratuita** e premere INVIO sulla tastiera.

Per cancellare la ricerca:

- Eliminare il testo nel campo di **ricerca disponibile** e premere INVIO sulla tastiera.

### <a name="device-group-or-device-ip-address-search"></a>Ricerca di indirizzi IP del dispositivo o del gruppo di dispositivi

Cercare gli avvisi che fanno riferimento a indirizzi IP o gruppi di dispositivi specifici. I gruppi di dispositivi vengono creati nella mappa del dispositivo.

Per usare i filtri avanzati:

1. Selezionare **filtri avanzati** dalla visualizzazione principale degli **avvisi** .

2. Scegliere un gruppo di dispositivi o un dispositivo.

3. Selezionare **Conferma**.

4. Per cancellare la ricerca, selezionare **Clear All (Cancella tutto**).

### <a name="security-versus-operational-alert-search"></a>Sicurezza rispetto alla ricerca operativa degli avvisi

Spostarsi tra la visualizzazione degli avvisi operativi e di sicurezza:

- Gli avvisi di **sicurezza** rappresentano il potenziale traffico malware, le anomalie di rete, le violazioni dei criteri e le violazioni del protocollo.

- Gli avvisi **operativi** rappresentano le anomalie di rete, le violazioni dei criteri e le violazioni del protocollo.

Quando nessuna delle opzioni è selezionata, vengono visualizzati tutti gli avvisi.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-security.png" alt-text="Sicurezza nella schermata avvisi.":::

## <a name="alert-page-options"></a>Opzioni pagina avviso

I messaggi di avviso forniscono le seguenti azioni:

- Selezionare questa selezione :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-an-alert-icon.png" border="false"::: per confermare un avviso.

- Selezionare questa selezione :::image type="icon" source="media/how-to-work-with-alerts-sensor/unacknowledge-an-alert-icon.png" border="false"::: per non riconoscere un avviso.

- Selezionare questa impostazione :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: per bloccare un avviso.

- Selezionare :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: per Rimuovi un avviso.

- Selezionare questa selezione :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-all-alerts-icon.png" border="false"::: per confermare tutti gli avvisi.

- Selezionare :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-and-acknowledge-all-alerts.png" border="false"::: per apprendere e riconoscere tutti gli avvisi.

- Selezionare :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-csv.png" border="false"::: questa opzione per esportare l'elenco degli avvisi in un file CSV e selezionare l'opzione Esporta. Scegliere **Esporta avvisi** per l'opzione normale da esportare a CSV. In alternativa, scegliere **Esporta avvisi estesi** per poter aggiungere righe separate per ulteriori informazioni su un avviso nel file CSV.

## <a name="alert-pop-up-window-options"></a>Opzioni finestra popup avviso

- Selezionare l' :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-pdf.png" border="false"::: icona per scaricare un report degli avvisi come file PDF.

- Selezionare l' :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-pcap.png" border="false"::: icona per scaricare il file PCAP. Il file è visualizzabile con Wireshark, l'analizzatore del protocollo di rete gratuito.

- Selezionare :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-filtered-pcap.png" border="false"::: questa finestra per scaricare un file PCAP filtrato contenente solo i pacchetti di avviso pertinenti, riducendo così le dimensioni del file di output e consentendo un'analisi più mirata. È possibile visualizzare il file usando Wireshark.

- Selezionare l' :::image type="icon" source="media/how-to-work-with-alerts-sensor/show-alert-in-timeline.png" border="false"::: icona per visualizzare l'avviso nella sequenza temporale dell'evento.

- Selezionare l' :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: icona per aggiungere l'avviso.

- Selezionare l' :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: icona per Rimuovi l'avviso.

- Selezionare :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-icon.png" border="false"::: questa finestra per approvare il traffico (solo analisti di sicurezza e amministratori).

- Selezionare un dispositivo per visualizzarlo nella mappa del dispositivo.

## <a name="next-steps"></a>Passaggi successivi

[Gestire gli eventi degli avvisi](how-to-manage-the-alert-event.md)

[Accelerare i flussi di lavoro degli avvisi](how-to-accelerate-alert-incident-response.md)
