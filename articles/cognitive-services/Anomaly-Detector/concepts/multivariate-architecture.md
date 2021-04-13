---
title: Architettura di manutenzione predicativo per l'uso dell'API MultiVariante del rilevatore di anomalie
titleSuffix: Azure Cognitive Services
description: Architettura di riferimento per l'uso delle API multivariate del rilevatore di anomalie per applicare il rilevamento delle anomalie ai dati della serie temporale per la manutenzione predittiva.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: rilevamento anomalie, Machine Learning, algoritmi
ms.openlocfilehash: 4d379cfe01dcbd88531b98a32b45e0b30f6adeef
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315596"
---
# <a name="predictive-maintenance-solution-with-anomaly-detector-multivariate"></a>Soluzione di manutenzione predittiva con rilevamento anomalie multivariato

Molti settori diversi necessitano di soluzioni di manutenzione predittiva per ridurre i rischi e ottenere informazioni dettagliate di utilità pratica attraverso l'elaborazione dei dati dalle apparecchiature. La manutenzione predittiva valuta la condizione delle apparecchiature eseguendo il monitoraggio online. L'obiettivo è quello di eseguire operazioni di manutenzione prima che si verifichino peggioramenti o interruzioni delle apparecchiature.

Il monitoraggio dello stato di integrità delle apparecchiature può risultare complesso, in quanto ogni componente all'interno dell'apparecchiatura può generare decine di segnali, ad esempio vibrazione, orientamento e rotazione.  Questo può essere ancora più complesso quando questi segnali hanno una relazione implicita e devono essere monitorati e analizzati insieme. La definizione di regole diverse per tali segnali e la loro correlazione manuale possono essere costose. La funzionalità MultiVariante del rilevatore di anomalie consente:

* Più segnali correlati da monitorare insieme e le correlazioni tra di essi vengono contabilizzate nel modello.
* In ogni anomalia acquisita, la classificazione dei contributi dei diversi segnali può aiutare con la spiegazione delle anomalie e l'analisi della causa radice dell'evento imprevisto.
* Il modello di rilevamento delle anomalie multivariate viene compilato in modo non supervisionato. I modelli possono essere sottoposti a training in modo specifico per diversi tipi di apparecchiature.

In questo esempio viene fornita un'architettura di riferimento per una soluzione di manutenzione predittiva basata su MultiVariante del rilevatore di anomalie.

## <a name="reference-architecture"></a>Architettura di riferimento

[![Diagramma dell'architettura che inizia con i dati del sensore raccolti sul perimetro con una parte di apparecchiature industriali e tiene traccia della pipeline di elaborazione/analisi fino a un output finale di un avviso di evento imprevisto generato dopo l'esecuzione del rilevatore di anomalie. ](../media/multivariate-architecture/multivariate-architecture.png)](../media/multivariate-architecture/multivariate-architecture.png#lightbox)

Nell'architettura precedente, gli eventi di streaming provenienti dai dati dei sensori verranno archiviati in Azure Data Lake e quindi elaborati da un modulo di trasformazione dei dati per essere convertiti in un formato di serie temporali. Nel frattempo, l'evento di streaming attiverà il rilevamento in tempo reale con il modello sottoposto a training. In generale, sarà presente un modulo per gestire il ciclo di vita del modello multivariato, ad esempio il *servizio Bridge* in questa architettura.

**Training del modello**: prima di usare il rilevatore di anomalie MultiVariante per rilevare le anomalie per un componente o un'apparecchiatura. È necessario eseguire il training di un modello su segnali specifici (serie temporali) generati da questa entità. Il *servizio Bridge* recupererà i dati cronologici e invierà un processo di training al rilevamento delle anomalie e manterrà l'ID del modello nella meta di archiviazione del *modello* .

**Convalida del modello**: il tempo di training di un determinato modello può variare in base al volume dei dati di training. Il *servizio Bridge* potrebbe eseguire query sullo stato del modello e sulle informazioni di diagnostica a intervalli regolari. Potrebbe essere necessario convalidare la qualità del modello prima di metterla online. Se nello scenario sono presenti etichette, è possibile utilizzare tali etichette per verificare la qualità del modello. In caso contrario, è possibile utilizzare le informazioni di diagnostica per valutare la qualità del modello ed è inoltre possibile eseguire il rilevamento sui dati cronologici con il modello sottoposto a training e valutare il risultato per sottotestare la validità del modello.

**Inferenza del modello**: il rilevamento online verrà eseguito con il modello valido e l'ID del risultato può essere archiviato nella *tabella di inferenza*. Il processo di training e il processo di inferenza vengono eseguiti in modo asincrono. In generale, un'attività di rilevamento può essere completata in pochi secondi. I segnali usati per il rilevamento devono essere quelli usati per il training. Ad esempio, se si usano le vibrazioni, l'orientamento e la rotazione per il training, nel rilevamento i tre segnali devono essere inclusi come input.

**Avviso per eventi imprevisti** È possibile eseguire query sui risultati del rilevamento con ID risultati. Ogni risultato contiene la gravità di ogni anomalia e il rango di contributo. È possibile usare la classificazione dei contributi per comprendere il motivo per cui si è verificata questa anomalia e il segnale che lo ha causato. È possibile impostare soglie diverse per la gravità per generare avvisi e notifiche da inviare ai tecnici dei campi per condurre il lavoro di manutenzione.

## <a name="next-steps"></a>Passaggi successivi

- [Guide introduttive](../quickstarts/client-libraries-multivariate.md).
- [Procedure](../concepts/best-practices-multivariate.md)consigliate: questo articolo descrive i modelli consigliati da usare con le API multivariate.