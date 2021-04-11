---
title: Errore durante il superamento delle dimensioni del processo
description: Viene descritto come risolvere gli errori quando la dimensione o il modello del processo è troppo grande.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: b39a0bba15e73bab1a85cbd9e36efebf82d6cf42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889366"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>Risoluzione degli errori per la dimensione del processo superata

Questo articolo descrive come risolvere gli errori di **JobSizeExceededException** e **DeploymentJobSizeExceededException** .

## <a name="symptom"></a>Sintomo

Quando si distribuisce un modello, viene visualizzato un errore che informa che la distribuzione ha superato i limiti.

## <a name="cause"></a>Causa

Questo errore viene ricevuto quando la distribuzione supera uno dei limiti consentiti. Questo errore viene in genere visualizzato quando il modello o il processo che esegue la distribuzione è troppo grande.

Il processo di distribuzione non può superare 1 MB. Il processo include i metadati relativi alla richiesta. Per i modelli di grandi dimensioni, i metadati combinati con il modello possono superare le dimensioni consentite per un processo.

Il modello non può superare i 4 MB. Il limite di 4 MB si applica allo stato finale del modello dopo che è stato espanso per le definizioni di risorse che usano [Copy](copy-resources.md) per creare molte istanze. Lo stato finale include anche i valori risolti per le variabili e i parametri.

Altri limiti per il modello sono:

* 256 parametri
* 256 variabili
* 800 risorse (incluso il conteggio copie)
* 64 valori di output
* 24.576 caratteri in un'espressione di modello

Quando si usano i cicli di copia per distribuire la risorsa, non usare il nome del ciclo come dipendenza:

```json
dependsOn: [ "nicLoop" ]
```

Usare invece l'istanza della risorsa dal ciclo da cui è necessario dipendono. Ad esempio:

```json
dependsOn: [
    "[resourceId('Microsoft.Network/networkInterfaces', concat('nic-', copyIndex()))]"
]
```

## <a name="solution-1---simplify-template"></a>Soluzione 1-semplificare il modello

La prima opzione consiste nel semplificare il modello. Questa opzione funziona quando il modello distribuisce molti tipi di risorse diversi. Provare a dividere il modello in [modelli collegati](linked-templates.md). Dividere i tipi di risorse in gruppi logici e aggiungere un modello collegato per ogni gruppo. Ad esempio, se è necessario distribuire numerose risorse di rete, è possibile spostare tali risorse in un modello collegato.

È possibile impostare altre risorse come dipendenti dal modello collegato e [ottenere i valori dall'output del modello collegato](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---reduce-name-size"></a>Soluzione 2-ridurre le dimensioni del nome

Provare ad abbreviare la lunghezza dei nomi usati per [parametri](template-parameters.md), [variabili](template-variables.md)e [output](template-outputs.md). Quando questi valori vengono ripetuti tramite i cicli di copia, un nome grande viene moltiplicato più volte.