---
title: Analizzare gli eventi imprevisti con Azure Sentinel| Microsoft Docs
description: Questa esercitazione illustra come usare Azure Sentinel creare regole di avviso avanzate che generano eventi imprevisti che è possibile assegnare e analizzare.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2021
ms.author: yelevin
ms.openlocfilehash: 8980a8920b4f41f5a8e6afe106415032eef2055b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375840"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Esercitazione: Analizzare gli eventi imprevisti con Azure Sentinel

> [!IMPORTANT]
> Il grafico dell'indagine è ora disponibile in **Disponibilità generale.** 

Questa esercitazione consente di analizzare gli eventi imprevisti con Azure Sentinel. Dopo aver connesso le origini dati a Azure Sentinel, si vuole ricevere una notifica quando si verifica qualcosa di sospetto. A tale scopo, è Azure Sentinel creare regole di avviso avanzate che generano eventi imprevisti che è possibile assegnare e analizzare.

Questo articolo riguarda:
> [!div class="checklist"]
> * Analizzare gli eventi imprevisti
> * Usare il grafico di indagine
> * Rispondere alle minacce

Un evento imprevisto può includere più avvisi. Si tratta di un'aggregazione di tutte le evidenze rilevanti per un'indagine specifica. Un evento imprevisto viene creato in base alle regole di analisi create nella **pagina Analisi.** Le proprietà correlate agli avvisi, ad esempio la gravità e lo stato, vengono impostate a livello di evento imprevisto. Dopo aver Azure Sentinel quali tipi di minacce si stanno cercando e come trovarle, è possibile monitorare le minacce rilevate analizzando gli eventi imprevisti.

## <a name="prerequisites"></a>Prerequisiti
- Sarà possibile analizzare l'evento imprevisto solo se sono stati usati i campi di mapping delle entità quando si configura la regola di analisi. Il grafico di indagine richiede che l'evento imprevisto originale includa entità.

- Se si dispone di un utente guest che deve assegnare eventi imprevisti, all'utente deve essere assegnato il ruolo [Lettore](../active-directory/roles/permissions-reference.md#directory-readers) directory nel tenant Azure AD locale. Questo ruolo viene assegnato agli utenti normali (non guest) per impostazione predefinita.

## <a name="how-to-investigate-incidents"></a>Come analizzare gli eventi imprevisti

1. Selezionare **Eventi imprevisti**. La **pagina Eventi** imprevisti consente di conoscere il numero di eventi imprevisti disponibili, il numero di eventi aperti, il numero impostato su **In** corso e il numero di eventi chiusi. Per ogni evento imprevisto è possibile visualizzare l'ora in cui si è verificato e lo stato dell'evento imprevisto. Esaminare la gravità per decidere quali eventi imprevisti gestire per primi.

    ![Visualizzare la gravità dell'evento imprevisto](media/tutorial-investigate-cases/incident-severity.png)

1. È possibile filtrare gli eventi imprevisti in base alle esigenze, ad esempio in base allo stato o alla gravità.

1. Per avviare un'indagine, selezionare un evento imprevisto specifico. A destra è possibile visualizzare informazioni dettagliate sull'evento imprevisto, tra cui la gravità, il riepilogo del numero di entità coinvolte, gli eventi non elaborati che hanno attivato l'evento imprevisto e l'ID univoco dell'evento imprevisto.

1. Per visualizzare altri dettagli sugli avvisi e sulle  entità nell'evento imprevisto, selezionare Visualizza dettagli completi nella pagina dell'evento imprevisto ed esaminare le schede pertinenti che riepilogano le informazioni sull'evento imprevisto. 

    ![Visualizzare i dettagli relativi all'avviso](media/tutorial-investigate-cases/incident-timeline.png)

    Ad esempio:

    - Nella scheda **Sequenza temporale** esaminare la sequenza temporale degli avvisi e dei segnalibri nell'evento imprevisto, che consente di ricostruire la sequenza temporale dell'attività dell'utente malintenzionato.
    - Nella scheda **Avvisi** esaminare l'avviso stesso. È possibile visualizzare tutte le informazioni rilevanti sull'avviso, ad esempio la query che ha attivato l'avviso, il numero di risultati restituiti per ogni query e la possibilità di eseguire playbook sugli avvisi. Per approfondire ulteriormente l'evento imprevisto, selezionare il numero di **eventi**. Verrà aperta la query che ha generato i risultati e gli eventi che hanno attivato l'avviso in Log Analytics. 
    - Nella scheda **Entità è** possibile visualizzare tutte le entità di cui è stato eseguito il mapping come parte della definizione della regola di avviso.

1. Se si sta esaminando attivamente un evento imprevisto, è buona idea impostare lo stato dell'evento su **In** corso fino a quando non lo si chiude.

1. Gli eventi imprevisti possono essere assegnati a un utente specifico. Per ogni evento imprevisto è possibile assegnare un proprietario impostando il **campo Proprietario dell'evento** imprevisto. Tutti gli eventi imprevisti iniziano come non assegnati. È anche possibile aggiungere commenti in modo che altri analisti siano in grado di comprendere ciò che si è analizzato e quali sono i problemi relativi all'evento imprevisto.

    ![Assegnare l'evento imprevisto all'utente](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Selezionare **Analisi** per visualizzare la mappa di indagine.

## <a name="use-the-investigation-graph-to-deep-dive"></a>Usare il grafico di indagine per un approfondimento

Il grafico di indagine consente agli analisti di porre le domande giuste per ogni indagine. Il grafico di indagine consente di comprendere l'ambito e identificare la causa radice di una potenziale minaccia per la sicurezza correlando i dati rilevanti con qualsiasi entità coinvolta. È possibile approfondire e analizzare qualsiasi entità presentata nel grafico selezionandola e scegliendo tra diverse opzioni di espansione.  
  
Il grafico di indagine offre:

- **Contesto visivo dai dati non elaborati:** il grafico visivo live visualizza le relazioni tra entità estratte automaticamente dai dati non elaborati. In questo modo è possibile visualizzare facilmente le connessioni tra origini dati diverse.

- **Individuazione dell'ambito di indagine completo:** espandere l'ambito di indagine usando query di esplorazione incorporate per visualizzare l'ambito completo di una violazione.

- **Passaggi di indagine predefiniti:** usare opzioni di esplorazione predefinite per assicurarsi di porre le domande giuste di fronte a una minaccia.

Per usare il grafico di indagine:

1. Selezionare un evento imprevisto e quindi **selezionare Analizza**. Verrà quindi avviato il grafo dell'indagine. Il grafico fornisce una mappa illustrativa delle entità direttamente connesse all'avviso e di ogni risorsa connessa ulteriormente.

   > [!IMPORTANT] 
   > - Sarà possibile analizzare l'evento imprevisto solo se sono stati usati i campi di mapping delle entità quando si configura la regola di analisi. Il grafico di indagine richiede che l'evento imprevisto originale includa entità.
   >
   > - Azure Sentinel attualmente supporta l'analisi degli eventi imprevisti fino a **30 giorni fa.**

   ![Visualizzare la mappa](media/tutorial-investigate-cases/map1.png)

1. Selezionare un'entità per aprire il **riquadro Entità** in modo da poter esaminare le informazioni sull'entità.

    ![Visualizzare le entità nella mappa](media/tutorial-investigate-cases/map-entities.png)
  
1. Espandere l'indagine passando il puntatore del mouse su ogni entità per visualizzare un elenco di domande progettate dagli esperti di sicurezza e dagli analisti per ogni tipo di entità per approfondire l'indagine. Queste opzioni vengono chiamate query **di esplorazione**.

    ![Esplorare altri dettagli](media/tutorial-investigate-cases/exploration-cases.png)

   Ad esempio, in un computer è possibile richiedere avvisi correlati. Se si seleziona una query di esplorazione, i diritti risultanti vengono aggiunti nuovamente al grafico. In questo esempio la selezione **di Avvisi correlati** ha restituito gli avvisi seguenti nel grafico:

    ![Visualizzare gli avvisi correlati](media/tutorial-investigate-cases/related-alerts.png)

1. Per ogni query di esplorazione, è possibile selezionare l'opzione per aprire i risultati degli eventi non elaborati e la query usata in Log Analytics, selezionando **Eventi \>**.

1. Per comprendere l'evento imprevisto, il grafico offre una sequenza temporale parallela.

    ![Visualizzare la sequenza temporale nella mappa](media/tutorial-investigate-cases/map-timeline.png)

1. Passare il puntatore del mouse sulla sequenza temporale per vedere quali elementi del grafico si sono verificati in un punto nel tempo.

    ![Usare la sequenza temporale nella mappa per analizzare gli avvisi](media/tutorial-investigate-cases/use-timeline.png)

## <a name="closing-an-incident"></a>Chiusura di un evento imprevisto

Dopo aver risolto un evento imprevisto specifico, ad esempio quando l'indagine ha raggiunto la sua conclusione, è necessario impostare lo stato dell'evento imprevisto su **Chiuso**. Quando si esegue questa operazione, verrà chiesto di classificare l'evento imprevisto specificando il motivo per cui lo si sta chiudendo. Questo passaggio è obbligatorio. Fare **clic su Seleziona** classificazione e scegliere una delle opzioni seguenti nell'elenco a discesa:

- Vero positivo - attività sospetta
- Positivo non dannoso - sospetto ma previsto
- Falso positivo - logica di avviso non corretta
- Falso positivo - dati non corretti
- Indeterminato

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-dropdown.png" alt-text="Screenshot che evidenzia le classificazioni disponibili nell'elenco Seleziona classificazione.":::

Dopo aver scelto la classificazione appropriata, aggiungere un testo descrittivo nel **campo** Commento. Ciò sarà utile nel caso in cui sia necessario fare riferimento a questo evento imprevisto. Al **termine,** fare clic su Applica per chiudere l'evento imprevisto.

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-comment-apply.png" alt-text="{alt-text}":::

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come iniziare a esaminare gli eventi imprevisti usando Azure Sentinel. Continuare con l'esercitazione [su come rispondere alle minacce usando playbook automatizzati.](tutorial-respond-threats-playbook.md)
> [!div class="nextstepaction"]
> [Rispondere alle minacce](tutorial-respond-threats-playbook.md) per automatizzare le risposte alle minacce.

