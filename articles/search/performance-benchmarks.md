---
title: Benchmark delle prestazioni
titleSuffix: Azure Cognitive Search
description: Informazioni sulle prestazioni di Azure ricerca cognitiva tramite diversi benchmark delle prestazioni
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 9f4473d6c8a584bf60e5c8fe2d69d6a56a55e71d
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108316"
---
# <a name="azure-cognitive-search-performance-benchmarks"></a>Benchmark delle prestazioni di Azure ricerca cognitiva

Le prestazioni di Azure ricerca cognitiva dipendono da [diversi fattori](search-performance-tips.md) , tra cui le dimensioni del servizio di ricerca e i tipi di query che si sta inviando. Per stimare le dimensioni del servizio di ricerca necessarie per il carico di lavoro, sono stati eseguiti diversi benchmark per documentare le prestazioni per diversi servizi e configurazioni di ricerca. Questi benchmark non garantiscono in alcun modo un certo livello di prestazioni del servizio, ma possono dare un'idea delle prestazioni che ci si può aspettare.

Per coprire un intervallo di casi d'uso diversi, sono stati eseguiti i benchmark per due scenari principali:

* **Ricerca e-commerce** : questo benchmark emula uno scenario di e-commerce reale ed è basato sulla società di e-commerce nordica [CDON](https://cdon.com).
* **Ricerca di documenti** : questo scenario è costituito dalla ricerca di parole chiave su documenti full-text di [Semantic Scholar](http://s2-public-api-prod.us-west-2.elasticbeanstalk.com/corpus/download/). Questo emula una tipica soluzione di ricerca di documenti.

Sebbene questi scenari riflettano diversi casi di utilizzo, ogni scenario è diverso, quindi è sempre consigliabile testare le prestazioni del singolo carico di lavoro. È stata pubblicata una [soluzione di test delle prestazioni con JMeter](https://github.com/Azure-Samples/azure-search-performance-testing) , in modo da poter eseguire test simili con il proprio servizio.

## <a name="testing-methodology"></a>Metodologia di test

Per eseguire il benchmark delle prestazioni di Azure ricerca cognitiva, sono stati eseguiti test per due scenari diversi a livelli diversi e combinazioni di replica/partizione.

Per creare questi benchmark, è stata usata la metodologia seguente:

1. Il test inizia alle `X` query al secondo (query al secondo) per 180 secondi. Si tratta in genere di 5 o 10 query al secondo.
2. QUERY al secondo quindi aumentata da `X` ed è stato eseguito per altri 180 secondi
3. Ogni 180 secondi, il test è aumentato di `X` query al secondo fino a quando la latenza media è aumentata oltre 1000 ms o inferiore al 99% delle query ha avuto esito positivo.

Il grafico seguente fornisce un esempio visivo dell'aspetto del carico di query del test:

![Test di esempio](./media/performance-benchmarks/example-test.png)

Ogni scenario ha usato almeno 10.000 query univoche per evitare che i test siano eccessivamente distorta dalla memorizzazione nella cache.

> [!IMPORTANT]
> Questi test includono solo i carichi di lavoro di query. Se si prevede di avere una volumne elevata di operazioni di indicizzazione, assicurarsi di fattorizzarla nei test di stima e delle prestazioni. In questa [esercitazione](tutorial-optimize-indexing-push-api.md)è possibile trovare il codice di esempio per simulare l'indicizzazione.

### <a name="definitions"></a>Definizioni

- **Query al secondo massimo** : i numeri query al secondo massimi riportati di seguito si basano sul massimo query al secondo raggiunto in un test in cui il 99% delle query è stato completato senza limitazione e la latenza media è rimasta sotto 1000 ms.

- **Percentuale del numero massimo di query al secondo** : una percentuale del valore massimo di query al secondo per un determinato test. Se, ad esempio, un determinato test ha raggiunto un massimo di 100 query al secondo, il 20% del numero massimo di query al secondo sarà 20 query al secondo.

- **Latenza** : latenza del server per una query; questi numeri non includono [Round Trip Delay (RTT)](https://en.wikipedia.org/wiki/Round-trip_delay). I valori riportati di seguito sono in millisecondi (MS).

### <a name="disclaimer"></a>Dichiarazione di non responsabilità

Il codice usato per eseguire questi benchmark è disponibile [qui](https://github.com/Azure-Samples/azure-search-performance-testing/tree/main/other_tools). Vale la pena notare che sono stati osservati livelli query al secondo leggermente inferiori con la [soluzione di test delle prestazioni di JMeter](https://github.com/Azure-Samples/azure-search-performance-testing) rispetto ai benchmark seguenti. Le differenze possono essere attribuite a differenze nello stile dei test. In questo modo si parla dell'importanza di eseguire i test delle prestazioni in modo analogo al carico di lavoro di produzione.

Questi benchmark non garantiscono in alcun modo un certo livello di prestazioni del servizio, ma possono dare un'idea delle prestazioni che è possibile aspettarsi in base allo scenario.

In caso di domande o dubbi, contattare Microsoft all'indirizzo azuresearch_contact@microsoft.com .

## <a name="benchmark-1-e-commerce-search"></a>Benchmark 1: ricerca E-Commerce

:::row:::
   :::column span="1":::
      ![Logo di CDON](./media/performance-benchmarks/cdon-logo-160px2.png)
   :::column-end:::
   :::column span="3":::
      Questo benchmark è stato creato in collaborazione con l'azienda di e-commerce, [CDON](https://cdon.com), il più grande marketplace online della regione nordica con le operazioni in Svezia, Finlandia, Norvegia e Danimarca. Grazie ai suoi commercianti 1.500, CDON offre un'ampia gamma di prodotti che include oltre 8 milioni prodotti. In 2020, CDON aveva oltre 120 milioni visitatori e 2 milioni clienti attivi. Altre informazioni sull'uso di CDON di Azure ricerca cognitiva in [questo articolo](https://pulse.microsoft.com/transform/na/fa1-how-cdon-has-been-using-technology-to-become-the-leading-marketplace-in-the-nordics/).
   :::column-end:::
:::row-end:::

Per eseguire questi test, abbiamo usato uno snapshot dell'indice di ricerca di produzione di CDON e migliaia di query univoche dal relativo [sito Web](https://cdon.com).

### <a name="scenario-details"></a>Dettagli dello scenario

- **Conteggio documenti**: 6 milioni 
- **Dimensioni indice**: 20 GB
- **Schema dell'indice**: un indice wide con 250 campi totali, 25 campi disponibili per la ricerca e 200 campi facet/filtrabili
- **Tipi di query**: query di ricerca full-text inclusi facet, filtri, ordinamento e profili di Punteggio

### <a name="s1-performance"></a>Prestazioni S1

#### <a name="queries-per-second"></a>Query al secondo

Il grafico seguente mostra il carico di query più elevato che un servizio può gestire per un lungo periodo di tempo in termini di query al secondo (query al secondo).

![QUERY al secondo eCommerce più grande e gestibile](./media/performance-benchmarks/s1-ecom-qps.png)

#### <a name="query-latency"></a>Latenza query

La latenza delle query varia in base al carico del servizio e i servizi con maggiore stress avranno una latenza di query media superiore. Nella tabella seguente sono illustrati i 25, 50, 75, 90, 95 e 99 ° percentile della latenza delle query per tre diversi livelli di utilizzo.

| Percentuale del numero massimo di query al secondo  | Latenza media | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- | 
| 20%  | 104 ms  | 35 ms  | 115 ms   | 177 ms | 257 MS | 738 MS |
| 50%  | 140 ms  | 47 ms  | 144 ms   | 241 MS | 400 ms | 1175 MS |
| 80%  | 239 MS  | 77 ms  | 248 MS   | 466 MS | 763 MS | 1752 MS | 


### <a name="s2-performance"></a>Prestazioni S2

#### <a name="queries-per-second"></a>Query al secondo

Il grafico seguente mostra il carico di query più elevato che un servizio può gestire per un lungo periodo di tempo in termini di query al secondo (query al secondo).

![QUERY al secondo ecommerce S2 più grande](./media/performance-benchmarks/s2-ecom-qps.png)

#### <a name="query-latency"></a>Latenza query

La latenza delle query varia in base al carico del servizio e i servizi con maggiore stress avranno una latenza di query media superiore. Nella tabella seguente sono illustrati i 25, 50, 75, 90, 95 e 99 ° percentile della latenza delle query per tre diversi livelli di utilizzo.

| Percentuale del numero massimo di query al secondo  | Latenza media | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- | 
| 20%  | 56 ms | 21 MS | 68 ms  | 106 ms  | 132 MS | 210 ms | 
| 50%  | 71 ms  | 26 ms  | 83 ms   | 132 MS | 177 ms | 329 ms |
| 80%  | 140 ms  | 47 ms  | 153 ms   | 293 MS | 452 MS | 924 MS | 

### <a name="s3-performance"></a>Prestazioni S3

#### <a name="queries-per-second"></a>Query al secondo

Il grafico seguente mostra il carico di query più elevato che un servizio può gestire per un lungo periodo di tempo in termini di query al secondo (query al secondo).

![QUERY al secondo eCommerce più grande e gestibile](./media/performance-benchmarks/s3-ecom-qps.png)

In questo caso, si noterà che l'aggiunta di una seconda partizione aumenta significativamente il numero massimo di query al secondo, ma l'aggiunta di una terza partizione comporta una riduzione dei ritorni marginali. Il miglioramento più piccolo è probabilmente dovuto al fatto che tutti i dati sono già stati inseriti nella memoria attiva S3 con solo due partizioni.

#### <a name="query-latency"></a>Latenza query

La latenza delle query varia in base al carico del servizio e i servizi con maggiore stress avranno una latenza di query media superiore. Nella tabella seguente sono illustrati i 25, 50, 75, 90, 95 e 99 ° percentile della latenza delle query per tre diversi livelli di utilizzo.

| Percentuale del numero massimo di query al secondo  | Latenza media | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 50 ms  | 20 ms  | 64 MS   | 83 ms | 98 ms | 160 ms |
| 50%  | 62 ms  | 24 ms  | 80 ms   | 107 MS | 130 ms | 253 MS |
| 80%  | 115 ms  | 38 ms  | 121 ms   | 218 ms | 352 ms | 828 MS |

## <a name="benchmark-2-document-search"></a>Benchmark 2: ricerca documenti

### <a name="scenario-details"></a>Dettagli dello scenario

- **Conteggio documenti**: 7,5 milioni
- **Dimensioni indice**: 22 GB
- **Schema dell'indice**: 23 campi; 8 ricercabile, 10 filtrabile/facet
- **Tipi di query**: ricerca di parole chiave con facet e evidenziazione dei riscontri

### <a name="s1-performance"></a>Prestazioni S1

#### <a name="queries-per-second"></a>Query al secondo

Il grafico seguente mostra il carico di query più elevato che un servizio può gestire per un lungo periodo di tempo in termini di query al secondo (query al secondo).

![Ricerca doc query al secondo più elevata, S1](./media/performance-benchmarks/s1-docsearch-qps.png)

#### <a name="query-latency"></a>Latenza query

La latenza delle query varia in base al carico del servizio e i servizi con maggiore stress avranno una latenza di query media superiore. Nella tabella seguente sono illustrati i 25, 50, 75, 90, 95 e 99 ° percentile della latenza delle query per tre diversi livelli di utilizzo.

| Percentuale del numero massimo di query al secondo  | Latenza media | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 67 ms  | 44 ms  | 77 ms   | 103 MS | 126 ms | 216 ms |
| 50%  | 93 ms  | 59 ms  | 110 ms   | 150 ms | 184 MS | 304 MS |
| 80%  | 150 ms  | 96 ms  | 184 MS   | 248 MS | 297 MS | 424 MS |

### <a name="s2-performance"></a>Prestazioni S2

#### <a name="queries-per-second"></a>Query al secondo

Il grafico seguente mostra il carico di query più elevato che un servizio può gestire per un lungo periodo di tempo in termini di query al secondo (query al secondo).

![Ricerca doc query al secondo più elevata (S2)](./media/performance-benchmarks/s2-docsearch-qps.png)

#### <a name="query-latency"></a>Latenza query

La latenza delle query varia in base al carico del servizio e i servizi con maggiore stress avranno una latenza di query media superiore. Nella tabella seguente sono illustrati i 25, 50, 75, 90, 95 e 99 ° percentile della latenza delle query per tre diversi livelli di utilizzo.

| Percentuale del numero massimo di query al secondo  | Latenza media | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 45 ms  | 31 ms  | 55 ms   | 73 ms | 84 ms | 109 ms |
| 50%  | 63 ms  | 39 ms  | 81 ms   | 106 ms | 123 MS | 163 MS |
| 80%  | 115 ms  | 73 ms  | 145 ms   | 191 MS | 224 MS | 291 MS |

## <a name="takeaways"></a>Risultati

Grazie a questi benchmark, è possibile ottenere un'idea delle prestazioni offerte da Azure ricerca cognitiva. È anche possibile visualizzare la differenza tra i servizi a livelli diversi.

Di seguito sono riportate alcune opzioni chiave di questi benchmark:

* Un S2 può in genere gestire almeno quattro volte il volume di query come S1
* Un S2 ha in genere una latenza inferiore rispetto a un S1 in volumi di query paragonabili
* Quando si aggiungono repliche, il query al secondo che un servizio può gestire in genere viene scalato in modo lineare (ad esempio, se una replica è in grado di gestire 10 query al secondo quindi cinque repliche possono in genere gestire 50 query al secondo)
* Maggiore è il carico sul servizio, maggiore sarà la latenza media

È anche possibile notare che le prestazioni possono variare drasticamente tra gli scenari. Se non si desidera ottenere le prestazioni desiderate, consultare i [Suggerimenti per ottenere prestazioni migliori](search-performance-tips.md).

## <a name="next-steps"></a>Passaggi successivi

Ora che sono stati visualizzati i benchmark delle prestazioni, è possibile ottenere altre informazioni su come analizzare le prestazioni e i fattori chiave di ricerca cognitiva che influenzano le prestazioni.

> [!div class="nextstepaction"]
> [Analizzare le prestazioni](search-performance-analysis.md) 
>  [Suggerimenti per migliorare le prestazioni](search-performance-tips.md)