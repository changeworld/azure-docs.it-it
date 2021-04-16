---
title: Abilitare l'estensione vm con l'interfaccia della riga di comando di Azure
description: Questo articolo descrive come distribuire estensioni di macchina virtuale in Azure Arc server abilitati in esecuzione in ambienti cloud ibridi usando l'interfaccia della riga di comando di Azure.
ms.date: 04/13/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 25e75ede30139201789cd86e6ebddda09a664eb4
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388738"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Abilitare le estensioni di macchine virtuali di Azure tramite l'interfaccia della riga di comando di Azure

Questo articolo illustra come distribuire e disinstallare le estensioni di macchina virtuale, supportate Azure Arc server abilitati, in un computer ibrido Linux o Windows usando l'interfaccia della riga di comando di Azure.

> [!NOTE]
> Azure Arc server abilitati non supporta la distribuzione e la gestione delle estensioni di macchina virtuale nelle macchine virtuali di Azure. Per le macchine virtuali di Azure, vedere l'articolo panoramica [dell'estensione vm](../../virtual-machines/extensions/overview.md) seguente.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando di Azure

I comandi ConnectedMachine non vengono forniti come parte dell'interfaccia della riga di comando di Azure. Prima di usare l'interfaccia della riga di comando di Azure per gestire le estensioni vm nel server ibrido gestito dai server abilitati per Arc, è necessario caricare l'estensione ConnectedMachine. Eseguire il comando seguente per ottenerlo:

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>Abilitare l'estensione

Per abilitare un'estensione vm nel server abilitato per Arc, usare [az connectedmachine extension create](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_create) con i parametri `--machine-name` , , , , e `--extension-name` `--location` `--type` `settings` `--publisher` .

L'esempio seguente abilita l'estensione di macchina virtuale Log Analytics in un server abilitato per Arc:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentForLinux or MicrosoftMonitoringAgent" --location "eastus" --settings '{\"workspaceId\":\"myWorkspaceId\"}' --protected-settings '{\"workspaceKey\":\"myWorkspaceKey\"}' --resource-group "myResourceGroup" --type-handler-version "1.13" --type "OmsAgentForLinux or MicrosoftMonitoringAgent" --publisher "Microsoft.EnterpriseCloud.Monitoring" 
```

L'esempio seguente abilita l'estensione script personalizzata in un server abilitato per Arc:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

L'esempio seguente abilita l'estensione Key Vault macchina virtuale (anteprima) in un server abilitato per Arc:

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>Elencare le estensioni installate

Per ottenere un elenco delle estensioni vm nel server abilitato per Arc, usare [az connectedmachine extension list](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_list) con i `--machine-name` parametri e `--resource-group` .

Esempio:

```azurecli
az connectedmachine extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Per impostazione predefinita, l'output del comando dell'interfaccia della riga di comando di Azure è in formato JSON, ovvero JavaScript Object Notation. Per trasformare l'output predefinito in un elenco o in una tabella, ad esempio, usare [az configure --output](/cli/azure/reference-index). È possibile anche aggiungere `--output` a qualsiasi comando per apportare una modifica una sola volta nel formato di output.

L'esempio seguente mostra l'output JSON parziale del `az connectedmachine extension -list` comando :

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>Rimuovere un'estensione installata

Per rimuovere un'estensione di macchina virtuale installata nel server abilitato per Arc, usare [az connectedmachine extension delete](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_delete) con `--extension-name` i parametri e `--machine-name` `--resource-group` .

Ad esempio, per rimuovere l'estensione della macchina virtuale Log Analytics per Linux, eseguire il comando seguente:

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentForLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi

- È possibile distribuire, gestire e rimuovere le estensioni macchina virtuale [usando Azure PowerShell](manage-vm-extensions-powershell.md), dal portale di Azure [o](manage-vm-extensions-portal.md) [Azure Resource Manager modelli](manage-vm-extensions-template.md).

- Le informazioni sulla risoluzione dei problemi sono disponibili nella guida [Risolvere i problemi relativi alle estensioni macchina virtuale.](troubleshoot-vm-extensions.md)

- Per altre informazioni sui comandi, vedere l'articolo [Panoramica](/cli/azure/ext/connectedmachine/connectedmachine/extension) dell'estensione vm dell'interfaccia della riga di comando di Azure.
