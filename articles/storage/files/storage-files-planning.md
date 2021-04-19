---
title: Pianificazione per la distribuzione dei File di Azure | Microsoft Docs
description: Informazioni sulla pianificazione di una File di Azure distribuzione. È possibile montare direttamente una condivisione file di Azure o memorizzare nella cache la condivisione file di Azure locale con Sincronizzazione file di Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 2cb3bee770653173f1a40b209c27d2dc92c7df11
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718036"
---
# <a name="planning-for-an-azure-files-deployment"></a>Pianificazione per la distribuzione dei file di Azure
[File di Azure](storage-files-introduction.md) può essere distribuito in due modi principali: montando direttamente le condivisioni file di Azure serverless o memorizzando nella cache le condivisioni file di Azure in locale usando Sincronizzazione file di Azure. L'opzione di distribuzione scelta modifica gli aspetti da considerare durante la pianificazione della distribuzione. 

- Montaggio diretto di una condivisione file di **Azure:** poiché File di Azure fornisce l'accesso Server Message Block (SMB) o NFS (Network File System), è possibile montare condivisioni file di Azure in locale o nel cloud usando i client SMB o NFS standard disponibili nel sistema operativo. Poiché le condivisioni file di Azure sono serverless, la distribuzione per gli scenari di produzione non richiede la gestione di dispositivi NAS o file server. Ciò significa che non è necessario applicare patch software o sostituire dischi fisici. 

- **Memorizzare nella cache locale una condivisione file di Azure con Sincronizzazione file di Azure**: Sincronizzazione file di Azure consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Sincronizzazione file di Azure trasforma un'istanza locale (o cloud) di Windows Server in una cache rapida della condivisione file SMB di Azure. 

Questo articolo illustra principalmente le considerazioni sulla distribuzione di una condivisione file di Azure da montare direttamente da un client locale o cloud. Per pianificare una distribuzione Sincronizzazione file di Azure, vedere [Planning for an Sincronizzazione file di Azure deployment](storage-sync-files-planning.md).

## <a name="available-protocols"></a>Protocolli disponibili

File di Azure offre due protocolli che possono essere usati durante il montaggio delle condivisioni file, SMB e NFS (Network File System). Per informazioni dettagliate su questi protocolli, vedere Protocolli [di condivisione file di Azure.](storage-files-compare-protocols.md)

> [!IMPORTANT]
> La maggior parte del contenuto di questo articolo si applica solo alle condivisioni SMB. Qualsiasi elemento che si applica alle condivisioni NFS indica in modo specifico che è applicabile.

## <a name="management-concepts"></a>Concetti relativi alla gestione
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Quando si distribuiscono condivisioni file di Azure negli account di archiviazione, è consigliabile:

- Distribuzione di condivisioni file di Azure solo in account di archiviazione con altre condivisioni file di Azure. Anche se gli account di archiviazione per utilizzo gpv2 consentono di avere account di archiviazione con scopo misto, poiché le risorse di archiviazione come le condivisioni file di Azure e i contenitori BLOB condividono i limiti dell'account di archiviazione, la combinazione delle risorse può rendere più difficile risolvere i problemi di prestazioni in un secondo momento. 

- Quando si distribuiscono condivisioni file di Azure, prestare attenzione alle limitazioni di un account di archiviazione in termini di operazioni di I/O al secondo. Idealmente, si dovrebbe mantenere un mapping 1:1 tra condivisioni file e account di archiviazione, ma questo non sempre è possibile, a causa dei diversi limiti e restrizioni imposti dall'organizzazione e da Azure. Quando non è possibile distribuire una sola condivisione file per account di archiviazione, valutare quali condivisioni saranno particolarmente attive e quali lo saranno meno, per assicurarsi di non inserire le condivisioni file più utilizzate nello stesso account di archiviazione.

- Distribuire gli account per uso gpv2 e FileStorage e aggiornare gli account di archiviazione per uso gpv1 e classico solo quando vengono trovati nell'ambiente in uso. 

## <a name="identity"></a>Identità
Per accedere a una condivisione file di Azure, l'utente della condivisione file deve essere autenticato e avere l'autorizzazione per accedere alla condivisione. Questa operazione viene eseguita in base all'identità dell'utente che accede alla condivisione file. File di Azure si integra con tre provider di identità principali:
- **On-premises Active Directory Domain Services (AD DS, or on-premises AD DS):** gli account di archiviazione di Azure possono essere aggiunti a un dominio di proprietà del cliente, Active Directory Domain Services, proprio come un dispositivo Windows Server file server o NAS. È possibile distribuire un controller di dominio in locale, in una macchina virtuale di Azure o anche come macchina virtuale in un altro provider di servizi cloud. File di Azure è indipendente dalla posizione in cui è ospitato il controller di dominio. Dopo aver aggiunto un account di archiviazione a un dominio, l'utente finale può montare una condivisione file con l'account utente con cui ha eseguito l'accesso al PC. L'autenticazione basata su AD usa il protocollo di autenticazione Kerberos.
- **Azure Active Directory Domain Services (Azure AD DS):** Azure AD DS un controller di dominio gestito da Microsoft che può essere usato per le risorse di Azure. L'aggiunta di un dominio all'account di archiviazione Azure AD DS vantaggi simili all'aggiunta di un dominio a un'istanza di Active Directory di proprietà del cliente. Questa opzione di distribuzione è particolarmente utile per gli scenari di lift-and-shift delle applicazioni che richiedono autorizzazioni basate su AD. Poiché Azure AD DS l'autenticazione basata su AD, questa opzione usa anche il protocollo di autenticazione Kerberos.
- **Chiave dell'account di archiviazione di** Azure: le condivisioni file di Azure possono anche essere montate con una chiave dell'account di archiviazione di Azure. Per montare una condivisione file in questo modo, il nome dell'account di archiviazione viene usato come nome utente e la chiave dell'account di archiviazione viene usata come password. L'uso della chiave dell'account di archiviazione per montare la condivisione file di Azure è in effetti un'operazione di amministratore, poiché la condivisione file montata avrà autorizzazioni complete per tutti i file e le cartelle nella condivisione, anche se dispongono di ACL. Quando si usa la chiave dell'account di archiviazione per il montaggio su SMB, viene usato il protocollo di autenticazione NTLMv2.

Per i clienti che eseguono la migrazione da file server locali o creano nuove condivisioni file in File di Azure progettate per comportarsi come file server Windows o appliance NAS, l'aggiunta del dominio all'account di archiviazione in **Active Directory** di proprietà del cliente è l'opzione consigliata. Per altre informazioni sull'aggiunta dell'account di archiviazione a un dominio di Active Directory di proprietà del cliente, vedere [Azure Files Active Directory overview](storage-files-active-directory-overview.md) (Panoramica di Active Directory per File di Azure).

Se si intende usare la chiave dell'account di archiviazione per accedere alle condivisioni file di Azure, è consigliabile usare gli endpoint di servizio come descritto nella [sezione](#networking) Rete.

## <a name="networking"></a>Rete
Le condivisioni file di Azure sono accessibili da qualsiasi posizione tramite l'endpoint pubblico dell'account di archiviazione. Questo significa che le richieste autenticate, ad esempio quelle autorizzate dall'identità di accesso di un utente, possono avere origine in modo sicuro all'interno o all'esterno di Azure. In molti ambienti dei clienti, un montaggio iniziale della condivisione file di Azure nella workstation locale avrà esito negativo, anche se i montaggi dalle macchine virtuali di Azure riescono. Il motivo è che molte organizzazioni e provider di servizi Internet (ISP) bloccano la porta usata da SMB per comunicare, ovvero la porta 445. Passare a [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) per visualizzare un riepilogo degli ISP in grado di consentire o proibire l'accesso dalla porta 445.

Per sbloccare l'accesso alla condivisione file di Azure, sono disponibili due opzioni principali:

- Sbloccare la porta 445 per la rete locale dell'organizzazione. È possibile accedere esternamente alle condivisioni file di Azure solo tramite l'endpoint pubblico usando protocolli sicuri per Internet, ad esempio SMB 3.0 e l'API FileREST. Questo è il modo più semplice per accedere alla condivisione file di Azure dall'ambiente locale perché non richiede una configurazione di rete avanzata oltre alla modifica delle regole delle porte in uscita dell'organizzazione, tuttavia è consigliabile rimuovere le versioni legacy e deprecate del protocollo SMB, in questo caso SMB 1.0. Per informazioni su come eseguire questa operazione, vedere [Protezione di Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server) e Protezione di [Linux.](storage-how-to-use-files-linux.md#securing-linux)

- Accedere alle condivisioni file di Azure tramite una connessione ExpressRoute o VPN. Quando si accede alla condivisione file di Azure tramite un tunnel di rete, è possibile montare la condivisione file di Azure come una condivisione file locale perché il traffico SMB non attraversa il limite dell'organizzazione.   

Anche se dal punto di vista tecnico è notevolmente più semplice montare le condivisioni file di Azure tramite l'endpoint pubblico, la maggior parte dei clienti sceglierà di montare le proprie condivisioni file di Azure tramite una connessione ExpressRoute o VPN. Il montaggio con queste opzioni è possibile con condivisioni SMB e NFS. A tale scopo, è necessario configurare quanto segue per l'ambiente:  

- Tunneling di rete tramite **ExpressRoute, VPN** da sito a sito o da punto a sito: il tunneling in una rete virtuale consente l'accesso alle condivisioni file di Azure da locale, anche se la porta 445 è bloccata.
- **Endpoint privati: gli** endpoint privati assegnano all'account di archiviazione un indirizzo IP dedicato dall'interno dello spazio indirizzi della rete virtuale. Ciò consente il tunneling di rete senza la necessità di aprire reti locali fino a tutti gli intervalli di indirizzi IP di proprietà dei cluster di archiviazione di Azure. 
- **Inoltro DNS:** configurare il DNS locale per risolvere il nome dell'account di archiviazione (per le aree del cloud pubblico) per risolvere l'indirizzo IP degli `storageaccount.file.core.windows.net` endpoint privati.

Per pianificare la rete associata alla distribuzione di una condivisione file di Azure, vedere considerazioni File di Azure [sulla rete](storage-files-networking-overview.md).

## <a name="encryption"></a>Crittografia
File di Azure supporta due tipi diversi di crittografia: la crittografia in transito, correlata alla crittografia usata durante il montaggio/accesso alla condivisione file di Azure, e la crittografia in stato di inquieto, che si riferisce alla modalità di crittografia dei dati quando vengono archiviati su disco. 

### <a name="encryption-in-transit"></a>Crittografia in transito

> [!IMPORTANT]
> Questa sezione illustra i dettagli della crittografia in transito per le condivisioni SMB. Per informazioni dettagliate sulla crittografia in transito con le condivisioni NFS, vedere [Sicurezza](storage-files-compare-protocols.md#security).

Per impostazione predefinita, la crittografia in transito è abilitata per tutti gli account di archiviazione di Azure. Quindi, quando si monta una condivisione file su SMB o vi si accede tramite il protocollo FileREST (ad esempio tramite il portale di Azure, PowerShell/interfaccia della riga di comando o Azure SDK), File di Azure consentirà la connessione solo se viene eseguita con SMB 3.0 o versione successiva con crittografia oppure HTTPS. I client che non supportano SMB 3.0 oppure che supportano SMB 3.0 ma non la crittografia SMB non potranno montare la condivisione file di Azure se è abilitata la crittografia in transito. Per altre informazioni sui sistemi operativi che supportano SMB 3.0 con crittografia, vedere la documentazione dettagliata per [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)e [Linux](storage-how-to-use-files-linux.md). Tutte le versioni correnti di PowerShell, interfaccia della riga di comando e SDK supportano HTTPS.  

È possibile disabilitare la crittografia in transito per un account di archiviazione di Azure. Quando la crittografia è disabilitata, File di Azure consentirà anche SMB 2.1, SMB 3.0 senza crittografia e chiamate API FileREST non crittografate su HTTP. La crittografia in transito viene in genere disabilitata principalmente per supportare un'applicazione legacy che deve essere eseguita in un sistema operativo meno recente, ad esempio Windows Server 2008 R2 o una distribuzione Linux precedente. File di Azure consente connessioni SMB 2.1 solo all'interno della stessa area di Azure della condivisione file di Azure. Un client SMB 2.1 situato all'esterno dell'area di Azure in cui si trova la condivisione file di Azure, ad esempio in locale o in un'area di Azure differente, non potrà accedere alla condivisione file.

È fortemente consigliabile verificare che la crittografia dei dati in transito sia abilitata.

Per altre informazioni sulla crittografia in transito, vedere [Richiedere il trasferimento sicuro in Archiviazione di Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>Protezione dei dati
File di Azure ha un approccio a più livelli per garantire il backup, il ripristino e la protezione dei dati dalle minacce alla sicurezza.

### <a name="soft-delete"></a>Eliminazione temporanea
L'eliminazione soft per le condivisioni file (anteprima) è un'impostazione a livello di account di archiviazione che consente di ripristinare la condivisione file quando viene eliminata accidentalmente. Quando una condivisione file viene eliminata, passa a uno stato eliminato temporaneamente anziché essere cancellata definitivamente. È possibile configurare la quantità di tempo in cui i dati eliminati temporaneamente sono recuperabili prima che venga eliminato definitivamente e annullare l'eliminazione della condivisione in qualsiasi momento durante questo periodo di conservazione. 

È consigliabile attivare l'eliminazione soft per la maggior parte delle condivisioni file. Se si dispone di un flusso di lavoro in cui l'eliminazione della condivisione è comune e prevista, è possibile decidere di avere un breve periodo di conservazione o di non avere l'eliminazione soft abilitata.

Per altre informazioni sull'eliminazione definitiva, vedere Impedire [l'eliminazione accidentale dei dati.](./storage-files-prevent-file-share-deletion.md)

### <a name="backup"></a>Backup
È possibile eseguire il backup della condivisione file di Azure tramite [snapshot](./storage-snapshots-files.md)di condivisione, che sono copie tempor istantanee di sola lettura della condivisione. Gli snapshot sono incrementali, ovvero contengono solo la quantità di dati che sono stati modificati dopo lo snapshot precedente. È possibile avere fino a 200 snapshot per condivisione file e conservarli per un massimo di 10 anni. È possibile creare manualmente questi snapshot nel portale di Azure, tramite PowerShell o l'interfaccia della riga di comando oppure è possibile usare [Backup di Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Gli snapshot vengono archiviati all'interno della condivisione file, vale a dire che se si elimina la condivisione file, verranno eliminati anche gli snapshot. Per proteggere i backup degli snapshot dall'eliminazione accidentale, assicurarsi che l'eliminazione automatica sia abilitata per la condivisione.

[Backup di Azure per le condivisioni file di Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) gestisce la pianificazione e la conservazione degli snapshot. Le funzionalità GFS (Father-Son) consentono di creare snapshot giornalieri, settimanali, mensili e annuale, ognuno con un periodo di conservazione distinto. Backup di Azure orchestra anche l'abilitazione dell'eliminazione soft e accetta un blocco di eliminazione su un account di archiviazione non appena una condivisione file al suo interno viene configurata per il backup. Infine, Backup di Azure funzionalità chiave di monitoraggio e avviso che consentono ai clienti di avere una visualizzazione consolidata dell'area di backup.

È possibile eseguire ripristini sia a livello di elemento che a livello di condivisione nel portale di Azure usando Backup di Azure. È necessario scegliere solo il punto di ripristino (uno snapshot specifico), il file o la directory specifica, se pertinente, e quindi il percorso (originale o alternativo) in cui si vuole eseguire il ripristino. Il servizio di backup gestisce la copia dei dati dello snapshot e mostra lo stato di avanzamento del ripristino nel portale.

Per altre informazioni sul backup, vedere Informazioni [sul backup di condivisione file di Azure.](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

### <a name="azure-defender-for-azure-files"></a>Azure Defender per File di Azure 
Azure Defender per Archiviazione di Azure (in precedenza Advanced Threat Protection per Archiviazione di Azure) offre un ulteriore livello di intelligence per la sicurezza che fornisce avvisi quando rileva attività anomale nell'account di archiviazione, ad esempio tentativi di accesso insoliti. Esegue anche l'analisi della reputazione degli hash di malware e avvisa il malware noto. È possibile configurare Azure Defender a livello di sottoscrizione o di account di archiviazione tramite Centro sicurezza di Azure. 

Per altre informazioni, vedere [Introduction to Azure Defender for Storage](../../security-center/defender-for-storage-introduction.md).

## <a name="storage-tiers"></a>Livelli di archiviazione
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Abilitare condivisioni file standard fino a 100 TiB
Per impostazione predefinita, le condivisioni file standard possono estendersi solo fino a 5 TiB, ma è possibile aumentare il limite di condivisione a 100 TiB. Per informazioni su come aumentare il limite di condivisione, vedere [Abilitare e creare condivisioni file di grandi dimensioni.](storage-files-how-to-create-large-file-share.md)


#### <a name="limitations"></a>Limitazioni
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Ridondanza
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migrazione
In molti casi, non verrà stabilita una nuova condivisione file netta per l'organizzazione, ma si esegue la migrazione di una condivisione file esistente da un dispositivo file server o NAS locale a File di Azure. La scelta della strategia e dello strumento di migrazione più appropriata per lo scenario è importante per il successo della migrazione. 

[L'articolo panoramica sulla](storage-files-migration-overview.md) migrazione illustra brevemente le nozioni di base e contiene una tabella che contiene guide alla migrazione che probabilmente coprono lo scenario.

## <a name="next-steps"></a>Passaggi successivi
* [Pianificazione di una distribuzione Sincronizzazione file di Azure distribuzione](storage-sync-files-planning.md)
* [Distribuzione di File di Azure](./storage-how-to-create-file-share.md)
* [Distribuzione di Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)
* [Vedere l'articolo panoramica della migrazione per trovare la guida alla migrazione per lo scenario](storage-files-migration-overview.md)