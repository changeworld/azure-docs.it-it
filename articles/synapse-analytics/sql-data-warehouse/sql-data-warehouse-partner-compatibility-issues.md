---
title: Problemi di compatibilità con applicazioni di terze parti e Azure Synapse Analytics
description: Descrive i problemi noti che le applicazioni di terze parti possono trovare con Azure Synapse
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: sql
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: 3bad9d6464b41ff1b7ad03147d3a48c50c787cb0
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568115"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>Problemi di compatibilità con applicazioni di terze parti e Azure Synapse Analytics

Le applicazioni create per SQL Server funzionano perfettamente con Azure Synapse pool SQL dedicati. In alcuni casi, tuttavia, le funzionalità e gli elementi del linguaggio comunemente usati in SQL Server potrebbero non essere disponibili in Azure Synapse o comportarsi in modo diverso.

Questo articolo elenca i problemi noti che possono verificarsi quando si usano applicazioni di terze parti con Azure Synapse Analytics. 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Errore tableau: "Tentativo di completamento di una transazione non riuscito. Nessuna transazione corrispondente trovata"

A partire da Azure Synapse pool SQL dedicato versione 10.0.11038.0, alcune query Tableau che effettuano chiamate stored procedure potrebbero non riuscire con il messaggio di errore seguente: "**[Microsoft][ODBC Driver 17 for SQL Server][SQL Server]111214; Tentativo di completamento di una transazione non riuscito. Nessuna transazione corrispondente trovata.**"

### <a name="cause"></a>Causa

Si tratta di un problema Azure Synapse pool SQL dedicato causato dall'introduzione di nuove stored procedure di sistema chiamate automaticamente dai driver ODBC e JDBC. Una di queste stored procedure di sistema può causare l'interruzione delle transazioni aperte in caso di esito negativo dell'esecuzione. Questo problema può verificarsi a seconda della logica dell'applicazione client.

### <a name="solution"></a>Soluzione
I clienti che vedono questo particolare problema quando usano Tableau connesso Azure Synapse pool SQL dedicati devono impostare FMTONLY su YES nella connessione SQL. Per Tableau Desktop e Tableau Server, è necessario usare un file di personalizzazione dell'origine dati Tableau per assicurarsi che Tableau passi questo parametro al driver.  

> [!NOTE] 
> Microsoft non offre supporto per strumenti di terze parti. Anche se è stato testato che questa soluzione funziona con Tableau Desktop 2020.3.2, è consigliabile usare questa soluzione alternativa nella propria capacità.
>

* [Per informazioni su come eseguire personalizzazioni globali con un file TDC in Tableau Desktop, vedere la documentazione di Tableau Desktop.](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [Per informazioni su come eseguire personalizzazioni globali con un file TDC in Tableau Server, vedere Using a . File TDC con Tableau Server.](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

L'esempio seguente illustra un file Tableau TDC che passa il parametro FMTONLY=YES alla stringa di connessione SQL:

```json
<connection-customization class='azure_sql_dw' enabled='true' version='18.1'>
    <vendor name='azure_sql_dw' />
    <driver name='azure_sql_dw' />
    <customizations>        
        <customization name='odbc-connect-string-extras' value='UseFMTONLY=yes' />
    </customizations>
</connection-customization>
```
Per altre informazioni sull'uso dei file TDC, contattare il supporto di Tableau. 

## <a name="see-also"></a>Vedi anche

* [Elementi del linguaggio T-SQL per il pool SQL dedicato in Azure Synapse Analytics.](./sql-data-warehouse-reference-tsql-language-elements.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
* [Istruzioni T-SQL supportate per il pool SQL dedicato in Azure Synapse Analytics.](./sql-data-warehouse-reference-tsql-statements.md)