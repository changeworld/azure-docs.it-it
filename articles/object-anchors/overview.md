---
title: Panoramica degli ancoraggi degli oggetti di Azure
description: Informazioni su come gli ancoraggi degli oggetti di Azure consentono di rilevare gli oggetti nel mondo fisico.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: 90b718e34a12fe71eec4e972bbf8de42d8b52007
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063764"
---
# <a name="azure-object-anchors-overview"></a>Panoramica degli ancoraggi degli oggetti di Azure

Gli ancoraggi degli oggetti di Azure consentono a un'applicazione di rilevare un oggetto nel mondo fisico usando un modello 3D e di stimarne la posizione 6DoF. La posizione 6DoF (6 gradi di libertà) viene definita come rotazione e conversione tra un modello 3D e la relativa controparte fisica, ovvero l'oggetto reale.

Gli ancoraggi degli oggetti di Azure sono costituiti da un servizio per la conversione dei modelli e da un SDK client di runtime per HoloLens. Il servizio accetta un modello a oggetti 3D e restituisce un modello di ancoraggio di oggetti di Azure. Il modello di ancoraggio di oggetti di Azure viene usato insieme a Runtime SDK per consentire a un'applicazione HoloLens di caricare un modello a oggetti, rilevare e tenere traccia delle istanze del modello nel mondo fisico.

:::image type="content" source="./media/aoa-overview.jpg" alt-text="Ancoraggi di oggetti di Azure in azione":::

## <a name="examples"></a>Esempio

Alcuni casi d'uso di esempio abilitati dagli ancoraggi degli oggetti di Azure includono:

- **Training**. Creazione di esperienze di formazione di realtà mista per i dipendenti, senza la necessità di inserire marcatori o di dedicare tempo alla regolazione manuale dell'allineamento ologramma Per potenziare le esperienze di formazione di realtà miste con rilevamento e rilevamento automatici, inserire il modello nel servizio Azure Object Anchors e un passaggio più vicino a un'esperienza senza marcatore.

- **Linee guida** per le attività. L'esplorazione dei dipendenti tramite un set di attività può essere molto semplificata quando si usa la realtà mista. La sovrapposizione di istruzioni digitali e procedure consigliate, oltre all'oggetto fisico, è una parte di macchinari in una fabbrica o una caffettiera nella cucina del team, che può ridurre notevolmente le difficoltà di completamento del set di attività. L'attivazione di queste esperienze richiede in genere una forma di marcatore o di allineamento manuale, ma con gli ancoraggi degli oggetti di Azure è possibile creare un'esperienza che rilevi automaticamente l'oggetto correlato all'attività. Quindi, scorre senza intoppi le linee guida per la realtà mista senza marcatori o allineamento manuale.

- **Ricerca di asset**. Se si dispone già di un modello 3D di un oggetto nello spazio fisico, gli ancoraggi degli oggetti di Azure consentono di individuare e tenere traccia delle istanze dell'oggetto nell'ambiente fisico.

## <a name="next-steps"></a>Passaggi successivi

Le sezioni seguenti forniscono informazioni su come iniziare a usare e creare app con gli ancoraggi degli oggetti di Azure.

> [!div class="nextstepaction"]
> [Inserimento di modelli](quickstarts/get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [Unity in HoloLens](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity HoloLens con MRTK](quickstarts/get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [DirectX HoloLens](quickstarts/get-started-hololens-directx.md)
