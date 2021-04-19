---
title: Desktop virtuale Windows configura la condivisione file montaggio app MSIX - Azure
description: Come configurare una condivisione file per montaggio app MSIX desktop virtuale Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a2d4ebee02d85d10d5db8ec2de0bb1be334770dc
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717658"
---
# <a name="set-up-a-file-share-for-msix-app-attach"></a>Configurare una condivisione file per montaggio app MSIX

Tutte le immagini MSIX devono essere archiviate in una condivisione di rete accessibile agli utenti in un pool host con autorizzazioni di sola lettura.

montaggio app MSIX non ha dipendenze dal tipo di infrastruttura di archiviazione utilizzata dalla condivisione file. Le considerazioni per la montaggio app MSIX condivisione sono uguali a quelle per una condivisione FSLogix. Per altre informazioni sui requisiti di archiviazione, vedere Opzioni di archiviazione per i contenitori del profilo [FSLogix in Desktop virtuale Windows.](store-fslogix-profile.md)

## <a name="performance-requirements"></a>Requisiti per le prestazioni

montaggio app MSIX limiti delle dimensioni delle immagini per il sistema dipendono dal tipo di archiviazione in uso per archiviare i file VHD o VHDx, nonché dalle limitazioni delle dimensioni dei file VHD, VHSD o CIM e file system.

La tabella seguente fornisce un esempio del numero di risorse richieste da una singola immagine MSIX da 1 GB con un'app MSIX al suo interno per ogni macchina virtuale:

| Risorsa             | Requisiti |
|----------------------|--------------|
| Operazioni di I/O al secondo con stato fisso    | 1 I/O al secondo       |
| Accesso all'avvio del computer | 10 operazioni di I/O al secondo      |
| Latenza              | 400 ms       |

I requisiti possono variare notevolmente a seconda del numero di applicazioni in pacchetto MSIX archiviate nell'immagine MSIX. Per immagini MSIX di dimensioni maggiori, è necessario allocare più larghezza di banda.

### <a name="storage-recommendations"></a>Indicazioni sull'archiviazione

Azure offre più opzioni di archiviazione che possono essere usate per misx montaggio app. È consigliabile usare File di Azure o Azure NetApp Files poiché queste opzioni offrono il valore migliore tra costi e costi generali di gestione. L'articolo Opzioni di archiviazione per i contenitori del profilo [FSLogix in Desktop](store-fslogix-profile.md) virtuale Windows confronta le diverse soluzioni di archiviazione gestite offerte da Azure nel contesto di Desktop virtuale Windows.

### <a name="optimize-msix-app-attach-performance"></a>Ottimizzare montaggio app MSIX prestazioni

Di seguito sono riportati altri aspetti che è consigliabile eseguire per ottimizzare montaggio app MSIX prestazioni:

- La soluzione di archiviazione che si usa per montaggio app MSIX deve essere nella stessa posizione del data center degli host della sessione.
- Per evitare colli di bottiglia delle prestazioni, escludere i file VHD, VHDX e CIM seguenti dalle analisi antivirus:
   
    - <MSIXAppAttachFileShare \> \* . Vhd
    - <MSIXAppAttachFileShare \> \* . VHDX
    - \\\\storageaccount.file.core.windows.net condividere \\ \* \* . Vhd
    - \\\\storageaccount.file.core.windows.net condividere \\ \* \* . VHDX
    - <MSIXAppAttachFileShare>. Cim
    - \\\\storageaccount.file.core.windows.net condividere \\ \* \* . Cim

- Separare l'infrastruttura di archiviazione montaggio app MSIX contenitori di profili FSLogix.
- Tutti gli account di sistema delle macchine virtuali e gli account utente devono avere autorizzazioni di sola lettura per accedere alla condivisione file.
- Tutti i piani di ripristino di emergenza per Desktop virtuale Windows devono includere la replica montaggio app MSIX condivisione file nel percorso di failover secondario. Per altre informazioni sul ripristino di emergenza, vedere Configurare un piano di [continuità aziendale e ripristino di emergenza.](disaster-recovery.md)

## <a name="how-to-set-up-the-file-share"></a>Come configurare la condivisione file

Il processo di installazione montaggio app MSIX condivisione file è in gran parte uguale al processo di installazione per le condivisioni file di profilo [FSLogix](create-host-pools-user-profile.md). Tuttavia, è necessario assegnare agli utenti autorizzazioni diverse. montaggio app MSIX necessarie autorizzazioni di sola lettura per accedere alla condivisione file.

Se si archiviano le applicazioni MSIX in File di Azure, per gli host sessione sarà necessario assegnare a tutte le macchine virtuali host sessione sia il controllo degli accessi in base al ruolo dell'account di archiviazione che le autorizzazioni NTFS (New Technology File System) per la condivisione.

| Oggetto di Azure                      | Ruolo obbligatorio                                     | Funzione Role                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| Host sessione (oggetti computer vm)| Collaboratore per la condivisione SMB di dati per file di archiviazione          | Lettura ed esecuzione, lettura, elencare il contenuto della cartella  |
| Amministratori in Condivisione file              | Collaboratore con privilegi elevati per la condivisione SMB di dati per file di archiviazione | Controllo completo                                  |
| Utenti nella condivisione file               | Collaboratore per la condivisione SMB di dati per file di archiviazione          | Lettura ed esecuzione, lettura, elencare il contenuto della cartella  |

Per assegnare le autorizzazioni per le macchine virtuali dell'host di sessione per l'account di archiviazione e la condivisione file:

1. Creare un Active Directory Domain Services di sicurezza di Active Directory.

2. Aggiungere gli account computer per tutte le macchine virtuali host di sessione come membri del gruppo.

3. Sincronizzare il gruppo di Servizi di dominio Active Directory Azure Active Directory (Azure AD).

4. Creare un account di archiviazione.

5. Creare una condivisione file nell'account di archiviazione seguendo le istruzioni riportate in [Creare una condivisione file di Azure](../storage/files/storage-how-to-create-file-share.md#create-a-file-share).

6. Aggiungere l'account di archiviazione a Servizi di dominio Active Directory seguendo le istruzioni riportate nella prima parte: abilitare l'autenticazione di Servizi di dominio Active Directory [per le condivisioni file di Azure.](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module)

7. Assegnare il gruppo di Servizi di dominio Active Directory sincronizzato Azure AD e assegnare all'account di archiviazione il ruolo Collaboratore condivisione SMB dati file di archiviazione.

8. Montare la condivisione file in qualsiasi host di sessione seguendo le istruzioni riportate nella seconda parte: assegnare autorizzazioni a livello di [condivisione a un'identità](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).

9. Concedere autorizzazioni NTFS per la condivisione file al gruppo servizi di dominio Active Directory.

10. Configurare le autorizzazioni NTFS per gli account utente. Sarà necessaria un'unità operativa (OU) di origine da Servizi di dominio Active Directory a cui appartengono gli account nella macchina virtuale.

Dopo aver assegnato l'identità all'archiviazione, seguire le istruzioni negli articoli [in](#next-steps) Passaggi successivi per concedere altre autorizzazioni necessarie all'identità assegnata alle macchine virtuali.

È anche necessario assicurarsi che le macchine virtuali host della sessione abbiano le autorizzazioni New Technology File System (NTFS). Per usare queste autorizzazioni, è necessario disporre di un contenitore di unità operative provenienti da Active Directory Domain Services (AD DS) e gli utenti devono essere membri di tale unità operativa.

## <a name="next-steps"></a>Passaggi successivi

Ecco le altre operazioni da eseguire dopo aver configurato la condivisione file:

- Per informazioni su come configurare Azure Active Directory Domain Services (AD DS), vedere Creare un contenitore di profili con File di Azure [e Servizi di dominio Active Directory.](create-file-share.md)
- Per informazioni su come configurare File di Azure e Azure AD DS, vedere Creare [un contenitore](create-profile-container-adds.md)di profili con File di Azure e Azure AD DS .
- Per informazioni su come configurare le Azure NetApp Files per montaggio app MSIX, vedere Creare un contenitore di profili [con Azure NetApp Files e Servizi di dominio Active Directory.](create-fslogix-profile-container.md)
- Per informazioni su come usare una condivisione file basata su macchina virtuale, vedere Creare un contenitore di profili per un [pool di host usando una condivisione file.](create-host-pools-user-profile.md)

Al termine, ecco alcune altre risorse che possono risultare utili:

- Porre le domande della community su questa funzionalità nella [techCommunity di Desktop virtuale Windows.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- È anche possibile lasciare feedback per Desktop virtuale Windows nell'[hub di commenti e suggerimenti per Desktop virtuale Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
- [montaggio app MSIX glossario](app-attach-glossary.md)
- [montaggio app MSIX domande frequenti](app-attach-faq.md)
