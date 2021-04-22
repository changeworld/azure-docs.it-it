---
title: "Guida introduttiva: Creare un'istanza Azure Data Factory'interfaccia della riga di comando di Azure"
description: Questa guida introduttiva crea un Azure Data Factory, tra cui un servizio collegato, set di dati e una pipeline. È possibile eseguire la pipeline per eseguire un'azione di copia file.
author: linda33wj
ms.author: jingwang
ms.service: azure-cli
ms.topic: quickstart
ms.date: 03/24/2021
ms.custom:
- template-quickstart
- devx-track-azurecli
ms.openlocfilehash: b40407f4c4fb81bbf76bd0b552f3c9f2c827232a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871523"
---
# <a name="quickstart-create-an-azure-data-factory-using-azure-cli"></a>Guida introduttiva: Creare un'istanza Azure Data Factory'interfaccia della riga di comando di Azure

Questa guida introduttiva descrive come usare l'interfaccia della riga di comando di Azure per creare un Azure Data Factory. La pipeline creata in questo data factory copia i dati da una cartella a un'altra in un Archiviazione BLOB di Azure. Per informazioni su come trasformare i dati usando Azure Data Factory, vedere [Trasformare i dati in Azure Data Factory](transform-data.md).

Per un'introduzione al servizio Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment.md)]

> [!NOTE]
> Per creare istanze di Data Factory, l'account utente usato per accedere ad Azure deve essere un membro del ruolo collaboratore o proprietario oppure un amministratore della sottoscrizione di Azure. Per altre informazioni, vedere [Ruoli di Azure.](quickstart-create-data-factory-powershell.md#azure-roles)

## <a name="prepare-a-container-and-test-file"></a>Preparare un contenitore e un file di test

Questa guida introduttiva usa un account Archiviazione di Azure, che include un contenitore con un file.

1. Per creare un gruppo di risorse denominato `ADFQuickStartRG` , usare il comando [az group create:](/cli/azure/group#az_group_create)

   ```azurecli
   az group create --name ADFQuickStartRG --location eastus
   ```

1. Creare un account di archiviazione usando il [comando az storage account create:](/cli/azure/storage/container#az_storage_container_create)

   ```azurecli
   az storage account create --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --location eastus
   ```

1. Creare un contenitore `adftutorial` denominato usando il comando [az storage container create:](/cli/azure/storage/container#az_storage_container_create)

   ```azurecli
   az storage container create --resource-group ADFQuickStartRG --name adftutorial \
       --account-name adfquickstartstorage --auth-mode key
   ```

1. Nella directory locale creare un file denominato `emp.txt` da caricare. Se si lavora in Azure Cloud Shell, è possibile trovare la directory di lavoro corrente usando il `echo $PWD` comando Bash. È possibile usare i comandi Bash standard, ad esempio `cat` , per creare un file:

   ```console
   cat > emp.txt
   This is text.
   ```

   Usare **CTRL+D** per salvare il nuovo file.

1. Per caricare il nuovo file nel contenitore di archiviazione di Azure, usare il [comando az storage BLOB upload:](/cli/azure/storage/blob#az_storage_blob_upload)

   ```azurecli
   az storage blob upload --account-name adfquickstartstorage --name input/emp.txt \
       --container-name adftutorial --file emp.txt --auth-mode key
   ```

   Questo comando viene caricato in una nuova cartella denominata `input` .

## <a name="create-a-data-factory"></a>Creare una data factory

Per creare un'istanza data factory Azure, eseguire il [comando az datafactory factory create:](/cli/azure/datafactory/factory#az_datafactory_factory_create)

```azurecli
az datafactory factory create --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

> [!IMPORTANT]
> Sostituire `ADFTutorialFactory` con un nome univoco data factory globale, ad esempio ADFTutorialFactorySP1127.

È possibile visualizzare il data factory creato usando il [comando az datafactory factory show:](/cli/azure/datafactory/factory#az_datafactory_factory_show)

```azurecli
az datafactory factory show --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

## <a name="create-a-linked-service-and-datasets"></a>Creare un servizio collegato e set di dati

Creare quindi un servizio collegato e due set di dati.

1. Ottenere la stringa di connessione per l'account di archiviazione usando [il comando az storage account show-connection-string:](/cli/azure/datafactory/factory#az_datafactory_factory_show)

   ```azurecli
   az storage account show-connection-string --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --key primary
   ```

1. Nella directory di lavoro creare un file JSON con questo contenuto, che include la stringa di connessione del passaggio precedente. Assegnare al file il nome `AzureStorageLinkedService.json` :

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

1. Creare un servizio collegato, denominato `AzureStorageLinkedService` , usando il comando [az datafactory linked-service create:](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_create)

   ```azurecli
   az datafactory linked-service create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --linked-service-name AzureStorageLinkedService \
       --properties @AzureStorageLinkedService.json
   ```

1. Nella directory di lavoro creare un file JSON con questo contenuto denominato `InputDataset.json` :

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

1. Creare un set di dati di input `InputDataset` denominato usando il comando [az datafactory dataset create:](/cli/azure/datafactory/dataset#az_datafactory_dataset_create)

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name InputDataset --factory-name ADFQuickStartFactory \
       --properties @InputDataset.json
   ```

1. Nella directory di lavoro creare un file JSON con questo contenuto denominato `OutputDataset.json` :

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

1. Creare un set di dati di output `OutputDataset` denominato usando il comando [az datafactory dataset create:](/cli/azure/datafactory/dataset#az_datafactory_dataset_create)

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name OutputDataset --factory-name ADFQuickStartFactory \
       --properties @OutputDataset.json
   ```

## <a name="create-and-run-the-pipeline"></a>Creare ed eseguire la pipeline

Infine, creare ed eseguire la pipeline.

1. Nella directory di lavoro creare un file JSON con questo contenuto denominato `Adfv2QuickStartPipeline.json` :

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

1. Creare una pipeline denominata `Adfv2QuickStartPipeline` usando il [comando az datafactory pipeline create:](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create)

   ```azurecli
   az datafactory pipeline create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --name Adfv2QuickStartPipeline \
       --pipeline @Adfv2QuickStartPipeline.json
   ```

1. Eseguire la pipeline usando il [comando az datafactory pipeline create-run:](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create_run)

   ```azurecli
   az datafactory pipeline create-run --resource-group ADFQuickStartRG \
       --name Adfv2QuickStartPipeline --factory-name ADFTutorialFactory
   ```

   Questo comando restituisce un ID esecuzione. Copiarlo per usarlo nel comando successivo.

1. Verificare che l'esecuzione della pipeline sia riuscita usando il [comando az datafactory pipeline-run show:](/cli/azure/datafactory/pipeline-run#az_datafactory_pipeline_run_show)

   ```azurecli
   az datafactory pipeline-run show --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --run-id 00000000-0000-0000-0000-000000000000
   ```

È anche possibile verificare che la pipeline sia stata eseguita come previsto usando il [portale di Azure](https://portal.azure.com/). Per altre informazioni, vedere [Esaminare le risorse distribuite.](quickstart-create-data-factory-powershell.md#review-deployed-resources)

## <a name="clean-up-resources"></a>Pulire le risorse

Tutte le risorse in questa guida introduttiva fanno parte dello stesso gruppo di risorse. Per rimuoverli tutti, usare il [comando az group delete:](/cli/azure/group#az_group_delete)

```azurecli
az group delete --name ADFQuickStartRG
```

Se si usa questo gruppo di risorse per altri elementi, eliminare invece le singole risorse. Ad esempio, per rimuovere il servizio collegato, usare [il comando az datafactory linked-service delete.](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_delete)

In questa guida introduttiva sono stati creati i file JSON seguenti:

- AzureStorageLinkedService.jssu
- InputDataset.jssu
- OutputDataset.jssu
- Adfv2QuickStartPipeline.jssu

Eliminarli usando i comandi Bash standard.

## <a name="next-steps"></a>Passaggi successivi

- [Pipeline e attività in Azure Data Factory](concepts-pipelines-activities.md)
- [Servizi collegati in Azure Data Factory](concepts-linked-services.md)
- [Set di dati in Azure Data Factory](concepts-datasets-linked-services.md)
- [Trasformare i dati in Azure Data Factory](transform-data.md)
