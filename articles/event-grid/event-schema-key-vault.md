---
title: Azure Key Vault come origine griglia di eventi
description: Descrive le proprietà e lo schema forniti per gli eventi di Azure Key Vault con griglia di eventi di Azure
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: ea8821b15000b74a10f28730ccf82b538e7819e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363407"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure Key Vault come origine griglia di eventi

Questo articolo fornisce le proprietà e lo schema per gli eventi in [Azure Key Vault](../key-vault/index.yml). Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).


## <a name="available-event-types"></a>Tipi di evento disponibili

Un account Azure Key Vault genera i tipi di eventi seguenti:

| Nome completo evento | Nome visualizzato evento | Descrizione |
| ---------- | ----------- |---|
| Microsoft. Vault. CertificateNewVersionCreated | Certificato nuova versione creata | Attivato quando viene creato un nuovo certificato o una nuova versione del certificato. |
| Microsoft. Vault. CertificateNearExpiry | Certificato vicino alla scadenza | Attivato quando la versione corrente del certificato sta per scadere. (L'evento viene attivato 30 giorni prima della data di scadenza). |
| Microsoft. Vault. CertificateExpired | Il certificato è scaduto | Attivato quando il certificato è scaduto. |
| Microsoft. Vault. KeyNewVersionCreated | Chiave nuova versione creata | Attivato quando viene creata una nuova chiave o nuova versione della chiave. |
| Microsoft. Vault. KeyNearExpiry | Chiave prossima alla scadenza | Attivato quando la versione corrente di una chiave sta per scadere. (L'evento viene attivato 30 giorni prima della data di scadenza). |
| Microsoft. Vault. fileexpire | Chiave scaduta | Attivato quando un tasto è scaduto. |
| Microsoft. Vault. SecretNewVersionCreated | La nuova versione del segreto è stata creata | Attivato quando viene creata una nuova versione del segreto o del nuovo segreto. |
| Microsoft. Vault. SecretNearExpiry | Segreto vicino alla scadenza | Attivato quando la versione corrente di un segreto sta per scadere. (L'evento viene attivato 30 giorni prima della data di scadenza). |
| Microsoft. Vault. SecretExpired | Il segreto è scaduto | Attivato quando un segreto è scaduto. |
| Microsoft. Vault. VaultAccessPolicyChanged | Criteri di accesso dell'insieme di credenziali modificati | Attivato quando vengono modificati i criteri di accesso in Key Vault. Include uno scenario in cui il modello di autorizzazione Key Vault viene modificato in/dal controllo degli accessi in base al ruolo di Azure.   |

## <a name="event-examples"></a>Esempi di eventi

# <a name="event-grid-event-schema"></a>[Schema di eventi di Griglia di eventi](#tab/event-grid-event-schema)

Nell'esempio seguente viene illustrato lo schema per **Microsoft. SecretNewVersionCreated**:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

# <a name="cloud-event-schema"></a>[Schema evento cloud](#tab/cloud-event-schema)

Nell'esempio seguente viene illustrato lo schema per **Microsoft. SecretNewVersionCreated**:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "source":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "type":"Microsoft.KeyVault.SecretNewVersionCreated",
      "time":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "specversion":"1.0"
   }
]
```

---

### <a name="event-properties"></a>Proprietà degli eventi

# <a name="event-grid-event-schema"></a>[Schema di eventi di Griglia di eventi](#tab/event-grid-event-schema)
Un evento presenta i seguenti dati di primo livello:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| `topic` | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene specificato da Griglia di eventi. |
| `subject` | string | Percorso dell'oggetto dell'evento definito dall'origine di pubblicazione. |
| `eventType` | string | Uno dei tipi di evento registrati per l'origine evento. |
| `eventTime` | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| `id` | string | Identificatore univoco dell'evento. |
| `data` | object | Dati degli eventi di configurazione dell'app. |
| `dataVersion` | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'origine di pubblicazione. |
| `metadataVersion` | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene specificato da Griglia di eventi. |


# <a name="cloud-event-schema"></a>[Schema evento cloud](#tab/cloud-event-schema)

Un evento presenta i seguenti dati di primo livello:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| `source` | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene specificato da Griglia di eventi. |
| `subject` | string | Percorso dell'oggetto dell'evento definito dall'origine di pubblicazione. |
| `type` | string | Uno dei tipi di evento registrati per l'origine evento. |
| `time` | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| `id` | string | Identificatore univoco dell'evento. |
| `data` | object | Dati degli eventi di configurazione dell'app. |
| `specversion` | string | Versione della specifica dello schema CloudEvents. |

---
 

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | Descrizione |
| ---------- | ----------- |---|
| `id` | string | ID dell'oggetto che ha attivato questo evento. |
| `vaultName` | string | Nome dell'insieme di credenziali delle chiavi dell'oggetto che ha generato l'evento |
| `objectType` | string | Tipo dell'oggetto che ha attivato questo evento. |
| `objectName` | string | Nome dell'oggetto che ha attivato questo evento. |
| `version` | string | Versione dell'oggetto che ha attivato questo evento. |
| `nbf` | d'acquisto | La data precedente in secondi a partire dal 1970-01-01T00:00:00Z dell'oggetto che ha attivato questo evento |
| `exp` | d'acquisto | Data di scadenza in secondi da 1970-01-01T00:00:00Z dell'oggetto che ha attivato questo evento |

## <a name="tutorials-and-how-tos"></a>Esercitazioni e procedure
|Titolo  |Descrizione  |
|---------|---------|
| [Monitoraggio di eventi di Key Vault con griglia di eventi di Azure](../key-vault/general/event-grid-overview.md) | Panoramica dell'integrazione di Key Vault con griglia di eventi. |
| [Esercitazione: creare e monitorare Key Vault eventi con griglia di eventi](../key-vault/general/event-grid-logicapps.md) | Informazioni su come configurare le notifiche di griglia di eventi per Key Vault. |


## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a griglia di eventi di Azure, vedere informazioni su [griglia di eventi](overview.md).
* Per altre informazioni su come creare una sottoscrizione di griglia di eventi di Azure, vedere [schema di sottoscrizione di griglia di eventi](subscription-creation-schema.md).
* Per ulteriori informazioni su Key Vault, vedere [che cos'è Azure Key Vault?](../key-vault/general/overview.md)

