---
title: Integrazione e recapito continui per l'area di lavoro synapse
description: Informazioni su come usare l'integrazione e il recapito continui per distribuire le modifiche nell'area di lavoro da un ambiente (sviluppo, test, produzione) a un altro.
author: liudan66
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 833478d956560c981bd6cc3ba03b48bb602f563c
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739675"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Integrazione e recapito continui per l'Azure Synapse lavoro

## <a name="overview"></a>Panoramica

L'integrazione continua (CI, Continuous Integration) è il processo di automazione della compilazione e del test del codice ogni volta che un membro del team esegue il commit delle modifiche al controllo della versione. La distribuzione continua è il processo di compilazione, test, configurazione e distribuzione da più ambienti di test o di staging in un ambiente di produzione.

In un Azure Synapse Analytics di lavoro, l'integrazione e il recapito continui (CI/CD) sposta tutte le entità da un ambiente (sviluppo, test, produzione) a un altro. Per alzare di livello l'area di lavoro a un'altra area di lavoro, sono disponibili due parti. Usare prima di tutto un [modello Azure Resource Manager (modello arm)](../../azure-resource-manager/templates/overview.md) per creare o aggiornare le risorse dell'area di lavoro (pool e area di lavoro). Eseguire quindi la migrazione degli artefatti (script SQL, notebook, definizione del processo Spark, pipeline, set di dati, flussi di dati e così via) con gli strumenti ci/CD di Azure Synapse Analytics in Azure DevOps. 

Questo articolo illustra come usare una pipeline di Azure DevOps per automatizzare la distribuzione di un'Azure Synapse di lavoro in più ambienti.

## <a name="prerequisites"></a>Prerequisiti

Questi prerequisiti e configurazioni devono essere soddisfatti per automatizzare la distribuzione di un'Azure Synapse di lavoro in più ambienti.

### <a name="azure-devops"></a>Azure DevOps

- Un Azure DevOps è stato preparato per l'esecuzione della pipeline di versione.
- [Concedere a tutti gli utenti che archiviano il codice "Basic"](/azure/devops/organizations/accounts/add-organization-users?view=azure-devops&tabs=preview-page)l'accesso a livello di organizzazione, in modo che possano visualizzare il repo.
- Concedere diritti di proprietario al Azure Synapse di lavoro.
- Assicurarsi di aver creato un agente Azure DevOps macchina virtuale self-hosted o di usare un Azure DevOps ospitato.
- Autorizzazioni per [creare una connessione Azure Resource Manager servizio per il gruppo di risorse](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml).
- Un amministratore Azure Active Directory (Azure AD) deve installare l'estensione Azure DevOps di lavoro [synapse](/azure/devops/marketplace/install-extension)Deployment Agent nell'organizzazione Azure DevOps.
- Creare o designare un account del servizio esistente per l'esecuzione della pipeline. È possibile usare un token di accesso personale invece di un account del servizio, ma le pipeline non funzioneranno dopo l'eliminazione dell'account utente.

### <a name="azure-active-directory"></a>Azure Active Directory

- In Azure AD creare un'entità servizio da usare per la distribuzione. L'attività Distribuzione dell'area di lavoro Synapse non supporta l'uso di un'identità gestita nella versione 1* e versioni precedenti.
- Azure AD diritti di amministratore sono necessari per questa azione.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

> [!NOTE]
> È possibile automatizzare e distribuire questi prerequisiti usando la stessa pipeline, un modello arm o l'interfaccia della riga di comando di Azure, ma il processo non è descritto in questo articolo.

- L'area di lavoro "source" usata per lo sviluppo deve essere configurata con un repository Git in Synapse Studio. Per altre informazioni, vedere [Controllo del codice sorgente in Synapse Studio](source-control.md#configuration-method-2-manage-hub).

- Area di lavoro vuota in cui eseguire la distribuzione. Per configurare l'area di lavoro vuota:

  1. Creare una nuova area Azure Synapse Analytics lavoro.
  1. Concedere all'agente di macchine virtuali e al collaboratore dell'entità servizio i diritti per il gruppo di risorse in cui è ospitata la nuova area di lavoro.
  1. Nella nuova area di lavoro non configurare la connessione al repository Git.
  1. Nella portale di Azure trovare la nuova area di lavoro Azure Synapse Analytics e concedere a se stessi e a chi eseguirà la pipeline Azure DevOps Azure Synapse Analytics diritti di proprietario dell'area di lavoro. 
  1. Aggiungere l'Azure DevOps macchina virtuale e l'entità servizio al ruolo Collaboratore per l'area di lavoro (questa operazione deve essere stata ereditata, ma verificarne l'identità).
  1. Nell'Azure Synapse Analytics di lavoro passare a **Studio**  >  **Manage**  >  **IAM**(Gestione IAM di Studio). Aggiungere l'Azure DevOps macchina virtuale e l'entità servizio al gruppo amministratori dell'area di lavoro.
  1. Aprire l'account di archiviazione usato per l'area di lavoro. In IAM aggiungere l'agente vm e l'entità servizio al ruolo Collaboratore selezione dati BLOB di archiviazione.
  1. Creare un insieme di credenziali delle chiavi nella sottoscrizione di supporto e assicurarsi che sia l'area di lavoro esistente che la nuova area di lavoro abbia almeno le autorizzazioni GET e LIST per l'insieme di credenziali.
  1. Per il funzionamento della distribuzione automatizzata, assicurarsi che tutte le stringhe di connessione specificate nei servizi collegati siano presenti nell'insieme di credenziali delle chiavi.

### <a name="additional-prerequisites"></a>Prerequisiti aggiuntivi
 
 - I pool di Spark e i runtime di integrazione self-hosted non vengono creati in una pipeline. Se si dispone di un servizio collegato che usa un runtime di integrazione self-hosted, crearne uno manualmente nella nuova area di lavoro.
 - Se si sviluppano notebook e sono connessi a un pool di Spark, creare nuovamente il pool di Spark nell'area di lavoro.
 - I notebook collegati a un pool di Spark che non esistono in un ambiente non verranno distribuiti.
 - I nomi dei pool di Spark devono essere gli stessi in entrambe le aree di lavoro.
 - Assegnare a tutti i database, ai pool SQL e ad altre risorse lo stesso nome in entrambe le aree di lavoro.
 - Se i pool SQL di cui è stato effettuato il provisioning vengono sospesi quando si tenta di eseguire la distribuzione, la distribuzione potrebbe non riuscire.

Per altre informazioni, vedere [CI CD in Azure Synapse Analytics Part 4 - The Release Pipeline](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434). 


## <a name="set-up-a-release-pipeline"></a>Configurare una pipeline di versione

1.  In [Azure DevOps](https://dev.azure.com/)aprire il progetto creato per la versione.

1.  Sul lato sinistro della pagina selezionare **Pipeline**, quindi selezionare **Versioni**.

    ![Selezionare Pipeline, Versioni](media/create-release-1.png)

1.  Selezionare **Nuova pipeline** oppure, se sono presenti pipeline esistenti, selezionare **Nuova**, quindi **Nuova pipeline di versione**.

1.  Selezionare il modello **Fase vuota**.

    ![Selezionare Fase vuota](media/create-release-select-empty.png)

1.  Nella casella **Nome fase** immettere il nome dell'ambiente.

1.  Selezionare **Aggiungi artefatto** e quindi selezionare il repository Git configurato con il Synapse Studio. Selezionare il repository Git usato per gestire il modello arm di pool e area di lavoro. Se si usa GitHub come origine, è necessario creare una connessione al servizio per l'account GitHub ed eseguire il pull dei repository. Per altre informazioni sulla connessione [al servizio](/azure/devops/pipelines/library/service-endpoints) 

    ![Aggiungere un ramo di pubblicazione](media/release-creation-github.png)

1.  Selezionare il ramo del modello di Arm per l'aggiornamento delle risorse. Per la **versione predefinita**, selezionare **Più recente dal ramo predefinito**.

    ![Aggiungere un modello di Arm](media/release-creation-arm-branch.png)

1.  Selezionare la [pubblicazione del ramo](source-control.md#configure-publishing-settings) del repository per il **ramo predefinito**. Per impostazione predefinita, questa pubblicazione del ramo è `workspace_publish`. Per la **versione predefinita**, selezionare **Più recente dal ramo predefinito**.

    ![Aggiungere un elemento](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-an-arm-template-to-create-and-update-resource"></a>Configurare un'attività di gestione delle fasi per un modello arm per creare e aggiornare una risorsa 

Se si dispone di un modello arm per distribuire una risorsa, ad esempio un'area di lavoro di Azure Synapse, pool Spark e SQL o un insieme di credenziali delle chiavi, aggiungere un'attività di distribuzione di Azure Resource Manager per creare o aggiornare tali risorse:

1. Nella visualizzazione dei passaggi selezionare **Visualizza le attività della fase**.

    ![Visualizzazione dei passaggi](media/release-creation-stage-view.png)

1. Creare una nuova attività. Cercare Distribuzione **di modelli arm** e quindi selezionare **Aggiungi**.

1. Nell'attività Distribuzione selezionare la sottoscrizione, il gruppo di risorse e il percorso per l'area di lavoro di destinazione. Fornire le credenziali, se necessario.

1. Nell'elenco **Azione** selezionare **Creare o aggiornare un gruppo di risorse**.

1. Selezionare il pulsante con i puntini di sospensione ( **...** ) accanto alla casella **Modello**. Cercare il modello Azure Resource Manager'area di lavoro di destinazione

1. Selezionare **...** accanto alla casella **Parametri modello** per scegliere il file dei parametri.

1. Selezionare **...** accanto alla casella Override template parameters (Sostituisci **parametri modello)** e immettere i valori dei parametri desiderati per l'area di lavoro di destinazione. 

1. Selezionare **Incrementale** per la **Modalità di distribuzione**.
    
    ![distribuzione di aree di lavoro e pool](media/pools-resource-deploy.png)

1. (Facoltativo) Aggiungere Azure PowerShell per **l'assegnazione** di ruolo con concessione e aggiornamento dell'area di lavoro. Se si usa la pipeline di versione per creare un'area di lavoro Synapse, l'entità servizio della pipeline viene aggiunta come amministratore predefinito dell'area di lavoro. È possibile eseguire PowerShell per concedere ad altri account l'accesso all'area di lavoro. 
    
    ![concedere l'autorizzazione](media/release-creation-grant-permission.png)

 > [!WARNING]
> In modalità di distribuzione completa, le risorse presenti nel gruppo di risorse ma non specificate nel nuovo modello Resource Manager verranno **eliminate.** Per altre informazioni, vedere Azure Resource Manager [modalità di distribuzione](../../azure-resource-manager/templates/deployment-modes.md)

## <a name="set-up-a-stage-task-for-synapse-artifacts-deployment"></a>Configurare un'attività di fase per la distribuzione di artefatti Synapse 

Usare l'estensione per la distribuzione dell'area di lavoro [Synapse](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) per distribuire altri elementi nell'area di lavoro Synapse, ad esempio set di dati, script SQL, notebook, definizione del processo Spark, flusso di dati, pipeline, servizio collegato, credenziali e IR (Integration Runtime).  

1. Cercare e ottenere l'estensione da **Azure DevOps marketplace**(https://marketplace.visualstudio.com/azuredevops) 

     ![Ottenere l'estensione](media/get-extension-from-market.png)

1. Selezionare un'organizzazione per installare l'estensione. 

     ![Installare l'estensione](media/install-extension.png)

1. Assicurarsi che all Azure DevOps'entità servizio della pipeline sia stata concessa l'autorizzazione della sottoscrizione e che sia stata assegnata anche come amministratore dell'area di lavoro per l'area di lavoro di destinazione. 

1. Creare una nuova attività. Cercare distribuzione **dell'area di lavoro Synapse** e quindi selezionare **Aggiungi**.

     ![Aggiungere l'estensione](media/add-extension-task.png)

1.  Nell'attività selezionare **...** accanto alla **casella Modello** per scegliere il file modello.

1. Selezionare **...** accanto alla casella **Parametri modello** per scegliere il file dei parametri.

1. Selezionare la connessione, il gruppo di risorse e il nome dell'area di lavoro di destinazione. 

1. Selezionare **...** accanto alla casella **Sostituisci** parametri modello e immettere i valori dei parametri desiderati per l'area di lavoro di destinazione, incluse le stringhe di connessione e le chiavi dell'account usate nei servizi collegati. [Fare clic qui per altre informazioni] (https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434)

    ![Distribuzione dell'area di lavoro synapse](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> Negli scenari CI/CD, il tipo di runtime di integrazione (IR) in ambienti diversi deve essere lo stesso. Ad esempio, se si dispone di un runtime di integrazione self-hosted nell'ambiente di sviluppo, anche lo stesso runtime di integrazione deve essere di tipo self-hosted in altri ambienti, come quelli di test e produzione. Analogamente, se si condividono runtime di integrazione tra più fasi, è necessario configurarli come self-hosted collegati in tutti gli ambienti, ad esempio quelli di sviluppo, test e produzione.

## <a name="create-release-for-deployment"></a>Creare una versione per la distribuzione 

Dopo aver salvato tutte le modifiche, è possibile selezionare **Crea versione** per creare manualmente una versione. Per automatizzare la creazione di versioni, vedere [Trigger versione di Azure DevOps](/azure/devops/pipelines/release/triggers)

   ![Selezionare Crea versione](media/release-creation-manually.png)

## <a name="use-custom-parameters-of-the-workspace-template"></a>Usare parametri personalizzati del modello di area di lavoro 

Si usano CI/CD automatizzati e si vogliono modificare alcune proprietà durante la distribuzione, ma le proprietà non sono parametrizzate per impostazione predefinita. In questo caso, è possibile eseguire l'override del modello di parametro predefinito.

Per eseguire l'override del modello di parametro predefinito, è necessario creare un modello di parametro personalizzato, un file denominato **template-parameters-definition.js** on nella cartella radice del ramo git collaboration. È necessario usare il nome file esatto. Durante la pubblicazione dal ramo di collaborazione, l'area di lavoro Synapse leggerà questo file e userà la relativa configurazione per generare i parametri. Se non viene trovato alcun file, viene usato il modello di parametro predefinito.

### <a name="custom-parameter-syntax"></a>Sintassi dei parametri personalizzata

Di seguito sono riportate alcune linee guida per la creazione del file di parametri personalizzati:

* Immettere il percorso della proprietà nel tipo di entità pertinente.
* L'impostazione di un nome di proprietà su indica che si desidera parametrizzare tutte le proprietà al suo sotto (solo fino al primo `*` livello, non in modo ricorsivo). È anche possibile fornire eccezioni a questa configurazione.
* Quando si imposta il valore di una proprietà come stringa, si indica che si vuole parametrizzare la proprietà. Usare il formato `<action>:<name>:<stype>`.
   *  `<action>` può essere uno dei caratteri seguenti:
      * `=` significa mantenere il valore corrente come valore predefinito per il parametro.
      * `-` significa che non mantenere il valore predefinito per il parametro .
      * `|` è un caso speciale per i segreti di Azure Key Vault per le stringhe di connessione o le chiavi.
   * `<name>` è il nome del parametro. Se è vuoto, viene usato il nome della proprietà. Se il valore inizia con un carattere `-`, il nome viene abbreviato. Ad esempio, `AzureStorage1_properties_typeProperties_connectionString` viene abbreviato in `AzureStorage1_connectionString`.
   * `<stype>` è il tipo di parametro. Se `<stype>` è vuoto, il tipo predefinito è `string` . Valori supportati: `string` , , , , e `securestring` `int` `bool` `object` `secureobject` `array` .
* Se si specifica una matrice nel file, la proprietà corrispondente nel modello è una matrice. Synapse scorre tutti gli oggetti nella matrice usando la definizione specificata. Il secondo oggetto, una stringa, diventa il nome della proprietà, che viene usato come nome per il parametro per ogni iterazione.
* Una definizione non può essere specifica di un'istanza di risorsa. Qualunque definizione viene applicata a tutte le risorse di quel tipo.
* Per impostazione predefinita, vengono parametrizzate tutte le stringhe sicure, ad esempio i segreti di Key Vault, e le stringhe sicure, ad esempio le stringhe di connessione, le chiavi e i token.

### <a name="parameter-template-definition-samples"></a>Esempi di definizione del modello di parametro 

Di seguito è riportato un esempio dell'aspetto di una definizione di modello di parametro:

```json
{
"Microsoft.Synapse/workspaces/notebooks": {
        "properties":{
            "bigDataPool":{
                "referenceName": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/sqlscripts": {
     "properties": {
         "content":{
             "currentConnection":{
                    "*":"-"
                 }
            } 
        }
    },
    "Microsoft.Synapse/workspaces/pipelines": {
        "properties": {
            "activities": [{
                 "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.Synapse/workspaces/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                     "*": "="
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.Synapse/workspaces/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```

Di seguito è riportata una spiegazione del modo in cui viene costruito il modello precedente, suddiviso per tipo di risorsa.

#### <a name="notebooks"></a>Notebook 

* Qualsiasi proprietà nel percorso `properties/bigDataPool/referenceName` viene parametrizzata con il relativo valore predefinito. È possibile parametrizzare il pool Spark collegato per ogni file di notebook. 

#### <a name="sql-scripts"></a>Script SQL 

* Le proprietà (poolName e databaseName) nel percorso vengono parametrizzate come `properties/content/currentConnection` stringhe senza i valori predefiniti nel modello. 

#### <a name="pipelines"></a>Pipeline

* Qualunque proprietà nel percorso `activities/typeProperties/waitTimeInSeconds` è parametrizzata. Qualunque attività in una pipeline che dispone di una proprietà a livello di codice denominata `waitTimeInSeconds` (ad esempio, l'attività `Wait`) viene parametrizzata come numero, con un nome predefinito. Non avrà tuttavia un valore predefinito nel modello di Resource Manager. Sarà un input obbligatorio durante la distribuzione di Resource Manager.
* Analogamente, una proprietà denominata `headers` (ad esempio, in `Web` un'attività) viene parametrizzata con tipo `object` (Object). Ha un valore predefinito, che è lo stesso valore di quello della factory di origine.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Tutte le proprietà nel percorso `typeProperties` vengono parametrizzate con i rispettivi valori predefiniti. Esistono ad esempio due proprietà nelle proprietà del tipo `IntegrationRuntimes`: `computeProperties` e `ssisProperties`. Entrambi i tipi di proprietà vengono creati con i rispettivi valori e tipi predefiniti (oggetto).

#### <a name="triggers"></a>Trigger

* In `typeProperties`, sono parametrizzate due proprietà. La prima è `maxConcurrency`, che è specificata per avere un valore predefinito ed è di tipo`string`. Ha il nome di parametro predefinito `<entityName>_properties_typeProperties_maxConcurrency`.
* Anche la proprietà `recurrence` è parametrizzata. Al suo interno, tutte le proprietà a tale livello vengono specificate per essere parametrizzate come stringhe, con valori predefiniti e nomi di parametro. Un'eccezione è la proprietà `interval`, che è parametrizzata come tipo `int`. Il nome del parametro ha il suffisso `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Analogamente, la proprietà `freq` è una stringa e viene parametrizzata come stringa. Tuttavia, la proprietà `freq` è parametrizzata senza un valore predefinito. Il nome viene abbreviato e seguito da un suffisso. Ad esempio: `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* I servizi collegati sono univoci. Poiché i servizi collegati e i set di dati hanno un'ampia gamma di tipi, è possibile fornire una personalizzazione specifica del tipo. In questo esempio, per tutti i servizi collegati di tipo `AzureDataLakeStore`, verrà applicato un modello specifico. Per tutti gli altri (tramite `*`), verrà applicato un modello diverso.
* La proprietà `connectionString` verrà parametrizzata come valore `securestring`. Non avrà un valore predefinito. Avrà un nome di parametro abbreviato con il suffisso `connectionString`.
* La proprietà `secretAccessKey` è un `AzureKeyVaultSecret`, ad esempio in un servizio collegato Amazon S3. Viene parametrizzata automaticamente come segreto di Azure Key Vault e recuperata dall'insieme di credenziali delle chiavi configurato. È anche possibile parametrizzare l'insieme di credenziali delle chiavi stesso.

#### <a name="datasets"></a>Set di dati

* Sebbene la personalizzazione specifica del tipo sia disponibile per i set di dati, è possibile fornire la configurazione senza avere esplicitamente una configurazione a livello di \*. Nell'esempio precedente, vengono parametrizzate tutte le proprietà del set di dati in `typeProperties`.


## <a name="best-practices-for-cicd"></a>Procedure consigliate per la pipeline CI/CD

Se si usa l'integrazione git con l'area di lavoro Azure Synapse e si dispone di una pipeline CI/CD che sposta le modifiche dallo sviluppo al test e quindi all'ambiente di produzione, è consigliabile usare queste procedure consigliate:

-   **Integrazione di Git**. Configurare solo l'area di Azure Synapse di sviluppo con l'integrazione git. Le modifiche alle aree di lavoro di test e produzione vengono distribuite tramite CI/CD e non è necessaria l'integrazione con Git.
-   **Preparare i pool prima della migrazione degli elementi**. Se è presente uno script SQL o un notebook collegato ai pool nell'area di lavoro di sviluppo, è previsto lo stesso nome dei pool in ambienti diversi. 
-   **Infrastruttura come codice (IaC).** La gestione dell'infrastruttura (reti, macchine virtuali, servizi di bilanciamento del carico e topologia di connessione) in un modello descrittivo usa lo stesso controllo delle versioni utilizzato dal team DevOps per il codice sorgente. 
-   **Altri**. Vedere [le procedure consigliate per gli artefatti di AdF](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)

## <a name="troubleshooting-artifacts-deployment"></a>Risoluzione dei problemi relativi alla distribuzione di artefatti 

### <a name="use-the-azure-synapse-analytics-workspace-deployment-task"></a>Usare l'attività Azure Synapse Analytics di distribuzione dell'area di lavoro

In Azure Synapse Analytics sono presenti diversi elementi che non sono risorse arm. Questo comportamento è diverso da Azure Data Factory. L'attività di distribuzione del modello arm non funzionerà correttamente per distribuire Azure Synapse Analytics artefatti.
 
### <a name="unexpected-token-error-in-release"></a>Errore imprevisto del token nella versione

Quando il file di parametri contiene valori di parametro che non sono preceduti da caratteri di escape, la pipeline di rilascio non riuscirà ad analizzare il file e genererà l'errore "token imprevisto". È consigliabile eseguire l'override dei parametri o Azure Key Vault per recuperare i valori dei parametri. È anche possibile usare caratteri di escape doppi come soluzione alternativa.
