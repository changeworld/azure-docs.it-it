---
title: Creare un dashboard del portale di Azure con l'interfaccia della riga di comando di Azure
description: "Avvio rapido: Informazioni su come creare un dashboard nel portale di Azure con l'interfaccia della riga di comando di Azure. Un dashboard offre una visualizzazione mirata e organizzata delle risorse cloud."
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.date: 12/4/2020
ms.openlocfilehash: ede915df5cd2967c3b6b700bcb9174c89af8f233
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745656"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-azure-cli"></a>Avvio rapido: Creare un dashboard del portale di Azure con l'interfaccia della riga di comando di Azure

Un dashboard nel portale di Azure offre una visualizzazione mirata e organizzata delle risorse cloud.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Se si hanno più sottoscrizioni di Azure, scegliere quella appropriata in cui fatturare le risorse.
Selezionare una sottoscrizione usando il comando [az account set](/cli/azure/account#az_account_set):

  ```azurecli
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```

- Creare un [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md) con il comando [az group create](/cli/azure/group#az_group_create) o usare un gruppo di risorse esistente:

  ```azurecli
  az group create --name myResourceGroup --location centralus
  ```

   Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Per creare una macchina virtuale, usare il comando [az vm create](/cli/azure/vm#az_vm_create):

```azurecli
az vm create --resource-group myResourceGroup --name SimpleWinVM --image win2016datacenter \
   --admin-username azureuser --admin-password 1StrongPassword$
```

> [!Note]
> La password deve essere complessa.
> Devono essere un nome utente e una password nuovi
> e non, ad esempio, quelli dell'account usato per accedere ad Azure.
> Per altre informazioni, vedere i [requisiti relativi ai nomi utente](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) e i [requisiti relativi alle password](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

La distribuzione viene avviata e in genere impiega alcuni minuti.
Al termine della distribuzione, passare alla sezione successiva.

## <a name="download-the-dashboard-template"></a>Scaricare il modello di dashboard

Poiché i dashboard di Azure sono risorse, possono essere rappresentati come JSON.
Per altre informazioni, vedere [Struttura dei dashboard di Azure](./azure-portal-dashboards-structure.md).

Scaricare il file [portal-dashboard-template-testvm.json](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json).

Personalizzare il modello scaricato sostituendo i propri valori ai valori seguenti:

* `<subscriptionID>`: Sottoscrizione in uso
* `<rgName>`: gruppo di risorse, ad esempio `myResourceGroup`
* `<vmName>`: nome della macchina virtuale, ad esempio `SimpleWinVM`
* `<dashboardTitle>`: titolo del dashboard, ad esempio `Simple VM Dashboard`
* `<location>`: area di Azure, ad esempio `centralus`

Per altre informazioni, vedere [Informazioni di riferimento sui modelli di dashboard nel portale Microsoft](/azure/templates/microsoft.portal/dashboards).

## <a name="deploy-the-dashboard-template"></a>Distribuire il modello di dashboard

È ora possibile distribuire il modello dall'interfaccia della riga di comando di Azure.

1. Eseguire il comando [az portal dashboard create](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) per distribuire il modello:

   ```azurecli
   az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
      --input-path portal-dashboard-template-testvm.json --location centralus
   ```

1. Verificare che il dashboard sia stato creato correttamente eseguendo il comando [az portal dashboard show](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show):

   ```azurecli
   az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
   ```

Per visualizzare tutti i dashboard della sottoscrizione corrente, usare [az portal dashboard list](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

È anche possibile visualizzare tutti i dashboard per un gruppo di risorse:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

È possibile aggiornare un dashboard con il comando [az portal dashboard update](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update):

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

Verificare che sia possibile visualizzare i dati relativi alla macchina virtuale dal portale di Azure.

1. Nel portale di Azure selezionare **Dashboard**.

   ![passaggio al dashboard nel portale di Azure](media/quickstart-portal-dashboard-powershell/navigate-to-dashboards.png)

1. Nella pagina del dashboard selezionare **Simple VM Dashboard**.

   ![Passare a Simple VM Dashboard](media/quickstart-portal-dashboard-powershell/select-simple-vm-dashboard.png)

1. Esaminare il dashboard. Come si può notare, parte del contenuto è statico, ma sono presenti anche alcuni grafici che mostrano le prestazioni della VM.

   ![Esaminare il dashboard Simple VM Dashboard](media/quickstart-portal-dashboard-powershell/review-simple-vm-dashboard.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere la macchina virtuale e il dashboard associato, eliminare il gruppo di risorse che li contiene.

> [!CAUTION]
> L'esempio seguente elimina il gruppo di risorse specificato e tutte le risorse al suo interno.
> Se nel gruppo di risorse specificato sono presenti anche risorse diverse da quelle usate in questo articolo, verranno eliminate.

```azurecli
az group delete --name myResourceGroup
```

Per rimuovere solo il dashboard, usare il comando [az portal dashboard delete](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_delete):

```azurecli
az portal dashboard delete --resource-group myResourceGroup --name "Simple VM Dashboard"
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul supporto dell'interfaccia della riga di comando di Azure per i dashboard, vedere [az portal dashboard](/cli/azure/ext/portal/portal/dashboard).
