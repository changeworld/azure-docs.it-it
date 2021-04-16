---
title: Backup di Azure matrice di supporto per SQL Server backup in macchine virtuali di Azure
description: Fornisce un riepilogo delle impostazioni di supporto e delle limitazioni quando si esegue il backup di SQL Server nelle macchine virtuali di Azure con il Backup di Azure servizio.
ms.topic: conceptual
ms.date: 04/07/2021
ms.custom: references_regions
ms.openlocfilehash: bcbac4f6a91ad77d21eb6274aa03d251b8fbfe7c
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515057"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Matrice di supporto per SQL Server backup in macchine virtuali di Azure

È possibile usare Backup di Azure per eseguire il backup SQL Server database in macchine virtuali di Azure ospitate nella Microsoft Azure cloud. Questo articolo riepiloga le impostazioni di supporto generale e le limitazioni per scenari e distribuzioni di SQL Server backup in macchine virtuali di Azure.

## <a name="scenario-support"></a>Supporto degli scenari

**Supporto** | **Dettagli**
--- | ---
**Distribuzioni supportate** | Sono supportate VM di Azure del Marketplace SQL e non del Marketplace (SQL Server installato manualmente).
**Aree supportate** | Australia sud-orientale (ASE), Australia orientale (AE), Australia centrale (AC), Australia centrale 2 (AC) <br> Brasile meridionale (BRS)<br> Canada centrale (CNC), Canada orientale (CE)<br> Asia sud-orientale (SEA), Asia orientale (EA) <br> Stati Uniti orientali (EUS), Stati Uniti orientali 2 (EUS2), Stati Uniti centro-occidentali (WCUS), Stati Uniti occidentali (WUS), Stati Uniti occidentali 2 (WUS 2), Stati Uniti centro-settentrionali (NCUS), Stati Uniti centrali (CUS), Stati Uniti centro-meridionali (SCUS) <br> India centrale (INC), India meridionale (INS), India occidentale <br> Giappone orientale (JPE), Giappone occidentale (JPW) <br> Corea centrale (KRC), Corea meridionale (KRS) <br> Europa settentrionale (NE), Europa occidentale <br> Regno Unito meridionale (UKS), Regno Unito occidentale (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Texas, US DoD (area centrale), US DoD (area orientale) <br> Germania settentrionale, Germania centro-occidentale <br> Svizzera settentrionale, Svizzera occidentale <br> Francia centrale <br> Cina orientale, Cina orientale 2, Cina settentrionale, Cina settentrionale 2
**Sistemi operativi supportati** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux non è attualmente supportato.
**Versioni di SQL Server supportate** | SQL Server 2019, SQL Server 2017 come indicato nella [pagina Ricerca nel ciclo di vita del supporto](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 e SP come indicato nella [pagina Ricerca nel ciclo di vita del supporto](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.<br><br>Le versioni di Express Local DB non sono supportate.
**Versioni di .NET supportate** | .NET Framework 4.5.2 o versioni successive installato nella macchina virtuale

## <a name="feature-considerations-and-limitations"></a>Considerazioni e limitazioni sulle funzionalità

|Impostazione  |Limite massimo |
|---------|---------|
|Numero di database che possono essere protetti in un server (e in un insieme di credenziali)    |   2000      |
|Dimensioni del database supportate (oltre a questo, potrebbero verificarsi problemi di prestazioni)   |   6 TB*      |
|Numero di file supportati in un database    |   1000      |

_*Il limite delle dimensioni del database dipende dalla velocità di trasferimento dei dati supportate e dalla configurazione del limite di tempo di backup. Non è il limite rigido. [Altre informazioni sulle](#backup-throughput-performance) prestazioni della velocità effettiva di backup._

* Il backup di SQL Server può essere configurato nel portale di Azure o in **PowerShell**. L'interfaccia della riga di comando non è supportata.
* La soluzione è supportata in entrambe le tipologie di [distribuzione](../azure-resource-manager/management/deployment-models.md): macchine virtuali di Azure Resource Manager e macchine virtuali classiche.
* Sono supportati tutti i tipi di backup (completo/differenziale/log) e i modelli di recupero (registrazione semplice/completa/con registrazione bulk).
* Per **i database di sola** lettura: i backup completi e di sola copia sono gli unici tipi di backup supportati.
* La compressione nativa SQL è supportata se abilitata in modo esplicito dall'utente nei criteri di backup. Backup di Azure esegue l'override delle impostazioni predefinite a livello di istanza con la clausola COMPRESSION/NO_COMPRESSION, a seconda del valore di questo controllo impostato dall'utente.
* TDE: è supportato il backup del database abilitato. Per ripristinare un database crittografato con TDE in un altro SQL Server, è necessario innanzitutto ripristinare il [certificato nel server di destinazione.](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server) È disponibile la compressione dei backup per i database abilitati per TDE SQL Server 2016 e versioni successive, ma con dimensioni di trasferimento inferiori, come illustrato [qui.](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593)
* Le operazioni di backup e ripristino per i database mirror e gli snapshot di database non sono supportate.
* SQL Server'istanza **del cluster** di failover non è supportata.
* L'uso di più soluzioni di backup per eseguire il backup dell'istanza SQL Server o del gruppo di disponibilità SQL Always On autonomo può causare un errore di backup. Evitare di farlo. Anche il backup di due nodi di un gruppo di disponibilità singolarmente con soluzioni uguali o differenti potrebbe generare errori.
* Quando i gruppi di disponibilità sono configurati, i backup vengono esere esere dai diversi nodi in base ad alcuni fattori. Il comportamento di backup per un gruppo di disponibilità è riepilogato di seguito.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Comportamento di backup con i gruppi di disponibilità AlwaysOn

È consigliabile configurare il backup in un solo nodo di un gruppo di disponibilità. Configurare sempre il backup nella stessa area del nodo primario. In altre parole, è sempre necessario che il nodo primario sia presente nell'area in cui si sta configurando il backup. Se tutti i nodi del gruppo di disponibilità si trova nella stessa area in cui è configurato il backup, non c'è alcun problema.

#### <a name="for-cross-region-ag"></a>Per i gruppi di disponibilità tra aree

* Indipendentemente dalla preferenza di backup, i backup verranno eseguiti solo dai nodi che si trova nella stessa area in cui è configurato il backup. Ciò è dovuto al fatto che i backup tra aree non sono supportati. Se sono presenti solo due nodi e il nodo secondario si trova nell'altra area, i backup continueranno a essere eseguiti dal nodo primario, a meno che la preferenza di backup non sia "solo secondaria".
* Se viene eseguito il failover di un nodo in un'area diversa da quella in cui è configurato il backup, i backup avranno esito negativo sui nodi nell'area di cui è stato eseguito il failover.

In base alle preferenze e ai tipi di backup (completo/differenziale/log/completo solo copia), i backup vengono eseguiti da uno specifico nodo (primario o secondario).

#### <a name="backup-preference-primary"></a>Preferenza di backup: Primaria

**Tipo di backup** | **Node**
--- | ---
Full | Principale
Differenziale | Principale
Log |  Principale
Completo solo copia |  Principale

#### <a name="backup-preference-secondary-only"></a>Preferenza di backup: Solo secondario

**Tipo di backup** | **Node**
--- | ---
Full | Principale
Differenziale | Principale
Log |  Secondari
Completo solo copia |  Secondari

#### <a name="backup-preference-secondary"></a>Preferenza di backup: Secondario

**Tipo di backup** | **Node**
--- | ---
Full | Principale
Differenziale | Principale
Log |  Secondari
Completo solo copia |  Secondari

#### <a name="no-backup-preference"></a>Nessuna preferenza di backup

**Tipo di backup** | **Node**
--- | ---
Full | Principale
Differenziale | Principale
Log |  Secondari
Completo solo copia |  Secondari

## <a name="backup-throughput-performance"></a>Prestazioni della velocità effettiva del backup

Backup di Azure supporta una velocità di trasferimento dati coerente di 200 Mbps per backup completi e differenziali di database SQL di grandi dimensioni (di 500 GB). Per usare le prestazioni ottimali, assicurarsi che:

- La macchina virtuale sottostante (contenente l'SQL Server, che ospita il database, è configurata con la velocità effettiva di rete richiesta. Se la velocità effettiva massima della macchina virtuale è inferiore a 200 Mbps, Backup di Azure possibile trasferire i dati alla velocità ottimale.<br></br>Inoltre, per il disco che contiene i file di database deve essere disponibile una velocità effettiva sufficiente. [Altre informazioni sulla](../virtual-machines/disks-performance.md) velocità effettiva e sulle prestazioni del disco nelle macchine virtuali di Azure. 
- I processi in esecuzione nella macchina virtuale non utilizzano la larghezza di banda della macchina virtuale. 
- Le pianificazioni dei backup vengono distribuite in un subset di database. Più backup in esecuzione simultaneamente in una macchina virtuale condividono la frequenza di utilizzo della rete tra i backup. [Altre informazioni](faq-backup-sql-server.yml#can-i-control-how-many-concurrent-backups-run-on-the-sql-server-) su come controllare il numero di backup simultanei.

>[!NOTE]
> [Scaricare Resource Planner dettagliato per calcolare](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) il numero approssimativo di database protetti consigliati per ogni server in base alle risorse della macchina virtuale, alla larghezza di banda e ai criteri di backup.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [eseguire il backup di SQL Server database](backup-azure-sql-database.md) in esecuzione in una macchina virtuale di Azure.