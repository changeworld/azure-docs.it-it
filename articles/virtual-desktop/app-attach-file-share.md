---
title: Desktop virtuale Windows configurare la condivisione file MSIX app di connessione anteprima-Azure
description: Come configurare una condivisione file per la connessione all'app MSIX per desktop virtuale di Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 49a350b77958901aae5e54e82d856e4f3772702e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97930787"
---
# <a name="set-up-a-file-share-for-msix-app-attach-preview"></a>Configurare una condivisione file per la connessione all'app MSIX (anteprima)

> [!IMPORTANT]
> La connessione all'app MSIX è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tutte le immagini MSIX devono essere archiviate in una condivisione di rete a cui possono accedere gli utenti in un pool di host con autorizzazioni di sola lettura.

La connessione all'app MSIX (anteprima) non presenta dipendenze dal tipo di infrastruttura di archiviazione usato dalla condivisione file. Le considerazioni per la condivisione di associazione dell'app MSIX sono le stesse di quelle per una condivisione FSLogix. Per altre informazioni sui requisiti di archiviazione, vedere [Opzioni di archiviazione per i contenitori del profilo FSLogix in desktop virtuale di Windows](store-fslogix-profile.md).

## <a name="performance-requirements"></a>Requisiti per le prestazioni

I limiti delle dimensioni dell'immagine di alconnessione app MSIX per il sistema dipendono dal tipo di archiviazione usato per archiviare i file VHD o VHDx, nonché dalle limitazioni delle dimensioni dei file VHD, VHSD o CIM e del file system.

La tabella seguente riporta un esempio del numero di risorse per una singola immagine MSIX da 1 GB con un'app MSIX necessaria per ogni macchina virtuale:

| Risorsa             | Requisiti |
|----------------------|--------------|
| IOPs a stato stazionario    | 1 IOPs       |
| Accesso all'avvio del computer | 10 IOPs      |
| Latenza              | 400 ms       |

I requisiti possono variare notevolmente a seconda del numero di applicazioni in pacchetto MSIX memorizzate nell'immagine di MSIX. Per le immagini MSIX più grandi, è necessario allocare una maggiore larghezza di banda.

### <a name="storage-recommendations"></a>Indicazioni sull'archiviazione

Azure offre più opzioni di archiviazione che possono essere usate per la connessione all'app MISX. È consigliabile usare File di Azure o Azure NetApp Files perché queste opzioni offrono il miglior valore tra costi e overhead di gestione. L'articolo [Opzioni di archiviazione per i contenitori di profili FSLogix nel desktop virtuale di Windows](store-fslogix-profile.md) Confronta le diverse soluzioni di archiviazione gestite offerte da Azure nel contesto di desktop virtuale di Windows.

### <a name="optimize-msix-app-attach-performance"></a>Ottimizzare le prestazioni di connessione dell'app MSIX

Di seguito sono riportate alcune altre operazioni consigliate per ottimizzare le prestazioni di alMSIXzione dell'app:

- La soluzione di archiviazione usata per la connessione all'app MSIX deve trovarsi nella stessa posizione del Data Center degli host della sessione.
- Per evitare colli di bottiglia delle prestazioni, escludere i file VHD, VHDX e CIM seguenti dalle analisi antivirus:
   
    - <MSIXAppAttachFileShare \> \* . VHD
    - <MSIXAppAttachFileShare \> \* . VHDX
    - \\\\\\condivisione StorageAccount.file.Core.Windows.NET \* \* . VHD
    - \\\\\\condivisione StorageAccount.file.Core.Windows.NET \* \* . VHDX
    - <MSIXAppAttachFileShare>. CIM
    - \\\\\\condivisione StorageAccount.file.Core.Windows.NET \* \* . CIM

- Separare l'infrastruttura di archiviazione per la connessione dell'app MSIX dai contenitori del profilo FSLogix.
- Per accedere alla condivisione file, tutti gli account di sistema VM e gli account utente devono disporre di autorizzazioni di sola lettura.
- Eventuali piani di ripristino di emergenza per desktop virtuale di Windows devono includere la replica della condivisione file MSIX app allegare nel percorso di failover secondario. Per altre informazioni sul ripristino di emergenza, vedere [configurare un piano di continuità aziendale e ripristino di emergenza](disaster-recovery.md).

## <a name="how-to-set-up-the-file-share"></a>Come configurare la condivisione file

Il processo di installazione per la condivisione file di MSIX app è in gran parte uguale al [processo di installazione per le condivisioni file del profilo FSLogix](create-host-pools-user-profile.md). Tuttavia, è necessario assegnare agli utenti autorizzazioni diverse. Il collegamento dell'app MSIX richiede autorizzazioni di sola lettura per accedere alla condivisione file.

Se si archiviano le applicazioni MSIX in File di Azure, per gli host di sessione è necessario assegnare tutte le VM host della sessione sia il controllo degli accessi in base al ruolo dell'account di archiviazione (RBAC) sia le autorizzazioni NTFS (New Technology File System) per la condivisione.

| Oggetto di Azure                      | Ruolo obbligatorio                                     | Role (funzione)                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| Host sessione (oggetti computer macchina virtuale)| Collaboratore per la condivisione SMB di dati per file di archiviazione          | Lettura ed esecuzione, lettura, visualizzazione contenuto cartella  |
| Amministratori nella condivisione file              | Collaboratore con privilegi elevati per la condivisione SMB di dati per file di archiviazione | Controllo completo                                  |
| Utenti nella condivisione file               | Collaboratore per la condivisione SMB di dati per file di archiviazione          | Lettura ed esecuzione, lettura, visualizzazione contenuto cartella  |

Per assegnare le autorizzazioni VM host sessione per l'account di archiviazione e la condivisione file:

1. Creare un gruppo di sicurezza Active Directory Domain Services (AD DS).

2. Aggiungere gli account computer per tutte le macchine virtuali host sessione come membri del gruppo.

3. Sincronizzare il gruppo servizi di dominio Active Directory con Azure Active Directory (Azure AD).

4. Creare un account di archiviazione.

5. Creare una condivisione file nell'account di archiviazione seguendo le istruzioni riportate in [creare una condivisione file di Azure](../storage/files/storage-how-to-create-file-share.md#create-file-share).

6. Aggiungere l'account di archiviazione a servizi di dominio Active Directory seguendo le istruzioni riportate nella [parte 1: abilitare l'autenticazione di Active Directory per le condivisioni file di Azure](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module).

7. Assegnare il gruppo di servizi di dominio Active Directory sincronizzato a Azure AD e assegnare l'account di archiviazione al ruolo Collaboratore condivisione SMB dei dati del file di archiviazione.

8. Montare la condivisione file in qualsiasi host sessione seguendo le istruzioni riportate nella [parte 2: assegnare autorizzazioni a livello di condivisione a un'identità](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).

9. Concedere le autorizzazioni NTFS per la condivisione file al gruppo servizi di dominio Active Directory.

10. Configurare le autorizzazioni NTFS per gli account utente. È necessaria un'unità operativa (OU) originata da servizi di dominio Active Directory a cui appartengono gli account della VM.

Dopo aver assegnato l'identità alla risorsa di archiviazione, seguire le istruzioni riportate negli articoli della [procedura successiva](#next-steps) per concedere altre autorizzazioni necessarie all'identità assegnata alle macchine virtuali.

È anche necessario assicurarsi che le macchine virtuali host della sessione dispongano di nuove autorizzazioni NTFS (Technology File System). È necessario disporre di un contenitore di unità operative originato da Active Directory Domain Services (AD DS) e gli utenti devono essere membri di tale unità operativa per usare tali autorizzazioni.

## <a name="next-steps"></a>Passaggi successivi

Di seguito sono riportate le altre operazioni che è necessario eseguire dopo aver configurato la condivisione file:

- Informazioni su come configurare Azure Active Directory Domain Services (AD DS) in [creare un contenitore di profili con file di Azure e servizi di dominio Active Directory](create-file-share.md).
- Per informazioni su come configurare File di Azure e Azure AD DS, vedere [creare un contenitore di profili con file di Azure e Azure AD DS](create-profile-container-adds.md).
- Informazioni su come configurare Azure NetApp Files per la connessione all'app MSIX in [creare un contenitore di profili con Azure NetApp files e servizi di dominio Active Directory](create-fslogix-profile-container.md).
- Per informazioni su come usare una condivisione file basata su macchina virtuale, vedere [creare un contenitore di profili per un pool host usando una condivisione file](create-host-pools-user-profile.md).

Al termine, di seguito sono riportate alcune altre risorse che possono risultare utili:

- Porre le domande della community su questa funzionalità nel [desktop virtuale di Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).
- È anche possibile lasciare feedback per Desktop virtuale Windows nell'[hub di commenti e suggerimenti per Desktop virtuale Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
- [Glossario per il fissaggio dell'app MSIX](app-attach-glossary.md)
- [Domande frequenti sull'aggiunta di app MSIX](app-attach-faq.md)
