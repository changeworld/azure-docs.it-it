---
title: Eseguire la prima chiamata API per accedere ai dati di analisi del Marketplace commerciale
description: Esempi per imparare a usare l'API per accedere ai dati di analisi del Marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 9e5fbdfca80d19f026a014a89ffbf137bacb521c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639569"
---
# <a name="make-your-first-api-call-to-access-commercial-marketplace-analytics-data"></a>Eseguire la prima chiamata API per accedere ai dati di analisi del Marketplace commerciale

Per un elenco delle API per l'accesso ai dati di analisi del Marketplace commerciale, vedere [API per l'accesso ai dati di analisi del Marketplace commerciale](analytics-available-apis.md). Prima di effettuare la prima chiamata all'API, assicurarsi di aver soddisfatto i [prerequisiti](analytics-prerequisites.md) per accedere ai dati di analisi del Marketplace commerciale a livello di codice.

## <a name="token-generation"></a>Generazione di token

Prima di chiamare uno dei metodi, è innanzitutto necessario ottenere un token di accesso Azure Active Directory (Azure AD). È necessario passare il token di accesso Azure AD all'intestazione di autorizzazione di ogni metodo nell'API. Dopo aver ottenuto un token di accesso, è necessario usare 60 minuti prima della scadenza. Dopo la scadenza del token, è possibile aggiornare il token e continuare a usarlo per ulteriori chiamate all'API.

Per generare un token, fare riferimento a una richiesta di esempio riportata di seguito. I tre valori necessari per generare il token sono `clientId` , `clientSecret` e `tenantId` . Il `resource` parametro deve essere impostato su `https://graph.windows.net` .

***Esempio di richiesta***:

```json
curl --location --request POST 'https://login.microsoftonline.com/{TenantId}/oauth2/token' \
--header 'return-client-request-id: true' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'resource=https://graph.windows.net' \
--data-urlencode 'client_id={client_id}' \
--data-urlencode 'client_secret={client_secret}' \
--data-urlencode 'grant_type=client_credentials'
```

***Esempio di risposta***:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1612794445",
    "not_before": "1612790545",
    "resource": "https://graph.windows.net",
    "access_token": {Token}
}
```

Per altre informazioni su come ottenere un token di Azure AD per l'applicazione, vedere [accedere ai dati di analisi usando i servizi di archiviazione](/windows/uwp/monetize/access-analytics-data-using-windows-store-services#step-2-obtain-an-azure-ad-access-token).

## <a name="programmatic-api-call"></a>Chiamata API a livello di codice

Dopo aver ottenuto il token Azure AD come descritto nella sezione precedente, seguire questa procedura per creare il primo report di accesso a livello di codice.

I dati possono essere scaricati dai set di dati seguenti (DataSetName):

- ISVCustomer
- ISVMarketplaceInsights
- ISVUsage
- ISVOrder

Nelle sezioni seguenti vengono illustrati esempi di come accedere a livello di codice `OrderId` dal set di dati ISVOrder.

### <a name="step-1-make-a-rest-call-using-the-get-datasets-api"></a>Passaggio 1: effettuare una chiamata REST usando l'API Get DataSets

La risposta dell'API fornisce il nome del set di dati da cui è possibile scaricare il report. Per il set di dati specifico, la risposta dell'API fornisce anche l'elenco delle colonne selezionabili che è possibile usare per il modello di report personalizzato. Per ottenere i nomi delle colonne, è inoltre possibile fare riferimento alle seguenti tabelle:

- [Tabella Dettagli ordini](orders-dashboard.md#orders-details-table)
- [Tabella dettagli utilizzo](usage-dashboard.md#usage-details-table)
- [Tabella Dettagli cliente](customer-dashboard.md#customer-details-table)
- [Tabella dettagli di Marketplace Insights](insights-dashboard.md#marketplace-insights-details-table)

***Esempio di richiesta***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset ' \ 
--header 'Authorization: Bearer <AzureADToken>'
```

***Esempio di risposta***:

```json
{
    "value": [
        {
            "datasetName": "ISVOrder",
            "selectableColumns": [
                "MarketplaceSubscriptionId",
                "MonthStartDate",
                "OfferType",
                "AzureLicenseType",
                "Sku",
                "CustomerCountry",
                "IsPreviewSKU",
                "OrderId",
                "OrderQuantity",
                "CloudInstanceName",
                "IsNewCustomer",
                "OrderStatus",
                "OrderCancelDate",
                "CustomerCompanyName",
                "CustomerName",
                "OrderPurchaseDate",
                "OfferName",
                "TrialEndDate",
                "CustomerId",
                "BillingAccountId"
            ],
            "availableMetrics": [],
            "availableDateRanges": [
                "LAST_MONTH",
                "LAST_3_MONTHS",
                "LAST_6_MONTHS",
                "LIFETIME"
            ]
        },
    ],
    "totalCount": 1,
    "message": "Dataset fetched successfully",
    "statusCode": 200
}
```

### <a name="step-2-create-the-custom-query"></a>Passaggio 2: creare la query personalizzata

In questo passaggio verrà usato l'ID ordine del report Orders per creare una query personalizzata per il report desiderato. Il valore predefinito `timespan` se non è specificato nella query è sei mesi.

***Esempio di richiesta***:

```json
curl 
--location 
--request POST ' https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries' \ 
--header ' Authorization: Bearer <AzureAD_Token>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
            '{ 
                "Query": "SELECT OrderId from ISVOrder", 
                "Name": "ISVOrderQuery1", 
                "Description": "Get a list of all Order IDs" 
             }'
```

***Esempio di risposta***:

```json
{
    "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": "ISVOrderQuery1",
            "description": "Get a list of all Order IDs”,
            "query": "SELECT OrderId from ISVOrder",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34",
            "modifiedTime": null
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

Al completamento dell'esecuzione della query, `queryId` viene generato un oggetto che deve essere utilizzato per generare il report.

### <a name="step-3-execute-test-query-api"></a>Passaggio 3: eseguire l'API di query di test

In questo passaggio si userà l'API di query di test per ottenere le prime 100 righe per la query creata.

***Esempio di richiesta***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery=SELECT%20OrderId%20from%20ISVOrder' \ 
--header ' Authorization: Bearer <AzureADToken>'
```

***Esempio di risposta***:

```json
{
    "value": [
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2ba8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bb8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bc8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bd8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2be8"
        },
               .
               .
               .

        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf0"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf1"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf2"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf3"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf4"
        }
    ],
    "totalCount": 100,
    "message": null,
    "statusCode": 200
}
```

### <a name="step-4-create-the-report"></a>Passaggio 4: creare il report

In questo passaggio verrà usato il generato in precedenza `QueryId` per creare il report.

***Esempio di richiesta***:

```json
curl 
--location 
--request POST 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport' \ 
--header ' Authorization: Bearer <AzureADToken>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
                 '{
                   “ReportName": "ISVReport1",
                   "Description": "Report for getting list of Order Ids",
                   "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
                   "StartTime": "2021-01-06T19:00:00Z”,
                   "RecurrenceInterval": 48,
                   "RecurrenceCount": 20,
                    "Format": "csv"
                  }'
```

<br>

_**Tabella 1: Descrizione dei parametri usati in questo esempio di richiesta**_

| Parametro | Descrizione |
| ------------ | ------------- |
| `Description` | Fornire una breve descrizione del report in fase di generazione. |
| `QueryId` | Si tratta dell'oggetto `queryId` che è stato generato quando la query è stata creata nel passaggio 2: creare una query personalizzata. |
| `StartTime` | Ora di inizio della prima esecuzione del report. |
| `RecurrenceInterval` | Intervallo di ricorrenza fornito durante la creazione del report. |
| `RecurrenceCount` | Numero di ricorrenze fornito durante la creazione del report. |
| `Format` | Sono supportati i formati di file CSV e TSV. |
|||

***Esempio di risposta***:

```json
{
    "value": [
        {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVReport1",
            "description": "Report for getting list of Order Ids",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT OrderId from ISVOrder",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv"
        }
    ],
    "totalCount": 1,
    "message": "Report created successfully",
    "statusCode": 200
}
```

Al completamento dell'esecuzione, `reportId` viene generato un oggetto che deve essere utilizzato per pianificare un download del report.

### <a name="step-5-execute-report-executions-api"></a>Passaggio 5: eseguire l'API per l'esecuzione di report

Per ottenere il percorso sicuro (URL) del report, verrà ora eseguita l'API di esecuzione del report.

***Esempio di richiesta***:

```json
Curl
--location
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/72fa95ab-35f5-4d44-a1ee-503abbc88003' \
--header ' Authorization: Bearer <AzureADToken>' \
```

***Esempio di risposta***:

```json
{
    "value": [
        {
            "executionId": "1f18b53b-df30-4d98-85ee-e6c7e687aeed",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Pending",
            "reportAccessSecureLink": null,
            "reportExpiryTime": null,
            "reportGeneratedTime": null
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

## <a name="next-steps"></a>Passaggi successivi

- È possibile provare le API tramite l' [URL dell'API spavalderia](https://swagger.io/docs/specification/api-host-and-base-path/)
- [Paradigma di accesso a livello di codice](analytics-programmatic-access.md)
