---
title: Supporto del file System di rete 3,0 nell'archivio BLOB di Azure (anteprima) | Microsoft Docs
description: L'archiviazione BLOB supporta ora il protocollo NFS (Network File System) 3,0. Questo supporto consente ai client Linux di montare un contenitore nell'archiviazione BLOB da una macchina virtuale (VM) di Azure o da un computer in esecuzione in locale.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/19/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: b00956a6fb5a79b09602ca1752cc547595f32db6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103224595"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Supporto del protocollo NFS (Network File System) 3,0 nell'archivio BLOB di Azure (anteprima)

L'archiviazione BLOB supporta ora il protocollo NFS (Network File System) 3,0. Questo supporto fornisce la compatibilità di Linux file system alla scalabilità e ai prezzi di archiviazione degli oggetti e consente ai client Linux di montare un contenitore nell'archiviazione BLOB da una macchina virtuale (VM) di Azure o da un computer locale. 

> [!NOTE]
> Il supporto del protocollo NFS 3,0 nell'archivio BLOB di Azure è in versione di anteprima pubblica. Supporta gli account di archiviazione GPV2 con prestazioni del livello standard nelle aree seguenti: Australia orientale, Corea centrale e Stati Uniti centro-meridionali. L'anteprima supporta anche il BLOB in blocchi con livello di prestazioni Premium in tutte le aree pubbliche.

Si tratta sempre di una sfida per eseguire carichi di lavoro legacy su larga scala, ad esempio HPC (High Performance Computing) nel cloud. Un motivo è che le applicazioni spesso usano protocolli di file tradizionali, ad esempio NFS o Server Message Block (SMB) per accedere ai dati. Inoltre, i servizi di archiviazione cloud nativi si concentrano sull'archiviazione di oggetti che hanno uno spazio dei nomi flat e metadati completi anziché file System che forniscono uno spazio dei nomi gerarchico e operazioni efficienti sui metadati. 

L'archiviazione BLOB supporta ora uno spazio dei nomi gerarchico e, se combinato con il supporto del protocollo NFS 3,0, Azure rende molto più semplice eseguire le applicazioni legacy oltre all'archiviazione di oggetti cloud su larga scala. 

## <a name="applications-and-workloads-suited-for-this-feature"></a>Applicazioni e carichi di lavoro adatti per questa funzionalità

La funzionalità del protocollo NFS 3,0 è ideale per l'elaborazione di carichi di lavoro elevati di velocità effettiva elevata, scalabilità elevata, lettura, ad esempio l'elaborazione di supporti, simulazioni di rischio e sequenziazione di genomica. È consigliabile usare questa funzionalità per qualsiasi altro tipo di carico di lavoro che usa più lettori e molti thread, che richiedono una larghezza di banda elevata. 

## <a name="nfs-30-and-the-hierarchical-namespace"></a>NFS 3,0 e lo spazio dei nomi gerarchico

Il supporto del protocollo NFS 3,0 richiede che i BLOB siano organizzati in uno spazio dei nomi gerarchico. Quando si crea un account di archiviazione, è possibile abilitare uno spazio dei nomi gerarchico. La possibilità di utilizzare uno spazio dei nomi gerarchico è stata introdotta da Azure Data Lake Storage Gen2. Organizza gli oggetti (file) in una gerarchia di directory e sottodirectory nello stesso modo in cui è organizzata la file system nel computer.  Lo spazio dei nomi gerarchico viene scalato in modo lineare e non degrada la capacità o le prestazioni dei dati. Protocolli diversi si estendono dallo spazio dei nomi gerarchico. Il protocollo NFS 3,0 è uno dei protocolli disponibili.   

> [!div class="mx-imgBorder"]
> ![spazio dei nomi gerarchico](./media/network-protocol-support/hierarchical-namespace-and-nfs-support.png)
  
## <a name="data-stored-as-block-blobs"></a>Dati archiviati come BLOB in blocchi

Se si Abilita il supporto del protocollo NFS 3,0, tutti i dati nell'account di archiviazione verranno archiviati come BLOB in blocchi. I BLOB in blocchi sono ottimizzati per elaborare in modo efficiente grandi quantità di dati con intensa attività di lettura. I BLOB in blocchi sono costituiti da blocchi. Ogni blocco è identificato da un ID blocco. Un BLOB in blocchi può includere fino a 50.000 blocchi. Ogni blocco in un BLOB in blocchi può avere dimensioni diverse, fino alla dimensione massima consentita per la versione del servizio utilizzata dall'account.

Quando l'applicazione effettua una richiesta utilizzando il protocollo NFS 3,0, la richiesta viene convertita in una combinazione di operazioni BLOB in blocchi. Ad esempio, le richieste di lettura RPC (Remote Procedure Call) per NFS 3,0 vengono convertite in [Get BLOB](/rest/api/storageservices/get-blob) Operation. NFS 3,0 scrivere le richieste RPC vengono convertite in una combinazione di [Get Block List](/rest/api/storageservices/get-block-list), [Put Block](/rest/api/storageservices/put-block)e [Put Block List](/rest/api/storageservices/put-block-list).

## <a name="general-workflow-mounting-a-storage-account-container"></a>Flusso di lavoro generale: montaggio di un contenitore dell'account di archiviazione

I client Linux possono montare un contenitore nell'archiviazione BLOB da una macchina virtuale (VM) di Azure o da un computer locale. Per montare un contenitore dell'account di archiviazione, è necessario eseguire queste operazioni.

1. Registrare la funzionalità del protocollo NFS 3,0 con la sottoscrizione.

2. Verificare che la funzionalità sia registrata.

3. Creare una rete virtuale di Azure (VNet).

4. Configurare la sicurezza di rete.

5. Creare e configurare un account di archiviazione che accetti il traffico solo dal VNet.

6. Creare un contenitore nell'account di archiviazione.

7. Montare il contenitore.

Per istruzioni dettagliate, vedere [montare l'archiviazione BLOB usando il protocollo NFS (Network File System) 3,0 (anteprima)](network-file-system-protocol-support-how-to.md).

> [!IMPORTANT]
> È importante completare queste attività in ordine. Non è possibile montare i contenitori creati prima di abilitare il protocollo NFS 3,0 nell'account. Inoltre, dopo aver abilitato il protocollo NFS 3,0 nell'account, non è possibile disabilitarlo.

## <a name="network-security"></a>Sicurezza di rete

L'account di archiviazione deve essere contenuto all'interno di un VNet. Un VNet consente ai client di connettersi in modo sicuro all'account di archiviazione. L'unico modo per proteggere i dati nell'account consiste nell'usare un VNet e altre impostazioni di sicurezza di rete. Qualsiasi altro strumento utilizzato per proteggere i dati, inclusi l'autorizzazione della chiave dell'account, la sicurezza Azure Active Directory (AD) e gli elenchi di controllo di accesso (ACL), non sono ancora supportati negli account in cui è abilitato il supporto del protocollo NFS 3,0. 

Per altre informazioni, vedere [consigli sulla sicurezza di rete per l'archiviazione BLOB](security-recommendations.md#networking).

## <a name="supported-network-connections"></a>Connessioni di rete supportate

Un client può connettersi tramite un endpoint pubblico o [privato](../common/storage-private-endpoints.md)e può connettersi da uno dei seguenti percorsi di rete:

- VNet configurato per l'account di archiviazione. 

  In questo articolo si farà riferimento a VNet come *VNet primario*. Per altre informazioni, vedere [concedere l'accesso da una rete virtuale](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Un VNet con peering che si trova nella stessa area del VNet primario.

  È necessario configurare l'account di archiviazione per consentire l'accesso a questo VNet con peering. Per altre informazioni, vedere [concedere l'accesso da una rete virtuale](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Una rete locale connessa alla VNet primaria usando un gateway [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) o un [gateway ExpressRoute](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md). 

  Per altre informazioni, vedere [configurazione dell'accesso dalle reti locali](../common/storage-network-security.md#configuring-access-from-on-premises-networks).

- Una rete locale connessa a una rete con peering.

  Questa operazione può essere eseguita usando un gateway [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) o un [gateway ExpressRoute](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) insieme al [transito del gateway](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit). 

> [!IMPORTANT]
> Se ci si connette da una rete locale, assicurarsi che il client consenta la comunicazione in uscita attraverso le porte 111 e 2048. Il protocollo NFS 3,0 utilizza queste porte.

## <a name="azure-storage-features-not-yet-supported"></a>Funzionalità di archiviazione di Azure non ancora supportate

Le funzionalità di archiviazione di Azure seguenti non sono supportate quando si Abilita il protocollo NFS 3,0 per l'account. 

- Sicurezza di Azure Active Directory (AD)

- Elenchi di controllo di accesso (ACL) simili a POSIX

- Possibilità di abilitare il supporto per NFS 3,0 negli account di archiviazione esistenti

- La possibilità di disabilitare il supporto per NFS 3,0 in un account di archiviazione (dopo averla abilitata)

- Possibilità di scrivere nei BLOB usando le API REST o gli SDK. 
  
## <a name="nfs-30-features-not-yet-supported"></a>Funzionalità di NFS 3,0 non ancora supportate

Le funzionalità NFS 3,0 seguenti non sono ancora supportate.

- NFS 3,0 su UDP. È supportato solo NFS 3,0 su TCP.

- Blocco di file con Network Lock Manager (NLM). I comandi di montaggio devono includere il `-o nolock` parametro.

- Montaggio delle sottodirectory. È possibile montare solo la directory radice (contenitore).

- Elencare i montaggi (ad esempio, usando il comando `showmount -a` )

- Elenco delle esportazioni (ad esempio, tramite il comando `showmount -e` )

- Collegamento reale

- Esportazione di un contenitore come di sola lettura

## <a name="nfs-30-clients-not-yet-supported"></a>Client NFS 3,0 non ancora supportati

I client NFS 3,0 seguenti non sono ancora supportati.

- Client Windows per NFS

## <a name="pricing"></a>Prezzi

Durante la fase di anteprima, i dati archiviati nell'account di archiviazione vengono fatturati con la stessa velocità di capacità di archiviazione BLOB per GB al mese. 

Una transazione non viene addebitata durante l'anteprima. Il prezzo per le transazioni è soggetto a modifiche e verrà determinato quando sarà disponibile a livello generale.

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [montare l'archiviazione BLOB usando il protocollo NFS (Network File System) 3,0 (anteprima)](network-file-system-protocol-support-how-to.md).

- Per ottimizzare le prestazioni, vedere [considerazioni sulle prestazioni di Network File System (NFS) 3,0 nell'archivio BLOB di Azure (anteprima)](network-file-system-protocol-support-performance.md).
