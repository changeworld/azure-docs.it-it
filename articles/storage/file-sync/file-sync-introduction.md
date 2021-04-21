---
title: Introduzione alla Sincronizzazione file di Azure | Microsoft Docs
description: Panoramica di Sincronizzazione file di Azure, un servizio che consente di creare e usare condivisioni file di rete nel cloud usando il protocollo SMB standard del settore.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 04/19/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: da40783e3d299b0edf27c6d045dbc6dcfc5cf964
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797662"
---
# <a name="what-is-azure-file-sync"></a>Che cos'è Sincronizzazione file di Azure?
Sincronizzazione file di Azure consente di centralizzare le condivisioni file dell'organizzazione in File di Azure, mantenendo al tempo stesso la flessibilità, le prestazioni e la compatibilità di un file server Windows. Anche se alcuni utenti possono scegliere di mantenere una copia completa dei dati in locale, Sincronizzazione file di Azure ha anche la possibilità di trasformare Windows Server in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Si può usare qualsiasi numero di cache necessario in tutto il mondo.   

## <a name="videos"></a>Video
| Introduzione a Sincronizzazione file di Azure | File di Azure con sincronizzazione (Ignite 2019)  |
|-|-|
| [![Screencast del video di introduzione a Sincronizzazione file di Azure (fare clic per riprodurre)](../files/media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Screencast della presentazione su File di Azure e la sincronizzazione (fare clic per riprodurre)](../files/media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

## <a name="benefits-of-azure-file-sync"></a>Vantaggi dell'Sincronizzazione file di Azure

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
Con la livellistica cloud abilitata, i file a cui si accede più di frequente vengono memorizzati nella cache nel server locale e i file a cui si accede meno frequentemente vengono a livelli nel cloud. È possibile controllare la quantità di spazio su disco locale usata per la memorizzazione nella cache. I file a livelli possono essere richiamati rapidamente su richiesta, rendendo l'esperienza facile, consentendo al tempo stesso di ridurre i costi, in quanto è necessario archiviare solo una frazione dei dati in locale. Per altre informazioni sulla creazione di livelli cloud, vedere [Panoramica del cloud a livelli.](file-sync-cloud-tiering-overview.md) 

### <a name="multi-site-access-and-sync"></a>Accesso e sincronizzazione multisosto
Sincronizzazione file di Azure è ideale per scenari di accesso distribuito. Per ogni ufficio, è possibile effettuare il provisioning di un'istanza locale di Windows Server come parte della Sincronizzazione file di Azure distribuzione. Le modifiche apportate a un server in un ufficio vengono sincronizzate automaticamente con i server in tutti gli altri uffici.

### <a name="business-continuity-and-disaster-recovery"></a>Continuità aziendale e ripristino di emergenza
Sincronizzazione file di Azure è supportato da File di Azure, che offre diverse opzioni di ridondanza per l'archiviazione a disponibilità elevata. Poiché Azure contiene copie resilienti dei dati, il server locale diventa un dispositivo di memorizzazione nella cache usa e getta e il ripristino da un server non riuscito può essere eseguito aggiungendo un nuovo server alla Sincronizzazione file di Azure distribuzione. Anziché eseguire il ripristino da un backup locale, eseguire il provisioning di un altro server Windows, installare l'agente Sincronizzazione file di Azure e quindi aggiungerlo alla distribuzione Sincronizzazione file di Azure locale. Sincronizzazione file di Azure scarica lo spazio dei nomi del file prima di scaricare i dati, in modo che il server possa essere operativo il prima possibile. Per un ripristino ancora più veloce, è possibile avere un server warm stand by come parte della distribuzione oppure è possibile usare Sincronizzazione file di Azure clustering di Windows.

## <a name="cloud-side-backup"></a>Backup sul lato cloud
Ridurre la spesa per i backup locali tramite l'esecuzione di backup centralizzati nel cloud Backup di Azure. File di Azure condivisioni SMB hanno funzionalità di snapshot nativi e il processo può essere automatizzato usando Backup di Azure pianificare i backup e gestirne la conservazione. Backup di Azure si integra anche con i server locali, quindi quando si esegue il ripristino nel cloud, queste modifiche vengono scaricate automaticamente nei server Windows.  

## <a name="next-steps"></a>Passaggi successivi
* [Pianificazione per la distribuzione di Sincronizzazione file di Azure](file-sync-planning.md)
* [Panoramica del cloud a livelli](file-sync-cloud-tiering-overview.md)
* [Monitorare Sincronizzazione file di Azure](file-sync-monitoring.md)