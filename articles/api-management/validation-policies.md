---
title: Criteri di convalida di gestione API di Azure | Microsoft Docs
description: Informazioni sui criteri che è possibile usare in gestione API di Azure per convalidare le richieste e le risposte.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 03/12/2021
ms.author: apimpm
ms.openlocfilehash: 1a835d26b4c41c92b9849856a2f31b3550947bd8
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801894"
---
# <a name="api-management-policies-to-validate-requests-and-responses"></a>Criteri di gestione API per convalidare le richieste e le risposte

Questo articolo fornisce un riferimento per i seguenti criteri di gestione API. Per informazioni sull'aggiunta e sulla configurazione dei criteri, vedere [Criteri di Gestione API](./api-management-policies.md).

Usare i criteri di convalida per convalidare le richieste e le risposte API rispetto a uno schema OpenAPI e proteggere da vulnerabilità come l'inserimento di intestazioni o payload. Sebbene non sia una sostituzione per un Web Application Firewall, i criteri di convalida forniscono la flessibilità necessaria per rispondere a una classe di minacce aggiuntiva che non sono coperti da prodotti di sicurezza basati su regole statiche e predefinite.

## <a name="validation-policies"></a>Criteri di convalida

- [Convalidare il contenuto](#validate-content) : convalida la dimensione o lo schema JSON di un corpo della richiesta o della risposta rispetto allo schema dell'API. 
- [Validate parameters](#validate-parameters) : convalida i parametri dell'intestazione della richiesta, della query o del percorso rispetto allo schema dell'API.
- [Convalidare](#validate-headers) le intestazioni: convalida le intestazioni di risposta rispetto allo schema dell'API.
- [Convalidare il codice](#validate-status-code) di stato: convalida i codici di stato HTTP nelle risposte rispetto allo schema dell'API.

> [!NOTE]
> La dimensione massima dello schema API che può essere utilizzata da un criterio di convalida è 4 MB. Se lo schema supera questo limite, i criteri di convalida restituiranno errori in fase di esecuzione. Per aumentarlo, contattare il [supporto tecnico](https://azure.microsoft.com/support/options/). 

## <a name="actions"></a>Azioni

Ogni criterio di convalida include un attributo che specifica un'azione eseguita da gestione API durante la convalida di un'entità in una richiesta o risposta API rispetto allo schema dell'API. È possibile specificare un'azione per gli elementi rappresentati nello schema dell'API e, a seconda del criterio, per gli elementi che non sono rappresentati nello schema dell'API. Un'azione specificata nell'elemento figlio di un criterio esegue l'override di un'azione specificata per il padre.

Azioni disponibili:

| Azione         | Descrizione          |                                                                                                                         
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| ignore | Ignorare la convalida. |
| impedire | Blocca l'elaborazione della richiesta o della risposta, registra l'errore di convalida dettagliata e restituisce un errore. L'elaborazione viene interrotta quando viene rilevato il primo set di errori. |
| rilevare | Errori di convalida del log senza interrompere l'elaborazione della richiesta o della risposta. |

## <a name="logs"></a>Log

Per informazioni dettagliate sugli errori di convalida durante l'esecuzione dei criteri, accedere alla variabile `context.Variables` specificata nell' `errors-variable-name` attributo nell'elemento radice del criterio. Quando viene configurata in un' `prevent` azione, un errore di convalida blocca un'ulteriore elaborazione della richiesta o della risposta e viene propagata anche alla `context.LastError` Proprietà. 

Per esaminare gli errori, utilizzare un criterio di [traccia](api-management-advanced-policies.md#Trace) per registrare gli errori dalle variabili di contesto a [Application Insights](api-management-howto-app-insights.md).

## <a name="performance-implications"></a>Implicazioni per le prestazioni

L'aggiunta di criteri di convalida può influenzare la velocità effettiva dell'API. Si applicano i principi generali seguenti:
* Maggiore è la dimensione dello schema dell'API, minore sarà la velocità effettiva. 
* Maggiore è il payload in una richiesta o risposta, minore sarà la velocità effettiva. 
* Le dimensioni dello schema API hanno un maggiore effetto sulle prestazioni rispetto alle dimensioni del payload. 
* La convalida in base a uno schema API con una dimensione di diversi megabyte può causare timeout della richiesta o della risposta in determinate condizioni. L'effetto è più pronunciato nei livelli di  **utilizzo** e di **sviluppo** del servizio. 

È consigliabile eseguire test di carico con i carichi di lavoro di produzione previsti per valutare l'effetto dei criteri di convalida sulla velocità effettiva dell'API.

## <a name="validate-content"></a>Convalidare il contenuto

Il `validate-content` criterio convalida la dimensione o lo schema JSON di un corpo della richiesta o della risposta rispetto allo schema dell'API. Non sono supportati formati diversi da JSON.

### <a name="policy-statement"></a>Istruzione del criterio

```xml
<validate-content unspecified-content-type-action="ignore|prevent|detect" max-size="size in bytes" size-exceeded-action="ignore|prevent|detect" errors-variable-name="variable name">
    <content type="content type string, for example: application/json, application/hal+json" validate-as="json" action="ignore|prevent|detect" />
</validate-content>
```

### <a name="example"></a>Esempio

Nell'esempio seguente il payload JSON nelle richieste e nelle risposte viene convalidato in modalità di rilevamento. I messaggi con payload maggiori di 100 KB sono bloccati. 

```xml
<validate-content unspecified-content-type-action="prevent" max-size="102400" size-exceeded-action="prevent" errors-variable-name="requestBodyValidation">
    <content type="application/json" validate-as="json" action="detect" />
    <content type="application/hal+json" validate-as="json" action="detect" />
</validate-content>

```

### <a name="elements"></a>Elementi

| Nome         | Descrizione                                                                                                                                   | Obbligatoria |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| convalida-contenuto | Elemento radice.                                                                                                                               | Sì      |
| contenuto | Aggiungere uno o più di questi elementi per convalidare il tipo di contenuto nella richiesta o nella risposta ed eseguire l'azione specificata.  | No |

### <a name="attributes"></a>Attributi

| Nome                       | Descrizione                                                                                                                                                            | Obbligatoria | Predefinito |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| non specificato-Content-Type-Action | [Azione](#actions) da eseguire per richieste o risposte con un tipo di contenuto non specificato nello schema dell'API. |  Sì     | N/D   |
| dimensioni massime | Lunghezza massima del corpo della richiesta o della risposta in byte, verificata rispetto all' `Content-Length` intestazione. Se il corpo della richiesta o il corpo della risposta è compresso, questo valore è la lunghezza decompressa. Valore massimo consentito: 102.400 byte (100 KB).  | Sì       | N/D   |
| dimensioni-superato-azione | [Azione](#actions) da eseguire per le richieste o le risposte il cui corpo supera le dimensioni specificate in `max-size` . |  Sì     | N/D   |
| errori-nome-variabile | Nome della variabile in `context.Variables` cui registrare gli errori di convalida.  |   Sì    | N/D   |
| tipo | Tipo di contenuto per eseguire la convalida del corpo per, verificato rispetto all' `Content-Type` intestazione. Questo valore non fa distinzione tra maiuscole e minuscole. Se è vuoto, si applica a ogni tipo di contenuto specificato nello schema dell'API. |   No    |  N/D  |
| convalida-come | Motore di convalida da usare per la convalida del corpo di una richiesta o di una risposta con un tipo di contenuto corrispondente. Attualmente, l'unico valore supportato è "JSON".   |  Sì     |  N/D  |
| azione | [Azione](#actions) da eseguire per richieste o risposte il cui corpo non corrisponde al tipo di contenuto specificato.  |  Sì      | N/D   |

### <a name="usage"></a>Utilizzo

Questo criterio può essere usato nelle [sezioni](./api-management-howto-policies.md#sections) e negli [ambiti](./api-management-howto-policies.md#scopes) del criterio seguenti.

-   **Sezioni del criterio:** inbound, outbound, on-error

-   **Ambiti del criterio:** tutti gli ambiti

## <a name="validate-parameters"></a>Convalida parametri

Il `validate-parameters` criterio convalida i parametri di intestazione, query o percorso nelle richieste rispetto allo schema dell'API.

> [!IMPORTANT]
> Se è stata importata un'API usando una versione dell'API di gestione precedente a `2021-01-01-preview` , il `validate-parameters` criterio potrebbe non funzionare. Potrebbe essere necessario [reimportare l'API usando la versione dell'](/rest/api/apimanagement/2021-01-01-preview/apis/createorupdate) API di gestione `2021-01-01-preview` o versioni successive.


### <a name="policy-statement"></a>Istruzione del criterio

```xml
<validate-parameters specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect" errors-variable-name="variable name"> 
    <headers specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </headers>
    <query specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </query>
    <path specified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </path>
</validate-parameters>
```

### <a name="example"></a>Esempio

In questo esempio, tutti i parametri di query e percorso vengono convalidati in modalità di prevenzione e intestazioni in modalità di rilevamento. Viene eseguito l'override della convalida per diversi parametri di intestazione:

```xml
<validate-parameters specified-parameter-action="prevent" unspecified-parameter-action="prevent" errors-variable-name="requestParametersValidation"> 
    <headers specified-parameter-action="detect" unspecified-parameter-action="detect">
        <parameter name="Authorization" action="prevent" />
        <parameter name="User-Agent" action="ignore" />
        <parameter name="Host" action="ignore" />
        <parameter name="Referrer" action="ignore" />
    </headers>   
</validate-parameters>
```

### <a name="elements"></a>Elementi

| Nome         | Descrizione                                                                                                                                   | Obbligatoria |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Validate-parametri | Elemento radice. Specifica le azioni di convalida predefinite per tutti i parametri nelle richieste.                                                                                                                              | Sì      |
| headers | Aggiungere questo elemento per eseguire l'override delle azioni di convalida predefinite per i parametri di intestazione nelle richieste.   | No |
| query | Aggiungere questo elemento per eseguire l'override delle azioni di convalida predefinite per i parametri di query nelle richieste.  | No |
| path | Aggiungere questo elemento per eseguire l'override delle azioni di convalida predefinite per i parametri del percorso URL nelle richieste.  | No |
| parametro | Aggiungere uno o più elementi per i parametri denominati per eseguire l'override della configurazione di livello superiore delle azioni di convalida. | No |

### <a name="attributes"></a>Attributi

| Nome                       | Descrizione                                                                                                                                                            | Obbligatoria | Predefinito |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| parametro-Action specificato | [Azione](#actions) da eseguire per i parametri della richiesta specificati nello schema dell'API. <br/><br/> Quando viene specificato in `headers` un `query` elemento, o `path` , il valore esegue l'override del valore di `specified-parameter-action` nell' `validate-parameters` elemento.  |  Sì     | N/D   |
| parametro-Action non specificato | [Azione](#actions) da eseguire per i parametri della richiesta non specificati nello schema dell'API. <br/><br/>Quando viene specificato in `headers` un `query` elemento o, il valore esegue l'override del valore di `unspecified-parameter-action` nell' `validate-parameters` elemento. |  Sì     | N/D   |
| errori-nome-variabile | Nome della variabile in `context.Variables` cui registrare gli errori di convalida.  |   Sì    | N/D   |
| name | Nome del parametro di cui eseguire l'override dell'azione di convalida. Questo valore non fa distinzione tra maiuscole e minuscole.  | Sì | N/D |
| azione | [Azione](#actions) da eseguire per il parametro con il nome corrispondente. Se il parametro viene specificato nello schema dell'API, questo valore esegue l'override della configurazione di livello superiore `specified-parameter-action` . Se il parametro non è specificato nello schema dell'API, questo valore esegue l'override della configurazione di livello superiore `unspecified-parameter-action` .| Sì | N/D | 

### <a name="usage"></a>Utilizzo

Questo criterio può essere usato nelle [sezioni](./api-management-howto-policies.md#sections) e negli [ambiti](./api-management-howto-policies.md#scopes) del criterio seguenti.

-   **Sezioni del criterio:** inbound

-   **Ambiti del criterio:** tutti gli ambiti

## <a name="validate-headers"></a>Convalida intestazioni

Il `validate-headers` criterio convalida le intestazioni di risposta rispetto allo schema dell'API.

> [!IMPORTANT]
> Se è stata importata un'API usando una versione dell'API di gestione precedente a `2021-01-01-preview` , il `validate-headers` criterio potrebbe non funzionare. Potrebbe essere necessario reimportare l'API usando la versione dell'API di gestione `2021-01-01-preview` o versioni successive.

### <a name="policy-statement"></a>Istruzione del criterio

```xml
<validate-headers specified-header-action="ignore|prevent|detect" unspecified-header-action="ignore|prevent|detect" errors-variable-name="variable name">
    <header name="header name" action="ignore|prevent|detect" />
</validate-headers>
```

### <a name="example"></a>Esempio

```xml
<validate-headers specified-header-action="ignore" unspecified-header-action="prevent" errors-variable-name="responseHeadersValidation" />
```
### <a name="elements"></a>Elementi

| Nome         | Descrizione                                                                                                                                   | Obbligatoria |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Validate-intestazioni | Elemento radice. Specifica le azioni di convalida predefinite per tutte le intestazioni nelle risposte.                                                                                                                              | Sì      |
| header | Aggiungere uno o più elementi per le intestazioni denominate per eseguire l'override delle azioni di convalida predefinite per le intestazioni nelle risposte. | No |

### <a name="attributes"></a>Attributi

| Nome                       | Descrizione                                                                                                                                                            | Obbligatoria | Predefinito |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| intestazione specificata-azione | [Azione](#actions) da eseguire per le intestazioni di risposta specificate nello schema dell'API.  |  Sì     | N/D   |
| non specificato-header-Action | [Azione](#actions) da eseguire per le intestazioni di risposta che non sono specificate nello schema dell'API.  |  Sì     | N/D   |
| errori-nome-variabile | Nome della variabile in `context.Variables` cui registrare gli errori di convalida.  |   Sì    | N/D   |
| name | Nome dell'intestazione per cui eseguire l'override dell'azione di convalida. Questo valore non fa distinzione tra maiuscole e minuscole. | Sì | N/D |
| azione | [Azione](#actions) da eseguire per l'intestazione con il nome corrispondente. Se l'intestazione è specificata nello schema dell'API, questo valore esegue l'override del valore di `specified-header-action` nell' `validate-headers` elemento. In caso contrario, esegue l'override del valore di `unspecified-header-action` nell'elemento Validate-Headers. | Sì | N/D | 

### <a name="usage"></a>Utilizzo

Questo criterio può essere usato nelle [sezioni](./api-management-howto-policies.md#sections) e negli [ambiti](./api-management-howto-policies.md#scopes) del criterio seguenti.

-   **Sezioni del criterio:** in uscita, in errore

-   **Ambiti del criterio:** tutti gli ambiti

## <a name="validate-status-code"></a>Convalida codice di stato

Il `validate-status-code` criterio convalida i codici di stato HTTP nelle risposte rispetto allo schema dell'API. Questi criteri possono essere usati per impedire la perdita di errori back-end, che possono contenere tracce dello stack. 

### <a name="policy-statement"></a>Istruzione del criterio

```xml
<validate-status-code unspecified-status-code-action="ignore|prevent|detect" errors-variable-name="variable name">
    <status-code code="HTTP status code number" action="ignore|prevent|detect" />
</validate-status-code>
```

### <a name="example"></a>Esempio

```xml
<validate-status-code unspecified-status-code-action="prevent" errors-variable-name="responseStatusCodeValidation" />
```

### <a name="elements"></a>Elementi

| Nome         | Descrizione                                                                                                                                   | Obbligatoria |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Validate-status-code | Elemento radice.                                                                                                | Sì      |
| status-code | Aggiungere uno o più elementi per i codici di stato HTTP per eseguire l'override dell'azione di convalida predefinita per i codici di stato nelle risposte. | No |

### <a name="attributes"></a>Attributi

| Nome                       | Descrizione                                                                                                                                                            | Obbligatoria | Predefinito |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| non specificato-status-code-Action | [Azione](#actions) da eseguire per i codici di stato HTTP nelle risposte non specificate nello schema dell'API.  |  Sì     | N/D   |
| errori-nome-variabile | Nome della variabile in `context.Variables` cui registrare gli errori di convalida.  |   Sì    | N/D   |
| codice | Codice di stato HTTP per cui eseguire l'override dell'azione di convalida. | Sì | N/D |
| azione | [Azione](#actions) da eseguire per il codice di stato corrispondente, che non è specificato nello schema dell'API. Se il codice di stato viene specificato nello schema dell'API, questa sostituzione non ha effetto. | Sì | N/D | 

### <a name="usage"></a>Utilizzo

Questo criterio può essere usato nelle [sezioni](./api-management-howto-policies.md#sections) e negli [ambiti](./api-management-howto-policies.md#scopes) del criterio seguenti.

-   **Sezioni del criterio:** in uscita, in errore

-   **Ambiti del criterio:** tutti gli ambiti


## <a name="validation-errors"></a>Errori di convalida
Nella tabella seguente sono elencati tutti i possibili errori dei criteri di convalida. 

* **Dettagli** -può essere usato per analizzare gli errori. Non concepito per essere condiviso pubblicamente.
* **Risposta pubblica** -errore restituito al client. Non perde i dettagli di implementazione.

| **Nome**                             | **Tipo**                                                        | **Regola di convalida** | **Dettagli**                                                                                                                                       | **Risposta pubblica**                                                                                                                       | **Azione**           |
|----|----|---|---|---|----|
| **convalida-contenuto** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| |RequestBody                                                     | SizeLimit           | Il corpo della richiesta è lungo {size} byte e supera il limite configurato di {maxSize} byte.                                                       | Il corpo della richiesta è lungo {size} byte e supera il limite di {maxSize} byte.                                                          | rileva/Impedisci |
||ResponseBody                                                    | SizeLimit           | Il corpo della risposta è lungo {size} byte e supera il limite configurato di {maxSize} byte.                                                      | Impossibile elaborare la richiesta a causa di un errore interno. Contattare il proprietario dell'API.                                                       | rileva/Impedisci |
| MessageContentType                 | RequestBody                                                     | Non specificata         | Il tipo di contenuto non specificato {messageContentType} non è consentito.                                                                                     | Il tipo di contenuto non specificato {messageContentType} non è consentito.                                                                             | rileva/Impedisci |
| MessageContentType                 | ResponseBody                                                    | Non specificata         | Il tipo di contenuto non specificato {messageContentType} non è consentito.                                                                                     | Impossibile elaborare la richiesta a causa di un errore interno. Contattare il proprietario dell'API.                                                       | rileva/Impedisci |
| | ApiSchema                                                       |                     | Lo schema dell'API non esiste oppure non è stato possibile risolverlo.                                                                                          | Impossibile elaborare la richiesta a causa di un errore interno. Contattare il proprietario dell'API.                                                       | rileva/Impedisci |
|                                      | ApiSchema                                                       |                     | Lo schema dell'API non specifica le definizioni.                                                                                                        | Impossibile elaborare la richiesta a causa di un errore interno. Contattare il proprietario dell'API.                                                       | rileva/Impedisci |
| MessageContentType                 | RequestBody/ResponseBody                                      | MissingDefinition   | Lo schema dell'API non contiene la definizione {definitionName}, associato al tipo di contenuto {messageContentType}.                        | Impossibile elaborare la richiesta a causa di un errore interno. Contattare il proprietario dell'API.                                                       | rileva/Impedisci |
| MessageContentType                 | RequestBody                                                     | IncorrectMessage    | Il corpo della richiesta non è conforme alla definizione {definitionName}, che è associata al tipo di contenuto {messageContentType}.<br/><br/>{valError. Message} riga: {valError. LineNumber}, posizione: {valError. LinePosition}                  | Il corpo della richiesta non è conforme alla definizione {definitionName}, che è associata al tipo di contenuto {messageContentType}.<br/><br/>{valError. Message} riga: {valError. LineNumber}, posizione: {valError. LinePosition}            | rileva/Impedisci |
| MessageContentType                 | ResponseBody                                                    | IncorrectMessage    | Il corpo della risposta non è conforme alla definizione {definitionName}, che è associata al tipo di contenuto {messageContentType}.<br/><br/>{valError. Message} riga: {valError. LineNumber}, posizione: {valError. LinePosition}                                       | Impossibile elaborare la richiesta a causa di un errore interno. Contattare il proprietario dell'API.                                                       | rileva/Impedisci |
|                                      | RequestBody                                                     | ValidationException | Non è possibile convalidare il corpo della richiesta per il tipo di contenuto {messageContentType}.<br/><br/>{Dettagli eccezione}                                                                | Impossibile elaborare la richiesta a causa di un errore interno. Contattare il proprietario dell'API.                                                       | rileva/Impedisci |
|                                      | ResponseBody                                                    | ValidationException | Non è possibile convalidare il corpo della risposta per il tipo di contenuto {messageContentType}.<br/><br/>{Dettagli eccezione}                                                                | Impossibile elaborare la richiesta a causa di un errore interno. Contattare il proprietario dell'API.                                                       | rileva/Impedisci |
| **Validate-Parameter/Validate-Headers** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {paramName}/{headerName}           | QueryParameter/PathParameter/RequestHeader                  | Non specificata         | Non è consentito specificare il parametro {path/parametro di query/intestazione} {paramName}.                                                               | Non è consentito specificare il parametro {path/parametro di query/intestazione} {paramName}.                                                       | rileva/Impedisci |
| headerName                         | ResponseHeader                                                  | Non specificata         | L'intestazione non specificata {headerName} non è consentita.                                                                                                   | Impossibile elaborare la richiesta a causa di un errore interno. Contattare il proprietario dell'API.                                                       | rileva/Impedisci |
|                                      |ApiSchema                                                       |                     | Lo schema dell'API non esiste oppure non è stato possibile risolverlo.                                                                                            | Impossibile elaborare la richiesta a causa di un errore interno. Contattare il proprietario dell'API.                                                       | rileva/Impedisci |
|                                       | ApiSchema                                                       |                     | Lo schema API non specifica le definizioni.                                                                                                          | Impossibile elaborare la richiesta a causa di un errore interno. Contattare il proprietario dell'API.                                                       | rileva/Impedisci |
| paramName                          | QueryParameter/PathParameter/RequestHeader/ResponseHeader | MissingDefinition   | Lo schema dell'API non contiene la definizione {definitionName}, che è associato al parametro/intestazione del percorso {query} {paramName}.  | Impossibile elaborare la richiesta a causa di un errore interno. Contattare il proprietario dell'API.                                                       | rileva/Impedisci |
| paramName                          | QueryParameter/PathParameter/RequestHeader                  | IncorrectMessage    | La richiesta non può contenere più valori per il parametro {query/parametro/percorso/intestazione} {paramName}.                                           | La richiesta non può contenere più valori per il parametro {query/parametro/percorso/intestazione} {paramName}.                                   | rileva/Impedisci |
| headerName                         | ResponseHeader                                                  | IncorrectMessage    | La risposta non può contenere più valori per l'intestazione {HeaderName}.                                                                              | Impossibile elaborare la richiesta a causa di un errore interno. Contattare il proprietario dell'API.                                                       | rileva/Impedisci |
| paramName                          | QueryParameter/PathParameter/RequestHeader                  | IncorrectMessage    | Il valore del parametro {query/parametro/percorso/intestazione} {paramName} non è conforme alla definizione.<br/><br/>{valError. Message} riga: {valError. LineNumber}, posizione: {valError. LinePosition}                                          | Il valore del parametro {query/parametro/percorso/intestazione} {paramName} non è conforme alla definizione.<br/><br/>{valError. Message} riga: {valError. LineNumber}, posizione: {valError. LinePosition}                              | rileva/Impedisci |
| headerName                         | ResponseHeader                                                  | IncorrectMessage    | Il valore dell'intestazione {headerName} non è conforme alla definizione.<br/><br/>{valError. Message} riga: {valError. LineNumber}, posizione: {valError. LinePosition}                                                                              | Impossibile elaborare la richiesta a causa di un errore interno. Contattare il proprietario dell'API.                                                       | rileva/Impedisci |
| paramName                          | QueryParameter/PathParameter/RequestHeader                  | IncorrectMessage    | Non è possibile analizzare il valore dell'intestazione {parametro/parametro di query} {paramName} in base alla definizione. <br/><br/>ex. Messaggio                                | Non è stato possibile analizzare il valore dell'intestazione {parametro/parametro di query} {paramName} in base alla definizione. <br/><br/>ex. Messaggio                      | rileva/Impedisci |
| headerName                         | ResponseHeader                                                  | IncorrectMessage    | Non è stato possibile analizzare il valore dell'intestazione {headerName} in base alla definizione.                                                                  | Impossibile elaborare la richiesta a causa di un errore interno. Contattare il proprietario dell'API.                                                       | rileva/Impedisci |
| paramName                          | QueryParameter/PathParameter/RequestHeader                  | ValidationError     | {Parametro di query/parametro di percorso/intestazione} non è possibile convalidare {paramName}.<br/><br/>{Dettagli eccezione}                                                                      | Impossibile elaborare la richiesta a causa di un errore interno. Contattare il proprietario dell'API.                                                       | rileva/Impedisci |
| headerName                         | ResponseHeader                                                  | ValidationError     | Non è possibile convalidare l'intestazione {HeaderName}.<br/><br/>{Dettagli eccezione}                                                                                                          | Impossibile elaborare la richiesta a causa di un errore interno. Contattare il proprietario dell'API.                                                       | rileva/Impedisci |
| **Validate-status-code**             |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {status-code}                        | StatusCode                                                      | Non specificata         | Il codice di stato della risposta {status-code} non è consentito.                                                                                                | Impossibile elaborare la richiesta a causa di un errore interno. Contattare il proprietario dell'API.                                                       | rileva/Impedisci |


Nella tabella seguente sono elencati tutti i possibili valori del motivo di un errore di convalida insieme ai possibili valori dei messaggi:

|  **Motivo**         |    **Messaggio** |
|---|---|  
| Richiesta non valida       |     {Details} per la variabile di contesto, {Public Response} per il client|
| Risposta non consentita  | {Details} per la variabile di contesto, {Public Response} per il client |






## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'utilizzo dei criteri, vedere:

-   [Criteri di Gestione API](api-management-howto-policies.md)
-   [Trasformare le API](transform-api.md)
-   [Riferimento ai criteri](./api-management-policies.md) per un elenco completo di istruzioni dei criteri e delle relative impostazioni
-   [Esempi di criteri](./policy-reference.md)
-   [Gestione degli errori](./api-management-error-handling-policies.md)
