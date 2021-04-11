---
title: Integrazione e recapito continui per l'area di lavoro sinapsi
description: Informazioni su come usare l'integrazione e il recapito continui per distribuire le modifiche nell'area di lavoro da un ambiente (sviluppo, test, produzione) a un altro.
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: de3738573bb9bb6f045a45d290c74ba9e6902a5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103561958"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Integrazione e distribuzione continue per l'area di lavoro di Azure sinapsi

## <a name="overview"></a>Panoramica

Integrazione continua (CI) è il processo di automazione della compilazione e del test del codice ogni volta che un membro del team eseguirà il commit delle modifiche al controllo della versione. La distribuzione continua (CD) è il processo per compilare, testare, configurare e distribuire da più ambienti di test o di gestione temporanea in un ambiente di produzione.

Per l'area di lavoro di Azure sinapsi, l'integrazione e la distribuzione continua (CI/CD) spostano tutte le entità da un ambiente (sviluppo, test, produzione) a un altro. Per innalzare di livello l'area di lavoro a un'altra area di lavoro, sono disponibili due parti: usare [modelli di Azure Resource Manager](../../azure-resource-manager/templates/overview.md) per creare o aggiornare le risorse dell'area di lavoro eseguire la migrazione di elementi (script SQL, notebook, definizione del processo Spark, pipeline, set di dati, flussi di dati e così via) con gli strumenti di integrazione continua/recapito continuo sinapsi in Azure DevOps. 

Questo articolo descrive come usare la pipeline di rilascio di Azure per automatizzare la distribuzione di un'area di lavoro sinapsi in più ambienti.

## <a name="prerequisites"></a>Prerequisiti

-   L'area di lavoro usata per lo sviluppo è stata configurata con un repository git in studio, vedere [controllo del codice sorgente in sinapsi Studio](source-control.md).
-   Un progetto DevOps di Azure è stato preparato per l'esecuzione della pipeline di rilascio.

## <a name="set-up-a-release-pipelines"></a>Configurare una pipeline di versione

1.  In [Azure DevOps](https://dev.azure.com/)aprire il progetto creato per la versione.

1.  Sul lato sinistro della pagina selezionare **Pipeline**, quindi selezionare **Versioni**.

    ![Selezionare Pipeline, Versioni](media/create-release-1.png)

1.  Selezionare **Nuova pipeline** oppure, se sono presenti pipeline esistenti, selezionare **Nuova**, quindi **Nuova pipeline di versione**.

1.  Selezionare il modello **Fase vuota**.

    ![Selezionare Fase vuota](media/create-release-select-empty.png)

1.  Nella casella **Nome fase** immettere il nome dell'ambiente.

1.  Selezionare **Aggiungi artefatto**, quindi selezionare il repository git configurato con lo sviluppo sinapsi Studio. Selezionare il repository git usato per la gestione del modello ARM dei pool e dell'area di lavoro. Se si usa GitHub come origine, è necessario creare una connessione del servizio per l'account GitHub e i repository di pull. Per ulteriori informazioni sulla [connessione al servizio](/azure/devops/pipelines/library/service-endpoints) 

    ![Aggiungi ramo di pubblicazione](media/release-creation-github.png)

1.  Selezionare il ramo del modello ARM per l'aggiornamento delle risorse. Per la **versione predefinita**, selezionare **Più recente dal ramo predefinito**.

    ![Aggiungi modello ARM](media/release-creation-arm-branch.png)

1.  Selezionare la [pubblicazione del ramo](source-control.md#configure-publishing-settings) del repository per il **ramo predefinito**. Per impostazione predefinita, questa pubblicazione del ramo è `workspace_publish`. Per la **versione predefinita**, selezionare **Più recente dal ramo predefinito**.

    ![Aggiungere un elemento](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>Configurare un'attività di gestione temporanea per la creazione e l'aggiornamento di risorse ARM 

Aggiungere un'attività di distribuzione Azure Resource Manager per creare o aggiornare le risorse, tra cui l'area di lavoro e i pool:

1. Nella visualizzazione dei passaggi selezionare **Visualizza le attività della fase**.

    ![Visualizzazione dei passaggi](media/release-creation-stage-view.png)

1. Creare una nuova attività. Cercare la **distribuzione del modello ARM** e quindi selezionare **Aggiungi**.

1. Nell'attività di distribuzione selezionare la sottoscrizione, il gruppo di risorse e il percorso dell'area di lavoro di destinazione. Fornire le credenziali, se necessario.

1. Nell'elenco **Azione** selezionare **Creare o aggiornare un gruppo di risorse**.

1. Selezionare il pulsante con i puntini di sospensione ( **...** ) accanto alla casella **Modello**. Cercare il modello di Azure Resource Manager dell'area di lavoro di destinazione

1. Selezionare **...** accanto alla casella **Parametri modello** per scegliere il file dei parametri.

1. Selezionare **...** accanto alla casella **Sostituisci parametri modello** e immettere i valori dei parametri desiderati per l'area di lavoro di destinazione. 

1. Selezionare **Incrementale** per la **Modalità di distribuzione**.
    
    ![distribuzione di area di lavoro e pool](media/pools-resource-deploy.png)

1. Opzionale Aggiungere **Azure PowerShell** per l'assegnazione del ruolo dell'area di lavoro Concedi e aggiorna. Se si usa la pipeline di versione per creare un'area di lavoro sinapsi, l'entità servizio della pipeline viene aggiunta come amministratore dell'area di lavoro predefinito. È possibile eseguire PowerShell per concedere ad altri account l'accesso all'area di lavoro. 
    
    ![Concedi autorizzazione](media/release-creation-grant-permission.png)

 > [!WARNING]
> In modalità di distribuzione completa, le risorse esistenti nel gruppo di risorse, ma non specificate nel nuovo modello di Gestione risorse verranno **eliminate**. Per altre informazioni, vedere [Azure Resource Manager modalità di distribuzione](../../azure-resource-manager/templates/deployment-modes.md)

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>Configurare un'attività di gestione temporanea per la distribuzione di artefatti 

Usare l'estensione per la [distribuzione dell'area](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) di lavoro sinapsi per distribuire altri elementi nell'area di lavoro sinapsi, ad esempio set di dati, script SQL, notebook, definizione del processo Spark, flusso di dati, pipeline, servizio collegato, credenziali e IR (Integration Runtime).  

1. Cercare e ottenere l'estensione da **Azure DevOps Marketplace**(https://marketplace.visualstudio.com/azuredevops) 

     ![Ottenere l'estensione](media/get-extension-from-market.png)

1. Selezionare un'organizzazione in cui installare l'estensione. 

     ![Installare l'estensione](media/install-extension.png)

1. Verificare che all'entità servizio della pipeline di Azure DevOps sia stata concessa l'autorizzazione della sottoscrizione e che sia stata assegnata anche come amministratore dell'area di lavoro per l'area di lavoro 

1. Creare una nuova attività. Cercare la **distribuzione dell'area di lavoro sinapsi**, quindi selezionare **Aggiungi**.

     ![Aggiungi estensione](media/add-extension-task.png)

1.  Nell'attività selezionare **...** accanto alla casella **modello** per scegliere il file modello.

1. Selezionare **...** accanto alla casella **Parametri modello** per scegliere il file dei parametri.

1. Selezionare la connessione, il gruppo di risorse e il nome dell'area di lavoro di destinazione. 

1. Selezionare **...** accanto alla casella **Sostituisci parametri modello** e immettere i valori dei parametri desiderati per l'area di lavoro di destinazione. 

    ![Distribuzione dell'area di lavoro sinapsi](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> Negli scenari CI/CD, il tipo di runtime di integrazione (IR) in ambienti diversi deve essere lo stesso. Ad esempio, se si dispone di un runtime di integrazione self-hosted nell'ambiente di sviluppo, anche lo stesso runtime di integrazione deve essere di tipo self-hosted in altri ambienti, come quelli di test e produzione. Analogamente, se si condividono runtime di integrazione tra più fasi, è necessario configurarli come self-hosted collegati in tutti gli ambienti, ad esempio quelli di sviluppo, test e produzione.

## <a name="create-release-for-deployment"></a>Crea versione per la distribuzione 

Dopo aver salvato tutte le modifiche, è possibile selezionare **Crea versione** per creare manualmente una versione. Per automatizzare la creazione di versioni, vedere [Trigger versione di Azure DevOps](/azure/devops/pipelines/release/triggers)

   ![Selezionare Crea versione](media/release-creation-manually.png)

## <a name="use-custom-parameters-of-the-workspace-template"></a>Utilizzare parametri personalizzati del modello di area di lavoro 

Si usa l'integrazione continua/recapito continuo automatizzata e si desidera modificare alcune proprietà durante la distribuzione, ma le proprietà non sono parametrizzate per impostazione predefinita. In questo caso, è possibile eseguire l'override del modello di parametro predefinito.

Per eseguire l'override del modello di parametro predefinito, è necessario creare un modello di parametro personalizzato, un file denominato **template-parameters-definition.js** nella cartella radice del ramo di collaborazione git. È necessario usare il nome file esatto. Quando si esegue la pubblicazione dal ramo collaborazione, l'area di lavoro di sinapsi legge questo file e usa la relativa configurazione per generare i parametri. Se non viene trovato alcun file, viene utilizzato il modello di parametro predefinito.

### <a name="custom-parameter-syntax"></a>Sintassi dei parametri personalizzata

Di seguito sono riportate alcune linee guida per la creazione del file di parametri personalizzati:

* Immettere il percorso della proprietà nel tipo di entità pertinente.
* L'impostazione di un nome di proprietà su `*` indica che si desidera parametrizzare tutte le proprietà al suo interno (solo per il primo livello, non in modo ricorsivo). È anche possibile fornire eccezioni a questa configurazione.
* Quando si imposta il valore di una proprietà come stringa, si indica che si vuole parametrizzare la proprietà. Usare il formato `<action>:<name>:<stype>`.
   *  `<action>` può essere uno di questi caratteri:
      * `=` indica che il valore corrente viene mantenuto come valore predefinito per il parametro.
      * `-` significa che non si mantiene il valore predefinito per il parametro.
      * `|` è un caso speciale per i segreti Azure Key Vault per le stringhe di connessione o le chiavi.
   * `<name>` è il nome del parametro. Se è vuoto, viene usato il nome della proprietà. Se il valore inizia con un carattere `-`, il nome viene abbreviato. Ad esempio, `AzureStorage1_properties_typeProperties_connectionString` viene abbreviato in `AzureStorage1_connectionString`.
   * `<stype>` tipo di parametro. Se `<stype>` è vuoto, il tipo predefinito è `string` . Valori supportati: `string` , `securestring` , `int` , `bool` , `object` `secureobject` e `array` .
* La specifica di una matrice nel file indica che la proprietà corrispondente nel modello è una matrice. La sinapsi esegue l'iterazione di tutti gli oggetti nella matrice utilizzando la definizione specificata. Il secondo oggetto, una stringa, diventa il nome della proprietà, che viene usato come nome per il parametro per ogni iterazione.
* Una definizione non può essere specifica di un'istanza di risorsa. Qualunque definizione viene applicata a tutte le risorse di quel tipo.
* Per impostazione predefinita, vengono parametrizzate tutte le stringhe sicure, ad esempio i segreti di Key Vault, e le stringhe sicure, ad esempio le stringhe di connessione, le chiavi e i token.

### <a name="parameter-template-definition-samples"></a>Esempi di definizione di modello di parametro 

Di seguito è riportato un esempio di come appare la definizione di un modello di parametro:

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

* Qualsiasi proprietà nel percorso `properties/bigDataPool/referenceName` è parametrizzata con il relativo valore predefinito. È possibile parametrizzare il pool di Spark collegato per ogni file del notebook. 

#### <a name="sql-scripts"></a>Script SQL 

* Le proprietà (poolname e DatabaseName) nel percorso `properties/content/currentConnection` vengono parametrizzate come stringhe senza i valori predefiniti nel modello. 

#### <a name="pipelines"></a>Pipeline

* Qualunque proprietà nel percorso `activities/typeProperties/waitTimeInSeconds` è parametrizzata. Qualunque attività in una pipeline che dispone di una proprietà a livello di codice denominata `waitTimeInSeconds` (ad esempio, l'attività `Wait`) viene parametrizzata come numero, con un nome predefinito. Non avrà tuttavia un valore predefinito nel modello di Resource Manager. Sarà un input obbligatorio durante la distribuzione di Resource Manager.
* Analogamente, una proprietà chiamata `headers` (ad esempio, in un' `Web` attività) è parametrizzata con il tipo `object` (oggetto). Ha un valore predefinito, che è lo stesso valore di quello della factory di origine.

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

Se si usa l'integrazione git con l'area di lavoro sinapsi e si ha una pipeline di integrazione continua/distribuzione continua che sposta le modifiche dallo sviluppo al test e quindi alla produzione, è consigliabile eseguire le procedure consigliate seguenti:

-   **Integrazione di Git**. Configurare solo l'area di lavoro sinapsi di sviluppo con l'integrazione git. Le modifiche alle aree di lavoro di test e produzione vengono distribuite tramite CI/CD e non richiedono l'integrazione git.
-   **Preparare i pool prima della migrazione degli artefatti**. Se è presente un notebook o uno script SQL collegato ai pool nell'area di lavoro di sviluppo, è previsto lo stesso nome dei pool in ambienti diversi. 
-   **Infrastruttura come codice (IaC)**. La gestione dell'infrastruttura (reti, macchine virtuali, bilanciamenti del carico e topologia di connessione) in un modello descrittivo usa lo stesso controllo delle versioni usato dal team di DevOps per il codice sorgente. 
-   **Altre**. Vedere le [procedure consigliate per gli artefatti ADF](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)

## <a name="troubleshooting-artifacts-deployment"></a>Risoluzione dei problemi di distribuzione degli artefatti 

### <a name="use-the-synapse-workspace-deployment-task"></a>Usare l'attività di distribuzione dell'area di lavoro sinapsi

In sinapsi sono presenti diversi artefatti che non sono risorse ARM. Questo comportamento è diverso da Azure Data Factory. L'attività di distribuzione del modello ARM non funzionerà correttamente per distribuire gli artefatti sinapsi
 
### <a name="unexpected-token-error-in-release"></a>Errore di token imprevisto nella versione

Quando il file di parametri contiene valori di parametro che non sono preceduti da un carattere di escape, la pipeline di versione non riuscirà ad analizzare il file e genererà l'errore "token imprevisto". È consigliabile eseguire l'override dei parametri o usare Azure Vault per recuperare i valori dei parametri. È anche possibile usare caratteri di escape doppi come soluzione alternativa.
