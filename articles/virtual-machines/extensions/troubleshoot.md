---
title: Risoluzione degli errori delle estensioni VM Windows
description: Informazioni sulla risoluzione degli errori delle estensioni della macchina virtuale Windows di Azure
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/29/2016
ms.openlocfilehash: 8cc8a0b5ae0a83a152168b14a2c5577f8f7b48ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564799"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Risoluzione degli errori delle estensioni di macchina virtuale Windows di Azure
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Visualizzazione dello stato dell'estensione
I modelli di Azure Resource Manager possono essere eseguiti da Azure PowerShell. Una volta che il modello viene eseguito, sarà possibile visualizzare lo stato dell'estensione da Azure Resource Explorer o dagli strumenti da riga di comando.

Ecco un esempio:

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

Di seguito è riportato l'output di esempio:

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>Risoluzione degli errori delle estensioni

### <a name="rerun-the-extension-on-the-vm"></a>Eseguire nuovamente l'estensione nella macchina virtuale
Se si eseguono gli script nella macchina virtuale usando l'estensione script personalizzato, è possibile riscontrare in alcuni casi un errore in cui la creazione della macchina virtuale è riuscita, ma lo script ha avuto esito negativo. In queste condizioni, il metodo consigliato per risolvere il problema consiste nel rimuovere l'estensione ed eseguire nuovamente il modello.
Nota: In futuro, questa funzionalità potrebbe essere migliorata in modo da eliminare la necessità di disinstallazione dell'estensione.

#### <a name="remove-the-extension-from-azure-powershell"></a>Rimuovere l'estensione da Azure PowerShell
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

Una volta rimossa l'estensione, il modello può essere eseguito nuovamente per eseguire gli script nella macchina virtuale.

### <a name="trigger-a-new-goalstate-to-the-vm"></a>Attivare un nuovo GoalState alla macchina virtuale
È possibile notare che un'estensione non è stata eseguita o che non è in esecuzione a causa di un "generatore di certificati CRP di Windows Azure" mancante (il certificato viene usato per proteggere il trasporto delle impostazioni protette dell'estensione).
Il certificato verrà rigenerato automaticamente riavviando l'agente guest di Windows dall'interno della macchina virtuale:
- Aprire Gestione attività
- Vai alla scheda Dettagli
- Individuare il processo di WindowsAzureGuestAgent.exe
- Fare clic con il pulsante destro del mouse e scegliere "Termina attività". Il processo verrà riavviato automaticamente


È anche possibile attivare un nuovo GoalState per la macchina virtuale eseguendo una "macchina virtuale riapplicata". La [riapplicazione](/rest/api/compute/virtualmachines/reapply) della macchina virtuale è un'API introdotta in 2020 per riapplicare lo stato di una macchina virtuale. È consigliabile eseguire questa operazione in un momento in cui è possibile tollerare un breve tempo di inattività delle macchine virtuali. Sebbene Reapply stesso non provochi un riavvio della macchina virtuale e la maggior parte delle volte che si chiama riapply non riavvia la macchina virtuale, c'è un rischio molto ridotto che un altro aggiornamento in sospeso al modello di macchina virtuale viene applicato quando Reapply attiva un nuovo obiettivo e che potrebbe richiedere un riavvio. 

Portale di Azure:

Nel portale selezionare la macchina virtuale e nel riquadro sinistro sotto **supporto e risoluzione dei problemi** selezionare **Ridistribuisci + riapplica**, quindi selezionare **Riapplica**.


Azure PowerShell *(sostituire il nome RG e il nome della macchina virtuale con i valori seguenti)*:

```azurepowershell
Set-AzVM -ResourceGroupName <RG Name> -Name <VM Name> -Reapply
```

INTERFACCIA *della riga di comando di Azure (sostituire il nome RG e il nome della macchina virtuale con i valori)*:

```azurecli
az vm reapply -g <RG Name> -n <VM Name>
```

Se una "macchina virtuale riapplica" non funziona, è possibile aggiungere un nuovo disco dati vuoto alla macchina virtuale dalla portale di gestione di Azure e quindi rimuoverlo in un secondo momento dopo che il certificato è stato aggiunto nuovamente.