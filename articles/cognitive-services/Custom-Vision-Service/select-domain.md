---
title: Selezionare un dominio per un progetto di Visione personalizzata-Visione artificiale
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come selezionare un dominio per il progetto nella Servizio visione artificiale personalizzato.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 8aba6f13957d37f843114572f001029baf41ded6
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889349"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Selezionare un dominio per un progetto di Visione personalizzata

Dalla scheda impostazioni del progetto Visione personalizzata è possibile selezionare un dominio per il progetto. Scegliere il dominio più vicino allo scenario. Se si accede a Visione personalizzata tramite una libreria client o un'API REST, è necessario specificare un ID di dominio quando si crea il progetto. È possibile ottenere un elenco di ID di dominio con i [domini Get](https://westus2.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeab)oppure usare la tabella seguente.

## <a name="image-classification"></a>Classificazione immagini

|Dominio|Scopo|
|---|---|
|__Generale__| Ottimizzato per un'ampia gamma di attività di classificazione di immagini. Se nessuno degli altri domini specifici è appropriato o se non si è certi del dominio da scegliere, selezionare uno dei domini generali. ID: `ee85a74c-405e-4adc-bb47-ffa8ca0c9f31`|
|__Generale [a1]__| Ottimizzato per una migliore precisione con tempi di inferenza paragonabili come dominio generale. Consigliato per set di impostazioni di grandi dimensioni o scenari utente più complessi. Questo dominio richiede più tempo di training. ID: `a8e3c40f-fb4a-466f-832a-5e457ae4a344`|
|__Generale [a2]__| Ottimizzato per una migliore precisione con tempi di inferenza più rapidi rispetto ai domini generali [a1] e generali. Consigliato per la maggior parte dei set di impostazioni. Questo dominio richiede meno tempo di training rispetto ai domini generali e generali [a1]. ID: `2e37d7fb-3a54-486a-b4d6-cfc369af0018` |
|__Food__|Ottimizzato per fotografie di piatti come nel menù di un ristorante. Se si vogliono classificare fotografie di singoli frutti o verdure, usare il dominio Food (Cibo). ID: `c151d5b5-dd07-472a-acc8-15d29dea8518`|
|__Punti di riferimento__|Ottimizzato per i luoghi di interesse riconoscibili, sia naturali che artificiali. Il dominio offre i migliori risultati quando il luogo di interesse è chiaramente visibile nella fotografia. Il dominio è efficace anche se il luogo è leggermente nascosto da utenti posti davanti. ID: `ca455789-012d-4b50-9fec-5bb63841c793`|
|__Retail__|Ottimizzato per le immagini che si trovano in un catalogo di vendita o in un sito Web di vendita. Se si desidera la classificazione ad alta precisione tra abiti, pantaloni e camicie, utilizzare questo dominio. ID: `b30a91ae-e3c1-4f73-a81e-c270bff27c39`|
|__Domini compatti__| Ottimizzato per i vincoli di classificazione in tempo reale nei dispositivi perimetrali.|


> [!NOTE]
> I domini generali [a1] e generali [a2] possono essere utilizzati per una vasta gamma di scenari e sono ottimizzati per la precisione. Usare il modello generale [a2] per migliorare la velocità di inferenza e il tempo di training più breve. Per i set di risultati di dimensioni maggiori, è consigliabile utilizzare General [a1] per eseguire il rendering di un'accuratezza migliore rispetto a generale [a2], sebbene sia necessaria una maggiore formazione e tempi di inferenza. Il modello generale richiede più tempo di inferenza rispetto a generale [a1] e generale [a2].

## <a name="object-detection"></a>Rilevamento di oggetti

|Dominio|Scopo|
|---|---|
|__Generale__| Ottimizzato per un'ampia gamma di attività di rilevamento oggetti. Se nessuno degli altri domini è appropriato oppure non si è certi del dominio da scegliere, selezionare il dominio generale. ID: `da2e3a8a-40a5-4171-82f4-58522f70fbc1`|
|__Generale [a1]__| Ottimizzato per una migliore precisione con tempi di inferenza paragonabili come dominio generale. Consigliato per esigenze più accurate per la posizione dell'area, set di impostazioni di grandi dimensioni o scenari utente più complessi. Questo dominio richiede più tempo di training e i risultati non sono deterministici: è prevista una differenza tra +-1% di precisione media (mAP) e gli stessi dati di training. ID: `9c616dff-2e7d-ea11-af59-1866da359ce6`|
|__Logo__|Ottimizzato per il rilevamento di logo dei marchi nelle immagini. ID: `1d8ffafe-ec40-4fb2-8f90-72b3b6cecea4`|
|__Prodotti sugli scaffali__|Ottimizzato per il rilevamento e la classificazione dei prodotti sugli scaffali. ID: `3780a898-81c3-4516-81ae-3a139614e1f3`|
|__Domini compatti__| Ottimizzato per i vincoli del rilevamento di oggetti in tempo reale nei dispositivi perimetrali.|

## <a name="compact-domains"></a>Domini compatti

I modelli generati da domini compatti possono essere esportati per l'esecuzione in locale. Nell'API di anteprima pubblica di Visione personalizzata 3,4 è possibile ottenere un elenco delle piattaforme esportabili per i domini Compact chiamando l'API getdomains.

Le prestazioni del modello variano in base al dominio selezionato. Nella tabella seguente vengono segnalate le dimensioni e il tempo di inferenza del modello su Intel Desktop CPU e NVidia GPU \[ 1 \] . Questi numeri non includono la pre-elaborazione e il tempo di postelaborazione.

|Attività|Dominio|ID|Dimensioni del modello|Tempo di inferenza CPU|Tempo di inferenza GPU|
|---|---|---|---|---|---|
|Classificazione|General (compact) (Generale - compatto)|`0732100f-1a38-4e49-a514-c9b44c697ab5`|6 MB|10 ms|5 ms|
|Classificazione|Generale (Compact) [S1]|`a1db07ca-a19a-4830-bae8-e004a42dc863`|43 MB|50 ms|5 ms|
|Rilevamento di oggetti|General (compact) (Generale - compatto)|`a27d5ca5-bb19-49d8-a70a-fec086c47f5b`|45 MB|35 ms|5 ms|
|Rilevamento di oggetti|Generale (Compact) [S1]|`7ec2ac80-887b-48a6-8df9-8b1357765430`|14 MB|27 ms|7 ms|

>[!NOTE]
>Il dominio __generale (Compact)__ per il rilevamento di oggetti richiede una logica di postelaborazione speciale. Per informazioni dettagliate, vedere uno script di esempio nel pacchetto zip esportato. Se è necessario un modello senza la logica di postelaborazione, usare __General (Compact) [S1]__.

>[!IMPORTANT]
>Non vi è alcuna garanzia che i modelli esportati forniscano esattamente lo stesso risultato dell'API di stima nel cloud. Una lieve differenza nella piattaforma in esecuzione o l'implementazione della pre-elaborazione può causare una differenza maggiore negli output del modello. Per informazioni dettagliate sulla logica di pre-elaborazione, vedere [questo documento](quickstarts/image-classification.md).

\[1 \] Intel Xeon E5-2690 CPU e NVIDIA Tesla M60
