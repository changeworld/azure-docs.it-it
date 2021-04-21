---
title: Applicare la licenza di Windows alle macchine virtuali host di sessione - Azure
description: Descrive come applicare la licenza di Windows per le macchine virtuali Desktop virtuale Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 08/14/2019
ms.author: helohr
ms.openlocfilehash: fa3c9f82e99536b07a27656e0143d6b2fcc89a44
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833869"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Applicare la licenza di Windows alle macchine virtuali host di sessione

I clienti con licenza appropriata per l'esecuzione di carichi di lavoro di Desktop virtuale Windows sono idonei per applicare una licenza di Windows alle macchine virtuali host di sessione ed eseguirle senza pagare un'altra licenza. Per altre informazioni, vedere [Prezzi di Desktop virtuale Windows.](https://azure.microsoft.com/pricing/details/virtual-desktop/)

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Modi per usare la licenza di Desktop virtuale Windows
Le licenze di Desktop virtuale Windows consentono di applicare una licenza a qualsiasi macchina virtuale Windows o Windows Server registrata come host sessione in un pool host e che riceve le connessioni utente. Questa licenza non si applica alle macchine virtuali in esecuzione come server di condivisione file, controller di dominio e così via.

Esistono alcuni modi per usare la licenza di Desktop virtuale Windows:
- È possibile creare un pool di host e le relative macchine virtuali host di sessione [usando l'Azure Marketplace che offre](./create-host-pools-azure-marketplace.md). Alle macchine virtuali create in questo modo viene applicata automaticamente la licenza.
- È possibile creare un pool host e le relative macchine virtuali host di sessione usando il modello [gitHub Azure Resource Manager .](./virtual-desktop-fall-2019/create-host-pools-arm-template.md) Alle macchine virtuali create in questo modo viene applicata automaticamente la licenza.
- È possibile applicare una licenza a una macchina virtuale host di sessione esistente. A tale scopo, seguire prima le istruzioni in Creare un pool di host con [PowerShell](./create-host-pools-powershell.md) per creare un pool di host e le macchine virtuali associate, quindi tornare a questo articolo per informazioni su come applicare la licenza.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Applicare una licenza di Windows a una macchina virtuale host di sessione
Verificare di aver prima [installato e configurato l'ultima versione di Azure PowerShell](/powershell/azure/). Eseguire il cmdlet di PowerShell seguente per applicare la licenza di Windows:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Verificare che la macchina virtuale host della sessione usa il vantaggio licenze
Dopo aver distribuito la macchina virtuale, eseguire questo cmdlet ot verificare il tipo di licenza:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Una macchina virtuale host di sessione con la licenza di Windows applicata mostrerà un aspetto simile al seguente:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Le macchine virtuali senza la licenza di Windows applicata mostreranno un aspetto simile al seguente:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Eseguire il cmdlet seguente per visualizzare un elenco di tutte le macchine virtuali host di sessione a cui è applicata la licenza di Windows nella sottoscrizione di Azure:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```

## <a name="requirements-for-deploying-windows-server-remote-desktop-services"></a>Requisiti per la distribuzione di Windows Server Servizi Desktop remoto

Se si distribuisce Windows Server 2019, 2016 o 2012 R2 come host di Desktop virtuale Windows nella distribuzione, un server licenze Servizi Desktop remoto deve essere accessibile da tali macchine virtuali. Il Servizi Desktop remoto server licenze può trovarsi in locale o in Azure. Per altre informazioni, vedere [Attivare il server Servizi Desktop remoto licenze.](/windows-server/remote/remote-desktop-services/rds-activate-license-server)
