---
title: Query Store scenari - Database di Azure per PostgreSQL - Flex Server
description: Questo articolo descrive alcuni scenari per la Query Store database di Azure per PostgreSQL - Flex Server.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: ac0374cf91229e7f1b3f84d967d9825e2a3090f2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559152"
---
# <a name="usage-scenarios-for-query-store---flexible-server"></a>Scenari di utilizzo per Query Store - Server flessibile

**Si applica a:** Database di Azure per PostgreSQL - Server singolo versioni 11, 12

È possibile usare Query Store in svariati scenari, in cui è fondamentale garantire prestazioni dei carichi di lavoro prevedibili e tenerne traccia. Considera gli esempi che seguono: 
- Identificazione e ottimizzazione delle query con costo più elevato 
- Test A/B 
- Prestazioni stabili durante gli aggiornamenti 
- Identificazione e miglioramento dei carichi di lavoro ad hoc 

## <a name="identify-and-tune-expensive-queries"></a>Identificare e ottimizzare le query con costo elevato 

### <a name="identify-longest-running-queries"></a>Identificare le query in esecuzione da più tempo 
Usare Query Store viste nel database azure_sys del server per identificare rapidamente le query con esecuzione più lunga. Queste query tendono in genere a utilizzare la maggior parte delle risorse. L'ottimizzazione delle query con esecuzione più lunga può migliorare le prestazioni liberando le risorse usate da altre query in esecuzione nel sistema. 

### <a name="target-queries-with-performance-deltas"></a>Identificare le query con valori differenziali nelle prestazioni 
Query Store seziona i dati delle prestazioni in intervalli di tempo che consentono di tenere traccia delle prestazioni di una query nel corso del tempo. In questo modo è possibile identificare esattamente quali query contribuiscono ad aumentare il tempo totale impiegato. Di conseguenza è possibile risolvere in modo mirato i problemi del carico di lavoro.

### <a name="tuning-expensive-queries"></a>Ottimizzazione delle query con costo elevato 
Quando si identifica una query con prestazioni non ottimali, l'azione eseguita dipende dalla natura del problema: 
- Assicurarsi che le statistiche siano aggiornate per le tabelle sottostanti usate dalla query.
- Considerare la possibilità di riscrivere le query con costo elevato. Ad esempio, sfruttare la parametrizzazione delle query e ridurre l'uso di SQL dinamico. Implementare la logica ottimale durante la lettura dei dati, ad esempio applicando i filtri dei dati sul lato database, non sul lato applicazione. 


## <a name="ab-testing"></a>Test A/B 
Usare Query Store per confrontare le prestazioni del carico di lavoro prima e dopo una modifica dell'applicazione che si prevede di introdurre o prima e dopo la migrazione. Scenari di esempio per l'Query Store per valutare l'impatto delle modifiche alle prestazioni del carico di lavoro: 
- Migrazione tra le versioni di PostgreSQL. 
- Implementazione di una nuova versione di un'applicazione. 
- Aggiunta di altre risorse al server. 
- Creazione di indici mancanti nelle tabelle a cui fanno riferimento query con costo elevato. 
- Migrazione da server singolo a Flex Server. 
 
In tutti questi scenari applicare il flusso di lavoro seguente: 
1. Eseguire il carico di lavoro con Query Store prima della modifica pianificata per generare una baseline delle prestazioni. 
2. Applicare le modifiche dell'applicazione in un momento controllato. 
3. Continuare a eseguire il carico di lavoro per il tempo necessario per generare un'immagine delle prestazioni del sistema dopo la modifica. 
4. Confrontare i risultati ottenuti prima e dopo la modifica. 
5. Decidere se mantenere la modifica o eseguire il rollback. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Identificare e migliorare i carichi di lavoro ad hoc 
Alcuni carichi di lavoro non includono query dominanti che è possibile ottimizzare per migliorare le prestazioni complessive dell'applicazione. Questi carichi di lavoro sono in genere caratterizzati da un numero relativamente elevato di query univoche, ognuna delle quali consuma una parte delle risorse di sistema. Ogni query univoca viene eseguita raramente, quindi il consumo individuale del runtime non è critico. D'altra parte, dato che l'applicazione genera continuamente nuove query, una parte significativa delle risorse di sistema viene impiegata per la compilazione delle query, ma ciò non è l'ideale. In genere, questa situazione si verifica se l'applicazione genera query (invece di usare le stored procedure o le query con parametri) o se si basa su framework di mapping relazionale a oggetti che generano query per impostazione predefinita. 
 
Se si ha il controllo del codice dell'applicazione, considerare la possibilità di riscrivere il livello di accesso ai dati per usare le stored procedure o le query con parametri. Tuttavia, questa situazione può essere migliorata anche senza modifiche dell'applicazione forzando la parametrizzazione delle query per l'intero database (tutte le query) o per i singoli modelli di query con lo stesso hash di query. 

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [procedure consigliate per usare Query Store](concepts-query-store-best-practices.md)
