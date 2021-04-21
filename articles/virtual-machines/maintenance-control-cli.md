---
title: Controllo della manutenzione per le macchine virtuali di Azure tramite l'interfaccia della riga di comando
description: Informazioni su come controllare quando viene applicata la manutenzione alle macchine virtuali di Azure usando il controllo di manutenzione e l'interfaccia della riga di comando.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/20/2020
ms.author: cynthn
ms.openlocfilehash: c57f66eca5d15024c6b10e8fad12ddb575b9f894
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765900"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>Controllare gli aggiornamenti con Controllo manutenzione e l'interfaccia della riga di comando di Azure

Il controllo della manutenzione consente di decidere quando applicare gli aggiornamenti della piattaforma all'infrastruttura host delle macchine virtuali isolate e degli host dedicati di Azure. Questo argomento illustra le opzioni dell'interfaccia della riga di comando di Azure per il controllo di manutenzione. Per altre informazioni sui vantaggi dell'uso del controllo di manutenzione, delle relative limitazioni e di altre opzioni di gestione, vedere Gestione degli aggiornamenti della piattaforma [con controllo di manutenzione](maintenance-control.md).

## <a name="create-a-maintenance-configuration"></a>Creare una configurazione della manutenzione

Usare `az maintenance configuration create` per creare una configurazione di manutenzione. Questo esempio crea una configurazione di manutenzione denominata *myConfig* con ambito host. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host\
   --location eastus
```

Copiare l'ID di configurazione dall'output per usarlo in un secondo momento.

`--maintenance-scope host`L'uso di garantisce che la configurazione di manutenzione sia usata per controllare gli aggiornamenti all'infrastruttura host.

Se si tenta di creare una configurazione con lo stesso nome, ma in un percorso diverso, verrà visualizzato un errore. I nomi di configurazione devono essere univoci per il gruppo di risorse.

È possibile eseguire query per le configurazioni di manutenzione disponibili usando `az maintenance configuration list` .

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>Creare una configurazione di manutenzione con finestra pianificata
È anche possibile dichiarare una finestra pianificata quando Azure applierà gli aggiornamenti alle risorse. Questo esempio crea una configurazione di manutenzione denominata myConfig con una finestra pianificata di 5 ore il quarto lunedì di ogni mese. Dopo aver creato una finestra pianificata, non è più necessario applicare manualmente gli aggiornamenti.

```azurecli-interactive
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host \
   --location eastus \
   --maintenance-window-duration "05:00" \
   --maintenance-window-recur-every "Month Fourth Monday" \
   --maintenance-window-start-date-time "2020-12-30 08:00" \
   --maintenance-window-time-zone "Pacific Standard Time"
```

> [!IMPORTANT]
> La **durata della** manutenzione deve essere di *2 ore* o più. La **ricorrenza di manutenzione** deve essere impostata su almeno una volta in 35 giorni.

La ricorrenza di manutenzione può essere espressa come giornaliera, settimanale o mensile. Di seguito sono riportati alcuni esempi:
- **daily**- maintenance-window-recur-every: "Day" **o** "3Days"
- **weekly**- maintenance-window-recur-every: "3Weeks" **o** "Week Saturday,Sunday"
- **monthly**- maintenance-window-recur-every: "Month day23,day24" **o** "Month Last Sunday" **o** "Month Fourth Monday"


## <a name="assign-the-configuration"></a>Assegnare la configurazione

Usare `az maintenance assignment create` per assegnare la configurazione alla macchina virtuale isolata o host dedicato di Azure.

### <a name="isolated-vm"></a>Macchina virtuale isolata

Applicare la configurazione a una macchina virtuale usando l'ID della configurazione. Specificare e specificare il nome della macchina virtuale per e il gruppo di risorse per per la macchina virtuale in e il `--resource-type virtualMachines` percorso della macchina virtuale per `--resource-name` `--resource-group` `--location` . 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>Host dedicato

Per applicare una configurazione a un host dedicato, è necessario includere `--resource-type hosts` , con il nome del gruppo host e `--resource-parent-name` `--resource-parent-type hostGroups` . 

Il parametro `--resource-id` è l'ID dell'host. È possibile usare [az vm host get-instance-view](/cli/azure/vm/host#az_vm_host_get_instance_view) per ottenere l'ID dell'host dedicato.

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>Controllare la configurazione

È possibile verificare che la configurazione sia stata applicata correttamente o verificare quale configurazione è attualmente applicata usando `az maintenance assignment list` .

### <a name="isolated-vm"></a>Macchina virtuale isolata

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>Host dedicato 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>Verificare la presenza di aggiornamenti in sospeso

Usare `az maintenance update list` per verificare se sono presenti aggiornamenti in sospeso. Aggiornare --subscription in modo che sia l'ID della sottoscrizione che contiene la macchina virtuale.

Se non sono presenti aggiornamenti, il comando restituirà un messaggio di errore che conterrà il testo: `Resource not found...StatusCode: 404` .

Se sono presenti aggiornamenti, ne verrà restituito solo uno, anche se sono presenti più aggiornamenti in sospeso. I dati per questo aggiornamento verranno restituiti in un oggetto :

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>Macchina virtuale isolata

Verificare la presenza di aggiornamenti in sospeso per una macchina virtuale isolata. In questo esempio l'output viene formattato come tabella per la leggibilità.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Host dedicato

Per verificare la presenza di aggiornamenti in sospeso per un host dedicato. In questo esempio l'output viene formattato come tabella per la leggibilità. Sostituire i valori per le risorse con i propri.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>Applicare gli aggiornamenti

Usare `az maintenance apply update` per applicare gli aggiornamenti in sospeso. In caso di esito positivo, questo comando restituirà JSON contenente i dettagli dell'aggiornamento.

### <a name="isolated-vm"></a>Macchina virtuale isolata

Creare una richiesta per applicare gli aggiornamenti a una macchina virtuale isolata.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Host dedicato

Applicare gli aggiornamenti a un host dedicato.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>Controllare lo stato dell'applicazione degli aggiornamenti 

È possibile controllare lo stato degli aggiornamenti usando `az maintenance applyupdate get` . 

È possibile usare come nome dell'aggiornamento per visualizzare i risultati dell'ultimo aggiornamento o sostituire con il nome dell'aggiornamento restituito `default` `myUpdateName` durante l'esecuzione di `az maintenance applyupdate create` .

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime sarà l'ora in cui l'aggiornamento è stato completato, avviato dall'utente o dalla piattaforma nel caso in cui non sia stata usata la finestra di auto-manutenzione. Se non è mai stato applicato un aggiornamento tramite il controllo di manutenzione, verrà visualizzato il valore predefinito.

### <a name="isolated-vm"></a>Macchina virtuale isolata

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>Host dedicato

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Eliminare una configurazione di manutenzione

Usare `az maintenance configuration delete` per eliminare una configurazione di manutenzione. L'eliminazione della configurazione rimuove il controllo di manutenzione dalle risorse associate.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --resource-name myConfig
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere [Manutenzione e aggiornamenti.](maintenance-and-updates.md)
