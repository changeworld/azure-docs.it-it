---
title: Aggiornare continuamente il codice dell'app per le funzioni Azure DevOps
description: Informazioni su come configurare una pipeline Azure DevOps destinazione Funzioni di Azure.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli
ms.openlocfilehash: a99e313a0c3fe9093137d4acaa64e789ef5e10e3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762210"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Recapito continuo tramite Azure DevOps

È possibile distribuire automaticamente la funzione in un'app Funzioni di Azure usando [Azure Pipelines](/azure/devops/pipelines/).

Sono disponibili due opzioni per definire la pipeline:

- **File YAML:** un file YAML descrive la pipeline. Il file potrebbe avere una sezione delle istruzioni di compilazione e una sezione di versione. Il file YAML deve essere nello stesso repo dell'app.
- **Modello:** i modelli sono attività pronte per compilare o distribuire l'app.

## <a name="yaml-based-pipeline"></a>Pipeline basata su YAML

Per creare una pipeline basata su YAML, compilare prima l'app e quindi distribuire l'app.

### <a name="build-your-app"></a>Compilare l'app

La modalità di compilazione dell'app Azure Pipelines dipende dal linguaggio di programmazione dell'app. Ogni linguaggio dispone di istruzioni di compilazione specifiche che creano un elemento di distribuzione. Un artefatto di distribuzione viene usato per distribuire l'app per le funzioni in Azure.

# <a name="c"></a>[C\#](#tab/csharp)

È possibile usare l'esempio seguente per creare un file YAML per compilare un'app .NET:

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: false
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

È possibile usare l'esempio seguente per creare un file YAML per compilare un'app JavaScript:

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

È possibile usare uno degli esempi seguenti per creare un file YAML per compilare un'app per una versione specifica di Python. Python è supportato solo per le app per le funzioni in esecuzione in Linux.

**Versione 3.7**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**Versione 3.6**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

È possibile usare l'esempio seguente per creare un file YAML per creare un pacchetto di un'app PowerShell. PowerShell è supportato solo per Windows Funzioni di Azure.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>Distribuire l'app

È necessario includere uno degli esempi YAML seguenti nel file YAML, a seconda del sistema operativo host.

#### <a name="windows-function-app"></a>App per le funzioni di Windows

È possibile usare il frammento di codice seguente per distribuire un'app per le funzioni di Windows:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>App per le funzioni Linux

È possibile usare il frammento di codice seguente per distribuire un'app per le funzioni Linux:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>Pipeline basata su modello

I modelli Azure DevOps sono gruppi predefiniti di attività che compilano o distribuiscono un'app.

### <a name="build-your-app"></a>Compilare l'app

La modalità di compilazione dell'app in Azure Pipelines dipende dal linguaggio di programmazione dell'app. Ogni linguaggio ha istruzioni di compilazione specifiche che creano un artefatto di distribuzione. Un artefatto di distribuzione viene usato per aggiornare l'app per le funzioni in Azure.

Per usare i modelli di compilazione predefiniti, quando si crea una nuova pipeline di compilazione, selezionare Usa **l'editor** classico per creare una pipeline usando i modelli di progettazione.

![Selezionare l'Azure Pipelines classico](media/functions-how-to-azure-devops/classic-editor.png)

Dopo aver configurato l'origine del codice, cercare i Funzioni di Azure di compilazione. Selezionare il modello che corrisponde alla lingua dell'app.

![Selezionare un modello Funzioni di Azure compilazione](media/functions-how-to-azure-devops/build-templates.png)

In alcuni casi, gli artefatti di compilazione hanno una struttura di cartelle specifica. Potrebbe essere necessario selezionare la casella **di controllo Prepend root folder name to archive paths** (Antepone il nome della cartella radice ai percorsi di archiviazione).

![Opzione per anteporre il nome della cartella radice](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>App JavaScript

Se l'app JavaScript ha una dipendenza dai moduli nativi di Windows, è necessario aggiornare la versione del pool di agenti a **Hosted VS2017**.

![Aggiornare la versione del pool di agenti](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Distribuire l'app

Quando si crea una nuova pipeline di versione, cercare il modello Funzioni di Azure versione.

![Cercare il modello Funzioni di Azure versione](media/functions-how-to-azure-devops/release-template.png)

La distribuzione in uno slot di distribuzione non è supportata nel modello di versione.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Creare una pipeline di compilazione usando l'interfaccia della riga di comando di Azure

Per creare una pipeline di compilazione in Azure, usare il `az functionapp devops-pipeline create` [comando](/cli/azure/functionapp/devops-pipeline#az_functionapp_devops_pipeline_create). La pipeline di compilazione viene creata per compilare e rilasciare tutte le modifiche al codice apportate nel repo. Il comando genera un nuovo file YAML che definisce la pipeline di compilazione e rilascio e quindi esegue il commit nel proprio repo. I prerequisiti per questo comando dipendono dal percorso del codice.

- Se il codice si trova in GitHub:

    - È necessario disporre **delle autorizzazioni di** scrittura per la sottoscrizione.

    - È necessario essere l'amministratore del progetto in Azure DevOps.

    - È necessario disporre delle autorizzazioni per creare un token di accesso personale (PAT) GitHub con autorizzazioni sufficienti. Per altre informazioni, vedere [Requisiti di autorizzazione di GitHub PAT.](/azure/devops/pipelines/repos/github#repository-permissions-for-personal-access-token-pat-authentication)

    - È necessario disporre delle autorizzazioni per eseguire il commit nel ramo principale nel repository GitHub in modo da poter eseguire il commit del file YAML rigenerato automaticamente.

- Se il codice è in Azure Repos:

    - È necessario disporre **delle autorizzazioni di** scrittura per la sottoscrizione.

    - È necessario essere l'amministratore del progetto in Azure DevOps.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare la panoramica [Funzioni di Azure.](functions-overview.md)
- Esaminare la [Azure DevOps panoramica.](/azure/devops/pipelines/)