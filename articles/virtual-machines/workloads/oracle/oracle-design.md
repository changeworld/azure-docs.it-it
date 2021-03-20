---
title: Progettare e implementare un database Oracle in Azure | Microsoft Docs
description: Progettare e implementare un database Oracle nell'ambiente Azure.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 12/17/2020
ms.author: kegorman
ms.reviewer: tigorman
ms.openlocfilehash: 6e59d0065dfa74979bf3bbc72458bda516e3b641
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101669976"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Progettare e implementare un database Oracle in Azure

## <a name="assumptions"></a>Presupposti

- Si sta pianificando la migrazione di un database Oracle da locale ad Azure.
- Si ha il [pacchetto di diagnostica](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) o il [repository del carico di lavoro automatico](https://www.oracle.com/technetwork/database/manageability/info/other-manageability/wp-self-managing-database18c-4412450.pdf) per i Oracle database si sta cercando di eseguire la migrazione
- È possibile comprendere le diverse metriche in Oracle.
- Si ha una conoscenza di base delle prestazioni delle applicazioni e dell'utilizzo della piattaforma.

## <a name="goals"></a>Obiettivi

- Sapere come ottimizzare la distribuzione di Oracle in Azure.
- Esplorare le opzioni di ottimizzazione delle prestazioni per un database Oracle in un ambiente Azure.
- Hanno una chiara previsione tra i limiti di ottimizzazione fisica tramite l'architettura e i vantaggi o l'ottimizzazione logica del codice del database, (SQL) e la progettazione complessiva del database.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Differenze tra un'implementazione locale e in Azure 

Di seguito sono descritti alcuni importanti elementi da tenere in considerazione quando si esegue la migrazione di applicazioni locali ad Azure. 

Una differenza importante è che in un'implementazione in Azure le risorse, ad esempio VM, dischi e reti virtuali, vengono condivise con gli altri client. Le risorse possono anche essere limitate in base ai requisiti. Invece di focalizzarsi sull'evitare gli errori (MTBF), Azure è più orientato alla sopravvivenza agli errori (MTTR).

La tabella seguente elenca alcune differenze tra un'implementazione locale e un'implementazione in Azure di un database Oracle.


|  | Implementazione locale | Implementazione in Azure |
| --- | --- | --- |
| **Funzionalità di rete** |LAN/WAN  |SDN (Software Defined Networking)|
| **Gruppo di sicurezza** |Strumenti di restrizione per indirizzi IP/porte |[Gruppo di sicurezza di rete (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
| **Resilienza** |MTBF (tempo medio tra gli errori) |MTTR (tempo medio per il ripristino)|
| **Manutenzione pianificata** |Applicazione di patch/aggiornamenti|[Set di disponibilità](/previous-versions/azure/virtual-machines/windows/infrastructure-example) (applicazione di patch/aggiornamenti gestita da Azure) |
| **Risorsa** |Dedicato  |Condivisa con altri client|
| **Aree** |Data center |[Coppie di aree](../../regions.md#region-pairs)|
| **Archiviazione** |Dischi fisici/SAN |[Archiviazione gestita da Azure](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
| **Ridimensiona** |Scalabilità verticale |Scalabilità orizzontale|


### <a name="requirements"></a>Requisiti

- Determinare l'utilizzo effettivo della CPU, perché Oracle è concesso in licenza da Core, il dimensionamento delle esigenze di vCPU può essere un esercizio essenziale per ridurre i costi. 
- Determinare le dimensioni del database, l'archiviazione dei backup e il tasso di crescita.
- Determinare i requisiti di i/o, che è possibile stimare in base ai report di Oracle Statspack e AWR o dagli strumenti di monitoraggio dell'archiviazione a livello di sistema operativo.

## <a name="configuration-options"></a>Opzioni di configurazione

Esistono quattro potenziali aree che è possibile ottimizzare per migliorare le prestazioni in un ambiente Azure:

- Dimensioni della macchina virtuale
- Velocità effettiva della rete
- Tipi di disco e configurazioni
- Impostazioni della cache su disco

### <a name="generate-an-awr-report"></a>Generare un report AWR

Se si dispone di un database Oracle Enterprise Edition esistente e si prevede di eseguire la migrazione ad Azure, sono disponibili diverse opzioni. Se si dispone del [pacchetto di diagnostica](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) per le istanze di Oracle, è possibile eseguire il report AWR di Oracle per ottenere le metriche (IOPS, Mbps, GiBs e così via). Per i database senza la licenza del pacchetto di diagnostica o per un database Standard Edition, è possibile raccogliere le stesse metriche importanti con un report Statspack dopo la raccolta degli snapshot manuali.  La differenza principale tra questi due metodi di creazione di report è che AWR viene automaticamente raccolto e fornisce altre informazioni sul database rispetto all'opzione di segnalazione predecessore di Statspack.

È possibile valutare se eseguire il report AWR durante i carichi di lavoro sia normali che di picco, per poter effettuare un confronto. Per raccogliere il carico di lavoro più accurato, prendere in considerazione un report finestra esteso di una settimana, rispetto a una esecuzione di 24 ore e tenere presente che AWR fornisce medie come parte dei calcoli nel report.  Per la migrazione di un Data Center, è consigliabile raccogliere i report per il dimensionamento nei sistemi di produzione e stimare le copie di database rimanenti usate per test, test, sviluppo e così via, in base alle percentuali (UAT uguale a produzione, test e sviluppo 50% del dimensionamento di produzione e così via).

Per impostazione predefinita, il repository AWR conserva 8 giorni di dati e acquisisce snapshot a intervalli orari.  Per eseguire un report AWR dalla riga di comando, è possibile eseguire le operazioni seguenti da un terminale:

```bash
$ sqlplus / as sysdba
SQL> @$ORACLE_HOME/rdbms/admin/awrrpt.sql;
```

### <a name="key-metrics"></a>Metriche principali

Il report richiederà le seguenti informazioni:
- Tipo di report: HTML o testo (HTML in 12,1 e fornisce informazioni aggiuntive rispetto al formato del testo).
- Il numero di giorni di snapshot da visualizzare (per intervalli di un'ora, un report di una settimana sarebbe un 168 diverso negli ID snapshot)
- SnapshotID iniziale per la finestra del report.
- SnapshotId finale per la finestra del report.
- Nome del report che deve essere creato dallo script AWR.

Se si esegue AWR in un cluster di applicazioni reale, (RAC) il report della riga di comando è awrgrpt. SQL anziché awrrpt. SQL.  Il report "g" creerà un report per tutti i nodi del database RAC in un singolo report e in cui è necessario eseguirne uno in ogni nodo RAC.

Di seguito sono indicate le metriche che è possibile ottenere dal report AWR:

- Nome del database, nome dell'istanza e nome host
- Versione del database (supporto per Oracle)
- CPU/Core
- SGA/PGA (e Advisor per comunicare se è sottodimensionato)
- Memoria totale in GB
- % CPU occupato
- CPU database
- IOPs (lettura/scrittura)
- MBPs (lettura/scrittura)
- Velocità effettiva della rete
- Latenza di rete (bassa/alta)
- Principali eventi di attesa 
- Impostazioni dei parametri per il database
- È il database RAC, Exadata, che usa funzionalità o configurazioni avanzate

### <a name="virtual-machine-size"></a>Dimensioni della macchina virtuale

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. stimare le dimensioni della macchina virtuale in base all'utilizzo di CPU, memoria e I/O dal report AWR

Un elemento che è possibile osservare sono i primi cinque eventi in primo piano, che indicano dove sono presenti i colli di bottiglia del sistema.

Nel diagramma seguente, ad esempio, la sincronizzazione dei file di log è all'inizio. Indica il numero di attese necessarie prima che LGWR scriva il buffer del log nel file di log di ripristino. Questi risultati indicano che è necessario migliorare le prestazioni della risorsa di archiviazione o dei dischi. Il diagramma indica anche il numero di CPU (core) e la quantità di memoria.

![Screenshot che mostra la sincronizzazione dei file di log nella parte superiore della tabella.](./media/oracle-design/cpu_memory_info.png)

Il diagramma seguente mostra l'I/O totale di lettura e scrittura. Sono stati registrati 59 GB in lettura e 247,3 GB in scrittura durante il periodo di esecuzione del report.

![Screenshot che mostra l'i/O totale di lettura e scrittura.](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. scegliere una macchina virtuale

In base alle informazioni raccolte dal report AWR, il passaggio successivo è scegliere una macchina virtuale di dimensioni simili che soddisfi i requisiti. È possibile trovare un elenco delle VM disponibili nell'articolo [Ottimizzate per la memoria](../../sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. ottimizzare il dimensionamento delle macchine virtuali con una serie di VM simile basata sul ACU

Dopo avere scelto la VM, prestare attenzione all'ACU per la VM. In base al valore ACU, è possibile scegliere una macchina virtuale diversa più adatta agli specifici requisiti. Per altre informazioni, vedere [Unità di calcolo di Azure](../../acu.md).

![Screenshot della pagina delle unità ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Velocità effettiva della rete

Come illustra il diagramma seguente, esiste una relazione tra velocità effettiva e IOPS:

![Screenshot della velocità effettiva](./media/oracle-design/throughput.png)

La velocità effettiva totale della rete viene stimata in base alle informazioni seguenti:
- Traffico SQL*Net
- Mbps x numero di server (flusso in uscita, ad esempio Oracle Data Guard)
- Altri fattori, ad esempio la replica dell'applicazione

![Screenshot della velocità effettiva SQL*Net](./media/oracle-design/sqlnet_info.png)

In base ai requisiti di larghezza di banda della rete, sono disponibili diversi tipi di gateway tra cui scegliere, ad esempio Basic, VpnGw e Azure ExpressRoute. Per altre informazioni, vedere la [pagina Prezzi di Gateway VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Indicazioni**

- La latenza di rete è superiore rispetto a una distribuzione locale. La riduzione dei round trip di rete può migliorare notevolmente le prestazioni.
- Per ridurre i round trip, consolidare le applicazioni con transazioni elevate o con un livello di comunicazioni elevato nella stessa macchina virtuale.
- Usare macchine virtuali con [rete accelerata](../../../virtual-network/create-vm-accelerated-networking-cli.md) per migliorare le prestazioni di rete.
- Per alcune distribuzioni di Linux, prendere in considerazione l'abilitazione del [supporto Trim/annullare](/previous-versions/azure/virtual-machines/linux/configure-lvm#trimunmap-support).
- Installare [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) in una macchina virtuale separata.
- Per impostazione predefinita, le pagine di grandi dimensioni non sono abilitate in Linux. Prendere in considerazione l'abilitazione di pagine di grandi dimensioni e impostare `use_large_pages = ONLY` sul Oracle DB. Questo può contribuire a migliorare le prestazioni. Altre informazioni sono disponibili [qui](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390).

### <a name="disk-types-and-configurations"></a>Tipi di disco e configurazioni

- *Dischi del sistema operativo predefiniti*: questi tipi di dischi offrono dati persistenti e memorizzazione nella cache. Sono ottimizzati per l'accesso del sistema operativo all'avvio e non sono progettati per i carichi di lavoro transazionali o di data warehouse (analitici).

- *Dischi gestiti*: Azure gestisce gli account di archiviazione usati per i dischi delle macchine virtuali. È possibile specificare il tipo di disco (più spesso unità SSD Premium per carichi di lavoro Oracle) e le dimensioni del disco necessario. Azure crea e gestisce il disco automaticamente.  SSD Premium disco gestito è disponibile solo per le serie di VM ottimizzate per la memoria e appositamente progettate. Dopo avere scelto determinate dimensioni per la VM, il menu mostra solo gli SKU di Archiviazione Premium disponibili in base a tali dimensioni della VM.

![Screenshot della pagina del disco gestito](./media/oracle-design/premium_disk01.png)

Dopo avere configurato la risorsa di archiviazione in una macchina virtuale, è possibile sottoporre i dischi a test di carico prima di creare un database. Conoscendo la velocità di I/O in termini di latenza e velocità effettiva, è possibile determinare se le macchine virtuali supportano la velocità effettiva prevista con gli obiettivi di latenza.

Sono disponibili diversi strumenti per i test di carico delle applicazioni, ad esempio Oracle Orion, Sysbench, ZOTICONE e fio.

Dopo avere distribuito un database Oracle, eseguire nuovamente il test di carico. Avviare i carico di lavoro normali e di picco. I risultati indicheranno la baseline dell'ambiente.  Essere realistici nel test del carico di lavoro: non ha senso eseguire un carico di lavoro che non sia come quello che verrà eseguito nella macchina virtuale in realtà.

Poiché Oracle è un database con utilizzo intensivo di i/o per molti, è molto importante ridimensionare l'archiviazione in base alla velocità di IOPS anziché alle dimensioni di archiviazione. Se ad esempio le operazioni di I/O al secondo necessarie sono 5.000, ma servono solo 200 GB, si potrebbe comunque scegliere il disco Premium classe P30 anche se ha più di 200 GB di spazio di archiviazione.

Dal report AWR si può ottenere la frequenza di operazioni di I/O, che è determinata da log di rollforward, letture fisiche e frequenza delle scritture.  Verificare sempre che la serie di macchine virtuali scelta abbia la possibilità di gestire anche la richiesta di i/o del carico di lavoro.  Se la macchina virtuale ha un limite di i/o inferiore rispetto alla risorsa di archiviazione, il limite massimo verrà impostato dalla macchina virtuale.

![Screenshot della pagina del report AWR](./media/oracle-design/awr_report.png)

Ad esempio: la dimensione di rollforward è 12.200.000 byte al secondo, equivalente a 11,63 Mbps.
Le operazioni di I/O al secondo corrispondono a 12.200.000/2.358 = 5.174.

Dopo avere ottenuto un quadro preciso dei requisiti di I/O, è possibile scegliere la combinazione delle unità più adatte per soddisfare tali requisiti.

**Indicazioni**

- Per lo spazio di tabella dei dati, ripartire il carico di lavoro di I/O tra diversi dischi usando l'archiviazione gestita o Oracle ASM.
- Utilizzare la compressione avanzata Oracle per ridurre I/O (sia per i dati sia per gli indici).
- Separare i log di rollforward, i Temp e gli spazi di tabella di annullamento in dischi dati separati.
- Non inserire alcun file dell'applicazione nei dischi del sistema operativo predefiniti (dev/sda). Questi dischi non sono ottimizzati per l'avvio rapido delle macchine virtuali e potrebbero non offrire prestazioni valide per l'applicazione.
- Quando si usano macchine virtuali della serie M nell'archiviazione Premium, abilitare [acceleratore di scrittura](../../how-to-enable-write-accelerator.md) sul disco dei log di rollforward.
- Provare a trasferire i log di ripristino con latenza elevata a ultra disk.

### <a name="disk-cache-settings"></a>Impostazioni della cache su disco

Sono disponibili tre opzioni per la memorizzazione nella cache dell'host, ma per un database Oracle, per un carico di lavoro del database è consigliabile solo la memorizzazione nella cache di sola lettura.  ReadWrite può presentare vulnerabilità significative a un file di dati, in cui l'obiettivo di una scrittura di database è registrarlo nel file di dati, non memorizzare nella cache le informazioni.

Diversamente da un file system o un'applicazione, per un database, la raccomandazione per la memorizzazione nella cache dell'host è *ReadOnly*: tutte le richieste vengono memorizzate nella cache per le letture future. Tutte le Scritture continuano a essere scritte su disco.

**Indicazioni**

Per ottimizzare la velocità effettiva, è consigliabile iniziare con **ReadOnly** per la memorizzazione nella cache dell'host, quando possibile. Per archiviazione Premium, tenere presente che è necessario disabilitare le "barriere" quando si monta il file system con le opzioni di sola **lettura** . Aggiornare il file /etc/fstab con l'UUID dei dischi.

![Screenshot della pagina del disco gestito che mostra le opzioni ReadOnly e None.](./media/oracle-design/premium_disk02.png)

- Per i dischi del sistema operativo, usare la memorizzazione nella cache di **lettura/scrittura** predefinita e usare unità SSD Premium per le VM del carico di lavoro Oracle  Assicurarsi anche che anche il volume usato per lo scambio sia su unità SSD Premium.
- Per tutti i FILE di archivio, utilizzare **ReadOnly** per la memorizzazione nella cache. La memorizzazione nella cache di sola lettura è disponibile solo per i dischi gestiti Premium, P30 e versioni successive.  È previsto un limite di un volume 4095GiB che può essere usato con la memorizzazione nella cache di sola lettura.  Per impostazione predefinita, qualsiasi allocazione di dimensioni maggiori Disabilita la memorizzazione nella cache dell'host.

Se i carichi di lavoro variano molto tra il giorno e la sera e il carico di lavoro di i/o può supportarlo, P1-P20 SSD Premium con picchi possono fornire le prestazioni necessarie durante i caricamenti in batch notturni o richieste di i/o limitate.  

## <a name="security"></a>Sicurezza

Dopo avere installato e configurato l'ambiente Azure, il passaggio successivo consiste nel proteggere la rete. Di seguito sono elencati alcuni suggerimenti:

- *Criteri del gruppo di sicurezza di rete*: il gruppo di sicurezza di rete può essere definito da una subnet o una scheda di interfaccia di rete. È più semplice controllare l'accesso a livello di subnet per la sicurezza e forzare il routing per elementi come i firewall applicazione.

- *Jumpbox*: per una maggiore sicurezza dell'accesso, gli amministratori non devono connettersi direttamente al servizio dell'applicazione o al database. Viene usato un jumpbox come elemento intermedio tra il computer dell'amministratore e le risorse di Azure.
![Screenshot della pagina della topologia jumpbox](./media/oracle-design/jumpbox.png)

    Il computer dell'amministratore deve offrire accesso con restrizioni IP al solo jumpbox. Il jumpbox deve avere accesso all'applicazione e al database.

- *Rete privata* (subnet): è consigliabile tenere il servizio dell'applicazione e il database in subnet separate, per garantire un maggiore controllo ai criteri del gruppo di sicurezza di rete.


## <a name="additional-reading"></a>Altre letture

- [Configurare Oracle ASM](configure-oracle-asm.md)
- [Configurare Oracle Data Guard](configure-oracle-dataguard.md)
- [Configurare Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Backup e ripristino di Oracle](./oracle-overview.md)

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Creare VM a disponibilità elevata](../../linux/create-cli-complete.md)
- [Esplorare gli esempi dell'interfaccia della riga di comando di Azure per la distribuzione della VM](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
