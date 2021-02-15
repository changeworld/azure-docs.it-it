---
title: Attività di accodamento a variabile in Azure Data Factory
description: Informazioni su come impostare l'attività di accodamento a variabile per aggiungere un valore a una variabile di matrice esistente definita in una pipeline di Data Factory
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: 5a9ed44e05c371460ae3ceab721f2236f6ec7fd6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383415"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Attività di accodamento a variabile in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Usare l'attività di accodamento a variabile per aggiungere un valore a una variabile di matrice esistente definita in una pipeline di Data Factory.

## <a name="type-properties"></a>Proprietà del tipo

Proprietà | Descrizione | Obbligatoria
-------- | ----------- | --------
name | Nome dell'attività nella pipeline | Sì
description | Testo che descrive l'attività | no
type | Il tipo di attività è AppendVariable | sì
Valore | Valore letterale stringa o valore di oggetto espressione da accodare a una variabile specificata | sì
variableName | Nome della variabile che verrà modificata dall'attività, la variabile deve essere di tipo "Array" | sì

## <a name="next-steps"></a>Passaggi successivi
Informazioni su un'attività del flusso di controllo correlato supportata da Data Factory: 

- [Configurare un'attività variabile](control-flow-set-variable-activity.md)
