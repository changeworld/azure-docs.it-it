---
title: Connettersi a GitHub
description: Usare GitHub per specificare i riferimenti alle entità di Common Data Model
author: dcstwh
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: weetok
ms.openlocfilehash: 7461a0332a36509c7bb6dfdd6db5948b056b35a6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222113"
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