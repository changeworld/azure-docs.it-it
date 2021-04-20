---
title: Installare l'agente di Log Analytics in computer Linux
description: Questo articolo descrive come connettere computer Linux ospitati in altri cloud o in locale Monitoraggio di Azure con l'agente di Log Analytics per Linux.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 23597804a34a9bc409db179010569024aa472016
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725927"
---
# <a name="install-log-analytics-agent-on-linux-computers"></a>Installare l'agente di Log Analytics in computer Linux
Questo articolo fornisce informazioni dettagliate sull'installazione dell'agente di Log Analytics nei computer Linux usando i metodi seguenti:

* [Installare l'agente per Linux usando uno script wrapper](#install-the-agent-using-wrapper-script) ospitato in GitHub. Questo è il metodo consigliato per installare e aggiornare l'agente quando il computer dispone di connettività a Internet, direttamente o tramite un server proxy.
* [Scaricare e installare manualmente](#install-the-agent-manually) l'agente. Questa operazione è necessaria quando il computer Linux non ha accesso a Internet e comunica con Monitoraggio di Azure o Automazione di Azure tramite il [gateway di Log Analytics.](./gateway.md) 

>[!IMPORTANT]
> I metodi di installazione descritti in questo articolo vengono in genere usati per le macchine virtuali in locale o in altri cloud. Vedere [Opzioni di installazione](./log-analytics-agent.md#installation-options) per opzioni più efficienti che è possibile usare per le macchine virtuali di Azure.



## <a name="supported-operating-systems"></a>Sistemi operativi supportati

Vedere [Panoramica degli Monitoraggio di Azure per](agents-overview.md#supported-operating-systems) un elenco delle distribuzioni Linux supportate dall'agente di Log Analytics.

>[!NOTE]
>OpenSSL 1.1.0 è supportato solo su piattaforme x86_x64 (64 bit), mentre le versioni di OpenSSL precedenti a 1.x non sono supportate su alcuna piattaforma.

>[!NOTE]
>L'esecuzione dell'agente Linux di Log Analytics nei contenitori non è supportata. Se è necessario monitorare i contenitori, sfruttare la [soluzione](../containers/containers.md) Monitoraggio contenitori per gli host Docker o [informazioni](../containers/container-insights-overview.md) dettagliate sui contenitori per Kubernetes.

A partire dalle versioni rilasciate dopo il mese di agosto 2018, al modello di supporto sono state apportate le modifiche seguenti:  

* Sono supportate solo le versioni server, non quelle client.  
* Maggiore supporto per una delle [distribuzioni Linux approvate in Azure](../../virtual-machines/linux/endorsed-distros.md). Si noti che potrebbe passare del tempo tra una nuova distribuzione e/o una nuova versione Linux approvata in Azure e supportata per l'agente di Log Analytics per Linux.
* Tutte le versioni minori sono supportate per ogni versione principale elencata.
* Non sono supportate le versioni che hanno superato la data di fine del supporto.
* Supporta solo le immagini di macchine virtuali. I contenitori, anche quelli derivati dalle immagini ufficiali degli editori di distribuzione, non sono supportati.
* Non sono supportate le nuove versioni di AMI.  
* Sono supportate solo le versioni che eseguono OpenSSL 1.x per impostazione predefinita.

>[!NOTE]
>Se si usa una distribuzione o una versione attualmente non supportata e non allineata al modello di supporto, è consigliabile creare una copia tramite fork del repository, nella consapevolezza che il supporto tecnico Microsoft non fornisce assistenza per versioni dell'agente con fork.

### <a name="python-requirement"></a>Requisito di Python

A partire da Agent versione 1.13.27, l'agente Linux supporterà sia Python 2 che 3. È sempre consigliabile usare l'agente più recente. 

Se si usa una versione precedente dell'agente, è necessario che la macchina virtuale usi Python 2 per impostazione predefinita. Se la macchina virtuale usa una distribuzione che non include Python 2 per impostazione predefinita, è necessario installarla. I comandi di esempio seguenti installeranno Python 2 in diverse distribuzione.

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SUSE: `zypper install -y python2`

L'alias dell'eseguibile python2 deve essere *python.* Di seguito è riportato un metodo che è possibile usare per impostare questo alias:

1. Eseguire il comando seguente per rimuovere eventuali alias esistenti.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Eseguire il comando seguente per creare l'alias.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## <a name="supported-linux-hardening"></a>Protezione avanzata linux supportata
L'agente OMS ha un supporto limitato per la personalizzazione e la protezione avanzata per Linux.

Sono attualmente supportati gli elementi seguenti: 
- Fips

Le tabelle seguenti non sono supportate:
- Cis
- Selinux

Il supporto della protezione avanzata CIS e SELINUX è pianificato per [l'agente di monitoraggio di Azure.](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) Altri metodi di protezione avanzata e personalizzazione non sono supportati né pianificati per l'agente OMS.  

## <a name="agent-prerequisites"></a>Prerequisiti per l'agente

La tabella seguente evidenzia i pacchetti necessari per [le distribuzioni Linux](#supported-operating-systems) supportate in cui verrà installato l'agente.

|Pacchetto necessario |Descrizione |Versione minima |
|-----------------|------------|----------------|
|Glibc |    Libreria GNU C | 2.5-12 
|Openssl    | Librerie OpenSSL | 1.0.x o 1.1.x |
|Curl | Client Web cURL | 7.15.5 |
|Python | | 2.7 o 3.6+
|Python-ctypes | | 
|PAM | Moduli di autenticazione modulare | | 

>[!NOTE]
>Per raccogliere i messaggi SysLog, è necessario rsyslog o syslog-ng. Il daemon SysLog predefinito nella versione 5 di Red Hat Enterprise Linux, CentOS e nella versione Oracle Linux (sysklog) non è supportato per la raccolta di eventi SysLog. Per raccogliere i dati di SysLog da questa versione delle distribuzioni, è necessario installare e configurare il daemon rsyslog in modo da sostituire sysklog.

## <a name="network-requirements"></a>Requisiti di rete
Vedere [Panoramica dell'agente di Log Analytics](./log-analytics-agent.md#network-requirements) per i requisiti di rete per l'agente Linux.

## <a name="agent-install-package"></a>Pacchetto di installazione dell'agente

L'agente di Log Analytics per Linux è costituito da più pacchetti. Il file di versione contiene i pacchetti seguenti, disponibili eseguendo il bundle della shell con il `--extract` parametro :

**Pacchetto** | **Versione** | **Descrizione**
----------- | ----------- | --------------
omsagent | 1.13.9 | Agente di Log Analytics per Linux
omsconfig | 1.1.1 | Agente di configurazione per l'agente di Log Analytics
omi | 1.6.4 | Open Management Infrastructure (OMI): un server CIM leggero. *Si noti che OMI richiede l'accesso radice per eseguire un processo cron necessario per il funzionamento del servizio*
scx | 1.6.4 | Provider OMI CIM per metriche delle prestazioni del sistema operativo
apache-cimprov | 1.0.1 | Monitoraggio delle prestazioni del server HTTP Apache per OMI. Installato solo se viene rilevato il server HTTP Apache.
mysql-cimprov | 1.0.1 | Monitoraggio delle prestazioni del server MySQL per OMI. Installato solo se viene rilevato il server MySQL/MariaDB.
docker-cimprov | 1.0.0 | Provider Docker per OMI. Installato solo se viene rilevato Docker.

### <a name="agent-installation-details"></a>Dettagli di installazione dell'agente

Dopo aver installato l'agente di Log Analytics per i pacchetti Linux, vengono applicate le modifiche di configurazione aggiuntive a livello di sistema seguenti. Questi elementi vengono rimossi quando viene disinstallato il pacchetto omsagent.

* Viene creato un utente senza privilegi denominato `omsagent` . Il daemon viene eseguito con queste credenziali. 
* Un file di *inclusione sudoers* viene creato in `/etc/sudoers.d/omsagent` . In questo modo `omsagent` si autorizza a riavviare i daemon syslog e omsagent. Se le direttive *sudo include* non sono supportate nella versione installata di sudo, queste voci verranno scritte in `/etc/sudoers` .
* La configurazione di SysLog viene modificata in modo da inoltrare un sottoinsieme di eventi all'agente. Per altre informazioni, vedere [Configurare la raccolta dati Syslog](data-sources-syslog.md).

In un computer Linux monitorato, l'agente è elencato come `omsagent` . `omsconfig` è l'agente di log analytics per l'agente di configurazione Linux che cerca la nuova configurazione sul lato portale ogni 5 minuti. La configurazione nuova e aggiornata viene applicata ai file di configurazione dell'agente che si trovano in `/etc/opt/microsoft/omsagent/conf/omsagent.conf` .

## <a name="install-the-agent-using-wrapper-script"></a>Installare l'agente usando lo script wrapper

La procedura seguente configura l'agente per Log Analytics in Azure e nel cloud Azure per enti pubblici usando lo script wrapper per i computer Linux in grado di comunicare direttamente o tramite un server proxy per scaricare l'agente ospitato in GitHub e installare l'agente.  

Se il computer Linux deve comunicare tramite un server proxy con Log Analytics, questa configurazione può essere specificata nella riga di comando includendo `-p [protocol://][user:password@]proxyhost[:port]` . La *proprietà* del protocollo accetta o e la proprietà proxyhost accetta un nome di dominio completo o un indirizzo `http` IP del server `https` proxy.  

ad esempio `https://proxy01.contoso.com:30443`

Se l'autenticazione è necessaria in entrambi i casi, è necessario specificare il nome utente e la password. ad esempio `https://user01:password@proxy01.contoso.com:30443`

1. Per configurare il computer Linux per la connessione a un'area di lavoro Log Analytics, eseguire il comando seguente specificando l'ID dell'area di lavoro e la chiave primaria. Il comando seguente scarica l'agente, convalida il relativo checksum e ne esegue l'installazione.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Il comando seguente include il parametro `-p` del proxy e la sintassi di esempio per i casi in cui il server proxy richiede l'autenticazione:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Per configurare il computer Linux per connettersi all'area di lavoro Log Analytics nel cloud di Azure per enti pubblici, eseguire il comando seguente specificando l'ID e la chiave primaria dell'area di lavoro copiati in precedenza. Il comando seguente scarica l'agente, convalida il relativo checksum e ne esegue l'installazione. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Il comando seguente include il parametro `-p` del proxy e la sintassi di esempio per i casi in cui il server proxy richiede l'autenticazione:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Riavviare l'agente eseguendo il comando seguente: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 


## <a name="install-the-agent-manually"></a>Installare l'agente manualmente

L'agente di Log Analytics per Linux viene fornito in un bundle di script della shell autoestraibile e installabile. Questo bundle contiene pacchetti Debian e RPM per ognuno dei componenti dell'agente e può essere installato direttamente o estratto per recuperare i singoli pacchetti. Viene fornito un bundle per x64 e uno per le architetture x86. 

> [!NOTE]
> Per le macchine virtuali di Azure, è consigliabile installarvi l'agente usando [l'estensione macchina virtuale Azure Log Analytics](../../virtual-machines/extensions/oms-linux.md) per Linux. 

1. [Scaricare](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) e trasferire il bundle appropriato (x64 o x86) nella macchina virtuale Linux o nel computer fisico usando scp/sftp.

2. Installare il bundle usando `--install` l'argomento . Per eseguire l'onboard in un'area di lavoro Log Analytics durante l'installazione, specificare `-w <WorkspaceID>` i `-s <workspaceKey>` parametri e copiati in precedenza.

    >[!NOTE]
    >È necessario usare l'argomento se sono installati pacchetti dipendenti, ad esempio `--upgrade` omi, scx, omsconfig o le relative versioni precedenti, come nel caso in cui sia già installato l'agente system Center Operations Manager per Linux. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Per configurare l'agente Linux per l'installazione e la connessione a un'area di lavoro Log Analytics tramite un gateway di Log Analytics, eseguire il comando seguente specificando i parametri proxy, ID area di lavoro e chiave dell'area di lavoro. Questa configurazione può essere specificata nella riga di comando includendo `-p [protocol://][user:password@]proxyhost[:port]` . La *proprietà proxyhost* accetta un nome di dominio completo o un indirizzo IP del server gateway di Log Analytics.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Se è necessaria l'autenticazione, è necessario specificare il nome utente e la password. Ad esempio: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Per configurare il computer Linux per la connessione a un'area di lavoro Log Analytics in Azure per enti pubblici cloud, eseguire il comando seguente specificando l'ID dell'area di lavoro e la chiave primaria copiati in precedenza.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Se si vogliono installare i pacchetti dell'agente e configurarlo per l'esecuzione di report in un'area di lavoro Log Analytics specifica in un secondo momento, eseguire il comando seguente:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Se si desidera estrarre i pacchetti dell'agente dal bundle senza installare l'agente, eseguire il comando seguente:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="upgrade-from-a-previous-release"></a>Eseguire l'aggiornamento da una versione precedente

L'aggiornamento da una versione precedente, a partire dalla versione 1.0.0-47, è supportato in ogni versione. Eseguire l'installazione con il `--upgrade` parametro per aggiornare tutti i componenti dell'agente alla versione più recente.

## <a name="cache-information"></a>Memorizzare nella cache le informazioni
I dati dell'agente di Log Analytics per Linux vengono memorizzati nella cache nel computer locale in *%STATE_DIR_WS%/out_oms_common*.buffer* prima di essere inviati Monitoraggio di Azure. I dati di log personalizzati vengono memorizzati nel buffer in *%STATE_DIR_WS%/out_oms_blob*.buffer*. Il percorso può essere diverso per alcune [soluzioni e tipi di dati](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=).

L'agente tenta di caricare ogni 20 secondi. In caso di esito negativo, attenderà un periodo di tempo in aumento esponenziale fino al completamento: 30 secondi prima del secondo tentativo, 60 secondi prima del terzo, 120 secondi... e così via fino a un massimo di 16 minuti tra un nuovo tentativo e l'altro fino a quando non viene eseguita di nuovo la connessione. L'agente ritenterà fino a 6 volte per un determinato blocco di dati prima di eliminare e passare a quello successivo. Questa operazione continua fino a quando l'agente non è in grado di caricarlo di nuovo. Ciò significa che i dati possono essere memorizzati nel buffer fino a circa 30 minuti prima di essere eliminati.

Le dimensioni predefinite della cache sono 10 MB, ma possono essere modificate nel [file omsagent.conf.](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf)


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle modalità di riconfigurazione, aggiornamento o rimozione dell'agente dalla macchina virtuale, rivedere [Gestione e manutenzione dell'agente di Log Analytics per Windows e Linux](agent-manage.md).

- Se si verificano problemi di installazione o gestione dell'agente, vedere l'articolo relativo alla [risoluzione dei problemi dell'agente Linux](agent-linux-troubleshoot.md).
