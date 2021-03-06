---
title: Distribuire modelli di Resource Manager tramite GitHub Actions
description: Descrive come distribuire modelli di Azure Resource Manager (modelli arm) usando GitHub Actions.
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: ec29ae019555c54ccdcef9dd743706f8d6401bbd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781974"
---
# <a name="deploy-arm-templates-by-using-github-actions"></a>Distribuire modelli di Arm usando GitHub Actions

[GitHub Actions](https://docs.github.com/en/actions) è una suite di funzionalità di GitHub che consente di automatizzare i flussi di lavoro di sviluppo software nella stessa posizione in cui si archivia il codice e si collabora a richieste pull e problemi.

Usare [l'azione Azure Resource Manager modello](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) di distribuzione per automatizzare la distribuzione di un modello di Azure Resource Manager (modello arm) in Azure.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un account GitHub. Se non è disponibile, iscriversi per riceverne uno [gratuito](https://github.com/join).
    - Un repository GitHub per archiviare i modelli Resource Manager e i file del flusso di lavoro. Per crearne uno, vedere [Creazione di un nuovo repository](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository).


## <a name="workflow-file-overview"></a>Panoramica dei file del flusso di lavoro

Un flusso di lavoro viene definito da un file YAML (con estensione yml) nel percorso `/.github/workflows/` del repository. Questa definizione contiene i vari passaggi e i parametri che costituiscono il flusso di lavoro.

Il file è costituito da due sezioni:

|Sezione  |Attività  |
|---------|---------|
|**autenticazione** | 1. Definire un'entità servizio. <br /> 2. Creare un segreto GitHub. |
|**Distribuzione** | 1. Distribuire il modello Resource Manager distribuzione. |

## <a name="generate-deployment-credentials"></a>Generare le credenziali per la distribuzione


È possibile creare un'[entità servizio](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) con il comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) dell'[interfaccia della riga di comando di Azure](/cli/azure/). Eseguire questo comando con [Azure Cloud Shell](https://shell.azure.com/) nel portale di Azure oppure selezionando il pulsante **Prova**.

Creare un gruppo di risorse, se non se ne ha già uno.

```azurecli-interactive
    az group create -n {MyResourceGroup} -l {location}
```

Sostituire il segnaposto `myApp` con il nome dell'applicazione.

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

Nell'esempio precedente sostituire i segnaposto con l'ID sottoscrizione e il nome del gruppo di risorse. L'output è un oggetto JSON con le credenziali di assegnazione di ruolo che forniscono l'accesso all'app del servizio app simile a questo esempio. Copiare l'oggetto JSON per un uso successivo. Saranno necessarie solo le sezioni con i valori `clientId`, `clientSecret`, `subscriptionId` e `tenantId`.

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> È sempre consigliabile concedere l'accesso minimo. L'ambito nell'esempio precedente è limitato al gruppo di risorse.



## <a name="configure-the-github-secrets"></a>Configurare i segreti GitHub

È necessario creare segreti per le credenziali di Azure, il gruppo di risorse e le sottoscrizioni.

1. In [GitHub](https://github.com/) esplorare il repository.

1. Selezionare **Settings > Secrets > New secret** (Impostazioni > Segreti > Nuovo segreto).

1. Incollare l'intero output JSON del comando dell'interfaccia della riga di comando di Azure nel campo del valore del segreto. Assegnare al segreto il nome `AZURE_CREDENTIALS`.

1. Creare un altro segreto denominato `AZURE_RG` . Aggiungere il nome del gruppo di risorse al campo del valore del segreto (ad esempio: `myResourceGroup` ).

1. Creare un segreto aggiuntivo denominato `AZURE_SUBSCRIPTION` . Aggiungere l'ID sottoscrizione al campo del valore del segreto (ad esempio: `90fd3f9d-4c61-432d-99ba-1273f236afa2` ).

## <a name="add-resource-manager-template"></a>Aggiungere un modello di Resource Manager

Aggiungere un Resource Manager al repository GitHub. Questo modello crea un account di archiviazione.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

È possibile inserire il file in un punto qualsiasi del repository. L'esempio di flusso di lavoro nella sezione successiva presuppone che il file modello sia denominato **azuredeploy.jsin** e che sia archiviato nella radice del repository.

## <a name="create-workflow"></a>Creare un flusso di lavoro

Il file del flusso di lavoro deve essere archiviato nella cartella **.github/workflows** nella radice del repository. Il file del flusso di lavoro può avere estensione **.yml** o **.yaml**.

1. Dal repository GitHub, selezionare **Actions** dal menu in alto.
1. Selezionare **Nuovo flusso di lavoro**.
1. Selezionare **Set up a workflow yourself** (Configurare manualmente un flusso di lavoro).
1. Se si preferisce un nome diverso da **main.yml**, rinominare il file del flusso di lavoro. Ad esempio: **deployStorageAccount.yml**.
1. Sostituire il contenuto del file .yml con quanto segue:

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@main

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

          # Deploy ARM template
        - name: Run ARM deploy
          uses: azure/arm-deploy@v1
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./azuredeploy.json
            parameters: storageAccountType=Standard_LRS

          # output containerName variable from template
        - run: echo ${{ steps.deploy.outputs.containerName }}
    ```
    > [!NOTE]
    > È invece possibile specificare un file di parametri di formato JSON nell'azione di distribuzione arm (ad esempio: `.azuredeploy.parameters.json` ).

    La prima sezione del file del flusso di lavoro include:

    - **name**: Nome del flusso di lavoro.
    - **on**: Nome degli eventi GitHub che attivano il flusso di lavoro. Il flusso di lavoro viene attivato quando è presente un evento push nel ramo principale, che modifica almeno uno dei due file specificati. I due file sono il file del flusso di lavoro e il file di modello.

1. Selezionare **Start commit** (Avvia commit).
1. Selezionare **Commit direttamente nel ramo principale.**
1. Selezionare **Commit new file** (Commit nuovo file) (o **Commit modifiche**).

Dato che il flusso di lavoro è configurato per essere attivato dal file del flusso di lavoro o dal file di modello da aggiornare, il flusso di lavoro viene avviato subito dopo il commit delle modifiche.

## <a name="check-workflow-status"></a>Controllare lo stato del flusso di lavoro

1. Selezionare la **scheda** Azioni. Verrà visualizzato un flusso **di lavoro Crea deployStorageAccount.yml.** L'esecuzione del flusso di lavoro richiede da 1 a 2 minuti.
1. Selezionare il flusso di lavoro per aprirlo.
1. Selezionare **Esegui distribuzione arm** dal menu per verificare la distribuzione.

## <a name="clean-up-resources"></a>Pulire le risorse
Quando il gruppo di risorse e il repository non sono più necessari, pulire le risorse distribuite eliminando il gruppo di risorse e il repository GitHub.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare il primo modello di Arm](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Modulo Learn: Automatizzare la distribuzione di modelli arm usando GitHub Actions](/learn/modules/deploy-templates-command-line-github-actions/)
