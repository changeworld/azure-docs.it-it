---
title: Errore durante il superamento delle dimensioni del processo
description: Viene descritto come risolvere gli errori quando la dimensione o il modello del processo è troppo grande.
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.openlocfilehash: 1fde4918aff6e3bf494876f83c5b4313b3c5f3d2
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610404"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>Risoluzione degli errori per la dimensione del processo superata

Questo articolo descrive come risolvere gli errori di **JobSizeExceededException** e **DeploymentJobSizeExceededException** .

## <a name="symptom"></a>Sintomo

Quando si distribuisce un modello, viene visualizzato un errore che informa che la distribuzione ha superato i limiti.

## <a name="cause"></a>Causa

Questo errore viene ricevuto quando la distribuzione supera uno dei limiti consentiti. Questo errore viene in genere visualizzato quando il modello o il processo che esegue la distribuzione è troppo grande.

Il processo di distribuzione non può superare 1 MB. Il processo include i metadati relativi alla richiesta. Per i modelli di grandi dimensioni, i metadati combinati con il modello possono superare le dimensioni consentite per un processo.


Il modello non può superare i 4 MB. Il limite di 4 MB si applica allo stato finale del modello dopo che è stato espanso per le definizioni di risorse che usano [Copy](copy-resources.md) per creare molte istanze. Lo stato finale include anche i valori risolti per variabili e parametri.

Altri limiti per il modello sono:

* 256 parametri
* 256 variabili
* 800 risorse (incluso il conteggio copie)
* 64 valori di output
* 24.576 caratteri in un'espressione di modello

## <a name="solution-1---simplify-template"></a>Soluzione 1-semplificare il modello

La prima opzione consiste nel semplificare il modello. Questa opzione funziona quando il modello distribuisce molti tipi di risorse diversi. Provare a dividere il modello in [modelli collegati](linked-templates.md). Dividere i tipi di risorse in gruppi logici e aggiungere un modello collegato per ogni gruppo. Ad esempio, se è necessario distribuire numerose risorse di rete, è possibile spostare tali risorse in un modello collegato.

È possibile impostare altre risorse come dipendenti dal modello collegato e [ottenere i valori dall'output del modello collegato](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---reduce-name-size"></a>Soluzione 2-ridurre le dimensioni del nome

Provare ad abbreviare la lunghezza dei nomi usati per [parametri](template-parameters.md), [variabili](template-variables.md)e [output](template-outputs.md). Quando questi valori vengono ripetuti tramite i cicli di copia, un nome grande viene moltiplicato più volte.

## <a name="solution-3---use-serial-copy"></a>Soluzione 3: usare la copia seriale

Provare a modificare il ciclo di copia da [parallelo a elaborazione seriale](copy-resources.md#serial-or-parallel). Usare questa opzione solo quando si ritiene che l'errore provenga dalla distribuzione di un numero elevato di risorse tramite copia. Questa modifica può aumentare significativamente il tempo di distribuzione perché le risorse non vengono distribuite in parallelo.
