---
title: "Guida introduttiva: creare un Azure Data Factory usando l'interfaccia della riga di comando"
description: Questa Guida introduttiva crea una Azure Data Factory, inclusi un servizio collegato, set di impostazioni e una pipeline. È possibile eseguire la pipeline per eseguire un'azione di copia file.
author: linda33wj
ms.author: jingwang
ms.service: azure-cli
ms.topic: quickstart
ms.date: 03/24/2021
ms.custom:
- template-quickstart
- devx-track-azurecli
ms.openlocfilehash: 9af5f276e49e9eb2756dc544db353c75c99bc5a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105938063"
---
# <a name="quickstart-create-an-azure-data-factory-using-azure-cli"></a>Guida introduttiva: creare un Azure Data Factory usando l'interfaccia della riga di comando

Questa Guida introduttiva descrive come usare l'interfaccia della riga di comando di Azure per creare un Azure Data Factory. La pipeline creata in questo data factory copia i dati da una cartella a un'altra in un archivio BLOB di Azure. Per informazioni su come trasformare i dati usando Azure Data Factory, vedere [trasformare i dati in Azure Data Factory](transform-data.md).

Per un'introduzione al servizio Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment.md)]

> [!NOTE]
> Per creare istanze di Data Factory, l'account utente usato per accedere ad Azure deve essere un membro del ruolo collaboratore o proprietario oppure un amministratore della sottoscrizione di Azure. Per altre informazioni, vedere [ruoli di Azure](quickstart-create-data-factory-powershell.md#azure-roles).

## <a name="prepare-a-container-and-test-file"></a>Preparare un contenitore e un file di test

Questa Guida introduttiva usa un account di archiviazione di Azure, che include un contenitore con un file.

1. Per creare un gruppo di risorse denominato `ADFQuickStartRG` , usare il comando [AZ Group create](/cli/azure/group#az_group_create) :

   ```azurecli
   az group create --name ADFQuickStartRG --location eastus
   ```

1. Creare un account di archiviazione usando il comando [AZ storage account create](/cli/azure/storage/container#az_storage_container_create) :

   ```azurecli
   az storage account create --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --location eastus
   ```

1. Creare un contenitore denominato `adftutorial` usando il comando [AZ Storage container create](/cli/azure/storage/container#az_storage_container_create) :

   ```azurecli
   az storage container create --resource-group ADFQuickStartRG --name adftutorial \
       --account-name adfquickstartstorage --auth-mode key
   ```

1. Nella directory locale creare un file denominato `emp.txt` da caricare. Se si sta lavorando in Azure Cloud Shell, è possibile trovare la directory di lavoro corrente usando il `echo $PWD` comando bash. Per creare un file, è possibile usare i comandi bash standard, `cat` ad esempio:

   ```console
   cat > emp.txt
   This is text.
   ```

   Usare **CTRL + D** per salvare il nuovo file.

1. Per caricare il nuovo file nel contenitore di archiviazione di Azure, usare il comando [AZ storage BLOB upload](/cli/azure/storage/blob#az_storage_blob_upload) :

   ```azurecli
   az storage blob upload --account-name adfquickstartstorage --name input/emp.txt \
       --container-name adftutorial --file emp.txt --auth-mode key
   ```

   Questo comando viene caricato in una nuova cartella denominata `input` .

## <a name="create-a-data-factory"></a>Creare una data factory

Per creare un data factory di Azure, eseguire il comando [AZ DataFactory factory create](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_create) :

```azurecli
az datafactory factory create --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

> [!IMPORTANT]
> Sostituire `ADFTutorialFactory` con un nome data factory univoco globale, ad esempio ADFTutorialFactorySP1127.

È possibile visualizzare il data factory creato usando il comando [AZ DataFactory Factory Show](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_show) :

```azurecli
az datafactory factory show --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

## <a name="create-a-linked-service-and-datasets"></a>Creare un servizio collegato e set di impostazioni

Successivamente, creare un servizio collegato e due set di impostazioni.

1. Ottenere la stringa di connessione per l'account di archiviazione usando il comando [AZ storage account Show-Connection-String](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_show) :

   ```azurecli
   az storage account show-connection-string --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --key primary
   ```

1. Nella directory di lavoro creare un file JSON con questo contenuto, che include la stringa di connessione nel passaggio precedente. Assegnare un nome al file `AzureStorageLinkedService.json` :

   ```json
   {
       "type":"AzureStorage",
           "typeProperties":{
           "connectionString":{
           "type": "SecureString",
           "value":"DefaultEndpointsProtocol=https;AccountName=adfquickstartstorage;AccountKey=K9F4Xk/EhYrMBIR98rtgJ0HRSIDU4eWQILLh2iXo05Xnr145+syIKNczQfORkQ3QIOZAd/eSDsvED19dAwW/tw==;EndpointSuffix=core.windows.net"
           }
       }
   }
   ```

1. Creare un servizio collegato, denominato `AzureStorageLinkedService` , usando il comando [AZ DataFactory linked-service create](/cli/azure/ext/datafactory/datafactory/linked-service#ext_datafactory_az_datafactory_linked_service_create) :

   ```azurecli
   az datafactory linked-service create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --linked-service-name AzureStorageLinkedService \
       --properties @AzureStorageLinkedService.json
   ```

1. Nella directory di lavoro creare un file JSON con questo contenuto, denominato `InputDataset.json` :

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "input",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. Creare un set di dati `InputDataset` di input denominato usando il comando [AZ DataFactory DataSet create](/cli/azure/ext/datafactory/datafactory/dataset#ext_datafactory_az_datafactory_dataset_create) :

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name InputDataset --factory-name ADFQuickStartFactory \
       --properties @InputDataset.json
   ```

1. Nella directory di lavoro creare un file JSON con questo contenuto, denominato `OutputDataset.json` :

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "output",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. Creare un set di dati `OutputDataset` di output denominato usando il comando [AZ DataFactory DataSet create](/cli/azure/ext/datafactory/datafactory/dataset#ext_datafactory_az_datafactory_dataset_create) :

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name OutputDataset --factory-name ADFQuickStartFactory \
       --properties @OutputDataset.json
   ```

## <a name="create-and-run-the-pipeline"></a>Creare ed eseguire la pipeline

Infine, creare ed eseguire la pipeline.

1. Nella directory di lavoro creare un file JSON con il contenuto denominato `Adfv2QuickStartPipeline.json` :

   ```json
   {
       "name": "Adfv2QuickStartPipeline",
       "properties": {
           "activities": [
               {
                   "name": "CopyFromBlobToBlob",
                   "type": "Copy",
                   "dependsOn": [],
                   "policy": {
                       "timeout": "7.00:00:00",
                       "retry": 0,
                       "retryIntervalInSeconds": 30,
                       "secureOutput": false,
                       "secureInput": false
                   },
                   "userProperties": [],
                   "typeProperties": {
                          "source": {
                           "type": "BinarySource",
                           "storeSettings": {
                               "type": "AzureBlobStorageReadSettings",
                               "recursive": true
                           }
                       },
                       "sink": {
                           "type": "BinarySink",
                           "storeSettings": {
                               "type": "AzureBlobStorageWriteSettings"
                           }
                       },
                       "enableStaging": false
                   },
                   "inputs": [
                       {
                           "referenceName": "InputDataset",
                           "type": "DatasetReference"
                       }
                   ],
                   "outputs": [
                       {
                           "referenceName": "OutputDataset",
                           "type": "DatasetReference"
                       }
                   ]
               }
           ],
           "annotations": []
       }
   }
   ```

1. Creare una pipeline denominata `Adfv2QuickStartPipeline` usando il comando [AZ DataFactory pipeline create](/cli/azure/ext/datafactory/datafactory/pipeline#ext_datafactory_az_datafactory_pipeline_create) :

   ```azurecli
   az datafactory pipeline create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --name Adfv2QuickStartPipeline \
       --pipeline @Adfv2QuickStartPipeline.json
   ```

1. Eseguire la pipeline usando il comando [AZ DataFactory pipeline create-Run](/cli/azure/ext/datafactory/datafactory/pipeline#ext_datafactory_az_datafactory_pipeline_create_run) :

   ```azurecli
   az datafactory pipeline create-run --resource-group ADFQuickStartRG \
       --name Adfv2QuickStartPipeline --factory-name ADFTutorialFactory
   ```

   Questo comando restituisce un ID esecuzione. Copiarlo per usarlo nel comando successivo.

1. Verificare che l'esecuzione della pipeline sia riuscita con il comando [AZ DataFactory pipeline-Run Show](/cli/azure/ext/datafactory/datafactory/pipeline-run#ext_datafactory_az_datafactory_pipeline_run_show) :

   ```azurecli
   az datafactory pipeline-run show --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --run-id 00000000-0000-0000-0000-000000000000
   ```

È anche possibile verificare che la pipeline sia stata eseguita come previsto usando il [portale di Azure](https://portal.azure.com/). Per altre informazioni, vedere [esaminare le risorse distribuite](quickstart-create-data-factory-powershell.md#review-deployed-resources).

## <a name="clean-up-resources"></a>Pulire le risorse

Tutte le risorse in questa Guida introduttiva fanno parte dello stesso gruppo di risorse. Per rimuoverli tutti, usare il comando [AZ Group Delete](/cli/azure/group#az_group_delete) :

```azurecli
az group delete --name ADFQuickStartRG
```

Se si usa questo gruppo di risorse per qualsiasi altra operazione, eliminare le singole risorse. Per rimuovere il servizio collegato, ad esempio, usare il comando [AZ DataFactory linked-service Delete](/cli/azure/ext/datafactory/datafactory/linked-service#ext_datafactory_az_datafactory_linked_service_delete) .

In questa Guida introduttiva sono stati creati i file JSON seguenti:

- AzureStorageLinkedService.js
- InputDataset.js
- OutputDataset.js
- Adfv2QuickStartPipeline.js

Eliminarli usando i comandi bash standard.

## <a name="next-steps"></a>Passaggi successivi

- [Pipeline e attività in Azure Data Factory](concepts-pipelines-activities.md)
- [Servizi collegati in Azure Data Factory](concepts-linked-services.md)
- [Set di dati in Azure Data Factory](concepts-datasets-linked-services.md)
- [Trasformare i dati in Azure Data Factory](transform-data.md)
