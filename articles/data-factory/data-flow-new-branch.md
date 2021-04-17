---
title: Più rami nel flusso di dati di mapping
description: Replica dei flussi di dati nel flusso di dati di mapping con più rami
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 04/16/2021
ms.openlocfilehash: f9f2bf2e2204e6b74bb8a31ac856dbe276a6e983
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588752"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Creazione di un nuovo ramo nel flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aggiungere un nuovo ramo per eseguire più set di operazioni e trasformazioni sullo stesso flusso di dati. L'aggiunta di un nuovo ramo è utile quando si vuole usare la stessa origine a per più sink o per il self-join dei dati.

È possibile aggiungere un nuovo ramo dall'elenco delle trasformazioni in modo analogo ad altre trasformazioni. **New Branch** sarà disponibile come azione solo quando è presente una trasformazione esistente dopo la trasformazione che si sta tentando di creare un ramo.

![Screenshot che mostra l'opzione Nuovo ramo nel menu Più input/output.](media/data-flow/new-branch2.png "Aggiunta di un nuovo ramo")

Nell'esempio seguente il flusso di dati legge i dati delle corse dei taxi. L'output aggregato per giorno e fornitore è obbligatorio. Anziché creare due flussi di dati separati che leggono dalla stessa origine, è possibile aggiungere un nuovo ramo. In questo modo entrambe le aggregazioni possono essere eseguite come parte dello stesso flusso di dati. 

![Screenshot che mostra il flusso di dati con due rami dall'origine.](media/data-flow/new-branch.png "Aggiunta di un nuovo ramo")

> [!NOTE]
> Quando si fa clic sul segno più (+) per aggiungere trasformazioni al grafico, verrà visualizzata l'opzione Nuovo ramo solo in caso di blocchi di trasformazione successivi. Ciò è dovuto al fatto che New Branch crea un riferimento al flusso esistente e richiede un'ulteriore elaborazione upstream su cui operare. Se l'opzione Nuovo ramo non è visualizzata, aggiungere prima una colonna derivata o un'altra trasformazione, quindi tornare al blocco precedente. Verrà visualizzato Nuovo ramo come opzione.

## <a name="next-steps"></a>Passaggi successivi

Dopo la diramazione, è possibile usare le [trasformazioni del flusso di dati](data-flow-transformation-overview.md)
