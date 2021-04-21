---
title: Disabilitare o rimuovere l'agente di provisioning
description: Informazioni su come disabilitare o rimuovere l'agente di provisioning nelle macchine virtuali e nelle immagini Linux.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: c70b02bdc554c723f53ad5f8c0d36c5eca87811e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774369"
---
# <a name="disable-or-remove-the-linux-agent-from-vms-and-images"></a>Disabilitare o rimuovere l'agente Linux da macchine virtuali e immagini

Prima di rimuovere l'agente Linux, è necessario comprendere quale macchina virtuale non sarà in grado di eseguire dopo la rimozione dell'agente Linux.

Le estensioni delle [](../extensions/overview.md) macchine virtuali di Azure sono piccole applicazioni che forniscono attività di automazione e configurazione post-distribuzione nelle macchine virtuali di Azure, le estensioni vengono installate e gestite dal piano di controllo di Azure. È compito dell'agente Linux di [Azure](../extensions/agent-linux.md) elaborare i comandi di estensione della piattaforma e verificare lo stato corretto dell'estensione all'interno della macchina virtuale.

La piattaforma Azure ospita diverse estensioni, tra cui applicazioni di utilità, sicurezza, monitoraggio e configurazione delle VM. Esiste un'ampia scelta di estensioni di prima e di terze parti, esempi di scenari chiave per cui vengono usate le estensioni:
* Supporto di servizi di Azure di prima parte, ad esempio Backup di Azure, monitoraggio, crittografia del disco, sicurezza, replica del sito e altri.
* Reimpostazioni SSH/password
* Configurazione della macchina virtuale: esecuzione di script personalizzati, installazione di Chef, agenti Puppet e così via.
* Prodotti di terze parti, ad esempio prodotti av, strumenti di vulnerabilità delle macchine virtuali, macchine virtuali e strumenti di monitoraggio delle app.
* Le estensioni possono essere aggregate con una nuova distribuzione di VM. Possono, ad esempio, fare parte di una distribuzione più ampia, in le applicazioni vengono configurate durante il provisioning della VM, o venire eseguite nei sistemi gestiti dalle estensioni supportate dopo la distribuzione.

## <a name="disabling-extension-processing"></a>Disabilitazione dell'elaborazione dell'estensione

Esistono diversi modi per disabilitare l'elaborazione delle estensioni,  a seconda delle esigenze, ma prima di continuare, [](/cli/azure/vm/extension#az_vm_extension_list) è necessario rimuovere tutte le estensioni distribuite nella macchina virtuale, ad esempio usando l'interfaccia della riga di comando di Azure, è possibile elencare ed [eliminare](/cli/azure/vm/extension#az_vm_extension_delete):

```azurecli
az vm extension delete -g MyResourceGroup --vm-name MyVm -n extension_name
```
> [!Note]
> 
> Se non si fa questa operazione, la piattaforma tenterà di inviare la configurazione dell'estensione e il timeout dopo 40 minuti.

### <a name="disable-at-the-control-plane"></a>Disabilitare nel piano di controllo
Se non si è certi che saranno necessarie estensioni in futuro, è possibile lasciare installato l'agente Linux nella macchina virtuale e quindi disabilitare la funzionalità di elaborazione delle estensioni dalla piattaforma. Questa opzione è disponibile nella versione api o successiva e non ha una dipendenza dalla versione dell'agente `Microsoft.Compute` `2018-06-01` Linux installata.

```azurecli
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
È possibile riabilitarla facilmente dalla piattaforma, con il comando precedente, ma impostarla su "true".

## <a name="remove-the-linux-agent-from-a-running-vm"></a>Rimuovere l'agente Linux da una macchina virtuale in esecuzione

Assicurarsi di aver **rimosso tutte** le estensioni esistenti dalla macchina virtuale in precedenza, come indicato in precedenza.

### <a name="step-1-remove-the-azure-linux-agent"></a>Passaggio 1: Rimuovere l'agente Linux di Azure

Se si rimuove solo l'agente Linux e non gli artefatti di configurazione associati, è possibile reinstallare in un secondo momento. Eseguire una delle operazioni seguenti, come radice, per rimuovere l'agente Linux di Azure:

#### <a name="for-ubuntu-1804"></a>Per Ubuntu >=18.04
```bash
apt -y remove walinuxagent
```

#### <a name="for-redhat--77"></a>Per Redhat >= 7.7
```bash
yum -y remove WALinuxAgent
```

#### <a name="for-suse"></a>Per SUSE
```bash
zypper --non-interactive remove python-azure-agent
```

### <a name="step-2-optional-remove-the-azure-linux-agent-artifacts"></a>Passaggio 2: (facoltativo) Rimuovere gli artefatti dell'agente Linux di Azure
> [!IMPORTANT] 
>
> È possibile rimuovere tutti gli artefatti associati dell'agente Linux, ma ciò significa che non è possibile reinstallarlo in un secondo momento. Pertanto, è consigliabile disabilitare prima l'agente Linux, rimuovendo l'agente Linux solo usando il comando precedente. 

Se si sa che l'agente Linux non verrà reinstallato di nuovo, è possibile eseguire quanto segue:

#### <a name="for-ubuntu-1804"></a>Per Ubuntu >=18.04
```bash
apt -y purge walinuxagent
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-redhat--77"></a>Per Redhat >= 7.7
```bash
yum -y remove WALinuxAgent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-suse"></a>Per SUSE
```bash
zypper --non-interactive remove python-azure-agent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

## <a name="preparing-an-image-without-the-linux-agent"></a>Preparazione di un'immagine senza l'agente Linux
Se si dispone di un'immagine che contiene già cloud-init e si vuole rimuovere l'agente Linux, ma si esegue comunque il provisioning usando cloud-init, eseguire i passaggi nel passaggio 2 (e facoltativamente nel passaggio 3) come radice per rimuovere l'agente Linux di Azure e quindi le operazioni seguenti rimuoveranno la configurazione cloud-init e i dati memorizzati nella cache e prepareranno la macchina virtuale per creare un'immagine personalizzata.

```bash
cloud-init clean --logs --seed 
```

## <a name="deprovision-and-create-an-image"></a>Deprovisioning e creazione di un'immagine
L'agente Linux è in grado di pulire alcuni dei metadati dell'immagine esistenti, con il passaggio "waagent -deprovision+user", tuttavia, dopo la rimozione, sarà necessario eseguire azioni come quelle riportate di seguito e rimuovere eventuali altri dati sensibili.

- Rimuovere tutte le chiavi host SSH esistenti

   ```bash
   rm /etc/ssh/ssh_host_*key*
   ```
- Eliminare l'account amministratore

   ```bash
   touch /var/run/utmp
   userdel -f -r <admin_user_account>
   ```
- Eliminare la password radice

   ```bash
   passwd -d root
   ```

Dopo aver completato l'operazione precedente, è possibile creare l'immagine personalizzata usando l'interfaccia della riga di comando di Azure.


**Creare un'immagine gestita normale**
```azurecli
az vm deallocate -g <resource_group> -n <vm_name>
az vm generalize -g <resource_group> -n <vm_name>
az image create -g <resource_group> -n <image_name> --source <vm_name>
```

**Creare una versione dell'immagine in una raccolta immagini condivise**

```azurecli
az sig image-version create \
    -g $sigResourceGroup 
    --gallery-name $sigName 
    --gallery-image-definition $imageDefName 
    --gallery-image-version 1.0.0 
    --managed-image /subscriptions/00000000-0000-0000-0000-00000000xxxx/resourceGroups/imageGroups/providers/images/MyManagedImage
```
### <a name="creating-a-vm-from-an-image-that-does-not-contain-a-linux-agent"></a>Creazione di una macchina virtuale da un'immagine che non contiene un agente Linux
Quando si crea la macchina virtuale dall'immagine senza agente Linux, è necessario assicurarsi che la configurazione di distribuzione della macchina virtuale indichi che le estensioni non sono supportate in questa macchina virtuale.

> [!NOTE] 
> 
> Se non si fa questa operazione, la piattaforma tenterà di inviare la configurazione dell'estensione e il timeout dopo 40 minuti.

Per distribuire la macchina virtuale con le estensioni disabilitate, è possibile usare l'interfaccia della riga di comando di Azure [con --enable-agent](/cli/azure/vm#az_vm_create).

```azurecli
az vm create \
    --resource-group $resourceGroup \
    --name $prodVmName \
    --image RedHat:RHEL:8.1-ci:latest \
    --admin-username azadmin \
    --ssh-key-value "$sshPubkeyPath" \
    --enable-agent false
```

In alternativa, è possibile eseguire questa operazione usando Azure Resource Manager (ARM) impostando `"provisionVMAgent": false,` .

```json
"osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "provisionVMAgent": false,
        "ssh": {
            "publicKeys": [
                {
                    "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
                    "keyData": "[parameters('adminPublicKey')]"
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Provisioning di Linux.](provisioning.md)
