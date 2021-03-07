---
title: Usare Azure Pipelines per compilare e distribuire soluzioni HPC
description: Informazioni su come distribuire una pipeline di compilazione/versione per un'applicazione HPC in esecuzione in Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/04/2021
ms.topic: how-to
ms.openlocfilehash: 7170044af58a508ff5a43751cc376f8b8d498444
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435546"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Usare Azure Pipelines per compilare e distribuire soluzioni HPC

Gli strumenti forniti da Azure DevOps possono tradursi in soluzioni di compilazione e test automatizzate di High Performance Computing (HPC). [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) offre una gamma di moderni processi di integrazione continua e distribuzione continua (CD) per la compilazione, la distribuzione, il testing e il monitoraggio del software. Questi processi accelerano la distribuzione del software, consentendo di concentrarsi sul codice anziché supportare l'infrastruttura e le operazioni.

Questo articolo illustra come configurare i processi CI/CD usando [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) per le soluzioni HPC distribuite in Azure batch.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessaria un' [organizzazione di Azure DevOps](/azure/devops/organizations/accounts/create-organization). È anche necessario [creare un progetto in Azure DevOps](/azure/devops/organizations/projects/create-project).

Prima di iniziare, è utile avere una conoscenza di base del [controllo del codice sorgente](/azure/devops/user-guide/source-control) e della [sintassi del modello Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) .

## <a name="create-an-azure-pipeline"></a>Creare una pipeline di Azure

In questo esempio si creerà una pipeline di compilazione e di rilascio per distribuire un'infrastruttura Azure Batch e rilasciare un pacchetto di applicazione. Supponendo che il codice venga sviluppato localmente, questo è il flusso di distribuzione generale:

![Diagramma che mostra il flusso di distribuzione nella pipeline,](media/batch-ci-cd/DeploymentFlow.png)

In questo esempio vengono utilizzati diversi modelli di Azure Resource Manager e file binari esistenti. È possibile copiare questi esempi nel repository ed eseguirne il push in Azure DevOps.

### <a name="understand-the-azure-resource-manager-templates"></a>Informazioni sui modelli di Azure Resource Manager

Questo esempio usa diversi modelli di Azure Resource Manager per distribuire la soluzione. Vengono usati tre modelli di funzionalità (simili a unità o moduli) per implementare un componente specifico di funzionalità. Viene quindi usato un modello di soluzione end-to-end (deployment.json) per distribuire i modelli di funzionalità sottostanti. Questa [struttura di modelli collegati ](../azure-resource-manager/templates/deployment-tutorial-linked-template.md) consente di testare e riutilizzare singolarmente ogni modello di funzionalità in tutte le soluzioni.

![Diagramma che illustra la struttura di un modello collegato con Azure Resource Manager modelli.](media/batch-ci-cd/ARMTemplateHierarchy.png)

Questo modello definisce un account di archiviazione di Azure, che è necessario per distribuire l'applicazione nell'account batch. Per informazioni dettagliate, vedere la [Guida di riferimento del modello Gestione risorse per i tipi di risorse Microsoft. storage](/azure/templates/microsoft.storage/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

Il modello successivo definisce un [account Azure batch](accounts.md). L'account batch funge da piattaforma per eseguire numerose applicazioni tra i [pool](nodes-and-pools.md#pools). Per informazioni dettagliate, vedere la [Guida di riferimento del modello Gestione risorse per i tipi di risorsa Microsoft.Batch](/azure/templates/microsoft.batch/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

Il modello successivo crea un pool di batch nell'account batch. Per informazioni dettagliate, vedere la [Guida di riferimento del modello Gestione risorse per i tipi di risorsa Microsoft.Batch](/azure/templates/microsoft.batch/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

Il modello finale funge da agente di orchestrazione, distribuendo i tre modelli di funzionalità sottostanti.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resource Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resource Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

### <a name="understand-the-hpc-solution"></a>Informazioni sulla soluzione HPC

Come indicato in precedenza, in questo esempio vengono utilizzati diversi modelli di Azure Resource Manager e file binari esistenti. È possibile copiare questi esempi nel repository ed eseguirne il push in Azure DevOps.

Per questa soluzione, ffmpeg viene usato come pacchetto dell'applicazione. È possibile [scaricare il pacchetto ffmpeg](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08) se non è già presente.

![Screenshot della struttura del repository.](media/batch-ci-cd/git-repository.jpg)

Il repository contiene quattro sezioni principali:

- Una cartella **ARM-templates** che contiene i modelli di Azure Resource Manager
- Una cartella **HPC-Application** contenente la versione di Windows a 64 bit di [ffmpeg 4.3.1](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08).
- Una cartella **Pipelines** , che contiene un file YAML che definisce il processo della pipeline di compilazione.
- Facoltativo: cartella **client-Application** , che è una copia dell'esempio di [elaborazione di file .NET Azure batch con ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) . Questa applicazione non è necessaria per questo articolo.


> [!NOTE]
> Questo è solo un esempio di struttura per una codebase. Questo approccio viene usato ai fini della dimostrazione che l'applicazione, l'infrastruttura e il codice della pipeline sono archiviati nello stesso repository.

Ora che il codice sorgente è configurato, è possibile iniziare la prima compilazione.

## <a name="continuous-integration"></a>Integrazione continua

[Azure Pipelines](/azure/devops/pipelines/get-started/), all'interno di Azure DevOps Services, consente di implementare una pipeline di compilazione, test e distribuzione per le applicazioni.

In questa fase della pipeline, i test vengono in genere eseguiti per convalidare il codice e compilare i componenti del software appropriati. Il numero e i tipi di test e le eventuali attività aggiuntive eseguite dipendono dalla strategia di compilazione e versione più ampia.

## <a name="prepare-the-hpc-application"></a>Preparare l'applicazione HPC

Questa sezione illustra come usare la cartella **HPC-Application** . Questa cartella contiene il software (ffmpeg) che verrà eseguito all'interno dell'account Azure Batch.

1. Passare alla sezione Compilazioni di Azure Pipelines nell'organizzazione di Azure DevOps. Creare una **nuova pipeline**.

    ![Screenshot della schermata nuova pipeline.](media/batch-ci-cd/new-build-pipeline.jpg)

1. Per creare una pipeline di compilazione, sono disponibili due opzioni:

    a. [Utilizzare la finestra di progettazione visiva](/azure/devops/pipelines/get-started-designer). A tale scopo, selezionare "usa la finestra di progettazione visiva" nella pagina **nuova pipeline** .

    b. [Usare le compilazioni YAML](/azure/devops/pipelines/get-started-yaml). È possibile creare una nuova pipeline YAML facendo clic sull'opzione Azure Repos o GitHub nella pagina **nuova pipeline** . In alternativa, è possibile archiviare l'esempio seguente nel controllo del codice sorgente e fare riferimento a un file YAML esistente selezionando progettazione visiva, quindi usando il modello YAML.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. Una volta configurata la compilazione in base alle esigenze, selezionare **Salva e accoda**. Se è abilitata l'integrazione continua (nella sezione **Trigger**), la compilazione viene attivata automaticamente quando viene eseguito un nuovo commit nel repository, in base alle condizioni impostate nella compilazione.

    ![Screenshot di una pipeline di compilazione esistente.](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Visualizzare gli aggiornamenti dinamici sullo stato di avanzamento della compilazione in Azure DevOps passando alla sezione **Build** di Azure Pipelines. Selezionare la compilazione appropriata dalla definizione di compilazione.

    ![Screenshot degli output Live da Build in Azure DevOps.](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Se si usa un'applicazione client per eseguire la soluzione HPC, è necessario creare una definizione di compilazione separata per l'applicazione. Per le guide pratiche, vedere la documentazione di [Azure Pipelines](/azure/devops/pipelines/get-started/index).

## <a name="continuous-deployment"></a>Distribuzione continua

Azure Pipelines viene inoltre usato per distribuire l'applicazione e l'infrastruttura sottostante. Le [pipeline di versione](/azure/devops/pipelines/release) consentono la distribuzione continua e automatizzano il processo di rilascio.

### <a name="deploy-your-application-and-underlying-infrastructure"></a>Distribuire l'applicazione e l'infrastruttura sottostante

Per la distribuzione dell'infrastruttura sono necessari alcuni passaggi. Poiché questa soluzione USA [modelli collegati](../azure-resource-manager/templates/linked-templates.md), questi modelli dovranno essere accessibili da un endpoint pubblico (http o HTTPS). Potrebbe trattarsi di un repository in GitHub o di un account di Archiviazione BLOB di Azure o di un'altra posizione di archiviazione. Gli artefatti del modello caricati possono rimanere protetti, in quanto possono essere mantenuti in modalità privata, ma essere comunque accessibili tramite un token di firma di accesso condiviso (SAS).

L'esempio seguente illustra come distribuire un'infrastruttura con modelli da un BLOB di Archiviazione di Azure.

1. Creare una **nuova definizione di versione**, quindi selezionare una definizione vuota. Rinominare l'ambiente appena creato con un elemento pertinente per la pipeline.

    ![Screenshot della pipeline della versione iniziale.](media/batch-ci-cd/Release-0.jpg)

1. Creare una dipendenza dalla pipeline di compilazione per ottenere l'output per l'applicazione HPC.

    > [!NOTE]
    > Prendere nota dell' **alias di origine**, che sarà necessario quando le attività vengono create all'interno della definizione di versione.

    ![Screenshot che mostra un collegamento artefatto a HPCApplicationPackage nella pipeline di compilazione appropriata.](media/batch-ci-cd/Release-1.jpg)

1. Creare un collegamento a un altro artefatto. In questo caso, un repository di Azure. Questa operazione è necessaria per accedere ai modelli di Resource Manager archiviati nel repository. Poiché i modelli di Resource Manager non richiedono la compilazione, non è necessario eseguirne il push tramite una pipeline di compilazione.

    > [!NOTE]
    > Ancora una volta, annotare l' **alias di origine**, perché sarà necessario in un secondo momento.

    ![Screenshot che mostra un collegamento artefatto al Azure Repos.](media/batch-ci-cd/Release-2.jpg)

1. Passare alla sezione **Variabili**. È possibile creare una serie di variabili nella pipeline in modo da non dover immettere di nuovo le stesse informazioni in più attività. In questo esempio vengono utilizzate le variabili seguenti:

   - **applicationStorageAccountName**: nome dell'account di archiviazione che include i file binari dell'applicazione HPC
   - **batchAccountApplicationName**: nome dell'applicazione nell'account batch
   - **batchAccountName**: nome dell'account batch
   - **batchAccountPoolName**: nome del pool di macchine virtuali che esegue l'elaborazione
   - **batchApplicationId**: ID univoco per l'applicazione batch
   - **batchApplicationVersion**: versione semantica dell'applicazione batch (ovvero i file binari ffmpeg)
   - **location**: percorso per le risorse di Azure da distribuire
   - **resourceGroupName**: nome del gruppo di risorse da creare e in cui verranno distribuite le risorse
   - **storageAccountName**: nome dell'account di archiviazione che include i modelli di gestione risorse collegati

   ![Screenshot che mostra le variabili impostate per la versione Azure Pipelines.](media/batch-ci-cd/Release-4.jpg)

1. Passare alle attività per l'ambiente di sviluppo. Nello snapshot seguente è possibile visualizzare sei attività. Queste attività consentono di scaricare i file ffmpeg compressi, distribuire un account di archiviazione per ospitare i modelli annidati di Resource Manager, copiare i modelli di Resource Manager nell'account di archiviazione, distribuire l'account di Azure Batch e le dipendenze necessarie, creare un'applicazione nell'account di Azure Batch e caricare il pacchetto dell'applicazione nell'account di Azure Batch.

    ![Screenshot che illustra le attività usate per rilasciare l'applicazione HPC per Azure Batch.](media/batch-ci-cd/Release-3.jpg)

1. Aggiungere l'attività **Scarica l'artefatto della pipeline (anteprima)** e impostare le proprietà seguenti:
    - **Nome visualizzato:** scaricare il pacchetto dell'applicazione nell'agente
    - **Nome dell'artefatto da scaricare:** hpc-application
    - **Percorso di download:** $(System.DefaultWorkingDirectory)

1. Creare un account di archiviazione per archiviare i modelli di Azure Resource Manager. È possibile usare un account di archiviazione esistente dalla soluzione, ma per supportare questo esempio autonomo e l'isolamento del contenuto, verrà creato un account di archiviazione dedicato.

    Aggiungere l'attività **Distribuzione gruppo di risorse di Azure** e impostare le proprietà seguenti:
    - **Nome visualizzato:** Distribuire un account di archiviazione per i modelli di Gestione risorse
    - **Sottoscrizione di Azure:** Selezionare la sottoscrizione di Azure appropriata
    - **Azione**: Creare o aggiornare un gruppo di risorse
    - **Gruppo di risorse**: $(resourceGroupName)
    - **Posizione**: $(location)
    - **Modello**: $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/storageAccount.json
    - **Override dei parametri del modello**: -accountName $(storageAccountName)

1. Caricare gli elementi dal controllo del codice sorgente nell'account di archiviazione usando Azure Pipelines. Come parte di questa attività Azure Pipelines, l'URI del contenitore dell'account di archiviazione e il token di firma di accesso condiviso possono essere restituiti a una variabile in Azure Pipelines, in modo che possano essere riutilizzati in questa fase dell'agente.

    Aggiungere l'attività **Copia dei file di Azure** e impostare le proprietà seguenti:
    - **Origine:** $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/
    - **Tipo di connessione ad Azure:** Azure Resource Manager
    - **Sottoscrizione di Azure:** Selezionare la sottoscrizione di Azure appropriata
    - **Tipo di destinazione:** BLOB Azure
    - **Account di archiviazione di Resource Manager**: $(storageAccountName)
    - **Nome del contenitore**: templates
    - **URI del contenitore di archiviazione**: templateContainerUri
    - **Token SAS del contenitore di archiviazione**: templateContainerSasToken

1. Distribuire il modello dell'agente di orchestrazione. Questo modello include i parametri per l'URI del contenitore dell'account di archiviazione e il token SAS. Le variabili necessarie nel modello di Gestione risorse sono contenute nella sezione Variables della definizione di versione o sono state impostate da un'altra attività Azure Pipelines (ad esempio, parte dell'attività di copia BLOB di Azure).

    Aggiungere l'attività **Distribuzione gruppo di risorse di Azure** e impostare le proprietà seguenti:
    - **Nome visualizzato**: distribuire Azure Batch
    - **Sottoscrizione di Azure:** Selezionare la sottoscrizione di Azure appropriata
    - **Azione**: Creare o aggiornare un gruppo di risorse
    - **Gruppo di risorse**: $(resourceGroupName)
    - **Posizione**: $(location)
    - **Modello**: $(System.ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/deployment.json
    - **Eseguire l'override dei parametri del modello**: `-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)`

   Una procedura comune consiste nell'usare le attività di Azure Key Vault. Se l'entità servizio connessa alla sottoscrizione di Azure dispone di criteri di accesso appropriati impostati, può scaricare i segreti da un Azure Key Vault e usarli come variabili nella pipeline. Il nome del segreto verrà impostato con il valore associato. È ad esempio possibile fare riferimento a un segreto di sshPassword con $(sshPassword) nella definizione di versione.

1. I passaggi successivi chiamano l'interfaccia della riga di comando di Azure. Il primo viene usato per creare un'applicazione in Azure Batch e caricare i pacchetti associati.

    Aggiungere l'attività **Interfaccia della riga di comando di Azure** e impostare le proprietà seguenti:
    - **Nome visualizzato:** Crea applicazione nell'account Azure Batch
    - **Sottoscrizione di Azure:** Selezionare la sottoscrizione di Azure appropriata
    - **Percorso dello script**: script inline
    - **Script inline**: `az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)`

1. Il secondo passaggio viene usato per caricare i pacchetti associati nell'applicazione (in questo caso, i file ffmpeg).

    Aggiungere l'attività **Interfaccia della riga di comando di Azure** e impostare le proprietà seguenti:
    - **Nome visualizzato:** Carica il pacchetto nell'account Azure Batch
    - **Sottoscrizione di Azure:** Selezionare la sottoscrizione di Azure appropriata
    - **Percorso dello script**: script inline
    - **Script inline**: `az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip`

    > [!NOTE]
    > il numero di versione del pacchetto dell'applicazione è impostato su una variabile. Ciò consente di sovrascrivere le versioni precedenti del pacchetto e di controllare manualmente il numero di versione del pacchetto di cui è stato eseguito il push a Azure Batch.

1. Creare una nuova versione selezionando **Versione > Crea nuova versione**. Una volta attivato, selezionare il collegamento alla nuova versione per visualizzare lo stato.

1. Visualizzare l'output Live dall'agente selezionando il pulsante **log** sotto l'ambiente.

    ![Screenshot che mostra lo stato della versione.](media/batch-ci-cd/Release-5.jpg)

## <a name="test-the-environment"></a>Testare l'ambiente

Dopo aver configurato l'ambiente, verificare che sia possibile completare correttamente i test seguenti.

Connettersi al nuovo account di Azure Batch usando l'interfaccia della riga di comando di Azure da un prompt dei comandi di PowerShell.

- Accedere all'account Azure con `az login` e seguire le istruzioni per l'autenticazione.
- Ora autenticare l'account Batch: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Elencare le applicazioni disponibili

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Verificare che il pool sia valido

```azurecli
az batch pool list
```

Si noti il valore di `currentDedicatedNodes` dall'output di questo comando. Questo valore viene regolato nel test successivo.

#### <a name="resize-the-pool"></a>Ridimensionare il pool

Ridimensionare il pool in modo che siano disponibili nodi di calcolo per il test delle attività e del processo, usare il comando dell'elenco di pool per visualizzare lo stato corrente finché il ridimensionamento non è stato completato e sono disponibili nodi.

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come interagire con un account batch tramite una semplice applicazione, vedere le esercitazioni seguenti.

- [Eseguire un carico di lavoro parallelo con Azure Batch usando l'API Python](tutorial-parallel-python.md)
- [Eseguire un carico di lavoro parallelo con Azure Batch usando l'API .NET](tutorial-parallel-dotnet.md)
