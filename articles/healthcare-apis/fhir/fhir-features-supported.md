---
title: Funzionalità FHIR supportate in Azure - API di Azure per FHIR
description: Questo articolo illustra le funzionalità della specifica FHIR implementate in API di Azure per FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/15/2021
ms.author: cavoeg
ms.openlocfilehash: 56e3ba46ffb43aec907d729a2e74cdf6f7a62c32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530632"
---
# <a name="features"></a>Funzionalità

API di Azure per FHIR offre una distribuzione completamente gestita del server Microsoft FHIR per Azure. Il server è un'implementazione dello standard [FHIR.](https://hl7.org/fhir) Questo documento elenca le principali funzionalità del server FHIR.

## <a name="fhir-version"></a>Versione di FHIR

Versione più recente supportata: `4.0.1`

Le versioni precedenti attualmente supportate includono: `3.0.2`

## <a name="rest-api"></a>API REST

| API                            | Supportato - PaaS | Supportato - OSS (SQL) | Supportato - OSS (Cosmos DB) | Commento                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| lettura                           | Sì       | Sì       | Sì       |                                                     |
| vread                          | Sì       | Sì       | Sì       |                                                     |
| update                         | Sì       | Sì       | Sì       |                                                     |
| aggiornamento con blocco ottimistico | Sì       | Sì       | Sì       |                                                     |
| update (condizionale)           | Sì       | Sì       | Sì       |                                                     |
| benda                          | No        | No        | No        |                                                     |
| eliminare                         | Sì       | Sì       | Sì       |  Vedere la nota seguente.                                   |
| delete (condizionale)           | No        | No        | No        |                                                     |
| history                        | Sì       | Sì       | Sì       |                                                     |
| create                         | Sì       | Sì       | Sì       | Supportare sia POST/PUT                               |
| create (condizionale)           | Sì       | Sì       | Sì       | Problemi [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| ricerca                         | Parziale   | Parziale   | Parziale   | Vedere la sezione Ricerca più avanti.                           |
| ricerca concatenata                 | Parziale       | Sì       | Parziale   | Vedere la nota 2 riportata di seguito.                                   |
| ricerca concatenata inversa         | Parziale       | Sì       | Parziale   | Vedere la nota 2 riportata di seguito.                                   |
| capabilities                   | Sì       | Sì       | Sì       |                                                     |
| o batch                          | Sì       | Sì       | Sì       |                                                     |
| transaction                    | No        | Sì       | No        |                                                     |
| paging                         | Parziale   | Parziale   | Parziale   | `self` e `next` sono supportati                     |
| Intermediari                 | No        | No        | No        |                                                     |

> [!Note]
> L'eliminazione definita dalla specifica FHIR richiede che, dopo l'eliminazione, le successive operazioni di lettura non specifiche della versione di una risorsa restituiranno un codice di stato HTTP 410 e la risorsa non verrà più trovata durante la ricerca. Il API di Azure per FHIR consente anche di eliminare completamente (inclusa tutta la cronologia) la risorsa. Per eliminare completamente la risorsa, è possibile passare le impostazioni di un `hardDelete` parametro a true ( `DELETE {server}/{resource}/{id}?hardDelete=true` ). Se non si passa questo parametro o si imposta su false, le versioni precedenti della risorsa `hardDelete` saranno ancora disponibili.


 **Nota 2**
* Aggiunge il supporto MVP per la ricerca FHIR concatenata e inversa in CosmosDB. 

  Nel API di Azure per FHIR e nel server FHIR open source supportato da Cosmos, la ricerca concatenata e la ricerca concatenata inversa sono un'implementazione MVP. Per eseguire la ricerca concatenata Cosmos DB, l'implementazione di illustra l'espressione di ricerca ed esegue sotto query per risolvere le risorse corrispondenti. Questa operazione viene eseguita per ogni livello dell'espressione. Se una query restituisce più di 100 risultati, verrà generato un errore. Per impostazione predefinita, la ricerca concatenata è dietro un flag di funzionalità. Per usare la ricerca concatenata in Cosmos DB, usare l'intestazione `x-ms-enable-chained-search: true` . Per altri dettagli, vedere [Richiesta pull 1695.](https://github.com/microsoft/fhir-server/pull/1695)

## <a name="search"></a>Cerca

Sono supportati tutti i tipi di parametri di ricerca. 

| Tipo di parametro di ricerca | Supportato - PaaS | Supportato - OSS (SQL) | Supportato - OSS (Cosmos DB) | Commento |
|-----------------------|-----------|-----------|-----------|---------|
| Number                | Sì       | Sì       | Sì       |         |
| Date/DateTime         | Sì       | Sì       | Sì       |         |
| string                | Sì       | Sì       | Sì       |         |
| Token                 | Sì       | Sì       | Sì       |         |
| Riferimento             | Sì       | Sì       | Sì       |         |
| Composite             | Sì       | Sì       | Sì       |         |
| Quantità              | Sì       | Sì       | Sì       |         |
| URI                   | Sì       | Sì       | Sì       |         |
| Speciali               | No        | No        | No        |         |


| Modificatori             | Supportato - PaaS | Supportato - OSS (SQL) | Supportato - OSS (Cosmos DB) | Comment |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Sì       | Sì       | Sì       |         |
|`:exact`               | Sì       | Sì       | Sì       |         |
|`:contains`            | Sì       | Sì       | Sì       |         |
|`:text`                | Sì       | Sì       | Sì       |         |
|`:[type]` (riferimento)  | Sì       | Sì       | Sì       |         |
|`:not`                 | Sì       | Sì       | Sì       |         |
|`:below` (uri)         | Sì       | Sì       | Sì       |         |
|`:above` (uri)         | No        | No        | No        | Problemi [#158](https://github.com/Microsoft/fhir-server/issues/158) |
|`:in` (token)          | No        | No        | No        |         |
|`:below` (token)       | No        | No        | No        |         |
|`:above` (token)       | No        | No        | No        |         |
|`:not-in` (token)      | No        | No        | No        |         |

| Parametro di ricerca comune | Supportato - PaaS | Supportato - OSS (SQL) | Supportato - OSS (Cosmos DB) | Comment |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Sì       | Sì       | Sì       |         |
| `_lastUpdated`          | Sì       | Sì       | Sì       |         |
| `_tag`                  | Sì       | Sì       | Sì       |         |
| `_list`                 | Sì       | Sì       | Sì       |         |
| `_type`                 | Sì       | Sì       | Sì       | Problema [#1562](https://github.com/microsoft/fhir-server/issues/1562)        |
| `_security`             | Sì       | Sì       | Sì       |         |
| `_profile`              | Parziale   | Parziale   | Parziale   | Supportato in STU3. Se il database è **stato creato dopo** il 20 febbraio 2021, si avrà anche il supporto per R4. Microsoft sta lavorando per abilitare _profile database creati prima del 20 febbraio 2021. |
| `_text`                 | No        | No        | No        |         |
| `_content`              | No        | No        | No        |         |
| `_has`                  | No        | No        | No        |         |
| `_query`                | No        | No        | No        |         |
| `_filter`               | No        | No        | No        |         |

| Parametri dei risultati della ricerca | Supportato - PaaS | Supportato - OSS (SQL) | Supportato - OSS (Cosmos DB) | Comment |
|-------------------------|-----------|-----------|-----------|---------|
| `_elements`             | Sì       | Sì       | Sì       | Problema [#1256](https://github.com/microsoft/fhir-server/issues/1256)        |
| `_count`                | Sì       | Sì       | Sì       | `_count` è limitato a 1000 caratteri. Se impostato su un valore maggiore di 1000, verrà restituito solo 1000 e nel bundle verrà restituito un avviso. |
| `_include`              | Sì       | Sì       | Sì       |Gli elementi inclusi sono limitati a 100. Includi in PaaS e OSS Cosmos DB non include il supporto per l'iterazione.|
| `_revinclude`           | Sì       | Sì       | Sì       | Gli elementi inclusi sono limitati a 100. Includi in PaaS e OSS Cosmos DB non include il supporto [di :iterate](https://github.com/microsoft/fhir-server/issues/1313). Problema [#1319](https://github.com/microsoft/fhir-server/issues/1319)|
| `_summary`              | Parziale   | Parziale   | Parziale   | `_summary=count` è supportato |
| `_total`                | Parziale   | Parziale   | Parziale   | `_total=none` e `_total=accurate`      |
| `_sort`                 | Parziale   | Parziale   | Parziale   |   `_sort=_lastUpdated` è supportato       |
| `_contained`            | No        | No        | No        |         |
| `containedType`         | No        | No        | No        |         |
| `_score`                | No        | No        | No        |         |

## <a name="extended-operations"></a>Operazioni estese

Tutte le operazioni supportate che estendono l'API RESTful.

| Tipo di parametro di ricerca | Supportato - PaaS | Supportato - OSS (SQL) | Supportato - OSS (Cosmos DB) | Commento |
|------------------------|-----------|-----------|-----------|---------|
| $export (intero sistema) | Sì       | Sì       | Sì       |         |
| Paziente/$export        | Sì       | Sì       | Sì       |         |
| Gruppo/$export          | Sì       | Sì       | Sì       |         |
| $convert-data          | Sì       | Sì       | Sì       |         |


## <a name="persistence"></a>Persistenza

Il server Microsoft FHIR ha un modulo di persistenza collegabile (vedere [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Attualmente il codice open source del server FHIR include [un'implementazione](../../cosmos-db/index-overview.md) per Azure Cosmos DB database [SQL.](https://azure.microsoft.com/services/sql-database/)

Cosmos DB è un database multi model distribuito a livello globale (API SQL, API MongoDB e così via). Supporta livelli [di coerenza diversi.](../../cosmos-db/consistency-levels.md) Il modello di distribuzione predefinito configura il server FHIR con coerenza, ma i criteri di coerenza possono essere modificati (in genere relaxed) su richiesta tramite l'intestazione `Strong` `x-ms-consistency-level` della richiesta.

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Il server FHIR [usa](https://azure.microsoft.com/services/active-directory/) Azure Active Directory per il controllo di accesso. In particolare, viene applicato il controllo degli accessi in base al ruolo, se il parametro di configurazione è impostato su e tutte le richieste (ad eccezione di ) al server FHIR devono avere l'intestazione della richiesta `FhirServer:Security:Enabled` `true` `/metadata` `Authorization` impostata su `Bearer <TOKEN>` . Il token deve contenere uno o più ruoli come definito `roles` nell'attestazione. Una richiesta sarà consentita se il token contiene un ruolo che consente l'azione specificata nella risorsa specificata.

Attualmente, le azioni consentite per un determinato ruolo vengono applicate a livello *globale* all'API.

## <a name="service-limits"></a>Limiti del servizio

* [**Unità richiesta : è**](../../cosmos-db/concepts-limits.md) possibile configurare fino a 10.000 UNITÀ RICHIESTA nel portale per API di Azure per FHIR. Saranno necessarie almeno 400 UNITÀ DI RETE o 40 UNITÀ DI ARCHIVIAZIONE/GB, a seconda di quale sia il valore maggiore. Se sono necessarie più di 10.000 UNITÀ RICHIESTA, è possibile inserire un ticket di supporto per aumentarlo. Il valore massimo disponibile è 1.000.000.

* **Connessioni simultanee** **e** istanze: per impostazione predefinita, sono disponibili cinque connessioni simultanee in due istanze del cluster (per un totale di 10 richieste simultanee). Se si ritiene di aver bisogno di più richieste simultanee, aprire un ticket di supporto con i dettagli sulle proprie esigenze.

* **Dimensioni bundle:** ogni bundle è limitato a 500 elementi.

* **Dimensioni dei** dati: i dati e i documenti devono essere leggermente inferiori a 2 MB.

## <a name="performance-expectations"></a>Aspettative di prestazioni

Le prestazioni del sistema dipendono dal numero di UNITÀ richiesta, dalle connessioni simultanee e dal tipo di operazioni eseguite (Put, Post e così via). Di seguito sono riportati alcuni intervalli generali di ciò che ci si può aspettare in base alle UNITÀ di ricerca configurate. In generale, le prestazioni vengono ridimensionate in modo lineare con un aumento delle unità richieste:

| N. di unità di ricerca | Risorse/sec |    Spazio di archiviazione massimo (GB)*    |
|----------|---------------|--------|                 
| 400      | 5-10          |     10   |
| 1\.000    | 100-150       |      25  |
| 10,000   | 225-400       |      250  |
| 100,000  | 2,500-4,000   |      2\.500  |

Nota: per Cosmos DB requisito, è necessario un requisito di una velocità effettiva minima di 40 RU/s per GB di spazio di archiviazione. 

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono disponibili informazioni sulle funzionalità FHIR supportate in API di Azure per FHIR. Distribuire quindi il API di Azure per FHIR.
 
>[!div class="nextstepaction"]
>[Distribuire l'API di Azure per FHIR](fhir-paas-portal-quickstart.md)
