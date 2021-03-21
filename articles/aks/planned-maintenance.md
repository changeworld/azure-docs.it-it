---
title: Usare la manutenzione pianificata per il cluster Azure Kubernetes Service (AKS) (anteprima)
titleSuffix: Azure Kubernetes Service
description: Informazioni su come usare la manutenzione pianificata in Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: 8526d7c1c436074fbf6f838caf232e1abee06339
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670376"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>Usare la manutenzione pianificata per pianificare le finestre di manutenzione per il cluster Azure Kubernetes Service (AKS) (anteprima)

Il cluster AKS prevede una manutenzione regolare eseguita automaticamente. Per impostazione predefinita, questa operazione può essere eseguita in qualsiasi momento. La manutenzione pianificata consente di pianificare le finestre di manutenzione settimanali che aggiorneranno il piano di controllo, nonché i pod del sistema Kube in un'istanza di VMSS e ridurranno al minimo l'effetto del carico di lavoro. Una volta pianificata, tutte le operazioni di manutenzione si verificheranno durante la finestra selezionata. È possibile pianificare una o più finestre settimanali nel cluster specificando un giorno o un intervallo di tempo in un giorno specifico. Le finestre di manutenzione vengono configurate tramite l'interfaccia della riga di comando

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>Limitazioni

Quando si usa la manutenzione pianificata, si applicano le restrizioni seguenti:

- AKS si riserva il diritto di interrompere queste finestre per le operazioni di manutenzione non pianificate/reattive che sono urgenti o critiche.
- Attualmente, l'esecuzione di operazioni di manutenzione è considerata *solo il massimo sforzo* e non è garantito che si verifichino all'interno di una finestra specificata.
- Gli aggiornamenti non possono essere bloccati per più di sette giorni.

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando aks-preview

È anche necessaria l'estensione dell'interfaccia della riga di comando di Azure *AKS-Preview* versione 0.5.4 o successiva. Installare l'estensione dell'interfaccia della riga di comando di Azure *AKS-Preview* usando il comando [AZ Extension Add][az-extension-add] . In alternativa, installare gli eventuali aggiornamenti disponibili usando il comando [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>Consenti manutenzione ogni lunedì alle ore 11.00-8.00

Per aggiungere una finestra di manutenzione, è possibile usare il `az aks maintenanceconfiguration add` comando.

> [!IMPORTANT]
> Le finestre di manutenzione pianificate vengono specificate nel formato UTC (Coordinated Universal Time).

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

L'output di esempio seguente mostra la finestra di manutenzione da 1:00 a 2:00 ogni lunedì.

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

Per consentire la manutenzione in qualsiasi momento nel corso di un giorno, omettere il parametro dell' *ora di inizio* . Ad esempio, il comando seguente imposta la finestra di manutenzione per il giorno intero ogni lunedì:

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>Aggiungere una configurazione di manutenzione con un file JSON

È anche possibile usare un file JSON per creare una finestra di manutenzione anziché usare i parametri. Creare un `test.json` file con il contenuto seguente:

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

Il file JSON precedente specifica le finestre di manutenzione ogni martedì alle ore 11.00-3:00 e ogni mercoledì alle 11.00-2:00 e alle 6.00-7:00. Esiste anche un'eccezione da *2021-05-26T03:00:00Z* a *2021-05-30T12:00:00Z* , in cui la manutenzione non è consentita anche se si sovrappone a una finestra di manutenzione. Il comando che segue aggiunge le finestre di manutenzione da `test.json` .

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>Aggiorna una finestra di manutenzione esistente

Per aggiornare una configurazione di manutenzione esistente, usare il `az aks maintenanceconfiguration update` comando.

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>Elencare tutte le finestre di manutenzione in un cluster esistente

Per visualizzare tutte le finestre di configurazione di manutenzione correnti nel cluster AKS, usare il `az aks maintenanceconfiguration list` comando.

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

Nell'output seguente è possibile vedere che sono disponibili due finestre di manutenzione configurate per myAKSCluster. Una finestra è il lunedì alle 11.00 e un'altra finestra è il venerdì alle 4:00.

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

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>Mostra una finestra di configurazione di manutenzione specifica in un cluster AKS

Per visualizzare una finestra di configurazione di manutenzione specifica nel cluster AKS, usare il `az aks maintenanceconfiguration show` comando.

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

L'output di esempio seguente mostra la finestra di manutenzione per *impostazione predefinita*:

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

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>Eliminare una determinata finestra di configurazione di manutenzione in un cluster AKS esistente

Per eliminare una determinata finestra di configurazione di manutenzione nel cluster AKS, usare il `az aks maintenanceconfiguration delete` comando.

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare l'aggiornamento del cluster AKS, vedere [aggiornare un cluster AKS][aks-upgrade]


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[aks-upgrade]: upgrade-cluster.md
