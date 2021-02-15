---
title: Rilevare l'attività dei sensori
description: La sequenza temporale dell'evento presenta una cronologia delle attività rilevate sulla rete, inclusi avvisi e azioni di gestione degli avvisi, eventi di rete e operazioni dell'utente, ad esempio l'accesso utente e l'eliminazione dell'utente.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: b8a7679521096cf3d98e8f55748eb0398fdc4ab8
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523601"
---
# <a name="track-sensor-activity"></a>Rilevare l'attività dei sensori

## <a name="event-timeline"></a>Sequenza temporale eventi

La sequenza temporale dell'evento presenta una cronologia delle attività rilevate dal sensore. Ad esempio:

  - Avvisi e azioni di gestione degli avvisi

  - Eventi di rete

  - Operazioni utente, ad esempio l'accesso utente e l'eliminazione di utenti

La sequenza temporale dell'evento fornisce una visualizzazione cronologica degli eventi che si sono verificati nella rete. La sequenza temporale degli eventi consente di comprendere e analizzare la catena di eventi che hanno preceduto e seguito un attacco o un evento imprevisto, che facilita l'analisi e l'analisi forense.

> [!NOTE]
> Gli *amministratori* e gli *analisti della sicurezza* possono eseguire le procedure descritte in questa sezione.

Per visualizzare i registri eventi:

- Dal menu laterale selezionare **Timeline evento**.

   :::image type="content" source="media/how-to-track-sensor-activity/event-timeline.png" alt-text="Visualizzare gli eventi nella sequenza temporale degli eventi.":::

Oltre a visualizzare gli eventi rilevati dal sensore, è possibile aggiungere manualmente gli eventi alla sequenza temporale. Questo processo è utile se l'evento si è verificato in un sistema esterno ma ha un effetto sulla rete ed è importante registrare l'evento e presentarlo come parte della sequenza temporale.

Per aggiungere gli eventi manualmente:

- Selezionare **Crea evento**.

Per esportare le informazioni del registro eventi in un file CSV:

- Selezionare **Esporta**.

## <a name="filter-the-event-timeline"></a>Filtrare la sequenza temporale dell'evento

Filtrare la sequenza temporale per visualizzare i dispositivi e gli eventi di interesse.

Per filtrare la sequenza temporale:

1. Selezionare **filtri avanzati**.

   :::image type="content" source="media/how-to-track-sensor-activity/advance-filters.png" alt-text="Utilizzare la finestra filtri avanzati eventi per filtrare gli eventi.":::

2. Impostare i filtri per gli eventi, come indicato di seguito:

   - **Includi indirizzo**: Visualizza gli eventi per dispositivi specifici.

   - **Escludi indirizzo**: Nascondi gli eventi per dispositivi specifici.

   - **Includi tipi di evento**: Visualizza tipi di eventi specifici.

   - **Escludi tipi di eventi**: Nascondi tipi di eventi specifici.

   - **Gruppo di dispositivi**: selezionare un gruppo di dispositivi, così come è stato definito nella mappa del dispositivo. Vengono presentati solo gli eventi del gruppo.

3. Selezionare **Cancella tutto** per cancellare tutti i filtri selezionati.

4. Eseguire la ricerca solo in base agli **avvisi**, **avvisi e notifiche** o **tutti gli eventi**.

5. Selezionare **Seleziona data** per scegliere una data specifica. Scegliere un giorno, un'ora e un minuto. Vengono visualizzati gli eventi dall'intervallo di tempo selezionato.

6.  Selezionare **operazioni utente** per includere o escludere gli eventi operativi dell'utente.

7.  Selezionare la freccia (**V**) per visualizzare altre informazioni sull'evento:

    - Selezionare gli eventuali avvisi correlati per visualizzare una descrizione dettagliata dell'avviso.

    - Selezionare il dispositivo per visualizzare il dispositivo sulla mappa.

    - Selezionare **Filtra eventi per dispositivi correlati** se si vuole filtrare in base ai dispositivi correlati.

    - Selezionare il **file PCAP** per scaricare il file PCAP (se esistente) che contiene un'acquisizione di pacchetti dell'intera rete in un momento specifico. 
    
      Il file PCAP contiene informazioni tecniche che consentono ai tecnici di rete di determinare i parametri esatti dell'evento. È possibile analizzare il file PCAP con un analizzatore del protocollo di rete, ad esempio Wireshark, un'applicazione open source.

## <a name="next-steps"></a>Passaggi successivi

[Visualizzare gli avvisi](how-to-view-alerts.md)
