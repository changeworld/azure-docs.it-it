---
title: Eseguire il mapping dei campi dati alle entità Sentinel di Azure | Microsoft Docs
description: Eseguire il mapping dei campi dati nelle tabelle alle entità Sentinel di Azure nelle regole di analisi, per informazioni sugli eventi imprevisti migliori
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
ms.openlocfilehash: cb91d269f6b166510db54637d17d776e71137408
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102456295"
---
# <a name="map-data-fields-to-entities-in-azure-sentinel"></a>Eseguire il mapping dei campi dati alle entità in Sentinel di Azure 

> [!IMPORTANT]
>
> - La nuova versione della funzionalità di mapping delle entità è in **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

> [!IMPORTANT]
>
> - Per informazioni importanti sulla compatibilità con le versioni precedenti e sulle differenze tra le versioni nuove e precedenti del mapping di entità, vedere le [Note sulla nuova versione](#notes-on-the-new-version) alla fine di questo documento.

## <a name="introduction"></a>Introduzione

Il mapping delle entità è parte integrante della configurazione delle [regole di analisi query pianificate](tutorial-detect-threats-custom.md). Arricchisce l'output delle regole (avvisi ed eventi imprevisti) con informazioni essenziali che fungono da blocchi predefiniti di qualsiasi processo di analisi e azioni correttive che seguono.

La procedura descritta di seguito fa parte della procedura guidata di creazione delle regole di analisi. Viene trattato in modo indipendente per risolvere lo scenario di aggiunta o modifica di mapping di entità in una regola di analisi esistente.

## <a name="how-to-map-entities"></a>Come eseguire il mapping delle entità

1. Dal menu di navigazione di Azure Sentinel selezionare **Analytics**.

1. Selezionare una regola di query pianificata e fare clic su **modifica**. In alternativa, creare una nuova regola facendo clic su **crea &#10132; regola di query pianificata** nella parte superiore della schermata.

1. Fare clic sulla scheda **impostare la logica della regola** .

    :::image type="content" source="media/map-data-fields-to-entities/map-entities.png" alt-text="Eseguire il mapping di campi a entità":::

1. Nella sezione **miglioramento avvisi** , in **mapping entità**, selezionare un tipo di entità nell'elenco a discesa **tipo di entità** .

1. Selezionare un **identificatore** per l'entità. Gli identificatori sono attributi di un'entità che è in grado di identificarli a sufficienza. Sceglierne uno dall'elenco a discesa **identificatore** , quindi scegliere un campo dati dall'elenco a discesa **valore** corrispondente all'identificatore. Con alcune eccezioni, l'elenco di **valori** viene popolato dai campi dati nella tabella definita come oggetto della query della regola.

    È possibile definire **fino a tre identificatori** per un'entità specificata. Alcuni identificatori sono obbligatori, mentre altri sono facoltativi. È necessario scegliere almeno un identificatore obbligatorio. In caso contrario, verrà visualizzato un messaggio di avviso che indica quali identificatori sono obbligatori. Per ottenere risultati ottimali, per la massima identificazione univoca, è necessario usare **identificatori sicuri** quando possibile e l'uso di più identificatori sicuri consentirà una maggiore correlazione tra le origini dati. Vedere l'elenco completo delle [entità e degli identificatori](entities-reference.md)disponibili.

1. Fare clic su **Aggiungi nuova entità** per eseguire il mapping di più entità. È possibile eseguire il mapping **di un massimo di cinque entità** in un'unica regola di analisi. È anche possibile eseguire il mapping di più di uno dello stesso tipo. È ad esempio possibile eseguire il mapping di due entità **IP** , una da un campo dell' *indirizzo IP di origine* e una da un campo dell' *indirizzo IP di destinazione* . In questo modo è possibile tenerne traccia.

    Se si cambia idea o si commette un errore, è possibile rimuovere un mapping di entità facendo clic sull'icona del cestino accanto all'elenco a discesa entità.

1. Al termine del mapping delle entità, fare clic sulla scheda **revisione e creazione** . Una volta completata la convalida della regola, fare clic su **Salva**.

## <a name="notes-on-the-new-version"></a>Note sulla nuova versione

- Se in precedenza sono stati definiti mapping di entità per questa regola di analisi usando la versione precedente, tali mapping vengono visualizzati nel codice della query. I mapping di entità definiti nella nuova versione **non vengono visualizzati nel codice della query**. Le regole di analisi possono supportare solo una versione di mapping di entità alla volta e la nuova versione ha la precedenza. Pertanto, ogni singolo mapping definito in questo caso provocherà il fatto che **tutti** i mapping definiti nel codice della query vengano **ignorati** durante l'esecuzione della query. 

- Se è ancora necessario usare la **versione precedente** del mapping di entità (purché la nuova versione sia ancora in anteprima), è comunque possibile accedervi usando un flag di funzionalità nell'URL. Posizionare il cursore tra `https://portal.azure.com/` e `#blade` e inserire il testo `?feature.EntityMapping=false` .

  - I limiti della versione precedente continueranno a essere applicati. È possibile eseguire il mapping solo dell'utente, dell'host, dell'indirizzo IP, dell'URL e delle entità hash del file e solo di uno di essi.

  - È necessario **rimuovere** tutti i mapping di entità creati usando la nuova versione **prima** di tornare alla versione precedente. in caso contrario, i mapping di entità che usano la versione precedente **non funzioneranno**.

- Quando la nuova versione del mapping di entità è disponibile a livello generale, non sarà più possibile usare la versione precedente. Si consiglia di eseguire la migrazione dei mapping di entità obsoleti alla nuova versione.


## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come eseguire il mapping dei campi dati alle entità in Azure Sentinel Analytics Rules. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Ottenere l'immagine completa sulle [regole di analisi query pianificate](tutorial-detect-threats-custom.md).
- Altre informazioni sulle [entità in Sentinel di Azure](entities-in-azure-sentinel.md).
