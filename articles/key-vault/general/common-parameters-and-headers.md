---
title: Parametri e intestazioni comuni
description: Parametri e intestazioni comuni a tutte le operazioni eseguibili con le risorse di Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 616b6061b08258d465b09902556de6903b873199
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749871"
---
# <a name="common-parameters-and-headers"></a>Parametri e intestazioni comuni

Le seguenti informazioni sono comuni a tutte le operazioni che è possibile eseguire in relazione alle risorse di Key Vault:

- L'intestazione HTTP `Host` deve essere sempre presente e deve specificare il nome host dell'insieme di credenziali. Esempio: `Host: contoso.vault.azure.net`. Si noti che la maggior parte delle tecnologie client popola `Host` l'intestazione dall'URI. Ad esempio, `GET https://contoso.vault.azure.net/secrets/mysecret{...}` imposta come `Host` `contoso.vault.azure.net` . Ciò significa che se si accede Key Vault usando un indirizzo IP non elaborato come , il valore automatico dell'intestazione sarà errato e sarà necessario verificare manualmente che l'intestazione contenga il nome host dell'insieme `GET https://10.0.0.23/secrets/mysecret{...}` `Host` di `Host` credenziali.
- Sostituire `{api-version}` con la versione dell'API nell'URI.
- Sostituire `{subscription-id}` con l'identificatore della sottoscrizione nell'URI
- Sostituire `{resource-group-name}` con il gruppo di risorse. Per altre informazioni, vedere Uso di gruppi di risorse per gestire le risorse di Azure.
- Sostituire `{vault-name}` con il nome dell'insieme di credenziali delle chiavi nell'URI.
- Impostare l'intestazione Content-Type su application/json.
- Impostare l'intestazione dell'autorizzazione su un token JSON Web ottenuto da Azure Active Directory. Per altre informazioni, vedere [Autenticazione Azure Resource Manager](authentication-requests-and-responses.md) richieste.

## <a name="common-error-response"></a>Risposta di errore comune
Il servizio userà i codici di stato HTTP per indicare l'esito positivo o negativo. Gli errori contengono inoltre una risposta nel formato seguente:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Nome dell'elemento | Type | Descrizione |
|---|---|---|
| codice | string | Il tipo di errore che si è verificato.|
| message | string | Descrizione della causa dell'errore. |



## <a name="see-also"></a>Vedere anche
 [Azure Key Vault informazioni di riferimento sulle API REST](/rest/api/keyvault/)
