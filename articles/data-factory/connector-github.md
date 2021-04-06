---
title: Connettersi a GitHub
description: Usare GitHub per specificare i riferimenti alle entità di Common Data Model
author: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 5d555d7bc4d3aae9c016cacbe17b68c30859d99a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100372281"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Usare GitHub per leggere i riferimenti alle entità di Common Data Model

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Il connettore GitHub in Azure Data Factory viene usato solo per ricevere lo schema di riferimento dell'entità per il formato [Common Data Model](format-common-data-model.md) nel flusso di dati di mapping.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di GitHub sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type deve essere impostata su **GitHub**. | sì
| userName | Nome utente GitHub | sì |
| password | Password di GitHub | sì |

## <a name="next-steps"></a>Passaggi successivi

Creare un [set](data-flow-source.md) di dati di origine nel flusso di dati del mapping.