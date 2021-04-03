---
title: Modulo PowerShell per Azure Lab Services | Microsoft Docs
description: Questo articolo fornisce informazioni su un modulo di PowerShell che consente di gestire gli artefatti in Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4f990b35a41f040d34fab156d3f3d450ad7561a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94646526"
---
# <a name="azlabservices-powershell-module-preview"></a>Modulo Az.LabServices di PowerShell (anteprima)
AZ. LabServices è un modulo di PowerShell che semplifica la gestione di Azure Lab Services. Offre funzioni componibili per la creazione, l'esecuzione di query, l'aggiornamento e l'eliminazione di account Lab, Lab, macchine virtuali e immagini. Per ulteriori informazioni su questo modulo, vedere [AZ. LabServices Home page su GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Questo modulo è in **Anteprima**. 

## <a name="example-command"></a>Comando di esempio
Di seguito è riportato un esempio di utilizzo di un comando di PowerShell per arrestare tutte le macchine virtuali in esecuzione in tutti i Lab.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Introduzione
1. Installare [Azure PowerShell](/powershell/azure/) se non esiste nel computer. 
2. Scaricare [AZ. LabServices. psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) nel computer.
3. Importare il modulo:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Eseguire il comando seguente per elencare tutti i Lab nella sottoscrizione.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Passaggi successivi
Vedere [AZ. LabServices Home page su GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).