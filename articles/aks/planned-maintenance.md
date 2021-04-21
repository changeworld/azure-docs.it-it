---
title: Usare manutenzione pianificata per il cluster servizio Azure Kubernetes (AKS) (anteprima)
titleSuffix: Azure Kubernetes Service
description: Informazioni su come usare la manutenzione pianificata in servizio Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: f1e0822e77d8466b1b9796041fbdba53c3f9c91f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782910"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>Usare Manutenzione pianificata per pianificare le finestre di manutenzione per il cluster servizio Azure Kubernetes (anteprima)

Il cluster del servizio Web Disaks ha una manutenzione regolare eseguita automaticamente. Per impostazione predefinita, questa operazione può avvenire in qualsiasi momento. Manutenzione pianificata consente di pianificare finestre di manutenzione settimanali che aggiorneranno il piano di controllo e i pod kube-system in un'istanza di VMSS e ridurre al minimo l'impatto del carico di lavoro. Dopo la pianificazione, tutta la manutenzione verrà eseguita durante la finestra selezionata. È possibile pianificare una o più finestre settimanali nel cluster specificando un giorno o un intervallo di tempo in un giorno specifico. Le finestre di manutenzione vengono configurate tramite l'interfaccia della riga di comando di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>Limitazioni

Quando si usa manutenzione pianificata, si applicano le restrizioni seguenti:

- Il servizio AKS si riserva il diritto di interrompere queste finestre per operazioni di manutenzione non pianificate/reattive urgenti o critiche.
- Attualmente, l'esecuzione  di operazioni di manutenzione è considerata solo un'operazione ottimale e non è garantita l'esecuzione all'interno di una finestra specificata.
- Gli aggiornamenti non possono essere bloccati per più di sette giorni.

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando aks-preview

È anche necessaria l'estensione dell'interfaccia della riga di comando di Azure *aks-preview* versione 0.5.4 o successiva. Installare *l'estensione dell'interfaccia della* riga di comando di Azure aks-preview usando il [comando az extension add.][az-extension-add] In caso di installazione di eventuali aggiornamenti disponibili, usare [il comando az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>Consenti manutenzione ogni lunedì dalle 1:00 alle 2:00

Per aggiungere una finestra di manutenzione, è possibile usare il `az aks maintenanceconfiguration add` comando .

> [!IMPORTANT]
> Le finestre manutenzione pianificata vengono specificate in Coordinated Universal Time (UTC).

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

L'output di esempio seguente mostra la finestra di manutenzione dalle 13:00 alle 2:00 ogni lunedì.

```json
{- Finished ..
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

Per consentire la manutenzione in qualsiasi momento durante un giorno, omettere il *parametro dell'ora di* inizio. Ad esempio, il comando seguente imposta la finestra di manutenzione per l'intero giorno ogni lunedì:

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>Aggiungere una configurazione di manutenzione con un file JSON

È anche possibile usare un file JSON per creare una finestra di manutenzione invece di usare i parametri. Creare un `test.json` file con il contenuto seguente:

```json
  {
        "timeInWeek": [
          {
            "day": "Tuesday",
            "hour_slots": [
              1,
              2
            ]
          },
          {
            "day": "Wednesday",
            "hour_slots": [
              1,
              6
            ]
          }
        ],
        "notAllowedTime": [
          {
            "start": "2021-05-26T03:00:00Z",
            "end": "2021-05-30T12:00:00Z"
          }
        ]
}
```

Il file JSON precedente specifica le finestre di manutenzione ogni martedì dalle 1:00 alle 3:00 e ogni mercoledì alle 13:00 - 2:00 e alle 6:00-7:00. Esiste anche un'eccezione *da 2021-05-26T03:00:00Z* a *2021-05-30T12:00:00Z* in cui la manutenzione non è consentita anche se si sovrappone a una finestra di manutenzione. Il comando seguente aggiunge le finestre di manutenzione da `test.json` .

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>Aggiornare una finestra di manutenzione esistente

Per aggiornare una configurazione di manutenzione esistente, usare il `az aks maintenanceconfiguration update` comando .

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>Elencare tutte le finestre di manutenzione in un cluster esistente

Per visualizzare tutte le finestre di configurazione di manutenzione correnti nel cluster del servizio Web Diaks, usare il `az aks maintenanceconfiguration list` comando .

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

Nell'output seguente è possibile vedere che per myAKSCluster sono configurate due finestre di manutenzione. Una finestra è il lunedì all'1:00 e un'altra finestra è il venerdì alle 4:00.

```json
[
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
    "name": "default",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Monday",
        "hourSlots": [
          1
        ]
      }
    ],
    "type": null
  },
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/testConfiguration",
    "name": "testConfiguration",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Friday",
        "hourSlots": [
          4
        ]
      }
    ],
    "type": null
  }
]
```

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>Visualizzare una finestra di configurazione della manutenzione specifica in un cluster del servizio Web Diaks

Per visualizzare una finestra di configurazione della manutenzione specifica nel cluster del servizio Web Diaks, usare il `az aks maintenanceconfiguration show` comando .

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

L'output di esempio seguente mostra la finestra di manutenzione per *l'impostazione predefinita*:

```json
{
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>Eliminare una determinata finestra di configurazione della manutenzione in un cluster del servizio Web Del servizio Web esistente

Per eliminare una determinata finestra di configurazione della manutenzione nel cluster del servizio Web Diaks, usare il `az aks maintenanceconfiguration delete` comando .

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni introduttive sull'aggiornamento del cluster del servizio Web Disattesa, vedere [Aggiornare un cluster del servizio AKS][aks-upgrade]


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
