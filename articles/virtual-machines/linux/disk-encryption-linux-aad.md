---
title: Crittografia dischi di Azure con un'app Azure AD per macchine virtuali IaaS Linux (versione precedente)
description: Questo articolo offre informazioni sull'abilitazione di Crittografia dischi di Microsoft Azure per le macchine virtuali IaaS Linux.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 94440b71eee1ff9dcc4a86733582e3e5f57f6a00
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764622"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Abilitare Crittografia dischi di Azure con Azure AD in macchine virtuali Linux (versione precedente)

La nuova versione di Crittografia dischi di Azure elimina la necessità di fornire un parametro dell'applicazione Azure Active Directory (Azure AD) per abilitare la crittografia del disco della macchina virtuale. Con la nuova versione, non è più necessario specificare le credenziali di Azure AD durante il passaggio di abilitazione della crittografia. Tutte le nuove macchine virtuali devono essere crittografate senza Azure AD parametri dell'applicazione usando la nuova versione. Per istruzioni su come abilitare la crittografia del disco della macchina virtuale usando la nuova versione, vedere [Crittografia dischi di Azure per vm Linux](disk-encryption-linux.md). Le macchine virtuali che sono già state crittografate con i parametri dell'applicazione Azure AD sono ancora supportate e dovrebbero continuare a essere gestite con la sintassi di AAD.

È possibile abilitare molti scenari di crittografia del disco e i passaggi possono variare in base allo scenario. Le sezioni seguenti illustrano in modo più dettagliato gli scenari per le macchine virtuali IaaS (Infrastructure as a Service) Linux. È possibile applicare la crittografia del disco solo alle macchine virtuali con [dimensioni e sistemi operativi supportati](disk-encryption-overview.md#supported-vms-and-operating-systems). È anche necessario soddisfare i prerequisiti seguenti:

- [Requisiti aggiuntivi per le macchine virtuali](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Rete e Criteri di gruppo](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Requisiti di archiviazione delle chiavi di crittografia](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Creare uno [snapshot,](snapshot-copy-managed-disk.md)eseguire un backup o entrambi prima di crittografare i dischi. I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Le macchine virtuali con dischi gestiti richiedono il backup prima della crittografia. Dopo aver eseguito un backup, è possibile usare il cmdlet Set-AzVMDiskEncryptionExtension per crittografare i dischi gestiti specificando il parametro -skipVmBackup. Per altre informazioni su come eseguire il backup e il ripristino di macchine virtuali crittografate, [vedere](../../backup/backup-azure-vms-encryption.md)Backup di Azure . 

>[!WARNING]
 > - Se in precedenza è stato [usato Crittografia dischi di Azure'app Azure AD per](disk-encryption-overview-aad.md) crittografare questa macchina virtuale, è necessario continuare a usare questa opzione per crittografare la macchina virtuale. Non è possibile [](disk-encryption-overview.md) usare Crittografia dischi di Azure in questa macchina virtuale crittografata perché non si tratta di uno scenario supportato, il che significa che il passaggio dall'applicazione Azure AD per questa macchina virtuale crittografata non è ancora supportato.
 > - Per assicurarsi che i segreti di crittografia non attraversino i limiti dell'area, Crittografia dischi di Azure l'insieme di credenziali delle chiavi e le macchine virtuali devono trovarsi nella stessa area. Creare e usare un insieme di credenziali delle chiavi nella stessa area della macchina virtuale da crittografare.
 > - Quando si crittografano i volumi del sistema operativo Linux, il processo può richiedere alcune ore. È normale che i volumi del sistema operativo Linux esereranno più tempo rispetto ai volumi di dati da crittografare.
> - Quando si crittografa volumi del sistema operativo Linux, la macchina virtuale deve essere considerata non disponibile. È consigliabile evitare gli accessi SSH mentre è in corso la crittografia per evitare di bloccare eventuali file aperti a cui è necessario accedere durante il processo di crittografia. Per controllare lo stato, usare i [comandi Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) o [vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) Questo processo può richiedere alcune ore per un volume del sistema operativo da 30 GB, più tempo aggiuntivo per la crittografia dei volumi di dati. Il tempo di crittografia del volume di dati è proporzionale alle dimensioni e alla quantità dei volumi di dati, a meno che non venga usata l'opzione **encrypt format all.** 
 > - La disabilitazione della crittografia nelle macchine virtuali Linux è supportata solo per i volumi di dati. Non è supportata nei dati o nei volumi del sistema operativo se il volume del sistema operativo è stato crittografato. 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"> </a> Abilitare la crittografia in una macchina virtuale IaaS Linux esistente o in esecuzione

In questo scenario è possibile abilitare la crittografia usando il modello di Azure Resource Manager, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando di Azure. 

>[!IMPORTANT]
 >È obbligatorio creare uno snapshot o eseguire il backup di un'istanza di macchina virtuale basata su disco gestito all'esterno e prima di abilitare Crittografia dischi di Azure. È possibile creare uno snapshot del disco gestito dal portale di Azure oppure è possibile usare [Backup di Azure](../../backup/backup-azure-vms-encryption.md). I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Dopo aver eseguito un backup, usare il cmdlet Set-AzVMDiskEncryptionExtension per crittografare i dischi gestiti specificando il parametro -skipVmBackup. Il Set-AzVMDiskEncryptionExtension non riesce nelle macchine virtuali basate su disco gestito fino a quando non viene eseguito un backup e non viene specificato questo parametro. 
>
>La crittografia o la disabilitazione della crittografia potrebbe causare il riavvio della macchina virtuale. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Abilitare la crittografia in una macchina virtuale Linux esistente o in esecuzione usando l'interfaccia della riga di comando di Azure 
È possibile abilitare la crittografia del disco nel disco rigido virtuale crittografato installando e usando lo strumento da riga di comando dell'interfaccia della riga di comando di [Azure 2.0.](/cli/azure) È possibile usarlo nel browser con [Azure Cloud Shell](../../cloud-shell/overview.md) oppure installarlo nel computer locale e usarlo in una sessione di PowerShell. Per abilitare la crittografia in macchine virtuali IaaS Linux esistenti o in esecuzione in Azure, usare i comandi seguenti dell'interfaccia della riga di comando:

Usare il comando [az vm encryption enable](/cli/azure/vm/encryption#az_vm_encryption_enable) per abilitare la crittografia in una macchina virtuale IaaS in esecuzione in Azure.

-  **Crittografare una macchina virtuale in esecuzione usando un segreto client:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Crittografare una macchina virtuale in esecuzione usando la chiave di crittografia della chiave per eseguire il wrapping del segreto client:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > La sintassi per il valore del parametro disk-encryption-keyvault è la stringa dell'identificatore completo: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br> La sintassi per il valore del parametro key-encryption-key è l'URI completo della chiave kek come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

- **Verificare che i dischi siano crittografati:** Per controllare lo stato della crittografia di una macchina virtuale IaaS, usare [il comando az vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Disabilitare la crittografia:** per disabilitare la crittografia, usare il comando [az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable). È possibile disabilitare la crittografia solo nei volumi di dati per le macchine virtuali Linux.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"></a> Abilitare la crittografia in una macchina virtuale Linux esistente o in esecuzione tramite PowerShell
Usare il cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) per abilitare la crittografia in una macchina virtuale IaaS in esecuzione in Azure. Creare uno [snapshot](snapshot-copy-managed-disk.md) o eseguire un backup della macchina [virtuale con](../../backup/backup-azure-vms-encryption.md) Backup di Azure prima che i dischi siano crittografati. Il parametro -skipVmBackup è già specificato negli script di PowerShell per crittografare una macchina virtuale Linux in esecuzione.

- **Crittografare una macchina virtuale in esecuzione usando un segreto client:** Lo script seguente inizializza le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale, l'insieme di credenziali delle chiavi, Azure AD'app e il segreto client dovrebbero essere già stati creati come prerequisiti. Sostituire MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret con i valori. Modificare il parametro -VolumeType per specificare i dischi da crittografare.

     ```azurepowershell
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $KVRGname = 'MyKeyVaultResourceGroup';
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Crittografare una macchina virtuale in esecuzione usando kek per eseguire il wrapping del segreto client:** Crittografia dischi di Azure consente di specificare una chiave esistente nell'insieme di credenziali delle chiavi per eseguire il wrapping dei segreti di crittografia del disco generati durante l'abilitazione della crittografia. Quando viene specificata una chiave di crittografia della chiave, Crittografia dischi di Azure tale chiave viene utilizzata per eseguire il wrapping dei segreti di crittografia prima di scrivere nell'insieme di credenziali delle chiavi. Modificare il parametro -VolumeType per specificare i dischi da crittografare. 

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

  >[!NOTE]
  > La sintassi per il valore del parametro disk-encryption-keyvault è la stringa dell'identificatore completo: /subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br>
  La sintassi per il valore del parametro key-encryption-key è l'URI completo della chiave kek come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]. 
    
- **Verificare che i dischi siano crittografati:** Per controllare lo stato di crittografia di una macchina virtuale IaaS, usare il cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Disabilitare la crittografia del disco:** per disabilitare la crittografia, usare il cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). È possibile disabilitare la crittografia solo nei volumi di dati per le macchine virtuali Linux.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"> </a> Abilitare la crittografia in una macchina virtuale IaaS Linux esistente o in esecuzione con un modello

È possibile abilitare la crittografia dei dischi nelle macchine virtuali IaaS Linux esistenti o in esecuzione usando il modello di [Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Selezionare **Distribuisci in Azure nel** modello di avvio rapido di Azure.

2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, i parametri, i termini legali e il contratto. Selezionare **Crea** per abilitare la crittografia nella macchina virtuale IaaS esistente o in esecuzione.

La tabella seguente elenca i parametri del modello di Resource Manager per macchine virtuali esistenti o in esecuzione che usano un ID client di Azure AD:

| Parametro | Descrizione |
| --- | --- |
| AADClientID | ID client dell'applicazione Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| AADClientSecret | Segreto client dell'applicazione Azure AD con le autorizzazioni per la scrittura di segreti nell'insieme di credenziali delle chiavi. |
| keyVaultName | Nome dell'insieme di credenziali delle chiavi in cui dovrà essere caricata la chiave. È possibile ottenerlo con il comando dell'interfaccia della riga di comando di Azure `az keyvault show --name "MySecureVault" --query KVresourceGroup`. |
|  keyEncryptionKeyURL | URL della chiave di crittografia della chiave usata per crittografare la chiave generata. Questo parametro è facoltativo se si seleziona **nokek** nell'elenco a **discesa UseExistingKek.** Se si seleziona **kek** nell'elenco a **discesa UseExistingKek,** è necessario immettere il _valore keyEncryptionKeyURL._ |
| volumeType | Tipo del volume in cui viene eseguita l'operazione di crittografia. I valori supportati validi sono _OS_ o _All._ Vedere le distribuzioni Linux supportate e le relative versioni per i dischi del sistema operativo e dei dati nella sezione dei prerequisiti precedente. |
| sequenceVersion | Versione della sequenza dell'operazione BitLocker. Incrementare questo numero di versione ogni volta che viene eseguita un'operazione di crittografia dei dischi nella stessa VM. |
| vmName | Nome della VM in cui deve essere eseguita l'operazione di crittografia. |
| passphrase | Immettere una passphrase complessa come chiave di crittografia dei dati. |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>Usare la funzionalità EncryptFormatAll per i dischi dati nelle macchine virtuali IaaS Linux
Il parametro EncryptFormatAll riduce la durata per la crittografia dei dischi dati Linux. Le partizioni che soddisfano determinati criteri vengono formattate (con le relative file system). Vengono quindi rimontate nel punto in cui si trovavano prima dell'esecuzione del comando. Se si vuole escludere un disco dati che soddisfa i criteri, è possibile smontarlo prima di eseguire il comando.

 Dopo aver eseguito questo comando, tutte le unità montate in precedenza vengono formattate. Il livello di crittografia viene quindi avviato sull'unità ora vuota. Quando questa opzione è selezionata, viene crittografato anche il disco temporaneo collegato alla macchina virtuale. Se l'unità temporanea viene reimpostata, viene riformattata e crittografata nuovamente per la macchina virtuale dalla soluzione Crittografia dischi di Azure all'opportunità successiva.

>[!WARNING]
> EncryptFormatAll non deve essere usato quando sono presenti dati necessari nei volumi di dati di una macchina virtuale. È possibile escludere i dischi dalla crittografia smontandoli. Provare prima di tutto il parametro EncryptFormatAll in una macchina virtuale di test per comprendere il parametro della funzionalità e la sua implicazione prima di provarlo nella macchina virtuale di produzione. L'opzione EncryptFormatAll formatta il disco dati, in modo che tutti i dati su di esso andranno persi. Prima di procedere, verificare che tutti i dischi da escludere siano smontati correttamente. </br></br>
 >Se si imposta questo parametro durante l'aggiornamento delle impostazioni di crittografia, potrebbe verificarsi un riavvio prima della crittografia effettiva. In questo caso, è anche necessario rimuovere il disco che non si vuole formattare dal file fstab. Analogamente, è necessario aggiungere la partizione che si vuole formattare con crittografia al file fstab prima di avviare l'operazione di crittografia. 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"></a> Criteri EncryptFormatAll
Il parametro passa attraverso tutte le partizioni e  le crittografa purché soddisfino tutti i criteri seguenti: 
- Non è una partizione di avvio/del sistema operativo/radice
- Non è già stata crittografata
- Non è un volume BEK
- Non è un volume RAID
- Non è un volume LVM
- È montata

Crittografare i dischi che compongono il volume RAID o LVM anziché il volume stesso.

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"> </a> Usare il parametro EncryptFormatAll con un modello
Per usare l'opzione EncryptFormatAll, usare qualsiasi modello di Azure Resource Manager preesistente che crittografa una macchina virtuale Linux e modificare il campo **EncryptionOperation** per la risorsa AzureDiskEncryption.

1. Ad esempio, usare il [modello di Resource Manager per crittografare una macchina virtuale IaaS Linux in esecuzione](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Selezionare **Distribuisci in Azure nel** modello di avvio rapido di Azure.
3. Modificare il **campo EncryptionOperation** da **EnableEncryption a** **EnableEncryptionFormatAl**.
4. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, gli altri parametri, i termini legali e il contratto. Selezionare **Crea** per abilitare la crittografia nella macchina virtuale IaaS esistente o in esecuzione.


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"></a> Usare il parametro EncryptFormatAll con un cmdlet di PowerShell
Usare il cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) con il parametro EncryptFormatAll.

**Crittografare una macchina virtuale in esecuzione usando un segreto client e EncryptFormatAll:** Ad esempio, lo script seguente inizializza le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension con il parametro EncryptFormatAll. Il gruppo di risorse, la macchina virtuale, l'insieme di credenziali delle chiavi, Azure AD'app e il segreto client dovrebbero essere già stati creati come prerequisiti. Sostituire MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret con i valori.
  
   ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"></a> Usare il parametro EncryptFormatAll con Logical Volume Manager (LVM) 
È consigliabile usare una configurazione LVM-on-crypt. Per tutti gli esempi seguenti, sostituire device-path e mountpoints con qualsiasi elemento adatto al caso d'uso. La configurazione può essere eseguita nel seguente modo:

- Aggiungere i dischi dati che compongono la macchina virtuale.
- Formattare, montare e aggiungere i dischi al file fstab.

    1. Formattare il disco appena aggiunto. Qui vengono usati i collegamenti simbolici generati da Azure. L'uso di collegamenti simbolici evita i problemi legati alla modifica dei nomi dei dispositivi. Per altre informazioni, vedere Risolvere i [problemi relativi ai nomi dei dispositivi.](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)
    
        ```console
        mkfs -t ext4 /dev/disk/azure/scsi1/lun0
        ```

    2. Montare i dischi.

        ```console
        mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint
        ```

    3. Aggiungerli al file fstab.

        ```console
        echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab
        ```

    4. Eseguire il cmdlet Set-AzVMDiskEncryptionExtension PowerShell con -EncryptFormatAll per crittografare questi dischi.

       ```azurepowershell-interactive
        Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
        ```

    5. Configurare LVM sui nuovi dischi. Si noti che le unità crittografate vengono sbloccate dopo il termine dell'avvio della macchina virtuale. Pertanto, anche il montaggio di LVM dovrà essere posticipato.




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a> Nuove macchine virtuali IaaS create da chiavi di crittografia e disco rigido virtuale crittografato dal cliente
In questo scenario è possibile abilitare la crittografia usando il modello di Resource Manager, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. Le sezioni seguenti descrivono in modo più dettagliato il modello di Resource Manager e i comandi dell'interfaccia della riga di comando. 

Usare le istruzioni nell'appendice per la preparazione delle immagini pre-crittografate che possono essere usate in Azure. Dopo aver creato l'immagine, è possibile usare i passaggi della sezione successiva per creare una VM di Azure crittografata.

* [Preparare un disco rigido virtuale Linux pre-crittografato](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >È obbligatorio creare uno snapshot o eseguire il backup di un'istanza di macchina virtuale basata su disco gestita all'esterno di e prima di abilitare Crittografia dischi di Azure. È possibile creare uno snapshot del disco gestito dal portale oppure usare Backup di Azure [.](../../backup/backup-azure-vms-encryption.md) I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Dopo aver eseguito un backup, usare il cmdlet Set-AzVMDiskEncryptionExtension per crittografare i dischi gestiti specificando il parametro -skipVmBackup. Il Set-AzVMDiskEncryptionExtension comando non riesce nelle macchine virtuali basate su disco gestite fino a quando non viene eseguito un backup e non viene specificato questo parametro. 
>
>La crittografia o la disabilitazione della crittografia potrebbe causare il riavvio della macchina virtuale.



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"> </a> Usare Azure PowerShell per crittografare le macchine virtuali IaaS con dischi rigidi virtuali pre-crittografati 
È possibile abilitare la crittografia del disco nel disco rigido virtuale crittografato usando il cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples) di PowerShell. Nell'esempio seguente vengono forniti alcuni parametri comuni. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Abilitare la crittografia in un disco dati appena aggiunto
È possibile aggiungere un nuovo disco dati usando [az vm disk attach](add-disk.md) o [tramite](attach-disk-portal.md)il portale di Azure . Prima che sia possibile eseguire la crittografia, è necessario montare il disco appena collegato. È necessario richiedere la crittografia dell'unità dati perché l'unità sarà inutilizzabile mentre è in corso la crittografia. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Abilitare la crittografia in un disco appena aggiunto con l'interfaccia della riga di comando di Azure
 Se la macchina virtuale è stata crittografata in precedenza con "All", il parametro --volume-type deve rimanere All. Tale parametro include il disco del sistema operativo e il disco dati. Se la macchina virtuale è stata crittografata in precedenza con un tipo di volume "sistema operativo", il parametro --volume-type deve essere modificato in Tutti in modo che sia il sistema operativo che il nuovo disco dati siano inclusi. Se la macchina virtuale è stata crittografata solo con il tipo di volume "Dati", può rimanere Dati come illustrato qui. L'aggiunta e il collegamento di un nuovo disco dati a una macchina virtuale non è una preparazione sufficiente per la crittografia. Anche il disco appena collegato deve essere formattato e montato correttamente all'interno della macchina virtuale prima di abilitare la crittografia. In Linux il disco deve essere montato in /etc/fstab con un nome di dispositivo a [blocchi permanente](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems). 

A differenza della sintassi di PowerShell, l'interfaccia della riga di comando non richiede di fornire una versione di sequenza univoca quando si abilita la crittografia. L'interfaccia della riga di comando genera e usa automaticamente uno specifico valore di versione di sequenza univoco.

-  **Crittografare una macchina virtuale in esecuzione usando un segreto client:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Crittografare una macchina virtuale in esecuzione usando la chiave di crittografia della chiave per eseguire il wrapping del segreto client:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Abilitare la crittografia in un disco appena aggiunto con Azure PowerShell
 Quando si usa PowerShell per crittografare un nuovo disco per Linux, è necessario specificarne una nuova versione. La versione della sequenza deve essere univoca. Lo script seguente genera un GUID per la versione della sequenza. 
 

-  **Crittografare una macchina virtuale in esecuzione usando un segreto client:** Lo script seguente inizializza le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale, l'insieme di credenziali delle chiavi, Azure AD app e il segreto client devono essere già stati creati come prerequisiti. Sostituire MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret con i valori specificati. Il parametro -VolumeType è impostato sui dischi dati e non sul disco del sistema operativo. Se la macchina virtuale è stata crittografata in precedenza con un tipo di volume "OS" o "All", il parametro -VolumeType deve essere modificato in All in modo da includere sia il sistema operativo che il nuovo disco dati.

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup'; 
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```
- **Crittografare una macchina virtuale in esecuzione usando kek per eseguire il wrapping del segreto client:** Crittografia dischi di Azure consente di specificare una chiave esistente nell'insieme di credenziali delle chiavi per eseguire il wrapping dei segreti di crittografia del disco generati durante l'abilitazione della crittografia. Quando viene specificata una chiave di crittografia della chiave, Crittografia dischi di Azure tale chiave viene utilizzata per eseguire il wrapping dei segreti di crittografia prima di scrivere nell'insieme di credenziali delle chiavi. Il parametro -VolumeType è impostato sui dischi dati e non sul disco del sistema operativo. Se la macchina virtuale è stata crittografata in precedenza con un tipo di volume "OS" o "All", il parametro -VolumeType deve essere modificato in All in modo che sia il sistema operativo che il nuovo disco dati siano inclusi.

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $vmName = 'MyExtraSecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```


>[!NOTE]
> La sintassi per il valore del parametro disk-encryption-keyvault è la stringa dell'identificatore completo: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]. </br> </br>
La sintassi per il valore del parametro key-encryption-key è l'URI completo della chiave kek come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

## <a name="disable-encryption-for-linux-vms"></a>Disabilitare la crittografia per le macchine virtuali Linux
È possibile disabilitare la crittografia usando Azure PowerShell, l'interfaccia della riga di comando di Azure o un Resource Manager modello. 

>[!IMPORTANT]
>La disabilitazione della crittografia con Crittografia dischi di Azure nelle macchine virtuali Linux è supportata solo per i volumi di dati. Non è supportato nei dati o nei volumi del sistema operativo se il volume del sistema operativo è stato crittografato. 

- **Disabilitare la crittografia del disco con Azure PowerShell:** Per disabilitare la crittografia, usare il cmdlet [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Disabilitare la crittografia con l'interfaccia della riga di comando di Azure:** per disabilitare la crittografia, usare il comando [az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable). 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Disabilitare la crittografia con un Resource Manager predefinito:** Per disabilitare la crittografia, usare il [modello Disabilitare la crittografia in una macchina virtuale Linux in](https://aka.ms/decrypt-linuxvm) esecuzione.
     1. Selezionare **Distribuisci in Azure**.
     2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione, la macchina virtuale, i termini legali e il contratto.
     3. Selezionare **Acquista** per disabilitare la crittografia del disco in una macchina virtuale Windows in esecuzione. 


## <a name="next-steps"></a>Passaggi successivi

- [panoramica di Crittografia dischi di Azure per Linux](disk-encryption-overview-aad.md)
- [Creazione e configurazione di un insieme di credenziali delle chiavi Crittografia dischi di Azure con Azure AD (versione precedente)](disk-encryption-key-vault-aad.md)