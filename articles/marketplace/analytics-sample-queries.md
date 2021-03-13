---
title: Query di esempio per l'analisi a livello di codice
description: Usare queste query di esempio per accedere ai dati di analisi di Microsoft Commercial Marketplace a livello di codice.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7d788448fb3f8a849f79e43fcb0737898f4c9e15
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584012"
---
# <a name="sample-queries-for-programmatic-analytics"></a>Query di esempio per l'analisi a livello di codice

In questo articolo vengono fornite query di esempio per gli ordini, l'utilizzo e i report dei clienti di Microsoft Commercial Marketplace. È possibile utilizzare queste query chiamando l'endpoint dell'API di [query di creazione report](analytics-programmatic-access.md#create-report-query-api) . Se necessario, è possibile modificare la chiamata all'API per la [creazione di report](analytics-programmatic-access.md#create-report-query-api) per aggiungere altre colonne, modificare il periodo di calcolo e aggiungere condizioni di filtro. I periodi di tempo supportati sono sei mesi (6M), 12 mesi (12M) e un periodo di tempo personalizzato.

Per informazioni dettagliate sui nomi delle colonne, sugli attributi e sulle descrizioni, vedere le tabelle seguenti:

- [Tabella Dettagli cliente](customer-dashboard.md#customer-details-table)
- [Tabella Dettagli ordini](orders-dashboard.md#orders-details-table)
- [Tabella dettagli utilizzo](usage-dashboard.md#usage-details-table)

## <a name="customers-report-queries"></a>Report dei clienti-query

Queste query di esempio si applicano al report Customers.

| **Descrizione query** | **Query di esempio** |
| --- | --- |
| Clienti attivi del partner fino alla data scelta | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 1` |
| Clienti con varianza del partner fino alla data scelta | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 0` |
| Elenco di nuovi clienti da una geografia specifica negli ultimi sei mesi | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE DateAcquired <= ‘2020-06-30’ AND CustomerCountryRegion = ‘United States’` |
|||

## <a name="usage-report-queries"></a>Query sui report sull'utilizzo

Queste query di esempio si applicano al report sull'utilizzo.

| **Descrizione query** | **Query di esempio** |
| --- | --- |
| Utilizzo normalizzato della macchina virtuale (VM) per il tipo di licenza Marketplace "fatturato tramite Azure" per gli ultimi 6M | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Utilizzo non elaborato della macchina virtuale per il tipo di licenza Marketplace "fatturato tramite Azure" per gli ultimi 12 | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_1_YEAR` |
| Utilizzo normalizzato della macchina virtuale per il tipo di licenza Marketplace "Bring your own License" per l'ultimo 6 m | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Utilizzo RAW della macchina virtuale per il tipo di licenza Marketplace "Bring your own License" per l'ultimo 6 m | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| In base alla data di utilizzo, utilizzo normalizzato totale giornaliero e "costi estesi stimati (PC/CC)" per i piani a pagamento per l'ultimo mese | `SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST_MONTH` |
| In base alla data di utilizzo, utilizzo totale giornaliero totale e "costi estesi stimati (PC/CC)" per i piani a pagamento per l'ultimo mese | `SELECT UsageDate, RawUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST\_MONTH` |
| Per un nome di offerta specifico, utilizzo normalizzato della macchina virtuale per il tipo di licenza Marketplace "fatturato tramite Azure" per l'ultimo 6M | `SELECT OfferName, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Per un nome di offerta specifico, utilizzo a consumo per l'ultimo 6M | `SELECT OfferName, MeteredUsage FROM ISVUsage WHERE OfferName = ‘Example Offer Name’ AND OfferType IN (‘SaaS’, ‘Azure Applications’) TIMESPAN LAST_6_MONTHS` |
|||

## <a name="orders-report-queries"></a>Query report ordini

Queste query di esempio si applicano al report Orders.

| **Descrizione query** | **Query di esempio** |
| --- | --- |
| Report ordini per tipo di licenza di Azure come "Enterprise" per l'ultimo 6 m | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Enterprise’ TIMESPAN LAST\_6\_MONTHS` |
| Report ordini per tipo di licenza di Azure come "con pagamento in base al consumo" per l'ultimo 6 m | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Pay as You Go’ TIMESPAN LAST_6_MONTHS` |
| Report degli ordini per un nome di offerta specifico per l'ultimo 6 m | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Report degli ordini per gli ordini attivi per l'ultimo 6 m | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| Report degli ordini per gli ordini annullati per l'ultimo 6 m | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Cancelled’ TIMESPAN LAST_6_MONTHS` |
|||

## <a name="next-steps"></a>Passaggi successivi

- [API per l'accesso ai dati di analisi del Marketplace commerciale](analytics-available-apis.md)
