---
title: includere file
description: includere file
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: d24e8957dc63024a46495e8a66bad7dbb3790f38
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389501"
---
| Nomi di dominio                  | Porte in uscita | Descrizione                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | Usata dal runtime di integrazione self-hosted per connettersi all'account di archiviazione di Azure quando si usa la funzionalità di copia temporanea. |
| `*.database.windows.net`      | 1433           | Obbligatori solo quando si esegue la copia da o nel database SQL di Azure o in Azure Synapse Analytics, facoltativi in caso contrario. Usare la funzionalità di copia temporanea per copiare i dati nel database SQL o in Azure Synapse Analytics senza aprire la porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Obbligatori solo quando si esegue la copia da o in Azure Data Lake Store, facoltativi in caso contrario. |
