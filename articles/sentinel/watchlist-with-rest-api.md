---
title: Gestire watchlists in Sentinel di Azure con l'API REST | Microsoft Docs
description: Questo articolo descrive in che modo è possibile gestire Azure Sentinel watchlists usando l'API REST di Log Analytics per creare, modificare ed eliminare watchlists e i relativi elementi.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2021
ms.author: yelevin
ms.openlocfilehash: ea571f9b033ba82709a13c6d32649f3228ee04b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98798466"
---
# <a name="manage-watchlists-in-azure-sentinel-using-rest-api"></a>Gestire watchlists in Sentinel di Azure con l'API REST

> [!IMPORTANT]
> La funzionalità watchlists è attualmente in fase di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Azure Sentinel, che è integrato in Log Analytics di monitoraggio di Azure, consente di usare Log Analytics API REST per gestire watchlists. Questo documento illustra come creare, modificare ed eliminare watchlists e i relativi elementi usando l'API REST.  Watchlists creato in questo modo verrà visualizzato nell'interfaccia utente di Sentinel di Azure.

## <a name="common-uri-parameters"></a>Parametri URI comuni

Di seguito sono riportati i parametri URI comuni per tutti i comandi dell'API Watcher:

| Nome | In | Obbligatoria | Tipo | Descrizione |
|-|-|-|-|-|
| **SubscriptionId** | path | sì | GUID | ID sottoscrizione di Azure |
| **ResourceGroupName** | path | sì | string | nome del gruppo di risorse all'interno della sottoscrizione |
| **workspaceName** | path | sì | string | nome dell'area di lavoro Log Analytics |
| **{watchlistAlias}** | path | sì* | string | nome di un controllo specificato<br>\* Non obbligatorio per il recupero di tutti i watchlists |
| **{watchlistItemId}** | path | Sì * * | GUID | ID dell'elemento da creare, aggiungere o eliminare dall'oggetto Watch List<br>\*\* Obbligatorio solo per i comandi degli elementi dell'elemento Watcher |
| **{API-Version}** | query | sì | string | versione del protocollo utilizzata per effettuare questa richiesta. A partire dal 29 ottobre 2020, la versione dell'API Watch List è *2019-01-01-Preview* |
| **{bearerToken}** | authorization | sì | token | token di autorizzazione di porta |
|  

## <a name="retrieve-all-watchlists"></a>Recupera tutti watchlists

Questo comando recupera tutti i watchlists associati a un'area di lavoro, senza i relativi elementi.

### <a name="request-uri"></a>URI richiesta
(L'URI è una singola riga, suddiviso per semplificare la leggibilità)

| Metodo | URI richiesta |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists?api-version={{api-version}}` |
|

### <a name="responses"></a>Risposte

| Codice di stato | Corpo della risposta | Descrizione |
|-|-|-|
| 200/OK | Elenco di watchlists esistenti o vuoti se non è stato trovato alcun controllo |  |
| 400/richiesta non valida |  | Sintassi della richiesta non valida. parametro della richiesta non valido... |
|

## <a name="look-up-a-watchlist-by-name"></a>Cerca un controllo in base al nome

Questo comando Recupera un controllo specifico associato a un'area di lavoro, senza i relativi elementi.

### <a name="request-uri"></a>URI richiesta
(L'URI è una singola riga, suddiviso per semplificare la leggibilità)

| Metodo | URI richiesta |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Risposte

| Codice di stato | Corpo della risposta | Descrizione |
|-|-|-|
| 200/OK | Il controllo richiesto |  |
| 400/richiesta non valida |  | Sintassi della richiesta non valida. parametro della richiesta non valido... |
| 404/non trovato |  | Non è stato trovato alcun controllo con il nome richiesto |
|

## <a name="create-a-watchlist"></a>Creare un controllo

Questo comando crea un oggetto Watch List e vi aggiunge elementi. Sono necessarie due chiamate a questo endpoint per creare l'oggetto Watch List e i relativi elementi: il primo creerà l'oggetto Watch List (padre) e il secondo aggiungerà gli elementi.

### <a name="request-uri"></a>URI richiesta
(L'URI è una singola riga, suddiviso per semplificare la leggibilità)

| Metodo | URI richiesta |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>Testo della richiesta

Ecco un esempio del corpo della richiesta di una richiesta di creazione dell'elenco di controllo:

```json
{
    "properties": {
        "displayName": "High Value Assets Watchlist",
        "source": "Local file",
        "provider": "Microsoft",
        "numberOfLinesToSkip":"1",
        "rawContent": "metadata line\nheader1, header2\nval1, val2",
        "contentType": "text/csv"
    }
}
```

### <a name="responses"></a>Risposte

| Codice di stato | Corpo della risposta | Descrizione |
|-|-|-|
| 200/OK | L'oggetto Watch List creato dalla richiesta, senza elementi |  |
| 400/richiesta non valida |  | Sintassi della richiesta non valida. parametro della richiesta non valido... |
| 409/conflitto |  | Operazione non riuscita. è presente un controllo esistente con lo stesso alias |
|

## <a name="delete-a-watchlist"></a>Elimina un controllo

Questo comando Elimina un oggetto Watch List e i relativi elementi.

### <a name="request-uri"></a>URI richiesta
(L'URI è una singola riga, suddiviso per semplificare la leggibilità)

| Metodo | URI richiesta |
|-|-|
| DOLETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Risposte

| Codice di stato | Corpo della risposta | Descrizione |
|-|-|-|
| 200/OK | Corpo della risposta vuoto |  |
| 204/nessun contenuto | Corpo della risposta vuoto | Nessun elemento eliminato |
| 400/richiesta non valida |  | Sintassi della richiesta non valida. parametro della richiesta non valido... |
|

## <a name="add-or-update-a-watchlist-item"></a>Aggiungere o aggiornare un elemento Watch List

Quando questo comando viene eseguito in un *watchlisItemId* esistente (Guid), aggiornerà l'elemento con i dati forniti nel corpo della richiesta. In caso contrario, verrà creato un nuovo elemento.

### <a name="request-uri"></a>URI richiesta
(L'URI è una singola riga, suddiviso per semplificare la leggibilità)

| Metodo | URI richiesta |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>Testo della richiesta

Di seguito è riportato un esempio del corpo della richiesta di una richiesta di aggiunta/aggiornamento elemento dell'elenco di controllo:

```json
{
    "properties": {
      "itemsKeyValue": {
           "Gateway subnet": "10.0.255.224/27",
           "Web Tier": "10.0.1.0/24",
           "Business tier": "10.0.2.0/24",
           "Private DMZ in": "10.0.0.0/27",
           "Public DMZ out": "10.0.0.96/27"
      }
}
}
```

### <a name="responses"></a>Risposte

| Codice di stato | Corpo della risposta | Descrizione |
|-|-|-|
| 200/OK | L'elemento dell'oggetto Watch List creato o aggiornato dalla richiesta |  |
| 400/richiesta non valida |  | Sintassi della richiesta non valida. parametro della richiesta non valido... |
| 409/conflitto |  | Operazione non riuscita. è presente un controllo esistente con lo stesso alias |
|

## <a name="delete-a-watchlist-item"></a>Elimina un elemento Watch List

Questo comando Elimina un elemento Watch List esistente.

### <a name="request-uri"></a>URI richiesta
(L'URI è una singola riga, suddiviso per semplificare la leggibilità)

| Metodo | URI richiesta |
|-|-|
| DOLETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Risposte

| Codice di stato | Corpo della risposta | Descrizione |
|-|-|-|
| 200/OK | Corpo della risposta vuoto |  |
| 204/nessun contenuto | Corpo della risposta vuoto | Nessun elemento eliminato |
| 400/richiesta non valida |  | Sintassi della richiesta non valida. parametro della richiesta non valido... |
|

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come gestire watchlists e i relativi elementi in Sentinel di Azure usando l'API Log Analytics. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Altre informazioni su [watchlists](watchlists.md)
- Esplorare altri usi dell' [API Sentinel di Azure](/rest/api/securityinsights/)