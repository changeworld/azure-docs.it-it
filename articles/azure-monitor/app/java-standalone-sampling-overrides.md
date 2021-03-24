---
title: Override del campionamento (anteprima)-Application Insights di monitoraggio di Azure per Java
description: Informazioni su come configurare le sostituzioni di campionamento in monitoraggio di Azure Application Insights per Java.
ms.topic: conceptual
ms.date: 03/22/2021
author: trask
ms.custom: devx-track-java
ms.author: trstalna
ms.openlocfilehash: 03d3093f14d97b2cc64d91e0d1b7adf34204a021
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962604"
---
# <a name="sampling-overrides-preview---azure-monitor-application-insights-for-java"></a>Override del campionamento (anteprima)-Application Insights di monitoraggio di Azure per Java

> [!NOTE]
> La funzionalità di override del campionamento è in anteprima.

Di seguito sono riportati alcuni casi d'uso per le sostituzioni di campionamento:
 * Evitare di raccogliere dati di telemetria per i controlli di integrità.
 * Non consentire la raccolta di dati di telemetria per chiamate di dipendenza rumorose
 * Ridurre il rumore da controlli di integrità o chiamate di dipendenza rumorose senza eliminarli completamente.
 * Raccoglie il 100% dei dati di telemetria per un tipo di richiesta importante, ad esempio `/login` , anche se il campionamento predefinito è configurato su un valore inferiore.

## <a name="terminology"></a>Terminologia

Prima di acquisire familiarità con le sostituzioni di campionamento, è necessario comprendere il termine *intervallo*. Un intervallo è un termine generale per:

* Una richiesta in ingresso.
* Dipendenza in uscita (ad esempio, una chiamata remota a un altro servizio).
* Dipendenza in-process, ad esempio il lavoro eseguito dai sottocomponenti del servizio.

Per gli override di campionamento, questi componenti span sono importanti:

* Attributi

Gli attributi span rappresentano proprietà standard e personalizzate di una determinata richiesta o dipendenza.

## <a name="getting-started"></a>Introduzione

Per iniziare, creare un file di configurazione denominato *applicationinsights.json*. Salvarlo nella stessa directory di *applicationinsights-Agent- \* . jar*. Usare il modello seguente.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            ...
          ],
          "percentage": 0
        },
        {
          "attributes": [
            ...
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="how-it-works"></a>Funzionamento

Quando viene avviato un intervallo, gli attributi presenti nell'intervallo in quel momento vengono utilizzati per verificare se una qualsiasi delle sostituzioni del campionamento corrisponde.

Se una delle sostituzioni di campionamento corrisponde, viene usata la relativa percentuale di campionamento per decidere se campionare o meno l'intervallo.

Viene utilizzato solo il primo override del campionamento corrispondente.

Se nessun override di campionamento corrisponde a:

* Se si tratta del primo intervallo nella traccia, viene utilizzata la [percentuale di campionamento normale](./java-standalone-config.md#sampling) .
* Se non si tratta del primo intervallo della traccia, viene utilizzata la decisione di campionamento padre.

> [!IMPORTANT]
> Quando si è deciso di non raccogliere un intervallo, non verranno raccolti anche tutti gli intervalli downstream, anche se sono presenti sostituzioni di campionamento che corrispondono all'intervallo downstream.
> Questo comportamento è necessario perché, in caso contrario, si verificheranno tracce interrotte, con intervalli downstream raccolti ma con un elemento padre di intervalli non raccolti.

> [!NOTE]
> La decisione di campionamento è basata sull'hashing di traceId (noto anche come operationId) a un numero compreso tra 0 e 100 e tale hash viene quindi confrontato con la percentuale di campionamento.
> Poiché tutti gli intervalli in una determinata traccia avranno lo stesso traceId, avranno lo stesso hash, quindi la decisione di campionamento sarà coerente nell'intera traccia.

## <a name="example-suppress-collecting-telemetry-for-health-checks"></a>Esempio: eliminazione della raccolta dei dati di telemetria per i controlli di integrità

Questa operazione eliminerà la raccolta dei dati di telemetria per tutte le richieste a `/health-checks` .

Questa operazione eliminerà anche la raccolta di eventuali intervalli downstream (dipendenze) che verrebbero normalmente raccolti in `/health-checks` .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/health-check",
              "matchType": "regexp"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-suppress-collecting-telemetry-for-a-noisy-dependency-call"></a>Esempio: eliminazione della raccolta dei dati di telemetria per una chiamata di dipendenza rumorosa

Questa operazione eliminerà la raccolta dei dati di telemetria per tutte le `GET my-noisy-key` chiamate Redis.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "db.system",
              "value": "redis",
              "matchType": "strict"
            },
            {
              "key": "db.statement",
              "value": "GET my-noisy-key",
              "matchType": "strict"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-collect-100-of-telemetry-for-an-important-request-type"></a>Esempio: raccolta del 100% dei dati di telemetria per un tipo di richiesta importante

Verrà raccolto il 100% dei dati di telemetria per `/login` .

Poiché gli intervalli a valle (dipendenze) rispettano la decisione di campionamento del padre (assente qualsiasi override di campionamento per tale intervallo downstream), verranno raccolti anche per tutte le richieste '/login '.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/login",
              "matchType": "regexp"
            }
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="common-span-attributes"></a>Attributi span comuni

In questa sezione vengono elencati alcuni attributi di intervallo comuni che possono essere utilizzati dagli override di campionamento.

### <a name="http-spans"></a>Intervalli HTTP

| Attributo  | Type | Descrizione | 
|---|---|---|
| `http.method` | string | Metodo di richiesta HTTP.|
| `http.url` | string | URL completo della richiesta HTTP nel formato `scheme://host[:port]/path?query[#fragment]` . Il frammento non viene in genere trasmesso tramite HTTP. Tuttavia, se il frammento è noto, deve essere incluso.|
| `http.status_code` | d'acquisto | [Codice di stato della risposta http](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | string | Tipo di protocollo HTTP. |
| `http.user_agent` | string | Valore dell'intestazione [http dell'agente utente](https://tools.ietf.org/html/rfc7231#section-5.5.3) inviata dal client. |

### <a name="jdbc-spans"></a>Intervalli JDBC

| Attributo  | Type | Descrizione  |
|---|---|---|
| `db.system` | string | Identificatore per il prodotto del sistema di gestione di database (DBMS) utilizzato. |
| `db.connection_string` | string | Stringa di connessione utilizzata per connettersi al database. Si consiglia di rimuovere le credenziali incorporate.|
| `db.user` | string | Nome utente per l'accesso al database. |
| `db.name` | string | Stringa utilizzata per segnalare il nome del database a cui si accede. Per i comandi che comunicano il database, è necessario impostare questa stringa sul database di destinazione, anche se il comando ha esito negativo.|
| `db.statement` | string | Istruzione di database in esecuzione.|
