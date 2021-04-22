---
title: Creare, visualizzare e gestire argomenti di sistema Griglia di eventi di Azure'interfaccia della riga di comando
description: Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per creare, visualizzare ed eliminare argomenti di sistema.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 34a098406762fd57dc9dc4b58fc375286f5d5b13
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874298"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Creare, visualizzare e gestire argomenti di sistema di Griglia di eventi con l'interfaccia della riga di comando di Azure
Questo articolo illustra come creare e gestire argomenti di sistema usando l'interfaccia della riga di comando di Azure. Per una panoramica degli argomenti di sistema, vedere [Argomenti di sistema](system-topics.md).

## <a name="install-extension-for-azure-cli"></a>Installare l'estensione per l'interfaccia della riga di comando di Azure
Per l'interfaccia della riga di comando di Azure, è necessaria l'[estensione Griglia di eventi](/cli/azure/azure-cli-extensions-list).

In Cloud Shell:

- Se l'estensione è stata installata in precedenza, aggiornarla: `az extension update -n eventgrid`
- Se l'estensione non è stata installata in precedenza, installarla:  `az extension add -n eventgrid`

Per un'installazione locale:

1. [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) Assicurarsi di avere la versione più recente installata controllando con `az --version`.
2. Disinstallare le versioni precedenti dell'estensione: `az extension remove -n eventgrid`
3. Installare l'estensione eventgrid con `az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>Creare un argomento di sistema

- Per creare prima un argomento di sistema in un'origine di Azure e quindi creare una sottoscrizione di eventi per tale argomento, vedere gli argomenti di riferimento seguenti:
    - [az eventgrid system-topic create](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_create)

        ```azurecli-interactive
        # Get the ID of the Azure source (for example: Azure Storage account)
        storageid=$(az storage account show \
                --name <AZURE STORAGE ACCOUNT NAME> \
                --resource-group <AZURE RESOURCE GROUP NAME> \
                    --query id --output tsv)
    
        # Create the system topic on the Azure source (example: Azure Storage account)
        az eventgrid system-topic create \
            -g <AZURE RESOURCE GROUP NAME> \
            --name <SPECIFY SYSTEM TOPIC NAME> \
            --location <LOCATION> \
            --topic-type microsoft.storage.storageaccounts \
            --source $storageid
        ```           

        Per un elenco di `topic-type` valori che è possibile usare per creare un argomento di sistema, eseguire il comando seguente. Questi valori dei tipi di argomento rappresentano le origini eventi che supportano la creazione di argomenti di sistema. Ignorare e `Microsoft.EventGrid.Topics` `Microsoft.EventGrid.Domains` dall'elenco. 

        ```azurecli-interactive
        az eventgrid topic-type  list --output json | grep -w id
        ```
    - [az eventgrid system-topic event-subscription create](/cli/azure/eventgrid/system-topic/event-subscription#az_eventgrid_system_topic_event-subscription-create)

        ```azurecli-interactive
        az eventgrid system-topic event-subscription create --name <SPECIFY EVENT SUBSCRIPTION NAME> \
            -g rg1 --system-topic-name <SYSTEM TOPIC NAME> \
            --endpoint <ENDPOINT URL>         
        ```
- Per creare un argomento di sistema (in modo implicito) quando si crea una sottoscrizione di eventi per un'origine di Azure, usare [il metodo az eventgrid event-subscription create.](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) Ecco un esempio:
    
    ```azurecli-interactive
    storageid=$(az storage account show --name <AZURE STORAGE ACCOUNT NAME> --resource-group <AZURE RESOURCE GROUP NAME> --query id --output tsv)
    endpoint=<ENDPOINT URL>

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <EVENT SUBSCRIPTION NAME> \
      --endpoint $endpoint
    ```
    Per un'esercitazione con istruzioni dettagliate, vedere [Sottoscrivere l'account di archiviazione.](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account)

## <a name="view-all-system-topics"></a>Visualizza tutto di sistema
Per visualizzare tutti gli argomenti di sistema e i dettagli di un argomento di sistema selezionato, usare i comandi seguenti:

- [az eventgrid system-topic list](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_list)

    ```azurecli-interactive
    az eventgrid system-topic list   
     ```
- [az eventgrid system-topic show](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_show)

    ```azurecli-interactive
    az eventgrid system-topic show -g <AZURE RESOURCE GROUP NAME> -n <SYSTEM TOPIC NAME>     
     ```

## <a name="delete-a-system-topic"></a>Eliminare un argomento di sistema
Per eliminare un argomento di sistema, usare il comando seguente: 

- [az eventgrid system-topic delete](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_delete)

    ```azurecli-interactive
    az eventgrid system-topic delete -g <AZURE RESOURCE GROUP NAME> --name <SYSTEM TOPIC NAME>   
     ```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sugli argomenti di sistema e sui tipi di argomento supportati [da Griglia di eventi di Azure,](system-topics.md) vedere gli argomenti di sistema Griglia di eventi di Azure. 