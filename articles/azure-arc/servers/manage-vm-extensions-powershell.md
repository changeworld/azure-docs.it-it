---
title: Abilitare l'estensione vm usando Azure PowerShell
description: Questo articolo descrive come distribuire estensioni di macchina virtuale Azure Arc server abilitati in esecuzione in ambienti cloud ibridi usando Azure PowerShell.
ms.date: 04/13/2021
ms.topic: conceptual
ms.openlocfilehash: 0cb854c9745e8bd7eef35c6f6467c284a6327349
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388585"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Abilitare le estensioni di macchine virtuali di Azure usando Azure PowerShell

Questo articolo illustra come distribuire e disinstallare le estensioni di macchine virtuali di Azure, supportate dai server abilitati per Azure Arc, in un computer ibrido Linux o Windows usando Azure PowerShell.

> [!NOTE]
> Azure Arc server abilitati non supporta la distribuzione e la gestione delle estensioni di macchina virtuale nelle macchine virtuali di Azure. Per le macchine virtuali di Azure, vedere l'articolo panoramica [dell'estensione vm](../../virtual-machines/extensions/overview.md) seguente.

## <a name="prerequisites"></a>Prerequisiti

- Un computer con Azure PowerShell. Per istruzioni, vedere [Installare e configurare Azure PowerShell](/powershell/azure/).

Prima di Azure PowerShell per gestire le estensioni vm nel server ibrido gestito dai server abilitati per Arc, è necessario installare il `Az.ConnectedMachine` modulo. Eseguire il comando seguente nel server abilitato per Arc:

`Install-Module -Name Az.ConnectedMachine`.

Al termine dell'installazione, viene restituito il messaggio seguente:

`The installed extension `Az.ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Abilitare l'estensione

Per abilitare un'estensione vm nel server abilitato per Arc, usare [New-AzConnectedMachineExtension con](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) i parametri `-Name` , , , , , - e `-ResourceGroupName` `-MachineName` `-Location` `-Publisher` `ExtensionType` `-Settings` .

L'esempio seguente abilita l'estensione di macchina virtuale Log Analytics in un server Linux abilitato per Arc:

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType "OmsAgentForLinux"
```

Per abilitare l'estensione macchina virtuale Log Analytics in un server Windows abilitato per Arc, modificare il valore del parametro `-ExtensionType` `"MicrosoftMonitoringAgent"` in nell'esempio precedente.

L'esempio seguente abilita l'estensione script personalizzata in un server abilitato per Arc:

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

### <a name="key-vault-vm-extension-preview"></a>Key Vault macchina virtuale (anteprima)

> [!WARNING]
> I client PowerShell spesso aggiungono a nella settings.jsin cui si verifica un errore `\` `"` akvvm_service errore: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

L'esempio seguente abilita l'estensione Key Vault macchina virtuale (anteprima) in un server abilitato per Arc:

```powershell
# Build settings
    $settings = @{
      secretsManagementSettings = @{
       observedCertificates = @(
        "observedCert1"
       )
      certificateStoreLocation = "myMachineName" # For Linux use "/var/lib/waagent/Microsoft.Azure.KeyVault.Store/"
      certificateStore = "myCertificateStoreName"
      pollingIntervalInS = "pollingInterval"
      }
    authenticationSettings = @{
     msiEndpoint = "http://localhost:40342/metadata/identity"
     }
    }

    $resourceGroup = "resourceGroupName"
    $machineName = "myMachineName"
    $location = "regionName"

    # Start the deployment
    New-AzConnectedMachineExtension -ResourceGroupName $resourceGRoup -Location $location -MachineName $machineName -Name "KeyVaultForWindows or KeyVaultforLinux" -Publisher "Microsoft.Azure.KeyVault" -ExtensionType "KeyVaultforWindows or KeyVaultforLinux" -Setting (ConvertTo-Json $settings)
```

## <a name="list-extensions-installed"></a>Elencare le estensioni installate

Per ottenere un elenco delle estensioni vm nel server abilitato per Arc, usare [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) con i `-MachineName` parametri e `-ResourceGroupName` .

Esempio:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>Rimuovere un'estensione installata

Per rimuovere un'estensione di macchina virtuale installata nel server abilitato per Arc, [usare Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) con `-Name` i parametri e `-MachineName` `-ResourceGroupName` .

Ad esempio, per rimuovere l'estensione macchina virtuale Log Analytics per Linux, eseguire il comando seguente:

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>Passaggi successivi

- È possibile distribuire, gestire e rimuovere estensioni macchina virtuale usando l'interfaccia della riga di comando di [Azure](manage-vm-extensions-cli.md), [portale di Azure](manage-vm-extensions-portal.md)o [Azure Resource Manager modelli](manage-vm-extensions-template.md).

- Le informazioni sulla risoluzione dei problemi sono disponibili nella guida [Risolvere i problemi relativi alle estensioni macchina virtuale.](troubleshoot-vm-extensions.md)
