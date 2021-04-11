---
title: Matrice di protezione MAB (server di Backup di Azure) v3 UR1
description: Questo articolo offre una matrice di supporto che elenca tutti i carichi di lavoro, i tipi di dati e le installazioni che vengono protetti dal server di Backup di Azure.
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: cfdd227135a2124e22a604bad4bd41594a38fb37
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561271"
---
# <a name="mabs-azure-backup-server-v3-ur1-protection-matrix"></a>Matrice di protezione MAB (server di Backup di Azure) v3 UR1

Questo articolo elenca i vari server e carichi di lavoro che è possibile proteggere con il server di Backup di Azure. La matrice seguente elenca cosa è possibile proteggere con il server di Backup di Azure.

Usare la matrice seguente per la UR1 di MAB V3:

* Carichi di lavoro - Tipo di carico di lavoro della tecnologia.

* Versione: versione di MAB supportata per i carichi di lavoro.

* Installazione di MAB: il computer o la posizione in cui si vuole installare MAB.

* Protezione e ripristino - Informazioni dettagliate sui carichi di lavoro, ad esempio il contenitore di archiviazione supportato o la distribuzione supportata.

>[!NOTE]
>Il supporto per l'agente protezione a 32 bit è deprecato con MAB V3 UR1. Vedere [deprecazione dell'agente protezione 32 bit](backup-mabs-whats-new-mabs.md#32-bit-protection-agent-deprecation).

## <a name="protection-support-matrix"></a>Matrice di supporto di protezione

Le sezioni seguenti illustrano in dettaglio la matrice di supporto per la protezione di MAB:

* [Backup di applicazioni](#applications-backup)
* [Backup di macchine virtuali](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>Backup di applicazioni

| **Carico di lavoro**               | **Versione**                                                  | **Installazione server di Backup di Azure**                       | **server di Backup di Azure supportati** | **Protezione e ripristino**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| Computer client (64 bit) | Windows 10                                                  | Server fisico  <br><br>    Macchina virtuale Hyper-V   <br><br>   Macchina virtuale VMware | UR1 V3                            | Volume, condivisione, cartella, file, volumi deduplicati   <br><br>   I volumi protetti devono essere formattati con NTFS. Le tabelle FAT e FAT32 non sono supportate.  <br><br>    I volumi devono essere almeno di 1 GB. Server di Backup di Azure utilizza Servizio Copia Shadow del volume (VSS) per eseguire lo snapshot dei dati e lo snapshot funziona solo se il volume è di almeno 1 GB. |
| Server (64 bit)          | Windows Server 2019, 2016, 2012 R2, 2012                    | Macchina virtuale di Azure (quando il carico di lavoro è in esecuzione come macchina virtuale di Azure)  <br><br>    Server fisico  <br><br>    Macchina virtuale Hyper-V <br><br>     Macchina virtuale VMware  <br><br>    Azure Stack | UR1 V3                            | Volume, condivisione, cartella, file <br><br>    Volumi deduplicati (solo NTFS) <br><br>Quando si protegge un volume di WS 2016 NTFS deduplicato con MAB V3 in esecuzione in Windows Server 2019, è possibile che i recuperi siano interessati. È stata rilevata una correzione per eseguire i recuperi in modo non duplicato che farà parte delle versioni successive di MAB. Se questa correzione è necessaria in MAB V3 UR1, contattare il supporto di MAB.<br><br> Quando si protegge un volume di WS 2019 NTFS deduplicato con MAB V3 in Windows Server 2016, i backup e i ripristini non saranno deduplicati. Ciò significa che i backup utilizzeranno più spazio sul server MAB rispetto al volume NTFS originale deduplicato.   <br><br>   Stato del sistema e bare metal (non supportato quando il carico di lavoro è in esecuzione come macchina virtuale di Azure) |
| Server (64 bit)          | Windows Server 2008 R2 SP1, Windows Server 2008 SP2 (è necessario installare [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)) | Server fisico  <br><br>    Macchina virtuale Hyper-V  <br><br>      Macchina virtuale VMware  <br><br>   Azure Stack | UR1 V3                            | Volume, condivisione, cartella, file, stato del sistema/bare metal        |
| SQL Server                | SQL Server 2019, 2017, 2016 e [SPS supportati](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016), 2014 e [SPS](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) supportati | Server fisico  <br><br>     Macchina virtuale Hyper-V   <br><br>     Macchina virtuale VMware  <br><br>   Macchina virtuale Azure (quando il carico di lavoro viene eseguito come macchina virtuale Azure)  <br><br>     Azure Stack | UR1 V3                            | Tutti gli scenari di distribuzione: database       <br><br>  MAB V3 UR1 supporta il backup dei database SQL su volumi ReFS     <br><br>     MAB non supporta SQL Server database ospitati in Windows Server 2012 Scale-Out file server (SOFS). <br><br>   MAB non è in grado di proteggere il gruppo di disponibilità distribuito (DAG) o il gruppo di disponibilità (AG) di SQL Server, in cui il nome del ruolo nel cluster di failover è diverso dal gruppo di disponibilità specificato in SQL.       |
| Exchange                   | Exchange 2019, 2016                                         | Server fisico   <br><br>   Macchina virtuale Hyper-V  <br><br>      Macchina virtuale VMware  <br><br>   Azure Stack  <br><br>    Macchina virtuale Azure (quando il carico di lavoro viene eseguito come macchina virtuale Azure) | UR1 V3                            | Proteggi (tutti gli scenari di distribuzione): Exchange Server autonomo, database in un gruppo di disponibilità del database (DAG)  <br><br>    Ripristino (tutti gli scenari di distribuzione): cassetta postale, database delle cassette postali in un gruppo di disponibilità dei database    <br><br>  Il backup di Exchange su ReFS è supportato con MAB V3 UR1 |
| SharePoint                 | SharePoint 2019, 2016 con SPs più recente                       | Server fisico  <br><br>    Macchina virtuale Hyper-V <br><br>    Macchina virtuale VMware  <br><br>   Macchina virtuale Azure (quando il carico di lavoro viene eseguito come macchina virtuale Azure)   <br><br>   Azure Stack | UR1 V3                            | Proteggi (tutti gli scenari di distribuzione): Farm, contenuto del server Web front-end  <br><br>    Ripristino (tutti gli scenari di distribuzione): Farm, database, applicazione Web, file o elemento elenco, ricerca SharePoint, server Web front-end  <br><br>    La protezione di una farm di SharePoint che usa la funzionalità AlwaysOn SQL Server 2012 per i database del contenuto non è supportata. |

## <a name="vm-backup"></a>Backup di macchine virtuali

| **Carico di lavoro**                                                 | **Versione**                                             | **Installazione server di Backup di Azure**                      | **server di Backup di Azure supportati** | **Protezione e ripristino**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Host Hyper-V-agente protezione MAB in server host Hyper-V, cluster o macchina virtuale | Windows Server 2019, 2016, 2012 R2, 2012               | Server fisico  <br><br>    Macchina virtuale Hyper-V <br><br>    Macchina virtuale VMware | UR1 V3                             | Protezione: computer Hyper-V, volumi condivisi cluster (CSVs)  <br><br>    Ripristino: macchina virtuale, ripristino a livello di elemento di file e cartelle disponibile solo per Windows, volumi, dischi rigidi virtuali |
| Macchine virtuali VMware                                                  | Server VMware 5,5, 6,0 o 6,5, 6,7 (versione con licenza) | Macchina virtuale Hyper-V  <br><br>   Macchina virtuale VMware         | UR1 V3                             | Proteggere: macchine virtuali VMware in volumi condivisi cluster (CSVs), NFS e archiviazione SAN   <br><br>     Ripristino: macchina virtuale, ripristino a livello di elemento di file e cartelle disponibile solo per Windows, volumi, dischi rigidi virtuali <br><br>    VMware vApp non è supportato. |

>[!NOTE]
> MAB non supporta il backup di macchine virtuali con dischi pass-through o quelli che usano un VHD remoto. In questi scenari è consigliabile usare il backup a livello di Guest con MAB e installare un agente nella macchina virtuale per eseguire il backup dei dati.

## <a name="linux"></a>Linux

| **Carico di lavoro** | **Versione**                               | **Installazione server di Backup di Azure**                      | **server di Backup di Azure supportati** | **Protezione e ripristino**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | Linux in esecuzione come Guest [Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) o [VMware](backup-azure-backup-server-vmware.md) | Server fisico, macchina virtuale Hyper-V locale, VM Windows in VMware | UR1 V3                             | Hyper-V deve essere in esecuzione in Windows Server 2012 R2, Windows Server 2016 o Windows Server 2019. Proteggi: intera macchina virtuale   <br><br>   Ripristino: intera macchina virtuale   <br><br>    Sono supportati solo snapshot coerenti a livello di file.    <br><br>   Per un elenco completo delle distribuzioni e delle versioni di Linux supportate, vedere l'articolo [relativo alle distribuzioni di Linux approvate da Azure](../virtual-machines/linux/endorsed-distros.md). |

## <a name="azure-expressroute-support"></a>Supporto di Azure ExpressRoute

È possibile eseguire il backup dei dati tramite Azure ExpressRoute con il peering pubblico (disponibile per i circuiti precedenti) e il peering Microsoft. Il backup sul peering privato non è supportato.

Con peering pubblico: garantire l'accesso ai seguenti domini/indirizzi:

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

Con il peering Microsoft selezionare i servizi/le aree e i valori della community pertinenti seguenti:

* Azure Active Directory (12076:5060)
* Area Microsoft Azure (in base alla posizione dell'insieme di credenziali di servizi di ripristino)
* Archiviazione di Azure (in base alla posizione dell'insieme di credenziali di servizi di ripristino)

Per ulteriori informazioni, vedere i [requisiti di routing di ExpressRoute](../expressroute/expressroute-routing.md).

>[!NOTE]
>Il peering pubblico è deprecato per i nuovi circuiti.

## <a name="cluster-support"></a>Supporto di cluster

Il server di Backup di Azure può proteggere i dati nelle applicazioni in cluster seguenti:

* File server

* SQL Server

* Hyper-V: se si protegge un cluster Hyper-V usando l'agente protezione MAB con scalabilità orizzontale, non è possibile aggiungere la protezione secondaria per i carichi di lavoro di Hyper-V protetti.

* Exchange Server - Il server di Backup di Azure può proteggere i cluster di dischi non condivisi per le versioni di Exchange Server supportate (replica continua cluster) e può inoltre proteggere Exchange Server configurato per la replica continua locale.

* SQL Server - Il server di Backup di Azure non supporta il backup dei database di SQL Server ospitati in volumi condivisi cluster (CSV).

>[!NOTE]
>MAB supporta solo la protezione delle macchine virtuali Hyper-V in volumi condivisi cluster (CSVs). La protezione di altri carichi di lavoro ospitati in Volumi condivisi cluster non è supportata.

Server di Backup di Azure possibile proteggere i carichi di lavoro del cluster che si trovano nello stesso dominio del server MAB e in un dominio figlio o trusted. Per proteggere le origini dati in domini o gruppi di lavoro non attendibili, usare l'autenticazione di NTLM o del certificato per un singolo server, o l'autenticazione del certificato solo per un cluster.

## <a name="data-protection-issues"></a>Problemi relativi alla protezione dati

* Non è possibile eseguire il backup di macchine virtuali usando unità condivise (potenzialmente collegate ad altre VM) perché la VSS writer Hyper-V non può eseguire il backup di volumi di cui è stato eseguito il backup da dischi rigidi virtuali condivisi.

* quando si protegge una cartella condivisa, il relativo percorso contiene il percorso logico del volume. Se la cartella condivisa viene spostata, la protezione avrà esito negativo. per spostare una cartella condivisa e protetta, rimuoverla dal relativo gruppo protezione dati, quindi aggiungerla alla protezione dopo aver eseguito lo spostamento.  Inoltre, se si modifica il percorso di un'origine dati protetta in un volume che utilizza il Encrypting File System (EFS) e il nuovo percorso del file supera i 5120 caratteri, la protezione dei dati avrà esito negativo.

* non è possibile cambiare il dominio di un computer protetto e continuare a usare la protezione senza interruzioni. Inoltre, non è possibile modificare il dominio di un computer protetto e associare le repliche e i punti di ripristino esistenti al computer quando viene riprotetto. Se è necessario modificare il dominio di un computer protetto, disabilitare prima la protezione per le origini dati nel computer. Proteggere quindi l'origine dati nel computer dopo avere impostato un nuovo dominio.

* non è possibile cambiare il nome di un computer protetto e continuare a usare la protezione senza interruzioni. Inoltre, non è possibile modificare il nome di un computer protetto e associare le repliche e i punti di ripristino esistenti al computer quando viene riprotetto. Se è necessario modificare il nome di un computer protetto, disabilitare prima la protezione per le origini dati nel computer. Proteggere quindi l'origine dati nel computer dopo avere impostato un nuovo nome.

* MAb identifica automaticamente il fuso orario di un computer protetto durante l'installazione dell'agente protezione. Se dopo la configurazione della protezione un computer protetto viene spostato in un fuso orario diverso, assicurarsi di cambiare l'ora del computer nel Pannello di controllo. Aggiornare quindi il fuso orario nel database di MAB.

* MAB può proteggere i carichi di lavoro nello stesso dominio del server MAB oppure nei domini figlio e trusted. È anche possibile proteggere i carichi di lavoro seguenti in gruppi di lavoro e domini non trusted usando l'autenticazione NTLM o del certificato:

  * SQL Server
  * File Server
  * Hyper-V

  Questi carichi di lavoro possono essere eseguiti in un singolo server o in una configurazione cluster. Per proteggere un carico di lavoro che non si trova in un dominio trusted, vedere [preparare i computer in gruppi di lavoro e domini non attendibili](/system-center/dpm/prepare-environment-for-dpm) per informazioni dettagliate sugli elementi supportati e sull'autenticazione necessaria.

## <a name="unsupported-data-types"></a>Tipi di dati non supportati

MAB non supporta la protezione dei tipi di dati seguenti:

* Collegamenti reali

* Reparse point, compresi i collegamenti DFS e i punti di giunzione

* Metadati dei punti di montaggio: è possibile che un gruppo protezione dati contenga dati con punti di montaggio. In questo caso DPM protegge il volume montato che costituisce la destinazione del punto di montaggio, ma non i relativi metadati. Quando si ripristinano dati contenenti punti di montaggio, è necessario ricreare manualmente la gerarchia dei punti di montaggio.

* Dati in volumi montati all'interno di volumi montati

* Cestino

* File di paging

* Cartella System Volume Information. Per proteggere le informazioni di sistema per un computer, è necessario selezionare lo stato del sistema del computer come membro del gruppo Proteggi.

* Volumi non NTFS

* File che contengono collegamenti reali o simbolici da Windows Vista.

* Dati nelle condivisioni file che ospitano UPD (dischi dei profili utente)

* File con una qualsiasi delle seguenti combinazioni di attributi:

  * Crittografia e analisi

  * Crittografia e Single Instance Storage (SIS)

  * Crittografia e riconoscimento maiuscole/minuscole

  * Crittografia e di tipo sparse

  * Riconoscimento maiuscole/minuscole e SIS

  * Compressione e SIS

## <a name="next-steps"></a>Passaggi successivi

* [Matrice di supporto per il backup con Backup di Microsoft Azure server o System Center DPM](backup-support-matrix-mabs-dpm.md)