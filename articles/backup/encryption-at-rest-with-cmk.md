---
title: Crittografia dei dati di backup tramite chiavi gestite dal cliente
description: Informazioni su Backup di Azure consente di crittografare i dati di backup usando chiavi gestite dal cliente .CMK.
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: bd51be06e707674f3e35b3478d7f99d096be912a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718774"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Crittografia dei dati di backup tramite chiavi gestite dal cliente

Backup di Azure consente di crittografare i dati di backup usando chiavi gestite dal cliente anziché chiavi gestite dalla piattaforma, che è abilitata per impostazione predefinita. Le chiavi usate per crittografare i dati di backup devono essere archiviate in [Azure Key Vault](../key-vault/index.yml).

La chiave di crittografia usata per crittografare i backup può essere diversa da quella usata per l'origine. I dati vengono protetti tramite una chiave DEK (Data Encryption Key) basata su AES 256, che a sua volta è protetta tramite le chiavi (KEK). In questo modo si ha il controllo completo sui dati e sulle chiavi. Per consentire la crittografia, è necessario che all'insieme di credenziali di Servizi di ripristino sia concesso l'accesso alla chiave di crittografia nel Azure Key Vault. È possibile modificare la chiave come e quando necessario.

Questo articolo illustra quanto segue:

- Creazione di un insieme di credenziali di Servizi di ripristino
- Configurazione dell'insieme di credenziali di Servizi di ripristino per crittografare i dati di backup usando chiavi gestite dal cliente
- Esecuzione del backup in insiemi di credenziali crittografati con chiavi gestite dal cliente
- Ripristino dei dati dai backup

## <a name="before-you-start"></a>Prima di iniziare

- Questa funzionalità consente di crittografare solo **i nuovi insiemi di credenziali di Servizi di ripristino.** Gli insiemi di credenziali contenenti elementi esistenti registrati o che hanno tentato di essere registrati in esso non sono supportati.

- Dopo l'allocazione per un insieme di credenziali di Servizi di ripristino, non è possibile ripristinare la crittografia con chiavi gestite dal cliente usando chiavi gestite dalla piattaforma (impostazione predefinita). È possibile modificare le chiavi di crittografia in base alle esigenze.

- Questa funzionalità attualmente non supporta il backup con **l'agente MARS** e potrebbe non essere possibile usare un insieme di credenziali crittografato con CMK per lo stesso. L'agente MARS usa una crittografia basata su passphrase dell'utente. Questa funzionalità non supporta anche il backup di macchine virtuali classiche.

- Questa funzionalità non è correlata a [Crittografia dischi di Azure](../security/fundamentals/azure-disk-encryption-vms-vmss.md), che usa la crittografia basata su guest dei dischi di una macchina virtuale usando BitLocker (per Windows) e DM-Crypt (per Linux)

- L'insieme di credenziali di Servizi di ripristino può essere crittografato solo con le chiavi archiviate in un Azure Key Vault, che si trova nella **stessa area**. Inoltre, le chiavi devono essere **solo chiavi RSA** e devono essere abilitate. 

- Lo spostamento dell'insieme di credenziali di Servizi di ripristino crittografati CMK tra gruppi di risorse e sottoscrizioni non è attualmente supportato.
- Quando si sposta un insieme di credenziali di Servizi di ripristino già crittografato con chiavi gestite dal cliente in un nuovo tenant, è necessario aggiornare l'insieme di credenziali di Servizi di ripristino per ricreare e riconfigurare l'identità gestita dell'insieme di credenziali e CMK (che dovrebbe essere nel nuovo tenant). In caso contrario, le operazioni di backup e ripristino inizieranno a non riuscire. Inoltre, tutte le autorizzazioni di controllo degli accessi in base al ruolo configurate all'interno della sottoscrizione dovranno essere riconfigurate.

- Questa funzionalità può essere configurata tramite portale di Azure e PowerShell.

    >[!NOTE]
    >Usare il modulo Az 5.3.0 o versione successiva per usare le chiavi gestite dal cliente per i backup nell'insieme di credenziali di Servizi di ripristino.
    
    >[!Warning]
    >Se si usa PowerShell per la gestione delle chiavi di crittografia per Backup, non è consigliabile aggiornare le chiavi dal portale.<br>Se si aggiorna la chiave dal portale, non è possibile usare PowerShell per aggiornare ulteriormente la chiave di crittografia, finché non è disponibile un aggiornamento di PowerShell per supportare il nuovo modello. È tuttavia possibile continuare ad aggiornare la chiave dal portale di Azure.

Se non è stato creato e configurato l'insieme di credenziali di Servizi di ripristino, è possibile leggere [qui](backup-create-rs-vault.md).

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Configurazione di un insieme di credenziali per la crittografia tramite chiavi gestite dal cliente

Questa sezione prevede i passaggi seguenti:

1. Abilitare l'identità gestita per l'insieme di credenziali di Servizi di ripristino

1. Assegnare le autorizzazioni all'insieme di credenziali per accedere alla chiave di crittografia nel Azure Key Vault

1. Abilitare la protezione dell'eliminazione e dell'eliminazione in Azure Key Vault

1. Assegnare la chiave di crittografia all'insieme di credenziali di Servizi di ripristino

È necessario che tutti questi passaggi siano seguiti nell'ordine indicato in precedenza per ottenere i risultati previsti. Ogni passaggio viene illustrato in dettaglio di seguito.

## <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Abilitare l'identità gestita per l'insieme di credenziali di Servizi di ripristino

Backup di Azure usa le identità gestite assegnate dal sistema e le identità gestite assegnate dall'utente per autenticare l'insieme di credenziali di Servizi di ripristino per accedere alle chiavi di crittografia archiviate nel Azure Key Vault. Per abilitare l'identità gestita per l'insieme di credenziali di Servizi di ripristino, seguire questa procedura.

>[!NOTE]
>Una volta abilitata, l'identità **gestita non** deve essere disabilitata (neanche temporaneamente). La disabilitazione dell'identità gestita può causare un comportamento incoerente.

### <a name="enable-system-assigned-managed-identity-for-the-vault"></a>Abilitare l'identità gestita assegnata dal sistema per l'insieme di credenziali

**Nel portale:**

1. Passare all'insieme di credenziali di Servizi di ripristino -> **Identity**

    ![Impostazioni di identità](media/encryption-at-rest-with-cmk/enable-system-assigned-managed-identity-for-vault.png)

1. Passare alla scheda **Assegnata dal** sistema.

1. Impostare Stato **su** **On**.

1. Fare clic **su Salva** per abilitare l'identità per l'insieme di credenziali.

Viene generato un ID oggetto, ovvero l'identità gestita assegnata dal sistema dell'insieme di credenziali.

>[!NOTE]
>Una volta abilitata, l'identità gestita non deve essere disabilitata (anche temporaneamente). La disabilitazione dell'identità gestita può causare un comportamento incoerente.


**Con PowerShell:**

Usare il [comando Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault) per abilitare l'identità gestita assegnata dal sistema per l'insieme di credenziali dei servizi di ripristino.

Esempio:

```AzurePowerShell
$vault=Get-AzRecoveryServicesVault -ResourceGroupName "testrg" -Name "testvault"

Update-AzRecoveryServicesVault -IdentityType SystemAssigned -VaultId $vault.ID

$vault.Identity | fl
```

Output:

```output
PrincipalId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
TenantId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Type        : SystemAssigned
```

### <a name="assign-user-assigned-managed-identity-to-the-vault"></a>Assegnare un'identità gestita assegnata dall'utente all'insieme di credenziali

Per assegnare l'identità gestita assegnata dall'utente per l'insieme di credenziali di Servizi di ripristino, seguire questa procedura:

1.  Passare all'insieme di credenziali di Servizi di ripristino -> **Identity**

    ![Assegnare un'identità gestita assegnata dall'utente all'insieme di credenziali](media/encryption-at-rest-with-cmk/assign-user-assigned-managed-identity-to-vault.png)

1.  Passare alla **scheda Assegnata dall'utente.**

1.  Fare **clic su +Aggiungi** per aggiungere un'identità gestita assegnata dall'utente.

1.  Nel pannello **Aggiungi identità gestita assegnata dall'utente** visualizzato selezionare la sottoscrizione per l'identità.

1.  Selezionare l'identità dall'elenco. È anche possibile filtrare in base al nome dell'identità o del gruppo di risorse.

1.  Al termine, fare clic **su Aggiungi** per completare l'assegnazione dell'identità.

## <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Assegnare le autorizzazioni all'insieme di credenziali di Servizi di ripristino per accedere alla chiave di crittografia nel Azure Key Vault

>[!Note]
>Se si usano identità assegnate dall'utente, è necessario assegnare le stesse autorizzazioni all'identità assegnata dall'utente.

È ora necessario consentire all'insieme di credenziali di Servizi di ripristino di accedere Azure Key Vault che contiene la chiave di crittografia. Questa operazione viene eseguita consentendo all'identità gestita dell'insieme di credenziali di Servizi di ripristino di accedere alla Key Vault.

**Nel portale**:

1. Passare ai criteri Azure Key Vault -> **di accesso .** Continuare con **+Aggiungi criteri di accesso**.

    ![Aggiungere criteri di accesso](./media/encryption-at-rest-with-cmk/access-policies.png)

1. In **Autorizzazioni chiave** selezionare **Ottieni**, **Elenca**, Annulla wrapping **della chiave** ed Eseguire il wrapping delle **operazioni** chiave. Specifica le azioni sulla chiave che saranno consentite.

    ![Assegnare le autorizzazioni chiave](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. Passare a **Seleziona entità e** cercare l'insieme di credenziali nella casella di ricerca usando il nome o l'identità gestita. Quando viene visualizzato, selezionare l'insieme di credenziali e scegliere **Seleziona** nella parte inferiore del riquadro.

    ![Selezionare un'entità](./media/encryption-at-rest-with-cmk/select-principal.png)

1. Al termine, selezionare **Aggiungi per** aggiungere il nuovo criterio di accesso.

1. Selezionare **Salva** per salvare le modifiche apportate ai criteri di accesso del Azure Key Vault.

## <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>Abilitare l'eliminazione e la protezione dall'eliminazione nel Azure Key Vault

È necessario abilitare **l'eliminazione e la protezione** dall'eliminazione Azure Key Vault in cui è archiviata la chiave di crittografia. È possibile eseguire questa operazione dall'interfaccia Azure Key Vault utente, come illustrato di seguito. In alternativa, è possibile impostare queste proprietà durante la creazione del Key Vault. Altre informazioni su queste Key Vault proprietà [sono disponibili qui.](../key-vault/general/soft-delete-overview.md)

![Abilitare l'eliminazione temporanea e la protezione dalla rimozione definitiva](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

È anche possibile abilitare l'eliminazione e la protezione dall'eliminazione tramite PowerShell usando la procedura seguente:

1. Accedere all'account Azure personale.

    ```azurepowershell
    Login-AzAccount
    ```

1. Selezionare la sottoscrizione che contiene l'insieme di credenziali.

    ```azurepowershell
    Set-AzContext -SubscriptionId SubscriptionId
    ```

1. Abilitare l'eliminazione temporanea

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

1. Abilitare la protezione dall'eliminazione

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="assign-encryption-key-to-the-rs-vault"></a>Assegnare la chiave di crittografia all'insieme di credenziali RS

>[!NOTE]
> Prima di procedere, verificare quanto segue:
>
> - Tutti i passaggi indicati in precedenza sono stati completati correttamente:
>   - L'identità gestita dell'insieme di credenziali di Servizi di ripristino è stata abilitata e sono state assegnate le autorizzazioni necessarie
>   - Nel Azure Key Vault è abilitata l'eliminazione e la protezione dall'eliminazione
> - L'insieme di credenziali di Servizi di ripristino per cui si vuole abilitare la crittografia CMK non **dispone** di elementi protetti o registrati

Una volta verificate le operazioni precedenti, continuare con la selezione della chiave di crittografia per l'insieme di credenziali.

### <a name="to-assign-the-key-in-the-portal"></a>Per assegnare la chiave nel portale

1. Passare all'insieme di credenziali di Servizi di ripristino -> **proprietà**

    ![Impostazioni di crittografia](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. Selezionare **Aggiorna** in **Impostazioni di crittografia**.

1. Nel riquadro Impostazioni di crittografia selezionare **Usa la propria chiave** e continuare a specificare la chiave usando uno dei modi seguenti. **Assicurarsi che la chiave da usare sia una chiave RSA 2048, che si trova in uno stato abilitato.**

    1. Immettere **l'URI della chiave** con cui si desidera crittografare i dati in questo insieme di credenziali di Servizi di ripristino. È anche necessario specificare la sottoscrizione in cui è presente il Azure Key Vault (che contiene questa chiave). Questo URI della chiave può essere ottenuto dalla chiave corrispondente nel Azure Key Vault. Assicurarsi che l'URI della chiave venga copiato correttamente. È consigliabile usare il pulsante Copia negli **Appunti** fornito con l'identificatore di chiave.

        >[!NOTE]
        >Quando si specifica la chiave di crittografia usando l'URI della chiave, la chiave non verrà ruotata automaticamente. È quindi necessario eseguire manualmente gli aggiornamenti delle chiavi specificando la nuova chiave quando necessario.

        ![Immettere l'URI della chiave](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. Individuare e selezionare la chiave dal Key Vault nel riquadro di selezione delle chiavi.

        >[!NOTE]
        >Quando si specifica la chiave di crittografia usando il riquadro di selezione delle chiavi, la chiave verrà ruotata automaticamente ogni volta che viene abilitata una nuova versione per la chiave. [Altre informazioni](#enabling-auto-rotation-of-encryption-keys) sull'abilitazione della rotazione automatica delle chiavi di crittografia.

        ![Selezionare la chiave dall'insieme di credenziali delle chiavi](./media/encryption-at-rest-with-cmk/key-vault.png)

1. Selezionare **Salva**.

1. **Monitoraggio dello stato e dello stato dell'aggiornamento** della chiave di crittografia: è possibile tenere traccia dello stato e dello stato dell'assegnazione della chiave di crittografia usando la visualizzazione **Processi di backup** sulla barra di spostamento a sinistra. Lo stato dovrebbe presto cambiare in **Completato.** L'insieme di credenziali crittograferà ora tutti i dati con la chiave specificata come KEK.

    ![Stato completato](./media/encryption-at-rest-with-cmk/status-succeeded.png)

    Gli aggiornamenti delle chiavi di crittografia vengono registrati anche nel log attività dell'insieme di credenziali.

    ![Log attività](./media/encryption-at-rest-with-cmk/activity-log.png)

### <a name="to-assign-the-key-with-powershell"></a>Per assegnare la chiave con PowerShell

Usare il [comando Set-AzRecoveryServicesVaultProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultproperty) per abilitare la crittografia usando chiavi gestite dal cliente e per assegnare o aggiornare la chiave di crittografia da usare.

Esempio:

```azurepowershell
$keyVault = Get-AzKeyVault -VaultName "testkeyvault" -ResourceGroupName "testrg" 
$key = Get-AzKeyVaultKey -VaultName $keyVault -Name "testkey" 
Set-AzRecoveryServicesVaultProperty -EncryptionKeyId $key.ID -KeyVaultSubscriptionId "xxxx-yyyy-zzzz"  -VaultId $vault.ID


$enc=Get-AzRecoveryServicesVaultProperty -VaultId $vault.ID
$enc.encryptionProperties | fl
```

Output:

```output
EncryptionAtRestType          : CustomerManaged
KeyUri                        : testkey
SubscriptionId                : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
LastUpdateStatus              : Succeeded
InfrastructureEncryptionState : Disabled
```

>[!NOTE]
> Questo processo rimane invariato quando si vuole aggiornare o modificare la chiave di crittografia. Se si vuole aggiornare e usare una chiave di un altro Key Vault (diversa da quella attualmente in uso), assicurarsi che:
>
> - L'insieme di credenziali delle chiavi si trova nella stessa area dell'insieme di credenziali di Servizi di ripristino
>
> - Per l'insieme di credenziali delle chiavi è abilitata l'eliminazione e la protezione dall'eliminazione
>
> - L'insieme di credenziali di Servizi di ripristino dispone delle autorizzazioni necessarie per accedere all'insieme di credenziali delle chiavi.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>Backup in un insieme di credenziali crittografato con chiavi gestite dal cliente

Prima di procedere alla configurazione della protezione, è consigliabile assicurarsi che sia rispettato l'elenco di controllo seguente. Questo è importante perché dopo aver configurato un elemento per il backup (o il tentativo di configurazione) in un insieme di credenziali non crittografato con la chiave gestita dal cliente, la crittografia con chiavi gestite dal cliente non può essere abilitata e continuerà a usare chiavi gestite dalla piattaforma.

>[!IMPORTANT]
> Prima di procedere alla configurazione della protezione, è necessario **aver** completato i passaggi seguenti:
>
>1. Creazione dell'insieme di credenziali per il backup
>1. A questo punto è stata abilitata l'identità gestita assegnata dal sistema dell'insieme di credenziali di Servizi di ripristino o è stata assegnata un'identità gestita assegnata dall'utente all'insieme di credenziali
>1. Autorizzazioni assegnate all'insieme di credenziali di Backup (o all'identità gestita assegnata dall'utente) per accedere alle chiavi di crittografia dall'Key Vault
>1. È stata abilitata l'eliminazione e la protezione dall'eliminazione per Key Vault
>1. È stata assegnata una chiave di crittografia valida per l'insieme di credenziali di Backup
>
>Se tutti i passaggi precedenti sono stati confermati, procedere con la configurazione del backup.

Il processo di configurazione ed esecuzione di backup in un insieme di credenziali di Servizi di ripristino crittografato con chiavi gestite dal cliente è identico a quello di un insieme di credenziali che usa chiavi gestite dalla piattaforma, senza apportare modifiche **all'esperienza**. Ciò vale per [il backup](./quick-backup-vm-portal.md) di macchine virtuali di Azure e per il backup di carichi di lavoro in esecuzione all'interno di una macchina virtuale , ad esempio SAP HANA [,](./tutorial-backup-sap-hana-db.md) [SQL Server](./tutorial-sql-backup.md) database.

## <a name="restoring-data-from-backup"></a>Ripristino dei dati dal backup

### <a name="vm-backup"></a>Backup di macchine virtuali

I dati archiviati nell'insieme di credenziali di Servizi di ripristino possono essere ripristinati in base alla procedura descritta [qui.](./backup-azure-arm-restore-vms.md) Quando si esegue il ripristino da un insieme di credenziali di Servizi di ripristino crittografato con chiavi gestite dal cliente, è possibile scegliere di crittografare i dati ripristinati con un set di crittografia del disco (DES).

#### <a name="restoring-vm--disk"></a>Ripristino di macchine virtuali/dischi

1. Quando si ripristina un disco o una macchina virtuale da un punto di ripristino "Snapshot", i dati ripristinati verranno crittografati con il DES usato per crittografare i dischi della macchina virtuale di origine.

1. Quando si ripristina un disco o una macchina virtuale da un punto di ripristino con Tipo di ripristino come "Insieme di credenziali", è possibile scegliere di crittografare i dati ripristinati usando un DES, specificato al momento del ripristino. In alternativa, è possibile scegliere di continuare con il ripristino dei dati senza specificare un DES, nel qual caso verranno crittografati usando chiavi gestite da Microsoft.

È possibile crittografare il disco/macchina virtuale ripristinato al termine del ripristino, indipendentemente dalla selezione effettuata durante l'avvio del ripristino.

![Punti di ripristino](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>Selezionare un set di crittografia del disco durante il ripristino dal punto di ripristino dell'insieme di credenziali

**Nel portale**:

Il set di crittografia del disco viene specificato in Impostazioni di crittografia nel riquadro di ripristino, come illustrato di seguito:

1. In **Encrypt disk(s) using your key**(Crittografa dischi con la chiave) selezionare **Sì**.

1. Nell'elenco a discesa selezionare il DES da usare per i dischi ripristinati. **Assicurarsi di avere accesso al des.**

>[!NOTE]
>La possibilità di scegliere un DES durante il ripristino non è disponibile se si ripristina una macchina virtuale che usa Crittografia dischi di Azure.

![Crittografare il disco usando la chiave](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

**Con PowerShell**:

Usare il [comando Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) con il parametro [ ] per specificare il DES da usare per crittografare `-DiskEncryptionSetId <string>` il disco ripristinato. [](/powershell/module/az.compute/get-azdiskencryptionset) Per altre informazioni sul ripristino dei dischi dal backup delle macchine virtuali, vedere [questo articolo.](./backup-azure-vms-automation.md#restore-an-azure-vm)

Esempio:

```azurepowershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $vault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $vault.ID
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $vault.ID
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -DiskEncryptionSetId “testdes1” -VaultId $vault.ID
```

#### <a name="restoring-files"></a>Ripristino di file

Quando si esegue un ripristino di file, i dati ripristinati verranno crittografati con la chiave usata per crittografare il percorso di destinazione.

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>Ripristino di SAP HANA/SQL nelle macchine virtuali di Azure

Quando si esegue il ripristino da un database SAP HANA/SQL di cui è stato eseguito il backup in una macchina virtuale di Azure, i dati ripristinati verranno crittografati usando la chiave di crittografia usata nel percorso di archiviazione di destinazione. Può essere una chiave gestita dal cliente o una chiave gestita dalla piattaforma usata per crittografare i dischi della macchina virtuale.

## <a name="additional-topics"></a>Argomenti aggiuntivi

### <a name="enable-encryption-using-customer-managed-keys-at-vault-creation-in-preview"></a>Abilitare la crittografia usando chiavi gestite dal cliente durante la creazione dell'insieme di credenziali (in anteprima)

>[!NOTE]
>L'abilitazione della crittografia per la creazione dell'insieme di credenziali tramite chiavi gestite dal cliente è disponibile in anteprima pubblica limitata e richiede l'elenco di sottoscrizioni consentite. Per iscriversi all'anteprima, compilare il [modulo e](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURDNTVVhGOUxXSVBZMEwxUU5FNDkyQkU4Ny4u) scrivere all'indirizzo [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

Quando la sottoscrizione è consentita, viene visualizzata la scheda **Crittografia** di backup. In questo modo è possibile abilitare la crittografia nel backup usando chiavi gestite dal cliente durante la creazione di un nuovo insieme di credenziali di Servizi di ripristino. Per abilitare la crittografia, seguire questa procedura:

1. Accanto alla scheda **Informazioni di base,** nella scheda Crittografia di **backup** specificare la chiave di crittografia e l'identità da usare per la crittografia.

   ![Abilitare la crittografia a livello di insieme di credenziali](media/encryption-at-rest-with-cmk/enable-encryption-using-cmk-at-vault.png)


   >[!NOTE]
   >Le impostazioni si applicano solo a Backup e sono facoltative.

1. Selezionare **Usa chiave gestita dal cliente** come Tipo di crittografia.

1. Per specificare la chiave da usare per la crittografia, selezionare l'opzione appropriata.

   È possibile specificare l'URI per la chiave di crittografia oppure cercare e selezionare la chiave. Quando si specifica la chiave usando l'opzione **Seleziona Key Vault,** la rotazione automatica della chiave di crittografia verrà abilitata automaticamente. [Altre informazioni sulla rotazione automatica.](#enabling-auto-rotation-of-encryption-keys) 

1. Specificare l'identità gestita assegnata dall'utente per gestire la crittografia con le chiavi gestite dal cliente. Fare **clic su** Seleziona per esplorare e selezionare l'identità richiesta.

1. Al termine, procedere con l'aggiunta di tag (facoltativo) e continuare a creare l'insieme di credenziali.

### <a name="enabling-auto-rotation-of-encryption-keys"></a>Abilitazione della rotazione automatica delle chiavi di crittografia

Quando si specifica la chiave gestita dal cliente che deve essere usata per crittografare i backup, usare i metodi seguenti per specificarla:

- Immettere l'URI della chiave
- Selezionare un'Key Vault

**L'uso dell'Key Vault** consente di abilitare la rotazione automatica per la chiave selezionata. In questo modo si elimina il lavoro manuale per l'aggiornamento alla versione successiva. Tuttavia, usando questa opzione:
- L'aggiornamento della versione chiave può richiedere fino a un'ora.
- Quando viene attivata una nuova versione della chiave, anche la versione precedente deve essere disponibile (nello stato abilitato) per almeno un processo di backup successivo dopo l'attivazione dell'aggiornamento della chiave.

### <a name="using-azure-policies-for-auditing-and-enforcing-encryption-utilizing-customer-managed-keys-in-preview"></a>Uso di Criteri di Azure per il controllo e l'applicazione della crittografia tramite chiavi gestite dal cliente (in anteprima)

Backup di Azure consente di usare Criteri di Azure per controllare e applicare la crittografia, usando chiavi gestite dal cliente, dei dati nell'insieme di credenziali di Servizi di ripristino. Uso dei criteri di Azure:

- I criteri di controllo possono essere usati per il controllo degli insiemi di credenziali con crittografia tramite chiavi gestite dal cliente abilitate dopo il 04/01/2021. Per gli insiemi di credenziali con la crittografia CMK abilitata prima di questa data, è possibile che i criteri non siano applicabili o che mostrino risultati falsi negativi, ovvero che questi insiemi di credenziali potrebbero essere segnalati come non conformi, nonostante la crittografia **CMK** sia abilitata.
- Per usare i criteri di controllo per il controllo degli insiemi di credenziali con la crittografia **CMK** abilitata prima del 01/04/2021, usare il portale di Azure per aggiornare una chiave di crittografia. Ciò consente di eseguire l'aggiornamento al nuovo modello. Se non si vuole modificare la chiave di crittografia, specificare di nuovo la stessa chiave tramite l'URI della chiave o l'opzione di selezione della chiave. 

   >[!Warning]
    >Se si usa PowerShell per la gestione delle chiavi di crittografia per il backup, non è consigliabile aggiornare le chiavi dal portale.<br>Se si aggiorna la chiave dal portale, non è possibile usare PowerShell per aggiornare ulteriormente la chiave di crittografia, fino a quando non è disponibile un aggiornamento di PowerShell per supportare il nuovo modello. È tuttavia possibile continuare ad aggiornare la chiave dal portale di Azure.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>È possibile crittografare un insieme di credenziali di Backup esistente con chiavi gestite dal cliente?

No, la crittografia CMK può essere abilitata solo per i nuovi insiemi di credenziali. Pertanto, l'insieme di credenziali non deve mai avere elementi protetti. Non è infatti necessario eseguire alcun tentativo di proteggere gli elementi nell'insieme di credenziali prima di abilitare la crittografia usando chiavi gestite dal cliente.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>Si è provato a proteggere un elemento nell'insieme di credenziali, ma l'operazione non è riuscita e l'insieme di credenziali non contiene ancora elementi protetti. È possibile abilitare la crittografia CMK per questo insieme di credenziali?

No, l'insieme di credenziali non deve avere avuto tentativi di proteggere gli elementi in passato.

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>Si dispone di un insieme di credenziali che usa la crittografia CMK. È possibile ripristinare la crittografia in un secondo momento usando chiavi gestite dalla piattaforma anche se gli elementi di backup sono protetti nell'insieme di credenziali?

No, dopo aver abilitato la crittografia CMK, non è possibile ripristinare l'uso delle chiavi gestite dalla piattaforma. È possibile modificare le chiavi usate in base alle esigenze.

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>La crittografia CMK per Backup di Azure si applica anche ai Azure Site Recovery?

No, questo articolo illustra solo la crittografia dei dati di backup. Per Azure Site Recovery, è necessario impostare la proprietà separatamente come disponibile dal servizio.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>Uno dei passaggi descritti in questo articolo è stato perso e si è proteso per proteggere l'origine dati. È comunque possibile usare la crittografia CMK?

Se non si segue la procedura descritta nell'articolo e si continua a proteggere gli elementi, è possibile che l'insieme di credenziali non sia in grado di usare la crittografia usando chiavi gestite dal cliente. È quindi consigliabile fare riferimento a questo [elenco di controllo](#backing-up-to-a-vault-encrypted-with-customer-managed-keys) prima di procedere alla protezione degli elementi.

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>L'uso della crittografia CMK aggiunge al costo dei backup?

L'uso della crittografia CMK per il backup non comporta costi aggiuntivi per l'utente. È tuttavia possibile continuare a sostenere costi per l'uso del Azure Key Vault in cui è archiviata la chiave.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica delle funzionalità di sicurezza in Backup di Azure](security-overview.md)
