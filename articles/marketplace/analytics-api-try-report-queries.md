---
title: Provare l'API per le query di report
description: Usare questa API per eseguire una query di report per i report di analisi del Marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 0db212be06182128bbd8a3bf694a2f893ce82eae
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583796"
---
# <a name="try-report-queries-api"></a>Provare l'API per le query di report

Questa API esegue un'istruzione di query del report. L'API restituisce solo 10 record che possono essere usati dal partner per verificare se i dati sono quelli previsti.

> [!IMPORTANT]
> Questa API ha un timeout di esecuzione della query di 100 secondi. Se si nota che l'API sta impiegando più di 100 secondi, è molto probabile che la query sia sintatticamente corretta, altrimenti è stato ricevuto un codice di errore diverso da 200. L'effettiva generazione del report verrà superata se la sintassi della query è corretta.

**Sintassi della richiesta**

| **Metodo** | **URI richiesta** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery={query text}` |
|||

**Intestazione della richiesta**

| **Intestazione** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| Autorizzazione | string | Obbligatorio. Il token di accesso Azure Active Directory (Azure AD) nel formato `Bearer <token>` |
| Content-Type | string | `Application/JSON` |
|||

**QueryParameter**

| **Nome parametro** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| `exportQuery` | string | Stringa di query del report che deve essere eseguita |
| `queryId` | string | ID di query esistente valido |
|||

**Parametro** Path  

nessuno

**Payload della richiesta**

nessuno

**Glossario**

Nessuno

**Risposta**

Il payload della risposta è strutturato nel modo seguente:

Codice di risposta: 200, 400, 401, 403, 404, 500

Payload risposta: prime 10 righe di esecuzione query
