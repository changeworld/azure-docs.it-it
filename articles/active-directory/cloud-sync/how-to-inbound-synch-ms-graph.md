---
title: Sincronizzazione in ingresso per la sincronizzazione cloud con MS API Graph
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
ms.openlocfilehash: e65569cadd8f778a94f93aa22dd3924c52ff12f8
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613771"
---
# <a name="inbound-synchronization-for-cloud-sync-using-ms-graph-api"></a>Sincronizzazione in ingresso per la sincronizzazione cloud con MS API Graph

Il documento seguente descrive come replicare un profilo di sincronizzazione da zero usando solo le API MSGraph.  
La struttura di questa procedura è costituita dai passaggi seguenti.  Ad esempio:

- [Configurazione di base](#basic-setup)
- [Creare entità servizio](#create-service-principals)
- [Crea processo di sincronizzazione](#create-sync-job)
- [Aggiorna dominio di destinazione](#update-targeted-domain)
- [Abilita hash delle password di sincronizzazione](#enable-sync-password-hashes-on-configuration-blade)
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
Successivamente, è necessario creare l' [applicazione/entità servizio AD2AAD](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)

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
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```
 Aggiungere la coppia chiave/valore seguente nella matrice di valori seguente in base a ciò che si sta tentando di eseguire:
 - Abilita sia i flag del tenant pH che la sincronizzazione {Key: "AppKey", valore: "{" appKeyScenario ":" AD2AADPasswordHash "}"}
 
 - Abilita solo flag del tenant di sincronizzazione (non attivare pH) {Key: "AppKey", valore: "{" appKeyScenario ":" AD2AADProvisioning "}"}
 ```
 Request body –
 {
    "value": [
               {
                 "key": "Domain",
                 "value": "{\"domain\":\"ad2aadTest.com\"}"
               }
             ]
  }
```

La risposta prevista è... HTTP 204/nessun contenuto

In questo caso, il valore "dominio" evidenziato è il nome del dominio Active Directory locale da cui effettuare il provisioning delle voci per Azure Active Directory.

## <a name="enable-sync-password-hashes-on-configuration-blade"></a>Abilitare la sincronizzazione degli hash delle password nel pannello di configurazione

 In questa sezione verrà trattata l'abilitazione della sincronizzazione degli hash delle password per una particolare configurazione. Questa operazione è diversa rispetto al segreto AppKey che Abilita il flag di funzionalità a livello di tenant, che è solo per un singolo dominio o configurazione. Per eseguire questa operazione, è necessario impostare la chiave dell'applicazione su pH 1.

1. Acquisisci lo schema (avviso è molto grande) 
 ```
 GET –https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
 ```
2. Eseguire questo mapping dell'attributo CredentialData:
 ``` 
 {
 "defaultValue": null,
 "exportMissingReferences": false,
 "flowBehavior": "FlowWhenChanged",
 "flowType": "Always",
 "matchingPriority": 0,
 "targetAttributeName": "CredentialData",
 "source": {
 "expression": "[PasswordHash]",
 "name": "PasswordHash",
 "type": "Attribute",
 "parameters": []
 }
 ```
3. Individuare i mapping di oggetti seguenti con i nomi seguenti nello schema
 - Provisioning di utenti Active Directory
 - Provisioning Active Directory InetOrgPerson

 I mapping degli oggetti si trovano all'interno dello schema. synchronizationRules [0]. objectMappings (per il momento è possibile presupporre che sia presente solo una regola di sincronizzazione)

4. Eseguire il mapping di CredentialData dal passaggio (2) e inserirlo nei mapping degli oggetti nel passaggio (3)

 Il mapping degli oggetti ha un aspetto simile al seguente:
 ```
 {
 "enabled": true,
 "flowTypes": "Add,Update,Delete",
 "name": "Provision Active Directory users",
 "sourceObjectName": "user",
 "targetObjectName": "User",
 "attributeMappings": [
 ...
 } 
 ```
 Copiare e incollare il mapping dal passaggio **create AD2AADProvisioning e AD2AADPasswordHash Jobs** precedente nella matrice attributeMappings. 

 L'ordine degli elementi in questa matrice non è rilevante (il back-end sarà ordinato). Prestare attenzione quando si aggiunge questo mapping degli attributi se il nome esiste già nella matrice (ad esempio, se è già presente un elemento in attributeMappings con CredentialData targetAttributeName). è possibile che si verifichino errori di conflitto oppure che i mapping preesistenti e nuovi possano essere combinati (in genere non desiderati). Il back-end non viene deduplicato. 

 Ricordarsi di eseguire questa operazione sia per gli utenti che per InetOrgPerson

5. Salvare lo schema creato 
 ```
 PUT –
 https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
```

 Aggiungere lo schema nel corpo della richiesta. 

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

- [Che cos'è Azure AD Connect sincronizzazione cloud?](what-is-cloud-sync.md)
- [Trasformazioni](how-to-transformation.md)
- [API di sincronizzazione Azure AD](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
