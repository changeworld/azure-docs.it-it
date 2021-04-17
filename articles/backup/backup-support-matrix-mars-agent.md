---
title: Matrice di supporto per l'agente MARS
description: Questo articolo riepiloga il Backup di Azure quando si esegue il backup di computer che eseguono l'agente di Servizi di ripristino di Microsoft Azure (MARS).
ms.date: 04/09/2021
ms.topic: conceptual
ms.openlocfilehash: 20bca0e9ca9dfd735501e68bd0e5a6d69d2ef68e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576500"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matrice di supporto per il backup con l'agente di Servizi di ripristino di Microsoft Azure

È possibile usare il [Backup di Azure](backup-overview.md) per eseguire il backup di computer e app locali e per eseguire il backup di macchine virtuali (VM) di Azure. Questo articolo riepiloga le impostazioni e le limitazioni del supporto quando si usa l'agente di Servizi di ripristino di Microsoft Azure (MARS) per eseguire il backup dei computer.

## <a name="the-mars-agent"></a>Agente MARS

Backup di Azure usa l'agente di Servizi di ripristino di Microsoft Azure per eseguire il backup dei dati da computer locali e macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino di backup in Azure. L'agente MARS può:

- Eseguire nei computer Windows locali in modo che possano eseguire il backup direttamente in un insieme di credenziali di Servizi di ripristino di backup in Azure.
- Eseguire nelle macchine virtuali Windows in modo che possano eseguire il backup direttamente in un insieme di credenziali.
- Eseguire in Backup di Microsoft Azure Server (MABS) o in un server System Center Data Protection Manager (DPM). In questo scenario, i computer e i carichi di lavoro esere il backup nel server di Backup di Microsoft Azure o nel server DPM. L'agente MARS quindi backup del server in un insieme di credenziali in Azure.

> [!NOTE]
>Backup di Azure non supporta la regolazione automatica dell'orologio per l'ora legale. Modificare i criteri per assicurarsi che l'ora legale sia presa in considerazione per evitare discrepanze tra l'ora effettiva e l'ora di backup pianificata.

Le opzioni di backup dipendono dalla posizione in cui è installato l'agente. Per altre informazioni, vedere Architettura [Backup di Azure tramite l'agente MARS.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders) Per informazioni sull'architettura di backup di DPM e mabs, vedere [Eseguire il backup in DPM o maBS.](backup-architecture.md#architecture-back-up-to-dpmmabs) Vedere anche [i requisiti](backup-support-matrix-mabs-dpm.md) per l'architettura di backup.

**Installazione** | **Dettagli**
--- | ---
Scaricare l'agente MARS più recente | È possibile scaricare la versione più recente dell'agente dall'insieme di credenziali oppure [scaricarla direttamente](https://aka.ms/azurebackup_agent).
Installazione diretta in un computer | È possibile installare l'agente MARS direttamente in un server Windows locale o in una macchina virtuale Windows che esegue uno dei [sistemi operativi supportati.](./backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)
Installazione in un server di backup | Quando si configura DPM o il server di Backup di Microsoft Azure per eseguire il backup in Azure, si scarica e installa nel server l'agente di Servizi di ripristino di Microsoft Azure. È possibile installare l'agente nei [sistemi operativi supportati nella](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) matrice di supporto del server di backup.

> [!NOTE]
> Per impostazione predefinita, le macchine virtuali di Azure abilitate per il backup hanno un Backup di Azure installazione dell'estensione. Questa estensione esegue il backup dell'intera macchina virtuale. È possibile installare ed eseguire l'agente di Servizi di ripristino di Microsoft Azure in una macchina virtuale di Azure insieme all'estensione se si intende eseguire il backup di cartelle e file specifici anziché della macchina virtuale completa.
> Quando si esegue l'agente MARS in una macchina virtuale di Azure, viene eseguito il backup di file o cartelle presenti nell'archiviazione temporanea nella macchina virtuale. I backup hanno esito negativo se i file o le cartelle vengono rimossi dall'archiviazione temporanea o se l'archiviazione temporanea viene rimossa.

## <a name="cache-folder-support"></a>Supporto della cartella cache

Quando si usa l'agente MARS per eseguire il backup dei dati, l'agente esegue uno snapshot dei dati e li archivia in una cartella della cache locale prima di inviare i dati ad Azure. La cartella cache (scratch) presenta diversi requisiti:

**Cache** | **Dettagli**
--- | ---
Dimensione |  Lo spazio disponibile nella cartella della cache deve essere almeno dal 5 al 10% delle dimensioni complessive dei dati di backup.
Località | La cartella della cache deve essere archiviata in locale nel computer di cui viene eseguito il backup e deve essere online. La cartella della cache non deve essere in una condivisione di rete, su supporti rimovibili o in un volume offline.
Cartella | La cartella della cache non deve essere crittografata in un volume deduplicato o in una cartella compressa, di tipo sparse o con un reparse point.
Modifiche alla posizione | È possibile modificare il percorso della cache arrestando il motore di backup ( ) e `net stop bengine` copiando la cartella della cache in una nuova unità. Assicurarsi che la nuova unità abbia spazio sufficiente. Aggiornare quindi due voci del Registro di sistema in **HKLM\SOFTWARE\Microsoft\Windows Backup di Azure** (**Config/ScratchLocation** e **Config/CloudBackupProvider/ScratchLocation**) nel nuovo percorso e riavviare il motore.

## <a name="networking-and-access-support"></a>Supporto delle funzionalità di rete e dell'accesso

### <a name="url-and-ip-access"></a>URL e accesso IP

L'agente di Servizi di ripristino di Microsoft Azure deve poter accedere a questi URL:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.windowsazure.com
- *. MicrosoftOnline.com
- *. Windows.net
- `www.msftconnecttest.com`

E a questi indirizzi IP:

- 20.190.128.0/18
- 40.126.0.0/18

L'accesso a tutti gli URL e gli indirizzi IP elencati in precedenza usa il protocollo HTTPS sulla porta 443.

Quando si esegue il backup di file e cartelle da macchine virtuali di Azure usando l'agente MARS, è necessario configurare anche la rete virtuale di Azure per consentire l'accesso. Se si usano gruppi di sicurezza di rete (NSG), usare il tag del servizio *AzureBackup* per consentire l'accesso in uscita a Backup di Azure. Oltre al tag di Backup di Azure, è necessario consentire la connettività per l'autenticazione e il trasferimento dei dati creando [regole NSG](../virtual-network/network-security-groups-overview.md#service-tags) simili per Azure AD (*AzureActiveDirectory*) e Archiviazione di Azure (*Storage*). I passaggi seguenti descrivono il processo di creazione di una regola per il tag di Backup di Azure:

1. In **Tutti i servizi**, passare a **Gruppi di sicurezza di rete** e selezionare il gruppo di sicurezza di rete.
2. In **Impostazioni** selezionare **Regole di sicurezza in uscita**.
3. Selezionare **Aggiungi**. Immettere tutti i dettagli necessari per la creazione di una nuova regola, come descritto nelle [impostazioni delle regole di sicurezza](../virtual-network/manage-network-security-group.md#security-rule-settings). Assicurarsi che l'opzione **Destinazione** sia impostata su *Tag del servizio* e che l'opzione **Tag del servizio di destinazione** sia impostata su *AzureBackup*.
4. Selezionare **Aggiungi** per salvare la regola di sicurezza in uscita appena creata.

È possibile creare in modo analogo le regole di sicurezza NSG in uscita NSG per Archiviazione di Azure e Azure AD. Per altre informazioni sui tag di servizio, vedere [questo articolo](../virtual-network/service-tags-overview.md).

### <a name="azure-expressroute-support"></a>Azure ExpressRoute supporto

È possibile eseguire il backup dei dati Azure ExpressRoute con peering pubblico (disponibile per circuiti vecchi) e peering Microsoft. Il backup su peering privato non è supportato.

Con peering pubblico: verificare l'accesso ai domini/indirizzi seguenti:

* URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* Indirizzi IP
  * 20.190.128.0/18
  * 40.126.0.0/18

Con il peering Microsoft, selezionare i servizi/aree geografiche seguenti e i valori della community pertinenti:

- Backup di Azure (in base alla posizione dell'insieme di credenziali di Servizi di ripristino)
- Azure Active Directory (12076:5060)
- Archiviazione di Azure (in base alla posizione dell'insieme di credenziali di Servizi di ripristino)

Per altre informazioni, vedere i requisiti [di routing di ExpressRoute.](../expressroute/expressroute-routing.md#bgp)

>[!NOTE]
>Il peering pubblico è deprecato per i nuovi circuiti.

### <a name="private-endpoint-support"></a>Supporto dell'endpoint privato

È ora possibile usare endpoint privati per eseguire il backup sicuro dei dati dai server all'insieme di credenziali di Servizi di ripristino. Poiché Azure Active Directory attualmente non supporta endpoint privati, gli indirizzi IP e i nomi di Azure Active Directory necessari per l'accesso in uscita dovranno essere consentiti separatamente.

Quando si usa l'agente MARS per eseguire il backup delle risorse locali, assicurarsi che alla rete locale (contenente le risorse di cui eseguire il backup) sia associato un peer con la rete virtuale di Azure che contiene un endpoint privato per l'insieme di credenziali. È quindi possibile continuare a installare l'agente MARS e configurare il backup. Tuttavia, è necessario assicurarsi che tutte le comunicazioni per il backup avvengano solo tramite la rete con peer.

Se si rimuovono endpoint privati per l'insieme di credenziali dopo la registrazione di un agente MARS, sarà necessario registrare nuovamente il contenitore con l'insieme di credenziali. Non è necessario arrestare la protezione.

Altre informazioni sugli [endpoint privati per Backup di Azure](private-endpoints.md).

### <a name="throttling-support"></a>Supporto della limitazione della larghezza di banda della rete

**Funzionalità** | **Dettagli**
--- | ---
Controllo della larghezza di banda | Supportata. Nell'agente MARS usare Modifica proprietà **per regolare** la larghezza di banda.
Limitazione della larghezza di banda della rete | Non disponibile per i computer di cui è stato eseguito il backup che eseguono Windows Server 2008 R2, Windows Server 2008 SP2 o Windows 7.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

>[!NOTE]
> L'agente MARS non supporta gli SKU di Windows Server Core.

È possibile usare l'agente MARS per eseguire il backup direttamente in Azure nei sistemi operativi elencati di seguito eseguiti in:

1. Server Windows locali
2. Macchine virtuali di Azure che eseguono Windows

I sistemi operativi devono essere a 64 bit e devono eseguire i Service Pack e gli aggiornamenti più recenti. La tabella seguente riepiloga questi sistemi operativi:

**Sistema operativo** | **File/cartelle** | **Stato del sistema** | **Requisiti software/modulo**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Sì | No |  Controllare la versione del server corrispondente per i requisiti software/modulo
Windows 8.1 (Enterprise, Pro)| Sì |No | Controllare la versione del server corrispondente per i requisiti software/modulo
Windows 8 (Enterprise, Pro) | Sì | No | Controllare la versione del server corrispondente per i requisiti software/modulo
Windows Server 2016 (Standard, Datacenter, Essentials) | Sì | Sì | - .NET 4.5 <br> - Windows PowerShell <br> - Versione più recente compatibile di Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Sì | Sì | - .NET 4.5 <br> - Windows PowerShell <br> - Versione più recente compatibile di Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 (Standard, Datacenter, Foundation) | Sì | Sì |- .NET 4.5 <br> - Windows PowerShell <br> - Versione più recente compatibile di Microsoft VC++ Redistributable <br> - Microsoft Management Console (MMC) 3.0 <br> - Gestione e manutenzione immagini distribuzione (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | Sì | No | - .NET 4.5 <br> - Windows PowerShell <br> - Versione più recente di Microsoft VC++ Redistributable compatibile <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2019 (Standard, Datacenter, Essentials) | Sì | Sì | - .NET 4.5 <br> - Windows PowerShell <br> - Versione più recente di Microsoft VC++ Redistributable compatibile <br> - Microsoft Management Console (MMC) 3.0

Per altre informazioni, vedere [Sistemi operativi MABS e DPM supportati.](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)

### <a name="operating-systems-at-end-of-support"></a>Sistemi operativi al termine del supporto

I sistemi operativi seguenti sono al termine del supporto ed è consigliabile aggiornare il sistema operativo per continuare a rimanere protetti.

Se gli impegni esistenti impediscono l'aggiornamento del sistema operativo, è consigliabile eseguire la migrazione dei server Windows alle macchine virtuali di Azure e sfruttare i backup delle macchine virtuali di Azure per continuare a rimanere protetti. Visitare la [pagina di migrazione qui](https://azure.microsoft.com/migration/windows-server/) per altre informazioni sulla migrazione del server Windows.

Per gli ambienti locali o ospitati, in cui non è possibile aggiornare il sistema operativo o eseguire la migrazione ad Azure, attivare gli aggiornamenti della sicurezza estesa per i computer in modo che continuino a essere protetti e supportati. Si noti che solo edizioni specifiche sono idonee per gli aggiornamenti della sicurezza estesa. Per altre [informazioni, visitare](https://www.microsoft.com/windows-server/extended-security-updates) la pagina delle domande frequenti.

| **Sistema operativo**                                       | **File/cartelle** | **Stato del sistema** | **Requisiti software/modulo**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Sì               | No                 | Controllare la versione del server corrispondente per i requisiti software/modulo |
| Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Foundation) | Sì               | Sì                | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - Compatibile con Microsoft VC++ Redistributable <br>  - Microsoft Management Console (MMC) 3.0 <br>  - Gestione e manutenzione immagini distribuzione (DISM.exe) |
| Windows Server 2008 SP2 (Standard, Datacenter, Foundation)  | Sì               | No                 | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - Compatibile con Microsoft VC++ Redistributable <br>  - Microsoft Management Console (MMC) 3.0 <br>  - Gestione e manutenzione immagini distribuzione (DISM.exe) <br>  - Virtual Server 2005 base + KB948515 |

## <a name="backup-limits"></a>Limiti relativi a Backup

### <a name="size-limits"></a>Limiti di dimensioni

Backup di Azure limita le dimensioni di un'origine dati di file o cartelle di cui è possibile eseguire il backup. Gli elementi di cui è stato fatto il backup da un singolo volume non possono superare le dimensioni riepilogate in questa tabella:

**Sistema operativo** | **Limite dimensioni**
--- | ---
Windows Server 2012 o versioni successive |54400 GB
Windows Server 2008 R2 SP1 |1\.700 GB
Windows Server 2008 SP2| 1\.700 GB
Windows 8 o versione successiva| 54400 GB
Windows 7| 1\.700 GB

### <a name="minimum-retention-limits"></a>Limiti minimi di conservazione

Di seguito sono riportate le durate minime di conservazione che è possibile impostare per i diversi punti di ripristino:

|Punto di ripristino |Duration  |
|---------|---------|
|Punto di ripristino giornaliero    |   7 giorni      |
|Punto di ripristino settimanale     |    4 settimane     |
|Punto di ripristino mensile    |   3 mesi      |
|Punto di ripristino annuale  |      1 anno   |

### <a name="other-limitations"></a>Altre limitazioni

- MARS non supporta la protezione di più computer con lo stesso nome in un singolo insieme di credenziali.

## <a name="supported-file-types-for-backup"></a>Tipi di file supportati per il backup

**Tipo** | **Supporto**
--- | ---
Crittografato<sup>*</sup>| Supportata.
Compresso | Supportata.
Sparse | Supportata.
Compresso e sparse |Supportata.
Collegamenti reali| Non supportata. Saltato.
Reparse point| Non supportata. Saltato.
Crittografato e sparse |Non supportata. Saltato.
Flusso compresso| Non supportata. Saltato.
Flusso di tipo sparse| Non supportata. Saltato.
OneDrive (i file sincronizzati sono flussi di tipo sparse)| Non supportata.
Cartelle con Replica DFS abilitate | Non supportata.

\* Assicurarsi che l'agente mars abbia accesso ai certificati necessari per accedere ai file crittografati. I file inaccessibili verranno ignorati.

## <a name="supported-drives-or-volumes-for-backup"></a>Unità o volumi supportati per il backup

**Unità/volume** | **Supporto** | **Dettagli**
--- | --- | ---
Volumi di sola lettura| Non supportato | Il Servizio Copia Shadow del volume funziona solo se il volume è scrivibile.
Volumi offline| Non supportato |VsS funziona solo se il volume è online.
Condivisione di rete| Non supportato |Il volume deve essere locale nel server.
Volumi bloccati da BitLocker| Non supportato |Il volume deve essere sbloccato prima dell'avvio del backup.
Identificazione del file system| Non supportato |È supportato solo NTFS.
Supporti rimovibili| Non supportato |Tutte le origini degli elementi di backup devono avere *uno stato* fisso.
Unità deduplicate | Supportato | Il servizio Backup di Azure converte i dati deduplicati in dati normali. Ottimizza, crittografa, archivia e invia i dati all'insieme di credenziali.

## <a name="support-for-initial-offline-backup"></a>Supporto per il backup offline iniziale

Backup di Azure il *seeding offline* per trasferire i dati di backup iniziali in Azure tramite dischi. Questo supporto è utile se è probabile che il backup iniziale sia compreso nell'intervallo di dimensioni di terabyte (TB). Il backup offline è supportato nei seguenti casi:

- Backup diretto di file e cartelle in computer locali che eseguono l'agente MARS.
- Backup di carichi di lavoro e file da un server DPM o da un server di Backup di Microsoft Azure.

Il backup offline non può essere usato per i file di stato del sistema.

## <a name="support-for-data-restoration"></a>Supporto per il ripristino dei dati

Usando la funzionalità [ripristino istantaneo](backup-instant-restore-capability.md) di Backup di Azure, è possibile ripristinare i dati prima che venga copiato nell'insieme di credenziali. Il computer di cui si esegue il backup deve essere in esecuzione .NET Framework 4.5.2 o versione successiva.

I backup non possono essere ripristinati in un computer di destinazione che esegue una versione precedente del sistema operativo. Ad esempio, un backup eseguito da un computer che esegue Windows 7 può essere ripristinato in Windows 8 o versioni successive. Ma un backup eseguito da un computer che esegue Windows 8 non può essere ripristinato in un computer che esegue Windows 7.

## <a name="previous-mars-agent-versions"></a>Versioni precedenti dell'agente MARS

Nella tabella seguente sono elencate le versioni precedenti dell'agente con i relativi collegamenti di download. È consigliabile aggiornare la versione dell'agente alla versione più recente, in modo da poter sfruttare le funzionalità più recenti e prestazioni ottimali.

**Versions** (Versioni) | **Articoli correlati**
--- | ---
[2.0.9145.0](https://download.microsoft.com/download/4/5/E/45EB38B4-2DA7-45FA-92E1-5CA1E23D18D1/MARSAgentInstaller.exe) | Non disponibile
[2.0.9151.0](https://download.microsoft.com/download/7/1/7/7177B70A-51E8-434D-BDF2-FA3A09E917D6/MARSAgentInstaller.exe) | Non disponibile
[2.0.9153.0](https://download.microsoft.com/download/3/D/D/3DD8A2FF-AC48-4A62-8566-B2C05F0BCCD0/MARSAgentInstaller.exe) | Non disponibile
[2.0.9162.0](https://download.microsoft.com/download/0/1/0/010E598E-6289-47DB-872A-FFAF5030E6BE/MARSAgentInstaller.exe) | Non disponibile
[2.0.9169.0](https://download.microsoft.com/download/f/7/1/f716c719-24bc-4337-af48-113baddc14d8/MARSAgentInstaller.exe) | [4515971](https://support.microsoft.com/help/4538314)
[2.0.9170.0](https://download.microsoft.com/download/1/8/7/187ca9a9-a6e5-45f0-928f-9a843d84aed5/MARSAgentInstaller.exe) | Non disponibile
[2.0.9173.0](https://download.microsoft.com/download/7/9/2/79263a35-de87-4ba6-9732-65563a4274b6/MARSAgentInstaller.exe) | [4538314](https://support.microsoft.com/help/4538314)
[2.0.9177.0](https://download.microsoft.com/download/3/0/4/304d3cdf-b123-42ee-ad03-98fb895bc38f/MARSAgentInstaller.exe) | Non disponibile
[2.0.9181.0](https://download.microsoft.com/download/6/6/9/6698bc49-e30b-4a3e-a1f4-5c859beafdcc/MARSAgentInstaller.exe) | Non disponibile
[2.0.9190.0](https://download.microsoft.com/download/a/c/e/aceffec0-794e-4259-8107-92a3f6c10f55/MARSAgentInstaller.exe) | [4575948](https://support.microsoft.com/help/4575948)
[2.0.9195.0](https://download.microsoft.com/download/6/1/3/613b70a7-f400-4806-9d98-ae26aeb70be9/MARSAgentInstaller.exe) | [4582474](https://support.microsoft.com/help/4582474)
[2.0.9197.0](https://download.microsoft.com/download/2/7/5/27531ace-3100-43bc-b4af-7367680ea66b/MARSAgentInstaller.exe) | [4589598](https://support.microsoft.com/help/4589598)
[2.0.9207.0](https://download.microsoft.com/download/b/5/a/b5a29638-1cef-4906-b704-4d3d914af76e/MARSAgentInstaller.exe) | [5001305](https://support.microsoft.com/help/5001305)

>[!NOTE]
>Le versioni dell'agente MARS con miglioramenti secondari dell'affidabilità e delle prestazioni non dispongono di un articolo della Knowledge Base.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni [sull'architettura di backup che usa l'agente MARS.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
- Informazioni sulle funzionalità supportate quando si esegue l'agente [MARS in MABS o in un server DPM.](backup-support-matrix-mabs-dpm.md)
