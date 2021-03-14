---
title: Procedure consigliate
description: Procedure consigliate per ottenere risultati migliori
author: ariye
ms.author: crtreasu
ms.date: 03/12/2021
ms.topic: best-practice
ms.service: azure-object-anchors
ms.openlocfilehash: e287d8305b3fd85fc992417e1563b1e58e6f8424
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463001"
---
# <a name="best-practices"></a>Procedure consigliate

Per ottenere risultati ottimali, è consigliabile provare alcuni di questi passaggi.

## <a name="ingestion"></a>Ingestion

- Controllare le dimensioni degli oggetti fisici. Gli ancoraggi degli oggetti di Azure funzionano meglio per gli oggetti la cui dimensione più piccola è compresa nell'intervallo tra 1 milione e 10 milioni di dollari.
- Esaminare il modello 3D in software come [**MeshLab**](https://www.meshlab.net/) per i dettagli seguenti.
  - Verificare che il modello 3D includa una mesh a triangolo e che i triangoli sulla superficie esterna facciano verso l'esterno. Ovvero, i vertici devono essere orientati affinché i normali seguano la regola a destra nell'orientamento verso l'esterno.
  - Verificare che il modello 3D sia specificato con le unità di scala corrette rispetto agli oggetti fisici. Le unità devono essere di uno dei seguenti: ***centimetri, decimetri, piedi, pollici, chilometri, metri, millimetri, iarde***.
  - Verificare la direzione di gravità nominale che corrisponde all'orientamento verticale reale dell'oggetto. Se il livello di gravità verso il basso dell'oggetto è-Y, usare ***(0,-1, 0)** _ o _*_ (0, 0,-1) _ * * per-Z e allo stesso modo per qualsiasi altra direzione.
  - Verificare che il modello 3D sia codificato in uno dei formati supportati: `.glb` ,, `.gltf` `.ply` , `.fbx` , `.obj` .
- Il servizio di conversione del modello potrebbe richiedere molto tempo per elaborare un modello di grandi dimensioni (livello di dettaglio) elevato. Per l'efficacia è possibile pre-elaborare il modello 3D per rimuovere i visi interni.

## <a name="detection"></a>Rilevamento

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Mixed-Reality/Azure-Object-Anchors-Detection-and-Alignment-Best-Practices/player]

- L'SDK di runtime specificato richiede un'area di ricerca fornita dall'utente per cercare e rilevare gli oggetti fisici. L'area di ricerca può essere un rettangolo di delimitazione, una sfera, una vista tronco o qualsiasi combinazione di essi. Per evitare un falso rilevamento, è preferibile impostare un'area di ricerca sufficientemente grande da coprire l'oggetto. Quando si usano le app di esempio fornite, è possibile basarsi su un lato dell'oggetto a circa 2 metri dalla superficie più vicina e avviare l'app.
- Prima di avviare l'app di ancoraggio degli oggetti di Azure in un dispositivo HoloLens 2, rimuovere gli ologrammi nella vicinanza dell'area di lavoro tramite le impostazioni principali dei dispositivi tramite ***le impostazioni >ologrammi >di sistema***

  Questo passaggio garantisce che se un nuovo oggetto, ad esempio un'automobile, è presente nello stesso spazio occupato da un altro oggetto o se l'oggetto è stato spostato dallo spazio di destinazione, eventuali ologrammi obsoleti e irrilevanti non vengono mantenuti e creano una visualizzazione confusa per l'oggetto attualmente in visualizzazione.
- Dopo aver rimosso gli ologrammi e prima di avviare l'app, eseguire l'analisi dell'oggetto, ad esempio un'automobile esaminando l'oggetto mentre si usa il dispositivo da circa 1-2 metri e lentamente intorno all'oggetto una o due volte.

  Questo passaggio garantisce che tutte le stime della superficie residua create nello spazio dagli oggetti e dalle analisi precedenti vengano aggiornate con le superfici dell'oggetto di destinazione corrente che si intende usare. In caso contrario, l'app potrebbe vedere due superfici fantasma che portano a un allineamento non accurato del modello 3D e degli ologrammi associati. L'analisi preliminare dell'oggetto consentirà anche di ridurre notevolmente la latenza di rilevamento degli ancoraggi degli oggetti di Azure, ad indicare, da 30 secondi a 5 secondi.
- Per gli oggetti scuri e altamente riflettenti, potrebbe essere necessario eseguire la scansione dell'oggetto in un intervallo più vicino e spostando il capo verso l'alto e verso il basso e verso sinistra e destra per consentire al dispositivo di visualizzare le superfici da più angoli e più distanze.
- Quando viene visualizzato un rilevamento di oggetti errato, ad esempio l'orientamento invertito o la posizione errata, ad esempio un modello inclinato, è necessario visualizzare il mapping spaziale. Spesso i risultati non corretti sono dovuti a una ricostruzione superficiale incompleta o incompleta. È possibile rimuovere gli ologrammi, analizzare l'oggetto ed eseguire di nuovo il rilevamento degli oggetti nell'app.
- L'SDK di runtime fornito offre alcuni parametri per consentire agli utenti di ottimizzare il rilevamento, come illustrato nelle app di esempio. I parametri predefiniti funzionano correttamente per la maggior parte degli oggetti. Se è necessario modificarli per oggetti specifici, di seguito sono riportati alcuni suggerimenti:
  - Usare una soglia di copertura della superficie inferiore se l'oggetto fisico è di grandi dimensioni, scuro o lucido.
  - Consentire una modifica di scala ridotta, ad esempio 0,1, per oggetti di grandi dimensioni come un'automobile.
  - Consentire una deviazione in gradi tra la direzione verticale e la gravità locali dell'oggetto quando l'oggetto si trova su una pendenza.
