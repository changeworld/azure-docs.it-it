---
title: Usare SQL dinamico in sinapsi SQL
description: Suggerimenti per l'uso di SQL dinamico in sinapsi SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 86d5028a09a805142f7632f93530f8a54965d82f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675024"
---
# <a name="dynamic-sql-in-synapse-sql"></a>SQL dinamico in sinapsi SQL

In questo articolo sono disponibili suggerimenti per l'uso di SQL dinamico e lo sviluppo di soluzioni con sinapsi SQL.

## <a name="dynamic-sql-example"></a>Esempio di SQL dinamico

Quando si sviluppa il codice dell'applicazione, potrebbe essere necessario usare SQL dinamico per offrire soluzioni flessibili, generiche e modulari.

> [!NOTE]
> Il pool SQL dedicato non supporta i tipi di dati BLOB in questo momento. Il mancato supporto dei tipi di dati BLOB potrebbe limitare le dimensioni delle stringhe poiché i tipi di dati BLOB includono i tipi varchar (max) e nvarchar (max). Se sono stati usati questi tipi nel codice dell'applicazione durante la creazione di stringhe di grandi dimensioni, è necessario separare il codice in blocchi e usare invece l'istruzione EXEC.

Un semplice esempio:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se la stringa è breve, è possibile usare [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?view=azure-sqldw-latest&preserve-view=true) come di consueto.

> [!NOTE]
> Le istruzioni eseguite come SQL dinamico saranno comunque soggette a tutte le regole di convalida T-SQL.

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](develop-overview.md).
