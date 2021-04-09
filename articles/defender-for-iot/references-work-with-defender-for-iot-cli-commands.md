---
title: Usare i comandi dell'interfaccia della riga di comando di Defender per IoT
description: Questo articolo descrive il Defender per i comandi dell'interfaccia della riga di comando per i sensori e le console di gestione locali.
ms.date: 12/12/2020
ms.topic: article
ms.openlocfilehash: 9cd3f4325db2bc45ddcd6cc011dd4993e385a43c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778679"
---
# <a name="work-with-defender-for-iot-cli-commands"></a>Usare i comandi dell'interfaccia della riga di comando di Defender per IoT

Questo articolo descrive i comandi dell'interfaccia della riga di comando per i sensori e le console di gestione locali. I comandi sono accessibili agli utenti seguenti:

- Amministratore
- CyberX 
- Supporto

Per iniziare a lavorare nell'interfaccia della riga di comando, connettersi usando un terminale. Ad esempio, il nome del terminale `Putty` e l' `Support` utente. 

## <a name="create-local-alert-exclusion-rules"></a>Creare regole di esclusione di avvisi locali

È possibile creare una regola di esclusione dell'avviso locale immettendo il comando seguente nell'interfaccia della riga di comando:

```azurecli-interactive
alerts exclusion-rule-create [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Con le regole di esclusione degli avvisi è possibile usare gli attributi seguenti:

| Attributo | Descrizione |
|--|--|
| [-h] | Stampa le informazioni della Guida per il comando. |
| -n nome | Nome della regola da creare. |
| [-TS ora] | Intervallo di tempo per cui la regola è attiva. Questa operazione deve essere specificata come segue:<br />`xx:yy-xx:yy`<br />È possibile definire più di un periodo di tempo utilizzando una virgola tra di essi. Ad esempio: `xx:yy-xx:yy, xx:yy-xx:yy`. |
| [-dir DIRECTION] | Direzione a cui viene applicata la regola. Questa operazione deve essere specificata come segue:<br />`both | src | dst` |
| [-dev device] | L'indirizzo IP e il tipo di indirizzo dei dispositivi da escludere dalla regola, specificati come:<br />`ip-x.x.x.x`<br />`mac-xx:xx:xx:xx:xx:xx`<br />`subnet: x.x.x.x/x` |
| [-a avvisi] | Nome dell'avviso che la regola escluderà:<br />`0x00000`<br />`0x000001` |

## <a name="append-local-alert-exclusion-rules"></a>Aggiungere regole di esclusione di avvisi locali

È possibile aggiungere regole di esclusione dell'avviso locale immettendo il comando seguente nell'interfaccia della riga di comando:

```azurecli-interactive
alerts exclusion-rule-append [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Gli attributi usati sono gli stessi descritti nella sezione creare regole di esclusione di avvisi locali. La differenza nell'utilizzo è che qui gli attributi vengono applicati alle regole esistenti.

## <a name="show-local-alert-exclusion-rules"></a>Mostra regole di esclusione avviso locale

Immettere il comando seguente per presentare l'elenco esistente di regole di esclusione:

```azurecli-interactive
alerts exclusion-rule-list [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

## <a name="delete-local-alert-exclusion-rules"></a>Elimina regole di esclusione avviso locale

È possibile eliminare una regola di esclusione degli avvisi esistente immettendo il comando seguente:

```azurecli-interactive
alerts exclusion-rule-remove [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

L'attributo seguente può essere usato con le regole di esclusione degli avvisi:

| Attributo | Descrizione|
| --------- | ---------------------------------- |
| -n nome | Nome della regola da eliminare. |

## <a name="sync-time-from-the-ntp-server"></a>Tempo di sincronizzazione dal server NTP

È possibile abilitare o disabilitare la sincronizzazione dell'ora da un server NTP specificato.

### <a name="enable-ntp-sync"></a>Abilitare la sincronizzazione NTP

Immettere il comando seguente per recuperare periodicamente l'ora dal server NTP specificato:

```azurecli-interactive
ntp enable IP
```

L'attributo che è possibile definire all'interno del comando è l'indirizzo IP del server NTP.

### <a name="disable-ntp-sync"></a>Disabilitare la sincronizzazione NTP

Immettere il comando seguente per disabilitare la sincronizzazione dell'ora con il server NTP specificato:

```azurecli-interactive
ntp disable IP
```

L'attributo che è possibile definire all'interno del comando è l'indirizzo IP del server NTP.

## <a name="network-configuration"></a>Configurazione di rete

La tabella seguente descrive i comandi disponibili per configurare le opzioni di rete per Azure Defender:

|Nome|Comando|Descrizione|
|-----------|-------|-----------|
|Ping|`ping IP`| Effettuare il ping di un indirizzo all'esterno del Defender per la piattaforma Internet.|
|Blink|`network blink`| Individuare una connessione facendo lampeggiare le spie dell'interfaccia. |
|Riconfigurare la rete |`network edit-settings`| Abilitare una modifica nei parametri di configurazione di rete. |
|Mostra impostazioni di rete |`network list`|Visualizza i parametri della scheda di rete. |
|Convalidare la configurazione di rete |`network validate` |Visualizza le impostazioni di rete di output. <br /> <br />Ad esempio: <br /> <br />Impostazioni di rete correnti: <br /> interfaccia: eth0 <br /> IP: 10.100.100.1 <br />subnet: 255.255.255.0 <br />gateway predefinito: 10.100.100.254 <br />DNS: 10.100.100.254 <br />interfacce di monitoraggio: eth1|
|Importazione di un certificato |`certificate import FILE` |Importa il certificato HTTPS. È necessario specificare il percorso completo, che conduce a un \* file. CRT. |
|Mostra la data |`date` |Restituisce la data corrente nell'host in formato GMT. |

## <a name="filter-network-configurations"></a>Filtrare le configurazioni di rete

Il `network capture-filter` comando consente agli amministratori di eliminare il traffico di rete che non deve essere analizzato. È possibile filtrare il traffico usando un elenco di inclusione o un elenco di esclusione.

```azurecli-interactive
network capture-filter
```

Dopo aver immesso il comando, verrà visualizzata la domanda seguente:

>`Would you like to supply devices and subnet masks you wish to include in the capture filter? [Y/N]:`

Selezionare `Y` per aprire un file nano in cui è possibile aggiungere un dispositivo, un canale, una porta e un subset in base alla sintassi seguente:

| Attributo | Descrizione |
|--|--|
| 1.1.1.1 | Include tutto il traffico per questo dispositivo. |
| 1.1.1.1, 2.2.2.2 | Include tutto il traffico per questo canale. |
| 1.1.1, 2.2.2 | Include tutto il traffico per questa subnet. |

Separare gli argomenti eliminando una riga.

Quando si include un dispositivo, un canale o una subnet, il sensore elabora tutto il traffico valido per tale argomento, incluse le porte e il traffico che in genere non vengono elaborati.

Verrà quindi richiesta la domanda seguente:

>`Would you like to supply devices and subnet masks you wish to exclude from the capture filter? [Y/N]:`

Selezionare `Y` questa impostazione per aprire un file nano in cui è possibile aggiungere un dispositivo, un canale, una porta e subset in base alla sintassi seguente:

| Attributo | Descrizione |
|--|--|
| 1.1.1.1 | Esclude tutto il traffico per questo dispositivo. |
| 1.1.1.1, 2.2.2.2 | Esclude tutto il traffico per questo canale, ovvero tutto il traffico tra due dispositivi. |
| 1.1.1.1, 2.2.2.2, 443 | Esclude tutto il traffico per questo canale in base alla porta. |
| 1.1.1 | Esclude tutto il traffico per questa subnet. |
| 1.1.1, 2.2.2 | Esclude tutto il traffico per tra subnet. |

Separare gli argomenti eliminando una riga.

Quando si esclude un dispositivo, un canale o una subnet, il sensore esclude tutto il traffico valido per tale argomento.

### <a name="ports"></a>Porte

Includere o escludere le porte UDP e TCP per tutto il traffico.

>`502`: porta singola

>`502,443`: entrambe le porte

>`Enter tcp ports to include (delimited by comma or Enter to skip):`

>`Enter udp ports to include (delimited by comma or Enter to skip):`

>`Enter tcp ports to exclude (delimited by comma or Enter to skip):`

>`Enter udp ports to exclude (delimited by comma or Enter to skip):`

### <a name="components"></a>Componenti

Viene richiesta la domanda seguente:

>`In which component do you wish to apply this capture filter?`

Le opzioni disponibili sono:,,,  `all` `dissector` `collector` `statistics-collector` , `rpc-parser` o `smb-parser` .

Nella maggior parte dei casi d'uso selezionare `all` .

### <a name="custom-base-capture-filter"></a>Filtro di acquisizione di base personalizzato

Il filtro di acquisizione di base è la linea di base per i componenti. Ad esempio, il filtro determina quali porte sono disponibili per il componente.

Selezionare `Y` per tutte le opzioni seguenti. Tutti i filtri vengono aggiunti alla baseline dopo l'impostazione delle modifiche. Se si effettua una modifica, la baseline esistente verrà sovrascritta.

>`Would you like to supply a custom base capture filter for the dissector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the statistics-collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the rpc-parser component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the smb-parser component? [Y/N]:`

>`Type Y for "internal" otherwise N for "all-connected" (custom operation mode enabled) [Y/N]:`

Se si sceglie di escludere una subnet, ad esempio 1.1.1:

- `internal` escluderà solo tale subnet.

- `all-connected` escluderà tale subnet e tutto il traffico da e verso la subnet.

Si consiglia di selezionare `internal` .

> [!NOTE]
> Le scelte vengono usate per tutti i filtri nello strumento e non sono dipendenti dalla sessione. In altre parole, non è mai possibile scegliere `internal`   alcuni filtri e  `all-connected`   per altri.

### <a name="comments"></a>Commenti

È possibile visualizzare i filtri in  ```/var/cyberx/properties/cybershark.properties``` :

- **statistiche-agente di raccolta**:  `bpf_filter property` in ```/var/cyberx/properties/net.stats.collector.properties```

- **dissector**: `override.capture_filter`   Proprietà in ```/var/cyberx/properties/cybershark.properties```

- **RPC-parser**: `override.capture_filter` Proprietà in ```/var/cyberx/properties/rpc-parser.properties```

- **SMB-parser**:  `override.capture_filter`   Proprietà in ```/var/cyberx/properties/smb-parser.properties```

- **agente di raccolta**: `general.bpf_filter`   Proprietà in ```/var/cyberx/properties/collector.properties```

È possibile ripristinare la configurazione predefinita immettendo il codice seguente per l'utente CyberX:

```azurecli-interactive
sudo cyberx-xsense-capture-filter -p all -m all-connected
```

## <a name="define-client-and-server-hosts"></a>Definire host client e server

Se Defender for Internet non rileva automaticamente il client e gli host server, immettere il comando seguente per impostare gli host client e server:

```azurecli-interactive
directions [-h] [--identifier IDENTIFIER] [--port PORT] [--remove] [--add]  
[--tcp] [--udp]
```

Con il comando è possibile usare gli attributi seguenti `directions` :

| Attributo | Descrizione |
|--|--|
| [-h] | Stampa le informazioni della Guida per il comando. |
| [--identificatore identificatore] | Identificatore del server. |
| [--porta porta] | Porta del server. |
| [--Remove] | Rimuove dall'elenco un host client o server. |
| [--Aggiungi] | Aggiunge un host client o server all'elenco. |
| [--TCP] | Utilizzare TCP durante la comunicazione con l'host. |
| [--UDP] | Utilizzare UDP durante la comunicazione con l'host. |

## <a name="system-actions"></a>Azioni di sistema
La tabella seguente descrive i comandi disponibili per eseguire diverse azioni di sistema all'interno di Defender per l'it:

|Nome|Codice|Descrizione|
|----|----|-----------|
|Mostra la data|`date`|Restituisce la data corrente nell'host in formato GMT.|
|Riavviare l'host|`system reboot`|Riavvia il dispositivo host.|
|Arrestare l'host|`system shutdown`|Arresta l'host.|
|Eseguire il backup del sistema|`system backup`|Avvia un backup immediato (un backup non pianificato).|
|Ripristinare il sistema da un backup|`system restore`|Ripristina dal backup più recente.|
|Elencare i file di backup|`system backup-list`|Elenca i file di backup disponibili.|
|Visualizza lo stato di tutte le Defender per i servizi della piattaforma Internet|`system sanity`|Controlla le prestazioni del sistema elencando lo stato corrente di tutte le Defender per i servizi della piattaforma Internet.|
|Mostra la versione del software|`system version`|Visualizza la versione del software attualmente in esecuzione nel sistema.|

## <a name="deploy-ssl-and-tls-certificates-to-appliances"></a>Distribuire certificati SSL e TLS alle appliance

Immettere il comando seguente per importare i certificati Enterprise SSL e TLS nell'interfaccia della riga di comando:

```azurecli-interactive
cyberx-xsense-certificate-import
```
Per usare lo strumento, è necessario caricare i file del certificato nel dispositivo. Questa operazione può essere eseguita tramite strumenti come WinSCP o wget. 

Il comando supporta i flag di input seguenti:

| Flag | Descrizione |
|--|--|
| -H | Mostra la sintassi della guida della riga di comando. |
| --CRT | Percorso del file di certificato (estensione CRT). |
| --key | \*File con estensione Key. La lunghezza della chiave deve essere almeno di 2.048 bit. |
| -catena | Percorso del file della catena di certificati (facoltativo). |
| --Pass | Passphrase utilizzata per crittografare il certificato (facoltativo). |
| --passphrase-set | Il valore predefinito è **false**, non **usato**. <br />Impostare su **true** per usare la passphrase precedente fornita con il certificato precedente (facoltativo). | 

Quando si usa lo strumento:

- Verificare che i file di certificato siano leggibili nell'appliance. 

- Verificare che il dominio dell'appliance (come appare nel certificato) con il server DNS e l'indirizzo IP corrispondente. 
    
## <a name="see-also"></a>Vedi anche

[Defender per le API del sensore API e della console di gestione](references-work-with-defender-for-iot-apis.md)
