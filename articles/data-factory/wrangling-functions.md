---
title: Funzioni di data wrangling in Azure Data Factory
description: Panoramica delle funzioni di data wrangling disponibili in Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: f7a4041d87e00fa01ae5ae4dff0cade3b9755d31
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600939"
---
# <a name="transformation-functions-in-power-query-for-data-wrangling"></a>Funzioni di trasformazione in Power Query per data wrangling

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Data Wrangling in Azure Data Factory consente di eseguire attività di preparazione e wrangling dei dati agile senza codice su scala cloud traducendo gli script Power Query in Flusso di dati ```M``` script. AdF si integra con [Power Query Online](/powerquery-m/power-query-m-reference) e rende disponibili Power Query per l'data wrangling tramite l'esecuzione di Spark usando l'infrastruttura Spark del flusso ```M``` di dati. 

> [!NOTE]
> Power Query in AdF è attualmente disponibile in anteprima pubblica

Attualmente non tutte le Power Query M sono supportate per le data wrangling nonostante siano disponibili durante la creazione. Durante la compilazione dei mash-up, verrà visualizzato il messaggio di errore seguente se una funzione non è supportata:

`UserQuery : Expression.Error: The transformation logic is not supported as it requires dynamic access to rows of data, which cannot be scaled out.`

Di seguito è riportato un elenco delle Power Query M supportate.

## <a name="column-management"></a>Gestione colonne

* Selezione: [Table.SelectColumns](/powerquery-m/table-selectcolumns)
* Rimozione: [Table.RemoveColumns](/powerquery-m/table-removecolumns)
* Ridenominazione: [Table.RenameColumns,](/powerquery-m/table-renamecolumns) [Table.PrefixColumns](/powerquery-m/table-prefixcolumns), [Table.TransformColumnNames](/powerquery-m/table-transformcolumnnames)
* Riordinamento: [Table.ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Filtro di riga

Usare la funzione M [Table.SelectRows](/powerquery-m/table-selectrows) per filtrare in base alle condizioni seguenti:

* Uguaglianza e disuguaglianza
* Confronti numerici, di testo e di data (ma non di DateTime)
* Informazioni numeriche, ad [esempio Number.IsEven](/powerquery-m/number-iseven) / [Odd](/powerquery-m/number-iseven)
* Contenimento del testo [tramite Text.Contains](/powerquery-m/text-contains), [Text.StartsWith](/powerquery-m/text-startswith)o [Text.EndsWith](/powerquery-m/text-endswith)
* Intervalli di date che [](/powerquery-m/date-functions)includono tutte le funzioni date 'IsIn' ) 
* Combinazioni di queste condizioni che usano e o o meno

## <a name="adding-and-transforming-columns"></a>Aggiunta e trasformazione di colonne

Le funzioni M seguenti aggiungono o trasformano colonne: [Table.AddColumn](/powerquery-m/table-addcolumn), [Table.TransformColumns](/powerquery-m/table-transformcolumns), [Table.ReplaceValue](/powerquery-m/table-replacevalue), [Table.DuplicateColumn](/powerquery-m/table-duplicatecolumn). Di seguito sono riportate le funzioni di trasformazione supportate.

* Aritmetica numerica
* Concatenazione di testo
* Aritmetica di data e ora (operatori aritmetici, [Date.AddDays,](/powerquery-m/date-adddays) [Date.AddMonths,](/powerquery-m/date-addmonths) [Date.AddQuarters,](/powerquery-m/date-addquarters) [Date.AddWeeks,](/powerquery-m/date-addweeks) [Date.AddYears)](/powerquery-m/date-addyears)
* Le durate possono essere usate per l'aritmetica di data e ora, ma devono essere trasformate in un altro tipo prima di essere scritte in un sink (operatori aritmetici, [#duration,](/powerquery-m/sharpduration) [Duration.Days,](/powerquery-m/duration-days) [Duration.Hours,](/powerquery-m/duration-hours) [Duration.Minutes,](/powerquery-m/duration-minutes) [Duration.Seconds,](/powerquery-m/duration-seconds) [Duration.TotalDays,](/powerquery-m/duration-totaldays) [Duration.TotalHours,](/powerquery-m/duration-totalhours) [Duration.TotalMinutes,](/powerquery-m/duration-totalminutes) [Duration.TotalSeconds)](/powerquery-m/duration-totalseconds)    
* La maggior parte delle funzioni numeriche standard, scientifica [](/powerquery-m/number-functions#rounding)e trigonometrica (tutte le funzioni in [Operazioni,](/powerquery-m/number-functions#operations)Arrotondamento e [Trigonometria](/powerquery-m/number-functions#trigonometry) ad eccezione di  Number.Factorial, Number.Permutations e Number.Combinations)
* Replacement ([Replacer.ReplaceText](/powerquery-m/replacer-replacetext), [Replacer.ReplaceValue](/powerquery-m/replacer-replacevalue), [Text.Replace](/powerquery-m/text-replace), [Text.Remove](/powerquery-m/text-remove))
* Estrazione di testo posizionale ([Text.PositionOf,](/powerquery-m/text-positionof) [Text.Length,](/powerquery-m/text-length) [Text.Start,](/powerquery-m/text-start) [Text.End,](/powerquery-m/text-end) [Text.Middle,](/powerquery-m/text-middle) [Text.ReplaceRange,](/powerquery-m/text-replacerange) [Text.RemoveRange](/powerquery-m/text-removerange))
* Formattazione di base del testo ([Text.Lower](/powerquery-m/text-lower), [Text.Upper](/powerquery-m/text-upper), [Text.Trim](/powerquery-m/text-trim) / [Start](/powerquery-m/text-trimstart) / [End](/powerquery-m/text-trimend), [Text.PadStart](/powerquery-m/text-padstart) / [End](/powerquery-m/text-padend), [Text.Reverse](/powerquery-m/text-reverse))
* Funzioni di data/ora ([Date.Day](/powerquery-m/date-day), [Date.Month](/powerquery-m/date-month), [Date.Year](/powerquery-m/date-year) [Time.Hour](/powerquery-m/time-hour), [Time.Minute](/powerquery-m/time-minute), [Time.Second](/powerquery-m/time-second), [Date.DayOfWeek](/powerquery-m/date-dayofweek), [Date.DayOfYear](/powerquery-m/date-dayofyear), [Date.DaysInMonth](/powerquery-m/date-daysinmonth))
* Espressioni If (ma i rami devono avere tipi corrispondenti)
* Filtri di riga come colonna logica
* Costanti number, text, logical, date e datetime

<a name="mergingjoining-tables"></a>Unione/unione di tabelle
----------------------
* Power Query genera un join annidato (Table.NestedJoin; gli utenti possono anche scrivere manualmente [Table.AddJoinColumn).](/powerquery-m/table-addjoincolumn)
    Gli utenti devono quindi espandere la colonna di join annidata in un join non annidato (Table.ExpandTableColumn, non supportato in nessun altro contesto).
* La funzione M   [Table.Join](/powerquery-m/table-join) può essere scritta direttamente per evitare la necessità di un passaggio di espansione aggiuntivo, ma l'utente deve assicurarsi che non siano presenti nomi di colonna duplicati tra le tabelle unite in join
* Tipi di join supportati:   [Inner,](/powerquery-m/joinkind-inner)   [LeftOuter,](/powerquery-m/joinkind-leftouter)   [RightOuter,](/powerquery-m/joinkind-rightouter)   [FullOuter](/powerquery-m/joinkind-fullouter)
* Sia   [Value.Equals che](/powerquery-m/value-equals)   [Value.NullableEquals](/powerquery-m/value-nullableequals) sono supportati come operatori di confronto di uguaglianza delle chiavi

## <a name="group-by"></a>Group by

Usare [Table.Group per](/powerquery-m/table-group) aggregare i valori.
* Deve essere usato con una funzione di aggregazione
* Funzioni di aggregazione supportate:   [List.Sum](/powerquery-m/list-sum),   [List.Count](/powerquery-m/list-count),   [List.Average](/powerquery-m/list-average),   [List.Min](/powerquery-m/list-min),   [List.Max](/powerquery-m/list-max),   [List.StandardDeviation](/powerquery-m/list-standarddeviation),   [List.First](/powerquery-m/list-first),   [List.Last](/powerquery-m/list-last)

## <a name="sorting"></a>Ordinamento

Usare [Table.Sort per](/powerquery-m/table-sort) ordinare i valori.

## <a name="reducing-rows"></a>Riduzione delle righe

Keep and Remove Top, Keep Range (funzioni M corrispondenti, solo conteggi di supporto, non condizioni: [Table.FirstN](/powerquery-m/table-firstn), [Table.Skip](/powerquery-m/table-skip), [Table.RemoveFirstN](/powerquery-m/table-removefirstn), [Table.Range](/powerquery-m/table-range), [Table.MinN](/powerquery-m/table-minn), [Table.MaxN](/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Funzioni note non supportate

| Funzione | Stato |
| -- | -- |
| Table.PromoteHeaders | Non supportata. Lo stesso risultato può essere ottenuto impostando "First row as header" (Prima riga come intestazione) nel set di dati. |
| Table.CombineColumns | Si tratta di uno scenario comune che non è supportato direttamente, ma che può essere ottenuto aggiungendo una nuova colonna che concatena due colonne specifiche.  Ad esempio, Table.AddColumn(RemoveEmailColumn, "Name", ogni [FirstName] & " " & [LastName]) |
| Table.TransformColumnTypes | Questa funzionalità è supportata nella maggior parte dei casi. Gli scenari seguenti non sono supportati: trasformazione della stringa in tipo valuta, trasformazione della stringa in tipo time, trasformazione della stringa in tipo Percentuale. |
| Table.NestedJoin | La semplice operazione di join restituirà un errore di convalida. Per il funzionamento, le colonne devono essere espanse. |
| Table.Distinct | La rimozione di righe duplicate non è supportata. |
| Table.RemoveLastN | La rimozione delle righe in basso non è supportata. |
| Table.RowCount | Non supportato, ma può essere ottenuto aggiungendo una colonna personalizzata contenente il valore 1, quindi aggregando tale colonna con List.Sum. Table.Group è supportato. | 
| Gestione degli errori a livello di riga | La gestione degli errori a livello di riga non è attualmente supportata. Ad esempio, per filtrare i valori non numerici da una colonna, un approccio consiste nel trasformare la colonna di testo in un numero. Ogni cella che non riesce a trasformare sarà in uno stato di errore e deve essere filtrata. Questo scenario non è possibile in M con scalabilità orizzontale. |
| Table.Transpose | Non supportato |
| Table.Pivot | Non supportato |
| Table.SplitColumn | Parzialmente supportato |

## <a name="m-script-workarounds"></a>Soluzioni alternative per lo script M

### <a name="for-splitcolumn-there-is-an-alternate-for-split-by-length-and-by-position"></a>Perché ```SplitColumn``` è disponibile un'alternativa per la divisione in base alla lunghezza e alla posizione

* Table.AddColumn(Source, "First characters", each Text.Start([Email], 7), type text)
* Table.AddColumn(#"Inserted first characters", "Text range", each Text.Middle([Email], 4, 9), type text)

Questa opzione è accessibile dall'opzione Estrai nella barra multifunzione

![Power Query Aggiungi colonna](media/wrangling-data-flow/pq-split.png)

### <a name="for-tablecombinecolumns"></a>Per ```Table.CombineColumns```

* Table.AddColumn(RemoveEmailColumn, "Name", ogni [FirstName] & " " & [LastName])


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare un data wrangling Power Query in ADF.](wrangling-tutorial.md)
