---
title: Ridimensiona e Gestisci competenze personalizzate
titleSuffix: Azure Cognitive Search
description: Scopri gli strumenti e le tecniche per scalare in modo efficiente un'abilità personalizzata per la massima velocità effettiva. Le competenze personalizzate richiamano la logica o i modelli di intelligenza artificiale personalizzati che è possibile aggiungere a una pipeline di indicizzazione arricchita con intelligenza artificiale in Azure ricerca cognitiva.
manager: luisca
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 4fdc222fa20aef6639bf6d5d485f7dcf6b6ca535
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641143"
---
# <a name="efficiently-scale-out-a-custom-skill"></a>Ridimensionare in modo efficiente un'abilità personalizzata

Le competenze personalizzate sono API Web che implementano un'interfaccia specifica. Un'abilità personalizzata può essere implementata in qualsiasi risorsa indirizzabile pubblicamente. Le implementazioni più comuni per le competenze personalizzate sono:
* Funzioni di Azure per competenze logiche personalizzate
* App Web di Azure per competenze di intelligenza artificiale in contenitori semplici
* Servizio Azure Kubernetes per competenze più complesse o più complesse.

## <a name="prerequisites"></a>Prerequisiti

+ Esaminare l' [interfaccia skill personalizzata](cognitive-search-custom-skill-interface.md) per un'introduzione all'interfaccia di input/output che deve essere implementata da un'abilità personalizzata.

+ Configurare l'ambiente. È possibile iniziare con [questa esercitazione end-to-end](/azure/azure-functions/create-first-function-vs-code-python) per configurare la funzione di Azure senza server usando le estensioni Visual Studio Code e Python.

## <a name="skillset-configuration"></a>Configurazione delle competenze

La configurazione di un'abilità personalizzata per ottimizzare la velocità effettiva del processo di indicizzazione richiede una conoscenza delle competenze, delle configurazioni dell'indicizzatore e del modo in cui le competenze si riferiscono a ogni documento. Ad esempio, il numero di volte in cui un'abilità viene richiamata per documento e la durata prevista per ogni chiamata.

### <a name="skill-settings"></a>Impostazioni delle competenze

Nell' [abilità personalizzata](cognitive-search-custom-skill-web-api.md) impostare i parametri seguenti.

1. Set `batchSize` di abilità personalizzate per configurare il numero di record inviati alla competenza in una singola chiamata della competenza.

2. Impostare `degreeOfParallelism` per calibrare il numero di richieste simultanee che l'indicizzatore farà per le proprie competenze.

3. Impostare `timeout` su un valore sufficiente per la capacità di rispondere con una risposta valida.

4. Nella `indexer` definizione impostare sul [`batchSize`](/rest/api/searchservice/create-indexer#indexer-parameters) numero di documenti che devono essere letti dall'origine dati e arricchiti simultaneamente.

### <a name="considerations"></a>Considerazioni

Per ottimizzare le prestazioni degli indicizzatori è necessario impostare queste variabili in modo da determinare se le proprie competenze sono migliori con numerose richieste di piccole dimensioni simultanee o meno richieste di grandi dimensioni. Di seguito sono riportate alcune domande da considerare:

* Qual è la cardinalità della chiamata di competenze? L'abilità viene eseguita una sola volta per ogni documento, ad esempio una competenza di classificazione dei documenti o può essere eseguita più volte per documento, una competenza di classificazione di paragrafo?

* In media, quanti documenti vengono letti dall'origine dati per compilare una richiesta di competenze in base alle dimensioni del batch di competenze? Idealmente, deve essere minore della dimensione del batch dell'indicizzatore. Con le dimensioni del batch maggiori di 1, le competenze possono ricevere record da più documenti di origine. Ad esempio, se il numero di batch dell'indicizzatore è 5 e il numero di batch di competenze è 50 e ogni documento genera solo cinque record, l'indicizzatore dovrà compilare una richiesta di competenze personalizzata tra più batch di indicizzatori.

* Il numero medio di richieste che un batch di indicizzatore può generare dovrebbe offrire un'impostazione ottimale per i gradi di parallelismo. Se l'infrastruttura che ospita la competenza non supporta il livello di concorrenza, è consigliabile comporre i gradi di parallelismo. Come procedura consigliata, testare la configurazione con alcuni documenti per convalidare le scelte sui parametri.

* Eseguendo il test con un campione di documenti più piccolo, valutare il tempo di esecuzione delle proprie competenze sul tempo totale impiegato per elaborare il subset di documenti. L'indicizzatore dedica più tempo alla creazione di un batch o in attesa di una risposta dalle competenze? 

* Prendere in considerazione le implicazioni upstream del parallelismo. Se l'input per un'abilità personalizzata è un output di una competenza precedente, tutte le competenze in questo ambito sono state scalate in modo efficace per ridurre al minimo la latenza?

## <a name="error-handling-in-the-custom-skill"></a>Gestione degli errori nell'abilità personalizzata

Le competenze personalizzate devono restituire un codice di stato di esito positivo HTTP 200 quando l'abilità viene completata correttamente. Se uno o più record in un batch generano errori, provare a restituire il codice di stato multiplo 207. L'elenco di errori o avvisi per il record deve contenere il messaggio appropriato.

Qualsiasi elemento in un batch che genera errori provocherà un errore nel documento corrispondente. Se è necessario che il documento abbia esito positivo, restituire un avviso.

Qualsiasi codice di stato superiore a 299 viene valutato come un errore e tutti gli arricchimenti non vengono completati, ottenendo un documento non riuscito. 

### <a name="common-error-messages"></a>Messaggi di errore comuni

* `Could not execute skill because it did not execute within the time limit '00:00:30'. This is likely transient. Please try again later. For custom skills, consider increasing the 'timeout' parameter on your skill in the skillset.` Impostare il parametro timeout sull'abilità in modo da consentire una durata di esecuzione più lunga.

* `Could not execute skill because Web Api skill response is invalid.` Indica la capacità di non restituire un messaggio nel formato di risposta alle competenze personalizzato. Questo potrebbe essere il risultato di un'eccezione non rilevata nell'abilità.

* `Could not execute skill because the Web Api request failed.` Probabilmente causato da errori di autorizzazione o eccezioni.

* `Could not execute skill.` In genere il risultato della risposta skill viene mappato a una proprietà esistente nella gerarchia del documento.

## <a name="testing-custom-skills"></a>Test delle competenze personalizzate

Iniziare testando la propria abilità personalizzata con un client dell'API REST per convalidare:

* Le competenze implementano l'interfaccia personalizzata per le richieste e le risposte

* La competenza restituisce un JSON valido con il `application/JSON` tipo MIME

* Restituisce un codice di stato HTTP valido

Creare una [sessione di debug](cognitive-search-debug-session.md) per aggiungere le competenze al suo complesso e assicurarsi che produca un arricchimento valido. Mentre una sessione di debug non consente di ottimizzare le prestazioni della competenza, consente di verificare che l'abilità sia configurata con valori validi e restituisca gli oggetti arricchiti previsti.

## <a name="best-practices"></a>Procedure consigliate

* Mentre le competenze possono accettare e restituire payload di dimensioni maggiori, è consigliabile limitare la risposta a 150 MB o meno quando viene restituito JSON.

* È consigliabile impostare le dimensioni del batch nell'indicizzatore e la competenza per assicurarsi che ogni batch di origini dati generi un payload completo per le proprie competenze.

* Per le attività a esecuzione prolungata, impostare il timeout su un valore sufficientemente elevato per assicurarsi che l'indicizzatore non si errori durante l'elaborazione simultanea dei documenti.

* Ottimizzare le dimensioni del batch dell'indicizzatore, le dimensioni del batch di competenze e i gradi di capacità del parallelismo per generare il modello di carico previsto dall'utente, meno richieste di grandi dimensioni o molte richieste di piccole dimensioni.

* Monitora le competenze personalizzate con log dettagliati degli errori, in quanto è possibile avere scenari in cui richieste specifiche hanno esito negativo in modo coerente in seguito alla variabilità dei dati.


## <a name="next-steps"></a>Passaggi successivi
Congratulazioni! L'abilità personalizzata è ora ridimensionata in modo da ottimizzare la velocità effettiva nell'indicizzatore. 

+ [Power Skills: un repository di competenze personalizzate](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Aggiungere un'abilità personalizzata a una pipeline di arricchimento di intelligenza artificiale](cognitive-search-custom-skill-interface.md)
+ [Aggiungere un Azure Machine Learning skill](./cognitive-search-aml-skill.md)
+ [Usare le sessioni di debug per testare le modifiche](./cognitive-search-debug-session.md)