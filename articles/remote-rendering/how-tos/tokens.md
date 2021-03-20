---
title: Ottenere i token di accesso al servizio
description: Viene descritto come creare i token per l'accesso alle API REST ARR
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9721685fc3ccd2c1c80b55e9118d6d347cc97a9c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97830701"
---
# <a name="get-service-access-tokens"></a>Ottenere i token di accesso al servizio

L'accesso alle API REST ARR viene concesso solo agli utenti autorizzati. Per dimostrare l'autorizzazione, è necessario inviare un *token di accesso* insieme alle richieste REST. Questi token vengono rilasciati dal *servizio token di sicurezza* (STS) in Exchange per una chiave dell'account. I token hanno una **durata di 24 ore** e possono quindi essere rilasciati agli utenti senza concedere loro l'accesso completo al servizio.

Questo articolo descrive come creare un token di accesso di questo tipo.

## <a name="prerequisites"></a>Prerequisiti

[Creare un account arr](create-an-account.md), se non ne è già presente uno.

## <a name="token-service-rest-api"></a>API REST del servizio token

Per creare i token di accesso, il *servizio token di sicurezza* fornisce una singola API REST. L'URL per il servizio STS dipende dal dominio account dell'account di rendering remoto. È nel formato https://sts . [ dominio dell'account], ad esempio `https://sts.southcentralus.mixedreality.azure.com`

### <a name="get-token-request"></a>Richiesta ' Get token '

| URI | Metodo |
|-----------|:-----------|
| /Accounts/**accountId**/token | GET |

| Intestazione | valore |
|--------|:------|
| Autorizzazione | "Bearer **accountId**:**AccountKey**" |

Sostituire *accountId* e *AccountKey* con i rispettivi dati.

### <a name="get-token-response"></a>Risposta ' Get token '

| Codice di stato | payload JSON | Commenti |
|-----------|:-----------|:-----------|
| 200 | AccessToken: stringa | Operazione riuscita |

| Intestazione | Scopo |
|--------|:------|
| MS-CV | Questo valore può essere utilizzato per tracciare la chiamata all'interno del servizio |

## <a name="getting-a-token-using-powershell"></a>Ottenere un token tramite PowerShell

Il codice di PowerShell riportato di seguito illustra come inviare la richiesta REST necessaria al servizio token di servizio. Il token viene quindi stampato nel prompt di PowerShell.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"
$accountDomain = "<account_domain_from_portal>

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.$accountDomain/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

Lo script stampa semplicemente il token nell'output, da cui è possibile copiare & incollarlo. Per un progetto reale, è consigliabile automatizzare questo processo.

## <a name="next-steps"></a>Passaggi successivi

* [Script di Azure PowerShell di esempio](../samples/powershell-example-scripts.md)
* [API front-end di Azure](../how-tos/frontend-apis.md)
* [API REST di gestione delle sessioni](../how-tos/session-rest-api.md)
