---
title: Pubblicazione automatica per l'integrazione e il recapito continui
description: Informazioni su come pubblicare automaticamente per l'integrazione e il recapito continui.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 496d2b6b3d669013c8174e9bc961d0a2f640bed3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462083"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Pubblicazione automatica per l'integrazione e il recapito continui

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Panoramica

L'integrazione continua è la procedura che consente di testare ogni modifica apportata alla base di codici automaticamente. Il prima possibile, il recapito continuo segue i test che si verificano durante l'integrazione continua e inserisce le modifiche in un sistema di gestione temporanea o di produzione.

In Azure Data Factory, l'integrazione continua e il recapito continuo (CI/CD) implicano lo sviluppo di pipeline Data Factory da un ambiente, ad esempio sviluppo, test e produzione, a un altro. Data Factory USA [modelli di Azure Resource Manager (modelli ARM)](../azure-resource-manager/templates/overview.md) per archiviare la configurazione delle varie entità Data Factory, ad esempio pipeline, set di dati e flussi di dati.

Per alzare di livello una data factory a un altro ambiente, esistono due metodi consigliati:

- Distribuzione automatizzata tramite l'integrazione di Data Factory con [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Caricamento manuale di un modello ARM usando Data Factory l'integrazione dell'esperienza utente con Azure Resource Manager.

Per ulteriori informazioni, vedere [integrazione continua e recapito in Azure Data Factory](continuous-integration-deployment.md).

Questo articolo è incentrato sui miglioramenti della distribuzione continua e sulla funzionalità di pubblicazione automatica per CI/CD.

## <a name="continuous-deployment-improvements"></a>Miglioramenti della distribuzione continua

La funzionalità di pubblicazione automatizzata consente di usare le funzionalità per la **convalida** e l' **esportazione di modelli ARM** dall'esperienza utente Data Factory e rende la logica utilizzabile tramite un pacchetto NPM disponibile pubblicamente [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . Per questo motivo, è possibile attivare le azioni a livello di codice anziché passare all'interfaccia utente di Data Factory e selezionare un pulsante manualmente. Questa funzionalità offrirà alle pipeline CI/CD un'esperienza di integrazione continuata.

### <a name="current-cicd-flow"></a>Flusso CI/CD corrente

1. Ogni utente apporta modifiche nei rami privati.
1. Il push al master non è consentito. Gli utenti devono creare una richiesta pull per apportare modifiche.
1. Gli utenti devono caricare l'interfaccia utente di Data Factory e selezionare **pubblica** per distribuire le modifiche Data Factory e generare i modelli ARM nel ramo di pubblicazione.
1. La pipeline di versione DevOps è configurata in modo da creare una nuova versione e distribuire il modello ARM ogni volta che viene effettuato il push di una nuova modifica nel ramo di pubblicazione.

![Diagramma che mostra il flusso CI/CD corrente.](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Passaggio manuale

Nel flusso CI/CD corrente, l'esperienza utente è l'intermediario per creare il modello ARM. Di conseguenza, un utente deve passare all'interfaccia utente di Data Factory e selezionare manualmente **pubblica** per avviare la generazione del modello ARM e rilasciarla nel ramo di pubblicazione.

### <a name="the-new-cicd-flow"></a>Nuovo flusso CI/CD

1. Ogni utente apporta modifiche nei rami privati.
1. Il push al master non è consentito. Gli utenti devono creare una richiesta pull per apportare modifiche.
1. La compilazione della pipeline di Azure DevOps viene attivata ogni volta che viene eseguito un nuovo commit nel database master. Convalida le risorse e genera un modello ARM come artefatto se la convalida ha esito positivo.
1. La pipeline di versione DevOps è configurata in modo da creare una nuova versione e distribuire il modello ARM ogni volta che è disponibile una nuova compilazione.

![Diagramma che mostra il nuovo flusso CI/CD.](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Cosa è cambiato?

- A questo punto è disponibile un processo di compilazione che usa una pipeline di compilazione DevOps.
- La pipeline di compilazione usa il pacchetto NPM ADFUtilities, che convaliderà tutte le risorse e genererà i modelli ARM. Questi modelli possono essere singoli e collegati.
- La pipeline di compilazione è responsabile della convalida Data Factory risorse e della generazione del modello ARM anziché dell'interfaccia utente di Data Factory (pulsante **pubblica** ).
- La definizione di versione DevOps utilizzerà ora la nuova pipeline di compilazione anziché l'artefatto git.

> [!NOTE]
> È possibile continuare a usare il meccanismo esistente, ovvero il `adf_publish` ramo, oppure è possibile usare il nuovo flusso. Entrambi sono supportati.

## <a name="package-overview"></a>Panoramica del pacchetto

Nel pacchetto sono attualmente disponibili due comandi:

- Esportare un modello di Azure Resource Manager
- Convalida

### <a name="export-arm-template"></a>Esportare un modello di Azure Resource Manager

Eseguire `npm run start export <rootFolder> <factoryId> [outputFolder]` per esportare il modello ARM usando le risorse di una determinata cartella. Questo comando esegue anche un controllo di convalida prima di generare il modello ARM. Ecco un esempio:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- `RootFolder` è un campo obbligatorio che rappresenta la posizione in cui si trovano le risorse Data Factory.
- `FactoryId` è un campo obbligatorio che rappresenta l'ID di risorsa Data Factory nel formato `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .
- `OutputFolder` è un parametro facoltativo che specifica il percorso relativo per salvare il modello ARM generato.
 
> [!NOTE]
> Il modello ARM generato non viene pubblicato nella versione live della factory. La distribuzione deve essere eseguita usando una pipeline CI/CD.
 
### <a name="validate"></a>Convalida

Eseguire `npm run start validate <rootFolder> <factoryId>` per convalidare tutte le risorse di una determinata cartella. Ecco un esempio:

```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- `RootFolder` è un campo obbligatorio che rappresenta la posizione in cui si trovano le risorse Data Factory.
- `FactoryId` è un campo obbligatorio che rappresenta l'ID di risorsa Data Factory nel formato `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .

## <a name="create-an-azure-pipeline"></a>Creare una pipeline di Azure

Sebbene i pacchetti NPM possano essere utilizzati in diversi modi, uno dei principali vantaggi viene utilizzato tramite la [pipeline di Azure](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0). Per ogni merge nel ramo di collaborazione, è possibile attivare una pipeline che prima convalida tutto il codice e quindi Esporta il modello ARM in un [artefatto di compilazione](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) che può essere utilizzato da una pipeline di versione. Il modo in cui differisce dal processo di integrazione continua/recapito continuo corrente è che la *pipeline di rilascio verrà indirizzata a questo artefatto anziché al `adf_publish` ramo esistente*.

Per iniziare, seguire questa procedura:

1.  Aprire un progetto DevOps di Azure e passare a **pipeline**. Selezionare **nuova pipeline**.

    ![Screenshot che mostra il pulsante nuova pipeline.](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
1.  Selezionare il repository in cui si vuole salvare lo script YAML della pipeline. Si consiglia di salvarlo in una cartella di compilazione nello stesso repository delle risorse Data Factory. Verificare che nell'archivio sia presente un *package.jssul* file che contiene il nome del pacchetto, come illustrato nell'esempio seguente:

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.3"
        }
    } 
    ```
    
1.  Selezionare **pipeline Starter**. Se il file YAML è stato caricato o Unito, come illustrato nell'esempio seguente, è anche possibile puntare direttamente a tale file e modificarlo.

    ![Screenshot che mostra la pipeline di avvio.](media/continuous-integration-deployment-improvements/starter-pipeline.png)

    ```yaml
    # Sample YAML file to validate and export an ARM template into a build artifact
    # Requires a package.json file located in the target repository
    
    trigger:
    - main #collaboration branch
    
    pool:
      vmImage: 'ubuntu-latest'
    
    steps:
    
    # Installs Node and the npm packages saved in your package.json file in the build
    
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    
    - task: Npm@1
      inputs:
        command: 'install'
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the Data Factory resources in the repository. You'll get the same validation errors as when "Validate All" is selected.
    # Enter the appropriate subscription and name for the source factory.
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder, which is the same as selecting "Publish" from the UX.
    # The ARM template generated isn't published to the live version of the factory. Deployment should be done by using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the artifact to be used as a source for a release pipeline.
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

1.  Immettere il codice YAML. Si consiglia di usare il file YAML come punto di partenza.
1.  Salvare ed eseguire. Se è stato usato YAML, viene attivato ogni volta che viene aggiornato il ramo principale.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'integrazione e il recapito continui, vedere Data Factory:

- [Integrazione continua e recapito in Azure Data Factory](continuous-integration-deployment.md).
