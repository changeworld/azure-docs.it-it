---
title: Dischi del sistema operativo temporanei
description: Altre informazioni sui dischi del sistema operativo phemeral per le macchine virtuali di Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 24b1be2ca55b057c887c8782ce7eea1150f143da
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762624"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Dischi del sistema operativo phemeral per macchine virtuali di Azure

I dischi del sistema operativo phemeral vengono creati nella risorsa di archiviazione della macchina virtuale locale e non vengono salvati nel Archiviazione di Azure. I dischi del sistema operativo phemeral funzionano bene per i carichi di lavoro senza stato, in cui le applicazioni sono a tolleranza di errori delle singole macchine virtuali, ma sono più influenzate dal tempo di distribuzione della macchina virtuale o dall'ottimizzazione delle singole istanze di macchina virtuale. Con il disco del sistema operativo phemeral si ottiene una latenza di lettura/scrittura inferiore per il disco del sistema operativo e una nuova immagine della macchina virtuale più veloce. 
 
Le funzionalità principali dei dischi ffimeri sono: 
- Ideale per le applicazioni senza stato.
- Possono essere usati sia con le immagini del Marketplace che con le immagini personalizzate.
- Possibilità di reimpostare o ricreare rapidamente l'immagine delle macchine virtuali e delle istanze del set di scalabilità allo stato di avvio originale.  
- Latenza più bassa, simile a un disco temporaneo. 
- I dischi del sistema operativo phemeral sono gratuiti, non si incorre in costi di archiviazione per il disco del sistema operativo.
- Sono disponibili in tutte le aree di Azure. 
- Il disco ffemero del sistema operativo è supportato da [Raccolta immagini condivise.](./shared-image-galleries.md) 
 

 
Differenze principali tra dischi del sistema operativo permanenti ed effimeri:

|                             | Disco del sistema operativo persistente                          | Disco del sistema operativo temporaneo                              |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| **Limite di dimensioni per il disco del sistema operativo**      | 2 TiB                                                                                        | Dimensioni della cache per le dimensioni della macchina virtuale o 2 TiB, a seconda di quale delle due dimensioni sia inferiore. Per le **dimensioni della cache in GiB,** vedere [DS](sizes-general.md), [ES](sizes-memory.md), [M](sizes-memory.md), [FS](sizes-compute.md)e [GS](sizes-previous-gen.md#gs-series)              |
| **Dimensioni delle macchine virtuali supportate**          | Tutti                                                                                          | Dimensioni delle macchine virtuali che supportano l'archiviazione Premium, ad esempio DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| **Supporto del tipo di disco**           | Disco del sistema operativo gestito e non gestito                                                                | Solo disco del sistema operativo gestito                                                               |
| **Supporto di area**              | Tutte le aree                                                                                  | Tutte le aree                              |
| **Salvataggio permanente dei dati**            | I dati del disco del sistema operativo scritti nel disco del sistema operativo vengono archiviati in Archiviazione di Azure                                  | I dati scritti nel disco del sistema operativo vengono archiviati nell'archiviazione locale della macchina virtuale e non vengono salvati in modo permanente Archiviazione di Azure. |
| **Stato di arresto deallocato**      | Le macchine virtuali e le istanze del set di scalabilità possono essere deallocate e riavviate dallo stato di arresto deallocato | Le macchine virtuali e le istanze del set di scalabilità non possono essere deallocate                                  |
| **Supporto di dischi del sistema operativo specializzato** | Sì                                                                                          | No                                                                                 |
| **Ridimensionamento del disco del sistema operativo**              | Supportato durante la creazione della macchina virtuale e dopo l'arresto della deallocazione della macchina virtuale                                | Supportato solo durante la creazione della macchina virtuale                                                  |
| **Ridimensionamento a una nuova dimensione della macchina virtuale**   | I dati del disco del sistema operativo vengono mantenuti                                                                    | I dati sul disco del sistema operativo vengono eliminati, viene eseguito di nuovo il provisioning del sistema operativo       
| **Posizionamento dei file di pagina**   | Per Windows, il file di pagina viene archiviato nel disco delle risorse                                              | Per Windows, il file di pagina viene archiviato nel disco del sistema operativo   |

## <a name="size-requirements"></a>Requisiti per le dimensioni

È possibile distribuire immagini di macchine virtuali e istanze fino alle dimensioni della cache della macchina virtuale. Ad esempio, le immagini standard di Windows Server dal marketplace sono circa 127 GiB, il che significa che è necessaria una dimensione di macchina virtuale con una cache maggiore di 127 GiB. In questo caso, la [Standard_DS2_v2](dv2-dsv2-series.md) ha una dimensione della cache di 86 GiB, che non è sufficientemente grande. La Standard_DS3_v2 ha una dimensione della cache di 172 GiB, sufficientemente grande. In questo caso, la Standard_DS3_v2 è la dimensione più piccola della serie DSv2 che è possibile usare con questa immagine. Le immagini Linux di base nel Marketplace e in Windows Server che sono denota da tendono a essere di circa 30 GiB e possono usare la maggior parte delle `[smallsize]` dimensioni di vm disponibili.

I dischi phemeral richiedono anche che le dimensioni della macchina virtuale supportino l'archiviazione Premium. Le dimensioni in genere (ma non sempre) hanno un nel nome, ad esempio `s` DSv2 ed EsV3. Per altre informazioni, vedere Dimensioni delle [macchine virtuali di Azure](sizes.md) per informazioni dettagliate sulle dimensioni che supportano l'archiviazione Premium.

## <a name="preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks"></a>Anteprima: i dischi temporanei del sistema operativo possono ora essere archiviati in dischi temporanei
I dischi del sistema operativo temporanei possono ora essere archiviati nel disco temporaneo/risorsa della macchina virtuale oltre che nella cache della macchina virtuale. È quindi ora possibile usare dischi del sistema operativo temporanei con una macchina virtuale che non ha una cache o non ha una cache sufficiente, ma ha un disco temporaneo/risorsa per archiviare il disco del sistema operativo temporaneo, ad esempio Dav3, Dav4, Eav4 ed Eav3. Se una macchina virtuale dispone di spazio temporaneo e cache sufficiente, sarà ora anche possibile specificare dove archiviare il disco temporaneo del sistema operativo usando una nuova proprietà denominata [DiffDiskPlacement.](/rest/api/compute/virtualmachines/list#diffdiskplacement) Con questa funzionalità, quando viene effettuato il provisioning di una macchina virtuale Windows, il file di paging viene configurato in modo che si trovi nel disco del sistema operativo. Questa funzionalità è attualmente in anteprima. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per iniziare, richiedere [l'accesso](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6cQw0fZJzdIsnbfbI13601URTBCRUZPMkQwWFlCOTRIMFBSNkM1NVpQQS4u)a .

## <a name="powershell"></a>PowerShell

Per usare un disco phemeral per la distribuzione di una macchina virtuale di PowerShell, [usare Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) nella configurazione della macchina virtuale. Impostare su `-DiffDiskSetting` `Local` e su `-Caching` `ReadOnly` .     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Per le distribuzioni di set di scalabilità, usare il cmdlet [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) nella configurazione. Impostare su `-DiffDiskSetting` `Local` e su `-Caching` `ReadOnly` .


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

Per usare un disco ffemero per la distribuzione di una macchina virtuale dell'interfaccia della riga di comando, impostare il parametro `--ephemeral-os-disk` in [az vm create](/cli/azure/vm#az_vm_create) su e il parametro su `true` `--os-disk-caching` `ReadOnly` .

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

Per i set di scalabilità, usare lo stesso `--ephemeral-os-disk true` parametro per [az-vmss-create](/cli/azure/vmss#az_vmss_create) e impostare il `--os-disk-caching` parametro su `ReadOnly` .

## <a name="portal"></a>Portale

Nel portale di Azure, è possibile scegliere di usare dischi effimeri quando si distribuisce una macchina virtuale aprendo la sezione Avanzate della **scheda** Dischi.  Per **Use ephemeral OS disk (Usa disco ffemero del sistema operativo)** selezionare **Sì**.

![Screenshot che mostra il pulsante di opzione per scegliere di usare un disco ffemero del sistema operativo](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Se l'opzione per l'uso di un disco ffemero è disattivata, è possibile che sia stata selezionata una dimensione della macchina virtuale che non ha dimensioni della cache maggiori dell'immagine del sistema operativo o che non supporta l'archiviazione Premium. Tornare alla pagina **Informazioni di** base e provare a scegliere un'altra dimensione della macchina virtuale.

È anche possibile creare set di scalabilità con dischi del sistema operativo effimeri usando il portale. Assicurarsi di selezionare una vm con dimensioni della cache sufficientemente grandi e quindi in Usa disco **ffemero** del sistema operativo selezionare **Sì**.

![Screenshot che mostra il pulsante di opzione per scegliere di usare un disco ffemero del sistema operativo per il set di scalabilità](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Distribuzione di modelli di set di scalabilità  
Il processo per creare un set di scalabilità che usa un disco del sistema operativo ffemero è quello di aggiungere la proprietà al tipo `diffDiskSettings` `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` di risorsa nel modello. Inoltre, i criteri di memorizzazione nella cache devono essere impostati su `ReadOnly` per il disco ffemero del sistema operativo. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
            "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>Distribuzione di modelli di macchina virtuale 
È possibile distribuire una macchina virtuale con un disco ffemero del sistema operativo usando un modello. Il processo per creare una macchina virtuale che usa dischi del sistema operativo ffimeri è aggiungere la proprietà al tipo di risorsa `diffDiskSettings` Microsoft.Compute/virtualMachines nel modello. Inoltre, i criteri di memorizzazione nella cache devono essere impostati su `ReadOnly` per il disco ffemero del sistema operativo. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Ricreare l'immagine di una macchina virtuale usando REST
È possibile ricreare l'immagine di un'istanza di macchina virtuale con un disco del sistema operativo ffemero usando l'API REST come descritto di seguito e tramite il portale di Azure passando al riquadro Panoramica della macchina virtuale. Per i set di scalabilità, la reimaging è già disponibile tramite PowerShell, l'interfaccia della riga di comando e il portale.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Domande frequenti

**D: Quali sono le dimensioni dei dischi del sistema operativo locali?**

A: Sono supportate le immagini personalizzate e della piattaforma, fino alle dimensioni della cache della macchina virtuale, in cui tutte le operazioni di lettura/scrittura nel disco del sistema operativo saranno locali nello stesso nodo della macchina virtuale. 

**D: È possibile ridimensionare il disco del sistema operativo phemeral?**

A: No, dopo il provisioning del disco del sistema operativo phemeral, il disco del sistema operativo non può essere ridimensionato. 

**D: È possibile collegare un Managed Disks a una macchina virtuale phemeral?**

A: Sì, è possibile collegare un disco dati gestito a una macchina virtuale che usa un disco del sistema operativo phemeral. 

**D: Tutte le dimensioni delle macchine virtuali saranno supportate per i dischi del sistema operativo phemeral?**

A: No, la maggior Archiviazione Premium sono supportate le dimensioni delle macchine virtuali (DS, ES, FS, GS, M e così via). Per sapere se una determinata dimensione di macchina virtuale supporta i dischi del sistema operativo phemeral, è possibile:

Chiamare il `Get-AzComputeResourceSku` cmdlet di PowerShell
```azurepowershell-interactive
 
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
   foreach($capability in $vmSize.capabilities)
   {
       if($capability.Name -eq 'EphemeralOSDiskSupported' -and $capability.Value -eq 'true')
       {
           $vmSize
       }
   }
}
```
 
**D: È possibile applicare il disco ffemero del sistema operativo alle macchine virtuali e ai set di scalabilità esistenti?**

A: No, il disco ffemero del sistema operativo può essere usato solo durante la creazione di macchine virtuali e set di scalabilità. 

**D: È possibile combinare dischi del sistema operativo normali e ffimeri in un set di scalabilità?**

A: No, non è possibile avere una combinazione di istanze di dischi del sistema operativo ffimeri e permanenti all'interno dello stesso set di scalabilità. 

**D: È possibile creare il disco ffemero del sistema operativo usando PowerShell o l'interfaccia della riga di comando?**

A: Sì, è possibile creare macchine virtuali con il disco del sistema operativo phemeral usando REST, modelli, PowerShell e l'interfaccia della riga di comando.

**D: Quali funzionalità non sono supportate con il disco del sistema operativo phemeral?**

A: I dischi ffimeri non supportano:
- Acquisizione di immagini di macchine virtuali
- Snapshot dei dischi 
- Crittografia dischi di Azure 
- Backup di Azure
- Azure Site Recovery  
- Scambio del disco del sistema operativo 

> [!NOTE]
> 
> Il disco ffemero non sarà accessibile tramite il portale. Si riceverà l'errore "Risorsa non trovata" o "404" quando si accede al disco phemeral previsto.
> 
 
## <a name="next-steps"></a>Passaggi successivi
È possibile creare una macchina virtuale con un disco ffemero del sistema operativo usando l'interfaccia della riga [di comando di Azure.](/cli/azure/vm#az_vm_create)
