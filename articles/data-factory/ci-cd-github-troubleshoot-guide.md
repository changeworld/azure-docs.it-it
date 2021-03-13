---
title: Risolvere i problemi relativi a CI-CD, Azure DevOps e GitHub in ADF
description: Usare metodi diversi per risolvere i problemi relativi a CI-CD in ADF.
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 03/12/2021
ms.openlocfilehash: 4be015b1a8ba4b6fc6ea3acc74318f9a8b298e8e
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418097"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>Risolvere i problemi relativi a CI-CD, Azure DevOps e GitHub in ADF 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra i metodi comuni per la risoluzione dei problemi per la distribuzione di Integration-Continuous continua (CI-CD), Azure DevOps e GitHub in Azure Data Factory.

In caso di domande o problemi nell'uso delle tecniche di controllo del codice sorgente o DevOps, di seguito sono riportati alcuni articoli che possono risultare utili:

- Per informazioni sul funzionamento del controllo del codice sorgente in ADF, vedere [controllo del codice sorgente in ADF](source-control.md) . 
- Vedere  [ci-CD in ADF](continuous-integration-deployment.md) per altre informazioni su come DevOps ci-CD viene praticato in ADF.
 
## <a name="common-errors-and-messages"></a>Errori comuni e messaggi

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>La connessione al repository Git non è riuscita a causa di un tenant diverso

#### <a name="issue"></a>Problema
    
A volte si verificano problemi di autenticazione come lo stato HTTP 401. In particolare quando si hanno più tenant con account Guest, le cose potrebbero diventare più complesse.

#### <a name="cause"></a>Causa

Ciò che abbiamo osservato è che il token è stato ottenuto dal tenant originale, ma ADF si trova nel tenant Guest e tenta di usare il token per visitare DevOps nel tenant Guest. Questo non è il comportamento previsto.

#### <a name="recommendation"></a>Recommendation

Usare invece il token emesso dal tenant Guest. Ad esempio, è necessario assegnare lo stesso Azure Active Directory come tenant Guest e DevOps, in modo che sia possibile impostare correttamente il comportamento del token e usare il tenant corretto.

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>I parametri del modello nel file dei parametri non sono validi

#### <a name="issue"></a>Problema

Se si elimina un trigger in dev Branch, che è già disponibile nel ramo di test o di produzione con la **stessa** configurazione, ad esempio Frequency e Interval, la distribuzione della pipeline di rilascio ha esito positivo e il trigger corrispondente verrà eliminato nei rispettivi ambienti. Tuttavia, se si dispone di una configurazione **diversa** (ad esempio frequenza e intervallo) per il trigger negli ambienti di test/produzione e se si elimina lo stesso trigger in dev, la distribuzione avrà esito negativo e si verificherà un errore.

#### <a name="cause"></a>Causa

La pipeline CI/CD ha esito negativo con l'errore seguente:

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>Recommendation

L'errore si verifica perché spesso si elimina un trigger, che è parametrizzato, quindi i parametri non saranno disponibili nel modello ARM, perché il trigger non esiste più. Poiché il parametro non è più presente nel modello ARM, è necessario aggiornare i parametri sottoposti a override nella pipeline DevOps. In caso contrario, ogni volta che i parametri nel modello ARM cambiano, devono aggiornare i parametri sottoposti a override nella pipeline DevOps (nell'attività di distribuzione).

### <a name="updating-property-type-is-not-supported"></a>Il tipo di proprietà di aggiornamento non è supportato

#### <a name="issue"></a>Problema

La pipeline di rilascio CI/CD ha esito negativo con l'errore seguente:

`
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
`

#### <a name="cause"></a>Causa

Questo è dovuto a un runtime di integrazione con lo stesso nome nella Factory di destinazione ma con un tipo diverso. Integration Runtime deve essere dello stesso tipo durante la distribuzione.

#### <a name="recommendation"></a>Recommendation

- Vedere le procedure consigliate per CI/CD di seguito:

    https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd 
- I runtime di integrazione non cambiano spesso e sono simili in tutte le fasi dell'integrazione continua/recapito continuo, quindi Data Factory si prevede di avere lo stesso nome e tipo di runtime di integrazione in tutte le fasi di CI/CD. Se il nome e i tipi & proprietà sono diversi, verificare che corrispondano alla configurazione del runtime di integrazione di origine e di destinazione e quindi distribuire la pipeline di rilascio.
- Se si desidera condividere runtime di integrazione in tutte le fasi, è consigliabile usare una factory ternaria per contenere solo i runtime di integrazione condivisi. È possibile usare questa factory condivisa in tutti gli ambienti come tipo di runtime di integrazione collegato.

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>Impossibile creare o aggiornare il documento a causa di un riferimento non valido

#### <a name="issue"></a>Problema

Quando si tenta di pubblicare le modifiche in un Data Factory, viene riportato il messaggio di errore seguente:

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`

#### <a name="symptom"></a>Sintomo

La configurazione di Git è stata scollegata e configurata di nuovo con il flag "Importa risorse" selezionato, che imposta il Data Factory come "sincronizzato". Ciò significa che non viene modificata la pubblicazione.

#### <a name="resolution"></a>Soluzione

Scollegare la configurazione git e configurarla di nuovo e assicurarsi di non selezionare la casella di controllo "Importa risorse esistenti".

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>Data Factory spostare il failover da un gruppo di risorse a un altro

#### <a name="issue"></a>Problema

Non è possibile spostare Data Factory da un gruppo di risorse a un altro, senza errori con l'errore seguente:

`
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>Soluzione

Per consentire l'operazione di spostamento, è necessario eliminare i componenti SSIS-IR e l'IRs condiviso. Se non si desidera eliminare i runtime di integrazione, il modo migliore consiste nel seguire il documento di copia e clonazione per eseguire la copia e, al termine, eliminare la vecchia Data Factory.

###  <a name="unable-to-export-and-import-arm-template"></a>Non è possibile esportare e importare il modello ARM

#### <a name="issue"></a>Problema

Non è possibile esportare e importare il modello ARM. Tuttavia, nella traccia del browser non si è verificato alcun errore nel portale. è possibile che venga visualizzato l'errore seguente:

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>Causa

È stato creato un ruolo del cliente come utente e non dispone dell'autorizzazione necessaria. Quando la factory viene caricata nell'interfaccia utente, viene controllata una serie di valori di controllo dell'esposizione per la factory. In questo caso, il ruolo di accesso dell'utente non è autorizzato ad accedere all'API *queryFeaturesValue* . Per accedere a questa API, la funzionalità parametri globali è disattivata. Il percorso del codice di esportazione ARM si basa in parte sulla funzionalità parametri globali.

#### <a name="resolution"></a>Soluzione

Per risolvere il problema, è necessario aggiungere l'autorizzazione seguente al ruolo: *Microsoft. DataFactory/factorys/queryFeaturesValue/Action*. Per impostazione predefinita, questa autorizzazione deve essere inclusa nel ruolo "collaboratore Data Factory".

###  <a name="automatic-publishing-for-cicd-without-clicking-publish-button"></a>Pubblicazione automatica per CI/CD senza fare clic sul pulsante pubblica  

#### <a name="issue"></a>Problema

La pubblicazione manuale con il pulsante clic nel portale di ADF non Abilita l'operazione di integrazione continua/recapito continuo automatica.

#### <a name="cause"></a>Causa

Fino a poco tempo fa, solo un modo per pubblicare la pipeline di ADF per le distribuzioni stava usando il pulsante del portale di ADF. A questo punto, è possibile rendere automatico il processo. 

#### <a name="resolution"></a>Soluzione

Il processo CI/CD è stato migliorato. La funzionalità di **pubblicazione automatica** accetta, convalida ed Esporta tutte le funzionalità del modello di Azure Resource Manager (ARM) dall'UX di ADF. Rende la logica utilizzabile tramite un pacchetto NPM disponibile pubblicamente [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . In questo modo è possibile attivare le azioni a livello di codice anziché passare all'interfaccia utente di ADF e fare clic su un pulsante. Ciò offre alle pipeline di integrazione continua/recapito continuo una **vera** e propria esperienza di integrazione continua. Per informazioni dettagliate, seguire i [miglioramenti alla pubblicazione ci/CD di ADF](./continuous-integration-deployment-improvements.md) . 

###  <a name="cannot-publish-because-of-4mb-arm-template-limit"></a>Impossibile pubblicare a causa del limite del modello ARM da 4 MB  

#### <a name="issue"></a>Problema

Non è possibile eseguire la distribuzione perché si raggiunge Azure Resource Manager limite di 4 MB di dimensioni totali del modello. È necessaria una soluzione da distribuire dopo aver oltrepassato il limite. 

#### <a name="cause"></a>Causa

Azure Resource Manager limita le dimensioni del modello a 4 MB. Limitare le dimensioni del modello a 4 MB e ogni file di parametri a 64 KB. Il limite di 4 MB si applica allo stato finale del modello dopo che è stato espanso con le definizioni di risorse iterative e i valori per variabili e parametri. Il limite è stato superato. 

#### <a name="resolution"></a>Soluzione

Per le piccole e medie soluzioni, un modello singolo è più facile da comprendere e gestire. È possibile vedere tutti i valori e le risorse in un unico file. Per gli scenari avanzati, i modelli collegati consentono di suddividere la soluzione in componenti di destinazione. Seguire le procedure consigliate per l' [uso di modelli collegati e annidati](../azure-resource-manager/templates/linked-templates.md?tabs=azure-powershell).

### <a name="cannot-connect-to-git-enterprise-cloud"></a>Non è possibile connettersi a GIT Enterprise Cloud 

##### <a name="issue"></a>Problema

Non è possibile connettersi a GIT Enterprise Cloud a causa di problemi di autorizzazione. È possibile visualizzare un errore simile a **422-entità non elaborabile.**

#### <a name="cause"></a>Causa

* Si usa Git Enterprise nel server locale. 
* OAuth non è stato configurato per ADF. 
* L'URL non è configurato correttamente.

##### <a name="resolution"></a>Soluzione

Per prima cosa si concede l'accesso OAuth ad ADF. Quindi, è necessario usare l'URL corretto per connettersi a GIT Enterprise. La configurazione deve essere impostata sulle organizzazioni del cliente. Ad esempio, ADF tenterà *https://hostname/api/v3/search/repositories?q=user%3 <customer credential> ....* at First e fail. Quindi tenterà *https://hostname/api/v3/orgs/ <org> / <repo> ...* e avrà esito positivo. 
 
### <a name="recover-from-a-deleted-data-factory"></a>Ripristino da un data factory eliminato

#### <a name="issue"></a>Problema
Data Factory eliminata dal cliente o il gruppo di risorse che contiene il Data Factory. Desidera ottenere informazioni su come ripristinare un data factory eliminato.

#### <a name="cause"></a>Causa

È possibile ripristinare il Data Factory solo se il cliente dispone del controllo del codice sorgente configurato (DevOps o Git). Verrà riportata tutta la risorsa pubblicata più recente e **non verrà** ripristinata la pipeline non pubblicata, il set di dati e il servizio collegato.

Se non è presente alcun controllo del codice sorgente, il recupero di un Data Factory eliminato dal back-end non è possibile perché quando il servizio riceve il comando Deleted, l'istanza viene eliminata e non è stato archiviato alcun backup.

#### <a name="resolution"></a>Soluzione

Per ripristinare il Data Factory eliminato con controllo del codice sorgente, vedere i passaggi seguenti:

 * Creare una nuova Azure Data Factory.

 * Riconfigurare git con le stesse impostazioni, ma assicurarsi di importare le risorse di Data Factory esistenti nel repository selezionato e scegliere nuovo ramo.

 * Creare una richiesta pull per unire le modifiche al ramo di collaborazione e pubblicare.

 * Se il cliente avesse un Integration Runtime self-hosted in ADF eliminato, dovrà creare una nuova istanza in un nuovo ADF, disinstallare e reinstallare l'istanza nel computer locale o nella macchina virtuale con la nuova chiave ottenuta. Al termine dell'installazione del runtime di integrazione, il cliente dovrà modificare il servizio collegato in modo che punti al nuovo runtime di integrazione e testare la connessione oppure avrà esito negativo con errore di **riferimento non valido.**



## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum di stack overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)
