---
title: Uso di Importazione/Esportazione di Azure per trasferire dati in BLOB di Azure | Microsoft Docs
description: Informazioni su come creare processi di importazione ed esportazione nel portale di Azure per trasferire dati da e verso BLOB di Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/15/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 39eb6c164751ebdfa293798850a8d663fe988b82
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875684"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Usare il servizio Importazione/Esportazione di Azure per trasferire dati in Archiviazione BLOB di Azure

Questo articolo offre istruzioni dettagliate su come usare il servizio Importazione/Esportazione di Azure per importare in tutta sicurezza grandi quantità di dati nell'archiviazione BLOB di Azure. Per importare dati in BLOB di Azure, il servizio richiede la spedizione di unità disco crittografate contenenti i dati a un data center di Azure.

## <a name="prerequisites"></a>Prerequisiti

Prima di creare un processo di importazione per trasferire dati in Archiviazione BLOB di Azure, esaminare attentamente e soddisfare l'elenco seguente di prerequisiti per questo servizio.
È necessario:

* Avere una sottoscrizione di Azure attiva che possa essere usata per il servizio Importazione/Esportazione.
* Avere almeno un account di archiviazione di Azure con un contenitore di archiviazione. Vedere l'elenco di [account di archiviazione e tipi di archiviazione supportati per il servizio Importazione/Esportazione](storage-import-export-requirements.md).
  * Per informazioni sulla creazione di un nuovo account di archiviazione, vedere [Come creare un account di archiviazione](../storage/common/storage-account-create.md).
  * Per informazioni sul contenitore di archiviazione, passare a [Creare un contenitore di archiviazione](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container).
* Avere un numero adeguato di dischi dei [tipi supportati](storage-import-export-requirements.md#supported-disks).
* Predisporre un sistema Windows con una [versione del sistema operativo supportata](storage-import-export-requirements.md#supported-operating-systems).
* Abilitare BitLocker nel sistema Windows. Vedere [How to enable BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/) (Come abilitare BitLocker).
* [Scaricare la versione più recente di WAImportExport 1](https://www.microsoft.com/download/details.aspx?id=42659) nel sistema Windows. La versione più recente dello strumento include aggiornamenti della sicurezza per consentire una protezione esterna per la chiave BitLocker e la funzionalità di modalità di sblocco aggiornata.

  * Decomprimere la cartella predefinita `waimportexportv1`. Ad esempio: `C:\WaImportExportV1`.
* Avere un account FedEx o DHL. Se si vuole usare un vettore diverso da FedEx/DHL, contattare il team Azure Data Box Operations all'indirizzo `adbops@microsoft.com` .
  * L'account deve essere valido, deve avere un saldo e deve avere le funzionalità di spedizione di ritorno.
  * Generare un numero di tracciabilità per il processo di esportazione.
  * Ogni processo deve avere un numero di tracciabilità separato. Più processi con lo stesso numero di tracciabilità non sono supportati.
  * Se non si dispone di un account del vettore, passare a:
    * [Creare un account FedEx](https://www.fedex.com/en-us/create-account.html)o
    * [Creare un account DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Passaggio 1: Preparare le unità

Questo passaggio genera un file journal. Il file journal archivia le informazioni di base, come numero di serie delle unità, chiave di crittografia e dettagli relativi all'account di archiviazione.

Per preparare le unità, eseguire le operazioni seguenti.

1. Connettere le unità disco al sistema Windows tramite connettori SATA.
2. Creare un singolo volume NTFS in ogni unità. Assegnare una lettera di unità al volume. Non usare punti di montaggio.
3. Abilitare la crittografia BitLocker nel volume NTFS. Se si usa un sistema Windows Server, seguire le istruzioni fornite in [How to enable BitLocker on Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/) (Come abilitare BitLocker in Windows Server 2012 R2).
4. Copiare i dati nel volume crittografato. Usare il trascinamento della selezione o Robocopy o uno strumento di copia simile. Viene creato un file journal (con estensione *jrn)* nella stessa cartella in cui si esegue lo strumento.

   Se l'unità è bloccata ed è necessario sbloccare l'unità, i passaggi per sbloccare potrebbero essere diversi a seconda del caso d'uso.

   * Se sono stati aggiunti dati a un'unità pre-crittografata (lo strumento WAImportExport non è stato usato per la crittografia), usare la chiave BitLocker (una password numerica specificata) nella finestra popup per sbloccare l'unità.

   * Se sono stati aggiunti dati a un'unità crittografata dallo strumento WAImportExport, usare il comando seguente per sbloccare l'unità:

        `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Aprire una finestra di PowerShell o della riga di comando con privilegi amministrativi. Per passare alla directory della cartella decompressa, eseguire il comando seguente:

    `cd C:\WaImportExportV1`
6. Per ottenere la chiave BitLocker dell'unità, eseguire il comando seguente:

    `manage-bde -protectors -get <DriveLetter>:`
7. Per preparare il disco, eseguire il comando seguente. **A seconda delle dimensioni dei dati, la preparazione del disco può richiedere da diverse ore a giorni.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    Viene creato un file journal nella stessa cartella in cui è stato eseguito lo strumento. Vengono creati altri due file, un file *XML* (cartella in cui è stato eseguito lo strumento) e un file *drive-manifest.xml* (cartella in cui si trovano i dati).

    I parametri usati vengono descritti nella tabella seguente:

    |Opzione  |Descrizione  |
    |---------|---------|
    |/j:     |Nome del file journal, con estensione jrn. Viene generato un file journal per ogni unità. È consigliabile usare il numero di serie del disco come nome del file journal.         |
    |/id:     |ID sessione. Usare un numero di sessione univoco per ogni istanza del comando.      |
    |/t:     |Lettera di unità del disco da spedire. Ad esempio, l'unità `D`.         |
    |/bk:     |Chiave di BitLocker per l'unità. La sua password numerica dall'output di `manage-bde -protectors -get D:`      |
    |/srcdir:     |Lettera di unità del disco da spedire seguita da `:\`. Ad esempio: `D:\`.         |
    |/dstdir:     |Nome del contenitore di destinazione in Archiviazione di Azure.         |
    |/blobtype:     |Questa opzione specifica il tipo di BLOB in cui si desidera importare i dati. Per i BLOB in blocchi, il tipo di BLOB `BlockBlob` è e per i BLOB di pagine è `PageBlob` .         |
    |/skipwrite:     | Specifica che non è necessario copiare nuovi dati e che i dati esistenti sul disco devono essere preparati.          |
    |/enablecontentmd5:     |L'opzione , se abilitata, garantisce che MD5 sia calcolato e impostato `Content-md5` come proprietà in ogni BLOB. Usare questa opzione solo se si vuole usare il campo dopo il caricamento dei dati `Content-md5` in Azure. <br> Questa opzione non influisce sul controllo di integrità dei dati (che si verifica per impostazione predefinita). L'impostazione aumenta il tempo necessario per caricare i dati nel cloud.          |
8. Ripetere il passaggio precedente per ogni disco che deve essere spedito. Viene creato un file journal con il nome specificato per ogni esecuzione della riga di comando.

    > [!IMPORTANT]
    > * Insieme al file journal, viene creato anche un file `<Journal file name>_DriveInfo_<Drive serial ID>.xml` nella stessa cartella in cui si trova lo strumento. Il file xml viene usato al posto del file journal quando si crea un processo se il file journal è troppo grande.
   > * La dimensione massima consentita dal portale per il file journal è 2 MB. Se il file journal supera tale limite, viene restituito un errore.

## <a name="step-2-create-an-import-job"></a>Passaggio 2: Creare un processo di importazione

### <a name="portal"></a>[Portale](#tab/azure-portal)

Per creare un processo di importazione nel portale di Azure, eseguire le operazioni seguenti.

1. Accedere all'indirizzo https://portal.azure.com/.
2. Cercare i **processi di importazione/esportazione**.

   ![Eseguire ricerche nei processi di importazione/esportazione](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Selezionare **+ Nuovo**.

   ![Selezionare Nuovo per creare un nuovo ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. In **Nozioni di base**:

   1. Selezionare una sottoscrizione.
   1. Selezionare un gruppo di risorse oppure **selezionare Crea nuovo** e crearne uno nuovo.
   1. Immettere un nome descrittivo per il processo di importazione. Usare il nome per tenere traccia dello stato dei processi.
      * Il nome può contenere solo lettere minuscole, numeri e segni meno.
      * Il nome deve iniziare con una lettera e non può contenere spazi.

   1. Selezionare **Importa in Azure**.

    ![Creare il processo di importazione - Passaggio 1](./media/storage-import-export-data-to-blobs/import-to-blob-3.png)

    Selezionare **Avanti: Dettagli processo per >** continuare.

5. In **Dettagli processo**:

   1. Caricare i file journal creati in [Passaggio 1: Preparare le unità](#step-1-prepare-the-drives). Se è stato usato `waimportexport.exe version1`, caricare un file per ogni unità preparata. Se le dimensioni del file journal superano 2 MB, è possibile usare il file `<Journal file name>_DriveInfo_<Drive serial ID>.xml` creato insieme al file journal.
   1. Selezionare l'area di Azure di destinazione per l'ordine.
   1. Selezionare l'account di archiviazione per l'importazione.
      
      La località di consegna viene popolata automaticamente in base all'area dell'account di archiviazione selezionato.
   1. Se non si vuole salvare un log dettagliato, deselezionare l'opzione Save verbose log (Salva log dettagliato) nel contenitore **BLOB "waimportexport".**

   ![Creare il processo di importazione - Passaggio 2](./media/storage-import-export-data-to-blobs/import-to-blob-4.png).

   Selezionare **Avanti: Spedizione >** per continuare.

6. In **Spedizione**:

   1. Selezionare il vettore nell'elenco a discesa. Se si vuole usare un vettore diverso da FedEx/DHL, scegliere un'opzione esistente nell'elenco a discesa. Contattare Azure Data Box operations `adbops@microsoft.com`  all'indirizzo con le informazioni relative al vettore che si prevede di usare.
   1. Immettere un numero di account di vettore valido creato con il vettore. Microsoft usa questo account per restituire le unità al cliente al termine del processo di importazione. In assenza di un numero di account, creare un account di vettore [FedEx](https://www.fedex.com/us/oadr/) o [DHL](https://www.dhl.com/).
   1.  Specificare un nome di contatto completo e valido, insieme a numero di telefono, indirizzo di posta elettronica, indirizzo, città, CAP, stato/provincia e paese/area.

       > [!TIP]
       > Anziché specificare un indirizzo di posta elettronica per un singolo utente, fornire un indirizzo di posta elettronica di gruppo. Ciò garantisce la ricezione di notifiche anche se non c'è più un amministratore.

   ![Creare il processo di importazione - Passaggio 3](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   Selezionare **Rivedi e crea** per continuare.

7. Nel riepilogo dell'ordine:

   1. Esaminare le **Condizioni** e quindi selezionare "Accetto che tutte le informazioni fornite siano corrette e accettino le condizioni". Viene quindi eseguita la convalida.
   1. Esaminare le informazioni sul processo fornite nel riepilogo. Prendere nota del nome del processo e dell'indirizzo di spedizione del data center di Azure per rispedire i dischi ad Azure. Queste informazioni verranno usate successivamente sull'etichetta indirizzo.
   1. Selezionare **Crea**.

     ![Creare il processo di importazione - Passaggio 4](./media/storage-import-export-data-to-blobs/import-to-blob-6.png)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare la procedura seguente per creare un processo di importazione nell'interfaccia della riga di comando di Azure.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Creare un processo

1. Usare il [comando az extension add](/cli/azure/extension#az_extension_add) per aggiungere l'estensione [az import-export:](/cli/azure/import-export)

    ```azurecli
    az extension add --name import-export
    ```

1. È possibile usare un gruppo di risorse esistente o crearne uno. Per creare un gruppo di risorse, eseguire il comando [az group create](/cli/azure/group#az_group_create):

    ```azurecli
    az group create --name myierg --location "West US"
    ```

1. È possibile usare un account di archiviazione esistente o crearne uno. Per creare un account di archiviazione, eseguire il [comando az storage account create:](/cli/azure/storage/account#az_storage_account_create)

    ```azurecli
    az storage account create --resource-group myierg --name myssdocsstorage --https-only
    ```

1. Per ottenere un elenco dei percorsi in cui è possibile spedire i dischi, usare il [comando az import-export location list:](/cli/azure/import-export/location#az_import_export_location_list)

    ```azurecli
    az import-export location list
    ```

1. Usare il [comando az import-export location show](/cli/azure/import-export/location#az_import_export_location_show) per ottenere i percorsi per l'area:

    ```azurecli
    az import-export location show --location "West US"
    ```

1. Eseguire il comando [az import-export create seguente](/cli/azure/import-export#az_import_export_create) per creare un processo di importazione:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name MyIEjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --drive-list bit-locker-key=439675-460165-128202-905124-487224-524332-851649-442187 \
            drive-header-hash= drive-id=AZ31BGB1 manifest-file=\\DriveManifest.xml \
            manifest-hash=69512026C1E8D4401816A2E5B8D7420D \
        --type Import \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --return-shipping carrier-name=FedEx carrier-account-number=123456789 \
        --storage-account myssdocsstorage
    ```

   > [!TIP]
   > Anziché specificare un indirizzo di posta elettronica per un singolo utente, fornire un indirizzo di posta elettronica di gruppo. Ciò garantisce la ricezione di notifiche anche se non c'è più un amministratore.

1. Usare il [comando az import-export list](/cli/azure/import-export#az_import_export_list) per visualizzare tutti i processi per il gruppo di risorse myierg:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Per aggiornare il processo o annullarlo, eseguire il [comando az import-export update:](/cli/azure/import-export#az_import_export_update)

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Usare la procedura seguente per creare un processo di importazione in Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Mentre il **modulo Az.ImportExport** di PowerShell è in anteprima, è necessario installarlo separatamente usando il `Install-Module` cmdlet . Quando il modulo di PowerShell diventerà disponibile a livello generale, entrerà a far parte delle future versioni del modulo Az di PowerShell e sarà disponibile per impostazione predefinita all'interno di Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Creare un processo

1. È possibile usare un gruppo di risorse esistente o crearne uno. Per creare un gruppo di risorse, eseguire il cmdlet [New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myierg -Location westus
   ```

1. È possibile usare un account di archiviazione esistente o crearne uno. Per creare un account di archiviazione, eseguire il cmdlet [New-AzStorageAccount:](/powershell/module/az.storage/new-azstorageaccount)

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName myierg -AccountName myssdocsstorage -SkuName Standard_RAGRS -Location westus -EnableHttpsTrafficOnly $true
   ```

1. Per ottenere un elenco dei percorsi in cui è possibile spedire i dischi, usare il cmdlet [Get-AzImportExportLocation:](/powershell/module/az.importexport/get-azimportexportlocation)

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Usare il `Get-AzImportExportLocation` cmdlet con il parametro per ottenere i percorsi per `Name` l'area:

   ```azurepowershell-interactive
   Get-AzImportExportLocation -Name westus
   ```

1. Eseguire [l'esempio New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) seguente per creare un processo di importazione:

   ```azurepowershell-interactive
   $driveList = @(@{
     DriveId = '9CA995BA'
     BitLockerKey = '439675-460165-128202-905124-487224-524332-851649-442187'
     ManifestFile = '\\DriveManifest.xml'
     ManifestHash = '69512026C1E8D4401816A2E5B8D7420D'
     DriveHeaderHash = 'AZ31BGB1'
   })

   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'MyIEjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      DriveList = $driveList
      JobType = 'Import'
      LogLevel = 'Verbose'
      ShippingInformationRecipientName = 'Microsoft Azure Import/Export Service'
      ShippingInformationStreetAddress1 = '3020 Coronado'
      ShippingInformationCity = 'Santa Clara'
      ShippingInformationStateOrProvince = 'CA'
      ShippingInformationPostalCode = '98054'
      ShippingInformationCountryOrRegion = 'USA'
      ShippingInformationPhone = '4083527600'
      ReturnAddressRecipientName = 'Gus Poland'
      ReturnAddressStreetAddress1 = '1020 Enterprise way'
      ReturnAddressCity = 'Sunnyvale'
      ReturnAddressStateOrProvince = 'CA'
      ReturnAddressPostalCode = '94089'
      ReturnAddressCountryOrRegion = 'USA'
      ReturnAddressPhone = '4085555555'
      ReturnAddressEmail = 'gus@contoso.com'
      ReturnShippingCarrierName = 'FedEx'
      ReturnShippingCarrierAccountNumber = '123456789'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

   > [!TIP]
   > Anziché specificare un indirizzo di posta elettronica per un singolo utente, fornire un indirizzo di posta elettronica di gruppo. Ciò garantisce la ricezione di notifiche anche se non c'è più un amministratore.

1. Usare il cmdlet [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) per visualizzare tutti i processi per il gruppo di risorse myierg:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Per aggiornare il processo o annullarlo, eseguire il cmdlet [Update-AzImportExport:](/powershell/module/az.importexport/update-azimportexport)

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-3-optional-configure-customer-managed-key"></a>Passaggio 3 (facoltativo): Configurare la chiave gestita dal cliente

Ignorare questo passaggio e andare al passaggio successivo se si vuole usare la chiave gestita da Microsoft per proteggere le chiavi BitLocker per le unità. Per configurare la propria chiave per proteggere la chiave BitLocker, seguire le istruzioni in Configurare le chiavi gestite dal cliente con [Azure Key Vault per Importazione/Esportazione](storage-import-export-encryption-key-portal.md)di Azure nel portale di Azure .

## <a name="step-4-ship-the-drives"></a>Passaggio 4: Spedire le unità

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>Passaggio 5: Aggiornare il processo con le informazioni di rilevamento

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>Passaggio 6: Verificare il caricamento dei dati in Azure

Tracciare il processo fino al completamento. Una volta completato il processo, verificare che i dati siano caricati in Azure. Eliminare i dati locali solo dopo aver verificato che il caricamento ha avuto esito positivo.

## <a name="next-steps"></a>Passaggi successivi

* [Visualizzare lo stato dei processi e delle unità](storage-import-export-view-drive-status.md)
* [Esaminare i requisiti di importazione/esportazione](storage-import-export-requirements.md)
