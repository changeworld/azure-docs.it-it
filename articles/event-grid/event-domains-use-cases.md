---
title: Casi d'uso per i domini di eventi in griglia di eventi di Azure
description: Questo articolo descrive alcuni casi d'uso per l'uso di domini eventi in griglia di eventi di Azure.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 00318fc78053ed55e3599c329746d89d2eee4f99
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204346"
---
# <a name="use-cases-for-event-domains-in-azure-event-grid"></a>Casi d'uso per i domini di eventi in griglia di eventi di Azure
Questo articolo descrive alcuni casi d'uso per l'uso di domini eventi in griglia di eventi di Azure. 

## <a name="use-case-1"></a>Caso d'uso 1 
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="use-case-2"></a>Caso d'uso 2
Quando si usano gli argomenti di sistema, è previsto un limite di 500 sottoscrizioni di eventi. Se sono necessarie più di 500 sottoscrizioni di eventi per un argomento di sistema, è possibile usare i domini. 

Si supponga di aver creato un argomento di sistema per un archivio BLOB di Azure ed è necessario creare più di 500 sottoscrizioni all'argomento, ma non è possibile a causa della limitazione (500 sottoscrizioni per argomento). In questo caso, è possibile usare la soluzione seguente che usa un dominio di evento. 

1. Creare un dominio, che può supportare fino a 100.000 argomenti e ogni argomento di dominio può avere sottoscrizioni di evento 500. Questo modello darebbe a 500 * 100.000 sottoscrizioni di eventi. 
1. Creare una sottoscrizione di funzioni di Azure per l'argomento sistema di archiviazione di Azure. Quando la funzione riceve eventi da archiviazione di Azure, può arricchire e pubblicare eventi in un argomento di dominio appropriato. Ad esempio, la funzione può analizzare il nome del file BLOB per determinare l'argomento del dominio di destinazione e pubblicare l'evento nell'argomento dominio. 

:::image type="content" source="./media/event-domains-use-cases/use-case-2.jpg" alt-text="Domini griglia AzureEvent-caso d'uso 2":::


## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulla configurazione di domini eventi, sulla creazione di argomenti, sulla creazione di sottoscrizioni di eventi e sulla pubblicazione di eventi, vedere [gestire i domini di eventi](./how-to-event-domains.md).
