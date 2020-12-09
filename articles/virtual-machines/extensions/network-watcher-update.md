---
title: Aggiornare l'estensione Network Watcher alla versione più recente
description: Informazioni su come aggiornare l'estensione Azure Network Watcher alla versione più recente.
services: virtual-machines-windows
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: 144320ea1b2505d8a43e1885091ec14a847e4ab1
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853663"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>Aggiornare l'estensione Network Watcher alla versione più recente

## <a name="overview"></a>Panoramica

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) è un servizio di monitoraggio, diagnostica e analisi delle prestazioni di rete che monitora le reti di Azure. L'estensione della macchina virtuale (VM) agente Network Watcher è un requisito per l'acquisizione del traffico di rete su richiesta e l'uso di altre funzionalità avanzate nelle VM di Azure. L'estensione Network Watcher viene utilizzata da funzionalità quali monitoraggio connessione, monitoraggio connessione (anteprima), risoluzione dei problemi di connessione e acquisizione pacchetti.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'estensione Network Watcher sia installata nella macchina virtuale.

## <a name="latest-version"></a>Versione più recente

È attualmente disponibile la versione più recente dell'estensione Network Watcher `1.4.1693.1` .

## <a name="update-your-extension-using-a-powershell-script"></a>Aggiornare l'estensione usando uno script di PowerShell
Clienti con distribuzioni di grandi dimensioni che devono aggiornare più macchine virtuali contemporaneamente. Per aggiornare manualmente le VM Select, vedere la sezione successiva. 

```powershell
<#
    .SYNOPSIS
    This script will scan all VMs in the provided subscription and upgrade any out of date AzureNetworkWatcherExtensions

    .DESCRIPTION
    This script should be no-op if AzureNetworkWatcherExtensions are up to date
    Requires Azure PowerShell 4.2 or higher to be installed (e.g. Install-Module AzureRM).

    .EXAMPLE
    .\UpdateVMAgentsInSub.ps1 -SubID F4BC4873-5DAB-491E-B713-1358EF4992F2 -NoUpdate

#>
[CmdletBinding()]
param(
    [Parameter(Mandatory=$true)]
    [string] $SubID,
    [Parameter(Mandatory=$false)]
    [Switch] $NoUpdate = $false,
    [Parameter(Mandatory=$false)]
    [string] $MinVersion = "1.4.1654.1"
)


function NeedsUpdate($version)
{
    if ($version -eq $MinVersion)
    {
        return $false
    }

    $lessThan = $true;
    $versionParts = $version -split '\.';
    $minVersionParts = $MinVersion -split '\.';
    for ($i = 0; $i -lt $versionParts.Length; $i++)
    {
        if ([int]$versionParts[$i] -gt [int]$minVersionParts[$i])
        {
            $lessThan = $false;
            break;
        }
    }

    return $lessThan
}

Write-Host "Scanning all VMs in the subscription: $($SubID)"
Select-AzSubscription -SubscriptionId $SubID;
$vms = Get-AzVM;
$foundVMs = $false;
Write-Host "Starting VM search, this may take a while"

foreach ($vmName in $vms)
{
    # Get Detailed VM info
    $vm = Get-AzVM -ResourceGroupName $vmName.ResourceGroupName -Name $vmName.name -Status;
    $isWindows = $vm.OsVersion -match "Windows";
    foreach ($extension in $vm.Extensions)
    {
        if ($extension.Name -eq "AzureNetworkWatcherExtension")
        {
            if (NeedsUpdate($extension.TypeHandlerVersion))
            {
                $foundVMs = $true;
                if (-not ($NoUpdate))
                {
                    Write-Host "Found VM that needs to be updated: subscriptions/$($SubID)/resourceGroups/$($vm.ResourceGroupName)/providers/Microsoft.Compute/virtualMachines/$($vm.Name) -> Updating " -NoNewline
                    Remove-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "AzureNetworkWatcherExtension" -Force
                    Write-Host "... " -NoNewline
                    $type = if ($isWindows) { "NetworkWatcherAgentWindows" } else { "NetworkWatcherAgentLinux" };
                    Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -Location $vmName.Location -VMName $vm.Name -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type $type -typeHandlerVersion "1.4"
                    Write-Host "Done"
                }
                else
                {
                    Write-Host "Found $(if ($isWindows) {"Windows"} else {"Linux"}) VM that needs to be updated: subscriptions/$($SubID)/resourceGroups/$($vm.ResourceGroupName)/providers/Microsoft.Compute/virtualMachines/$($vm.Name)"
                }
            }
        }
    }
}

if ($foundVMs)
{
    Write-Host "Finished $(if ($NoUpdate) {"searching"} else {"updating"}) out of date AzureNetworkWatcherExtension on VMs"
}
else
{
    Write-Host "All AzureNetworkWatcherExtensions up to date"
}

```

## <a name="update-your-extension-manually"></a>Aggiornare manualmente l'estensione

Per aggiornare l'estensione, è necessario essere a conoscenza della versione dell'estensione.

### <a name="check-your-extension-version"></a>Controllare la versione dell'estensione

È possibile controllare la versione dell'estensione usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell.

#### <a name="usetheazureportal"></a>Usare il portale di Azure

1. Passare al riquadro **estensioni** della macchina virtuale nella portale di Azure.
1. Selezionare l'estensione **AzureNetworkWatcher** per visualizzare il riquadro dei dettagli.
1. Individuare il numero di versione nel campo **versione** .  

#### <a name="use-the-azure-cli"></a>Usare l'interfaccia della riga di comando di Azure

Eseguire il comando seguente da un prompt dell'interfaccia della riga di comando di Azure:

```azurecli
az vm get-instance-view --resource-group  "SampleRG" --name "Sample-VM"
```
Individuare **"AzureNetworkWatcherExtension"** nell'output e identificare il numero di versione dal campo *"TypeHandlerVersion"* nell'output.  Nota: le informazioni sull'estensione vengono visualizzate più volte nell'output JSON. Esaminare il blocco "Extensions" per visualizzare il numero di versione completo dell'estensione. 

Verrà visualizzata una schermata simile alla seguente: schermata dell'interfaccia della riga di comando di ![ Azure](./media/network-watcher/azure-cli-screenshot.png)

#### <a name="usepowershell"></a>Usare PowerShell

Eseguire i comandi seguenti da un prompt di PowerShell:

```powershell
Get-AzVM -ResourceGroupName "SampleRG" -Name "Sample-VM" -Status
```
Individuare l'estensione Azure Network Watcher nell'output e identificare il numero di versione dal campo *"TypeHandlerVersion"* nell'output.   

Verrà visualizzata una schermata simile alla seguente: ![ PowerShell](./media/network-watcher/powershell-screenshot.png)

### <a name="update-your-extension"></a>Aggiornare l'estensione

Se la versione è inferiore alla versione più recente indicata sopra, aggiornare l'estensione usando una delle opzioni seguenti.

#### <a name="option-1-use-powershell"></a>Opzione 1: usare PowerShell

Eseguire i comandi seguenti:

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"

#Windows command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "NetworkWatcherAgentWindows" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -ForceRerun "True"

```

Se questo non funziona. Rimuovere e installare di nuovo l'estensione, eseguendo la procedura riportata di seguito. Verrà aggiunta automaticamente la versione più recente.

Rimozione dell'estensione 

```powershell
#Same command for Linux and Windows
Remove-AzVMExtension -ResourceGroupName "SampleRG" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension"
``` 

Installazione dell'estensione

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux" -typeHandlerVersion "1.4"

#Windows command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -typeHandlerVersion "1.4"
```

#### <a name="option-2-use-the-azure-cli"></a>Opzione 2: usare l'interfaccia della riga di comando di Azure

Forzare un aggiornamento.

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Se il tentativo non funziona, rimuovere e installare nuovamente l'estensione e attenersi alla procedura seguente per aggiungere automaticamente la versione più recente.

Rimuovere l'estensione.

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Installare di nuovo l'estensione.

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher"

```

#### <a name="option-3-reboot-your-vms"></a>Opzione 3: riavviare le VM

Se l'aggiornamento automatico è impostato su true per l'estensione Network Watcher, riavviare l'installazione della macchina virtuale nell'estensione più recente.

## <a name="support"></a>Supporto

Per ulteriori informazioni in qualsiasi punto di questo articolo, vedere la documentazione dell'estensione Network Watcher per [Linux](./network-watcher-linux.md) o [Windows](./network-watcher-windows.md). È anche possibile contattare gli esperti di Azure nei [Forum MSDN Azure e stack overflow](https://azure.microsoft.com/support/forums/). In alternativa, archiviare un evento imprevisto del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/)e selezionare **ottenere supporto**. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).
