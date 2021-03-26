---
title: Configurare le condizioni di corrispondenza del set di regole standard/Premium di Azure front door
description: Questo articolo fornisce un elenco delle varie condizioni di corrispondenza disponibili con il set di regole standard/Premium di Azure front door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/24/2021
ms.author: yuajia
ms.openlocfilehash: 039effb885463c1c53085535a6980601be890340
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561518"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Condizioni di corrispondenza del set di regole standard/Premium (anteprima) di Azure front door

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Nel [set di regole](concept-rule-set.md)standard/Premium di Azure front door, una regola è costituita da zero o più condizioni di corrispondenza e da un'azione. Questo articolo fornisce descrizioni dettagliate delle condizioni di corrispondenza che è possibile usare nel set di regole standard/Premium di Azure front door.

La prima parte di una regola è costituita da una o più condizioni di corrispondenza. Una regola può contenere fino a 10 condizioni di corrispondenza. Una condizione di corrispondenza identifica tipi specifici di richieste per cui vengono eseguite le azioni definite. Se si usano più condizioni di corrispondenza, verranno raggruppate insieme tramite la logica AND. Per tutte le condizioni di corrispondenza che supportano più valori, viene utilizzata la logica.

È possibile usare una condizione di corrispondenza per:

* Filtrare le richieste in base a un indirizzo IP, un paese o un'area specifica.
* Filtrare le richieste in base alle informazioni dell'intestazione.
* Filtrare le richieste provenienti da dispositivi mobili o desktop.
* Filtrare le richieste dal nome del file di richiesta e dall'estensione del file.
* Filtrare le richieste da URL richiesta, protocollo, percorso, stringa di query, argomenti post e così via.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="device-type"></a><a name="IsDevice"></a> Tipo di dispositivo

Usare la condizione di corrispondenza del **tipo di dispositivo** per identificare le richieste effettuate da un dispositivo mobile o da un dispositivo desktop.  

### <a name="properties"></a>Proprietà

| Proprietà | Valori supportati |
|-------|------------------|
| Operatore | <ul><li>Nella portale di Azure: `Equal` , `Not Equal`</li><li>Nei modelli ARM: `Equal` ; usare la `negateCondition` proprietà per specificare che _non è uguale_ a |
| Valore | `Mobile`, `Desktop` |

### <a name="example"></a>Esempio

In questo esempio si corrispondono a tutte le richieste che sono state rilevate come provenienti da un dispositivo mobile.

# <a name="portal"></a>[Portale](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/device-type.png" alt-text="Screenshot del portale che mostra la condizione di corrispondenza del tipo di dispositivo.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "IsDevice",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "Mobile"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'IsDevice'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'Mobile'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters'
  }
}
```

---

## <a name="post-args"></a><a name="PostArgs"></a> Argomenti Post

Usare la condizione di corrispondenza **post args** per identificare le richieste in base agli argomenti forniti nel corpo di una richiesta post. Una singola condizione di corrispondenza corrisponde a un singolo argomento del corpo della richiesta POST. È possibile specificare più valori per la corrispondenza, che verranno combinati usando la logica o.

> [!NOTE]
> La condizione di corrispondenza **post args** funziona con il `application/x-www-form-urlencoded` tipo di contenuto.

### <a name="properties"></a>Proprietà

| Proprietà | Valori supportati |
|-|-|
| Argomenti Post | Valore stringa che rappresenta il nome dell'argomento POST. |
| Operatore | Qualsiasi operatore dall' [elenco di operatori standard](#operator-list). |
| Valore | Uno o più valori stringa o Integer che rappresentano il valore dell'argomento POST per la corrispondenza. Se vengono specificati più valori, vengono valutati usando la logica o. |
| Trasformazione maiuscole/minuscole | `Lowercase`, `Uppercase` |

### <a name="example"></a>Esempio

In questo esempio si corrispondono a tutte le richieste POST in cui un `customerName` argomento viene fornito nel corpo della richiesta e dove il valore di `customerName` inizia con la lettera `J` o `K` . Viene usata una trasformazione case per convertire i valori di input in maiuscolo, in modo che i valori che iniziano con `J` ,, `j` `K` e `k` siano tutti corrispondenti.

# <a name="portal"></a>[Portale](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/post-args.png" alt-text="Screenshot del portale che mostra la condizione di corrispondenza post args.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "PostArgs",
  "parameters": {
    "selector": "customerName",
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
        "J",
        "K"
    ],
    "transforms": [
        "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'PostArgs'
  parameters: {
    selector: 'customerName'
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'J'
      'K'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters'
  }
}
```

---

## <a name="query-string"></a><a name="QueryString"></a> Stringa di query

Usare la condizione di corrispondenza della **stringa di query** per identificare le richieste che contengono una stringa di query specifica. È possibile specificare più valori per la corrispondenza, che verranno combinati usando la logica o.

> [!NOTE]
> L'intera stringa di query viene confrontata con una sola stringa, senza l'oggetto principale `?` .

### <a name="properties"></a>Proprietà

| Proprietà | Valori supportati |
|-|-|
| Operatore | Qualsiasi operatore dall' [elenco di operatori standard](#operator-list). |
| Stringa di query | Uno o più valori stringa o Integer che rappresentano il valore della stringa di query di cui trovare una corrispondenza. Non includere all' `?` inizio della stringa di query. Se vengono specificati più valori, vengono valutati usando la logica o. |
| Trasformazione maiuscole/minuscole | `Lowercase`, `Uppercase` |

### <a name="example"></a>Esempio

In questo esempio si corrispondono a tutte le richieste in cui la stringa di query contiene la stringa `language=en-US` . Si vuole che la condizione di corrispondenza sia con distinzione tra maiuscole e minuscole, quindi non si trasforma il caso.

# <a name="portal"></a>[Portale](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/query-string.png" alt-text="Screenshot del portale che mostra la condizione di corrispondenza della stringa di query.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "QueryString",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "language=en-US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'QueryString'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'language=en-US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters'
  }
}
```

---

## <a name="remote-address"></a><a name="RemoteAddress"></a> Indirizzo remoto

La condizione di corrispondenza dell' **indirizzo remoto** identifica le richieste in base alla posizione o all'indirizzo IP del richiedente. È possibile specificare più valori per la corrispondenza, che verranno combinati usando la logica o.

* Usare la notazione CIDR quando si specificano i blocchi di indirizzi IP. Ciò significa che la sintassi per un blocco di indirizzi IP è l'indirizzo IP di base seguito da una barra e dalle dimensioni del prefisso. Ad esempio:
    * **Esempio IPv4**: trova `5.5.5.64/26` la corrispondenza di tutte le richieste che arrivano dagli indirizzi 5.5.5.64 tramite 5.5.5.127.
    * **Esempio IPv6**: trova `1:2:3:/48` la corrispondenza di tutte le richieste provenienti dagli indirizzi da 1:2:3:0:0:0:0:0 a 1:2:3: ffff: ffff: ffff: ffff: FFFF.
* Quando si specificano più indirizzi IP e blocchi di indirizzi IP, viene applicata la logica ' OR '.
    * **Esempio IPv4**: se si aggiungono due indirizzi IP `1.2.3.4` e `10.20.30.40` , la condizione viene confrontata per tutte le richieste provenienti dall'indirizzo 1.2.3.4 o 10.20.30.40.
    * **Esempio IPv6**: se si aggiungono due indirizzi IP `1:2:3:4:5:6:7:8` e `10:20:30:40:50:60:70:80` , la condizione viene confrontata per tutte le richieste che arrivano dall'indirizzo 1:2:3:4:5:6:7:8 o 10:20:30:40:50:60:70:80.

### <a name="properties"></a>Proprietà

| Proprietà | Valori supportati |
|-|-|
| Operatore | <ul><li>Nella portale di Azure: `Geo Match` ,, `Geo Not Match` `IP Match` o `IP Not Match`</li><li>Nei modelli ARM: `GeoMatch` , `IPMatch` ; usare la `negateCondition` proprietà per specificare la mancata _corrispondenza geografica_ o l' _IP non corrispondente_</li></ul> |
| Valore | <ul><li>Per gli `IP Match` `IP Not Match` operatori OR: specificare uno o più intervalli di indirizzi IP. Se vengono specificati più intervalli di indirizzi IP, vengono valutati usando la logica o.</li><li>Per gli `Geo Match` `Geo Not Match` operatori OR: specificare una o più posizioni usando il relativo codice paese.</li></ul> |

### <a name="example"></a>Esempio

In questo esempio si corrispondono a tutte le richieste in cui la richiesta non è stata originata dal Stati Uniti.

# <a name="portal"></a>[Portale](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/remote-address.png" alt-text="Screenshot del portale che mostra la condizione di corrispondenza dell'indirizzo remoto.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RemoteAddress",
  "parameters": {
    "operator": "GeoMatch",
    "negateCondition": true,
    "matchValues": [
      "US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RemoteAddress'
  parameters: {
    operator: 'GeoMatch'
    negateCondition: true
    matchValues: [
      'US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters'
  }
}
```

---

## <a name="request-body"></a><a name="RequestBody"></a> Corpo della richiesta

La condizione di corrispondenza del **corpo della richiesta** identifica le richieste in base a testo specifico visualizzato nel corpo della richiesta. È possibile specificare più valori per la corrispondenza, che verranno combinati usando la logica o.

> [!NOTE]
> Se il corpo di una richiesta supera le dimensioni di 64KB, per la condizione di corrispondenza del **corpo della richiesta** verrà considerato solo il primo 64KB.

### <a name="properties"></a>Proprietà

| Proprietà | Valori supportati |
|-|-|
| Operatore | Qualsiasi operatore dall' [elenco di operatori standard](#operator-list). |
| Valore | Uno o più valori stringa o Integer che rappresentano il valore del testo del corpo della richiesta per cui trovare una corrispondenza. Se vengono specificati più valori, vengono valutati usando la logica o. |
| Trasformazione maiuscole/minuscole | `Lowercase`, `Uppercase` |

### <a name="example"></a>Esempio

In questo esempio si corrispondono a tutte le richieste in cui il corpo della richiesta contiene la stringa `ERROR` . Il corpo della richiesta viene trasformato in lettere maiuscole prima di valutare la corrispondenza, quindi `error` e anche altre varianti del case attivano questa condizione di corrispondenza.

# <a name="portal"></a>[Portale](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-body.png" alt-text="Screenshot del portale che mostra la condizione di corrispondenza del corpo della richiesta.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestBody",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "ERROR"
    ],
    "transforms": [
      "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestBody'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'ERROR'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters'
  }
}
```

---

## <a name="request-file-name"></a><a name="UrlFileName"></a> Nome file della richiesta

La condizione di corrispondenza **nome file richiesta** identifica le richieste che includono il nome file specificato nell'URL della richiesta. È possibile specificare più valori per la corrispondenza, che verranno combinati usando la logica o.

### <a name="properties"></a>Proprietà

| Proprietà | Valori supportati |
|-|-|
| Operatore | Qualsiasi operatore dall' [elenco di operatori standard](#operator-list). |
| Valore | Uno o più valori stringa o Integer che rappresentano il valore del nome del file di richiesta di cui trovare la corrispondenza. Se vengono specificati più valori, vengono valutati usando la logica o. |
| Trasformazione maiuscole/minuscole | `Lowercase`, `Uppercase` |

### <a name="example"></a>Esempio

In questo esempio si corrispondono a tutte le richieste in cui il nome del file di richiesta è `media.mp4` . Il nome del file viene trasformato in lettere minuscole prima di valutare la corrispondenza, quindi `MEDIA.MP4` anche altre varianti del case attivano questa condizione di corrispondenza.

# <a name="portal"></a>[Portale](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-name.png" alt-text="Screenshot del portale che mostra la condizione di corrispondenza nome file richiesta.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileName",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "media.mp4"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileName'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'media.mp4'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters'
  }
}
```

---

## <a name="request-file-extension"></a><a name="UrlFileExtension"></a> Estensione del file di richiesta

La condizione di corrispondenza dell' **estensione del file di richiesta** identifica le richieste che includono l'estensione di file specificata nel nome file nell'URL della richiesta. È possibile specificare più valori per la corrispondenza, che verranno combinati usando la logica o.

> [!NOTE]
> Non includere un punto iniziali. Usare, ad esempio, `html` invece di `.html`.

### <a name="properties"></a>Proprietà

| Proprietà | Valori supportati |
|-|-|
| Operatore | Qualsiasi operatore dall' [elenco di operatori standard](#operator-list). |
| Valore | Uno o più valori stringa o Integer che rappresentano il valore dell'estensione del file di richiesta di cui trovare una corrispondenza. Non includere un punto iniziali. Se vengono specificati più valori, vengono valutati usando la logica o. |
| Trasformazione maiuscole/minuscole | `Lowercase`, `Uppercase` |

### <a name="example"></a>Esempio

In questo esempio si corrispondono a tutte le richieste in cui l'estensione del file di richiesta è `pdf` o `docx` . L'estensione del file di richiesta viene trasformata in lettere minuscole prima di valutare la corrispondenza, quindi, `PDF` `DocX` e anche altre varianti del case attivano questa condizione di corrispondenza.

# <a name="portal"></a>[Portale](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-extension.png" alt-text="Screenshot del portale che mostra la condizione di corrispondenza dell'estensione del file di richiesta.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileExtension",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "pdf",
      "docx"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters"
  }
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileExtension'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'pdf'
      'docx'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters'
  }
}
```

---

## <a name="request-header"></a><a name="RequestHeader"></a> Intestazione della richiesta

La condizione di corrispondenza dell' **intestazione della richiesta** identifica le richieste che includono un'intestazione specifica nella richiesta. È possibile utilizzare questa condizione di corrispondenza per controllare se un'intestazione esiste indipendentemente dal relativo valore oppure per verificare se l'intestazione corrisponde a un valore specificato. È possibile specificare più valori per la corrispondenza, che verranno combinati usando la logica o.

### <a name="properties"></a>Proprietà

| Proprietà | Valori supportati |
|-|-|
| Nome intestazione | Valore stringa che rappresenta il nome dell'argomento POST. |
| Operatore | Qualsiasi operatore dall' [elenco di operatori standard](#operator-list). |
| Valore | Uno o più valori stringa o Integer che rappresentano il valore dell'intestazione della richiesta di cui trovare una corrispondenza. Se vengono specificati più valori, vengono valutati usando la logica o. |
| Trasformazione maiuscole/minuscole | `Lowercase`, `Uppercase` |

### <a name="example"></a>Esempio

In questo esempio si corrispondono a tutte le richieste in cui la richiesta contiene un'intestazione denominata `MyCustomHeader` , indipendentemente dal valore.

# <a name="portal"></a>[Portale](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-header.png" alt-text="Screenshot del portale che mostra la condizione di corrispondenza dell'intestazione della richiesta.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestHeader",
  "parameters": {
    "selector": "MyCustomHeader",
    "operator": "Any",
    "negateCondition": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestHeader'
  parameters: {
    selector: 'MyCustomHeader',
    operator: 'Any'
    negateCondition: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters'
  }
}
```

---

## <a name="request-method"></a><a name="RequestMethod"></a> Metodo di richiesta

La condizione di corrispondenza del **metodo di richiesta** identifica le richieste che usano il metodo di richiesta HTTP specificato. È possibile specificare più valori per la corrispondenza, che verranno combinati usando la logica o.

### <a name="properties"></a>Proprietà

| Proprietà | Valori supportati |
|-|-|
| Operatore | <ul><li>Nella portale di Azure: `Equal` , `Not Equal`</li><li>Nei modelli ARM: `Equal` ; usare la `negateCondition` proprietà per specificare che _non è uguale_ a |
| Metodo richiesta | Uno o più metodi HTTP da: `GET` , `POST` , `PUT` , `DELETE` , `HEAD` , `OPTIONS` , `TRACE` . Se vengono specificati più valori, vengono valutati usando la logica o. |

### <a name="example"></a>Esempio

In questo esempio si corrispondono a tutte le richieste in cui la richiesta usa il `DELETE` metodo.

# <a name="portal"></a>[Portale](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-method.png" alt-text="Screenshot del portale che mostra la condizione di corrispondenza del metodo di richiesta.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestMethod",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "DELETE"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestMethod'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'DELETE
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters'
  }
}
```

---

## <a name="request-path"></a><a name="UrlPath"></a> Percorso della richiesta

La condizione di corrispondenza **percorso richiesta** identifica le richieste che includono il percorso specificato nell'URL della richiesta. È possibile specificare più valori per la corrispondenza, che verranno combinati usando la logica o.

> [!NOTE]
> Il percorso è la parte dell'URL dopo il nome host e una barra. Ad esempio, nell'URL `https://www.contoso.com/files/secure/file1.pdf` il percorso è `files/secure/file1.pdf` .

### <a name="properties"></a>Proprietà

| Proprietà | Valori supportati |
|-|-|
| Operatore | Qualsiasi operatore dall' [elenco di operatori standard](#operator-list). |
| Valore | Uno o più valori stringa o Integer che rappresentano il valore del percorso della richiesta di cui trovare la corrispondenza. Non includere la barra iniziali. Se vengono specificati più valori, vengono valutati usando la logica o. |
| Trasformazione maiuscole/minuscole | `Lowercase`, `Uppercase` |

### <a name="example"></a>Esempio

In questo esempio si corrispondono a tutte le richieste in cui il percorso del file di richiesta inizia con `files/secure/` . L'estensione del file di richiesta viene trasformata in lettere minuscole prima di valutare la corrispondenza, quindi le richieste a `files/SECURE/` e altre varianti del caso attiveranno anche questa condizione di corrispondenza.

# <a name="portal"></a>[Portale](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-path.png" alt-text="Screenshot del portale che mostra la condizione di corrispondenza del percorso richiesta.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlPath",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "files/secure/"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlPath'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'files/secure/'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters'
  }
}
```

---

## <a name="request-protocol"></a><a name="RequestScheme"></a> Protocollo di richiesta

La condizione di corrispondenza del **protocollo di richiesta** identifica le richieste che utilizzano il protocollo specificato (http o HTTPS).

> [!NOTE]
> Il *protocollo* è talvolta definito anche *schema*.

### <a name="properties"></a>Proprietà

| Proprietà | Valori supportati |
|-|-|
| Operatore | <ul><li>Nella portale di Azure: `Equal` , `Not Equal`</li><li>Nei modelli ARM: `Equal` ; usare la `negateCondition` proprietà per specificare che _non è uguale_ a |
| Metodo richiesta | `HTTP`, `HTTPS` |

### <a name="example"></a>Esempio

In questo esempio si corrispondono a tutte le richieste in cui la richiesta usa il `HTTP` protocollo.

# <a name="portal"></a>[Portale](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-protocol.png" alt-text="Screenshot del portale che mostra la condizione di corrispondenza del protocollo di richiesta.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestScheme",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "HTTP"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestScheme'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'HTTP
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters'
  }
}
```

---

## <a name="request-url"></a><a name="RequestUrl"></a> URL della richiesta

Identifica le richieste che corrispondono all'URL specificato. Viene valutato l'intero URL. È possibile specificare più valori per la corrispondenza, che verranno combinati usando la logica o.

> [!TIP]
> Quando si usa questa condizione della regola, assicurarsi di includere il protocollo. Ad esempio, usare `https://www.contoso.com` anziché semplicemente `www.contoso.com` .

### <a name="properties"></a>Proprietà

| Proprietà | Valori supportati |
|-|-|
| Operatore | Qualsiasi operatore dall' [elenco di operatori standard](#operator-list). |
| Valore | Uno o più valori stringa o Integer che rappresentano il valore dell'URL della richiesta di cui trovare la corrispondenza. Se vengono specificati più valori, vengono valutati usando la logica o. |
| Trasformazione maiuscole/minuscole | `Lowercase`, `Uppercase` |

### <a name="example"></a>Esempio

In questo esempio si corrispondono a tutte le richieste in cui l'URL della richiesta inizia con `https://api.contoso.com/customers/123` . L'estensione del file di richiesta viene trasformata in lettere minuscole prima di valutare la corrispondenza, quindi le richieste a `https://api.contoso.com/Customers/123` e altre varianti del caso attiveranno anche questa condizione di corrispondenza.

# <a name="portal"></a>[Portale](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-url.png" alt-text="Screenshot del portale che mostra la condizione di corrispondenza URL richiesta.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestUri",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "https://api.contoso.com/customers/123"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestUri'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'https://api.contoso.com/customers/123'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters'
  }
}
```

---

## <a name="operator-list"></a><a name = "operator-list"></a> Elenco operatori

Per le regole che accettano valori dall'elenco di operatori standard, gli operatori seguenti sono validi:

| Operatore                   | Descrizione                                                                                                                    | Supporto del modello ARM                                            |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Qualsiasi                        | Corrisponde a quando è presente un valore, indipendentemente dall'elemento.                                                                     | `operator`: `Any`                                               |
| Uguale a                      | Corrisponde a quando il valore corrisponde esattamente alla stringa specificata.                                                                   | `operator`: `Equal`                                             |
| Contiene                   | Corrisponde a quando il valore contiene la stringa specificata.                                                                          | `operator`: `Contains`                                          |
| Minore di                  | Corrisponde a quando la lunghezza del valore è minore dell'integer specificato.                                                       | `operator`: `LessThan`                                          |
| Maggiore di               | Corrisponde a quando la lunghezza del valore è maggiore dell'integer specificato.                                                    | `operator`: `GreaterThan`                                       |
| Minore o uguale a         | Corrisponde a quando la lunghezza del valore è minore o uguale all'intero specificato.                                           | `operator`: `LessThanOrEqual`                                   |
| Maggiore o uguale a      | Corrisponde a quando la lunghezza del valore è maggiore o uguale all'intero specificato.                                        | `operator`: `GreaterThanOrEqual`                                |
| Inizia con                | Corrisponde a quando il valore inizia con la stringa specificata.                                                                       | `operator`: `BeginsWith`                                        |
| Termina con                  | Corrisponde a quando il valore termina con la stringa specificata.                                                                         | `operator`: `EndsWith`                                          |
| RegEx                      | Corrisponde a quando il valore corrisponde all'espressione regolare specificata. [Per ulteriori informazioni, vedere di seguito.](#regular-expressions)        | `operator`: `RegEx`                                             |
| Nessun                    | Corrisponde a quando non è presente alcun valore.                                                                                                | `operator`: `Any` e `negateCondition` : `true`                |
| Diverso da                  | Corrisponde a quando il valore non corrisponde alla stringa specificata.                                                                    | `operator`: `Equal` e `negateCondition` : `true`              |
| Non contiene               | Corrisponde a quando il valore non contiene la stringa specificata.                                                                  | `operator`: `Contains` e `negateCondition` : `true`           |
| Non minore di              | Corrisponde a quando la lunghezza del valore non è minore dell'intero specificato.                                                   | `operator`: `LessThan` e `negateCondition` : `true`           |
| Non maggiore di           | Corrisponde a quando la lunghezza del valore non è maggiore dell'intero specificato.                                                | `operator`: `GreaterThan` e `negateCondition` : `true`        |
| Non minore o uguale a     | Corrisponde a quando la lunghezza del valore non è minore o uguale all'intero specificato.                                       | `operator`: `LessThanOrEqual` e `negateCondition` : `true`    |
| Non maggiore o uguale a | Corrisponde a quando la lunghezza del valore non è maggiore o uguale all'intero specificato.                                    | `operator`: `GreaterThanOrEqual` e `negateCondition` : `true` |
| Non inizia con            | Corrisponde a quando il valore non inizia con la stringa specificata.                                                               | `operator`: `BeginsWith` e `negateCondition` : `true`         |
| Non termina con              | Corrisponde a quando il valore non termina con la stringa specificata.                                                                 | `operator`: `EndsWith` e `negateCondition` : `true`           |
| Non RegEx                  | Corrisponde a quando il valore non corrisponde all'espressione regolare specificata. [Per ulteriori informazioni, vedere di seguito.](#regular-expressions) | `operator`: `RegEx` e `negateCondition` : `true`              |

> [!TIP]
> Per gli operatori numerici, ad esempio *minore di* e *maggiore o uguale a*, il confronto si basa sulla lunghezza. Il valore della condizione di corrispondenza deve essere un numero intero che specifica la lunghezza che si desidera confrontare.

### <a name="regular-expressions"></a><a name="regular-expressions"></a> Espressioni regolari

Le espressioni regolari non supportano le operazioni seguenti:

* Backreference e acquisizione di sottoespressioni.
* Asserzioni di larghezza zero arbitrarie.
* Riferimenti a subroutine e modelli ricorsivi.
* Modelli condizionali.
* Verbi di controllo backtracking.
* `\C`Direttiva a byte singolo.
* `\R`Direttiva di corrispondenza di nuova riga.
* `\K`Inizio della direttiva di reimpostazione delle corrispondenze.
* Callout e codice incorporato.
* Raggruppamento atomico e quantificatori possessivi.

## <a name="arm-template-support"></a>Supporto del modello ARM

I set di regole possono essere configurati tramite Azure Resource Manager modelli. [Vedere un modello di esempio](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set). È possibile aggiungere condizioni di corrispondenza usando i frammenti di codice JSON o bicipite inclusi negli esempi precedenti.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [set di regole](concept-rule-set.md).
* Informazioni su come [configurare il primo set di regole](how-to-configure-rule-set.md).
* Altre informazioni sulle [azioni del set di regole](concept-rule-set-actions.md).
