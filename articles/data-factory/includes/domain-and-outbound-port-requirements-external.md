---
title: File di inclusione
description: includere file
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 4ef7bc7ed1ab255bffdfec10c07bc040019d9f1e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121685"
---
| Nomi di dominio                  | Porte in uscita | Descrizione                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | Usata dal runtime di integrazione self-hosted per connettersi all'account di archiviazione di Azure quando si usa la funzionalità di copia temporanea. |
| `*.database.windows.net`      | 1433           | Obbligatori solo quando si esegue la copia da o nel database SQL di Azure o in Azure Synapse Analytics, facoltativi in caso contrario. Usare la funzionalità di copia temporanea per copiare i dati nel database SQL o in Azure Synapse Analytics senza aprire la porta 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Obbligatori solo quando si esegue la copia da o in Azure Data Lake Store, facoltativi in caso contrario. |
