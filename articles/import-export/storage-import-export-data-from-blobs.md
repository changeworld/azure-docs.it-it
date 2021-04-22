---
title: Uso di Importazione/Esportazione di Azure per esportare dati da BLOB di Azure | Microsoft Docs
description: Informazioni su come creare processi di esportazione nel portale di Azure per trasferire dati da BLOB di Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 2d4885f23e775f84a412d176568d992ebe01166b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875702"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Usare il servizio Importazione/Esportazione di Azure per esportare dati da Archiviazione BLOB di Azure

Questo articolo offre istruzioni dettagliate su come usare il servizio Importazione/Esportazione di Azure per esportare in tutta sicurezza grandi quantità di dati da Archiviazione BLOB di Azure. Il servizio richiede la spedizione di unità vuote al data center di Azure. Il servizio esporta i dati dall'account di archiviazione alle unità e quindi rispedisce le unità.

## <a name="prerequisites"></a>Prerequisiti

Prima di creare un processo di esportazione per trasferire dati da Archiviazione BLOB di Azure, esaminare attentamente e soddisfare l'elenco seguente di prerequisiti per questo servizio.
È necessario:

- Avere una sottoscrizione di Azure attiva che possa essere usata per il servizio Importazione/Esportazione.
- Avere almeno un account di archiviazione di Azure. Vedere l'elenco di [account di archiviazione e tipi di archiviazione supportati per il servizio Importazione/Esportazione](storage-import-export-requirements.md). Per informazioni sulla creazione di un nuovo account di archiviazione, vedere [Come creare un account di archiviazione](../storage/common/storage-account-create.md).
- Avere un numero adeguato di dischi dei [tipi supportati](storage-import-export-requirements.md#supported-disks).
- Avere un account FedEx o DHL. Se si vuole usare un vettore diverso da FedEx/DHL, contattare il team Azure Data Box Operations all'indirizzo `adbops@microsoft.com` .
  - L'account deve essere valido, deve avere un saldo e deve avere le funzionalità di spedizione di ritorno.
  - Generare un numero di tracciabilità per il processo di esportazione.
  - Ogni processo deve avere un numero di tracciabilità separato. Più processi con lo stesso numero di tracciabilità non sono supportati.
  - Se non si dispone di un account del vettore, passare a:
    - [Creare un account FedEx](https://www.fedex.com/en-us/create-account.html)o
    - [Creare un account DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Passaggio 1: Creare un processo di esportazione

### <a name="portal"></a>[Portale](#tab/azure-portal)

Per creare un processo di esportazione nel portale di Azure, eseguire le operazioni seguenti.

1. Accedere all'indirizzo <https://portal.azure.com/>.
2. Cercare i **processi di importazione/esportazione**.

    ![Cercare processi di importazione/esportazione](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Selezionare **+ Nuovo**.

    ![Selezionare + Nuovo per creare un nuovo ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. In **Nozioni di base**:

   1. Selezionare una sottoscrizione.
   1. Selezionare un gruppo di risorse oppure **selezionare Crea nuovo** e crearne uno nuovo.
   1. Immettere un nome descrittivo per il processo di importazione. Usare il nome per tenere traccia dello stato dei processi.
       * Il nome può contenere solo lettere minuscole, numeri e segni meno.
       * Il nome deve iniziare con una lettera e non può contenere spazi.

   1. Selezionare **Esporta da Azure**.

    ![Opzioni di base per un ordine di esportazione](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

    Selezionare **Avanti: Dettagli processo per >** continuare.

5. In **Dettagli processo**:

   1. Selezionare l'area di Azure in cui si trova attualmente i dati.
   1. Selezionare l'account di archiviazione da cui esportare i dati. Usare un account di archiviazione vicino alla propria posizione.

      La località di consegna viene immessa automaticamente in base all'area dell'account di archiviazione selezionato.

   1. Specificare i dati BLOB da esportare dall'account di archiviazione all'unità o alle unità vuote. Scegliere uno dei tre metodi seguenti.

      - Scegliere **Esporta tutti** per i dati BLOB nell'account di archiviazione.

        ![Esporta tutti](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

      - Scegliere **Contenitori e BLOB selezionati** e specificare i contenitori e i BLOB da esportare. È possibile usare più metodi di selezione. Selezionando **un'opzione** Aggiungi si apre un pannello a destra in cui è possibile aggiungere le stringhe di selezione.

        |Opzione|Descrizione|
        |------|-----------|      
        |**Aggiungere contenitori**|Esportare tutti i BLOB in un contenitore.<br>Selezionare **Aggiungi contenitori** e immettere il nome di ogni contenitore.|
        |**Aggiungere BLOB**|Specificare i singoli BLOB da esportare.<br>Selezionare **Aggiungi BLOB**. Specificare quindi il percorso relativo del BLOB, a partire dal nome del contenitore. Utilizzare *$root* per specificare il contenitore radice.<br>I percorsi BLOB devono essere specificati in un formato valido per evitare errori durante l'elaborazione, come mostrato in questo screenshot. Per altre informazioni, vedere [Esempi di percorsi BLOB validi](#examples-of-valid-blob-paths).|
        |**Aggiungere prefissi**|Usare un prefisso per selezionare un set di contenitori con nome simile o BLOB con nome simile in un contenitore. Il prefisso può essere il prefisso del nome del contenitore, il nome completo del contenitore o un nome di contenitore completo seguito dal prefisso del nome del BLOB. |

        ![Esportare contenitori e BLOB selezionati](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - Scegliere Esporta da file di elenco BLOB **(formato XML)** e selezionare un file XML contenente un elenco di percorsi e prefissi per i BLOB da esportare dall'account di archiviazione. È necessario costruire il file XML e archiviarlo in un contenitore per l'account di archiviazione. Il file non può essere vuoto.

      > [!IMPORTANT]
      > Se si usa un file XML per selezionare i BLOB da esportare, assicurarsi che il file XML contenga percorsi e/o prefissi validi. Se il file non è valido o nessun dato corrisponde ai percorsi specificati, l'ordine termina con dati parziali o nessun dato esportato.

       Per informazioni su come aggiungere un file XML a un contenitore, vedere Esportare un ordine [usando un file XML.](../databox/data-box-deploy-export-ordered.md#export-order-using-xml-file)

      ![Esportare dal file elenco di BLOB](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > Se durante la copia dei dati è in uso un BLOB da esportare, il servizio Importazione/Esportazione di Azure crea uno snapshot del BLOB e lo copia.

   Selezionare **Avanti: Spedizione >** per continuare.

6. In **Spedizione**:

    - Selezionare il vettore nell'elenco a discesa. Se si vuole usare un vettore diverso da FedEx/DHL, scegliere un'opzione esistente nell'elenco a discesa. Contattare Azure Data Box operations team `adbops@microsoft.com`  all'indirizzo con le informazioni relative al vettore che si prevede di usare.
    - Immettere un numero di account di vettore valido creato con il vettore. Microsoft usa questo account per spedire le unità all'utente al termine del processo di esportazione.
    - Specificare un nome di contatto completo e valido, telefono, indirizzo di posta elettronica, indirizzo, città, zip, stato/provincia e paese/area geografica.

        > [!TIP]
        > Anziché specificare un indirizzo di posta elettronica per un singolo utente, fornire un indirizzo di posta elettronica di gruppo. Ciò garantisce la ricezione di notifiche anche se non c'è più un amministratore.

    Selezionare **Rivedi e crea** per continuare.

7. In **Rivedi e crea:**

   1. Esaminare i dettagli del processo.
   1. Annotare il nome del processo e le informazioni sul mittente della spedizione per spedire i dischi ad Azure.

      > [!NOTE]
      > Inviare sempre i dischi al data center indicato nel portale di Azure. Se i dischi vengono spediti al data center errato, non verrà elaborato il processo.

   1. Esaminare le Condizioni **per l'ordine** per l'eliminazione dei dati di origine e sulla privacy. Se si accettano le condizioni, selezionare la casella di controllo sotto le condizioni. Viene avviata la convalida dell'ordine.

   ![Esaminare e creare l'ordine di esportazione](./media/storage-import-export-data-from-blobs/export-from-blob-6-a.png)

 1. Al termine della convalida selezionare **Crea**.

<!--Replaced text: Steps 4 - end of "Create an export job." Wizard design changes required both screen and text updates.

4. In **Basics**:

    - Select **Export from Azure**.
    - Enter a descriptive name for the export job. Use the name you choose to track the progress of your jobs.
        - The name may contain only lowercase letters, numbers, hyphens, and underscores.
        - The name must start with a letter, and may not contain spaces.
    - Select a subscription.
    - Enter or select a resource group.

        ![Basics](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

5. In **Job details**:

    - Select the storage account where the data to be exported resides. Use a storage account close to where you are located.
    - The dropoff location is automatically populated based on the region of the storage account selected.
    - Specify the blob data you wish to export from your storage account to your blank drive or drives.
    - Choose to **Export all** blob data in the storage account.

         ![Export all](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

    - You can specify which containers and blobs to export.
        - **To specify a blob to export**: Use the **Equal To** selector. Specify the relative path to the blob, beginning with the container name. Use *$root* to specify the root container.
        - **To specify all blobs starting with a prefix**: Use the **Starts With** selector. Specify the prefix, beginning with a forward slash '/'. The prefix may be the prefix of the container name, the complete container name, or the complete container name followed by the prefix of the blob name. You must provide the blob paths in valid format to avoid errors during processing, as shown in this screenshot. For more information, see [Examples of valid blob paths](#examples-of-valid-blob-paths).

           ![Export selected containers and blobs](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - You can export from  the blob list file.

        ![Export from blob list file](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > If the blob to be exported is in use during data copy, Azure Import/Export service takes a snapshot of the blob and copies the snapshot.

6. In **Return shipping info**:

    - Select the carrier from the dropdown list. If you want to use a carrier other than FedEx/DHL, choose an existing option from the dropdown. Contact Azure Data Box Operations team at `adbops@microsoft.com`  with the information regarding the carrier you plan to use.
    - Enter a valid carrier account number that you have created with that carrier. Microsoft uses this account to ship the drives back to you once your export job is complete.
    - Provide a complete and valid contact name, phone, email, street address, city, zip, state/province, and country/region.

        > [!TIP]
        > Instead of specifying an email address for a single user, provide a group email. This ensures that you receive notifications even if an admin leaves.

7. In **Summary**:

    - Review the details of the job.
    - Make a note of the job name and provided Azure datacenter shipping address for shipping disks to Azure.

        > [!NOTE]
        > Always send the disks to the datacenter noted in the Azure portal. If the disks are shipped to the wrong datacenter, the job will not be processed.

    - Click **OK** to complete export job creation.
-->

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare la procedura seguente per creare un processo di esportazione nel portale di Azure.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Creare un processo

1. Usare il [comando az extension add](/cli/azure/extension#az_extension_add) per aggiungere l'estensione [az import-export:](/cli/azure/import-export)

    ```azurecli
    az extension add --name import-export
    ```

1. Per ottenere un elenco dei percorsi da cui è possibile ricevere dischi, usare [il comando az import-export location list:](/cli/azure/import-export/location#az_import_export_location_list)

    ```azurecli
    az import-export location list
    ```

1. Eseguire il comando [az import-export create seguente](/cli/azure/import-export#az_import_export_create) per creare un processo di esportazione che usa l'account di archiviazione esistente:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name Myexportjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --export blob-path=/ \
        --type Export \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --storage-account myssdocsstorage
    ```

    > [!TIP]
    > Anziché specificare un indirizzo di posta elettronica per un singolo utente, fornire un indirizzo di posta elettronica di gruppo. Ciò garantisce la ricezione di notifiche anche se non c'è più un amministratore.

   Questo processo esporta tutti i BLOB nell'account di archiviazione. È possibile specificare un BLOB per l'esportazione sostituendo questo valore per **--export**:

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   Questo valore del parametro esporta il BLOB *logo.bmp* nel contenitore radice.

   È anche possibile selezionare tutti i BLOB in un contenitore usando un prefisso. Sostituire questo valore per **--export**:

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   Per altre informazioni, vedere [Esempi di percorsi BLOB validi](#examples-of-valid-blob-paths).

   > [!NOTE]
   > Se il BLOB da esportare è in uso durante la copia dei dati, il servizio Importazione/Esportazione di Azure acquisisce uno snapshot del BLOB e copia lo snapshot.

1. Usare il [comando az import-export list](/cli/azure/import-export#az_import_export_list) per visualizzare tutti i processi per il gruppo di risorse myierg:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Per aggiornare il processo o annullarlo, eseguire il [comando az import-export update:](/cli/azure/import-export#az_import_export_update)

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Usare la procedura seguente per creare un processo di esportazione in Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Mentre il **modulo Az.ImportExport** di PowerShell è disponibile in anteprima, è necessario installarlo separatamente usando il `Install-Module` cmdlet . Quando il modulo di PowerShell diventerà disponibile a livello generale, entrerà a far parte delle future versioni del modulo Az di PowerShell e sarà disponibile per impostazione predefinita all'interno di Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Creare un processo

1. Per ottenere un elenco dei percorsi da cui è possibile ricevere dischi, usare il cmdlet [Get-AzImportExportLocation:](/powershell/module/az.importexport/get-azimportexportlocation)

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Eseguire [l'esempio New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) seguente per creare un processo di esportazione che usa l'account di archiviazione esistente:

   ```azurepowershell-interactive
   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'Myexportjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      ExportBlobListblobPath = '\'
      JobType = 'Export'
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
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

    > [!TIP]
    > Anziché specificare un indirizzo di posta elettronica per un singolo utente, fornire un indirizzo di posta elettronica di gruppo. Ciò garantisce la ricezione di notifiche anche se non c'è più un amministratore.

   Questo processo esporta tutti i BLOB nell'account di archiviazione. È possibile specificare un BLOB per l'esportazione sostituendo questo valore per **-ExportBlobListblobPath**:

   ```azurepowershell-interactive
   -ExportBlobListblobPath $root\logo.bmp
   ```

   Questo valore del parametro esporta il BLOB denominato *logo.bmp* nel contenitore radice.

   È anche possibile selezionare tutti i BLOB in un contenitore usando un prefisso. Sostituire questo valore per **-ExportBlobListblobPath**:

   ```azurepowershell-interactive
   -ExportBlobListblobPath '/myiecontainer'
   ```

   Per altre informazioni, vedere [Esempi di percorsi BLOB validi](#examples-of-valid-blob-paths).

   > [!NOTE]
   > Se il BLOB da esportare è in uso durante la copia dei dati, il servizio Importazione/Esportazione di Azure acquisisce uno snapshot del BLOB e copia lo snapshot.

1. Usare il cmdlet [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) per visualizzare tutti i processi per il gruppo di risorse myierg:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Per aggiornare il processo o annullarlo, eseguire il cmdlet [Update-AzImportExport:](/powershell/module/az.importexport/update-azimportexport)

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>Passaggio 2: Spedire le unità

In caso di dubbi sul numero di unità necessarie, passare a [Controllare il numero di unità](#check-the-number-of-drives). Se si conosce il numero di unità, procedere alla spedizione delle unità.

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Passaggio 3: Aggiornare il processo con informazioni di tracciabilità della spedizione

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>Passaggio 4: Ricevere i dischi

Quando il dashboard segnala che il processo è completo, i dischi vengono spediti all'utente e il numero di tracciabilità per la spedizione è disponibile nel portale.

1. Dopo aver ricevuto le unità con i dati esportati, è necessario ottenere le chiavi BitLocker per sbloccare le unità. Passare al processo di esportazione nel portale di Azure. Fare clic sulla scheda **Importazione/Esportazione**.
2. Selezionare e fare clic sul processo di esportazione nell'elenco. Passare a **Crittografia** e copiare le chiavi.

   ![Visualizzare le chiavi BitLocker per il processo di esportazione](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. Usare le chiavi BitLocker per sbloccare i dischi.

L'esportazione è stata completata.

## <a name="step-5-unlock-the-disks"></a>Passaggio 5: Sbloccare i dischi

Usare il comando seguente per sbloccare l'unità:

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

Di seguito è riportato un esempio dell'input di esempio.

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

A questo momento, è possibile eliminare il processo o lasciarlo. I processi vengono eliminati automaticamente dopo 90 giorni.

## <a name="check-the-number-of-drives"></a>Controllare il numero di unità

Questo *passaggio* facoltativo consente di determinare il numero di unità necessarie per il processo di esportazione. Eseguire questo passaggio in un sistema Windows con una [versione supportata del sistema operativo](storage-import-export-requirements.md#supported-operating-systems).

1. [Scaricare WAImportExport versione 1](https://www.microsoft.com/download/details.aspx?id=42659) nel sistema Windows.
2. Decomprimere la cartella predefinita `waimportexportv1`. Ad esempio: `C:\WaImportExportV1`.
3. Aprire una finestra di PowerShell o della riga di comando con privilegi amministrativi. Per passare alla directory della cartella decompressa, eseguire il comando seguente:

   `cd C:\WaImportExportV1`

4. Per controllare il numero di dischi necessari per i BLOB selezionati, eseguire il comando seguente:

   `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    I parametri vengono descritti nella tabella seguente:

    |Parametro della riga di comando|Descrizione|
    |--------------------------|-----------------|
    |**/logdir:**|facoltativo. Directory dei log. in cui vengono scritti file di log dettagliati. Se non è specificato, come directory dei log viene usata la directory corrente.|
    |**/sn:**|Obbligatorio. Il nome dell'account di archiviazione per il processo di esportazione.|
    |**/sk:**|Obbligatorio solo se non è specificata una firma di accesso condiviso del contenitore. Chiave dell'account per l'account di archiviazione per il processo di esportazione.|
    |**/csas:**|Obbligatorio solo se non è specificata una chiave dell'account di archiviazione. Firma di accesso condiviso del contenitore per l'elenco dei BLOB da esportare nel processo di esportazione.|
    |**/ExportBlobListFile:**|Obbligatorio. Percorso del file XML contenente l'elenco dei percorsi o dei prefissi dei percorsi BLOB per i BLOB da esportare. Formato di file usato nell'elemento `BlobListBlobPath` nell'operazione [Put Job](/rest/api/storageimportexport/jobs) dell'API REST del servizio Importazione/Esportazione.|
    |**/DriveSize:**|Obbligatorio. Dimensioni delle unità da usare per un processo di esportazione, ad *esempio,* 500 GB, 1,5 TB.|

    Vedere [Esempio di comando PreviewExport](#example-of-previewexport-command).

5. Controllare che sia possibile eseguire operazioni di lettura/scrittura nelle unità che verranno spedite per il processo di esportazione.

### <a name="example-of-previewexport-command"></a>Esempio di comando PreviewExport

L'esempio seguente illustra il comando `PreviewExport`:

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

Il file dell'elenco del BLOB di esportazione può contenere nomi e prefissi BLOB, come illustrato di seguito:

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

Lo strumento Importazione/Esportazione di Azure elenca tutti i BLOB da esportare e calcola come comprimerli in unità della dimensione specificata, tenendo conto dell'eventuale sovraccarico necessario, quindi stima il numero di unità richieste per contenere i BLOB e le informazioni sull'uso delle unità.

Di seguito è riportato un esempio dell'output, senza log informativi:

```powershell
Number of unique blob paths/prefixes:   3
Number of duplicate blob paths/prefixes:        0
Number of nonexistent blob paths/prefixes:      1

Drive size:     500.00 GB
Number of blobs that can be exported:   6
Number of blobs that cannot be exported:        2
Number of drives needed:        3
        Drive #1:       blobs = 1, occupied space = 454.74 GB
        Drive #2:       blobs = 3, occupied space = 441.37 GB
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="examples-of-valid-blob-paths"></a>Esempi di percorsi BLOB validi

La tabella seguente mostra alcuni esempi di percorsi BLOB validi:

   | Selettore | Percorso BLOB | Descrizione |
   | --- | --- | --- |
   | Starts With |/ |Esporta tutti i BLOB nell'account di archiviazione |
   | Starts With |/$root/ |Esporta tutti i BLOB nel contenitore radice |
   | Starts With |/book |Esporta tutti i BLOB in qualsiasi contenitore che inizia con il prefisso **book** |
   | Starts With |/music/ |Esporta tutti i BLOB nel contenitore **music** |
   | Starts With |/music/love |Esporta nel contenitore **music** tutti i BLOB che iniziano con il prefisso **love** |
   | Equal To |$root/logo.bmp |Esporta il BLOB **logo.bmp** nel contenitore radice |
   | Equal To |videos/story.mp4 |Esporta il BLOB **story.mp4** nel contenitore **video** |

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare lo stato dei processi e delle unità](storage-import-export-view-drive-status.md)
- [Esaminare i requisiti di importazione/esportazione](storage-import-export-requirements.md)
