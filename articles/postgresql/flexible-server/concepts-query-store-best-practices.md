---
title: Query Store procedure consigliate in Database di Azure per PostgreSQL - Flex Server
description: Questo articolo descrive le procedure consigliate per Query Store in Database di Azure per PostgreSQL - Flex Server.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 9ee2dc4b3e8ea6a9f892adbc378e8e13b8bd8160
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559113"
---
# <a name="best-practices-for-query-store---flexible-server"></a>Procedure consigliate per Query Store - Server flessibile

**Si applica a:** Database di Azure per PostgreSQL - Flex Server versioni 11, 12

Questo articolo illustra le procedure consigliate per usare Query Store in Database di Azure per PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Impostare la modalità di acquisizione query ottimale
Consentire a Query Store di acquisire i dati importanti. 

|**pg_qs.query_capture_mode** | **Scenario**|
|---|---|
|_Tutto_  |Analizzare l'intero carico di lavoro esaminando tutte le query, la rispettiva frequenza di esecuzione e altre statistiche. Identificare le nuove query nel carico di lavoro. Rilevare se vengono usate query ad hoc per identificare le opportunità di parametrizzazione automatica o utente. La modalità _All_ comporta un costo maggiore per il consumo delle risorse. |
|_Top_  |Concentrare l'attenzione sulle query più frequenti, ovvero quelle emesse dai client.
|_Nessuno_ |Se è impostata su Nessuna, Query Store non acquisisce le nuove query. Sono già stati acquisiti un set di query e un intervallo di tempo da analizzare e si vogliono eliminare eventuali distrazioni causate da altre query. _Nessuno è_ adatto per gli ambienti di test e di contrassegno dei panchini. La modalità _None_ deve essere usata con attenzione perché potrebbe impedire di tenere traccia di nuove query importanti e di ottimizzarle. |


> [!NOTE] 
> **pg_qs.query_capture_mode** prevale su **pgms_wait_sampling.query_capture_mode**. Se pg_qs.query_capture_mode è _none,_ l'impostazione pgms_wait_sampling.query_capture_mode non ha alcun effetto. 


## <a name="keep-the-data-you-need"></a>Conservare i dati necessari
Il parametro **pg_qs.retention_period_in_days** specifica il periodo di conservazione dati, in giorni, per Query Store. I dati delle query e delle statistiche meno recenti vengono eliminati. Per impostazione predefinita, Query Store è configurato per conservare i dati per 7 giorni. Evitare di conservare i dati cronologici che non si intende usare. Aumentare il valore se è necessario conservare i dati più a lungo.


## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come ottenere o impostare i parametri usando il [portale di Azure](howto-configure-server-parameters-using-portal.md) o l'[interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md).
