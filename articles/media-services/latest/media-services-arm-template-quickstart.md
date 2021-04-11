---
title: modello ARM dell'account di servizi multimediali: Descrizione di servizi multimediali di Azure: questo articolo illustra come usare un modello ARM per creare un account di servizi multimediali.
Servizi: Media-Services documentationcenter:'' Author: IngridAtMicrosoft Manager: FEMila Editor:''

ms. Service: Media-Services ms. workload: ms. Topic: Guida introduttiva ms. Date: 03/23/2021 ms. Author: inhenkel ms. Custom: subject-armqs

---

# <a name="quickstart-media-services-account-arm-template"></a>Avvio rapido: Modello di Resource Manager per l'account di Servizi multimediali

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Questo articolo illustra come creare un account di Servizi multimediali usando un modello di Azure Resource Manager.

## <a name="introduction"></a>Introduzione

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se si ha familiarità con i modelli di Azure Resource Manager, è possibile procedere alla [sezione sulla distribuzione](#deploy-the-template).

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-media-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Se non è mai stato distribuito un modello di Resource Manager finora, è utile leggere informazioni sui [modelli di Azure Resource Manager](../../azure-resource-manager/templates/index.yml) e seguire l'[esercitazione](../../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell).

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-media-services-create/).

La sintassi per la recinzione del codice JSON è la seguente:

:::code language="json" source="~/quickstart-templates/101-media-services-create/azuredeploy.json":::

Nel modello sono definiti tre tipi di risorsa di Azure:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): per creare un account di archiviazione
- [Microsoft.Media/mediaservices](/azure/templates/microsoft.media/mediaservices): per creare un account di Servizi multimediali

## <a name="set-the-account"></a>Impostare l'account

```azurecli-interactive

az account set --subscription {your subscription name or id}

```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

```azurecli-interactive

az group create --name {the name you want to give your resource group} --location "{pick a location}"

```

## <a name="assign-a-variable-to-your-deployment-file"></a>Assegnare una variabile al file di distribuzione

Per praticità, creare una variabile che archivia il percorso del file modello. Questa variabile rende più semplice l'esecuzione dei comandi di distribuzione perché non è necessario digitare nuovamente il percorso ogni volta che si esegue la distribuzione.

```azurecli-interactive

templateFile="{provide the path to the template file}"

```

## <a name="deploy-the-template"></a>Distribuire il modello

Verrà chiesto di immettere il nome dell'account di Servizi multimediali.

```azurecli-interactive

az deployment group create --name {the name you want to give to your deployment} --resource-group {the name of resource group you created} --template-file $templateFile

```

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Verrà visualizzata una risposta JSON simile a questa:

```json
{
  "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Resources/deployments/amsarmquickstartdeploy",
  "location": null,
  "name": "amsarmquickstartdeploy",
  "properties": {
    "correlationId": "{correlationid}",
    "debugSetting": null,
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
            "resourceGroup": "amsarmquickstartrg",
            "resourceName": "storagey44cfdmliwatk",
            "resourceType": "Microsoft.Storage/storageAccounts"
          }
        ],
        "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg",
        "resourceName": "{accountname}",
        "resourceType": "Microsoft.Media/mediaServices"
      }
    ],
    "duration": "PT1M10.8615001S",
    "error": null,
    "mode": "Incremental",
    "onErrorDeployment": null,
    "outputResources": [
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg"
      },
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
        "resourceGroup": "amsarmquickstartrg"
      }
    ],
    "outputs": null,
    "parameters": {
      "mediaServiceName": {
        "type": "String",
        "value": "{accountname}"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.Media",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "mediaServices"
          }
        ]
      },
      {
        "id": null,
        "namespace": "Microsoft.Storage",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "storageAccounts"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "templateHash": "{templatehash}",
    "templateLink": null,
    "timestamp": "2020-11-24T23:25:52.598184+00:00",
    "validatedResources": null
  },
  "resourceGroup": "amsarmquickstartrg",
  "tags": null,
  "type": "Microsoft.Resources/deployments"
}

```

Nel portale di Azure verificare che le risorse siano state create.

![Risorse della guida di avvio rapido create](./media/media-services-arm-template-quickstart/quickstart-arm-template-resources.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si prevede di usare le risorse appena create, è possibile eliminare il gruppo di risorse.

```azurecli-interactive

az group delete --name {name of the resource group}

```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di un modello di Resource Manager seguendo la procedura di creazione di un modello con parametri, variabili e altro ancora, vedere

> [!div class="nextstepaction"]
> [Esercitazione: Creare e distribuire il primo modello di Resource Manager](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)