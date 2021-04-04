---
title: Problemi di compatibilità con le applicazioni di terze parti e Azure sinapsi Analytics
description: Descrive i problemi noti che le applicazioni di terze parti possono trovare con la sinapsi di Azure
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: ''
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: a1031656eaa5125d07ae078773379270b26625e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98121380"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>Problemi di compatibilità con le applicazioni di terze parti e Azure sinapsi Analytics

Le applicazioni create per SQL Server funzioneranno senza interruzioni dei pool SQL dedicati a sinapsi di Azure. In alcuni casi, tuttavia, le funzionalità e gli elementi del linguaggio comunemente usati in SQL Server potrebbero non essere disponibili in sinapsi di Azure o potrebbero comportarsi in modo diverso.

Questo articolo elenca i problemi noti che si possono incontrare quando si usano applicazioni di terze parti con l'analisi delle sinapsi di Azure. 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Errore Tableau: "un tentativo di completamento di una transazione non è riuscito. Non sono state trovate transazioni corrispondenti "

A partire dalla versione 10.0.11038.0 del pool SQL dedicata a sinapsi di Azure, alcune query tableau che effettuano chiamate stored procedure potrebbero non riuscire con il seguente messaggio di errore: "**[Microsoft] [ODBC driver 17 for SQL Server] [SQL Server] 111214; Tentativo di completamento di una transazione non riuscito. Non sono state trovate transazioni corrispondenti ".**

### <a name="cause"></a>Causa

Si tratta di un problema nel pool SQL dedicato a sinapsi di Azure, causato dall'introduzione di nuove stored procedure di sistema chiamate automaticamente dai driver ODBC e JDBC. Una di queste stored procedure di sistema può causare l'interruzione delle transazioni aperte se l'esecuzione ha esito negativo. Questo problema può verificarsi a seconda della logica dell'applicazione client.

### <a name="solution"></a>Soluzione
I clienti che vedono questo particolare problema quando si usa Tableau connesso ai pool SQL dedicati a sinapsi di Azure devono impostare FMTONLY su Sì nella connessione SQL. Per Tableau desktop e Tableau Server, è necessario usare un file tableau (data source Customization) per assicurarsi che Tableau passi questo parametro al driver.  

> [!NOTE] 
> Microsoft non fornisce supporto per gli strumenti di terze parti. Mentre è stato testato che questa soluzione funziona con tableau desktop 2020.3.2, è consigliabile usare questa soluzione alternativa per la propria capacità.
>

* [Per informazioni su come creare personalizzazioni globali con un file TDC in Tableau desktop, vedere la documentazione su Tableau desktop.](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [Per informazioni su come creare personalizzazioni globali con un file TDC in Tableau Server, vedere Using a. File TDC con Tableau Server.](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

Nell'esempio seguente viene illustrato un file Tableau TDC che passa il parametro FMTONLY = YES alla stringa di connessione SQL:

```json
<connection-customization class='azure_sql_dw' enabled='true' version='18.1'>
    <vendor name='azure_sql_dw' />
    <driver name='azure_sql_dw' />
    <customizations>        
        <customization name='odbc-connect-string-extras' value='UseFMTONLY=yes' />
    </customizations>
</connection-customization>
```
Per ulteriori informazioni sull'utilizzo dei file TDC, contattare il supporto tableau. 

## <a name="see-also"></a>Vedi anche

* [Elementi del linguaggio T-SQL per pool SQL dedicato in Azure sinapsi Analytics.](./sql-data-warehouse-reference-tsql-language-elements.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
* [Istruzioni T-SQL supportate per il pool SQL dedicato in Azure sinapsi Analytics.](./sql-data-warehouse-reference-tsql-statements.md)