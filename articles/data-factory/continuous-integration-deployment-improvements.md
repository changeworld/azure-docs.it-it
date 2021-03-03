---
title: Pubblicazione automatica per l'integrazione e il recapito continui
description: Informazioni su come pubblicare automaticamente per l'integrazione e il recapito continui.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 49ec43e59989f3fdad8f5731867953cc7cbb5757
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699709"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Pubblicazione automatica per l'integrazione e il recapito continui

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Panoramica

L'integrazione continua è la pratica di testare automaticamente ogni modifica apportata alla codebase e il prima possibile recapito continuo segue il test che si verifica durante l'integrazione continua e inserisce le modifiche in un sistema di gestione temporanea o di produzione.

In Azure Data Factory per integrazione e recapito continui (CI/CD) si intende lo spostamento delle pipeline di Data Factory da un ambiente (sviluppo, test, produzione) a un altro. Azure Data Factory usa [modelli di Azure Resource Manager](../azure-resource-manager/templates/overview.md) per archiviare la configurazione di varie entità ADF (pipeline, set di dati, flussi di dati e così via). Per alzare di livello una data factory a un altro ambiente, esistono due metodi consigliati:

- Distribuzione automatizzata tramite l'integrazione di Data Factory con [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Caricamento manuale di un modello di Resource Manager usando l'integrazione di Data Factory UX con Azure Resource Manager.

Per ulteriori informazioni, vedere [integrazione continua e recapito in Azure Data Factory](continuous-integration-deployment.md).

Questo articolo è incentrato sui miglioramenti della distribuzione continua e sulla funzionalità di pubblicazione automatica per CI/CD.

## <a name="continuous-deployment-improvements"></a>Miglioramenti della distribuzione continua

La funzionalità di "pubblicazione automatizzata" accetta le funzionalità del modello di *convalida di tutte* le *Azure Resource Manager (ARM)* dall'UX di ADF e rende la logica utilizzabile tramite un pacchetto NPM disponibile pubblicamente [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . In questo modo è possibile attivare le azioni a livello di codice anziché passare all'interfaccia utente di ADF e fare clic su un pulsante. Ciò conferirà alle pipeline di integrazione continua/recapito continuo un'esperienza di integrazione continuata.

### <a name="current-cicd-flow"></a>Flusso CI/CD corrente

1. Ogni utente apporta modifiche nei rami privati.
2. Push al master non è consentito. gli utenti devono creare una richiesta pull al master per apportare modifiche.
3. Gli utenti devono caricare l'interfaccia utente di ADF e fare clic su pubblica per distribuire le modifiche apportate Data Factory e generare i modelli ARM nel ramo di pubblicazione.
4. La pipeline di versione DevOps è configurata in modo da creare una nuova versione e distribuire il modello ARM ogni volta che viene effettuato il push di una nuova modifica nel ramo di pubblicazione.

![Flusso CI/CD corrente](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Passaggio manuale

Nel flusso di integrazione continua/recapito continuo corrente, l'esperienza utente è l'intermediario per creare il modello ARM, pertanto un utente deve accedere all'interfaccia utente di ADF e fare clic manualmente su pubblica per avviare la generazione del modello ARM e rilasciarla nel ramo Publish, che è un po' di hack.

### <a name="the-new-cicd-flow"></a>Nuovo flusso CI/CD

1. Ogni utente apporta modifiche nei rami privati.
2. Push al master non è consentito. gli utenti devono creare una richiesta pull al master per apportare modifiche.
3. **La compilazione della pipeline di Azure DevOps viene attivata ogni volta che viene eseguito un nuovo commit nel database master, convalida le risorse e genera un modello ARM come artefatto se la convalida ha esito positivo.**
4. La pipeline di versione DevOps è configurata in modo da creare una nuova versione e distribuire il modello ARM ogni volta che è disponibile una nuova compilazione. 

![Nuovo flusso CI/CD](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Cosa è cambiato?

- A questo punto è disponibile un processo di compilazione con una pipeline di compilazione DevOps.
- La pipeline di compilazione usa il pacchetto NPM ADFUtilities, che convaliderà tutte le risorse e genererà i modelli ARM (modelli singoli e collegati).
- La pipeline di compilazione sarà responsabile della convalida delle risorse ADF e della generazione del modello ARM anziché dell'interfaccia utente di ADF (pulsante pubblica).
- La definizione di versione DevOps utilizzerà questa nuova pipeline di compilazione anziché l'artefatto git.

> [!NOTE]
> È possibile continuare a usare il meccanismo esistente (adf_publish Branch) o usare il nuovo flusso. Entrambi sono supportati. 

## <a name="package-overview"></a>Panoramica del pacchetto

Nel pacchetto sono attualmente disponibili due comandi:
- Esportare un modello di Azure Resource Manager
- Convalida

### <a name="export-arm-template"></a>Esportare un modello di Azure Resource Manager

Eseguire NPM Run Start Export <rootFolder> <factoryId> [CartellaOutput] per esportare il modello ARM usando le risorse di una determinata cartella. Questo comando esegue anche un controllo di convalida prima di generare il modello ARM. Di seguito è riportato un esempio:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- RootFolder è un campo obbligatorio che rappresenta il percorso in cui si trovano le risorse Data Factory.
- FactoryId è un campo obbligatorio che rappresenta l'ID risorsa di data factory nel formato: "/subscriptions/ <subId> /ResourceGroups/ <rgName> /providers/Microsoft.DataFactory/Factories/ <dfName> ".
- CartellaOutput è un parametro facoltativo che specifica il percorso relativo per salvare il modello ARM generato.
 
> [!NOTE]
> Il modello ARM generato non viene pubblicato nella `Live` versione della factory. La distribuzione deve essere eseguita usando una pipeline CI/CD. 
 

### <a name="validate"></a>Convalida

Eseguire NPM Run Start Validate <rootFolder> <factoryId> per convalidare tutte le risorse di una determinata cartella. Di seguito è riportato un esempio:
    
```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- RootFolder è un campo obbligatorio che rappresenta il percorso in cui si trovano le risorse Data Factory.
- FactoryId è un campo obbligatorio che rappresenta l'ID risorsa di data factory nel formato: "/subscriptions/ <subId> /ResourceGroups/ <rgName> /providers/Microsoft.DataFactory/Factories/ <dfName> ".


## <a name="create-an-azure-pipeline"></a>Creare una pipeline di Azure

Sebbene i pacchetti NPM possano essere utilizzati in diversi modi, uno dei principali vantaggi viene utilizzato tramite una [pipeline di Azure](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0). Per ogni merge nel ramo di collaborazione, è possibile attivare una pipeline che prima convalida tutto il codice e quindi Esporta il modello ARM in un [artefatto di compilazione](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) che può essere utilizzato da una pipeline di versione. **Il modo in cui differisce dal processo di integrazione continua/recapito continuo corrente è che la pipeline di rilascio verrà indirizzata a questo artefatto anziché al `adf_publish` ramo esistente.**

Per iniziare, seguire questa procedura:

1.  Aprire un progetto DevOps di Azure e passare a "Pipelines". Selezionare "nuova pipeline".

    ![Nuova pipeline](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
2.  Selezionare il repository in cui si vuole salvare lo script YAML della pipeline. Si consiglia di salvarlo in una cartella di *compilazione* all'interno dello stesso repository delle risorse di ADF. Verificare che nel repository sia presente anche un **package.jssul** file che contiene il nome del pacchetto (come illustrato nell'esempio seguente).

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
    
3.  Selezionare *pipeline Starter*. Se il file YAML è stato caricato o Unito (come illustrato nell'esempio seguente), è anche possibile puntare direttamente a tale file e modificarlo. 

    ![Pipeline di avvio](media/continuous-integration-deployment-improvements/starter-pipeline.png) 

    ```yaml
    # Sample YAML file to validate and export an ARM template into a Build Artifact
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
    
    # Validates all of the ADF resources in the repository. You will get the same validation errors as when "Validate All" is clicked
    # Enter the appropriate subscription and name for the source factory 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder. Same as clicking "Publish" from UX
    # The ARM template generated is not published to the ‘Live’ version of the factory. Deployment should be done using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the Artifact to be used as a source for a release pipeline
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

4.  Immettere nel codice YAML. È consigliabile prendere il file YAML e usarlo come punto di partenza.
5.  Salvare ed eseguire. Se si usa YAML, viene attivata ogni volta che viene aggiornato il ramo "Main".

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'integrazione e il recapito continui, vedere Data Factory: 

- [Integrazione continua e recapito in Azure Data Factory](continuous-integration-deployment.md).
