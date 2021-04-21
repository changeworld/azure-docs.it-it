---
title: Modificare la modalità di replica di un account di archiviazione
titleSuffix: Azure Storage
description: Informazioni su come modificare la modalità di replica dei dati in un account di archiviazione esistente.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eb8bbf852803df53c43cef90bd2229bfcddd60d4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766188"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Modificare la modalità di replica di un account di archiviazione

Archiviazione di Azure archivia sempre più copie dei dati in modo che siano protetti da eventi pianificati e non pianificati, inclusi errori hardware temporanei, interruzioni della rete o dell'alimentazione e forti calamità naturali. La ridondanza garantisce che l'account di archiviazione soddisfi il [Contratto di servizio per Archiviazione di Azure](https://azure.microsoft.com/support/legal/sla/storage/) anche in caso di errori.

Archiviazione di Azure offre i tipi di replica seguenti:

- Archiviazione con ridondanza locale (LRS)
- Archiviazione con ridondanza della zona (ZRS)
- Archiviazione con ridondanza geografica o archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)
- Archiviazione con ridondanza della zona geografica (GZRS) o archiviazione con ridondanza della zona geografica e accesso in lettura (RA-GZRS)

Per una panoramica di ognuna di queste opzioni, vedere Archiviazione di Azure [ridondanza](storage-redundancy.md).

## <a name="switch-between-types-of-replication"></a>Passare da un tipo di replica all'altro

È possibile passare un account di archiviazione da un tipo di replica a qualsiasi altro tipo, ma alcuni scenari sono più semplici di altri. Se si vuole aggiungere o rimuovere la replica geografica o l'accesso in lettura all'area secondaria, è possibile usare portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per aggiornare l'impostazione di replica. Tuttavia, se si vuole modificare la modalità di replica dei dati nell'area primaria, passando da LRS a ZRS o viceversa, è necessario eseguire una migrazione manuale.

La tabella seguente offre una panoramica di come passare da ogni tipo di replica a un altro:

| Commutazione | ... a LRS | ... a GRS/RA-GRS | ... a ZRS | ... a GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... da LRS</b> | N/D | Usare portale di Azure, PowerShell o l'interfaccia della riga di comando per modificare l'impostazione di replica<sup>1,2</sup> | Eseguire una migrazione manuale <br /><br /> OR <br /><br /> Richiedere una migrazione in tempo reale | Eseguire una migrazione manuale <br /><br /> OR <br /><br /> Passare prima a GRS/RA-GRS e quindi richiedere una migrazione in tempo<sup>reale 1</sup> |
| <b>... da GRS/RA-GRS</b> | Usare portale di Azure, PowerShell o l'interfaccia della riga di comando per modificare l'impostazione di replica | N/D | Eseguire una migrazione manuale <br /><br /> OR <br /><br /> Passare prima all'istanza di LRS e quindi richiedere una migrazione in tempo reale | Eseguire una migrazione manuale <br /><br /> OR <br /><br /> Richiedere una migrazione in tempo reale |
| <b>... da ZRS</b> | Eseguire una migrazione manuale | Eseguire una migrazione manuale | N/D | Richiedere una migrazione in tempo reale |
| <b>... da GZRS/RA-GZRS</b> | Eseguire una migrazione manuale | Eseguire una migrazione manuale | Usare portale di Azure, PowerShell o l'interfaccia della riga di comando per modificare l'impostazione di replica | N/D |

<sup>1</sup> Comporta un addebito una sola volta in uscita.<br />
<sup>2</sup> La migrazione dall'archiviazione con archiviazione con accesso in lettura all'archiviazione con accesso in lettura non è supportata se l'account di archiviazione contiene BLOB nel livello archivio.<br />
<sup>3</sup> La conversione da ZRS a GZRS/RA-GZRS o viceversa non è supportata nelle aree seguenti: Stati Uniti orientali 2, Stati Uniti orientali, Europa occidentale.

> [!CAUTION]
> Se è stato eseguito un [failover dell'account](storage-disaster-recovery-guidance.md) (RA-)GRS o (RA-)GZRS, l'account è con ridondanza locale nella nuova area primaria dopo il failover. La migrazione in tempo reale a ZRS o GZRS per un account LRS risultante da un failover non è supportata. Questo vale anche nel caso delle cosiddette operazioni di failback. Ad esempio, se si esegue un failover dell'account da RA-GZRS a LRS nell'area secondaria e quindi lo si configura nuovamente in RA-GRS ed si esegue un altro failover dell'account nell'area primaria originale, non è possibile contattare il supporto per la migrazione in tempo reale originale a RA-GZRS nell'area primaria. Sarà invece necessario eseguire una migrazione manuale a ZRS o GZRS.

## <a name="change-the-replication-setting"></a>Modificare l'impostazione di replica

È possibile usare portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per modificare l'impostazione di replica per un account di archiviazione, purché non si cambi la modalità di replica dei dati nell'area primaria. Se si esegue la migrazione da LRS nell'area primaria a ZRS nell'area primaria o viceversa, è necessario eseguire una migrazione manuale o una migrazione in tempo reale.

La modifica della modalità di replica dell'account di archiviazione non comporta tempi di insoddati per le applicazioni.

# <a name="portal"></a>[Portale](#tab/portal)

Per modificare l'opzione di ridondanza per l'account di archiviazione nel portale di Azure, seguire questa procedura:

1. Passare all'account di archiviazione nel portale di Azure.
1. Selezionare **l'impostazione** Configurazione.
1. Aggiornare **l'impostazione** Replica.

![Screenshot che mostra come modificare l'opzione di replica nel portale](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per modificare l'opzione di ridondanza per l'account di archiviazione con PowerShell, chiamare il [comando Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) e specificare il `-SkuName` parametro :

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per modificare l'opzione di ridondanza per l'account di archiviazione con l'interfaccia della riga di comando di Azure, chiamare il [comando az storage account update](/cli/azure/storage/account#az_storage_account_update) e specificare il parametro `--sku` :

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs-gzrs-or-ra-gzrs"></a>Eseguire una migrazione manuale a ZRS, GZRS o RA-GZRS

Se si vuole modificare il modo in cui i dati nell'account di archiviazione vengono replicati nell'area primaria, passando dall'archiviazione con registrazione locale a ZRS o viceversa, è possibile scegliere di eseguire una migrazione manuale. Una migrazione manuale offre maggiore flessibilità rispetto a una migrazione in tempo reale. È possibile controllare la tempistica di una migrazione manuale, quindi usare questa opzione se è necessario completare la migrazione entro una determinata data.

Quando si esegue una migrazione manuale da LRS a ZRS nell'area primaria o viceversa, l'account di archiviazione di destinazione può essere con ridondanza geografica e può anche essere configurato per l'accesso in lettura all'area secondaria. Ad esempio, è possibile eseguire la migrazione di un account di archiviazione con archiviazione con accesso in locale a un account GZRS o RA-GZRS in un unico passaggio.

Una migrazione manuale può comportare tempi di inattività dell'applicazione. Se l'applicazione richiede disponibilità elevata, Microsoft offre anche un'opzione di migrazione in tempo reale. Una migrazione in tempo reale è una migrazione sul posto senza tempi di inattività.

Con una migrazione manuale, si copiano i dati dall'account di archiviazione esistente a un nuovo account di archiviazione che usa l'archiviazione con accesso in base al ruolo nell'area primaria. Per eseguire una migrazione manuale, è possibile usare una delle opzioni seguenti:

- Copiare i dati usando uno strumento esistente, ad esempio AzCopy, una delle librerie client Archiviazione di Azure o uno strumento affidabile di terze parti.
- Se si ha familiarità con Hadoop o HDInsight, è possibile collegare sia l'account di archiviazione di origine che l'account di archiviazione di destinazione al cluster. Quindi, parallelizzare il processo di copia dei dati con uno strumento, ad esempio DistCp.

## <a name="request-a-live-migration-to-zrs-gzrs-or-ra-gzrs"></a>Richiedere una migrazione in tempo reale a ZRS, GZRS o RA-GZRS

Se è necessario eseguire la migrazione dell'account di archiviazione dall'archiviazione con accesso in tempo reale all'archiviazione con accesso in tempo reale nell'area primaria senza tempi di inattività dell'applicazione, è possibile richiedere una migrazione in tempo reale da Microsoft. Per eseguire la migrazione da LRS a GZRS o RA-GZRS, passare prima all'grs o ra-grs e quindi richiedere una migrazione in tempo reale. Analogamente, è possibile richiedere una migrazione in tempo reale da GRS o RA-GRS a GZRS o RA-GZRS. Per eseguire la migrazione dall'grs o ra-grs all'ZRS, passare prima all'istanza di LRS e quindi richiedere una migrazione in tempo reale.

Durante una migrazione in tempo reale, è possibile accedere ai dati nell'account di archiviazione senza perdita di durabilità o disponibilità. Il contratto Archiviazione di Azure viene mantenuto durante il processo di migrazione. Non si verifica alcuna perdita di dati associata a una migrazione in tempo reale. Gli endpoint di servizio, le chiavi di accesso, le firme di accesso condiviso e altre opzioni dell'account rimangono invariati dopo la migrazione.

ZRS supporta solo account per utilizzo generico v2, quindi assicurarsi di aggiornare l'account di archiviazione prima di inviare una richiesta per una migrazione in tempo reale all'archiviazione con archiviazione con accesso in tempo reale. Per altre informazioni, [Eseguire l'aggiornamento alla versione 2 di un account di archiviazione per uso generico](storage-account-upgrade.md). Un account di archiviazione deve contenere i dati di cui eseguire la migrazione tramite migrazione in tempo reale.

La migrazione in tempo reale è supportata solo per gli account di archiviazione che usano la replica con l'archiviazione con ridondanza locale o l'archiviazione con ridondanza geografica. Se l'account usa l'archiviazione con ridondanza geografica e accesso in lettura, è necessario innanzitutto modificare il tipo di replica dell'account in archiviazione con ridondanza locale o archiviazione con ridondanza geografica prima di procedere. Questo passaggio intermedio rimuove, prima della migrazione, l'endpoint secondario di sola lettura fornito dall'archiviazione con ridondanza geografica e accesso in lettura.

Sebbene Microsoft gestisca tempestivamente la richiesta di migrazione in tempo reale, non c'è alcuna garanzia per quanto riguarda il tempo necessario per il completamento di questo tipo di migrazione. Se è necessario eseguire la migrazione dei dati in un'archiviazione con ridondanza della zona entro una certa data, Microsoft consiglia di eseguire una migrazione manuale. In genere, maggiore è la quantità di dati nell'account, più tempo richiederà la migrazione dei dati.

È necessario eseguire una migrazione manuale se:

- Si vuole eseguire la migrazione dei dati in un account di archiviazione ZRS che si trova in un'area diversa dall'account di origine.
- L'account di archiviazione è un BLOB di pagine Premium o un account BLOB in blocchi.
- Si vuole eseguire la migrazione dei dati da ZRS a LRS, GRS o RA-GRS.
- L'account di archiviazione include i dati nel livello di archiviazione.

È possibile richiedere la migrazione in tempo reale tramite il [portale del supporto tecnico di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). 

> [!IMPORTANT]
> Se è necessario eseguire la migrazione di più account di archiviazione, creare un singolo ticket di supporto e specificare i nomi degli account da convertire nella **scheda** Dettagli.

Seguire questa procedura per richiedere una migrazione in tempo reale:

1. Nel portale di Azure passare a un account di archiviazione di cui si vuole eseguire la migrazione.
1. In **Supporto e risoluzione dei problemi** selezionare Nuova richiesta di **supporto**.
1. Completare **la scheda Informazioni di** base in base alle informazioni sull'account:
    - **Tipo di problema:** selezionare **Tecnico.**
    - **Servizio:** selezionare **Servizi e quindi** Gestione account di **archiviazione**.
    - **Risorsa:** selezionare un account di archiviazione di cui eseguire la migrazione. Se è necessario specificare più account di archiviazione, è possibile farlo nella **sezione** Dettagli.
    - **Tipo di problema:** scegliere **Migrazione dei dati**.
    - **Sottotipo problema:** scegliere **Esegui migrazione a ZRS, GZRS o RA-GZRS.**

    :::image type="content" source="media/redundancy-migration/request-live-migration-basics-portal.png" alt-text="Screenshot che mostra come richiedere una migrazione in tempo reale - scheda Informazioni di base":::

1. Selezionare **Avanti**. Nella scheda **Soluzioni** è possibile verificare l'idoneità degli account di archiviazione per la migrazione.
1. Selezionare **Avanti**. Se è necessario eseguire la migrazione di  più account di archiviazione, nella scheda Dettagli specificare il nome di ogni account, separato da un punto e virgola.

    :::image type="content" source="media/redundancy-migration/request-live-migration-details-portal.png" alt-text="Screenshot che mostra come richiedere una migrazione in tempo reale - scheda Dettagli":::

1. Compilare le informazioni aggiuntive necessarie nella **scheda Dettagli,** quindi selezionare **Rivedi e crea** per rivedere e inviare il ticket di supporto. Un utente del supporto tecnico contatta l'utente per fornire l'assistenza necessaria.

> [!NOTE]
> Le condivisioni file Premium (account FileStorage) sono disponibili solo per LRS e ZRS.
>
> Gli account di archiviazione GZRS non supportano attualmente il livello di archiviazione. Per altri [dettagli, vedere Archiviazione BLOB](../blobs/storage-blob-storage-tiers.md) di Azure: livelli di accesso ad accesso ad accesso rapido, ad accesso ad accesso archivio e ad accesso archivio.
>
> I dischi gestiti sono disponibili solo per l'archiviazione con accesso in lettura e non possono essere migrati all'archiviazione con archiviazione con archiviazione con accesso in lettura. È possibile archiviare snapshot e immagini per i dischi gestiti SSD Standard in un'archiviazione HDD Standard e scegliere tra le opzioni di archiviazione con [autorizzazioni di archiviazione.](https://azure.microsoft.com/pricing/details/managed-disks/) Per informazioni sull'integrazione con i set di disponibilità, vedere [Introduzione ad Azure Managed Disks.](../../virtual-machines/managed-disks-overview.md#integration-with-availability-sets)

## <a name="switch-from-zrs-classic"></a>Passare dalla versione classica di ZRS

> [!IMPORTANT]
> Microsoft imposterà come deprecati gli account della versione classica dell'archiviazione con ridondanza della zona e ne eseguirà la migrazione il 31 marzo 2021. Altri dettagli verranno inviati ai clienti della versione classica dell'archiviazione con ridondanza della zona prima di essere deprecati.
>
> Quando l'archiviazione con archiviazione con zona diventa disponibile a livello generale in una determinata area, i clienti non saranno più in grado di creare account della versione classica dell'archiviazione con archiviazione con portale di Azure in tale area. L'uso di Microsoft PowerShell e dell'interfaccia della riga di comando di Azure per creare gli account della versione classica dell'archiviazione con ridondanza della zona sarà disponibile finché la versione classica dell'archiviazione con ridondanza della zona viene deprecata. Per informazioni sulla posizione in cui è disponibile l'archiviazione con ridondanza della Archiviazione di Azure archiviazione con [ridondanza.](storage-redundancy.md)

La versione classica dell'archiviazione con ridondanza della zona replica i dati in modo asincrono tra data center in una o due aree. I dati replicati potrebbero non essere disponibili a meno che Microsoft non avvii il failover all'area secondaria. Gli account della versione classica dell'archiviazione con ridondanza della zona non possono essere convertiti in o da archiviazione con ridondanza locale, archiviazione con ridondanza geografica o archiviazione con ridondanza geografica e accesso in lettura. Gli account della versione classica dell'archiviazione con ridondanza della zona non supportano inoltre le metriche o la registrazione.

La versione classica dell'archiviazione con ridondanza della zona è disponibile solo per i **BLOB in blocchi** negli account di archiviazione di tipo Utilizzo generico V1 (GPv1). Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md).

Per eseguire manualmente la migrazione dei dati dell'account ZRS da o verso un account di archiviazione con accesso in locale, archiviazione con accesso in archiviazione con archiviazione con accesso in sola riga o versione classica, usare uno degli strumenti seguenti: AzCopy, Azure Storage Explorer, PowerShell o l'interfaccia della riga di comando di Azure. È anche possibile creare una soluzione di migrazione personalizzata con una delle librerie client di Archiviazione di Azure.

È anche possibile aggiornare l'portale di Azure account di archiviazione classica dell'archiviazione con privilegi di accesso in base all'account di archiviazione con archiviazione con privilegi di archiviazione con privilegi di accesso in base all'account di archiviazione con archiviazione con privilegi di archiviazione con privilegi di accesso in base al nome.

# <a name="portal"></a>[Portale](#tab/portal)

Per eseguire l'aggiornamento all'archiviazione con portale di Azure archiviazione con portale di Azure, passare alle **impostazioni di** configurazione dell'account e scegliere **Aggiorna:**

![Eseguire l'aggiornamento da ZRS classico a ZRS nel portale](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per eseguire l'aggiornamento all'ZRS tramite PowerShell, chiamare il comando seguente:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire l'aggiornamento a ZRS tramite l'interfaccia della riga di comando di Azure, chiamare il comando seguente:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Costi associati alla modifica della modalità di replica dei dati

I costi associati alla modifica della modalità di replica dei dati dipendono dal percorso di conversione. L'ordinamento dalle offerte di ridondanza Archiviazione di Azure più costose include LRS, ZRS, GRS, RA-GRS, GZRS e RA-GZRS.

Ad esempio, il passaggio *dall'archiviazione* con ridondanza locale a qualsiasi altro tipo di replica comporta costi aggiuntivi perché si sta passando a un livello di ridondanza più sofisticato. La *migrazione all'archiviazione* con grs o RA-GRS comporta un addebito per la larghezza di banda in uscita perché i dati (nell'area primaria) vengono replicati nell'area secondaria remota. Questo addebito è una sola volta al momento della configurazione iniziale. Dopo la copia dei dati, non sono presenti altri addebiti per la migrazione. Per informazioni dettagliate sui costi addebitati per la larghezza di banda, vedere la [pagina dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Se si esegue la migrazione dell'account di archiviazione dall'archiviazione con accesso in archiviazione locale, non sono presenti costi aggiuntivi, ma i dati replicati vengono eliminati dalla posizione secondaria.

> [!IMPORTANT]
> Se si esegue la migrazione dell'account di archiviazione da RA-GRS a GRS o LRS, tale account viene fatturato come RA-GRS per altri 30 giorni oltre la data in cui è stato convertito.

## <a name="see-also"></a>Vedere anche

- [Azure Storage redundancy](storage-redundancy.md) (Ridondanza di Archiviazione di Azure)
- [Controllare la proprietà Last Sync Time per un account di archiviazione](last-sync-time-get.md)
- [Usare la ridondanza geografica per progettare applicazioni a disponibilità elevata](geo-redundant-design.md)
