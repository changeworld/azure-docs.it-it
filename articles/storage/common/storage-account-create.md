---
title: Creare un account di archiviazione
titleSuffix: Azure Storage
description: Informazioni su come creare un account di archiviazione per archiviare BLOB, file, code e tabelle. Un account di archiviazione di Azure fornisce uno spazio dei nomi univoco Microsoft Azure per la lettura e la scrittura dei dati.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/19/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8c0158ea4e93b4bbefa7b41b5a280fa804ede35f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791284"
---
# <a name="create-a-storage-account"></a>Creare un account di archiviazione

Un account di archiviazione di Azure contiene tutti Archiviazione di Azure oggetti dati: BLOB, file, code e tabelle. L'account di archiviazione fornisce uno spazio dei nomi univoco per Archiviazione di Azure dati accessibili da qualsiasi parte del mondo tramite HTTP o HTTPS. Per altre informazioni sugli account di archiviazione di Azure, vedere Panoramica [dell'account di archiviazione.](storage-account-overview.md)

In questa procedura si apprenderà come creare un account di archiviazione usando portale di Azure [,](https://portal.azure.com/) [Azure PowerShell](/powershell/azure/), l'interfaccia della riga di comando di [Azure](/cli/azure)o un Azure Resource Manager [di archiviazione](../../azure-resource-manager/management/overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Nessuna.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per creare un account di archiviazione di Azure con PowerShell, assicurarsi di aver installato il modulo [Az PowerShell](https://www.powershellgallery.com/packages/Az)versione 0.7 o successiva. Per altre informazioni, vedere [Introduzione al modulo Azure PowerShell Az.](/powershell/azure/new-azureps-module-az)

Per trovare la versione corrente, eseguire il comando seguente:

```powershell
Get-InstalledModule -Name "Az"
```

Per installare o aggiornare Azure PowerShell, vedere [Installare Azure PowerShell modulo](/powershell/azure/install-az-ps).

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile accedere ad Azure ed eseguire i comandi dell'interfaccia della riga di comando di Azure in uno dei due modi seguenti:

- È possibile eseguire i comandi dell'interfaccia della riga di comando nel portale di Azure, in Azure Cloud Shell.
- È possibile installare l'interfaccia della riga di comando ed eseguire i relativi comandi in locale.

### <a name="use-azure-cloud-shell"></a>Usare Azure Cloud Shell

Azure Cloud Shell è una shell Bash gratuita che è possibile eseguire direttamente nel portale di Azure. L'interfaccia della riga di comando di Azure è preinstallata e configurata per l'uso con l'account corrente. Fare clic **Cloud Shell** pulsante del menu nella sezione in alto a destra del portale di Azure:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Il pulsante avvia una shell interattiva che è possibile usare per eseguire i passaggi descritti in questo articolo:

[![Screenshot che mostra la finestra di Cloud Shell nel portale](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Installare l'interfaccia della riga di comando in locale

È anche possibile installare e usare l'interfaccia della riga di comando di Azure in locale. Gli esempi in questo articolo richiedono l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

# <a name="template"></a>[Modello](#tab/template)

No.

---

Successivamente, accedere ad Azure.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Accedere al [portale di Azure](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate per eseguire l'autenticazione.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per avviare Azure Cloud Shell, accedere al [portale di Azure](https://portal.azure.com).

Per accedere all'installazione locale dell'interfaccia della riga di comando, eseguire il [comando az login:](/cli/azure/reference-index#az_login)

```azurecli-interactive
az login
```

# <a name="template"></a>[Modello](#tab/template)

N/D

---

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Un account di archiviazione è una Azure Resource Manager risorsa. Resource Manager è il servizio di distribuzione e gestione per Azure. Per altre informazioni, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/management/overview.md).

Ogni Resource Manager, incluso un account di archiviazione di Azure, deve appartenere a un gruppo di risorse di Azure. Un gruppo di risorse è un contenitore logico per raggruppare i servizi di Azure. Quando si crea un account di archiviazione, è possibile creare un nuovo gruppo di risorse o usarne uno esistente. Questa procedura illustra come creare un nuovo gruppo di risorse.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per creare un account di archiviazione di Azure con portale di Azure, seguire questa procedura:

1. Nel menu a sinistra del portale selezionare **Account di archiviazione** per visualizzare un elenco degli account di archiviazione.
1. Nella pagina **Account di archiviazione** selezionare **Nuovo**.

Le opzioni per il nuovo account di archiviazione sono organizzate in schede nella **pagina Crea un account di** archiviazione. Le sezioni seguenti descrivono ognuna delle schede e le relative opzioni.

### <a name="basics-tab"></a>Scheda Informazioni di base

Nella scheda **Informazioni di base** specificare le informazioni essenziali per l'account di archiviazione. Dopo aver  completato la scheda Informazioni di base, è possibile scegliere di personalizzare ulteriormente il nuovo account di archiviazione impostando le opzioni nelle altre schede oppure selezionare **Rivedi e** crea per accettare le opzioni predefinite e procedere con la convalida e la creazione dell'account.

Nella tabella seguente vengono descritti i campi della **scheda Informazioni di** base.

| Sezione | Campo | Obbligatoria o facoltativa | Descrizione |
|--|--|--|--|
| Dettagli del progetto | Subscription | Obbligatoria | Selezionare la sottoscrizione per il nuovo account di archiviazione. |
| Dettagli del progetto | Resource group | Obbligatoria | Creare un nuovo gruppo di risorse per questo account di archiviazione o selezionarne uno esistente. Per altre informazioni, vedere [Gruppi di risorse](../../azure-resource-manager/management/overview.md#resource-groups). |
| Dettagli dell'istanza | Nome account di archiviazione | Obbligatoria | Scegliere un nome univoco per l'account di archiviazione. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole. |
| Dettagli dell'istanza | Region | Obbligatoria | Selezionare l'area appropriata per l'account di archiviazione. Per altre informazioni, vedere [Aree e zone di disponibilità in Azure.](../../availability-zones/az-overview.md)<br /><br />Non tutte le aree sono supportate per tutti i tipi di account di archiviazione o configurazioni di ridondanza. Per altre informazioni, vedere [Ridondanza di Archiviazione di Azure](storage-redundancy.md).<br /><br />La scelta dell'area può avere un impatto sulla fatturazione. Per altre informazioni, vedere Fatturazione [dell'account di archiviazione.](storage-account-overview.md#storage-account-billing) |
| Dettagli dell'istanza | Prestazioni | Obbligatoria | Selezionare **Prestazioni standard** per gli account di archiviazione per utilizzo generico v2 (impostazione predefinita). Questo tipo di account è consigliato da Microsoft per la maggior parte degli scenari. Per altre informazioni, vedere [Tipi di account di archiviazione](storage-account-overview.md#types-of-storage-accounts).<br /><br />Selezionare **Premium per** gli scenari che richiedono bassa latenza. Dopo aver **selezionato Premium,** selezionare il tipo di account di archiviazione Premium da creare. Sono disponibili i tipi seguenti di account di archiviazione Premium: <ul><li>[BLOB in blocchi](../blobs/storage-blob-performance-tiers.md)</li><li>[Condivisioni file](../files/storage-files-planning.md#management-concepts)</li><li>[BLOB di pagine](../blobs/storage-blob-pageblob-overview.md)</li></ul> |
| Dettagli dell'istanza | Ridondanza | Obbligatoria | Selezionare la configurazione di ridondanza desiderata. Non tutte le opzioni di ridondanza sono disponibili per tutti i tipi di account di archiviazione in tutte le aree. Per altre informazioni sulle configurazioni di ridondanza, [vedere Archiviazione di Azure ridondanza.](storage-redundancy.md)<br /><br />Se si seleziona una configurazione con ridondanza geografica (GRS o GZRS), i dati vengono replicati in data center in un'area diversa. Per l'accesso in lettura ai dati nell'area secondaria, selezionare Rendi disponibile l'accesso in lettura ai dati in caso di **invariabilità a livello di area.** |

L'immagine seguente mostra una configurazione standard per un nuovo account di archiviazione.

:::image type="content" source="media/storage-account-create/create-account-basics-tab.png" alt-text="Screenshot che mostra una configurazione standard per un nuovo account di archiviazione - Scheda Informazioni di base":::

### <a name="advanced-tab"></a>Avanzate - scheda

Nella scheda **Avanzate** è possibile configurare opzioni aggiuntive e modificare le impostazioni predefinite per il nuovo account di archiviazione. Alcune di queste opzioni possono essere configurate anche dopo la creazione dell'account di archiviazione, mentre altre devono essere configurate al momento della creazione.

Nella tabella seguente vengono descritti i campi della **scheda** Avanzate.

| Sezione | Campo | Obbligatoria o facoltativa | Descrizione |
|--|--|--|--|
| Security | Abilita trasferimento sicuro | Facoltativo | Abilitare il trasferimento sicuro per richiedere che le richieste in ingresso a questo account di archiviazione siano effettuate solo tramite HTTPS (impostazione predefinita). Consigliato per una sicurezza ottimale. Per altre informazioni, vedere [Richiedere il trasferimento sicuro per garantire connessioni sicure.](storage-require-secure-transfer.md) |
| Security | Abilitare la crittografia dell'infrastruttura | Facoltativo | Per impostazione predefinita, la crittografia dell'infrastruttura non è abilitata. Abilitare la crittografia dell'infrastruttura per crittografare i dati a livello di servizio e di infrastruttura. Per altre informazioni, vedere Creare [un account di archiviazione con la crittografia dell'infrastruttura abilitata per la crittografia doppia dei dati.](infrastructure-encryption-enable.md) |
| Security | Abilita accesso pubblico ai BLOB | Facoltativo | Se abilitata, questa impostazione consente a un utente con le autorizzazioni appropriate di abilitare l'accesso pubblico anonimo a un contenitore nell'account di archiviazione (impostazione predefinita). La disabilitazione di questa impostazione impedisce l'accesso pubblico anonimo all'account di archiviazione. Per altre informazioni, vedere [Impedire l'accesso in lettura pubblico anonimo a contenitori e BLOB.](../blobs/anonymous-read-access-prevent.md)<br> <br> L'abilitazione dell'accesso pubblico al BLOB non rende disponibili i dati BLOB per l'accesso pubblico a meno che l'utente non eserviti il passaggio aggiuntivo per configurare in modo esplicito l'impostazione di accesso pubblico del contenitore. |
| Security | Abilitare l'accesso alla chiave dell'account di archiviazione (anteprima) | Facoltativo | Se abilitata, questa impostazione consente ai client di autorizzare le richieste all'account di archiviazione usando le chiavi di accesso dell'account o un account Azure Active Directory (Azure AD) (impostazione predefinita). La disabilitazione di questa impostazione impedisce l'autorizzazione con le chiavi di accesso dell'account. Per altre informazioni, vedere [Impedire l'autorizzazione della chiave condivisa per un account Archiviazione di Azure (anteprima).](shared-key-authorization-prevent.md) |
| Security | Versione minima TLS | Obbligatoria | Selezionare la versione minima di Transport Layer Security (TLS) per le richieste in ingresso all'account di archiviazione. Il valore predefinito è TLS versione 1.2. Se impostato sul valore predefinito, le richieste in ingresso effettuate con TLS 1.0 o TLS 1.1 vengono rifiutate. Per altre informazioni, vedere [Applicare una versione minima richiesta di Transport Layer Security (TLS) per le richieste a un account di archiviazione.](transport-layer-security-configure-minimum-version.md) |
| Data Lake Storage Gen2 | Abilitare lo spazio dei nomi gerarchico | Facoltativo | Per usare questo account di archiviazione per i Azure Data Lake Storage Gen2, configurare uno spazio dei nomi gerarchico. Per altre informazioni, vedere [Introduzione ad Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md). |
| Archiviazione BLOB | Abilitare la condivisione file di rete (NFS) v3 (anteprima) | Facoltativo | NFS v3 offre la compatibilità file system Linux su scala di archiviazione oggetti consente ai client Linux di montare un contenitore nell'archiviazione BLOB da una macchina virtuale di Azure o da un computer locale. Per altre informazioni, vedere Supporto del protocollo [NFS (Network File System) 3.0 in Archiviazione BLOB di Azure (anteprima).](../blobs/network-file-system-protocol-support.md) |
| Archiviazione BLOB | Livello di accesso | Obbligatoria | I livelli di accesso BLOB consentono di archiviare i dati BLOB nel modo più conveniente, in base all'utilizzo. Selezionare il livello frequente (impostazione predefinita) per i dati a cui si accede di frequente. Selezionare il livello ad accesso freddo per i dati a cui si accede raramente. Per altre informazioni, vedere Livelli di accesso per Archiviazione BLOB di Azure - accesso ad accesso [rapido, ad accesso rovente e archivio](../blobs/storage-blob-storage-tiers.md). |
| File di Azure | Abilitare condivisioni file di grandi dimensioni | Facoltativo | Disponibile solo per gli account di archiviazione Premium per le condivisioni file. Per altre informazioni, vedere Abilitare condivisioni file standard fino [a 100 TiB.](../files/storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) |
| Tabelle e code | Abilitare il supporto per le chiavi gestite dal cliente | Facoltativo | Per abilitare il supporto per le chiavi gestite dal cliente per tabelle e code, è necessario selezionare questa impostazione al momento della creazione dell'account di archiviazione. Per altre informazioni, vedere [Creare un account che supporti le chiavi gestite](account-encryption-key-create.md)dal cliente per tabelle e code. |

### <a name="networking-tab"></a>Scheda Rete

Nella scheda **Rete è** possibile configurare le impostazioni relative alla connettività di rete e alle preferenze di routing per il nuovo account di archiviazione. Queste opzioni possono essere configurate anche dopo la creazione dell'account di archiviazione.

La tabella seguente descrive i campi della **scheda** Rete.

| Sezione | Campo | Obbligatoria o facoltativa | Descrizione |
|--|--|--|--|
| Connettività di rete | Metodo di connettività | Obbligatoria | Per impostazione predefinita, il traffico di rete in ingresso viene instradato all'endpoint pubblico per l'account di archiviazione. È possibile specificare che il traffico deve essere instradato all'endpoint pubblico tramite una rete virtuale di Azure. È anche possibile configurare endpoint privati per l'account di archiviazione. Per altre informazioni, vedere [Usare endpoint privati per Archiviazione di Azure](storage-private-endpoints.md). |
| Routing di rete | Preferenza di routing | Obbligatoria | La preferenza di routing di rete specifica il modo in cui il traffico di rete viene instradato all'endpoint pubblico dell'account di archiviazione dai client su Internet. Per impostazione predefinita, un nuovo account di archiviazione usa il routing di rete Microsoft. È anche possibile scegliere di instradare il traffico di rete attraverso il POP più vicino all'account di archiviazione, con una possibile riduzione dei costi di rete. Per altre informazioni, vedere [Preferenza di routing di rete per Archiviazione di Azure](network-routing-preference.md). |

### <a name="data-protection-tab"></a>Scheda Protezione dati

Nella scheda **Protezione dati è** possibile configurare le opzioni di protezione dei dati dei BLOB nel nuovo account di archiviazione.  Queste opzioni possono essere configurate anche dopo la creazione dell'account di archiviazione. Per una panoramica delle opzioni di protezione dei dati in Archiviazione di Azure, vedere [Panoramica della protezione dei dati.](../blobs/data-protection-overview.md)

Nella tabella seguente vengono descritti i campi della **scheda Protezione** dati.

| Sezione | Campo | Obbligatoria o facoltativa | Descrizione |
|--|--|--|--|
| Ripristino | Abilitare il ripristino temporato per i contenitori | Facoltativo | Il ripristino temporico offre protezione da eliminazioni accidentali o danneggiamenti consentendo di ripristinare i dati BLOB in blocchi a uno stato precedente. Per altre informazioni, vedere [Ripristino temporato per BLOB in blocchi](../blobs/point-in-time-restore-overview.md).<br /><br />L'abilitazione del ripristino temporato consente anche il controllo delle versioni dei BLOB, l'eliminazione software del BLOB e il feed di modifiche BLOB. Queste funzionalità dei prerequisiti possono avere un impatto sui costi. Per altre informazioni, vedere [Prezzi e fatturazione](../blobs/point-in-time-restore-overview.md#pricing-and-billing) per il ripristino temporato. |
| Ripristino | Abilitare l'eliminazione temporanea per i BLOB | Facoltativo | L'eliminazione blob soft protegge un singolo BLOB, snapshot o versione da eliminazioni o sovrascritte accidentali mantenendo i dati eliminati nel sistema per un periodo di conservazione specificato. Durante il periodo di conservazione, è possibile ripristinare lo stato di un oggetto eliminato in modo soft al momento dell'eliminazione. Per altre informazioni, vedere [Eliminazione soft per i BLOB](../blobs/soft-delete-blob-overview.md).<br /><br />Microsoft consiglia di abilitare l'eliminazione blob soft per gli account di archiviazione e di impostare un periodo di conservazione minimo di sette giorni. |
| Ripristino | Abilitare l'eliminazione soft per i contenitori (anteprima) | Facoltativo | L'eliminazione soft del contenitore protegge un contenitore e il relativo contenuto da eliminazioni accidentali mantenendo i dati eliminati nel sistema per un periodo di conservazione specificato. Durante il periodo di conservazione, è possibile ripristinare lo stato di un contenitore eliminato in modo soft al momento dell'eliminazione. Per altre informazioni, vedere [Eliminazione soft per i contenitori (anteprima).](../blobs/soft-delete-container-overview.md)<br /><br />Microsoft consiglia di abilitare l'eliminazione soft del contenitore per gli account di archiviazione e di impostare un periodo di conservazione minimo di sette giorni. |
| Ripristino | Abilitare l'eliminazione soft per le condivisioni file | Facoltativo | L'eliminazione soft per le condivisioni file protegge una condivisione file e il relativo contenuto da eliminazioni accidentali mantenendo i dati eliminati nel sistema per un periodo di conservazione specificato. Durante il periodo di conservazione, è possibile ripristinare lo stato di una condivisione file eliminata definitivamente al momento dell'eliminazione. Per altre informazioni, vedere Impedire [l'eliminazione accidentale di condivisioni file di Azure.](../files/storage-files-prevent-file-share-deletion.md)<br /><br />Microsoft consiglia di abilitare l'eliminazione soft per le condivisioni file File di Azure carichi di lavoro e di impostare un periodo di conservazione minimo di sette giorni. |
| Rilevamento | Abilitare il controllo delle versioni per i BLOB | Facoltativo | Il controllo delle versioni dei BLOB salva automaticamente lo stato di un BLOB in una versione precedente quando il BLOB viene sovrascritto. Per altre informazioni, vedere Controllo [delle versioni dei BLOB.](../blobs/versioning-overview.md)<br /><br />Microsoft consiglia di abilitare il controllo delle versioni dei BLOB per una protezione dei dati ottimale per l'account di archiviazione. |
| Rilevamento | Abilitare il feed di modifiche BLOB | Facoltativo | Il feed di modifiche BLOB fornisce i log delle transazioni di tutte le modifiche a tutti i BLOB nell'account di archiviazione, nonché ai relativi metadati. Per altre informazioni, vedere [Supporto dei feed di modifiche in Archiviazione BLOB di Azure](../blobs/storage-blob-change-feed.md). |

### <a name="tags-tab"></a>Scheda Tag

Nella scheda **Tag** è possibile specificare i tag Resource Manager per organizzare le risorse di Azure. Per altre informazioni, vedere [Assegnare tag a risorse, gruppi di risorse e sottoscrizioni per l'organizzazione logica.](../../azure-resource-manager/management/tag-resources.md)

### <a name="review--create-tab"></a>Scheda Rivedi e crea

Quando si passa alla scheda **Rivedi e crea,** Azure esegue la convalida delle impostazioni dell'account di archiviazione scelte. Se la convalida ha esito positivo, è possibile procedere alla creazione dell'account di archiviazione.

Se la convalida non riesce, il portale indica quali impostazioni devono essere modificate.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per creare un account di archiviazione per utilizzo generico v2 con PowerShell, creare prima di tutto un nuovo gruppo di risorse chiamando il [comando New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

```azurepowershell-interactive
$resourceGroup = "<resource-group>"
$location = "<location>"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

In caso di dubbi su quale area specificare per il parametro `-Location`, è possibile recuperare un elenco di aree supportate per la sottoscrizione con il comando [Get-AzLocation](/powershell/module/az.resources/get-azlocation):

```azurepowershell-interactive
Get-AzLocation | select Location
```

Creare quindi un account di archiviazione standard per utilizzo generico v2 con archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) usando il [comando New-AzStorageAccount.](/powershell/module/az.storage/new-azstorageaccount) Tenere presente che il nome dell'account di archiviazione deve essere univoco in Azure, quindi sostituire il valore segnaposto tra parentesi quadre con il proprio valore univoco:

```azurepowershell-interactive
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

Per abilitare uno spazio dei nomi gerarchico per l'account di archiviazione Azure Data Lake Storage [,](https://azure.microsoft.com/services/storage/data-lake-storage/)includere il parametro nella chiamata al `-EnableHierarchicalNamespace $True` comando **New-AzStorageAccount.**

La tabella seguente mostra i valori da usare per i parametri e per creare un particolare tipo di account di `-SkuName` archiviazione con la configurazione di `-Kind` ridondanza desiderata.

| Tipo di account di archiviazione | Configurazioni di ridondanza supportate | Valore per il parametro -Kind | Valori possibili per il parametro -SkuName | Supporta lo spazio dei nomi gerarchico |
|--|--|--|--|--|
| Standard per utilizzo generico v2 | LRS/GRS/RA-GRS/ZRS/GZRS/RA-GZRS | StorageV2 | Standard_LRS/Standard_GRS/Standard_RAGRS/Standard_ZRS/Standard_GZRS/Standard_RAGZRS | Sì |
| BLOB in blocchi Premium | LRS/ZRS | BlockBlobStorage | Premium_LRS/Premium_ZRS | Sì |
| Condivisioni file Premium | LRS/ZRS | FileStorage | Premium_LRS/Premium_ZRS | No |
| BLOB di pagine Premium | LRS | StorageV2 | Premium_LRS | No |
| Versione 1 per utilizzo generico standard legacy | LRS/GRS/RA-GRS | Archiviazione | Standard_LRS/Standard_GRS/Standard_RAGRS | No |
| Archiviazione BLOB legacy | LRS/GRS/RA-GRS | BlobStorage | Standard_LRS/Standard_GRS/Standard_RAGRS | No |

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per creare un account di archiviazione per utilizzo generico v2 con l'interfaccia della riga di comando di Azure, creare prima un nuovo gruppo di risorse chiamando il [comando az group create.](/cli/azure/group#az_group_create)

```azurecli-interactive
az group create \
  --name storage-resource-group \
  --location westus
```

In caso di dubbi su quale area specificare per il parametro `--location`, è possibile recuperare un elenco di aree supportate per la sottoscrizione con il comando [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
  --query "[].{Region:name}" \
  --out table
```

Creare quindi un account di archiviazione standard per utilizzo generico v2 con archiviazione con ridondanza geografica e accesso in lettura usando il [comando az storage account create.](/cli/azure/storage/account#az_storage_account_create) Tenere presente che il nome dell'account di archiviazione deve essere univoco in Azure, quindi sostituire il valore segnaposto tra parentesi quadre con il proprio valore univoco:

```azurecli-interactive
az storage account create \
  --name <account-name> \
  --resource-group storage-resource-group \
  --location westus \
  --sku Standard_RAGRS \
  --kind StorageV2
```

Per abilitare uno spazio dei nomi gerarchico per l'account di archiviazione Azure Data Lake Storage [,](https://azure.microsoft.com/services/storage/data-lake-storage/)includere il parametro nella chiamata `--enable-hierarchical-namespace true` al comando **az storage account create.** La creazione di uno spazio dei nomi gerarchico richiede l'interfaccia della riga di comando di Azure versione 2.0.79 o successiva.

La tabella seguente mostra i valori da usare per i parametri e per creare un particolare tipo di account di `-sku` archiviazione con la configurazione di `-kind` ridondanza desiderata.

| Tipo di account di archiviazione | Configurazioni di ridondanza supportate | Valore per il parametro -kind | Valori possibili per il parametro -sku | Supporta lo spazio dei nomi gerarchico |
|--|--|--|--|--|
| Standard per utilizzo generico v2 | LRS/GRS/RA-GRS/ZRS/GZRS/RA-GZRS | StorageV2 | Standard_LRS/Standard_GRS/Standard_RAGRS/Standard_ZRS/Standard_GZRS/Standard_RAGZRS | Sì |
| BLOB in blocchi Premium | LRS/ZRS | BlockBlobStorage | Premium_LRS/Premium_ZRS | Sì |
| Condivisioni file Premium | LRS/ZRS | FileStorage | Premium_LRS/Premium_ZRS | No |
| BLOB di pagine Premium | LRS | StorageV2 | Premium_LRS | No |
| Standard legacy per utilizzo generico v1 | LRS/GRS/RA-GRS | Archiviazione | Standard_LRS/Standard_GRS/Standard_RAGRS | No |
| Archiviazione BLOB legacy | LRS/GRS/RA-GRS | BlobStorage | Standard_LRS/Standard_GRS/Standard_RAGRS | No |

# <a name="template"></a>[Modello](#tab/template)

È possibile usare l'interfaccia Azure PowerShell o l'interfaccia della riga di comando di Azure per distribuire un modello Resource Manager creare un account di archiviazione. Il modello usato in questo articolo è basato Azure Resource Manager [di avvio rapido.](https://azure.microsoft.com/resources/templates/101-storage-account-create/) Per eseguire gli script, selezionare **Prova per** aprire il Azure Cloud Shell. Per incollare lo script, fare clic con il pulsante destro del mouse nella shell e quindi scegliere **Incolla**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> Questo modello serve solo come esempio. Esistono molte impostazioni dell'account di archiviazione che non sono configurate come parte di questo modello. Ad esempio, se si vuole usare [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), è necessario modificare questo modello impostando la `isHnsEnabledad` proprietà `StorageAccountPropertiesCreateParameters` dell'oggetto su `true` .

Per informazioni su come modificare questo modello o crearne di nuovi, vedere:

- [Azure Resource Manager documentazione .](../../azure-resource-manager/index.yml)
- [Storage account template reference](/azure/templates/microsoft.storage/allversions)(Riferimento sul modello di account di archiviazione).
- [Additional storage account template samples](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)(Altri esempi di modelli di account di archiviazione).

---

## <a name="delete-a-storage-account"></a>Eliminare un account di archiviazione

L'eliminazione di un account di archiviazione elimina l'intero account, inclusi tutti i dati nell'account, e non può essere annullata.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Passare all'account di archiviazione nel [portale di Azure](https://portal.azure.com).
1. Fare clic su **Elimina**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per eliminare l'account di archiviazione, [usare il comando Remove-AzStorageAccount:](/powershell/module/az.storage/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eliminare l'account di archiviazione, usare il [comando az storage account delete:](/cli/azure/storage/account#az_storage_account_delete)

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Modello](#tab/template)

Per eliminare l'account di archiviazione, usare Azure PowerShell o l'interfaccia della riga di comando di Azure.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

In alternativa, è possibile eliminare il gruppo di risorse, che elimina l'account di archiviazione e qualsiasi altra risorsa in tale gruppo di risorse. Per altre informazioni sull'eliminazione di un gruppo di risorse, vedere [Eliminare il gruppo di risorse e le risorse](../../azure-resource-manager/management/delete-resource-group.md).

> [!WARNING]
> Non è possibile ripristinare un account di archiviazione eliminato, né recuperare gli elementi che conteneva prima dell'eliminazione. Assicurarsi di eseguire il backup di tutti gli elementi da salvare prima di eliminare l'account. Lo stesso vale per tutte le risorse nell'account: dopo aver eliminato un BLOB, una tabella, una coda o un file, non è più possibile recuperarlo.
>
> Se si prova a eliminare un account di archiviazione associato a una macchina virtuale di Azure, potrebbe essere visualizzato un errore che informa che l'account di archiviazione è ancora in uso. Per informazioni sulla risoluzione di questo errore, vedere [Risolvere gli errori durante l'eliminazione degli account di archiviazione.](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'account di archiviazione](storage-account-overview.md)
- [Upgrade to a general-purpose v2 storage account](storage-account-upgrade.md) (Eseguire l'aggiornamento alla versione 2 di un account di archiviazione per utilizzo generico)
- [Spostare un account di archiviazione in un'altra area](storage-account-move.md)
- [consente di ripristinare un account di archiviazione eliminato](storage-account-recover.md)