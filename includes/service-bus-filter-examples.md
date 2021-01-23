---
title: File di inclusione
description: File di inclusione
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 01/22/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ddc0234accd9f434aad850d2404e2f3001c4bae
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742685"
---
## <a name="examples"></a>Esempi

### <a name="filter-on-system-properties"></a>Filtrare le proprietà di sistema
Per fare riferimento a una proprietà di sistema in un filtro, utilizzare il formato seguente: `sys.<system-property-name>` . 

```csharp
sys.Label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

### <a name="filter-on-message-properties"></a>Filtrare le proprietà del messaggio
Ecco gli esempi di utilizzo delle proprietà dei messaggi in un filtro. È possibile accedere alle proprietà del messaggio usando `user.property-name` o semplicemente `property-name` .

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

### <a name="filter-on-message-properties-with-special-characters"></a>Filtrare le proprietà dei messaggi con caratteri speciali
Se il nome della proprietà del messaggio contiene caratteri speciali, utilizzare le virgolette doppie ( `"` ) per racchiudere il nome della proprietà. Se, ad esempio, il nome della proprietà è `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` , usare la sintassi seguente nel filtro. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

### <a name="filter-on-message-properties-with-numeric-values"></a>Filtrare le proprietà del messaggio con valori numerici
Negli esempi seguenti viene illustrato come è possibile utilizzare le proprietà con valori numerici nei filtri. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

### <a name="parameter-based-filters"></a>Filtri basati su parametri
Di seguito sono riportati alcuni esempi di utilizzo di filtri basati su parametri. In questi esempi, `DataTimeMp` è una proprietà del messaggio di tipo `DateTime` e `@dtParam` è un parametro passato al filtro come `DateTime` oggetto.

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

### <a name="using-in-and-not-in"></a>Uso di IN e NOT IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Per un esempio C#, vedere l' [argomento relativo ai filtri di esempio su GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


### <a name="set-rule-action-for-a-sql-filter"></a>Imposta azione regola per un filtro SQL

```csharp
// instantiate the ManagementClient
this.mgmtClient = new ManagementClient(connectionString);

// create the SQL filter
var sqlFilter = new SqlFilter("source = @stringParam");

// assign value for the parameter
sqlFilter.Parameters.Add("@stringParam", "orders");

// instantiate the Rule = Filter + Action
var filterActionRule = new RuleDescription
{
    Name = "filterActionRule",
    Filter = sqlFilter,
    Action = new SqlRuleAction("SET source='routedOrders'")
};

// create the rule on Service Bus
await this.mgmtClient.CreateRuleAsync(topicName, subscriptionName, filterActionRule);
```

### <a name="correlation-filter-using-correlationid"></a>Filtro di correlazione con CorrelationID

```csharp
new CorrelationFilter("Contoso");
```

Filtra i messaggi con `CorrelationID` impostato su `Contoso` . 

### <a name="correlation-filter-using-system-and-user-properties"></a>Filtro di correlazione con proprietà di sistema e utente

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

Equivale a: `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`

