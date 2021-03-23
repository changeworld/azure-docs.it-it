---
title: Acquisire informazioni su minacce locali, globali e a livello di area
description: Ottenere informazioni sulle minacce globali, regionali e locali usando la mappa del sito nella console di gestione locale.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: db3b9fbca9acd6c4ce1cfe137a4024f66d8a6292
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784119"
---
# <a name="gain-insight-into-global-regional-and-local-threats"></a>Acquisire informazioni su minacce locali, globali e a livello di area

La mappa del sito nella console di gestione locale consente di ottenere una copertura completa della sicurezza dividendo la rete in segmenti geografici e logici che riflettono la topologia aziendale:

- **Livello di struttura geografica**: un sito riflette un numero di dispositivi raggruppati in base a una posizione geografica presentata sulla mappa. Per impostazione predefinita, Azure Defender per le cose offre una mappa mondiale. Si aggiorna la mappa in modo da riflettere la struttura aziendale o aziendale. Usare, ad esempio, una mappa che riflette i siti in un paese specifico, in una città o in un campus industriale. Quando il colore del sito viene modificato sulla mappa, fornisce al team SOC un'indicazione dello stato critico del sistema nella struttura.

  La mappa è interattiva e consente di aprire ogni sito e approfondire le informazioni di questo sito.

- **Livello logico globale**: una business unit è un modo per dividere l'azienda in segmenti logici in base a settori specifici. Quando si esegue questa operazione, la topologia aziendale viene riflessa sulla mappa.

  Ad esempio, una società globale che contiene Glass Factory, fabbriche di plastica e fabbriche di automobili può essere gestita come tre diverse business unit. Un sito fisico situato a Toronto include tre diverse linee di produzione di vetro, una linea di produzione di plastica e una linea di produzione del motore di camion. Quindi, il sito dispone di rappresentanti di tutte e tre le business unit.

- **Livello di area geografica**: creare aree per dividere un'azienda globale in aree geografiche. Ad esempio, la società descritta potrebbe usare le aree America del Nord, Europa occidentale ed Europa orientale. America del Nord dispone di Factory di tutte e tre le business unit. L'Europa occidentale ha fabbriche e fabbriche di automobili ed Europa orientale ha solo le fabbriche plastiche.

- **Livello di segmento logico locale**: una zona è un segmento logico all'interno di un sito che definisce, ad esempio, un'area funzionale o una linea di produzione. L'uso delle zone consente l'applicazione di criteri di sicurezza rilevanti per la definizione della zona. Ad esempio, un sito che contiene cinque linee di produzione può essere segmentato in cinque zone.

- **Livello di visualizzazione locale**: una visualizzazione locale di un'installazione a sensore singolo fornisce informazioni sullo stato operativo e di sicurezza dei dispositivi connessi.

## <a name="work-with-site-map-views"></a>Utilizzare le viste della mappa del sito

La console di gestione locale fornisce una visualizzazione complessiva della rete industriale in una mappa correlata al contesto. La visualizzazione generale della mappa presenta la mappa globale dell'organizzazione con la posizione geografica di ogni sito.

:::image type="content" source="media/how-to-work-with-maps/enterprise.png" alt-text="Screenshot della visualizzazione mappa dell'organizzazione.":::

### <a name="color-coded-map-views"></a>Visualizzazioni mappa con codifica a colori

**Verde**: il numero di eventi di sicurezza è inferiore alla soglia definita da Defender for Internet per il sistema. Non è richiesto alcun intervento.

**Giallo**: il numero di eventi di sicurezza è uguale alla soglia definita da Defender for Internet per il sistema. Provare a esaminare gli eventi.  

**Rosso**: il numero di eventi di sicurezza è superiore alla soglia definita da Defender for Internet per il sistema. Eseguire un'azione immediata.

### <a name="risk-level-map-views"></a>Viste mappa a livello di rischio

**Valutazione** dei rischi: la vista valutazione dei rischi Visualizza informazioni sui rischi per il sito. Le informazioni sul rischio consentono di definire le priorità di mitigazione e di creare una mappa stradale per pianificare i miglioramenti della sicurezza.

**Risposta agli eventi imprevisti**: ottenere una visualizzazione centralizzata di tutti gli avvisi non riconosciuti in ogni sito all'interno dell'azienda. È possibile eseguire il drill-down e gestire gli avvisi rilevati in un sito specifico.

:::image type="content" source="media/how-to-work-with-maps/incident-responses.png" alt-text="Screenshot della visualizzazione mappa aziendale con risposta agli eventi imprevisti.":::

**Attività dannosa**: se è stato rilevato malware, il sito viene visualizzato in rosso. Ciò indica che è necessario intraprendere un'azione immediata.

:::image type="content" source="media/how-to-work-with-maps/malicious-activity.png" alt-text="Screenshot della visualizzazione mappa aziendale con attività dannose.":::

**Avvisi operativi**: questa vista mappa per i sistemi OT fornisce una migliore comprensione del sistema OT che può riscontrare interventi operativi, ad esempio interruzioni del PLC, caricamento del firmware e caricamento del programma.

:::image type="content" source="media/how-to-work-with-maps/operational-alerts.png" alt-text="Screenshot della visualizzazione mappa aziendale con avvisi operativi.":::

Per scegliere una vista mappa:

1. Selezionare la **visualizzazione predefinita** dalla mappa.
2. Selezionare una visualizzazione.

:::image type="content" source="media/how-to-work-with-maps/map-views.png" alt-text="Screenshot della visualizzazione predefinita della mappa del sito.":::

## <a name="update-the-site-map-image"></a>Aggiornare l'immagine della mappa del sito

Defender for Internet è una mappa globale predefinita. È possibile modificarlo in modo che corrisponda all'organizzazione, ad esempio una mappa paese o una mappa City. 

Per sostituire la mappa:

1. Nel riquadro sinistro selezionare impostazioni di **sistema**.

2. Selezionare la **modifica mappa del sito** e caricare il file grafico per sostituire la mappa predefinita.

## <a name="next-step"></a>Passaggio successivo

[Visualizzare gli avvisi](how-to-view-alerts.md)
