---
title: Paradigma di accesso a livello di codice
description: Comprendere il flusso di alto livello del modello di chiamata API per l'analisi a livello di codice. Vengono inoltre analizzate le API per l'accesso ai report di analisi nel Marketplace commerciale Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8e0b94a46e96dd8ba16040e16b421520eb67de19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584089"
---
# <a name="programmatic-access-paradigm"></a>Paradigma di accesso a livello di codice

In questo diagramma viene illustrato il modello di chiamata API utilizzato per creare un nuovo modello di report, pianificare il report personalizzato e recuperare i dati di errore.

[ ![ Viene illustrato il modello di chiamata API utilizzato per creare un nuovo modello di report, pianificare il report personalizzato e recuperare i dati di errore.](./media/analytics-programmatic-access/analytics-high-level-flow.png)](./media/analytics-programmatic-access/analytics-high-level-flow.png#lightbox) 
 ***Figura 1: flusso di alto livello del modello di chiamata API***

In questo elenco sono disponibili ulteriori informazioni sulla figura 1.

1. L'applicazione client può definire lo schema o il modello di report personalizzato chiamando l'API di query per la [creazione di report](#create-report-query-api). In alternativa, è possibile utilizzare un modello di report ( `QueryId` ) dall' [elenco di query di sistema](analytics-system-queries.md).
1. In seguito all'esito positivo, l'API Crea modello di report restituisce l'oggetto `QueryId` .
1. L'applicazione client chiama quindi l' [API di creazione report](#create-report-api) utilizzando `QueryID` insieme alla data di inizio del report, all'intervallo di ripetizione, alla ricorrenza e a un URI di callback facoltativo.
1. In seguito all'esito positivo, l' [API di creazione report](#create-report-api) restituisce l'oggetto `ReportID` .
1. L'applicazione client riceve una notifica all'URI di callback non appena i dati del report sono pronti per il download.
1. L'applicazione client usa quindi l' [API Get report executions](#get-report-executions-api) per eseguire una query sullo stato del report con l' `Report ID` intervallo di date e.
1. In seguito all'esito positivo, viene restituito il collegamento per il download del report e l'applicazione può avviare il download dei dati.

## <a name="report-query-language-specification"></a>Specifica del linguaggio di query del report

Sebbene vengano fornite [query di sistema](analytics-system-queries.md) che è possibile utilizzare per creare report, è anche possibile creare query personalizzate in base alle esigenze aziendali. Per ulteriori informazioni sulle query personalizzate, vedere [specifica della query personalizzata](analytics-custom-query-specification.md).

## <a name="create-report-query-api"></a>Creare un'API di query del report

Questa API consente di creare query personalizzate che definiscono il set di dati da cui devono essere esportate le colonne e le metriche. L'API offre la flessibilità necessaria per creare un nuovo modello di report in base alle esigenze aziendali.

È anche possibile usare le [query di sistema](analytics-system-queries.md) fornite. Quando non sono necessari modelli di report personalizzati, è possibile chiamare l' [API crea report](#create-report-api) direttamente usando l' [QueryIds](analytics-system-queries.md) delle query di sistema fornite.

Nell'esempio seguente viene illustrato come creare una query personalizzata per ottenere l' _utilizzo normalizzato e i costi finanziari stimati per gli SKU a pagamento_ dal set di dati [ISVUsage](analytics-make-your-first-api-call.md#programmatic-api-call) per l'ultimo mese.

*Sintassi della richiesta*

| Metodo | URI richiesta |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries` |
|||

*Intestazione della richiesta*

| Intestazione | Tipo | Descrizione |
| ------------- | ------------- | ------------- |
| Autorizzazione | string | Obbligatorio. Il token di accesso Azure Active Directory (Azure AD). Il formato è `Bearer <token>`. |
| Content-Type | `string` | `application/JSON` |
||||

*Parametro path*

nessuno

*Query parameter (Parametro di query)*

nessuno

*Esempio di payload della richiesta*

```json
{
    "Name": "ISVUsageQuery",
    "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
    "Query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"
}
```

*Glossario*

Questa tabella fornisce le definizioni chiave degli elementi nel payload della richiesta.

| Parametro | Obbligatoria | Descrizione | Valori consentiti |
| ------------ | ------------- | ------------- | ------------- |
| `Name` | Sì | Nome descrittivo della query | stringa |
| `Description` | No | Descrizione dei risultati restituiti dalla query | string |
| `Query` | Sì | Stringa di query del report | Tipo di dati: string<br>[Query personalizzata](analytics-sample-queries.md) in base alle esigenze aziendali |
|||||

> [!NOTE]
> Per esempi di query personalizzati, vedere [esempi di query di esempio](analytics-sample-queries.md).

*Risposta di esempio*

Il payload della risposta è strutturato nel modo seguente:

Codici di risposta: 200, 400, 401, 403, 500

Esempio di payload di risposta:

```json
{
  "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": " ISVUsageQuery",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs”,
            "query": " SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34Z"
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

*Glossario*

Questa tabella fornisce le definizioni chiave degli elementi nella risposta.

| Parametro | Descrizione |
| ------------ | ------------- |
| `QueryId` | UUID (Universally Unique Identifier) della query creata |
| `Name` | Nome descrittivo assegnato alla query nel payload della richiesta |
| `Description` | Descrizione specificata durante la creazione della query |
| `Query` | Query del report passata come input durante la creazione della query |
| `Type` | Impostare su `userDefined` |
| `User` | ID utente utilizzato per creare la query |
| `CreatedTime` | Ora UTC in cui è stata creata la query nel formato: AAAA-MM-GGThh: mm: ssZ |
| `TotalCount` | Numero di set di impostazioni nella matrice di valori |
| `StatusCode` | Codice risultato<br>I valori possibili sono 200, 400, 401, 403, 500 |
| `message` | Messaggio di stato dell'esecuzione dell'API |
|||

## <a name="create-report-api"></a>Crea API report

Durante la creazione di un modello di report personalizzato e la ricezione `QueryID` di come parte della risposta per la creazione di una [query di report](#create-report-query-api) , questa API può essere chiamata per pianificare una query da eseguire a intervalli regolari. È possibile impostare una frequenza e una pianificazione per il report da recapitare. Per le query di sistema fornite, l'API di creazione report può essere chiamata anche con [QueryId](analytics-sample-queries.md).

*Sintassi della richiesta*

| Metodo | URI richiesta |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport` |
|||

*Intestazione della richiesta*

| Intestazione | Tipo | Descrizione |
| ------ | ---- | ----------- |
| Autorizzazione | string | Obbligatorio. Il token di accesso Azure Active Directory (Azure AD). Il formato è `Bearer <token>`. |
| Tipo di contenuto | string | `application/JSON` |
||||

*Parametro path*

nessuno

*Query parameter (Parametro di query)*

nessuno

*Esempio di payload della richiesta*

```json
{
  "ReportName": "ISVUsageReport",
  "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
  "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c ",
  "StartTime": "2021-01-06T19:00:00Z ",
  "RecurrenceInterval": 48,
  "RecurrenceCount": 20,
  "Format": "csv",
  "CallbackUrl": "https://<SampleCallbackUrl>"
}
```

*Glossario*

Questa tabella fornisce le definizioni chiave degli elementi nel payload della richiesta.

| Parametro | Obbligatoria | Descrizione | Valori consentiti |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Sì | Nome assegnato al report | stringa |
| `Description` | No | Descrizione del report creato | string |
| `QueryId` | Sì | ID query report | string |
| `StartTime` | Sì | Timestamp UTC in corrispondenza del quale inizierà la generazione del report.<br>Il formato deve essere: AAAA-MM-GGThh: mm: ssZ | string |
| `RecurrenceInterval` | Sì | Frequenza espressa in ore in cui deve essere generato il report.<br>Il valore minimo è 4 e il valore massimo è 90. | numero intero |
| `RecurrenceCount` | No | Numero di report da generare. | numero intero |
| `Format` | No | Formato del file esportato.<br>Il formato predefinito è. CSV. | CSV/TSV |
| `CallbackUrl` | No | URL raggiungibile pubblicamente che può essere eventualmente configurato come destinazione di callback. | Stringa (URL http) |
| `ExecuteNow` | No | Questo parametro deve essere utilizzato per creare un report che verrà eseguito una sola volta. `StartTime`, `RecurrenceInterval` e `RecurrenceCount` vengono ignorati se è impostato su `true` . Il report viene eseguito immediatamente in modo asincrono. | true/false |
| `QueryStartTime` | No | Facoltativamente specifica l'ora di inizio per la query che estrae i dati. Questo parametro è applicabile solo per un report di esecuzione di una sola volta che ha `ExecuteNow` impostato su `true` . Il formato deve essere aaaa-MM-GGThh: mm: ssZ | Timestamp come stringa |
| `QueryEndTime` | No | Facoltativamente specifica l'ora di fine della query che estrae i dati. Questo parametro è applicabile solo per un report di esecuzione di una sola volta che ha `ExecuteNow` impostato su `true` . Il formato deve essere aaaa-MM-GGThh: mm: ssZ | Timestamp come stringa |
|||||

*Risposta di esempio*

Il payload della risposta è strutturato nel modo seguente:

Codice di risposta: 200, 400, 401, 403, 404, 500

Payload risposta:

```json
{
  "Value": [
    {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVUsageReport",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": "https://<SampleCallbackUrl>",
            "format": "csv"    
    }
  ],
  "TotalCount": 1,
  "Message": "Report created successfully",
  "StatusCode": 200
}
```

*Glossario*

Questa tabella fornisce le definizioni chiave degli elementi nella risposta.

| Parametro | Descrizione |
| ------------ | ------------- |
| `ReportId` | Identificatore univoco universale (UUID) del report creato |
| `ReportName` | Nome assegnato al report nel payload della richiesta |
| `Description` | Descrizione specificata durante la creazione del report |
| `QueryId` | ID query passato al momento della creazione del report |
| `Query` | Testo della query che verrà eseguito per il report |
| `User` | ID utente utilizzato per creare il report |
| `CreatedTime` | Ora UTC in cui il report è stato creato in questo formato: AAAA-MM-GGThh: mm: ssZ |
| `ModifiedTime` | Ora UTC dell'Ultima modifica del report nel formato: AAAA-MM-GGThh: mm: ssZ |
| `StartTime` | Ora UTC in cui l'esecuzione del report verrà avviata nel formato seguente: AAAA-MM-GGThh: mm: ssZ |
| `ReportStatus` | Stato dell'esecuzione del report. I valori possibili sono **Paused**, **Active** e **inactive**. |
| `RecurrenceInterval` | Intervallo di ricorrenza fornito durante la creazione del report |
| `RecurrenceCount` | Numero di ricorrenze fornito durante la creazione del report |
| `CallbackUrl` | URL callback specificato nella richiesta |
| `Format` | Formato dei file di report. I valori possibili sono CSV o TSV. |
| `TotalCount` | Numero di set di impostazioni nella matrice di valori |
| `StatusCode` | Codice risultato<br>I valori possibili sono 200, 400, 401, 403, 500 |
| `message` | Messaggio di stato dell'esecuzione dell'API |
|||

## <a name="get-report-executions-api"></a>Ottenere l'API per le esecuzioni di report

È possibile utilizzare questo metodo per eseguire una query sullo stato di un'esecuzione del report utilizzando l'oggetto `ReportId` ricevuto dall' [API di creazione report](#create-report-api). Il metodo restituisce il collegamento per il download del report se il report è pronto per il download. In caso contrario, il metodo restituisce lo stato. È anche possibile usare questa API per ottenere tutte le esecuzioni che si sono verificate per un determinato report.

> [!IMPORTANT]
> Per questa API sono impostati parametri di query predefiniti per `executionStatus=Completed` e  `getLatestExecution=true` . Quindi, la chiamata dell'API prima della prima esecuzione corretta del report restituirà 404. È possibile ottenere le esecuzioni in sospeso impostando `executionStatus=Pending` .

*Sintassi della richiesta*

| Metodo | URI richiesta |
| ------------ | ------------- |
| Recupero | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/{reportId}?executionId={executionId}&executionStatus={executionStatus}&getLatestExecution={getLatestExecution}` |
|||

*Intestazione della richiesta*

| Intestazione | Tipo | Descrizione |
| ------ | ------ | ------ |
| Autorizzazione | string | Obbligatorio. Il token di accesso Azure Active Directory (Azure AD). Il formato è `Bearer <token>`. |
| Tipo di contenuto | string | `application/json` |
||||

*Parametro path*

nessuno

*Query parameter (Parametro di query)*

| Nome parametro | Obbligatoria | Tipo | Descrizione |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Sì | string | Filtrare per ottenere i dettagli di esecuzione dei soli report con `reportId` specificati in questo argomento. `reportIds`È possibile specificare più elementi separandoli con un punto e virgola ";". |
| `executionId` | No | string | Filtrare per ottenere i dettagli dei soli report con `executionId` specificati in questo argomento. `executionIds`È possibile specificare più elementi separandoli con un punto e virgola ";". |
| `executionStatus` | No | stringa/enum | Filtrare per ottenere i dettagli dei soli report con `executionStatus` specificati in questo argomento.<br>I valori validi sono: `Pending` ,, `Running` `Paused` e `Completed` <br>Il valore predefinito è `Completed`. È possibile specificare più Stati separandoli con un punto e virgola ";". |
| `getLatestExecution` | No | boolean | L'API restituirà i dettagli dell'ultima esecuzione del report.<br>Per impostazione predefinita, il parametro è impostato su `true`. Se si sceglie di passare il valore di questo parametro come `false` , l'API restituirà le ultime istanze di esecuzione di 90 giorni. |
|||||

*Payload della richiesta*

nessuno

*Risposta di esempio*

Il payload della risposta è strutturato nel modo seguente:

Codici di risposta: 200, 400, 401, 403, 404, 500

Esempio di payload di risposta:

```json
{
    "value": [
        {
            "executionId": "a0bd78ad-1a05-40fa-8847-8968b718d00f",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 4,
            "recurrenceCount": 10,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Completed",
            "reportAccessSecureLink": "https://<path to report for download>",
            "reportExpiryTime": null,
            "reportGeneratedTime": "2021-01-13T14:40:46Z"
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

Al termine dell'esecuzione del report, viene visualizzato lo stato di esecuzione `Completed` . È possibile scaricare il report selezionando l'URL in `reportAccessSecureLink` .

*Glossario*

Definizioni chiave degli elementi nella risposta.

| Parametro | Descrizione |
| ------------ | ------------- |
| `ExecutionId` | UUID (Universally Unique Identifier) dell'istanza di esecuzione |
| `ReportId` | ID report associato all'istanza di esecuzione |
| `RecurrenceInterval` | Intervallo di ricorrenza fornito durante la creazione del report |
| `RecurrenceCount` | Numero di ricorrenze fornito durante la creazione del report |
| `CallbackUrl` | URL callback associato all'istanza di esecuzione |
| `Format` | Formato del file generato al termine dell'esecuzione |
| `ExecutionStatus` | Stato dell'istanza di esecuzione del report.<br>I valori validi sono: `Pending` ,, `Running` `Paused` e `Completed` |
| `ReportAccessSecureLink` | Collegamento attraverso il quale è possibile accedere in modo sicuro al report |
| `ReportExpiryTime` | Ora UTC dopo la quale il collegamento al report scadrà nel formato: AAAA-MM-GGThh: mm: ssZ |
| `ReportGeneratedTime` | Ora UTC in cui il report è stato generato in questo formato: AAAA-MM-GGThh: mm: ssZ |
| `TotalCount` | Numero di set di impostazioni nella matrice di valori |
| `StatusCode` | Codice risultato <br>I valori possibili sono 200, 400, 401, 403, 404 e 500 |
| `message` | Messaggio di stato dell'esecuzione dell'API |
|||

## <a name="next-steps"></a>Passaggi successivi
- È possibile provare le API tramite l' [URL dell'API spavalderia](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html)
- Introduzione all'accesso programmatico ai dati di analisi
