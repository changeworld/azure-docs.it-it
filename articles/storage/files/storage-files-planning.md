---
title: Pianificazione per la distribuzione dei File di Azure | Microsoft Docs
description: Comprendere la pianificazione di una distribuzione di File di Azure. È possibile montare direttamente una condivisione file di Azure o memorizzare nella cache una condivisione file di Azure locale con Sincronizzazione file di Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 8a96b44a280e0aea15a6d0843f02f4ed16f8fcf4
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879848"
---
# <a name="planning-for-an-azure-files-deployment"></a>Pianificazione per la distribuzione dei file di Azure
[File di Azure](storage-files-introduction.md) può essere distribuito in due modi principali: montando direttamente le condivisioni file di Azure senza server o memorizzando nella cache le condivisioni file di Azure in locale usando sincronizzazione file di Azure. L'opzione di distribuzione scelta cambia gli elementi che è necessario prendere in considerazione durante la pianificazione della distribuzione. 

- **Montaggio diretto di una condivisione file di Azure**: poiché file di Azure fornisce accesso SMB (Server Message Block) o NFS (Network File System), è possibile montare condivisioni file di Azure in locale o nel cloud usando i client standard SMB o NFS disponibili nel sistema operativo. Poiché le condivisioni file di Azure sono serverless, la distribuzione per gli scenari di produzione non richiede la gestione di dispositivi NAS o file server. Ciò significa che non è necessario applicare patch software o sostituire dischi fisici. 

- **Memorizzare nella cache locale una condivisione file di Azure con Sincronizzazione file di Azure**: Sincronizzazione file di Azure consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Sincronizzazione file di Azure trasforma un server Windows locale (o cloud) in una cache rapida della condivisione file SMB di Azure. 

Questo articolo illustra principalmente le considerazioni sulla distribuzione per la distribuzione di una condivisione file di Azure da montare direttamente da un client locale o cloud. Per pianificare una distribuzione di Sincronizzazione file di Azure, vedere [pianificazione di una distribuzione di sincronizzazione file di Azure](storage-sync-files-planning.md).

## <a name="available-protocols"></a>Protocolli disponibili

File di Azure offre due protocolli che possono essere usati per il montaggio di condivisioni file, SMB e NFS (Network File System). Per informazioni dettagliate su questi protocolli, vedere [protocolli di condivisione file di Azure](storage-files-compare-protocols.md).

> [!IMPORTANT]
> La maggior parte del contenuto di questo articolo si applica solo alle condivisioni SMB. Qualsiasi elemento che si applica alle condivisioni NFS lo indica in modo specifico.

## <a name="management-concepts"></a>Concetti relativi alla gestione
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Quando si distribuiscono condivisioni file di Azure in account di archiviazione, è consigliabile:

- Distribuire solo le condivisioni file di Azure in account di archiviazione con altre condivisioni file di Azure. Sebbene gli account di archiviazione GPv2 consentano di disporre di account di archiviazione per utilizzo misto, poiché le risorse di archiviazione come le condivisioni file di Azure e i contenitori BLOB condividono i limiti dell'account di archiviazione, la combinazione di risorse può rendere più difficile la risoluzione dei problemi di prestazioni in un secondo momento. 

- Quando si distribuiscono condivisioni file di Azure, prestare attenzione alle limitazioni di un account di archiviazione in termini di operazioni di I/O al secondo. Idealmente, si dovrebbe mantenere un mapping 1:1 tra condivisioni file e account di archiviazione, ma questo non sempre è possibile, a causa dei diversi limiti e restrizioni imposti dall'organizzazione e da Azure. Quando non è possibile distribuire una sola condivisione file per account di archiviazione, valutare quali condivisioni saranno particolarmente attive e quali lo saranno meno, per assicurarsi di non inserire le condivisioni file più utilizzate nello stesso account di archiviazione.

- Distribuire solo gli account GPv2 e filestorage e aggiornare utilizzo generico V1 e gli account di archiviazione classici quando vengono individuati nell'ambiente in uso. 

## <a name="identity"></a>Identità
Per accedere a una condivisione file di Azure, l'utente della condivisione file deve essere autenticato e avere l'autorizzazione per accedere alla condivisione. Questa operazione viene eseguita in base all'identità dell'utente che accede alla condivisione file. File di Azure si integra con tre provider di identità principali:
- **Active Directory Domain Services locale (servizi di dominio Active Directory o servizi di dominio Active Directory locale)**: gli account di archiviazione di Azure possono essere aggiunti a un dominio di proprietà del cliente, Active Directory Domain Services, proprio come un dispositivo Windows Server file server o NAS. È possibile distribuire un controller di dominio in locale, in una macchina virtuale di Azure o anche come macchina virtuale in un altro provider di servizi cloud. File di Azure è indipendente dalla posizione in cui è ospitato il controller di dominio. Una volta che un account di archiviazione è stato aggiunto a un dominio, l'utente finale può montare una condivisione file con l'account utente che ha eseguito l'accesso al proprio PC con. L'autenticazione basata su Active Directory usa il protocollo di autenticazione Kerberos.
- **Azure Active Directory Domain Services (Azure AD DS)**: Azure AD DS fornisce un controller di dominio gestito da Microsoft che può essere usato per le risorse di Azure. Il dominio che aggiunge l'account di archiviazione a Azure AD DS offre vantaggi simili al dominio che lo aggiunge a una Active Directory di proprietà del cliente. Questa opzione di distribuzione è particolarmente utile per gli scenari di Lift-and-Shift per le applicazioni che richiedono autorizzazioni basate su AD. Poiché Azure AD DS fornisce l'autenticazione basata su Active Directory, questa opzione Usa anche il protocollo di autenticazione Kerberos.
- **Chiave account di archiviazione** di Azure: le condivisioni file di Azure possono essere montate anche con una chiave dell'account di archiviazione di Azure. Per montare una condivisione file in questo modo, viene usato il nome dell'account di archiviazione come nome utente e la chiave dell'account di archiviazione viene usata come password. L'uso della chiave dell'account di archiviazione per montare la condivisione file di Azure è in realtà un'operazione di amministratore, poiché la condivisione file montata disporrà di autorizzazioni complete per tutti i file e le cartelle della condivisione, anche se contengono ACL. Quando si usa la chiave dell'account di archiviazione per il montaggio tramite SMB, viene usato il protocollo di autenticazione NTLMv2.

Per i clienti che eseguono la migrazione da file server locali o per la creazione di nuove condivisioni file in File di Azure progettati per comportarsi come i file server Windows o i dispositivi NAS, il dominio che aggiunge l'account di archiviazione al **Active Directory di proprietà del cliente** è l'opzione consigliata. Per altre informazioni sull'aggiunta dell'account di archiviazione a un dominio di Active Directory di proprietà del cliente, vedere [Azure Files Active Directory overview](storage-files-active-directory-overview.md) (Panoramica di Active Directory per File di Azure).

Se si intende usare la chiave dell'account di archiviazione per accedere alle condivisioni file di Azure, è consigliabile usare gli endpoint di servizio come descritto nella sezione [rete](#networking) .

## <a name="networking"></a>Rete
Le condivisioni file di Azure sono accessibili da qualsiasi posizione tramite l'endpoint pubblico dell'account di archiviazione. Questo significa che le richieste autenticate, ad esempio quelle autorizzate dall'identità di accesso di un utente, possono avere origine in modo sicuro all'interno o all'esterno di Azure. In molti ambienti dei clienti, un montaggio iniziale della condivisione file di Azure nella workstation locale avrà esito negativo, anche se i montaggi dalle macchine virtuali di Azure riescono. Il motivo è che molte organizzazioni e provider di servizi Internet (ISP) bloccano la porta usata da SMB per comunicare, ovvero la porta 445. Passare a [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) per visualizzare un riepilogo degli ISP in grado di consentire o proibire l'accesso dalla porta 445.

Per sbloccare l'accesso alla condivisione file di Azure, sono disponibili due opzioni principali:

- Sbloccare la porta 445 per la rete locale dell'organizzazione. È possibile accedere alle condivisioni file di Azure solo tramite l'endpoint pubblico usando protocolli Internet sicuri, ad esempio SMB 3,0 e l'API filerest. Questo è il modo più semplice per accedere alla condivisione file di Azure dall'ambiente locale perché non richiede una configurazione di rete avanzata oltre a modificare le regole della porta in uscita dell'organizzazione, ma è consigliabile rimuovere le versioni legacy e deprecate del protocollo SMB, ovvero SMB 1,0. Per informazioni su come eseguire questa operazione, vedere [protezione di Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server) e [protezione di Linux](storage-how-to-use-files-linux.md#securing-linux).

- Accedere alle condivisioni file di Azure tramite una connessione VPN o ExpressRoute. Quando si accede alla condivisione file di Azure tramite un tunnel di rete, è possibile montare la condivisione file di Azure come una condivisione file locale perché il traffico SMB non attraversa i limiti dell'organizzazione.   

Sebbene dal punto di vista tecnico, è molto più semplice montare le condivisioni file di Azure tramite l'endpoint pubblico, si prevede che la maggior parte dei clienti scelga di montare le condivisioni file di Azure tramite una connessione VPN o ExpressRoute. Il montaggio con queste opzioni è possibile con le condivisioni SMB e NFS. A tale scopo, sarà necessario configurare quanto segue per l'ambiente:  

- **Tunneling di rete con ExpressRoute, da sito a sito o VPN da punto a sito**: il tunneling in una rete virtuale consente l'accesso alle condivisioni file di Azure dall'ambiente locale, anche se la porta 445 è bloccata.
- **Endpoint privati**: gli endpoint privati assegnano all'account di archiviazione un indirizzo IP dedicato dallo spazio degli indirizzi della rete virtuale. Questo consente il tunneling di rete senza la necessità di aprire reti locali fino a tutti gli intervalli di indirizzi IP di proprietà dei cluster di archiviazione di Azure. 
- **Inoltri DNS**: configurare il DNS locale per risolvere il nome dell'account di archiviazione, ad esempio `storageaccount.file.core.windows.net` per le aree del cloud pubblico, per risolvere l'indirizzo IP degli endpoint privati.

Per pianificare la rete associata alla distribuzione di una condivisione file di Azure, vedere [file di Azure considerazioni sulla rete](storage-files-networking-overview.md).

## <a name="encryption"></a>Crittografia
File di Azure supporta due tipi diversi di crittografia, ovvero la crittografia in transito, che è correlata alla crittografia usata per il montaggio o l'accesso alla condivisione file di Azure e la crittografia dei dati inattivi, che fa riferimento alla modalità di crittografia dei dati quando vengono archiviati su disco. 

### <a name="encryption-in-transit"></a>Crittografia in transito

> [!IMPORTANT]
> Questa sezione illustra i dettagli della crittografia in transito per le condivisioni SMB. Per informazioni dettagliate sulla crittografia in transito con le condivisioni NFS, vedere [Sicurezza](storage-files-compare-protocols.md#security).

Per impostazione predefinita, la crittografia in transito è abilitata per tutti gli account di archiviazione di Azure. Quindi, quando si monta una condivisione file su SMB o vi si accede tramite il protocollo FileREST (ad esempio tramite il portale di Azure, PowerShell/interfaccia della riga di comando o Azure SDK), File di Azure consentirà la connessione solo se viene eseguita con SMB 3.0 o versione successiva con crittografia oppure HTTPS. I client che non supportano SMB 3.0 oppure che supportano SMB 3.0 ma non la crittografia SMB non potranno montare la condivisione file di Azure se è abilitata la crittografia in transito. Per altre informazioni sui sistemi operativi che supportano SMB 3.0 con crittografia, vedere la documentazione dettagliata per [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)e [Linux](storage-how-to-use-files-linux.md). Tutte le versioni correnti di PowerShell, interfaccia della riga di comando e SDK supportano HTTPS.  

È possibile disabilitare la crittografia in transito per un account di archiviazione di Azure. Quando la crittografia è disabilitata, File di Azure consentirà anche SMB 2,1, SMB 3,0 senza crittografia e chiamate API filerest non crittografate su HTTP. La crittografia in transito viene in genere disabilitata principalmente per supportare un'applicazione legacy che deve essere eseguita in un sistema operativo meno recente, ad esempio Windows Server 2008 R2 o una distribuzione Linux precedente. File di Azure consente connessioni SMB 2.1 solo all'interno della stessa area di Azure della condivisione file di Azure. Un client SMB 2.1 situato all'esterno dell'area di Azure in cui si trova la condivisione file di Azure, ad esempio in locale o in un'area di Azure differente, non potrà accedere alla condivisione file.

È fortemente consigliabile verificare che la crittografia dei dati in transito sia abilitata.

Per altre informazioni sulla crittografia in transito, vedere [Richiedere il trasferimento sicuro in Archiviazione di Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>Protezione dei dati
File di Azure offre un approccio a più livelli per garantire che i dati vengano sottoposti a backup, ripristinabili e protetti da minacce per la sicurezza.

### <a name="soft-delete"></a>Eliminazione temporanea
L'eliminazione temporanea per le condivisioni file (anteprima) è un'impostazione a livello di account di archiviazione che consente di ripristinare la condivisione file quando viene accidentalmente eliminata. Quando viene eliminata, una condivisione file passa a uno stato di eliminazione temporanea anziché essere cancellata definitivamente. È possibile configurare la quantità di tempo durante il quale i dati eliminati temporaneamente sono ripristinabili prima che vengano eliminati definitivamente ed eliminare la condivisione in qualsiasi momento durante questo periodo di conservazione. 

È consigliabile attivare l'eliminazione temporanea per la maggior parte delle condivisioni file. Se si dispone di un flusso di lavoro in cui l'eliminazione della condivisione è comune e prevista, è possibile che si disponga di un periodo di conservazione molto breve o che l'eliminazione temporanea non sia abilitata.

Per altre informazioni sull'eliminazione temporanea, vedere [impedire l'eliminazione accidentale di dati](./storage-files-prevent-file-share-deletion.md).

### <a name="backup"></a>Backup
È possibile eseguire il backup della condivisione file di Azure tramite [snapshot di condivisione](./storage-snapshots-files.md), che sono copie temporizzate di sola lettura della condivisione. Gli snapshot sono incrementali, ovvero contengono solo la quantità di dati modificata rispetto allo snapshot precedente. È possibile avere fino a 200 snapshot per ogni condivisione file e conservarli per un massimo di 10 anni. È possibile eseguire manualmente questi snapshot nel portale di Azure, tramite PowerShell o l'interfaccia della riga di comando oppure è possibile usare [backup di Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Gli snapshot vengono archiviati all'interno della condivisione file, vale a dire che se si elimina la condivisione file, verranno eliminati anche gli snapshot. Per proteggere i backup di snapshot da eliminazioni accidentali, verificare che l'eliminazione temporanea sia abilitata per la condivisione.

[Backup di Azure per le condivisioni file di Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) gestisce la pianificazione e la conservazione degli snapshot. Le funzionalità di nonno-padre-figlio (GFS) indicano che è possibile adottare snapshot giornalieri, settimanali, mensili e annuali, ognuno con un proprio periodo di conservazione distinto. Backup di Azure orchestra anche l'abilitazione dell'eliminazione temporanea e accetta un blocco di eliminazione su un account di archiviazione non appena viene configurata una condivisione file al suo interno per il backup. Infine, backup di Azure fornisce alcune funzionalità di monitoraggio e avviso chiave che consentono ai clienti di ottenere una visualizzazione consolidata del loro patrimonio di backup.

È possibile eseguire ripristini a livello di elemento e di condivisione nel portale di Azure usando backup di Azure. È sufficiente scegliere il punto di ripristino (uno snapshot particolare), il file o la directory specifica, se pertinente, quindi il percorso (originale o alternativo) in cui si vuole eseguire il ripristino. Il servizio di backup gestisce la copia dei dati dello snapshot e Mostra lo stato di avanzamento del ripristino nel portale.

Per ulteriori informazioni sul backup, vedere [informazioni sul backup di condivisioni file di Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="advanced-threat-protection-for-azure-files-preview"></a>Advanced Threat Protection per File di Azure (anteprima)
Advanced Threat Protection (ATP) per archiviazione di Azure offre un ulteriore livello di intelligence per la sicurezza che fornisce avvisi quando rileva attività anomale nell'account di archiviazione, ad esempio tentativi insoliti di accedere all'account di archiviazione. ATP esegue anche l'analisi della reputazione di hash malware e avvisa su malware noto. È possibile configurare ATP a livello di sottoscrizione o di account di archiviazione tramite il Centro sicurezza di Azure. 

Per altre informazioni, vedere [Advanced Threat Protection per archiviazione di Azure](../common/azure-defender-storage-configure.md).

## <a name="storage-tiers"></a>Livelli di archiviazione
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Abilitare condivisioni file standard fino a 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="limitations"></a>Limitazioni
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Ridondanza
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migrazione
In molti casi, non verrà stabilita una nuova condivisione file di rete per l'organizzazione, ma la migrazione di una condivisione file esistente da un dispositivo file server o NAS locale a File di Azure. La scelta della strategia e dello strumento di migrazione corretti per lo scenario è importante per il successo della migrazione. 

L' [articolo Panoramica della migrazione](storage-files-migration-overview.md) illustra brevemente le nozioni di base e contiene una tabella che conduce alle guide alla migrazione che probabilmente coprono lo scenario.

## <a name="next-steps"></a>Passaggi successivi
* [Pianificazione di una distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
* [Distribuzione di File di Azure](./storage-how-to-create-file-share.md)
* [Distribuzione di Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)
* [Vedere l'articolo Panoramica della migrazione per trovare la guida alla migrazione per lo scenario](storage-files-migration-overview.md)