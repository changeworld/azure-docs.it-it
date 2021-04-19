---
title: Montare una condivisione file NFS di Azure - File di Azure
description: Informazioni su come montare una condivisione file system di rete.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 4369619cd83dffe36cf156f523a951e1360438db
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717078"
---
# <a name="how-to-mount-an-nfs-file-share"></a>Come montare una condivisione file NFS

[File di Azure](storage-files-introduction.md) è il file system cloud facile da usare di Microsoft. Le condivisioni file di Azure possono essere montate nelle distribuzioni Linux usando il protocollo Server Message Block (SMB) o il protocollo NFS (Network File System). Questo articolo è incentrato sul montaggio con NFS. Per informazioni dettagliate sul montaggio con SMB, vedere Usare File di Azure [con Linux.](storage-how-to-use-files-linux.md) Per informazioni dettagliate su ognuno dei protocolli disponibili, vedere Protocolli [di condivisione file di Azure.](storage-files-compare-protocols.md)

## <a name="limitations"></a>Limitazioni

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Disponibilità a livello di area

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Prerequisiti

- [Creare una condivisione NFS.](storage-files-how-to-create-nfs-shares.md)

    > [!IMPORTANT]
    > Le condivisioni NFS sono accessibili solo da reti attendibili. Le connessioni alla condivisione NFS devono provenire da una delle origini seguenti:

- Usare una delle soluzioni di rete seguenti:
    - Creare [un endpoint privato](storage-files-networking-endpoints.md#create-a-private-endpoint) (scelta consigliata) o limitare [l'accesso all'endpoint pubblico.](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - [Configurare una VPN da punto a sito (P2S) in Linux per l'uso con File di Azure](storage-files-configure-p2s-vpn-linux.md).
    - [Configurare una VPN da sito a sito per l'uso con File di Azure](storage-files-configure-s2s-vpn.md).
    - Configurare [ExpressRoute.](../../expressroute/expressroute-introduction.md)

## <a name="disable-secure-transfer"></a>Disabilitare il trasferimento sicuro

1. Accedere all'portale di Azure e accedere all'account di archiviazione contenente la condivisione NFS creata.
1. Selezionare **Configurazione**.
1. Selezionare **Disabilitato per** Trasferimento sicuro **obbligatorio.**
1. Selezionare **Salva**.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Screenshot della schermata di configurazione dell'account di archiviazione con il trasferimento sicuro disabilitato.":::

## <a name="mount-an-nfs-share"></a>Montare una condivisione NFS

1. Dopo aver creato la condivisione file, selezionarla e selezionare **Connetti da Linux.**
1. Immettere il percorso di montaggio da usare, quindi copiare lo script.
1. Connettersi al client e usare lo script di montaggio fornito.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="Screenshot del pannello di connessione della condivisione file.":::

La condivisione NFS è stata montata.

### <a name="validate-connectivity"></a>Convalidare la connettività

Se il montaggio non è riuscito, è possibile che l'endpoint privato non sia stato configurato correttamente o non sia accessibile. Per informazioni dettagliate sulla verifica della connettività, vedere la sezione Verificare la [connettività](storage-files-networking-endpoints.md#verify-connectivity) dell'articolo endpoint di rete.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni File di Azure,vedere l'articolo [Planning for an File di Azure deployment (Pianificazione di](storage-files-planning.md)una distribuzione File di Azure distribuzione).
- In caso di problemi, vedere Risolvere i [problemi relativi alle condivisioni file NFS di Azure.](storage-troubleshooting-files-nfs.md)