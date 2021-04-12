---
title: 'Risoluzione dei problemi: lettura di testo UTF-8 da file CSV o PARQUET usando un pool SQL senza server'
description: Lettura di testo UTF-8 da file CSV o PARQUET usando un pool SQL senza server in Azure sinapsi Analytics
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: sql
ms.date: 12/03/2020
ms.openlocfilehash: 70ce3c82790db0296d5359b5db2e6a323306c309
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576418"
---
# <a name="troubleshoot-reading-utf-8-text-from-csv-or-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Risolvere i problemi di lettura del testo UTF-8 da file CSV o parquet usando un pool SQL senza server in Azure sinapsi Analytics

Questo articolo illustra la procedura di risoluzione dei problemi per la lettura di testo UTF-8 da file CSV o parquet usando un pool SQL senza server in Azure sinapsi Analytics.

Quando il testo UTF-8 viene letto da un file CSV o PARQUET usando un pool SQL senza server, alcuni caratteri speciali come ü e ö vengono convertiti erroneamente se la query restituisce colonne VARCHAR con regole di confronto non UTF8. Si tratta di un problema noto in SQL Server e in Azure SQL. Le regole di confronto non UTF8 sono l'impostazione predefinita in sinapsi SQL, in modo che le query dei clienti saranno interessate. I clienti che usano caratteri inglesi standard e alcuni subset di caratteri latini estesi potrebbero non notare gli errori di conversione. La conversione non corretta è illustrata in modo più dettagliato in [utilizzare sempre le regole di confronto UTF-8 per leggere il testo UTF-8 nel pool SQL senza server](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/always-use-utf-8-collations-to-read-utf-8-text-in-serverless-sql/ba-p/1883633)

## <a name="workaround"></a>Soluzione alternativa

La soluzione alternativa a questo problema consiste nell'usare sempre le regole di confronto UTF-8 per la lettura di testo UTF-8 da file CSV o PARQUET.

- In molti casi, è sufficiente impostare le regole di confronto UTF8 nel database (operazione dei metadati).

   ```sql
   alter database MyDB
         COLLATE Latin1_General_100_BIN2_UTF8;
   ```

- È possibile definire in modo esplicito le regole di confronto sulla colonna VARCHAR in OPENROWSET o nella tabella esterna:

   ```sql
   select geo_id, cases = sum(cases)
   from openrowset(
           bulk 'latest/ecdc_cases.parquet', data_source = 'covid', format = 'parquet'
       ) with ( cases int,
                geo_id VARCHAR(6) COLLATE Latin1_General_100_BIN2_UTF8 ) as rows
   group by geo_id
   ```
 
- Se non sono state specificate regole di confronto UTF8 in tabelle esterne che leggono dati UTF8, è necessario ricreare le tabelle esterne interessate e impostare le regole di confronto UTF8 sulle colonne VARCHAR (operazione dei metadati).


## <a name="next-steps"></a>Passaggi successivi

* [Eseguire query su file parquet con sinapsi SQL](../sql/query-parquet-files.md)
* [Eseguire query su file CSV con sinapsi SQL](../sql/query-single-csv-file.md)
* [CETAS con Synapse SQL](../sql/develop-tables-cetas.md)
* [Avvio rapido: Usare i pool SQL serverless](../quickstart-sql-on-demand.md)
