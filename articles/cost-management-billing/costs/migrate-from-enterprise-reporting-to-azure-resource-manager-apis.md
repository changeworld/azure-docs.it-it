---
title: Eseguire la migrazione dalle API di creazione report Enteprise a quelle di Azure Resource Manager
description: Questo articolo illustra le differenze tra le API di creazione report e le API di Azure Resource Manager, i possibili effetti della migrazione alle API di Azure Resource Manager e le nuove funzionalità disponibili con le nuove API di Azure Resource Manager.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 11/19/2020
ms.author: banders
ms.openlocfilehash: 93dda4fc3a152b0a07a95ff327c9ea619f25787c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355821"
---
# <a name="migrate-from-enterprise-reporting-to-azure-resource-manager-apis"></a>Eseguire la migrazione dalle API di creazione report Enteprise a quelle di Azure Resource Manager

Questo articolo aiuta gli sviluppatori che hanno creato soluzioni personalizzate con le [API di creazione report di Azure per clienti Enterprise](../manage/enterprise-api.md) a eseguire la migrazione alle API di Azure Resource Manager per Gestione costi. Il supporto di entità servizio per le nuove API di Azure Resource Manager è ora disponibile a livello generale. Le API di Azure Resource Manager sono in fase di sviluppo attivo. È consigliabile eseguire la migrazione a tali API invece di usare le precedenti API di creazione report di Azure per clienti Enterprise. Le API meno recenti verranno deprecate. Questo articolo illustra le differenze tra le API di creazione report e le API di Azure Resource Manager, i possibili effetti della migrazione alle API di Azure Resource Manager e le nuove funzionalità disponibili con le nuove API di Azure Resource Manager.

## <a name="api-differences"></a>Differenze tra le API

Di seguito vengono descritte le differenze tra le precedenti API di creazione report per clienti Enterprise e le nuove API di Azure Resource Manager.

| **Uso** | **API del contratto Enterprise** | **API di Azure Resource Manager** |
| --- | --- | --- |
| Autenticazione | Provisioning delle chiavi API effettuato in EA (Enterprise Agreement) Portal | Autenticazione di Azure Active Directory (Azure AD) tramite token utente o entità servizio. Le entità servizio prendono il posto delle chiavi API. |
| Ambiti e autorizzazioni | Tutte le richieste rientrano nell'ambito della registrazione. Le assegnazioni di autorizzazioni con chiave API determineranno se vengono restituiti i dati per l'intera registrazione, per un reparto o per uno specifico account. Nessuna autenticazione utente. | Agli utenti o alle entità servizio viene assegnato l'accesso all'ambito di registrazione, reparto o account. |
| Endpoint URI | https://consumption.azure.com | https://management.azure.com |
| Stato di sviluppo | In modalità di manutenzione. In corso di deprecazione. | In fase di sviluppo attivo |
| API disponibili | Solo quelle attualmente disponibili | Sono disponibili API equivalenti per sostituire qualsiasi API EA. <p> Sono inoltre disponibili [API di Gestione costi](/rest/api/cost-management/) aggiuntive, tra cui: <p> <ul><li>Budget</li><li>Avvisi<li>Esportazioni</li></ul> |

## <a name="migration-checklist"></a>Elenco di controllo per la migrazione

- Acquisire familiarità con le [API REST di Azure Resource Manager](/rest/api/azure).
- Determinare quali API EA si usano e vedere il [mapping di API EA con le nuove API di Azure Resource Manager](#ea-api-mapping-to-new-azure-resource-manager-apis) per verificare a quali API di Azure Resource Manager passare.
- Configurare l'autorizzazione e l'autenticazione del servizio per le API di Azure Resource Manager
  - Se non si usano ancora le API di Azure Resource Manager, [registrare l'app client con Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad). La registrazione crea automaticamente un'entità servizio da usare per chiamare le API.
  - Assegnare all'entità servizio l'accesso agli ambiti necessari, come descritto di seguito.
  - Aggiornare il codice di programmazione per l'uso dell'[autenticazione di Azure AD](/rest/api/azure/#create-the-request) con l'entità servizio.
- Testare le API e quindi aggiornare il codice di programmazione per sostituire le chiamate alle API EA con le chiamate alle API di Azure Resource Manager.
- Aggiornare la gestione degli errori per l'uso di nuovi codici errore. Alcune considerazioni di cui tenere conto:
  - Le API di Azure Resource Manager prevedono un periodo di timeout di 60 secondi.
  - Le API di Azure Resource Manager presentano un limite di frequenza. Se la frequenza viene superata, viene generato un errore 429 per numero eccessivo di richieste. Sviluppare le soluzioni in modo da non effettuare troppe chiamate alle API in un breve periodo di tempo.
- Esaminare le altre API di Gestione costi disponibili tramite Azure Resource Manager e valutarle per un uso successivo. Per altre informazioni, vedere [Usare altre API di Gestione costi](#use-additional-cost-management-apis).

## <a name="assign-service-principal-access-to-azure-resource-manager-apis"></a>Assegnare all'entità servizio l'accesso alle API di Azure Resource Manager

Dopo aver creato un'entità servizio per chiamare a livello di codice le API di Azure Resource Manager, è necessario assegnarle le autorizzazioni appropriate per l'esecuzione di richieste in Azure Resource Manager. Sono disponibili due framework di autorizzazioni per scenari diversi.

### <a name="azure-billing-hierarchy-access"></a>Accesso alla gerarchia di fatturazione di Azure

Per assegnare all'entità servizio le autorizzazioni per gli ambiti di account di fatturazione aziendale, reparti o registrazione, usare le API di [autorizzazioni di fatturazione](/rest/api/billing/2019-10-01-preview/billingpermissions), [definizioni di ruoli di fatturazione](/rest/api/billing/2019-10-01-preview/billingroledefinitions) e [assegnazioni di ruoli di fatturazione](/rest/api/billing/2019-10-01-preview/billingroleassignments).

- Usare le API di autorizzazioni di fatturazione per identificare le autorizzazioni che l'entità servizio ha già per un ambito specifico, ad esempio un account di fatturazione o un reparto.
- Usare le API di definizioni di ruoli di fatturazione per enumerare i ruoli disponibili che è possibile assegnare all'entità servizio.
  - Al momento, alle entità servizio è possibile assegnare solo i ruoli di amministratore EA di sola lettura e amministratore di reparto di sola lettura.
- Usare le API di assegnazioni di ruoli di fatturazione per assegnare un ruolo all'entità servizio.

L'esempio seguente mostra come chiamare l'API di assegnazioni di ruoli per concedere a un'entità servizio l'accesso all'account di fatturazione. È consigliabile usare [PostMan](https://postman.com) per eseguire queste configurazioni delle autorizzazioni una tantum.

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/createBillingRoleAssignment?api-version=2019-10-01-preview
```

#### <a name="request-body"></a>Request Body

```json
{
  "principalId": "00000000-0000-0000-0000-000000000000",
  "billingRoleDefinitionId": "/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.Billing/billingRoleDefinition/10000000-aaaa-bbbb-cccc-100000000000"
}

```

### <a name="azure-role-based-access-control"></a>Controllo degli accessi in base al ruolo di Azure

La nuova entità servizio supporta l'estensione ad ambiti specifici di Azure, come i gruppi di gestione, le sottoscrizioni e i gruppi di risorse. È possibile assegnare le autorizzazioni dell'entità servizio direttamente a questi ambiti [nel portale di Azure](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) o tramite [Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#assign-the-application-to-a-role).

## <a name="ea-api-mapping-to-new-azure-resource-manager-apis"></a>Mapping delle API EA con le nuove API di Azure Resource Manager

Usare la tabella seguente per identificare le API EA attualmente in uso e le API di Azure Resource Manager da usare in sostituzione.

| **Scenario** | **API EA** | **API di Azure Resource Manager** |
| --- | --- | --- |
| Riepilogo saldi | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) |[Microsoft.Consumption/balances](/rest/api/consumption/balances/getbybillingaccount) |
| Elenco prezzi | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | [Microsoft.Consumption/pricesheets/default](/rest/api/consumption/pricesheet) - da usare per i prezzi negoziati <p> [API di prezzi al dettaglio](/rest/api/cost-management/retail-prices/azure-retail-prices) - da usare per i prezzi al dettaglio |
| Dettagli dell'istanza riservata | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.CostManagement/generateReservationDetailsReport](/rest/api/cost-management/generatereservationdetailsreport) |
| Riepilogo delle istanze riservate | [/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid) |
| Raccomandazioni per le istanze riservate | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)<p>[/SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Addebiti per le istanze riservate | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | [Microsoft.Consumption/reservationTransactions](/rest/api/consumption/reservationtransactions/list) |

## <a name="migration-details-by-api"></a>Dettagli della migrazione per API

Le sezioni seguenti mostrano esempi di richieste di API precedenti con esempi delle nuove API sostitutive.

### <a name="balance-summary-api"></a>API di riepilogo saldi

Usare gli URI di richieste seguenti per chiamare la nuova API di riepilogo saldi. Per billingAccountID è necessario usare il proprio numero di registrazione.

#### <a name="supported-requests"></a>Richieste supportate

[Ottenere i saldi per la registrazione](/rest/api/consumption/balances/getbybillingaccount)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/balances?api-version=2019-10-01
```

### <a name="response-body-changes"></a>Modifiche del corpo della risposta

_Vecchio corpo della risposta_:

```json
{
        "id": "enrollments/100/billingperiods/201507/balancesummaries",
          "billingPeriodId": 201507,
          "currencyCode": "USD",
          "beginningBalance": 0,
          "endingBalance": 1.1,
          "newPurchases": 1,
          "adjustments": 1.1,
          "utilized": 1.1,
          "serviceOverage": 1,
          "chargesBilledSeparately": 1,
          "totalOverage": 1,
          "totalUsage": 1.1,
          "azureMarketplaceServiceCharges": 1,
          "newPurchasesDetails": [
            {
              "name": "",
              "value": 1
            }
          ],
          "adjustmentDetails": [
            {
              "name": "Promo Credit",
              "value": 1.1
            },
            {
              "name": "SIE Credit",
              "value": 1.0
            }
          ]
    }

```

_Nuovo corpo della risposta_:

Gli stessi dati sono ora disponibili nel campo `properties` della risposta dell'API. Potrebbero essere presenti modifiche secondarie in alcuni nomi dei campi.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/balances/balanceId1",
  "name": "balanceId1",
  "type": "Microsoft.Consumption/balances",
  "properties": {
    "currency": "USD  ",
    "beginningBalance": 3396469.19,
    "endingBalance": 2922371.02,
    "newPurchases": 0,
    "adjustments": 0,
    "utilized": 474098.17,
    "serviceOverage": 0,
    "chargesBilledSeparately": 0,
    "totalOverage": 0,
    "totalUsage": 474098.17,
    "azureMarketplaceServiceCharges": 609.82,
    "billingFrequency": "Month",
    "priceHidden": false,
    "newPurchasesDetails": [
      {
        "name": "Promo Purchase",
        "value": 1
      }
    ],
    "adjustmentDetails": [
      {
        "name": "Promo Credit",
        "value": 1.1
      },
      {
        "name": "SIE Credit",
        "value": 1
      }
    ]
  }
}

```

### <a name="price-sheet"></a>Elenco prezzi

Usare gli URI di richiesta seguenti per chiamare la nuova API di elenco prezzi.

#### <a name="supported-requests"></a>Richieste supportate

 È possibile chiamare l'API usando gli ambiti seguenti:

- Registrazione: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Sottoscrizione: `subscriptions/{subscriptionId}`

[_Ottenere l'elenco prezzi per il periodo di fatturazione corrente_](/rest/api/consumption/pricesheet/get)


```json
https://management.azure.com/{scope}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

[_Ottenere l'elenco prezzi per il periodo di fatturazione specificato_](/rest/api/consumption/pricesheet/getbybillingperiod)

```json
https://management.azure.com/{scope}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Modifiche del corpo della risposta

_Vecchia risposta_:

```json
      [
        {
              "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
              "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
              "meterName": "A1 VM",
              "unitOfMeasure": "100 Hours",
              "includedQuantity": 0,
              "partNumber": "N7H-00015",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        {
              "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
              "billingPeriodId": "201404",
            "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
              "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
              "unitOfMeasure": "100 GB",
              "includedQuantity": 0,
              "partNumber": "N9H-00402",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        ...
    ]

```

_Nuova risposta_:

I dati precedenti sono ora disponibili nel campo `pricesheets` della risposta dell'API. Vengono anche forniti dettagli sul contatore.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/pricesheets/default",
  "name": "default",
  "type": "Microsoft.Consumption/pricesheets",
  "properties": {
    "nextLink": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/microsoft.consumption/pricesheets/default?api-version=2018-01-31&$skiptoken=AQAAAA%3D%3D&$expand=properties/pricesheets/meterDetails",
    "pricesheets": [
      {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "meterId": "00000000-0000-0000-0000-000000000000",
        "unitOfMeasure": "100 Hours",
        "includedQuantity": 100,
        "partNumber": "XX-11110",
        "unitPrice": 0.00000,
        "currencyCode": "EUR",
        "offerId": "OfferId 1",
        "meterDetails": {
          "meterName": "Data Transfer Out (GB)",
          "meterCategory": "Networking",
          "unit": "GB",
          "meterLocation": "Zone 2",
          "totalIncludedQuantity": 0,
          "pretaxStandardRate": 0.000
        }
      }
    ]
  }
}

```

### <a name="reserved-instance-usage-details"></a>Dettagli sull'utilizzo di istanze riservate

Microsoft non si occupa attivamente delle API di dettagli delle prenotazioni di tipo sincrono. È consigliabile passare al nuovo modello di chiamata API asincrona supportato da SPN come parte della migrazione. Le richieste asincrone gestiscono in modo più efficace grandi quantità di dati e riducono gli errori di timeout.

#### <a name="supported-requests"></a>Richieste supportate

Usare gli URI di richieste seguenti per chiamare le nuove API di dettagli delle prenotazioni asincrone. Per `billingAccountId` è necessario usare il proprio numero di registrazione. È possibile chiamare l'API usando gli ambiti seguenti:

- Registrazione: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

#### <a name="sample-request-to-generate-a-reservation-details-report"></a>Richiesta di esempio per generare un report di dettagli delle prenotazioni

```json
POST 
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/generateReservationDetailsReport?startDate={startDate}&endDate={endDate}&api-version=2019-11-01

```

#### <a name="sample-request-to-poll-report-generation-status"></a>Richiesta di esempio per eseguire il polling sullo stato di generazione dei report

```json
GET
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/reservationDetailsOperationResults/{operationId}?api-version=2019-11-01

```

#### <a name="sample-poll-response"></a>Risposta del polling di esempio

```json
{
  "status": "Completed",
  "properties": {
    "reportUrl": "https://storage.blob.core.windows.net/details/20200911/00000000-0000-0000-0000-000000000000?sv=2016-05-31&sr=b&sig=jep8HT2aphfUkyERRZa5LRfd9RPzjXbzB%2F9TNiQ",
    "validUntil": "2020-09-12T02:56:55.5021869Z"
  }
}

```

#### <a name="response-body-changes"></a>Modifiche del corpo della risposta

Di seguito è riportata la risposta della precedente API di dettagli delle prenotazioni di tipo sincrono.

_Vecchia risposta_:

```json
{
    "reservationOrderId": "00000000-0000-0000-0000-000000000000",
    "reservationId": "00000000-0000-0000-0000-000000000000",
    "usageDate": "2018-02-01T00:00:00",
    "skuName": "Standard_F2s",
    "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/resourvegroup1/providers/microsoft.compute/virtualmachines/VM1",
    "totalReservedQuantity": 18.000000000000000,
    "reservedHours": 432.000000000000000,
    "usedHours": 400.000000000000000
}

```

_Nuova risposta_:

La nuova API crea automaticamente un file CSV. Vedere i campi del file seguenti.

| **Vecchia proprietà** | **Nuova proprietà** | **Note** |
| --- | --- | --- |
|  | InstanceFlexibilityGroup | Nuova proprietà per la flessibilità delle istanze. |
|  | InstanceFlexibilityRatio | Nuova proprietà per la flessibilità delle istanze. |
| instanceId | InstanceName |  |
|  | Tipo | Nuova proprietà. Il valore è `None`, `Reservation` o `IncludedQuantity`. |
| reservationId | ReservationId |  |
| reservationOrderId | ReservationOrderId |  |
| reservedHours | ReservedHours |  |
| skuName | SkuName |  |
| totalReservedQuantity | TotalReservedQuantity |  |
| usageDate | UsageDate |  |
| usedHours | UsedHours |  |

### <a name="reserved-instance-usage-summary"></a>Riepilogo dell'utilizzo di istanze riservate

Usare gli URI di richieste seguenti per chiamare la nuova API di riepilogo delle prenotazioni.

#### <a name="supported-requests"></a>Richieste supportate

 È possibile chiamare l'API usando gli ambiti seguenti:

- Registrazione: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

[_Ottenere il riepilogo giornaliero delle prenotazioni_](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

[_Ottenere il riepilogo mensile delle prenotazioni_](/rest/api/consumption/reservationssummaries/list#reservationsummariesmonthlywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Modifiche del corpo della risposta

_Vecchia risposta_:

```json
[
     {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_F1s",
        "reservedHours": 24,
        "usageDate": "2018-05-01T00:00:00",
        "usedHours": 23,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 95.83,
        "maxUtilizationPercentage": 100
    }
]

```

_Nuova risposta_:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/12345/providers/Microsoft.Consumption/reservationSummaries/reservationSummaries_Id1",
      "name": "reservationSummaries_Id1",
      "type": "Microsoft.Consumption/reservationSummaries",
      "tags": null,
      "properties": {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_B1s",
        "reservedHours": 720,
        "usageDate": "2018-09-01T00:00:00-07:00",
        "usedHours": 0,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 0,
        "maxUtilizationPercentage": 0
      }
    }
  ]
}

```

### <a name="reserved-instance-recommendations"></a>Raccomandazioni per le istanze riservate

Usare gli URI di richieste seguenti per chiamare la nuova API di raccomandazioni per le prenotazioni.

#### <a name="supported-requests"></a>Richieste supportate

 È possibile chiamare l'API usando gli ambiti seguenti:

- Registrazione: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Sottoscrizione: `subscriptions/{subscriptionId}`
- Gruppi di risorse: `subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`

[_Ottenere raccomandazioni_](/rest/api/consumption/reservationrecommendations/list)

Tramite questa API sono disponibili sia le raccomandazioni per ambito singolo che per ambiti condivisi. È anche possibile filtrare in base all'ambito come parametro facoltativo dell'API.

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Modifiche del corpo della risposta

Le raccomandazioni per ambiti condivisi o singoli sono raggruppate in un'unica API.

_Vecchia risposta_:

```json
[{
    "subscriptionId": "1111111-1111-1111-1111-111111111111",
    "lookBackPeriod": "Last7Days",
    "meterId": "2e3c2132-1398-43d2-ad45-1d77f6574933",
    "skuName": "Standard_DS1_v2",
    "term": "P1Y",
    "region": "westus",
    "costWithNoRI": 186.27634908960002,
    "recommendedQuantity": 9,
    "totalCostWithRI": 143.12931642978083,
    "netSavings": 43.147032659819189,
    "firstUsageDate": "2018-02-19T00:00:00"
}
]

```

_Nuova risposta_:

```json
{
  "value": [
    {
      "id": "billingAccount/123456/providers/Microsoft.Consumption/reservationRecommendations/00000000-0000-0000-0000-000000000000",
      "name": "00000000-0000-0000-0000-000000000000",
      "type": "Microsoft.Consumption/reservationRecommendations",
      "location": "westus",
      "sku": "Standard_DS1_v2",
      "kind": "legacy",
      "properties": {
        "meterId": "00000000-0000-0000-0000-000000000000",
        "term": "P1Y",
        "costWithNoReservedInstances": 12.0785105,
        "recommendedQuantity": 1,
        "totalCostWithReservedInstances": 11.4899644807748,
        "netSavings": 0.588546019225182,
        "firstUsageDate": "2019-07-07T00:00:00-07:00",
        "scope": "Shared",
        "lookBackPeriod": "Last7Days",
        "instanceFlexibilityRatio": 1,
        "instanceFlexibilityGroup": "DSv2 Series",
        "normalizedSize": "Standard_DS1_v2",
        "recommendedQuantityNormalized": 1,
        "skuProperties": [
          {
            "name": "Cores",
            "value": "1"
          },
          {
            "name": "Ram",
            "value": "1"
          }
        ]
      }
    },
   ]
}

```

### <a name="reserved-instance-charges"></a>Addebiti per le istanze riservate

Usare gli URI di richieste seguenti per chiamare la nuova API di addebiti per le istanze riservate.

#### <a name="supported-requests"></a>Richieste supportate

[_Ottenere gli addebiti delle prenotazioni per intervallo di date_](/rest/api/consumption/reservationtransactions/list)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/reservationTransactions?$filter=properties/eventDate+ge+2020-05-20+AND+properties/eventDate+le+2020-05-30&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Modifiche del corpo della risposta

_Vecchia risposta_:

```json
[
    {
        "purchasingEnrollment": "string",
        "armSkuName": "Standard_F1s",
        "term": "P1Y",
        "region": "eastus",
        "PurchasingsubscriptionGuid": "00000000-0000-0000-0000-000000000000",
        "PurchasingsubscriptionName": "string",
        "accountName": "string",
        "accountOwnerEmail": "string",
        "departmentName": "string",
        "costCenter": "",
        "currentEnrollment": "string",
        "eventDate": "string",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_F1s eastus 1 Year",
        "eventType": "Purchase",
        "quantity": int,
        "amount": double,
        "currency": "string",
        "reservationOrderName": "string"
    }
]

```
_Nuova risposta_:

```json
{
  "value": [
    {
      "id": "/billingAccounts/123456/providers/Microsoft.Consumption/reservationtransactions/201909091919",
      "name": "201909091919",
      "type": "Microsoft.Consumption/reservationTransactions",
      "tags": {},
      "properties": {
        "eventDate": "2019-09-09T19:19:04Z",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_DS1_v2 westus 1 Year",
        "eventType": "Cancel",
        "quantity": 1,
        "amount": -21,
        "currency": "USD",
        "reservationOrderName": "Transaction-DS1_v2",
        "purchasingEnrollment": "123456",
        "armSkuName": "Standard_DS1_v2",
        "term": "P1Y",
        "region": "westus",
        "purchasingSubscriptionGuid": "11111111-1111-1111-1111-11111111111",
        "purchasingSubscriptionName": "Infrastructure Subscription",
        "accountName": "Microsoft Infrastructure",
        "accountOwnerEmail": "admin@microsoft.com",
        "departmentName": "Unassigned",
        "costCenter": "",
        "currentEnrollment": "123456",
        "billingFrequency": "recurring"
      }
    },
  ]
}

```

## <a name="use-additional-cost-management-apis"></a>Usare le API aggiuntive di Gestione costi

Dopo aver eseguito la migrazione alle API di Azure Resource Manager per gli scenari di creazione report esistenti, è possibile usare anche molte altre API. Le API sono anche disponibili tramite Azure Resource Manager e possono essere automatizzate con l'autenticazione basata su entità servizio. Ecco un breve riepilogo delle nuove funzionalità disponibili.

- [Budget](/rest/api/consumption/budgets/createorupdate): per impostare soglie in modo da monitorare proattivamente i costi, avvisare gli stakeholder di rilievo e automatizzare le azioni in risposta alle violazioni delle soglie.

- [Avvisi](/rest/api/cost-management/alerts): per visualizzare informazioni sugli avvisi, tra cui, senza limitazioni, avvisi su budget, su fatture, su crediti e su quote.

- [Esportazioni](/rest/api/cost-management/exports): per pianificare un'esportazione ricorrente dei dati degli addebiti in un account di archiviazione di Azure a scelta. Si tratta della soluzione consigliata per i clienti con una presenza importante di Azure che vogliono analizzare i loro dati nei propri sistemi interni.

## <a name="next-steps"></a>Passaggi successivi

- Acquisire familiarità con le [API REST di Azure Resource Manager](/rest/api/azure).
- Se necessario, determinare quali API EA si usano e vedere il [mapping di API EA con le nuove API di Azure Resource Manager](#ea-api-mapping-to-new-azure-resource-manager-apis) per verificare a quali API di Azure Resource Manager passare.
- Se non si usano ancora le API di Azure Resource Manager, [registrare l'app client con Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Se necessario, aggiornare il codice di programmazione per l'uso dell'[autenticazione di Azure AD](/rest/api/azure/#create-the-request) con l'entità servizio.