---
title: Gestire gli eventi imprevisti della sicurezza nel centro sicurezza di Azure | Microsoft Docs
description: Questo documento consente di usare il Centro sicurezza di Azure per gestire gli eventi imprevisti della sicurezza.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 722a508679c74f9d62df07575ffa1006528f4398
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652107"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Gestire gli eventi imprevisti della sicurezza nel centro sicurezza di Azure

La valutazione e l'analisi degli avvisi di sicurezza possono richiedere molto tempo anche per gli analisti di sicurezza più qualificati. Per molti, è difficile capire da dove iniziare. 

Il Centro sicurezza usa l' [analisi](./security-center-alerts-overview.md) per connettere le informazioni tra [avvisi di sicurezza](security-center-managing-and-responding-alerts.md)distinti. Utilizzando queste connessioni, il Centro sicurezza può fornire un'unica visualizzazione di una campagna di attacco e gli avvisi correlati per comprendere le azioni dell'utente malintenzionato e le risorse interessate.

Questa pagina offre una panoramica degli eventi imprevisti nel centro sicurezza.

## <a name="what-is-a-security-incident"></a>Che cos'è un evento imprevisto della sicurezza?

Nel Centro sicurezza, un evento imprevisto della sicurezza è un'aggregazione di tutti gli avvisi relativi a una risorsa, in linea con i modelli delle [catene di attacco](alerts-reference.md#intentions). Gli eventi imprevisti vengono visualizzati nella pagina [avvisi di sicurezza](security-center-managing-and-responding-alerts.md) . Selezionare un evento imprevisto per visualizzare gli avvisi correlati e ottenere altre informazioni.

## <a name="managing-security-incidents"></a>Gestione degli eventi imprevisti della sicurezza

1. Nella pagina avvisi del Centro sicurezza, usare il pulsante **Aggiungi filtro** per filtrare in base al nome dell'avviso per l'evento imprevisto di sicurezza del nome dell'avviso **rilevato su più risorse**. 

    :::image type="content" source="media/security-center-incident/locating-incidents.png" alt-text="Individuazione degli eventi imprevisti nella pagina avvisi del Centro sicurezza di Azure":::

    L'elenco è ora filtrato per visualizzare solo gli eventi imprevisti. Si noti che gli eventi imprevisti di sicurezza hanno un'icona diversa per gli avvisi di sicurezza.

    :::image type="content" source="media/security-center-incident/incidents-list.png" alt-text="Elenco degli eventi imprevisti nella pagina avvisi del Centro sicurezza di Azure":::

1. Per visualizzare i dettagli di un evento imprevisto, selezionarne uno dall'elenco. Viene visualizzato un riquadro laterale con altri dettagli sull'evento imprevisto.

    :::image type="content" source="media/security-center-incident/incident-quick-peek.png" alt-text="Riquadro laterale che mostra i dettagli dell'evento imprevisto":::

1. Per visualizzare altri dettagli, selezionare **Visualizza dettagli completi**.

    [![Rispondere agli eventi imprevisti della sicurezza nel centro sicurezza di Azure](media/security-center-incident/incident-details.png)](media/security-center-incident/incident-details.png#lightbox)

    Il riquadro sinistro della pagina evento imprevisto di sicurezza Mostra informazioni di alto livello sull'evento imprevisto della sicurezza: titolo, gravità, stato, ora attività, descrizione e risorsa interessata. Accanto alla risorsa interessata è possibile visualizzare i tag di Azure pertinenti. Usare questi tag per dedurre il contesto aziendale della risorsa quando si esamina l'avviso.

    Il riquadro destro include la scheda **avvisi** con gli avvisi di sicurezza correlati come parte di questo evento imprevisto. 

    >[!TIP]
    > Per ulteriori informazioni su un avviso specifico, selezionarlo. 

    [![Scheda azione dell'evento imprevisto](media/security-center-incident/incident-take-action-tab.png)](media/security-center-incident/incident-take-action-tab.png#lightbox)

    Per passare alla scheda **azione** , selezionare la scheda o il pulsante nella parte inferiore del riquadro a destra. Usare questa scheda per eseguire altre azioni, ad esempio:
    - *Attenuazione della minaccia* : fornisce la procedura di correzione manuale per questo evento imprevisto della sicurezza
    - *Impedisci attacchi futuri* : fornisce consigli sulla sicurezza che consentono di ridurre la superficie di attacco, aumentare il comportamento di sicurezza ed evitare attacchi futuri
    - *Attiva risposta automatica* : consente di attivare un'app per la logica come risposta a questo evento imprevisto della sicurezza
    - Non *visualizzare avvisi simili* : consente di disattivare gli avvisi futuri con caratteristiche simili se l'avviso non è pertinente per l'organizzazione 

   > [!NOTE]
   > Lo stesso avviso può esistere come parte di un evento imprevisto, nonché essere visibile come un avviso autonomo.

1. Per correggere le minacce nell'evento imprevisto, seguire le procedure di correzione fornite con ogni avviso.


## <a name="next-steps"></a>Passaggi successivi

Questa pagina ha illustrato le funzionalità per gli eventi imprevisti di sicurezza del Centro sicurezza. Per informazioni correlate, vedere le pagine seguenti:

- [Avvisi di sicurezza nel centro sicurezza](security-center-alerts-overview.md)
- [Gestire e rispondere agli avvisi di sicurezza](security-center-managing-and-responding-alerts.md)