---
title: Sincronizzazione in ingresso per il provisioning del cloud con MS API Graph
description: Questo argomento descrive come abilitare la sincronizzazione in ingresso usando solo la API Graph
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f308f46fc021a1d08f4065d48558a6dd71786c7c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860356"
---
# <a name="inbound-synchronization-for-cloud-provisioning-using-ms-graph-api"></a>Sincronizzazione in ingresso per il provisioning del cloud con MS API Graph

Il documento seguente descrive come replicare un profilo di sincronizzazione da zero usando solo le API MSGraph.  
La struttura di questa procedura è costituita dai passaggi seguenti.  Ad esempio:

- [Configurazione di base](#basic-setup)
- [Creare entità servizio](#create-service-principals)
- [Crea processo di sincronizzazione](#create-sync-job)
- [Aggiorna dominio di destinazione](#update-targeted-domain)
- [Avvia processo di sincronizzazione](#start-sync-job)
- [Verifica stato](#review-status)

Usare questi comandi [modulo di Microsoft Azure Active Directory per Windows PowerShell](https://docs.microsoft.com/powershell/module/msonline/) per abilitare la sincronizzazione per un tenant di produzione, un prerequisito per poter chiamare il servizio Web di amministrazione per tale tenant.

## <a name="basic-setup"></a>Configurazione di base

### <a name="enable-tenant-flags"></a>Abilita flag tenant

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
Il primo di questi due comandi richiede le credenziali Azure Active Directory. Questi cmdlet identificano in modo implicito il tenant e lo abilitano per la sincronizzazione.

## <a name="create-service-principals"></a>Creare entità servizio
Successivamente, è necessario creare l' [applicazione/entità servizio AD2AAD](https://docs.microsoft.com/graph/apiapplicationtemplate-instantiate?view=graph-rest-beta&tabs=http)

È necessario usare questo ID applicazione 1a4721b3-e57f-4451-ae87-ef078703ec94. DisplayName è l'URL del dominio di Active Directory, se usato nel portale (ad esempio, contoso.com), ma può essere denominato qualcos'altro.

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>Crea processo di sincronizzazione
L'output del comando precedente restituirà il valore objectId dell'entità servizio creata. Per questo esempio, objectId è 614ac0e9-a59b-481f-bd8f-79a73d167e1c.  Usare Microsoft Graph per aggiungere un synchronizationJob a tale entità servizio.  

La documentazione per la creazione di un processo di sincronizzazione è disponibile [qui](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http).

Se l'ID precedente non è stato registrato, è possibile trovare l'entità servizio eseguendo la chiamata MS Graph seguente. Sono necessarie le autorizzazioni directory. Read. all per effettuare la chiamata:
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

Quindi, cercare il nome dell'app nell'output.

Eseguire i due comandi seguenti per creare due processi: uno per il provisioning di utenti/gruppi e uno per la sincronizzazione degli hash delle password. Si tratta della stessa richiesta due volte, ma con ID modello diversi.


Chiamare le due richieste seguenti:

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADProvisioning"
 } 
 ```

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADPasswordHash"
 }
 ```

Se si desidera creare entrambe le chiamate, sarà necessario effettuare due chiamate.

Valore restituito di esempio (per il provisioning):

 ```
HTTP 201/Created
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals('614ac0e9-a59b-481f-bd8f-79a73d167e1c')/synchronization/jobs/$entity",
    "id": "AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da",
    "templateId": "ADDCInPassthrough",
    "schedule": {
        "expiration": null,
        "interval": "PT40M",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "quarantine": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "troubleshootingUrl": null,
        "progress": [],
        "synchronizedEntryCountByType": []
    }
}
```

## <a name="update-targeted-domain"></a>Aggiorna dominio di destinazione
Per questo tenant, l'identificatore dell'oggetto e l'identificatore dell'applicazione dell'entità servizio sono i seguenti:

ObjectId: 8895955e-2e6c-4d79-8943-4d72ca36878f AppId: 00000014-0000-0000-C000-000000000000 DisplayName: testApp

Sarà necessario aggiornare il dominio di cui questa configurazione è destinata, quindi aggiornare i segreti per questo dominio.

Verificare che il nome di dominio usato sia lo stesso URL impostato per il controller di dominio locale

 ```
 PUT https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 Content-type: application/json
 {
  "value": [
    {
      "key": "Domain",
       {"value":[{"key":"Domain","value":"{\"domain\":\"[YOUR_DOMAIN_NAME]\"}"}]}
    }
  ]
 }
 ```

La risposta prevista è... HTTP 204/nessun contenuto

In questo caso, il valore "dominio" evidenziato è il nome del dominio Active Directory locale da cui effettuare il provisioning delle voci per Azure Active Directory.

## <a name="start-sync-job"></a>Avvia processo di sincronizzazione
Il processo può essere recuperato di nuovo tramite il comando seguente:

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

La documentazione per il recupero dei processi è disponibile [qui](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-list?view=graph-rest-beta&tabs=http). 
 
Per avviare il processo, emettere la richiesta, usando il valore objectId dell'entità servizio creata nel primo passaggio e l'identificatore del processo restituito dalla richiesta che ha creato il processo.

La documentazione per informazioni su come avviare un processo è disponibile [qui](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http). 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

La risposta prevista è... Contenuto HTTP 204/No.

Altri comandi per il controllo del processo sono descritti [qui](https://docs.microsoft.com/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta).
 
Per riavviare un processo, si userà...

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>Verifica stato
Recupera gli Stati del processo tramite...

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

Per informazioni rilevanti, vedere la sezione relativa allo stato dell'oggetto restituito

## <a name="next-steps"></a>Passaggi successivi 

- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)
- [Trasformazioni](how-to-transformation.md)
- [API di sincronizzazione Azure AD](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
