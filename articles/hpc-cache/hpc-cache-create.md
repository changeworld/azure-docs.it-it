---
title: Creare una cache HPC di Azure
description: Come creare un'istanza di Cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 02934a1943ef37d282dd2a2e7862c5695bbd6ecb
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862706"
---
# <a name="create-an-azure-hpc-cache"></a>Creare una cache HPC di Azure

Usare il portale di Azure o l'interfaccia della riga di comando di Azure per creare la cache.

![screenshot della panoramica della cache nel portale di Azure, con il pulsante Crea nella parte inferiore](media/hpc-cache-home-page.png)

Fare clic sull'immagine seguente per guardare una [dimostrazione video della](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) creazione di una cache e dell'aggiunta di una destinazione di archiviazione.

[![anteprima video: Cache HPC di Azure: Configurazione (fare clic per visitare la pagina del video)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[Portale](#tab/azure-portal)

## <a name="define-basic-details"></a>Definire i dettagli di base

![screenshot della pagina dei dettagli del progetto nel portale di Azure](media/hpc-cache-create-basics.png)

In **Dettagli progetto** selezionare la sottoscrizione e il gruppo di risorse in cui verrà ospitata la cache.

In **Dettagli servizio** impostare il nome della cache e questi altri attributi:

* Località: selezionare una delle [aree supportate](hpc-cache-overview.md#region-availability).
* Rete virtuale: è possibile selezionare una rete virtuale esistente oppure crearne una nuova.
* Subnet: scegliere o creare una subnet con almeno 64 indirizzi IP (/24). Questa subnet deve essere usata solo per questa Cache HPC di Azure istanza.

## <a name="set-cache-capacity"></a>Impostare la capacità della cache
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Nella pagina **Cache** è necessario impostare la capacità della cache. Il valore qui impostato determina la quantità di dati che la cache può contenere e la velocità con cui può gestire le richieste client.

La capacità influisce anche sul costo della cache.

Scegliere la capacità impostando questi due valori:

* La velocità massima di trasferimento dei dati per la cache (velocità effettiva), in GB al secondo
* La quantità di spazio di archiviazione allocata per i dati memorizzati nella cache, in TB

Scegliere uno dei valori disponibili per la velocità effettiva e le dimensioni di archiviazione della cache.

Tenere presente che la velocità effettiva di trasferimento dei dati dipende dal carico di lavoro, dalle velocità di rete e dal tipo di destinazioni di archiviazione. I valori scelti impostano la velocità effettiva massima per l'intero sistema di cache, ma questa viene in parte usata per attività di overhead. Ad esempio, se un client richiede un file che non è già archiviato nella cache o se il file è contrassegnato come non obsoleto, la cache usa parte della velocità effettiva per recuperarlo dall'archiviazione back-end.

Cache HPC di Azure determina quali file vengono memorizzati nella cache e precaricati per massimizzare le percentuali di riscontri nella cache. Il contenuto della cache viene valutato in modo continuo e i file vengono spostati nell'archiviazione a lungo termine quando l'accesso è meno frequente. Scegliere una dimensione di archiviazione della cache che possa contenere facilmente il set attivo di file di lavoro, oltre a spazio aggiuntivo per i metadati e altro sovraccarico.

![screenshot della pagina di determinazione delle dimensioni della cache](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Abilitare Azure Key Vault crittografia (facoltativo)

La **pagina Chiavi di crittografia del** disco viene visualizzata tra le schede **Cache** **e** Tag.<!-- Read [Regional availability](hpc-cache-overview.md#region-availability) to learn more about region support. -->

Se si vogliono gestire le chiavi di crittografia usate per l'archiviazione cache, specificare le informazioni Azure Key Vault nella pagina **Chiavi di crittografia del** disco. L'insieme di credenziali delle chiavi deve essere nella stessa area e nella stessa sottoscrizione della cache.

È possibile ignorare questa sezione se non sono necessarie chiavi gestite dal cliente. Azure crittografa i dati con chiavi gestite da Microsoft per impostazione predefinita. Per altre [informazioni, vedere Crittografia di](../storage/common/storage-service-encryption.md) Archiviazione di Azure.

> [!NOTE]
>
> * Non è possibile passare da chiavi gestite da Microsoft a chiavi gestite dal cliente dopo aver creato la cache.
> * Dopo aver creato la cache, è necessario autorizzarla ad accedere all'insieme di credenziali delle chiavi. Fare clic **sul pulsante Abilita**  crittografia nella pagina Panoramica della cache per attivare la crittografia. Eseguire questo passaggio entro 90 minuti dalla creazione della cache.
> * I dischi della cache vengono creati dopo questa autorizzazione. Ciò significa che il tempo di creazione iniziale della cache è breve, ma la cache non sarà pronta per l'uso per dieci o più minuti dopo l'autorizzazione dell'accesso.

Per una spiegazione completa del processo di crittografia della chiave gestita dal cliente, vedere [Usare chiavi di crittografia](customer-keys.md)gestite dal cliente per Cache HPC di Azure .

![screenshot della pagina delle chiavi di crittografia con l'opzione "Customer Managed" selezionata e i campi dell'insieme di credenziali delle chiavi visualizzati](media/create-encryption.png)

Selezionare **Customer managed (Gestito dal** cliente) per scegliere la crittografia della chiave gestita dal cliente. Vengono visualizzati i campi della specifica dell'insieme di credenziali delle chiavi. Selezionare la Azure Key Vault da usare, quindi selezionare la chiave e la versione da usare per la cache. La chiave deve essere una chiave RSA a 2048 bit. Da questa pagina è possibile creare un nuovo insieme di credenziali delle chiavi, una nuova chiave o una nuova versione della chiave.

Dopo aver creato la cache, è necessario autorizzarla a usare il servizio dell'insieme di credenziali delle chiavi. Per [informazioni dettagliate, Azure Key Vault autorizzare la crittografia dalla cache.](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache)

## <a name="add-resource-tags-optional"></a>Aggiungere tag di risorsa (facoltativo)

La pagina **Tag** consente di aggiungere [tag di risorsa](../azure-resource-manager/management/tag-resources.md) all'istanza di Cache HPC di Azure.

## <a name="finish-creating-the-cache"></a>Completare la creazione della cache

Dopo aver configurato la nuova cache, fare clic **sulla scheda Rivedi e** crea. Il portale convalida le selezioni e consente di esaminare le scelte effettuate. Se tutte le voci sono state impostate correttamente, fare clic su **Crea**.

La creazione della cache richiede circa 10 minuti. È possibile monitorare l'avanzamento dell'operazione nel pannello delle notifiche del portale di Azure.

![screenshot delle pagine "distribuzione in corso" e "notifiche" della creazione della cache nel portale](media/hpc-cache-deploy-status.png)

Al termine dell'operazione di creazione, viene visualizzata una notifica con un collegamento alla nuova istanza di Cache HPC di Azure e la cache viene visualizzata nell'elenco **Risorse** della sottoscrizione.

![screenshot dell'istanza di Cache HPC di Azure nel portale di Azure](media/hpc-cache-new-overview.png)

> [!NOTE]
> Se la cache usa chiavi di crittografia gestite dal cliente, la cache potrebbe essere visualizzata nell'elenco delle risorse prima che lo stato della distribuzione cambi per essere completato. Non appena lo stato della cache è In attesa **della chiave,** è [possibile](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) autorizzarla a usare l'insieme di credenziali delle chiavi.

## <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>Creare la cache con l'interfaccia della riga di comando di Azure

[Configurare l'interfaccia della riga di comando di Azure Cache HPC di Azure](./az-cli-prerequisites.md).

> [!NOTE]
> L'interfaccia della riga di comando di Azure attualmente non supporta la creazione di una cache con chiavi di crittografia gestite dal cliente. Usare il portale di Azure.

Usare il [comando az hpc-cache create](/cli/azure/hpc-cache#az_hpc_cache_create) per creare un nuovo Cache HPC di Azure.

Specificare questi valori:

* Nome del gruppo di risorse della cache
* Nome cache
* Area di Azure
* Subnet della cache, nel formato seguente:

  ``--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"``

  La subnet della cache richiede almeno 64 indirizzi IP (/24) e non può ospitare altre risorse.

* Capacità della cache. Due valori impostano la velocità effettiva massima del Cache HPC di Azure:

  * Dimensioni della cache (in GB)
  * SKU delle macchine virtuali usate nell'infrastruttura della cache

  [az hpc-cache skus list](/cli/azure/hpc-cache/skus) mostra gli SKU disponibili e le opzioni valide per le dimensioni della cache per ognuno. Le opzioni relative alle dimensioni della cache variano da 3 TB a 48 TB, ma sono supportati solo alcuni valori.

  Questo grafico mostra le combinazioni di dimensioni della cache e SKU valide al momento della preparazione del documento (luglio 2020).

  | Dimensioni cache | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | sì         | no          | no          |
  | 6144 GB    | sì         | sì         | no          |
  | 12288 GB   | sì         | sì         | sì         |
  | 24576 GB   | no          | sì         | sì         |
  | 49152 GB   | no          | no          | sì         |

  Per informazioni **importanti su** prezzi, velocità effettiva e su come ridimensionare la cache in modo appropriato per il flusso di lavoro, leggere la sezione Impostare la capacità della cache nella scheda delle istruzioni del portale.

Esempio di creazione della cache:

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

La creazione della cache richiede alcuni minuti. In caso di esito positivo, il comando create restituisce un output simile al seguente:

```azurecli
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },
  "id": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619",
  "location": "eastus",
  "mountAddresses": [
    "10.3.0.17",
    "10.3.0.18",
    "10.3.0.19"
  ],
  "name": "my-cache-0619",
  "provisioningState": "Succeeded",
  "resourceGroup": "doc-demo-rg",
  "sku": {
    "name": "Standard_2G"
  },
  "subnet": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default",
  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.42",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-04-01T15:19:54.068299+00:00",
    "pendingFirmwareVersion": null
  }
}
```

Il messaggio include alcune informazioni utili, tra cui:

* Indirizzi di montaggio client: usare questi indirizzi IP quando si è pronti per connettere i client alla cache. Per [altre informazioni, Cache HPC di Azure](hpc-cache-mount.md) mount the Cache HPC di Azure .
* Stato dell'aggiornamento: quando viene rilasciato un aggiornamento software, questo messaggio verrà modificato. È possibile [aggiornare manualmente il software](hpc-cache-manage.md#upgrade-cache-software) della cache in qualsiasi momento oppure verrà applicato automaticamente dopo diversi giorni.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> Il modulo Az.HPCCache di PowerShell è attualmente disponibile in anteprima pubblica. Questa versione in anteprima viene fornita senza un contratto di servizio. Non è la scelta consigliata per carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero avere funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Requisiti

Se si sceglie di usare PowerShell in locale, per questo articolo è necessario installare il modulo Az PowerShell e connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Per altre informazioni sull'installazione del modulo Az PowerShell, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps). Se si sceglie di usare Cloud Shell, vedere [Panoramica di Azure Cloud Shell](../cloud-shell/overview.md) per altre informazioni.

> [!IMPORTANT]
> Mentre il **modulo Az.HPCCache** di PowerShell è in anteprima, è necessario installarlo separatamente usando il `Install-Module` cmdlet . Quando questo modulo di PowerShell sarà disponibile a livello generale, farà parte delle future versioni del modulo Az PowerShell e sarà disponibile in modo nativo dall'interno Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>Creare la cache con Azure PowerShell

> [!NOTE]
> Azure PowerShell attualmente non supporta la creazione di una cache con chiavi di crittografia gestite dal cliente. Usare il portale di Azure.

Usare il cmdlet [New-AzHpcCache](/powershell/module/az.hpccache/new-azhpccache) per creare un nuovo Cache HPC di Azure.

Specificare questi valori:

* Nome del gruppo di risorse della cache
* Nome cache
* Area di Azure
* Subnet della cache, nel formato seguente:

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"`

  La subnet della cache richiede almeno 64 indirizzi IP (/24) e non può ospitare altre risorse.

* Capacità della cache. Due valori impostano la velocità effettiva massima del Cache HPC di Azure:

  * Dimensioni della cache (in GB)
  * SKU delle macchine virtuali usate nell'infrastruttura della cache

  [Get-AzHpcCacheSku](/powershell/module/az.hpccache/get-azhpccachesku) mostra gli SKU disponibili e le opzioni valide per le dimensioni della cache per ognuno. Le opzioni relative alle dimensioni della cache variano da 3 TB a 48 TB, ma sono supportati solo alcuni valori.

  Questo grafico mostra le combinazioni di dimensioni della cache e SKU valide al momento della preparazione del documento (luglio 2020).

  | Dimensioni cache | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | sì         | no          | no          |
  | 6144 GB    | sì         | sì         | no          |
  | 12.288 GB   | sì         | sì         | sì         |
  | 24.576 GB   | no          | sì         | sì         |
  | 49.152 GB   | no          | no          | sì         |

  Per informazioni **importanti su** prezzi, velocità effettiva e su come ridimensionare la cache in modo appropriato per il flusso di lavoro, leggere la sezione Impostare la capacità della cache nella scheda delle istruzioni del portale.

Esempio di creazione della cache:

```azurepowershell-interactive
$cacheParams = @{
  ResourceGroupName = 'doc-demo-rg'
  CacheName = 'my-cache-0619'
  Location = 'eastus'
  cacheSize = '3072'
  SubnetUri = "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default"
  Sku = 'Standard_2G'
}
New-AzHpcCache @cacheParams
```

La creazione della cache richiede alcuni minuti. In caso di esito positivo, il comando create restituisce l'output seguente:

```Output
cacheSizeGb       : 3072
health            : @{state=Healthy; statusDescription=The cache is in Running state}
id                : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619
location          : eastus
mountAddresses    : {10.3.0.17, 10.3.0.18, 10.3.0.19}
name              : my-cache-0619
provisioningState : Succeeded
resourceGroup     : doc-demo-rg
sku               : @{name=Standard_2G}
subnet            : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default
tags              :
type              : Microsoft.StorageCache/caches
upgradeStatus     : @{currentFirmwareVersion=5.3.42; firmwareUpdateDeadline=1/1/0001 12:00:00 AM; firmwareUpdateStatus=unavailable; lastFirmwareUpdate=4/1/2020 10:19:54 AM; pendingFirmwareVersion=}
```

Il messaggio include alcune informazioni utili, tra cui gli elementi seguenti:

* Indirizzi di montaggio client: usare questi indirizzi IP quando si è pronti per connettere i client alla cache. Per [altre informazioni, Cache HPC di Azure](hpc-cache-mount.md) vedere Montare il Cache HPC di Azure.
* Stato aggiornamento: quando viene rilasciato un aggiornamento software, questo messaggio cambia. È possibile [aggiornare manualmente il software](hpc-cache-manage.md#upgrade-cache-software) della cache in un momento pratico oppure viene applicato automaticamente dopo diversi giorni.

---

## <a name="next-steps"></a>Passaggi successivi

Dopo aver visualizzato la cache **nell'elenco** Risorse, è possibile passare al passaggio successivo.

* [Definire le destinazioni di](hpc-cache-add-storage.md) archiviazione per concedere alla cache l'accesso alle origini dati.
* Se si usano chiavi di crittografia [](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) gestite dal cliente, è necessario autorizzare la crittografia Azure Key Vault dalla pagina di panoramica della cache per completare la configurazione della cache. È necessario eseguire questo passaggio prima di poter aggiungere spazio di archiviazione. Per informazioni [dettagliate, vedere](customer-keys.md) Usare chiavi di crittografia gestite dal cliente.
