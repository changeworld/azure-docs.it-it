---
title: Domande frequenti sulle Azure NetApp Files | Microsoft Docs
description: Esaminare le domande frequenti sulle Azure NetApp Files, ad esempio rete, sicurezza, prestazioni, gestione della capacità e migrazione/protezione dei dati.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: b-juche
ms.openlocfilehash: a8c06b25b923d663e982e940100be7b9a2a009e1
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726845"
---
# <a name="faqs-about-azure-netapp-files"></a>Domande frequenti sulle Azure NetApp Files

Questo articolo contiene le risposte alle domande frequenti sulle Azure NetApp Files. 

## <a name="networking-faqs"></a>Domande frequenti sulla rete

### <a name="does-the-data-path-for-nfs-or-smb-go-over-the-internet"></a>Il percorso dati per NFS o SMB viene distribuito tramite Internet?  

No. Il percorso dati per NFS o SMB non passa attraverso Internet. Azure NetApp Files è un servizio nativo di Azure distribuito nella rete virtuale di Azure in cui è disponibile il servizio. Azure NetApp Files usa una subnet delegata ed effettua il provisioning di un'interfaccia di rete direttamente nella rete virtuale. 

Per [informazioni dettagliate, Azure NetApp Files linee guida per la pianificazione](./azure-netapp-files-network-topologies.md) della rete.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>È possibile connettere una rete virtuale già creata al servizio Azure NetApp Files rete virtuale?

Sì, è possibile connettere le reti virtuali create al servizio. 

Per [informazioni dettagliate, Azure NetApp Files linee guida per la pianificazione](./azure-netapp-files-network-topologies.md) della rete.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>È possibile montare un volume NFS di Azure NetApp Files nome FQDN DNS?

Sì, è possibile, se si creano le voci DNS necessarie. Azure NetApp Files fornisce l'indirizzo IP del servizio per il volume di cui è stato effettuato il provisioning. 

> [!NOTE] 
> Azure NetApp Files possibile distribuire altri IP per il servizio in base alle esigenze.  Potrebbe essere necessario aggiornare periodicamente le voci DNS.

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>È possibile impostare o selezionare il proprio indirizzo IP per un volume Azure NetApp Files?  

No. L'assegnazione IP Azure NetApp Files volumi è dinamica. L'assegnazione di indirizzi IP statici non è supportata. 

### <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>La Azure NetApp Files supporta la rete virtuale dual stack (IPv4 e IPv6) ?

No, Azure NetApp Files attualmente non supporta la rete virtuale dual stack (IPv4 e IPv6).  
 
## <a name="security-faqs"></a>Domande frequenti sulla sicurezza

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>È possibile crittografare il traffico di rete tra la macchina virtuale di Azure e l'archiviazione?

Il traffico dati tra i client NFSv4.1 e i volumi Azure NetApp Files può essere crittografato usando Kerberos con crittografia AES-256. Per informazioni dettagliate, vedere Configurare la crittografia [Kerberos NFSv4.1 Azure NetApp Files.](configure-kerberos-encryption.md)   

Il traffico dati tra i client NFSv3 o SMB3 Azure NetApp Files volumi non è crittografato. Tuttavia, il traffico da una macchina virtuale di Azure (che esegue un client NFS o SMB) a Azure NetApp Files è sicuro come qualsiasi altro traffico da macchina virtuale a macchina virtuale di Azure. Questo traffico è locale verso la rete del data center di Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>L'archiviazione può essere crittografata in stato di inquieto?

Tutti Azure NetApp Files volumi vengono crittografati usando lo standard FIPS 140-2. Tutte le chiavi vengono gestite dal Azure NetApp Files servizio. 

### <a name="how-are-encryption-keys-managed"></a>Come vengono gestite le chiavi di crittografia? 

La gestione delle Azure NetApp Files viene gestita dal servizio. Per ogni volume viene generata una chiave di crittografia dei dati XTS-AES-256 univoca. Una gerarchia di chiavi di crittografia viene usata per crittografare e proteggere tutte le chiavi di volume. Queste chiavi di crittografia non vengono mai visualizzate o segnalate in un formato non crittografato. Le chiavi di crittografia vengono eliminate immediatamente quando viene eliminato un volume.

Il supporto per le chiavi gestite dal cliente (Bring Your Own Key) tramite HSM dedicato di Azure è disponibile su base controllata nelle aree Stati Uniti orientali, Stati Uniti centro-meridionali, Stati Uniti occidentali 2 e US Gov Virginia stati uniti. È possibile richiedere l'accesso all'indirizzo [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) . Quando la capacità diventa disponibile, le richieste verranno approvate.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>È possibile configurare le regole dei criteri di esportazione NFS per controllare l'accesso alla destinazione Azure NetApp Files montaggio del servizio?

Sì, è possibile configurare fino a cinque regole in un singolo criterio di esportazione NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>I Azure NetApp Files supportano i gruppi di sicurezza di rete?

No, attualmente non è possibile applicare gruppi di sicurezza di rete alla subnet delegata Azure NetApp Files o alle interfacce di rete create dal servizio.

### <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>È possibile usare il controllo degli accessi in base al ruolo di Azure con Azure NetApp Files?

Sì, Azure NetApp Files supporta le funzionalità di Controllo degli accessi in base al ruolo di Azure. Oltre ai ruoli predefiniti di Azure, è possibile [creare ruoli personalizzati per](../role-based-access-control/custom-roles.md) Azure NetApp Files. 

Per l'elenco completo delle autorizzazioni Azure NetApp Files, vedere Operazioni del provider di risorse di Azure per [`Microsoft.NetApp`](../role-based-access-control/resource-provider-operations.md#microsoftnetapp) .

### <a name="are-azure-activity-logs-supported-on-azure-netapp-files"></a>I log attività di Azure sono supportati Azure NetApp Files?

Azure NetApp Files è un servizio nativo di Azure. Tutte le API PUT, POST e DELETE Azure NetApp Files vengono registrate. Ad esempio, i log mostrano attività quali chi ha creato lo snapshot, chi ha modificato il volume e così via.

Per l'elenco completo delle operazioni API, [Azure NetApp Files'API REST.](/rest/api/netapp/)

### <a name="can-i-use-azure-policies-with-azure-netapp-files"></a>È possibile usare i criteri di Azure con Azure NetApp Files?

Sì, è possibile creare [criteri di Azure personalizzati.](../governance/policy/tutorials/create-custom-policy-definition.md) 

Non è tuttavia possibile creare criteri di Azure (criteri di denominazione personalizzati) nell Azure NetApp Files intervazione. Vedere [Linee guida per Azure NetApp Files pianificazione della rete.](azure-netapp-files-network-topologies.md#considerations)

## <a name="performance-faqs"></a>Domande frequenti sulle prestazioni

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Cosa è necessario fare per ottimizzare o ottimizzare Azure NetApp Files prestazioni?

È possibile eseguire le azioni seguenti in base ai requisiti di prestazioni: 
- Assicurarsi che le dimensioni della macchina virtuale siano appropriate.
- Abilitare la rete accelerata per la macchina virtuale.
- Selezionare il livello di servizio e le dimensioni desiderate per il pool di capacità.
- Creare un volume con le dimensioni della quota desiderate per la capacità e le prestazioni.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Ricerca per categorie convertire i livelli di servizio basati sulla velocità effettiva Azure NetApp Files in operazioni di I/O al secondo?

È possibile convertire MB/s in operazioni di I/O al secondo usando la formula seguente:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Ricerca per categorie il livello di servizio di un volume?

È possibile modificare il livello di servizio di un volume esistente [](azure-netapp-files-service-levels.md) spostando il volume in un altro pool di capacità che usa il livello di servizio desiderato per il volume. Vedere [Modificare dinamicamente il livello di servizio di un volume](dynamic-change-volume-service-level.md). 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Ricerca per categorie monitorare Azure NetApp Files prestazioni?

Azure NetApp Files metriche delle prestazioni dei volumi. È anche possibile usare Monitoraggio di Azure per monitorare le metriche di utilizzo per Azure NetApp Files.  Vedere [Metriche per Azure NetApp Files](azure-netapp-files-metrics.md) per l'elenco delle metriche delle prestazioni per Azure NetApp Files.

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>Qual è l'impatto sulle prestazioni di Kerberos in NFSv4.1?

Vedere Impatto sulle prestazioni di Kerberos sui volumi [NFSv4.1](performance-impact-kerberos.md) per informazioni sulle opzioni di sicurezza per NFSv4.1, sui vettori di prestazioni testati e sull'impatto sulle prestazioni previsto. 

## <a name="nfs-faqs"></a>Domande frequenti su NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Si vuole che un volume sia montato automaticamente all'avvio o al riavvio di una macchina virtuale di Azure.  Ricerca per categorie l'host per i volumi NFS permanenti?

Per montare automaticamente un volume NFS all'avvio o al riavvio della macchina virtuale, aggiungere una voce al `/etc/fstab` file nell'host. 

Per [informazioni dettagliate, vedere Montare o](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) smontare un volume per macchine virtuali Windows o Linux.  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Perché il comando DF nel client NFS non mostra le dimensioni del volume di cui è stato effettuato il provisioning?

Le dimensioni del volume segnalate in DF sono le dimensioni massime a cui Azure NetApp Files volume può aumentare. Le dimensioni del Azure NetApp Files volume nel comando DF non riflettono la quota o le dimensioni del volume.  È possibile ottenere le dimensioni Azure NetApp Files volume o la quota tramite il portale di Azure o l'API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Quale versione di NFS Azure NetApp Files supporta?

Azure NetApp Files supporta NFSv3 e NFSv4.1. È possibile [creare un volume usando](azure-netapp-files-create-volumes.md) una delle due versioni NFS. 

### <a name="how-do-i-enable-root-squashing"></a>Ricerca per categorie lo squashing della radice?

È possibile specificare se l'account radice può accedere al volume o meno usando i criteri di esportazione del volume. Per [informazioni dettagliate, vedere Configurare i criteri di](azure-netapp-files-configure-export-policy.md) esportazione per un volume NFS.

### <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>È possibile usare lo stesso percorso di file (token di creazione del volume) per più volumi?

Sì, è possibile. Tuttavia, il percorso del file deve essere usato in una sottoscrizione diversa o in un'area diversa.   

Ad esempio, si crea un volume denominato `vol1` . Si crea quindi un altro volume denominato `vol1` anche in un pool di capacità diverso, ma nella stessa sottoscrizione e nella stessa area. In questo caso, l'uso dello stesso nome di volume `vol1` causerà un errore. Per usare lo stesso percorso di file, il nome deve essere in un'area o in una sottoscrizione diversa.

### <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>Quando si prova ad accedere ai volumi NFS tramite un client Windows, perché la ricerca di cartelle e sottocartelle da parte del client richiede molto tempo?

Assicurarsi che sia abilitato nel client Windows per velocizzare la ricerca di `CaseSensitiveLookup` cartelle e sottocartelle:

1. Usare il comando di PowerShell seguente per abilitare CaseSensitiveLookup:   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. Montare il volume nel server Windows.   
    Esempio:   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

### <a name="how-does-azure-netapp-files-support-nfsv41-file-locking"></a>In che modo Azure NetApp Files il blocco dei file NFSv4.1? 

Per i client NFSv4.1, Azure NetApp Files supporta il meccanismo di blocco dei file NFSv4.1 che mantiene lo stato di tutti i blocchi di file in un modello basato su lease. 

Per RFC 3530, Azure NetApp Files un singolo periodo di lease per tutto lo stato di un client NFS. Se il client non rinnova il lease entro il periodo definito, tutti gli stati associati al lease del client verranno rilasciati dal server.  

Ad esempio, se un client che monta un volume non risponde o si arresta in modo anomalo oltre i timeout, i blocchi verranno rilasciati. Il client può rinnovare il lease in modo esplicito o implicito eseguendo operazioni come la lettura di un file.   

Un periodo di tolleranza definisce un periodo di elaborazione speciale in cui i client possono provare a recuperare lo stato di blocco durante un ripristino del server. Il timeout predefinito per i lease è di 30 secondi con un periodo di tolleranza di 45 secondi. Dopo tale periodo, il lease del client verrà rilasciato.   

## <a name="smb-faqs"></a>Domande frequenti su SMB

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Quali versioni SMB sono supportate da Azure NetApp Files?

Azure NetApp Files supporta SMB 2.1 e SMB 3.1 (che include il supporto per SMB 3.0).    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>È necessaria una connessione Active Directory per l'accesso SMB? 

Sì, è necessario creare una connessione Active Directory prima di distribuire un volume SMB. I controller di dominio specificati devono essere accessibili dalla subnet delegata di Azure NetApp Files per una connessione riuscita.  Per [informazioni dettagliate, vedere Creare un volume SMB.](./azure-netapp-files-create-volumes-smb.md) 

### <a name="how-many-active-directory-connections-are-supported"></a>Quante connessioni Active Directory sono supportate?

Azure NetApp Files non supporta più connessioni Active Directory (AD) in una singola *area,* anche se le connessioni ad Active Directory sono in account NetApp diversi. Tuttavia, è possibile avere più connessioni AD in una *singola* sottoscrizione, purché le connessioni ad Active Directory siano in aree diverse. Se sono necessarie più connessioni AD in una singola area, è possibile usare sottoscrizioni separate a tale scopo. 

Viene configurata una connessione AD per ogni account NetApp. La connessione AD è visibile solo tramite l'account NetApp in cui è stata creata.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>La Azure NetApp Files supporta Azure Active Directory? 

Sono [supportati Azure Active Directory servizi](../active-directory-domain-services/overview.md) di dominio Active Directory Domain Services [(AD DS).](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) È possibile usare controller di dominio Active Directory esistenti con Azure NetApp Files. I controller di dominio possono risiedere in Azure come macchine virtuali o in locale tramite ExpressRoute o VPN da sito a sito. Azure NetApp Files non supporta l'aggiunta ad AD [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) in questo momento.

Se si usa Azure NetApp Files con Azure Active Directory Domain Services, il percorso dell'unità organizzativa sarà `OU=AADDC Computers` quando si configura Active Directory per l'account NetApp.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Quali versioni di Windows Server Active Directory sono supportate?

Azure NetApp Files supporta le versioni di Windows Server 2008r2SP1-2019 Active Directory Domain Services.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Perché lo spazio disponibile nel client SMB non mostra le dimensioni di cui è stato effettuato il provisioning?

Le dimensioni del volume segnalate dal client SMB sono le dimensioni massime a cui Azure NetApp Files volume può aumentare. Le dimensioni del volume Azure NetApp Files come illustrato nel client SMB non riflettono la quota o le dimensioni del volume. È possibile ottenere le dimensioni Azure NetApp Files volume o la quota tramite il portale di Azure o l'API.

### <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>Si verificano problemi di connessione alla condivisione SMB. Cosa devo fare?

Come procedura consigliata, impostare la tolleranza massima per la sincronizzazione dell'orologio del computer su cinque minuti. Per altre informazioni, vedere [Tolleranza massima per la sincronizzazione dell'orologio del computer.](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11)) 

### <a name="can-i-manage-smb-shares-sessions-and-open-files-through-computer-management-console-mmc"></a>È possibile gestire `SMB Shares` , e tramite Mmc `Sessions` `Open Files` (Computer Management Console)?

La gestione `SMB Shares` di , e tramite Mmc `Sessions` `Open Files` (Computer Management Console) non è attualmente supportata.

### <a name="how-can-i-obtain-the-ip-address-of-an-smb-volume-via-the-portal"></a>Come è possibile ottenere l'indirizzo IP di un volume SMB tramite il portale?

Usare il collegamento Json View (Visualizzazione **JSON)** nel riquadro di panoramica del volume e cercare **l'identificatore startIp** nelle **proprietà**  ->  **mountTargets**.

### <a name="smb-encryption-faqs"></a>Domande frequenti sulla crittografia SMB

Questa sezione risponde alle domande frequenti sulla crittografia SMB (SMB 3.0 e SMB 3.1.1).

#### <a name="what-is-smb-encryption"></a>Che cos'è la crittografia SMB?  

[La crittografia SMB](/windows-server/storage/file-server/smb-security) fornisce la crittografia end-to-end dei dati SMB e protegge i dati dalle intercettazioni di occorrenze in reti non attendibili. La crittografia SMB è supportata in SMB 3.0 e versioni successive. 

#### <a name="how-does-smb-encryption-work"></a>Come funziona la crittografia SMB?

Quando si invia una richiesta all'archiviazione, il client crittografa la richiesta, che viene quindi decrittografa dall'archiviazione. Le risposte vengono crittografate in modo analogo dal server e decrittografate dal client.

#### <a name="which-clients-support-smb-encryption"></a>Quali client supportano la crittografia SMB?

Windows 10, Windows 2012 e versioni successive supportano la crittografia SMB.

#### <a name="with-azure-netapp-files-at-what-layer-is-smb-encryption-enabled"></a>Con Azure NetApp Files, a quale livello è abilitata la crittografia SMB?  

La crittografia SMB è abilitata a livello di condivisione.

#### <a name="what-forms-of-smb-encryption-are-used-by-azure-netapp-files"></a>Quali forme di crittografia SMB vengono usate da Azure NetApp Files?

SMB 3.0 usa l'algoritmo AES-CCM, mentre SMB 3.1.1 usa l'algoritmo AES-GCM

#### <a name="is-smb-encryption-required"></a>È necessaria la crittografia SMB?

La crittografia SMB non è necessaria. Di conseguenza, è abilitato solo per una determinata condivisione se l'utente Azure NetApp Files abilitarla. Azure NetApp Files condivisioni non vengono mai esposte a Internet. Sono accessibili solo dall'interno di una determinata rete virtuale, tramite VPN o express route, quindi Azure NetApp Files condivisioni sono intrinsecamente sicure. La scelta di abilitare la crittografia SMB dipende interamente dall'utente. Prima di abilitare questa funzionalità, è necessario tenere presenti i problemi di prestazioni previsti.

#### <a name="what-is-the-anticipated-impact-of-smb-encryption-on-client-workloads"></a><a name="smb_encryption_impact"></a>Qual è l'impatto previsto della crittografia SMB sui carichi di lavoro client?

Anche se la crittografia SMB influisce sia sul client (sovraccarico della CPU per la crittografia e la decrittografia dei messaggi) che sull'archiviazione (riduzione della velocità effettiva), la tabella seguente evidenzia solo l'impatto sull'archiviazione. È consigliabile testare l'impatto delle prestazioni di crittografia sulle applicazioni prima di distribuire i carichi di lavoro nell'ambiente di produzione.

|     Profilo di I/O       |     Impatto        |
|-  |-  |
|     Leggere e scrivere carichi di lavoro      |     Dal 10% al 15%        |
|     Uso intensivo dei metadati        |     5%    |

## <a name="capacity-management-faqs"></a>Domande frequenti sulla gestione della capacità

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Ricerca per categorie monitorare l'utilizzo per il pool di capacità e il volume Azure NetApp Files? 

Azure NetApp Files metriche di utilizzo del volume e del pool di capacità. È anche possibile usare Monitoraggio di Azure per monitorare l'utilizzo per Azure NetApp Files. Per [informazioni dettagliate, Azure NetApp Files](azure-netapp-files-metrics.md) metriche. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>È possibile gestire i Azure NetApp Files tramite Azure Storage Explorer?

No. Azure NetApp Files non è supportato da Azure Storage Explorer.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Ricerca per categorie determinare se una directory sta per raggiunto il limite di dimensioni?

È possibile usare il comando da un client per verificare se una directory sta per raggiunto il limite di dimensioni massime per i metadati della `stat` directory (320 MB).   

Per una directory di 320 MB, il numero di blocchi è 655360, con dimensioni di blocco pari a 512 byte.  ovvero 320x1024x1024/512.  Questo numero si traduce in circa 4 milioni di file al massimo per una directory di 320 MB. Tuttavia, il numero effettivo di file massimi potrebbe essere inferiore, a seconda di fattori come il numero di file contenenti caratteri non ASCII nella directory. Di conseguenza, è necessario usare il comando come indicato di seguito `stat` per determinare se la directory sta per raggiunto il limite.  

Esempi:

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```


## <a name="data-migration-and-protection-faqs"></a>Domande frequenti sulla migrazione e la protezione dei dati

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Ricerca per categorie eseguire la migrazione dei dati Azure NetApp Files?
Azure NetApp Files fornisce volumi NFS e SMB.  È possibile usare qualsiasi strumento di copia basato su file per eseguire la migrazione dei dati al servizio. 

NetApp offre una soluzione basata su SaaS, [NetApp Cloud Sync.](https://cloud.netapp.com/cloud-sync-service)  La soluzione consente di replicare i dati NFS o SMB Azure NetApp Files le esportazioni NFS o le condivisioni SMB. 

È anche possibile usare un'ampia gamma di strumenti gratuiti per copiare i dati. Per NFS, è possibile usare gli strumenti dei carichi di lavoro, ad esempio [rsync,](https://rsync.samba.org/examples.html) per copiare e sincronizzare i dati di origine in un volume Azure NetApp Files locale. Per SMB, è possibile usare i carichi di lavoro [robocopy](/windows-server/administration/windows-commands/robocopy) nello stesso modo.  Questi strumenti possono anche replicare le autorizzazioni per file o cartelle. 

I requisiti per la migrazione dei dati da locale Azure NetApp Files sono i seguenti:The requirements for data migration from on premises to Azure NetApp Files are as follows: 

- Assicurarsi Azure NetApp Files sia disponibile nell'area di Azure di destinazione.
- Convalidare la connettività di rete tra l'origine e l Azure NetApp Files IP del volume di destinazione. Il trasferimento dei dati tra locale e il Azure NetApp Files è supportato tramite ExpressRoute.
- Creare il volume Azure NetApp Files destinazione.
- Trasferire i dati di origine nel volume di destinazione usando lo strumento di copia file preferito.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Ricerca per categorie creare una copia di un volume Azure NetApp Files in un'altra area di Azure?
    
Azure NetApp Files fornisce volumi NFS e SMB.  Qualsiasi strumento di copia basata su file può essere usato per replicare i dati tra aree di Azure. 

NetApp offre una soluzione basata su SaaS, [NetApp Cloud Sync.](https://cloud.netapp.com/cloud-sync-service)  La soluzione consente di replicare i dati NFS o SMB Azure NetApp Files le esportazioni NFS o le condivisioni SMB. 

È anche possibile usare un'ampia gamma di strumenti gratuiti per copiare i dati. Per NFS, è possibile usare gli strumenti dei carichi di lavoro, ad esempio [rsync,](https://rsync.samba.org/examples.html) per copiare e sincronizzare i dati di origine in un volume Azure NetApp Files locale. Per SMB, è possibile usare i carichi di lavoro [robocopy](/windows-server/administration/windows-commands/robocopy) nello stesso modo.  Questi strumenti possono anche replicare le autorizzazioni per file o cartelle. 

I requisiti per la replica di un volume Azure NetApp Files in un'altra area di Azure sono i seguenti: 
- Assicurarsi Azure NetApp Files sia disponibile nell'area di Azure di destinazione.
- Convalidare la connettività di rete tra reti virtuali in ogni area. Attualmente, il peering globale tra reti virtuali non è supportato.  È possibile stabilire la connettività tra reti virtuali collegando un circuito ExpressRoute o usando una connessione VPN da sito a sito. 
- Creare il volume Azure NetApp Files destinazione.
- Trasferire i dati di origine nel volume di destinazione usando lo strumento di copia file preferito.

### <a name="is-migration-with-azure-data-box-supported"></a>La migrazione con Azure Data Box supportata?

No. Azure Data Box attualmente non supporta Azure NetApp Files. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>La migrazione con il servizio Importazione/Esportazione di Azure è supportata?

No. Il servizio Importazione/Esportazione di Azure non supporta Azure NetApp Files attualmente.

## <a name="product-faqs"></a>Domande frequenti sul prodotto

### <a name="can-i-use-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>È possibile usare Azure NetApp Files NFS o SMB con soluzione Azure VMware (AVS)?

È possibile montare Azure NetApp Files NFS in macchine virtuali AvS Windows o Linux. È possibile eseguire il mapping Azure NetApp Files condivisioni SMB nelle macchine virtuali AvS Windows. Per altri dettagli, vedere [Azure NetApp Files con soluzione Azure VMware]( ../azure-vmware/netapp-files-with-azure-vmware-solution.md).  

### <a name="what-regions-are-supported-for-using-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Quali aree sono supportate per l'Azure NetApp Files di volumi NFS o SMB con soluzione Azure VMware (AVS)?

L'Azure NetApp Files di volumi NFS o SMB con AVS è supportato nelle aree seguenti: Stati Uniti orientali, Stati Uniti occidentali, Europa occidentale e Australia orientale.

## <a name="next-steps"></a>Passaggi successivi  

- [Microsoft Azure domande frequenti su ExpressRoute](../expressroute/expressroute-faqs.md)
- [Rete virtuale di Microsoft Azure domande frequenti](../virtual-network/virtual-networks-faq.md)
- [Come creare una richiesta di supporto in Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [Domande frequenti sulle prestazioni SMB per Azure NetApp Files](azure-netapp-files-smb-performance.md)
