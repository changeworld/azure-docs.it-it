---
author: craigshoemaker
ms.service: static-web-apps
ms.topic: include
ms.date: 03/25/2021
ms.author: cshoe
ms.openlocfilehash: 806a30214e0307b8fa101c0de51ed2f16622d433
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543559"
---
| Proprietà | Descrizione | Esempio | Obbligatoria |
|---|---|---|---|
| `app_location` | Percorso del codice dell'applicazione. | Immettere `/` se il codice sorgente dell'applicazione si trova nella radice del repository o `/app` se il codice dell'applicazione si trova in una directory denominata `app` . | Sì |
| `api_location` | Percorso del codice di Funzioni di Azure. | Immettere `/api` se il codice dell'app si trova in una cartella denominata `api` . Se nella cartella non viene rilevata alcuna app di funzioni di Azure, la compilazione non riesce, il flusso di lavoro presuppone che non si desideri un'API. | No |
| `output_location` | Percorso della directory di output di compilazione relativa ad `app_location`. | Se il codice sorgente dell'applicazione si trova in `/app` e lo script di compilazione restituisce i file nella `/app/build` cartella, impostare `build` come `output_location` valore. | No |