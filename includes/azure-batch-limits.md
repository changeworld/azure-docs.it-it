---
title: includere file
description: includere file
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 12/16/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 0a94b122f1cdd598eeac553c8cc784d2a0a5369f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97614515"
---
| **Risorsa** | **Limite predefinito** | **Limite massimo** |
| --- | --- | --- |
| Account Azure Batch per area per sottoscrizione | 1-3 |50 |
| Core dedicati per account Batch | 90-900 | Contattare il supporto tecnico |
| Core a bassa priorità per account Batch | 10-100 | Contattare il supporto tecnico |
| Processi **[attivi](/rest/api/batchservice/job/get#jobstate)** e pianificazioni di processi per ogni account Batch (i **processi** completati non hanno limiti) | 100-300 | 1\.000<sup>1</sup> |
| Pool per account Batch | 20-100 | 500<sup>1</sup> |

<sup>1</sup> Per richiedere un aumento oltre questo limite, contattare il supporto di Azure.

> [!NOTE]
> I limiti predefiniti variano a seconda del tipo di sottoscrizione usata per creare un account Batch. Le quote di core indicate sono per gli account in modalità servizio Batch. [Visualizzare le quote nell'account Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> Per una migliore gestione della capacità durante la pandemia sanitaria, in alcune aree e per alcuni tipi di sottoscrizione le quote predefinite di core per i nuovi account Batch sono state ridotte rispetto all'intervallo di valori indicato sopra, in alcuni casi fino a zero core. Quando si crea un nuovo account Batch, [controllare la quota di core](../articles/batch/batch-quota-limit.md#view-batch-quotas) e [richiedere un aumento](../articles/batch/batch-quota-limit.md#increase-a-quota), se necessario. In alternativa, provare a riutilizzare gli account Batch che hanno già una quota sufficiente.