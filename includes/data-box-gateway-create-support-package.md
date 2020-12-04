---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: f002fec531a0355e803a1545990fc0b13b535742
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582176"
---
Se si verificano problemi relativi ai dispositivi, è possibile creare un pacchetto per il supporto dai registri di sistema. Supporto tecnico Microsoft utilizza questo pacchetto per risolvere i problemi. Per creare un pacchetto per il supporto, attenersi alla procedura seguente:

1. [Connettersi all'interfaccia di PowerShell del dispositivo](#connect-to-the-powershell-interface).
2. Usare il `Get-HcsNodeSupportPackage` comando per creare un pacchetto per il supporto. L'utilizzo del cmdlet è il seguente:

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    Il cmdlet raccoglie i log dal dispositivo e li copia in una rete o in una condivisione locale specificata.

    I parametri utilizzati sono i seguenti:

    - `-Path` -Specificare la rete o il percorso locale in cui copiare il pacchetto di supporto. (obbligatorio)
    - `-Credential` -Specificare le credenziali per accedere al percorso protetto.
    - `-Zip` -Specificare per generare un file zip.
    - `-Include` -Specificare per includere i componenti da includere nel pacchetto per il supporto. Se non specificato, `Default` viene utilizzato.
    - `-IncludeArchived` -Specificare per includere i log archiviati nel pacchetto per il supporto.
    - `-IncludePeriodicStats` -Specificare per includere i log stat periodici nel pacchetto per il supporto.

    
