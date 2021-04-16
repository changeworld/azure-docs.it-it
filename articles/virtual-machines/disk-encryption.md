---
title: Crittografia lato server dei dischi gestiti di Azure
description: Archiviazione di Azure protegge i dati crittografandoli nello stato inattivo prima di renderli persistenti nei cluster di archiviazione. È possibile usare le chiavi gestite dal cliente per gestire la crittografia con le proprie chiavi oppure è possibile usare chiavi gestite da Microsoft per la crittografia dei dischi gestiti.
author: roygara
ms.date: 04/15/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4607778c78b8b062b265a5754337c09c41ba83f1
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531516"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Crittografia lato server di archiviazione su disco di Azure

La maggior parte dei dischi gestiti di Azure viene crittografata con la crittografia Archiviazione di Azure, che usa la crittografia lato server (SSE) per proteggere i dati e per soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Archiviazione di Azure crittografa automaticamente i dati archiviati in dischi gestiti di Azure (sistema operativo e dischi dati) in pausa per impostazione predefinita quando vengono salvati in modo permanente nel cloud. I dischi con la crittografia nell'host abilitata, tuttavia, non vengono crittografati tramite Archiviazione di Azure. Per i dischi con la crittografia nell'host abilitata, il server che ospita la macchina virtuale fornisce la crittografia per i dati e i dati crittografati vengono Archiviazione di Azure.

I dati nei dischi gestiti di Azure sono crittografati in modo trasparente con la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a 256 bit, una delle crittografie a blocchi più solide disponibili, conforme a FIPS 140-2. Per altre informazioni sui moduli crittografici sottostanti i dischi gestiti di Azure, vedere [API Cryptography Next Generation](/windows/desktop/seccng/cng-portal)

Archiviazione di Azure crittografia non influisce sulle prestazioni dei dischi gestiti e non è previsto alcun costo aggiuntivo. Per altre informazioni sulla crittografia Archiviazione di Azure, vedere crittografia [Archiviazione di Azure.](/azure/storage/common/storage-service-encryption)

> [!NOTE]
> I dischi temporanei non sono dischi gestiti e non vengono crittografati da SSE, a meno che non si abiliti la crittografia nell'host.

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

È possibile basarsi sulle chiavi gestite dalla piattaforma per la crittografia del disco gestito oppure è possibile gestire la crittografia usando le proprie chiavi. Se si sceglie di gestire la crittografia con le proprie chiavi, è possibile specificare una *chiave gestita dal cliente* da usare per crittografare e decrittografare tutti i dati presenti nei dischi gestiti. 

Le sezioni seguenti descrivono in modo più dettagliato tutte le opzioni per la gestione delle chiavi.

### <a name="platform-managed-keys"></a>Chiavi gestite dalla piattaforma

Per impostazione predefinita, i dischi gestiti usano chiavi di crittografia gestite dalla piattaforma. Tutti i dischi gestiti, gli snapshot, le immagini e i dati scritti in dischi gestiti esistenti vengono crittografati automaticamente in locale con chiavi gestite dalla piattaforma.

### <a name="customer-managed-keys"></a>Chiavi gestite dal cliente

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Restrizioni

Per il momento, le chiavi gestite dal cliente presentano le restrizioni seguenti:

- Se questa funzionalità è abilitata per il disco, non è possibile disabilitarla.
    Per risolvere questo problema, è necessario copiare tutti i dati usando il modulo [Azure PowerShell](windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) o l'interfaccia della riga di comando di [Azure](linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)in un disco gestito completamente diverso che non usa chiavi gestite dal cliente.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>Aree supportate

Le chiavi gestite dal cliente sono disponibili in tutte le aree in cui sono disponibili i dischi gestiti.

La rotazione automatica delle chiavi è disponibile in anteprima e disponibile solo nelle aree seguenti:

- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti centro-meridionali
- Stati Uniti occidentali
- Stati Uniti occidentali 2
- Europa settentrionale
- Europa occidentale
- Francia centrale

> [!IMPORTANT]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure Active Directory (Azure AD). Quando si configurano le chiavi gestite dal cliente, un'identità gestita viene assegnata automaticamente alle risorse dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o il disco gestito da una directory Azure AD a un'altra, l'identità gestita associata ai dischi gestiti non viene trasferita al nuovo tenant, pertanto le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere [Trasferimento di una sottoscrizione tra directory di Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

Per abilitare le chiavi gestite dal cliente per i dischi gestiti, vedere gli articoli che illustrano come abilitarlo con il modulo [Azure PowerShell](windows/disks-enable-customer-managed-keys-powershell.md), l'interfaccia della riga di comando di [Azure](linux/disks-enable-customer-managed-keys-cli.md) o il [portale di Azure](disks-enable-customer-managed-keys-portal.md). Per informazioni su come abilitare le chiavi gestite dal cliente con la rotazione automatica delle chiavi, vedere Configurare un Azure Key Vault e [DiskEncryptionSet](windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-with-automatic-key-rotation-preview)con rotazione automatica delle chiavi (anteprima).

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Crittografia nell'host: crittografia end-to-end per i dati della macchina virtuale

Quando si abilita la crittografia nell'host, la crittografia viene avviata nell'host vm stesso, il server di Azure a cui è allocata la macchina virtuale. I dati per il disco temporaneo e le cache del disco del sistema operativo/dati vengono archiviati in tale host vm. Dopo aver abilitato la crittografia nell'host, tutti questi dati vengono crittografati in stato di inquieto e vengono crittografati nel servizio di archiviazione, dove vengono resi persistenti. In sostanza, la crittografia nell'host crittografa i dati end-to-end. La crittografia nell'host non usa la CPU della macchina virtuale e non influisce sulle prestazioni della macchina virtuale. 

Quando si abilita la crittografia end-to-end, i dischi temporanei e i dischi del sistema operativo temporanei vengono crittografati quando sono in pausa con chiavi gestite dalla piattaforma. Le cache del sistema operativo e del disco dati vengono crittografate quando sono in stato in pausa con chiavi gestite dal cliente o gestite dalla piattaforma, a seconda del tipo di crittografia del disco selezionato. Ad esempio, se un disco è crittografato con chiavi gestite dal cliente, la cache per il disco viene crittografata con chiavi gestite dal cliente e, se un disco è crittografato con chiavi gestite dalla piattaforma, la cache per il disco viene crittografata con chiavi gestite dalla piattaforma.

### <a name="restrictions"></a>Restrizioni

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-vm-sizes"></a>Dimensioni delle macchine virtuali supportate

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

È anche possibile trovare le dimensioni delle macchine virtuali a livello di codice. Per informazioni su come recuperarle a livello di codice, vedere la sezione relativa alla ricerca delle dimensioni di macchina virtuale supportate nell'articolo Azure PowerShell [o](windows/disks-enable-host-based-encryption-powershell.md#finding-supported-vm-sizes) dell'interfaccia della riga di comando [di Azure.](linux/disks-enable-host-based-encryption-cli.md#finding-supported-vm-sizes)

Per abilitare la crittografia end-to-end usando la crittografia nell'host, vedere gli articoli che illustrano come abilitarla con il modulo [Azure PowerShell](windows/disks-enable-host-based-encryption-powershell.md), l'interfaccia della riga di comando di [Azure](linux/disks-enable-host-based-encryption-cli.md)o il [portale di Azure](disks-enable-host-based-encryption-portal.md).

## <a name="double-encryption-at-rest"></a>Crittografia doppia dei dati in stato di inquieto

I clienti con esigenze di sicurezza elevate che sono interessati dal rischio associato a un particolare algoritmo di crittografia, implementazione o chiave compromessa possono ora scegliere un livello aggiuntivo di crittografia usando un algoritmo o una modalità di crittografia diversi a livello di infrastruttura usando chiavi di crittografia gestite dalla piattaforma. Questo nuovo livello può essere applicato a dischi di dati, snapshot e immagini del sistema operativo e del sistema operativo persistenti, che verranno tutti crittografati con la crittografia doppia.

### <a name="supported-regions"></a>Aree supportate

La crittografia doppia è disponibile in tutte le aree in cui sono disponibili dischi gestiti.

Per abilitare la crittografia doppia dei dati in pausa per i dischi gestiti, vedere gli articoli che illustrano come abilitarla con il modulo [Azure PowerShell](windows/disks-enable-double-encryption-at-rest-powershell.md), l'interfaccia della riga di comando di [Azure](linux/disks-enable-double-encryption-at-rest-cli.md) o il [portale di Azure](disks-enable-double-encryption-at-rest-portal.md).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Crittografia lato server e Crittografia dischi di Azure

[Crittografia dischi di Azure](../security/fundamentals/azure-disk-encryption-vms-vmss.md) usa la funzionalità [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux o la funzionalità [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview) di Windows per crittografare i dischi gestiti con chiavi gestite dal cliente all'interno della macchina virtuale guest.  La crittografia lato server con chiavi gestite dal cliente migliora la funzionalità Crittografia dischi di Azure poiché consente di usare qualsiasi tipo e immagine di sistema operativo per le macchine virtuali crittografando i dati nel servizio di archiviazione.
> [!IMPORTANT]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure Active Directory (Azure AD). Quando si configurano le chiavi gestite dal cliente, un'identità gestita viene assegnata automaticamente alle risorse dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o il disco gestito da una directory di Azure AD a un'altra, l'identità gestita associata ai dischi gestiti non viene trasferita al nuovo tenant, quindi le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere [Trasferimento di una sottoscrizione tra directory di Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Passaggi successivi

- Abilitare la crittografia end-to-end usando la crittografia nell'host con il modulo [Azure PowerShell](windows/disks-enable-host-based-encryption-powershell.md), l'interfaccia della riga di comando di [Azure](linux/disks-enable-host-based-encryption-cli.md) [o](disks-enable-host-based-encryption-portal.md)portale di Azure .
- Abilitare la crittografia doppia dei dati in pausa per i dischi gestiti con il modulo [Azure PowerShell](windows/disks-enable-double-encryption-at-rest-powershell.md), l'interfaccia della riga di comando di [Azure](linux/disks-enable-double-encryption-at-rest-cli.md) [o](disks-enable-double-encryption-at-rest-portal.md)portale di Azure .
- Abilitare le chiavi gestite dal cliente per i dischi gestiti con il modulo [Azure PowerShell](windows/disks-enable-customer-managed-keys-powershell.md), l'interfaccia della riga di comando di [Azure](linux/disks-enable-customer-managed-keys-cli.md) [o](disks-enable-customer-managed-keys-portal.md)portale di Azure .
- [Esplorare i modelli di Azure Resource Manager per la creazione di dischi crittografati con chiavi gestite dal cliente](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Cos'è l'insieme di credenziali chiave di Azure?](../key-vault/general/overview.md)
