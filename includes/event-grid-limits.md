---
title: File di inclusione
description: File di inclusione
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 02/17/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2f52e8a89ec9dd78a1951836053cb2c698310bbd
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100645371"
---
I limiti seguenti si applicano agli **argomenti** (di sistema, personalizzati e dei partner) di Griglia di eventi di Azure. 

| Risorsa | Limite |
| --- | --- |
| Argomenti personalizzati per sottoscrizione di Azure | 100 |
| Sottoscrizioni di eventi per argomento | 500 |
| Velocità di pubblicazione per un argomento personalizzato o di partner (in ingresso) | 5.000 eventi/sec o 5 MB/sec (a seconda di quale viene soddisfatto per primo) |
| Dimensioni degli eventi | 1 MB  |
| Connessioni a endpoint privato per argomento  | 64 | 
| Regole del firewall IP per argomento | 16 | 

I limiti seguenti si applicano ai **domini** di Griglia di eventi. 

| Risorsa | Limite |
| --- | --- |
| Argomenti per dominio eventi | 100,000 |
| Sottoscrizioni di eventi per argomento all'interno di un dominio | 500 |
| Sottoscrizioni di eventi per ambito di dominio | 50 |
| Frequenza di pubblicazione per un dominio eventi (in ingresso) | 5.000 eventi/sec o 5 MB/sec (a seconda di quale viene soddisfatto per primo) |
| Domini eventi per sottoscrizione di Azure | 100 |
| Connessioni a endpoint privato per dominio | 64 | 
| Regole del firewall IP per dominio | 16 | 


