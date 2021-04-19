---
title: Creare una condivisione file di Azure
titleSuffix: Azure Files
description: Come creare una condivisione file di Azure usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 91f42ae671cb1696e5b088bafde8362cf19ce856
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717962"
---
# <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure
Per creare una condivisione file di Azure, è necessario rispondere a tre domande su come usarla:

- **Quali sono i requisiti di prestazioni per la condivisione file di Azure?**  
    File di Azure offre condivisioni file standard ospitate su hardware basato su disco rigido e condivisioni file Premium, ospitate su hardware basato su disco SSD.

- **Quali sono i requisiti di ridondanza per la condivisione file di Azure?**  
    Le condivisioni file Standard offrono archiviazione con ridondanza locale (LRS), con ridondanza della zona (ZRS), con ridondanza geografica (GRS) o con ridondanza geografica della zona (GZRS), ma la funzionalità di condivisione file di grandi dimensioni è supportata solo nelle condivisioni file con ridondanza locale e con ridondanza della zona. Le condivisioni file Premium non supportano alcuna forma di ridondanza geografica.

    Le condivisioni file Premium sono disponibili con ridondanza locale e ridondanza della zona in un subset di aree. Per scoprire se le condivisioni file Premium sono attualmente disponibili nella propria area, vedere la pagina dei prodotti [disponibili in](https://azure.microsoft.com/global-infrastructure/services/?products=storage) base all'area per Azure. Per informazioni sulle aree che supportano l'archiviazione con ridondanza della Archiviazione di Azure archiviazione con [ridondanza.](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

- **Quali dimensioni sono necessarie per la condivisione file?**  
    Negli account di archiviazione con ridondanza locale e della zona, le condivisioni file di Azure possono estendersi fino a 100 TiB, ma negli account di archiviazione con ridondanza geografica e area geografica le condivisioni file di Azure possono estendersi solo fino a 5 TiB. 

Per altre informazioni su queste tre opzioni, vedere [Planning for an File di Azure deployment](storage-files-planning.md).

## <a name="prerequisites"></a>Prerequisiti
- In questo articolo si presuppone che sia già stata creata una sottoscrizione di Azure. Se non si ha già una sottoscrizione, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Se si intende usare Azure PowerShell, [installare l'ultima versione](/powershell/azure/install-az-ps).
- Se si intende usare l'interfaccia della riga di comando di Azure, [installare l'ultima versione](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
Le condivisioni file di Azure vengono distribuite in *account di archiviazione*, ovvero oggetti di primo livello che rappresentano un pool di archiviazione condiviso. Questo pool di archiviazione può essere usato per distribuire più condivisioni file. 

Azure supporta più tipi di account di archiviazione per diversi scenari di archiviazione che i clienti possono avere, ma esistono due tipi principali di account di archiviazione per File di Azure. Il tipo di account di archiviazione da creare varia a seconda che si voglia creare una condivisione file standard o premium: 

- **Account di archiviazione per utilizzo generico v2**: gli account di archiviazione per utilizzo generico v2 consentono di distribuire condivisioni file di Azure in hardware standard o basato su disco rigido (HDD). Oltre a archiviare le condivisioni file di Azure, gli account di archiviazione per utilizzo generico v2 possono archiviare altre risorse di archiviazione, ad esempio contenitori BLOB, code o tabelle. Le condivisioni file possono essere distribuite nei livelli ottimizzati per le transazioni (impostazione predefinita), ad accesso caldo o ad accesso cool.

- **Account di archiviazione FileStorage**: gli account di archiviazione FileStorage consentono di distribuire condivisioni file di Azure in hardware Premium o basato su unità SSD. Gli account FileStorage possono essere usati solo per archiviare le condivisioni file di Azure. Non è infatti possibile distribuire altre risorse di archiviazione (contenitori BLOB, code, tabelle e così via) in un account FileStorage.

# <a name="portal"></a>[Portale](#tab/azure-portal)
Per creare un account di archiviazione tramite il portale di Azure, selezionare **+ Crea** una risorsa dal dashboard. Nella finestra di Azure Marketplace di ricerca cercare **l'account di archiviazione** e selezionare il risultato della ricerca risultante. Verrà visualizzata una pagina di panoramica per gli account di archiviazione. Selezionare **Crea per** procedere con la creazione guidata dell'account di archiviazione.

![Screenshot dell'opzione di creazione rapida dell'account di archiviazione in un browser](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="basics"></a>Nozioni di base
La prima sezione da completare per creare un account di archiviazione è contrassegnata con **l'etichetta Basics**. Contiene tutti i campi obbligatori per creare un account di archiviazione. Per creare un account di archiviazione  GPv2, assicurarsi che  il pulsante di opzione Prestazioni sia impostato su *Standard* e che l'elenco a discesa Tipo di account sia selezionato su *ArchiviazioneV2 (utilizzo generico v2).*

:::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-standard.png" alt-text="Screenshot del pulsante di opzione prestazioni con standard selezionato e tipo di account con archiviazionev2 selezionata.":::

Per creare un account di  archiviazione FileStorage, verificare che il pulsante di opzione Prestazioni sia impostato su *Premium* e che nell'elenco a discesa **Tipo di account Premium** sia selezionato **Condivisione** file.

:::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-premium.png" alt-text="Screenshot del pulsante di opzione prestazioni con l'opzione Premium selezionata e il tipo di account con filestorage selezionato.":::

Gli altri campi di base sono indipendenti dalla scelta dell'account di archiviazione:
- **Nome account di archiviazione:** nome della risorsa dell'account di archiviazione da creare. Questo nome deve essere univoco a livello globale, ma in caso contrario può essere qualsiasi nome desiderato. Il nome dell'account di archiviazione verrà usato come nome del server quando si monta una condivisione file di Azure tramite SMB.
- **Località:** area in cui distribuire l'account di archiviazione. Può trattarsi dell'area associata al gruppo di risorse o di qualsiasi altra area disponibile.
- **Replica:** anche se si tratta di una replica con etichetta, questo campo indica **effettivamente la ridondanza**. questo è il livello di ridondanza desiderato: ridondanza locale (LRS), ridondanza della zona (ZRS), ridondanza geografica (GRS) e ridondanza geografica della zona (GZRS). Questo elenco a discesa contiene anche ridondanza geografica e accesso in lettura (RA-GRS) e ridondanza della zona geografica e accesso in lettura (RA-GZRS), che non si applicano alle condivisioni file di Azure. Qualsiasi condivisione file creata in un account di archiviazione con questi elementi selezionati sarà rispettivamente con ridondanza geografica o con ridondanza geografica della zona. 

#### <a name="networking"></a>Rete
La sezione rete consente di configurare le opzioni di rete. Queste impostazioni sono facoltative per la creazione dell'account di archiviazione e possono essere configurate in un secondo momento, se necessario. Per altre informazioni su queste opzioni, vedere considerazioni sulla File di Azure [di rete.](storage-files-networking-overview.md)

#### <a name="data-protection"></a>Protezione dei dati
La sezione protezione dati consente di configurare i criteri di eliminazione soft per le condivisioni file di Azure nell'account di archiviazione. Altre impostazioni correlate all'eliminazione software per BLOB, contenitori, ripristino tempormente per contenitori, controllo delle versioni e feed di modifiche si applicano solo all'archiviazione BLOB di Azure.

#### <a name="advanced"></a>Avanzato
La sezione avanzata contiene diverse impostazioni importanti per le condivisioni file di Azure:

- **Trasferimento sicuro obbligatorio:** questo campo indica se l'account di archiviazione richiede la crittografia in transito per la comunicazione con l'account di archiviazione. Se è necessario il supporto di SMB 2.1, è necessario disabilitare questa opzione.

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-secure-transfer.png" alt-text="Screenshot del trasferimento sicuro abilitato nelle impostazioni avanzate per l'account di archiviazione.":::

- **Condivisioni file di grandi** dimensioni: questo campo abilita l'account di archiviazione per le condivisioni file che si estendono fino a 100 TiB. L'abilitazione di questa funzionalità limiterà l'account di archiviazione solo alle opzioni di archiviazione con ridondanza locale e con ridondanza della zona. Dopo aver abilitato un account di archiviazione per utilizzo utilizzo elevato per le condivisioni file di grandi dimensioni, non è possibile disabilitare la funzionalità di condivisione file di grandi dimensioni. Gli account di archiviazione FileStorage (account di archiviazione per le condivisioni file Premium) non hanno questa opzione, perché tutte le condivisioni file Premium possono aumentare fino a 100 TiB. 

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-large-file-shares.png" alt-text="Screenshot dell'impostazione di condivisione file di grandi dimensioni nel pannello Avanzato dell'account di archiviazione.":::

Le altre impostazioni disponibili nella scheda avanzate (spazio dei nomi gerarchico per Azure Data Lake Storage gen 2, livello BLOB predefinito, NFSv3 per l'archiviazione BLOB e così via) non si applicano a File di Azure.

> [!Important]  
> La selezione del livello di accesso BLOB non influisce sul livello della condivisione file.

#### <a name="tags"></a>Tag
I tag sono coppie nome-valore che consentono di classificare le risorse e visualizzare dati di fatturazione consolidati tramite l'applicazione dello stesso tag a più risorse e gruppi di risorse. Sono facoltativi e possono essere applicati dopo la creazione dell'account di archiviazione.

#### <a name="review--create"></a>Rivedi e crea
Il passaggio finale per creare l'account di archiviazione consiste nel selezionare **il pulsante Crea** nella scheda **Rivedi e** crea. Questo pulsante non sarà disponibile se tutti i campi obbligatori per un account di archiviazione non vengono compilati.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Per creare un account di archiviazione con PowerShell, si userà il `New-AzStorageAccount` cmdlet . Questo cmdlet include molte opzioni. vengono visualizzate solo le opzioni necessarie. Per altre informazioni sulle opzioni avanzate, vedere la documentazione [ `New-AzStorageAccount` del cmdlet](/powershell/module/az.storage/new-azstorageaccount).

Per semplificare la creazione dell'account di archiviazione e della condivisione file successiva, verranno archiviati diversi parametri nelle variabili. È possibile sostituire il contenuto della variabile con i valori che si desidera, ma si noti che il nome dell'account di archiviazione deve essere univoco a livello globale.

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Per creare un account di archiviazione in grado di archiviare condivisioni file di Azure standard, si userà il comando seguente. Il parametro è correlato al tipo di ridondanza desiderato. Se si desidera un account di archiviazione con ridondanza geografica o con ridondanza della zona geografica, è necessario `-SkuName` rimuovere anche il parametro `-EnableLargeFileShare` .

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Per creare un account di archiviazione in grado di archiviare condivisioni file premium di Azure, si userà il comando seguente. Si noti che il parametro è stato modificato per includere sia il livello di `-SkuName` `Premium` ridondanza desiderato con ridondanza locale ( `LRS` ). Il parametro è invece perché le condivisioni file Premium devono essere create in un account di archiviazione FileStorage anziché in un account di archiviazione `-Kind` `FileStorage` `StorageV2` GPv2.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
Per creare un account di archiviazione usando l'interfaccia della riga di comando di Azure, si userà il comando az storage account create. Questo comando include molte opzioni. vengono visualizzate solo le opzioni necessarie. Per altre informazioni sulle opzioni avanzate, vedere la documentazione [ `az storage account create` del comando](/cli/azure/storage/account).

Per semplificare la creazione dell'account di archiviazione e della condivisione file successiva, verranno archiviati diversi parametri nelle variabili. È possibile sostituire il contenuto della variabile con i valori che si desidera, ma si noti che il nome dell'account di archiviazione deve essere univoco a livello globale.

```azurecli
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Per creare un account di archiviazione in grado di archiviare condivisioni file di Azure standard, si userà il comando seguente. Il parametro è correlato al tipo di ridondanza desiderato. Se si vuole un account di archiviazione con ridondanza geografica o con ridondanza della zona geografica, è necessario rimuovere `--sku` anche il `--enable-large-file-share` parametro .

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Per creare un account di archiviazione in grado di archiviare condivisioni file di Azure Premium, si userà il comando seguente. Si noti che il parametro è stato modificato per includere sia che il livello di `--sku` `Premium` ridondanza desiderato di con ridondanza locale ( `LRS` ). Il parametro è invece di perché le condivisioni file Premium devono essere create in un account di archiviazione FileStorage anziché in un account di archiviazione per utilizzo `--kind` `FileStorage` `StorageV2` genericov2.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-a-file-share"></a>Creare una condivisione file
Dopo aver creato l'account di archiviazione, non resta che creare la condivisione file. Questo processo è per lo più lo stesso indipendentemente dal fatto che si utilizzi una condivisione file Premium o una condivisione file standard. È necessario considerare le differenze seguenti.

Le condivisioni file Standard possono essere distribuite in uno dei livelli standard: ottimizzato per le transazioni (impostazione predefinita), ad accesso più caldo o ad accesso più freddo. Si tratta di un livello per condivisione  file che non è interessato dal livello di accesso BLOB dell'account di archiviazione (questa proprietà si riferisce solo all'archiviazione BLOB di Azure, non è affatto File di Azure). È possibile modificare il livello della condivisione in qualsiasi momento dopo la distribuzione. Le condivisioni file Premium non possono essere convertite direttamente in alcun livello Standard.

> [!Important]  
> È possibile spostare le condivisioni file tra livelli all'interno di tipi di account di archiviazione GPv2 (ottimizzato per le transazioni, ad accesso frequente e ad accesso sporadico). Gli spostamenti delle condivisioni tra livelli comportano transazioni: il passaggio da un livello ad accesso più frequente a uno ad accesso più sporadico comporta l'addebito per le transazioni di scrittura del livello più sporadico per ogni file della condivisione, mentre un passaggio da un livello più sporadico a uno più frequente comporterà l'addebito delle transazioni di lettura del livello ad accesso sporadico per ogni file della condivisione.

La **proprietà quota** indica un valore leggermente diverso tra le condivisioni file Premium e Standard:

- Per le condivisioni file standard, si tratta di un limite superiore della condivisione file di Azure, oltre il quale gli utenti finali non possono andare. Se non viene specificata una quota, la condivisione file standard può estendersi fino a 100 TiB (o 5 TiB se la proprietà condivisioni file di grandi dimensioni non è impostata per un account di archiviazione).

- Per le condivisioni file Premium, quota significa **dimensioni di cui è stato effettuato il provisioning.** La dimensione di cui è stato effettuato il provisioning è l'importo che verrà fatturato, indipendentemente dall'utilizzo effettivo. Per altre informazioni su come pianificare una condivisione file Premium, vedere [Provisioning di condivisioni file Premium.](understanding-billing.md#provisioned-model)

# <a name="portal"></a>[Portale](#tab/azure-portal)
Se l'account di archiviazione è stato appena creato, è possibile accedervi dalla schermata di distribuzione selezionando **Vai alla risorsa**. Una volta nell'account di archiviazione, selezionare **condivisioni file** nel sommario per l'account di archiviazione.

Nell'elenco delle condivisioni file dovrebbero essere presenti tutte le condivisioni file create in precedenza in questo account di archiviazione. una tabella vuota se non sono ancora state create condivisioni file. Selezionare **+ Condivisione file** per creare una nuova condivisione file.

Sullo schermo dovrebbe essere visualizzato il pannello della nuova condivisione file. Completare i campi nel pannello nuova condivisione file per creare una condivisione file:

- **Nome**: nome della condivisione file da creare.
- **Quota**: quota della condivisione file per le condivisioni file standard. dimensioni di cui è stato effettuato il provisioning della condivisione file per le condivisioni file Premium.
- **Livelli:** il livello selezionato per una condivisione file. Questo campo è disponibile solo in un account di archiviazione per utilizzo **generico (GPv2).** È possibile scegliere transazioni ottimizzate, ad accesso caldo o ad accesso freddo. Il livello della condivisione può essere modificato in qualsiasi momento. È consigliabile scegliere il livello più caldo possibile durante una migrazione, per ridurre al minimo le spese delle transazioni e quindi passare a un livello inferiore, se necessario, al termine della migrazione.

Selezionare **Crea** per completare la creazione della nuova condivisione.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
È possibile creare una condivisione file di Azure con il [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) cmdlet . I comandi di PowerShell seguenti presuppongono che siano state impostate le variabili e come definito in precedenza nella sezione creazione di un account di `$resourceGroupName` `$storageAccountName` archiviazione con Azure PowerShell. 

L'esempio seguente illustra la creazione di una condivisione file con un livello esplicito usando il `-AccessTier` parametro . A tale scopo, è necessario usare il modulo Az.Storage di anteprima, come indicato nell'esempio. Se non viene specificato un livello, perché si usa il modulo Az.Storage disponibile a livello generale o perché non è stato incluso questo comando, il livello predefinito per le condivisioni file standard è ottimizzato per le transazioni.

> [!Important]  
> Per le condivisioni file Premium, `-QuotaGiB` il parametro fa riferimento alle dimensioni di cui è stato effettuato il provisioning della condivisione file. La dimensione di cui è stato effettuato il provisioning della condivisione file è l'importo che verrà fatturato, indipendentemente dall'utilizzo. Le condivisioni file standard vengono fatturate in base all'utilizzo anziché alle dimensioni di cui è stato effettuato il provisioning.

```powershell
# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myshare"

New-AzRmStorageShare `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $storageAccountName `
        -Name $shareName `
        -AccessTier TransactionOptimized `
        -QuotaGiB 1024 | `
    Out-Null
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
È possibile creare una condivisione file di Azure con il [`az storage share-rm create`](/cli/azure/storage/share-rm#az_storage_share_rm_create) comando . I comandi seguenti dell'interfaccia della riga di comando di Azure presuppongono che siano state impostate le variabili e come definito in precedenza nella sezione Creazione di un account di archiviazione con l'interfaccia della `$resourceGroupName` riga di comando di `$storageAccountName` Azure.

> [!Important]  
> Per le condivisioni file Premium, il parametro fa riferimento alle dimensioni di cui è `--quota` stato effettuato il provisioning della condivisione file. La dimensione di cui è stato effettuato il provisioning della condivisione file è l'importo che verrà fatturato, indipendentemente dall'utilizzo. Le condivisioni file standard vengono fatturate in base all'utilizzo anziché alle dimensioni di cui è stato effettuato il provisioning.

```azurecli
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "TransactionOptimized" \
    --quota 1024 \
    --output none
```

---

> [!Note]  
> Il nome della condivisione file deve essere composto solo da caratteri minuscoli. Per informazioni dettagliate su come denominare le condivisioni e i file, vedere [Naming and referencing shares, directories, files, and metadata](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Denominazione e riferimento a condivisioni, directory, file e metadati).

### <a name="change-the-tier-of-an-azure-file-share"></a>Modificare il livello di una condivisione file di Azure
Le condivisioni file distribuite nell'account di archiviazione per utilizzo generico **v2 (GPv2)** possono essere nei livelli ottimizzati per le transazioni, ad accesso più caldo o ad accesso cool. È possibile modificare il livello della condivisione file di Azure in qualsiasi momento, in base ai costi delle transazioni, come descritto in precedenza.

# <a name="portal"></a>[Portale](#tab/azure-portal)
Nella pagina dell'account  di archiviazione principale selezionare Condivisioni file e selezionare il riquadro Condivisioni **file.** È anche possibile passare a **Condivisioni file** tramite il sommario per l'account di archiviazione.

:::image type="content" source="media/storage-files-quick-create-use-windows/click-files.png" alt-text="Screenshot del pannello dell'account di archiviazione con le condivisioni file selezionate.":::

Nell'elenco di tabelle delle condivisioni file selezionare la condivisione file per cui si vuole modificare il livello. Nella pagina di panoramica della condivisione file selezionare **Cambia livello** dal menu.

![Screenshot della pagina di panoramica della condivisione file con il pulsante Cambia livello evidenziato](media/storage-how-to-create-file-share/change-tier-0.png)

Nella finestra di dialogo risultante selezionare il livello desiderato: ottimizzato per le transazioni, ad accesso caldo o ad accesso freddo.

![Screenshot della finestra di dialogo cambia livello](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Il cmdlet di PowerShell seguente presuppone che siano state impostate le variabili , , come descritto `$resourceGroupName` nelle sezioni precedenti di questo `$storageAccountName` `$shareName` documento.

```PowerShell
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
Il comando dell'interfaccia della riga di comando di Azure seguente presuppone che siano state impostate le variabili , e come descritto `$resourceGroupName` nelle sezioni precedenti di questo `$storageAccountName` `$shareName` documento.

```azurecli
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>Passaggi successivi
- [Pianificare una distribuzione di File di Azure](storage-files-planning.md) [o Pianificare una distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md). 
- [Panoramica della rete](storage-files-networking-overview.md).
- Connettere e montare una condivisione file [in Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)e [Linux.](storage-how-to-use-files-linux.md)