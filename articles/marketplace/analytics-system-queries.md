---
title: Elenco di query di sistema
description: Informazioni sulle query di sistema che è possibile usare per ottenere a livello di codice i dati di analisi sulle offerte nel Marketplace commerciale Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: f2b5f7eb559e349947f88067a3d2ea53d99b7cbf
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584009"
---
# <a name="list-of-system-queries"></a>Elenco di query di sistema

È possibile usare le query di sistema seguenti nell' [API crea report](analytics-programmatic-access.md#create-report-api) direttamente con un `QueryId` . Le query di sistema sono simili ai report di esportazione nel centro partner per un periodo di calcolo di sei mesi (6M).

Per ulteriori informazioni sui nomi di colonna, gli attributi e la descrizione, vedere i seguenti articoli:

- [Dashboard dei clienti in analisi del Marketplace commerciale](customer-dashboard.md#customer-details-table)
- [Dashboard degli ordini nell'analisi per il marketplace commerciale](orders-dashboard.md#orders-details-table)
- [Dashboard di utilizzo in analisi del Marketplace commerciale](usage-dashboard.md#usage-details-table)
- [Dashboard di informazioni dettagliate sul marketplace nell'analisi per il marketplace commerciale](insights-dashboard.md#marketplace-insights-details-table)

Nelle sezioni seguenti vengono fornite query di report per vari report.

## <a name="customers-report-query"></a>Query report clienti

**Descrizione report**: report clienti per l'ultimo 6 minuti

**QueryId**:  `b9df4929-073f-4795-b0cb-a2c81b11e28d`

**Query del report**:

`SELECT MarketplaceSubscriptionId,DateAcquired,DateLost,ProviderName,ProviderEmail,FirstName,LastName,Email,CustomerCompanyName,CustomerCity,CustomerPostalCode,CustomerCommunicationCulture,CustomerCountryRegion,AzureLicenseType,PromotionalCustomers,CustomerState,CommerceRootCustomer,CustomerId,BillingAccountId,ID  FROM ISVCustomer TIMESPAN LAST_6_MONTHS`

## <a name="orders-report-query"></a>Query report ordini

**Descrizione report**: Report ordini per l'ultimo 6 m

**QueryId**:  `fd0f299c-5a1c-4929-9f48-bfc6cc44355d`

**Query del report**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,Sku,CustomerCountry,IsPreviewSKU,OrderId,OrderQuantity,CloudInstanceName,IsNewCustomer,OrderStatus,OrderCancelDate,CustomerCompanyName,CustomerName,OrderPurchaseDate,OfferName,TrialEndDate,CustomerId,BillingAccountId FROM ISVOrder TIMESPAN LAST_6_MONTHS`

## <a name="usage-report-queries"></a>Query sui report sull'utilizzo

**Descrizione del report**: report di utilizzo normalizzato della macchina virtuale per l'ultimo 6m

**QueryId**:  `2c6f384b-ad52-4aed-965f-32bfa09b3778`

**Query del report**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,NormalizedUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Descrizione report**: report utilizzo non elaborato VM per l'ultimo 6 m

**QueryId**:  `3f19fb95-5bc4-4ee0-872e-cedd22578512`

**Query del report**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,RawUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Descrizione report**: report sull'utilizzo a consumo per l'ultimo 6m

**QueryId**:  `f0c4927f-1f23-4c99-be4a-1371a5a9a086`

**Query del report**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,MeteredUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('SaaS', 'Azure Applications') TIMESPAN LAST_6_MONTHS`

## <a name="marketplace-insights-report-query"></a>Query del report di Marketplace Insights

**Descrizione del report**: report di Marketplace Insights per l'ultimo 6 m

**QueryId**:  `6fd7624b-aa9f-42df-a61d-67d42fd00e92`

**Query del report**:

`Date,OfferName,ReferralDomain,CountryName,PageVisits,GetItNow,ContactMe,TestDrive,FreeTrial FROM ISVMarketplaceInsights TIMESPAN LAST_6_MONTHS`

## <a name="next-steps"></a>Passaggi successivi

- [API per l'accesso ai dati di analisi del Marketplace commerciale](analytics-available-apis.md)
- [Applicazione di esempio per l'accesso ai dati di analisi del Marketplace commerciale](analytics-sample-application.md)
