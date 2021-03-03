---
title: Log di diagnostica di hub di notifica di Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica di tutti i log operativi e di diagnostica disponibili per gli hub di notifica di Azure.
author: brannon
ms.author: brjones
ms.service: notification-hubs
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: b98a04a70062461cec603bea83052c4f1224819e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736237"
---
# <a name="enable-diagnostics-logs-for-notification-hubs"></a>Abilitare i log di diagnostica per gli hub di notifica

Quando si inizia a usare lo spazio dei nomi di hub di notifica di Azure, potrebbe essere necessario monitorare come e quando viene creato, eliminato o eseguito lo spazio dei nomi. Questo articolo fornisce una panoramica di tutti i log operativi e di diagnostica disponibili.

Hub di notifica di Azure supporta attualmente log attività e operativi, che acquisiscono *operazioni di gestione* eseguite nello spazio dei nomi di hub di notifica di Azure.

## <a name="diagnostic-logs-schema"></a>Schema dei log di diagnostica

Tutti i log vengono archiviati in formato JavaScript Object Notation (JSON) nelle due posizioni seguenti:

- **AzureActivity**: Visualizza i log delle operazioni e delle azioni eseguite sullo spazio dei nomi nell'portale di Azure o tramite Azure Resource Manager distribuzioni di modelli.
- **AzureDiagnostics**: Visualizza i log delle operazioni e delle azioni eseguite sullo spazio dei nomi tramite l'API o i client di gestione nell'SDK del linguaggio.

Le stringhe JSON del log di diagnostica includono gli elementi elencati nella tabella seguente:

| Nome | Descrizione |
| ------- | ------- |
| time | Timestamp UTC del log |
| resourceId | Percorso relativo della risorsa di Azure |
| operationName | Nome dell'operazione di gestione |
| category | Categoria log. Valori validi: `OperationalLogs` |
| callerIdentity | Identità del chiamante che ha avviato l'operazione di gestione |
| resultType | Stato dell'operazione di gestione. Valori validi: `Succeeded` o `Failed` |
| resultDescription | Descrizione dell'operazione di gestione |
| correlationId | ID di correlazione dell'operazione di gestione (se specificato) |
| callerIpAddress | Indirizzo IP del chiamante. Empty per le chiamate originate dalla portale di Azure |

Di seguito è riportato un esempio di stringa JSON di log operativo:

```json
{
    "operationName": "Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action",
    "resourceId": "/SUBSCRIPTIONS/2CAC2A14-BA6B-46A6-BCE8-2D9781A41BA2/RESOURCEGROUPS/SAMPLES/PROVIDERS/MICROSOFT.NOTIFICATIONHUBS/NAMESPACES/SAMPLE-NS",
    "time": "1/1/2021 5:16:32 AM +00:00",
    "category": "OperationalLogs",
    "resultType": "Succeeded",
    "resultDescription": "Gets Hub Authorization Rules",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "callerIdentity": "{ \"identityType\": \"Portal\", \"identity\": \"\" }"
}
```

Il `callerIdentity` campo può essere vuoto o una stringa JSON con uno dei formati seguenti.

Per le chiamate provenienti dal portale di Azure il `identity` campo è vuoto. Il log può essere correlato ai log attività per determinare l'utente che ha eseguito l'accesso.
```json
{
    "identityType": "Portal",
    "identity": ""
}
```

Per le chiamate effettuate tramite Azure Resource Manager il campo conterrà `identity` il nome utente dell'utente che ha eseguito l'accesso.
```json
{
   "identityType": "Username",
   "identity": "test@foo.com"
}
```

Per le chiamate all'API REST di hub di notifica, il campo conterrà `identity` il nome dei criteri di accesso usati per generare il token SharedAccessSignature.
```json
{
   "identityType": "KeyName",
   "identity": "SharedAccessRootKey2"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>Eventi e operazioni acquisiti nei log operativi

I log operativi acquisiscono tutte le operazioni di gestione eseguite nello spazio dei nomi di hub di notifica di Azure. Le operazioni sui dati non vengono acquisite a causa del volume elevato di operazioni sui dati eseguite in hub di notifica di Azure.

Le operazioni di gestione seguenti vengono acquisite nei log operativi: 

| Ambito | Nome operazione | Descrizione operazione |
| :-- | :-- | :-- |
| Spazio dei nomi | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Elencare le regole di autorizzazione |
| Spazio dei nomi | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Elimina regola di autorizzazione |
| Spazio dei nomi | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Elenco delle chiavi |
| Spazio dei nomi | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Ottenere la regola di autorizzazione |
| Spazio dei nomi | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Rigenerare le chiavi |
| Spazio dei nomi | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Crea o Aggiorna regola di autorizzazione |
| Spazio dei nomi | Microsoft. NotificationHubs/Namespaces/Delete | Elimina spazio dei nomi |
| Spazio dei nomi | Microsoft.NotificationHubs/Namespaces/read | Ottieni spazio dei nomi |
| Spazio dei nomi | Microsoft.NotificationHubs/Namespaces/write | Crea o Aggiorna spazio dei nomi |
| Hub di notifica | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Elencare le regole di autorizzazione |
| Hub di notifica | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Elimina regola di autorizzazione |
| Hub di notifica | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Elenco delle chiavi |
| Hub di notifica | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Leggi regola di autorizzazione |
| Hub di notifica | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Rigenerare le chiavi |
| Hub di notifica | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Crea o Aggiorna regola di autorizzazione |
| Hub di notifica | Microsoft. NotificationHubs/Namespaces/NotificationHubs/Delete | Elimina Hub di notifica |
| Hub di notifica | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Creare, aggiornare o ottenere le credenziali PNS |
| Hub di notifica | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Ottenere un hub di notifica |
| Hub di notifica | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Crea o aggiorna Hub di notifica |

## <a name="enable-operational-logs"></a>Abilitare i log operativi

Per impostazione predefinita, i log operativi sono disabilitati. Per abilitare i log, eseguire le operazioni seguenti:

1. Nel [portale di Azure](https://portal.azure.com)passare allo spazio dei nomi di hub di notifica di Azure e quindi selezionare impostazioni di **diagnostica** in **monitoraggio**.

   ![Collegamento "impostazioni di diagnostica"](./media/notification-hubs-diagnostic-logs/image-1.png)

1. Nel riquadro **impostazioni di diagnostica** selezionare **Aggiungi impostazione di diagnostica**.  

   ![Collegamento "Aggiungi impostazione di diagnostica"](./media/notification-hubs-diagnostic-logs/image-2.png)

1. Configurare le impostazioni di diagnostica eseguendo le operazioni seguenti:

   a. Nella casella **Nome** immettere un nome per le impostazioni di diagnostica.  

   b. Selezionare una delle tre destinazioni seguenti per i log di diagnostica:  
   - Se si seleziona **Invia a log Analytics area di lavoro**, è necessario specificare l'istanza di log Analytics a cui verrà inviata la diagnostica.  
   - Se si seleziona **archivia in un account di archiviazione**, è necessario configurare l'account di archiviazione in cui verranno archiviati i log di diagnostica.  
   - Se si seleziona **flusso in un hub eventi**, è necessario configurare l'hub eventi in cui si desidera trasmettere i log di diagnostica.

   c. Selezionare la casella di controllo **OperationalLogs** .

    ![Riquadro "impostazioni di diagnostica"](./media/notification-hubs-diagnostic-logs/image-3.png)

1. Selezionare **Salva**.

Le nuove impostazioni diventano effettive entro 10 minuti circa. I log vengono visualizzati nella destinazione di archiviazione configurata, nel riquadro **Log di diagnostica**.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla configurazione delle impostazioni di diagnostica, vedere:
* [Panoramica dei log di diagnostica di Azure](../azure-monitor/essentials/platform-logs-overview.md).

Per altre informazioni su Hub di notifica di Azure, vedere:
* [Informazioni su Hub di notifica](notification-hubs-push-notification-overview.md)