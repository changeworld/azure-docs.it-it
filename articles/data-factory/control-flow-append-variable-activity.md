---
title: Attività di accodamento a variabile in Azure Data Factory
description: Informazioni su come impostare l'attività di accodamento a variabile per aggiungere un valore a una variabile di matrice esistente definita in una pipeline di Data Factory
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.date: 10/09/2018
ms.openlocfilehash: 1ca58fc208bb02d137b977e0b18857e8c87a5440
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783847"
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
