---
title: Scalabilità basata sugli eventi in funzioni di Azure
description: Vengono illustrati i comportamenti di ridimensionamento delle app per le funzioni del piano a consumo e del piano Premium.
ms.date: 10/29/2020
ms.topic: conceptual
ms.service: azure-functions
ms.openlocfilehash: 8aca1ab6629f95ef9162e1247434bd3189d5a7d2
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937755"
---
# <a name="event-driven-scaling-in-azure-functions"></a>Scalabilità basata sugli eventi in funzioni di Azure

Nei piani di consumo e Premium, funzioni di Azure consente di ridimensionare le risorse di CPU e memoria aggiungendo altre istanze dell'host di funzioni. Il numero di istanze è determinato dal numero di eventi che attivano una funzione. 

Ogni istanza dell'host di funzioni nel piano a consumo è limitata a 1,5 GB di memoria e una CPU.  Un'istanza dell'host è l'intera app per le funzioni, che significa che tutte le funzioni all'interno di un'app per le funzioni condividono una risorsa all'interno di un'istanza e vengono ridimensionate allo stesso tempo. Le app per le funzioni che condividono la stessa scala del piano a consumo sono indipendenti.  Nel piano Premium, le dimensioni del piano determinano la memoria disponibile e la CPU per tutte le app del piano in tale istanza.  

I file di codice delle funzioni vengono archiviati nelle condivisioni File di Azure nell'account di archiviazione principale della funzione. Quando si elimina l'account di archiviazione principale dell'app per le funzioni, i file di codice delle funzioni vengono eliminati e non possono essere recuperati.

## <a name="runtime-scaling"></a>Ridimensionamento in fase di runtime

Funzioni di Azure usa un componente denominato *controller di scalabilità* per monitorare la frequenza degli eventi e determinare se aumentare il numero di istanze o ridurre le prestazioni. Il controller di scalabilità usa le funzionalità di euristica per ogni tipo di trigger. Quando si usa ad esempio un trigger di archiviazione code di Azure, la scalabilità dipende dalla lunghezza della coda e dal tempo di attesa del messaggio meno recente della coda.

L'unità di scala per funzioni di Azure è l'app per le funzioni. In caso di aumento del numero di istanze dell'app per le funzioni, vengono allocate altre risorse per l'esecuzione di più istanze dell'host di Funzioni di Azure. In caso di riduzione delle richieste di calcolo, il controller di scalabilità rimuove le istanze dell'host di Funzioni. Il numero di istanze viene infine "ridimensionato" a zero quando non è in esecuzione alcuna funzione all'interno di un'app per le funzioni.

![Monitoraggio degli eventi e creazione delle istanze da parte del controller di scalabilità](./media/functions-scale/central-listener.png)

## <a name="cold-start"></a>Avvio a freddo

Dopo che l'app per le funzioni è rimasta inattiva per un certo numero di minuti, la piattaforma può ridimensionare il numero di istanze in cui l'app viene eseguita fino a zero. La richiesta successiva presenta la latenza aggiuntiva di ridimensionamento da zero a uno. Questa latenza è detta avvio a _freddo_. Il numero di dipendenze richieste dall'app per le funzioni può avere un effetto sull'ora di inizio a freddo. L'avvio a freddo è un problema per le operazioni sincrone, ad esempio i trigger HTTP che devono restituire una risposta. Se l'avvio a freddo influisca sulle funzioni, provare a eseguire in un piano Premium o in un piano dedicato con l'impostazione **Always on** abilitata.   

## <a name="understanding-scaling-behaviors"></a>Introduzione al ridimensionamento

Il ridimensionamento può variare in base a numerosi fattori e comportarsi diversamente a seconda del trigger e della lingua selezionati. È necessario tenere presenti alcune complessità dei comportamenti di ridimensionamento:

* **Numero massimo di istanze:** Una singola app per le funzioni è scalabile solo a un massimo di 200 istanze. Una singola istanza può elaborare più di un messaggio o più di una richiesta alla volta. Pertanto, non esiste alcun limite per quanto riguarda il numero di esecuzioni parallele.  È possibile [specificare un valore massimo inferiore](#limit-scale-out) per limitare la scalabilità secondo le esigenze.
* **Frequenza nuova istanza:** Per i trigger HTTP, le nuove istanze vengono allocate al massimo una volta al secondo. Per i trigger non HTTP, le nuove istanze vengono allocate al massimo una volta ogni 30 secondi. La scalabilità è più veloce quando viene eseguita in un [piano Premium](functions-premium-plan.md).
* **Efficienza di ridimensionamento:** Per i trigger del bus di servizio, usare _Gestisci_ diritti sulle risorse per il ridimensionamento più efficiente. Con i diritti di _ascolto_ , il ridimensionamento non è accurato perché la lunghezza della coda non può essere usata per informare le decisioni di scalabilità. Per altre informazioni sull'impostazione dei diritti nei criteri di accesso del bus di servizio, vedere [criteri di autorizzazione dell'accesso condiviso](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies). Per i trigger dell'hub eventi, vedere le [linee guida per la scalabilità](functions-bindings-event-hubs-trigger.md#scaling) nell'articolo di riferimento. 

## <a name="limit-scale-out"></a>Limitare la scalabilità orizzontale

È possibile limitare il numero massimo di istanze usate da un'app per la scalabilità orizzontale.  Questa operazione è più comune nei casi in cui un componente downstream come un database ha una velocità effettiva limitata.  Per impostazione predefinita, le funzioni del piano a consumo aumentano fino a un massimo di 200 istanze, mentre le funzioni del piano Premium aumentano fino a un numero di istanze pari a 100.  È possibile specificare un valore massimo inferiore per un'app specifica modificando il `functionAppScaleLimit` valore.  Il `functionAppScaleLimit` valore di può essere impostato su `0` o `null` per senza restrizioni oppure su un valore valido compreso tra `1` e il valore massimo dell'app.

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <RESOURCE_GROUP> -n <FUNCTION_APP-NAME>/config/web --set properties.functionAppScaleLimit=<SCALE_LIMIT>
```

## <a name="best-practices-and-patterns-for-scalable-apps"></a>Procedure consigliate e modelli per app scalabili

Sono disponibili molti aspetti di un'app per le funzioni che influiscano sulla scalabilità, tra cui la configurazione dell'host, il footprint del runtime e l'efficienza delle risorse.  Per altre informazioni, vedere la [sezione relativa alla scalabilità nell'articolo sulle prestazioni](functions-best-practices.md#scalability-best-practices). È inoltre necessario comprendere il funzionamento delle connessioni quando l'app per le funzioni viene ridimensionata. Per altre informazioni, vedere [How to manage connections in Azure Functions](manage-connections.md) (Come gestire le connessioni in Funzioni di Azure).

Per altre informazioni sul ridimensionamento in Python e Node.js, vedere [Guida per sviluppatori Python di funzioni di Azure-scalabilità e concorrenza](functions-reference-python.md#scaling-and-performance) e [funzioni di Azure Node.js Guida per gli sviluppatori-scalabilità e concorrenza](functions-reference-node.md#scaling-and-concurrency).

## <a name="billing-model"></a>Modello di fatturazione

La fatturazione per i diversi piani è descritta in dettaglio nella [pagina dei prezzi di funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/). L'utilizzo viene aggregato a livello di app per le funzioni e viene calcolato solo il tempo di esecuzione del codice di tale funzione. Per la fatturazione vengono usate le unità seguenti:

* **Utilizzo delle risorse in gigabyte al secondo (GB-s)**. Calcolato come combinazione di dimensioni di memoria e tempo di esecuzione per tutte le funzioni in un'app per le funzioni. 
* **Esecuzioni**. Conteggiate ogni volta che una funzione viene eseguita in risposta a un trigger di evento.

Le query e le informazioni utili su come comprendere la fattura per il consumo sono disponibili [nelle domande frequenti sulla fatturazione](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="next-steps"></a>Passaggi successivi

+ [Opzioni di hosting di funzioni di Azure](functions-scale.md)

