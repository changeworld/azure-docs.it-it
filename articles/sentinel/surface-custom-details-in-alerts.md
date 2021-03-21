---
title: Dettagli personalizzati della superficie negli avvisi di Azure Sentinel | Microsoft Docs
description: Estrai ed estraggono i dettagli degli eventi personalizzati negli avvisi delle regole di analisi di Azure Sentinel, per ottenere informazioni più dettagliate sugli eventi imprevisti
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 45f0ef5366d97c275c40d4d436020dbaf3501d42
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102456256"
---
# <a name="surface-custom-event-details-in-alerts-in-azure-sentinel"></a>Dettagli dell'evento personalizzato di Surface negli avvisi in Sentinel di Azure 

> [!IMPORTANT]
>
> - La funzionalità dettagli personalizzati è in **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

## <a name="introduction"></a>Introduzione

Le [regole di analisi query pianificate](tutorial-detect-threats-custom.md) analizzano **gli eventi** dalle origini dati connesse a Sentinel di Azure e generano **avvisi** quando il contenuto di questi eventi è significativo dal punto di vista della sicurezza. Questi avvisi vengono ulteriormente analizzati, raggruppati e filtrati dai vari motori di Sentinel di Azure e distillati in **eventi imprevisti** che garantiscano l'attenzione di un analista Soc. Tuttavia, quando l'analista Visualizza l'evento imprevisto, solo le proprietà degli avvisi dei componenti sono immediatamente visibili. Come ottenere il contenuto effettivo, ovvero le informazioni contenute negli eventi, è necessario eseguire alcune operazioni di scavo.

Usando la funzionalità **dettagli personalizzati** nella **creazione guidata regola di analisi**, è possibile esporre i dati degli eventi negli avvisi costruiti da tali eventi, rendendo la parte dei dati dell'evento delle proprietà degli avvisi. Questo consente di ottenere la visibilità immediata del contenuto degli eventi negli eventi imprevisti, consentendo di valutare, analizzare, trarre conclusioni e rispondere con una velocità e un'efficienza notevolmente maggiori.

La procedura descritta di seguito fa parte della procedura guidata di creazione delle regole di analisi. Viene trattato in modo indipendente per risolvere lo scenario di aggiunta o modifica di dettagli personalizzati in una regola di analisi esistente.

## <a name="how-to-surface-custom-event-details"></a>Procedura per la superficie dei dettagli degli eventi personalizzati

1. Dal menu di navigazione di Azure Sentinel selezionare **Analytics**.

1. Selezionare una regola di query pianificata e fare clic su **modifica**. In alternativa, creare una nuova regola facendo clic su **crea &#10132; regola di query pianificata** nella parte superiore della schermata.

1. Fare clic sulla scheda **impostare la logica della regola** .

1. Nella sezione **miglioramento avvisi** selezionare **dettagli personalizzati**.

    :::image type="content" source="media/surface-custom-details-in-alerts/alert-enhancement.png" alt-text="Trovare e selezionare i dettagli personalizzati":::

1. Nella sezione Now-Expanded **Custom Details** aggiungere coppie chiave-valore corrispondenti ai dettagli che si desidera visualizzare:

    1. Nel campo **chiave** immettere un nome a scelta che verrà visualizzato come nome del campo negli avvisi.

    1. Nel campo **valore** selezionare il parametro dell'evento che si desidera esporre negli avvisi dall'elenco a discesa. Questo elenco verrà popolato con i valori corrispondenti ai campi delle tabelle che rappresentano l'oggetto della query della regola.
    
        :::image type="content" source="media/surface-custom-details-in-alerts/custom-details.png" alt-text="Aggiungi dettagli personalizzati":::

1. Fare clic su **Aggiungi nuovo** per ottenere ulteriori dettagli, ripetendo gli ultimi passaggi per definire le coppie chiave-valore. 

    Se si cambia idea o si commette un errore, è possibile rimuovere un dettaglio personalizzato facendo clic sull'icona a forma di cestino accanto all'elenco a discesa **valore** per quel dettaglio.

1. Al termine della definizione dei dettagli personalizzati, fare clic sulla scheda **revisione e creazione** . Una volta completata la convalida della regola, fare clic su **Salva**.

    > [!NOTE]
    > 
    >**Limiti del servizio**
    > - È possibile definire **fino a 20 dettagli personalizzati** in una singola regola di analisi.
    >
    > - Il limite di dimensioni per tutti i dettagli personalizzati, collettivamente, è **2 KB**.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come esporre i dettagli personalizzati negli avvisi usando le regole di analisi sentinella di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Ottenere l'immagine completa sulle [regole di analisi query pianificate](tutorial-detect-threats-custom.md).
- Altre informazioni sulle [entità in Sentinel di Azure](entities-in-azure-sentinel.md).
