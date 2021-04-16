---
title: Come configurare la sincronizzazione cloud a livello di codice usando MS API Graph
description: Questo argomento descrive come abilitare la sincronizzazione in ingresso usando solo il API Graph
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
ms.openlocfilehash: 8fe220cf7b5cb8b67e5ab7ded221494e89a28aa5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530266"
---
# <a name="how-to-programmatically-configure-cloud-sync-using-ms-graph-api"></a>Come configurare la sincronizzazione cloud a livello di codice usando MS API Graph

Il documento seguente descrive come replicare un profilo di sincronizzazione da zero usando solo le API MSGraph.  
La struttura di questa operazione è costituita dai passaggi seguenti.  ovvero:

- [Come configurare la sincronizzazione cloud a livello di codice usando MS API Graph](#how-to-programmatically-configure-cloud-sync-using-ms-graph-api)
  - [Configurazione di base](#basic-setup)
    - [Abilitare i flag del tenant](#enable-tenant-flags)
  - [Creare entità servizio](#create-service-principals)
  - [Creare un processo di sincronizzazione](#create-sync-job)
  - [Aggiornare il dominio di destinazione](#update-targeted-domain)
  - [Abilitare gli hash delle password di sincronizzazione nel pannello di configurazione](#enable-sync-password-hashes-on-configuration-blade)
  - [Eliminazioni accidentali](#accidental-deletes)
    - [Abilitazione e impostazione della soglia](#enabling-and-setting-the-threshold)
    - [Consentire le eliminazioni](#allowing-deletes)
  - [Avviare il processo di sincronizzazione](#start-sync-job)
  - [Verificare lo stato](#review-status)
  - [Passaggi successivi](#next-steps)

Usare questi [Modulo di Microsoft Azure Active Directory per Windows PowerShell](/powershell/module/msonline/) comandi per abilitare la sincronizzazione per un tenant di produzione, un requisito preliminare per poter chiamare il servizio Web di amministrazione per tale tenant.

## <a name="basic-setup"></a>Configurazione di base

### <a name="enable-tenant-flags"></a>Abilitare i flag del tenant

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
Il primo di questi due comandi richiede Azure Active Directory credenziali. Questi commandlet identificano in modo implicito il tenant e lo abilitano per la sincronizzazione.

## <a name="create-service-principals"></a>Creare entità servizio
Successivamente, è necessario creare l'applicazione [AD2AAD o l'entità servizio](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http&preserve-view=true)

È necessario usare questo ID applicazione 1a4721b3-e57f-4451-ae87-ef078703ec94. DisplayName è l'URL del dominio AD, se usato nel portale (ad esempio, contoso.com), ma può essere denominato altro.

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>Creare un processo di sincronizzazione
L'output del comando precedente restituirà l'objectId dell'entità servizio creata. Per questo esempio, objectId è 614ac0e9-a59b-481f-bd8f-79a73d167e1c.  Usare Microsoft Graph per aggiungere un processo di sincronizzazione a tale entità servizio.  

La documentazione per la creazione di un processo di sincronizzazione è disponibile [qui.](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta&preserve-view=true)

Se non è stato registrato l'ID precedente, è possibile trovare l'entità servizio eseguendo la chiamata MS Graph seguente. Per eseguire questa chiamata, sono necessarie le autorizzazioni Directory.Read.All:
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

Cercare quindi il nome dell'app nell'output.

Eseguire i due comandi seguenti per creare due processi: uno per il provisioning di utenti/gruppi e uno per la sincronizzazione dell'hash delle password. Si tratta della stessa richiesta due volte, ma con ID modello diversi.


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

Se si vogliono creare entrambe le chiamate, saranno necessarie due chiamate.

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

## <a name="update-targeted-domain"></a>Aggiornare il dominio di destinazione
Per questo tenant, l'identificatore di oggetto e l'identificatore dell'applicazione dell'entità servizio sono i seguenti:

ObjectId: 8895955e-2e6c-4d79-8943-4d72ca36878f AppId: 000000014-0000-0000-c000-00000000000000 DisplayName: testApp

Sarà necessario aggiornare il dominio di destinazione della configurazione, quindi aggiornare i segreti per questo dominio.

Assicurarsi che il nome di dominio in uso sia lo stesso URL impostato per il controller di dominio locale

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```
 Aggiungere la coppia chiave/valore seguente nella matrice di valori seguente in base alle operazioni che si sta tentando di eseguire:
 - Abilitare sia PHS che i flag del tenant di sincronizzazione { key: "AppKey", valore: "{"appKeyScenario":"AD2AADPasswordHash"}" }
 
 - Abilitare solo il flag del tenant di sincronizzazione (non attivare PHS) { chiave: "AppKey", valore: "{"appKeyScenario":"AD2AADProvisioning"}" }
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

La risposta prevista è ... HTTP 204/Nessun contenuto

In questo caso, il valore "Dominio" evidenziato è il nome del dominio Active Directory locale da cui eseguire il provisioning delle voci Azure Active Directory.

## <a name="enable-sync-password-hashes-on-configuration-blade"></a>Abilitare La sincronizzazione degli hash delle password nel pannello di configurazione

 Questa sezione tratta l'abilitazione della sincronizzazione degli hash delle password per una particolare configurazione. Questo è diverso dal segreto AppKey che abilita il flag di funzionalità a livello di tenant, che è solo per un singolo dominio/configurazione. È necessario impostare la chiave dell'applicazione su PHS per il funzionamento end-to-end.

1. Recuperare lo schema (avviso che è piuttosto grande) 
 ```
 GET –https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
 ```
2. Eseguire il mapping dell'attributo CredentialData seguente:
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
3. Trovare i mapping degli oggetti seguenti con i nomi seguenti nello schema
 - Effettuare il provisioning degli utenti di Active Directory
 - Effettuare il provisioning di inetOrgPersons di Active Directory

 I mapping degli oggetti sono all'interno di schema.synchronizationRules[0].objectMappings (per il momento è possibile presupporre che sia presente una sola regola di sincronizzazione)

4. Eseguire il mapping credentialData del passaggio (2) e inserirlo nei mapping degli oggetti nel passaggio (3)

 Il mapping degli oggetti è simile al seguente:
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
 Copiare e incollare il mapping dei processi **Create AD2AADProvisioning e AD2AADPasswordHash** precedenti nella matrice attributeMappings. 

 L'ordine degli elementi in questa matrice non è importante (il back-end verrà ordinato per l'utente). Prestare attenzione ad aggiungere questo mapping di attributi se il nome esiste già nella matrice ,ad esempio se è già presente un elemento in attributeMappings con targetAttributeName CredentialData. È possibile che si siano verificati errori di conflitto o che i mapping preesistenti e nuovi possano essere combinati insieme (in genere non desiderato). Il back-end non esegue la dedupazione per l'utente. 

 Ricordarsi di eseguire questa operazione sia per Gli utenti che per inetOrgpersons

5. Salvare lo schema creato 
 ```
 PUT –
 https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
```

 Aggiungere lo schema nel corpo della richiesta. 

## <a name="accidental-deletes"></a>Eliminazioni accidentali
Questa sezione illustra come abilitare/disabilitare a livello di codice e usare [eliminazioni accidentali a](how-to-accidental-deletes.md) livello di codice.


### <a name="enabling-and-setting-the-threshold"></a>Abilitazione e impostazione della soglia
Sono disponibili due impostazioni per processo che è possibile usare:

 - DeleteThresholdEnabled: abilita la prevenzione accidentale delle eliminazioni per il processo quando è impostato su 'true'. Impostare su 'true' per impostazione predefinita.
 - DeleteThresholdValue: definisce il numero massimo di eliminazioni consentite in ogni esecuzione del processo quando è abilitata la prevenzione delle eliminazioni accidentali. Il valore è impostato su 500 per impostazione predefinita.  Pertanto, se il valore è impostato su 500, il numero massimo di eliminazioni consentite sarà 499 in ogni esecuzione.

Le impostazioni di soglia di eliminazione fanno parte di `SyncNotificationSettings` e possono essere modificate tramite grafo. 

Sarà necessario aggiornare SyncNotificationSettings di destinazione della configurazione, quindi aggiornare i segreti.

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```

 Aggiungere la coppia chiave/valore seguente nella matrice di valori seguente in base alle operazioni che si sta tentando di eseguire:

```
 Request body -
 {
   "value":[
             {
               "key":"SyncNotificationSettings",
               "value": "{\"Enabled\":true,\"Recipients\":\"foobar@xyz.com\",\"DeleteThresholdEnabled\":true,\"DeleteThresholdValue\":50}"
              }
            ]
  }


```

L'impostazione "Abilitato" nell'esempio precedente consente di abilitare o disabilitare i messaggi di posta elettronica di notifica quando il processo viene messo in quarantena.


Attualmente non sono supportate le richieste PATCH per i segreti, quindi è necessario aggiungere tutti i valori nel corpo della richiesta PUT(come nell'esempio precedente) per mantenere gli altri valori.

I valori esistenti per tutti i segreti possono essere recuperati usando 

```
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
```

### <a name="allowing-deletes"></a>Consentire le eliminazioni
Per consentire il flusso delle eliminazioni dopo che il processo è passato in quarantena, è necessario eseguire un riavvio con solo "ForceDeletes" come ambito. 

```
Request:
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

```
Request Body:
{
  "criteria": {"resetScope": "ForceDeletes"}
}
```






## <a name="start-sync-job"></a>Avviare il processo di sincronizzazione
Il processo può essere recuperato di nuovo tramite il comando seguente:

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

La documentazione per il recupero dei processi è disponibile [qui.](/graph/api/synchronization-synchronizationjob-list?tabs=http&view=graph-rest-beta&preserve-view=true) 
 
Per avviare il processo, mettere la richiesta usando l'objectId dell'entità servizio creata nel primo passaggio e l'identificatore del processo restituito dalla richiesta che ha creato il processo.

La documentazione su come avviare un processo è disponibile [qui.](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta&preserve-view=true) 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

La risposta prevista è ... HTTP 204/Nessun contenuto.

Altri comandi per il controllo del processo sono documentati [qui.](/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta&preserve-view=true)
 
Per riavviare un processo, si userebbe ...

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>Verificare lo stato
Recuperare gli stati dei processi tramite ...

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

Per informazioni dettagliate, vedere la sezione "status" dell'oggetto restituito

## <a name="next-steps"></a>Passaggi successivi 

- [Che cos'è Azure AD Connect cloud?](what-is-cloud-sync.md)
- [Trasformazioni](how-to-transformation.md)
- [Azure AD'API di sincronizzazione](/graph/api/resources/synchronization-overview?view=graph-rest-beta&preserve-view=true)
