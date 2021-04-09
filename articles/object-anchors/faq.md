---
title: Domande frequenti
description: Domande frequenti sul servizio di ancoraggio degli oggetti di Azure.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 04/01/2020
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: aebc1013dcead6c32dab55512ce915e25f60f94a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047576"
---
# <a name="frequently-asked-questions-about-azure-object-anchors"></a>Domande frequenti sugli ancoraggi degli oggetti di Azure

Gli ancoraggi degli oggetti di Azure consentono a un'applicazione di rilevare un oggetto nel mondo fisico usando un modello 3D e di stimarne la posizione di 6 DoF.

Per altre informazioni, vedere [Panoramica degli ancoraggi degli oggetti di Azure](overview.md).

## <a name="product-faq"></a>Domande frequenti sul prodotto
**D: quali consigli sono disponibili per gli oggetti da usare?**

**R:** Per gli oggetti è consigliabile usare le proprietà seguenti:

* 1-10 metri per ogni dimensione
* Non simmetrico, con variazioni sufficienti in geometria
* Riflessività bassa (superfici opache) con colore chiaro
* Oggetti stazionari
* Nessuna o piccola quantità di articolazione
* Cancella sfondi senza ingombro minimo
* L'oggetto analizzato deve avere 1:1 corrispondenza con il modello con cui è stato eseguito il training

**D: quali sono le dimensioni massime dell'oggetto che è possibile elaborare per l'inserimento dei modelli?**

**R:** Ogni dimensione di un modello CAD deve essere inferiore a 10 metri.

**D: qual è la dimensione massima del modello CAD che può essere elaborata per l'inserimento?**

**R:** Le dimensioni del file di modello devono essere inferiori a 150 MB.

**D: quali sono i formati CAD supportati?**

**R:** Sono attualmente supportati `fbx` i `ply` tipi di file,,, `obj` `glb` e `gltf` .

**D: qual è la direzione e l'unità di gravità richieste dal servizio di inserimento di modelli? Come è possibile capirli?**

**R:** Direzione gravità è il vettore verso il basso che punta alla terra. Per i modelli CAD, la direzione della gravità è in genere l'opposto di una direzione verso l'alto. In molti casi + Z rappresenta ad esempio, nel qual caso-Z o `Vector3(0.0, 0.0, -1.0)` rappresenterebbe la direzione della gravità. Quando si determina la gravità, è consigliabile non solo prendere in considerazione il modello, ma anche l'orientamento in cui il modello verrà visualizzato in fase di esecuzione. Se si sta provando a rilevare una poltrona nel mondo reale su una superficie piana, la gravità potrebbe essere `Vector3(0.0, 0.0, -1.0)` . Tuttavia, se la poltrona si trova su una pendenza di 45 gradi, la gravità potrebbe essere `Vector3(0.0, -Sqrt(2)/2, -Sqrt(2)/2)` .

La direzione di gravità può essere ragionata con uno strumento di rendering 3D, ad esempio [MeshLab](http://www.meshlab.net/).

L'unità rappresenta l'unità di misura del modello. È possibile trovare le unità supportate usando l'enumerazione **Microsoft. Azure. ObjectAnchors.** inseriment. Unit.

**D: quanto tempo è necessario per inserire un modello CAD?**

**R:** Per un `ply` modello, in genere 3-15 minuti. Se si inviano modelli in altri formati, attendere 15-60 minuti a seconda delle dimensioni del file.

**D: quali sono i dispositivi supportati dagli ancoraggi degli oggetti?**

**R:** HoloLens 2 

**D: quale build del sistema operativo deve essere eseguita HoloLens?**

**R:** Build del sistema operativo 18363,720 o versione successiva, rilasciata dopo il 12 marzo 2020.

  Altre informazioni su [Windows 10 March 2020 Update](https://support.microsoft.com/help/4551762).

**D: quanto tempo è necessario per rilevare un oggetto in HoloLens?**

**R:** Dipende dalle dimensioni dell'oggetto e dal processo di analisi. Per ottenere un rilevamento più rapido, provare a seguire le procedure consigliate per un'analisi completa. Per gli oggetti più piccoli entro 2 metri in ogni dimensione, il rilevamento può verificarsi entro pochi secondi. Per gli oggetti più grandi, ad esempio un'automobile, l'utente deve eseguire un ciclo completo intorno all'oggetto per ottenere un rilevamento affidabile, il che significa che il rilevamento potrebbe richiedere decine di secondi.

**D: quali sono le procedure consigliate per l'uso di ancoraggi di oggetti in un'applicazione HoloLens?**

**Un**

 1. Eseguire la calibrazione degli occhi per ottenere un rendering accurato.
 2. Assicurarsi che la stanza abbia una trama visiva avanzata e un'illuminazione adeguata.
 3. Mantieni l'oggetto stazionario, se possibile, lontano dal disordine.
 4. Facoltativamente, deselezionare la cache di [mapping spaziale](/windows/mixed-reality/spatial-mapping) sul dispositivo HoloLens.
 5. Analizzare l'oggetto esplorando il controllo. Verificare che la maggior parte dell'oggetto venga osservato.
 6. Impostare un'area di ricerca sufficientemente grande per coprire l'oggetto.
 7. L'oggetto deve rimanere fisso durante il rilevamento.
 8. Avviare il rilevamento degli oggetti e visualizzare il rendering in base alla posa stimata.
 9. Bloccare l'oggetto rilevato o arrestare il rilevamento quando la posa è stabile e accurata per preservare la durata della batteria.

**D: qual è la precisione di una valutazione?**

**R:** Dipende dalla dimensione dell'oggetto, dal materiale, dall'ambiente e così via. Per gli oggetti di piccole dimensioni, la posa stimata può essere compresa nell'errore di 2 cm. Per oggetti di grandi dimensioni, ad esempio un'automobile, l'errore può essere fino a 2-8 cm.

**D: gli ancoraggi degli oggetti possono gestire gli oggetti mobili?**

**R:** Non sono supportati gli oggetti **dinamici** o in **continua evoluzione** .

**D: gli ancoraggi degli oggetti possono gestire la deformazione o le articolazioni?**

**R:** Parzialmente, a seconda della forma dell'oggetto o delle modifiche della geometria dovute alla deformazione o all'articolazione. Se la geometria dell'oggetto cambia molto, l'utente può creare un altro modello per tale configurazione e utilizzarlo per il rilevamento.

**D: quanti oggetti possono essere rilevati contemporaneamente da altri oggetti?**

**R:** È attualmente supportato il rilevamento di un singolo modello a oggetti alla volta. 

**D: gli ancoraggi degli oggetti possono rilevare più istanze dello stesso modello a oggetti?**

**R:** Sì, è possibile rilevare fino a 3 oggetti dello stesso tipo di modello. L'applicazione può chiamare `ObjectObserver.DetectAsync` più volte con query diverse per rilevare più istanze dello stesso modello.

**D: quali operazioni è necessario eseguire se il runtime degli oggetti Anchor non è in grado di rilevare l'oggetto?**

**Un**

* Assicurarsi che la stanza disponga di trame sufficienti aggiungendo alcuni manifesti.
* Eseguire l'analisi più completa dell'oggetto.
* Modificare i parametri del modello come descritto in precedenza.
* Specificare un rettangolo di delimitazione come area di ricerca che includa tutti o la maggior parte dell'oggetto.
* Cancellare la cache di mapping spaziale e ripetere l'analisi dell'oggetto.
* Acquisire la diagnostica e inviare i dati a Microsoft.

**D: come scegliere i parametri di query oggetto?**

**Un**

* Fornire aree di ricerca strette per coprire idealmente l'intero oggetto per migliorare la velocità e l'accuratezza del rilevamento.
* L'impostazione predefinita `ObjectQuery.MinSurfaceCoverage` del modello a oggetti è in genere corretta. in caso contrario, utilizzare un valore inferiore per ottenere un rilevamento più rapido.
* Utilizzare un valore minimo per `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees` se l'oggetto deve essere in alto a destra.
* Un'app deve sempre usare un `1:1` modello a oggetti per il rilevamento. La scala stimata dovrebbe essere vicina a 1 idealmente entro l'1% di errore. Un'app può impostare `ObjectQuery.MaxScaleChange` su `0` o `0.1` per disabilitare o abilitare la stima della scalabilità e valutare in modo qualitativo la posa dell'istanza.

**D: Ricerca per categorie recuperare i dati di diagnostica dell'oggetto da HoloLens?**

**R:** L'applicazione può specificare il percorso degli archivi di diagnostica. L'app di esempio Object Anchors scrive la diagnostica nella cartella **TempState** .

**D: perché il modello di origine non è allineato all'oggetto fisico quando si usa la funzione di ancoraggio restituita dall'SDK di Unity degli oggetti?**

**R:** Unity può modificare il sistema di coordinate durante l'importazione di un modello a oggetti. Ad esempio, Object Anchors Unity SDK inverte l'asse Z quando si esegue la conversione da un sistema di coordinate di destra a sinistra, ma Unity può applicare una rotazione aggiuntiva sull'asse X o Y. Uno sviluppatore può determinare questa rotazione aggiuntiva visualizzando e confrontando i sistemi di coordinate.

**D: è supportata la funzionalità 2D?**

**R:** Poiché è basato sulla geometria, è supportato solo il 3D.

**D: è possibile distinguere tra lo stesso modello in colori diversi?**

**R:** Poiché gli algoritmi sono basati sulla geometria, i colori diversi dello stesso modello non si comporteranno in modo diverso durante il rilevamento.

**D: è possibile utilizzare ancoraggi di oggetti senza connettività Internet?**

**Un** 
* Per l'inserimento e il training dei modelli, è necessaria la connettività perché si verifica nel cloud.
* Le sessioni di runtime sono completamente sul dispositivo e non richiedono la connettività perché tutti i calcoli si verificano in HoloLens 2.

## <a name="privacy-faq"></a>Domande frequenti sulla privacy
**D: in che modo l'ancoraggio di oggetti di Azure archivia i dati?**

**R:** Vengono archiviati solo i metadati di sistema, che vengono crittografati a riposo con una chiave di crittografia dei dati gestita da Microsoft.