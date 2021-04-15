---
title: Monitoraggio di Azure per soluzioni SAP provider| Microsoft Docs
description: Questo articolo fornisce le risposte alle domande frequenti su Monitoraggio di Azure per i provider di soluzioni SAP.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 93e97f1f04aea2a31b62b2014a88a5aaa998ed2d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376087"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Monitoraggio di Azure per i provider di soluzioni SAP (anteprima)

## <a name="overview"></a>Panoramica  

Nel contesto di Monitoraggio di Azure per soluzioni SAP, un tipo *di provider* fa riferimento a un *provider specifico.* Ad esempio *SAP HANA*, che è configurato per un componente specifico all'interno dell'ambiente SAP, ad esempio SAP HANA database. Un provider contiene le informazioni di connessione per il componente corrispondente e consente di raccogliere dati di telemetria da tale componente. Una Monitoraggio di Azure per soluzioni SAP risorsa di monitoraggio SAP può essere configurata con più provider dello stesso tipo di provider o con più provider di più tipi di provider.
   
I clienti possono scegliere di configurare tipi di provider diversi per abilitare la raccolta dei dati dal componente corrispondente nel panorama SAP. Ad esempio, i clienti possono configurare un provider per SAP HANA provider, un altro provider per il tipo di provider di cluster a disponibilità elevata e così via.  

I clienti possono anche scegliere di configurare più provider di un tipo di provider specifico per riutilizzare la stessa risorsa di monitoraggio SAP e il gruppo gestito associato. Altre informazioni sul gruppo di risorse gestite. Per l'anteprima pubblica, sono supportati i tipi di provider seguenti:   
- SAP HANA
- Cluster a disponibilità elevata
- Microsoft SQL Server
- SAP NetWeaver

![Monitoraggio di Azure per i provider di soluzioni SAP](./media/azure-monitor-sap/azure-monitor-providers.png)

Ai clienti è consigliabile configurare almeno un provider dai tipi di provider disponibili al momento della distribuzione della risorsa monitoraggio SAP. Configurando un provider, i clienti avviano la raccolta dati dal componente corrispondente per cui è configurato il provider.   

Se i clienti non configurano alcun provider al momento della distribuzione della risorsa di monitoraggio SAP, anche se la risorsa di monitoraggio SAP verrà distribuita correttamente, non verranno raccolti dati di telemetria. I clienti hanno la possibilità di aggiungere provider dopo la distribuzione tramite la risorsa di monitoraggio SAP portale di Azure. I clienti possono aggiungere o eliminare provider dalla risorsa di monitoraggio SAP in qualsiasi momento.

> [!Tip]
> Se si vuole che Microsoft implementi un provider specifico, lasciare commenti e suggerimenti tramite il collegamento alla fine di questo documento o contattare il team dell'account.  

## <a name="provider-type-sap-hana"></a>Tipo di provider SAP HANA

I clienti possono configurare uno o più provider di tipo provider *SAP HANA* abilitare la raccolta dei dati SAP HANA database. Il provider SAP HANA si connette al database di SAP HANA tramite la porta SQL, esegue il pull dei dati di telemetria dal database e lo inserisce nell'area di lavoro Log Analytics nella sottoscrizione del cliente. Il provider SAP HANA raccoglie i dati ogni minuto dal database SAP HANA.  

In anteprima pubblica, i clienti possono aspettarsi di visualizzare i dati seguenti con il provider SAP HANA: utilizzo dell'infrastruttura sottostante, stato dell'host SAP HANA, replica di sistema SAP HANA e dati di telemetria di SAP HANA Backup. Per configurare SAP HANA provider, l'indirizzo IP host, il numero di porta SQL HANA e il nome utente e la password SYSTEMDB sono obbligatori. I clienti sono consigliati di SAP HANA provider per SYSTEMDB, ma è possibile configurare più provider per altri tenant di database.

![Monitoraggio di Azure per provider di soluzioni SAP - SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-high-availability-cluster"></a>Tipo di provider Cluster a disponibilità elevata
I clienti possono configurare uno o più provider di cluster a disponibilità elevata di tipo *provider* per abilitare la raccolta dei dati dal cluster Pacemaker all'interno dell'ambiente SAP. Il provider di cluster a disponibilità elevata si connette a Pacemaker, usando un endpoint [ha_cluster_exporter,](https://github.com/ClusterLabs/ha_cluster_exporter) esegue il pull dei dati di telemetria dal database e lo inserisce nell'area di lavoro Log Analytics nella sottoscrizione del cliente. Il provider di cluster a disponibilità elevata raccoglie i dati ogni 60 secondi da Pacemaker.  

In anteprima pubblica, i clienti possono aspettarsi di visualizzare i dati seguenti con il provider di cluster a disponibilità elevata:   
 - Stato del cluster rappresentato come rollup dello stato del nodo e della risorsa 
 - [Altri](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Monitoraggio di Azure per provider di soluzioni SAP - Cluster a disponibilità elevata](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Per configurare un provider di cluster a disponibilità elevata, sono necessari due passaggi principali:

1. Installare [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) in *ogni nodo* all'interno del cluster Pacemaker.

   Sono disponibili due opzioni per l'installazione ha_cluster_exporter:
   
   - Usare Automazione di Azure script per distribuire un cluster a disponibilità elevata. Gli script [installano ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) in ogni nodo del cluster.  
   - Eseguire [un'installazione manuale.](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build) 

2. Configurare un provider di cluster a disponibilità elevata *per ogni* nodo all'interno del cluster Pacemaker.

   Per configurare il provider di cluster a disponibilità elevata, sono necessarie le informazioni seguenti:
   
   - **Nome**. Nome per questo provider. Deve essere univoco per questo Monitoraggio di Azure per le soluzioni SAP.
   - **Endpoint Prometheus**. http \: // \<servername or ip address\> :9664/metrics.
   - **SID**. Per i sistemi SAP, usare il SID SAP. Per altri sistemi ,ad esempio cluster NFS, usare un nome di tre caratteri per il cluster. Il SID deve essere distinto dagli altri cluster monitorati.   
   - **Nome cluster**. Nome del cluster utilizzato durante la creazione del cluster. Il nome del cluster è disponibile nella proprietà del cluster `cluster-name` .
   - **Nome host**. Nome host Linux della macchina virtuale.  


## <a name="provider-type-os-linux"></a>Tipo di provider Sistema operativo (Linux)
I clienti possono configurare uno o più provider di tipo provider OS (Linux) per abilitare la raccolta dati da BareMetal o dal nodo della macchina virtuale. Il provider del sistema operativo (Linux) si connette ai nodi BareMetal o VM, usando l'endpoint [Node_Exporter,](https://github.com/prometheus/node_exporter)esegue il pull dei dati di telemetria dai nodi e lo inserisce nell'area di lavoro Log Analytics nella sottoscrizione del   cliente. Il provider del sistema operativo (Linux) raccoglie i dati ogni 60 secondi per la maggior parte delle metriche dai nodi. 

Nell'anteprima pubblica i clienti possono aspettarsi di visualizzare i dati seguenti con il provider del sistema operativo (Linux): 
   - Utilizzo CPU, Utilizzo CPU per processo 
   - Utilizzo disco, lettura I/O & scrittura 
   - Distribuzione della memoria, utilizzo della memoria, utilizzo della memoria di swapping 
   - Utilizzo rete, Rete in ingresso e & traffico in uscita. 

Per configurare un provider del sistema operativo (Linux), sono necessari due passaggi principali:
1. Installare [Node_Exporter](https://github.com/prometheus/node_exporter)   in ogni nodo BareMetal o VM.
   Sono disponibili due opzioni per l'installazione [Node_exporter](https://github.com/prometheus/node_exporter): 
      - Per l'installazione di Automazione con Ansible [usare](https://github.com/prometheus/node_exporter) Node_Exporter in ogni nodo BareMetal o VM per installare il provider del sistema operativo (Linux).  
      - Eseguire [un'installazione manuale di](https://prometheus.io/docs/guides/node-exporter/).

2. Configurare un provider del sistema operativo (Linux) per ogni istanza bareMetal o del nodo della macchina virtuale nell'ambiente. 
   Per configurare il provider del sistema operativo (Linux), sono necessarie le informazioni seguenti: 
      - Name. Nome del provider. Deve essere univoco per questo Monitoraggio di Azure per le soluzioni SAP. 
      - Endpoint dell'esportatore di nodi. In genere http:// <servername or ip address> :9100/metrics 

> [!NOTE]
> 9100 è una porta esposta per l Node_Exporter endpoint.

> [!Warning]
> Assicurarsi che Node Exporter continui a essere in esecuzione dopo il riavvio del nodo. 


## <a name="provider-type-microsoft-sql-server"></a>Tipo di provider Microsoft SQL Server

I clienti possono configurare uno o più provider di tipo provider *Microsoft SQL Server* per abilitare la raccolta dati [da SQL Server in macchine virtuali](https://azure.microsoft.com/services/virtual-machines/sql-server/). SQL Server provider si connette a Microsoft SQL Server tramite la porta SQL, esegue il pull dei dati di telemetria dal database ed esegue il push nell'area di lavoro Log Analytics nella sottoscrizione del cliente. Il SQL Server deve essere configurato per l'autenticazione SQL e deve essere creato un account di accesso SQL Server, con il database SAP come database predefinito per il provider. SQL Server provider raccoglie dati da SQL Server ogni 60 secondi fino a ogni ora.  

In anteprima pubblica, i clienti possono aspettarsi di visualizzare i dati seguenti con il provider SQL Server: utilizzo dell'infrastruttura sottostante, principali istruzioni SQL, tabella più grande, problemi registrati nei log degli errori di SQL Server, processi di blocco e altri.  

Per configurare Microsoft SQL Server provider, sono necessari l'ID di sistema SAP, l'indirizzo IP host, il numero di porta SQL Server e il nome SQL Server di accesso e la password.

![Monitoraggio di Azure per i provider di soluzioni SAP - SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="provider-type-sap-netweaver"></a>Tipo di provider SAP NetWeaver

I clienti possono configurare uno o più provider di tipo provider SAP NetWeaver per abilitare la raccolta dati dal livello SAP NetWeaver. Il provider AmS NetWeaver sfrutta l'interfaccia del [servizio Web SAPControl](https://www.sap.com/documents/2016/09/0a40e60d-8b7c-0010-82c7-eda71af511fa.html) esistente per recuperare le informazioni di telemetria appropriate.

Per la versione corrente, di seguito sono riportati i metodi Web SOAP standard richiamati da AMS.
|metodo Web|    Abap|   Java|   Metriche|
|--|--|--|--|
|GetSystemInstanceList| X|  X|  Disponibilità dell'istanza, server messaggi, gateway, ICM, disponibilità ABAP|
|GetProcessList|    X|  X|  Se l'elenco di istanze è RED, è possibile ottenere il processo che causa il server red|
|GetQueueStatistic| X|  X|  Statistiche coda (DIA/BATCH/UPD)|
|ABAPGetWPTable|    X|   -| Utilizzo del processo di lavoro|
|EnqGetStatistic|   X   |X  |Locks|

In anteprima pubblica, i clienti possono aspettarsi di visualizzare i dati seguenti con il provider SAP NetWeaver: 
- Disponibilità del sistema e dell'istanza
- Utilizzo del processo di lavoro
- Utilizzo della coda
- Statistiche sui blocchi di accodamento.

![image](https://user-images.githubusercontent.com/75772258/114581825-a9f2eb00-9c9d-11eb-8e6f-79cee7c5093f.png)

## <a name="next-steps"></a>Passaggi successivi

- Creare la prima risorsa Monitoraggio di Azure per soluzioni SAP.
- Per domande sulle Monitoraggio di Azure per soluzioni SAP? Vedere la sezione [Domande](./azure-monitor-faq.md) frequenti
