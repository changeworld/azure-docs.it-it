---
title: Prerequisiti della cache HPC di Azure
description: Prerequisiti per l'uso della cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: 7a91cf5f9341d2b42f1c8f242d288b4ee59b632d
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471801"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Prerequisiti per cache HPC di Azure

Prima di usare la portale di Azure per creare una nuova cache HPC di Azure, assicurarsi che l'ambiente soddisfi questi requisiti.

## <a name="video-overviews"></a>Panoramica video

Guardare questi video per una rapida panoramica dei componenti del sistema e di cosa è necessario per collaborare.

(Fare clic sull'immagine del video o sul collegamento da controllare.)

* [Come funziona](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) : illustra il modo in cui la cache HPC di Azure interagisce con i client e l'archiviazione

  [![immagine di anteprima video: cache HPC di Azure: come funziona (fare clic per visitare la pagina video)](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [Prerequisiti](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) : descrive i requisiti per l'archiviazione NAS, l'archiviazione BLOB di Azure, l'accesso alla rete e l'accesso client

  [![immagine di anteprima video: cache HPC di Azure: prerequisiti (fare clic per visitare la pagina video)](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

Per indicazioni specifiche, leggere la parte restante di questo articolo.

## <a name="azure-subscription"></a>Sottoscrizione di Azure

È consigliabile usare una sottoscrizione A pagamento.

## <a name="network-infrastructure"></a>Infrastruttura di rete

Prima di poter usare la cache, è necessario configurare due prerequisiti relativi alla rete:

* Una subnet dedicata per l'istanza di cache HPC di Azure
* Supporto DNS in modo che la cache possa accedere all'archiviazione e ad altre risorse

### <a name="cache-subnet"></a>Subnet cache

Per la cache HPC di Azure è necessaria una subnet dedicata con queste qualità:

* La subnet deve avere almeno 64 indirizzi IP disponibili.
* La subnet non può ospitare altre macchine virtuali, anche per servizi correlati come i computer client.
* Se si usano più istanze di cache HPC di Azure, ciascuna di esse richiede una propria subnet.

La procedura consigliata consiste nel creare una nuova subnet per ogni cache. È possibile creare una nuova rete virtuale e una nuova subnet come parte della creazione della cache.

### <a name="dns-access"></a>Accesso DNS

Per accedere alle risorse all'esterno della rete virtuale, la cache deve essere DNS. A seconda delle risorse usate, potrebbe essere necessario configurare un server DNS personalizzato e configurare l'invio tra il server e i server DNS di Azure:

* Per accedere agli endpoint di archiviazione BLOB di Azure e ad altre risorse interne, è necessario il server DNS basato su Azure.
* Per accedere all'archiviazione locale, è necessario configurare un server DNS personalizzato in grado di risolvere i nomi host di archiviazione. È necessario eseguire questa operazione **prima** di creare la cache.

Se è necessario accedere solo all'archiviazione BLOB, è possibile usare il server DNS predefinito fornito da Azure per la cache. Tuttavia, se è necessario accedere ad altre risorse, è necessario creare un server DNS personalizzato e configurarlo per l'invio di eventuali richieste di risoluzione specifiche di Azure al server DNS di Azure.

Per usare un server DNS personalizzato, è necessario eseguire questi passaggi di configurazione prima di creare la cache:

* Creare la rete virtuale in cui viene ospitata la cache HPC di Azure.
* Creare il server DNS.
* Aggiungere il server DNS alla rete virtuale della cache.

  Seguire questa procedura per aggiungere il server DNS alla rete virtuale nel portale di Azure:

  1. Aprire la rete virtuale nella portale di Azure.
  1. Scegliere **server DNS** dal menu **Impostazioni** della barra laterale.
  1. Selezionare **Personalizzata**
  1. Immettere l'indirizzo IP del server DNS nel campo.

Un server DNS semplice può anche essere usato per bilanciare il carico delle connessioni client tra tutti i punti di montaggio della cache disponibili.

Per altre informazioni sulle reti virtuali di Azure e sulle configurazioni del server DNS, vedere [risoluzione dei nomi per le risorse nelle reti virtuali di Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="permissions"></a>Autorizzazioni

Verificare questi prerequisiti relativi alle autorizzazioni prima di iniziare a creare la cache.

* L'istanza della cache deve essere in grado di creare interfacce di rete virtuali (NIC). Per creare schede di interfaccia di rete, l'utente che crea la cache deve disporre di privilegi sufficienti nella sottoscrizione.

* Se si usa l'archiviazione BLOB, la cache HPC di Azure deve disporre dell'autorizzazione per accedere all'account di archiviazione. Usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per concedere all'archivio BLOB l'accesso alla cache. Sono necessari due ruoli: collaboratore account di archiviazione e collaboratore dati BLOB di archiviazione.

  Per aggiungere i ruoli, seguire le istruzioni riportate in [aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) .

## <a name="storage-infrastructure"></a>Infrastruttura di archiviazione
<!-- heading is linked in create storage target GUI as aka.ms/hpc-cache-prereq#storage-infrastructure - make sure to fix that if you change the wording of this heading -->

La cache supporta i contenitori BLOB di Azure, le esportazioni di archiviazione hardware NFS e i contenitori BLOB ADLS montati su NFS (attualmente in anteprima). Aggiungere le destinazioni di archiviazione dopo la creazione della cache.

Ogni tipo di archiviazione ha prerequisiti specifici.

### <a name="blob-storage-requirements"></a>Requisiti per l'archiviazione BLOB

Se si vuole usare l'archiviazione BLOB di Azure con la cache, è necessario un account di archiviazione compatibile e un contenitore BLOB vuoto o un contenitore popolato con i dati formattati della cache HPC di Azure, come descritto in [spostare i dati nell'archivio BLOB di Azure](hpc-cache-ingest.md).

> [!NOTE]
> Requisiti diversi si applicano all'archiviazione BLOB montata da NFS. Per informazioni dettagliate, leggere i [requisiti di archiviazione ADLS-NFS](#nfs-mounted-blob-adls-nfs-storage-requirements-preview) .

Creare l'account prima di provare ad aggiungere una destinazione di archiviazione. È possibile creare un nuovo contenitore quando si aggiunge la destinazione.

Per creare un account di archiviazione compatibile, usare le impostazioni seguenti:

* Prestazioni: **standard**
* Tipo di account: **archiviazione V2 (utilizzo generico v2)**
* Replica: **archiviazione con ridondanza locale (con ridondanza locale)**
* Livello di accesso (predefinito): **attivo**

È consigliabile usare un account di archiviazione nella stessa posizione della cache.

È anche necessario concedere all'applicazione cache l'accesso all'account di archiviazione di Azure, come indicato nelle [autorizzazioni](#permissions)sopra riportate. Seguire la procedura descritta in [aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) per assegnare alla cache i ruoli di accesso necessari. Se non si è il proprietario dell'account di archiviazione, chiedere al proprietario di eseguire questo passaggio.

### <a name="nfs-storage-requirements"></a>Requisiti di archiviazione NFS
<!-- linked from configuration.md -->

Se si usa un sistema di archiviazione NFS (ad esempio, un sistema NAS hardware locale), assicurarsi che soddisfi questi requisiti. Per verificare queste impostazioni, potrebbe essere necessario collaborare con gli amministratori di rete o i responsabili del firewall per il sistema di archiviazione (o data center).

> [!NOTE]
> La creazione della destinazione di archiviazione avrà esito negativo se la cache non dispone di accesso sufficiente al sistema di archiviazione NFS.

Altre informazioni sono disponibili in [risolvere i problemi relativi alla configurazione di NAS e alla destinazione di archiviazione NFS](troubleshoot-nas.md).

* **Connettività di rete:** La cache HPC di Azure necessita di un accesso di rete a larghezza di banda elevata tra la subnet della cache e il data center del sistema NFS. È consigliabile usare [ExpressRoute](../expressroute/index.yml) o un accesso simile. Se si usa una VPN, potrebbe essere necessario configurarla in modo da bloccare la connessione MSS TCP a 1350 per assicurarsi che i pacchetti di grandi dimensioni non vengano bloccati. Per ulteriori informazioni sulla risoluzione dei problemi relativi alle impostazioni VPN, vedere [restrizioni sulle dimensioni del pacchetto VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) .

* **Accesso alla porta:** La cache deve accedere a porte TCP/UDP specifiche sul sistema di archiviazione. Tipi diversi di archiviazione hanno requisiti di porta diversi.

  Per verificare le impostazioni del sistema di archiviazione, seguire questa procedura.

  * Eseguire un `rpcinfo` comando nel sistema di archiviazione per verificare le porte necessarie. Il comando seguente elenca le porte e formatta i risultati rilevanti in una tabella. (Usare l'indirizzo IP del sistema al posto del *<storage_IP termine>* .)

    È possibile eseguire questo comando da qualsiasi client Linux in cui è installata l'infrastruttura NFS. Se si usa un client all'interno della subnet del cluster, può essere utile anche per verificare la connettività tra la subnet e il sistema di archiviazione.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Assicurarsi che tutte le porte restituite dalla ``rpcinfo`` query consentano il traffico illimitato dalla subnet della cache HPC di Azure.

  * Se non è possibile usare il `rpcinfo` comando, assicurarsi che queste porte usate di frequente consentano il traffico in ingresso e in uscita:

    | Protocollo | Porta  | Servizio  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | mountd   |
    | TCP/UDP  | 4047  | status   |

    Alcuni sistemi utilizzano numeri di porta diversi per questi servizi. per assicurarsi, consultare la documentazione del sistema di archiviazione.

  * Controllare le impostazioni del firewall per assicurarsi che consentano il traffico su tutte le porte necessarie. Assicurarsi di controllare i firewall usati in Azure e nei firewall locali nel data center.

* **Accesso alla directory:** Abilitare il `showmount` comando nel sistema di archiviazione. Cache HPC di Azure usa questo comando per verificare che la configurazione di destinazione di archiviazione punti a un'esportazione valida e anche per assicurarsi che più montaggi non accedano alle stesse sottodirectory (rischio per collisioni di file).

  > [!NOTE]
  > Se il sistema di archiviazione NFS usa il sistema operativo ONTAP 9,2 di NetApp, non **abilitare `showmount`**. Per assistenza, [contattare il servizio Microsoft e il supporto tecnico](hpc-cache-support-ticket.md) .

  Altre informazioni sull'accesso all'elenco delle directory sono disponibili nell' [articolo sulla risoluzione dei problemi](troubleshoot-nas.md#enable-export-listing)di destinazione di archiviazione NFS.

* **Accesso alla radice** (lettura/scrittura): la cache si connette al sistema back-end come ID utente 0. Controllare le impostazioni seguenti nel sistema di archiviazione:
  
  * Abilitare `no_root_squash`. Questa opzione assicura che l'utente root remoto possa accedere ai file di proprietà della radice.

  * Controllare Esporta criteri per assicurarsi che non includano restrizioni per l'accesso radice dalla subnet della cache.

  * Se per l'archiviazione sono presenti esportazioni che sono sottodirectory di un'altra esportazione, verificare che la cache disponga dell'accesso radice al segmento più basso del percorso. Per informazioni dettagliate, vedere [accesso alla radice nei percorsi di directory](troubleshoot-nas.md#allow-root-access-on-directory-paths) nell'articolo Risoluzione dei problemi di destinazione archiviazione NFS.

* L'archiviazione back-end NFS deve essere una piattaforma hardware/software compatibile. Per informazioni dettagliate, contattare il team di cache HPC di Azure.

### <a name="nfs-mounted-blob-adls-nfs-storage-requirements-preview"></a>Requisiti di archiviazione BLOB montati per NFS (ADLS-NFS) (anteprima)

La cache HPC di Azure può anche usare un contenitore BLOB montato con il protocollo NFS come destinazione di archiviazione.

> [!NOTE]
> Il supporto del protocollo NFS 3,0 per archiviazione BLOB di Azure è in versione di anteprima pubblica. La disponibilità è limitata ed è possibile che le funzionalità cambino tra ora e quando la funzionalità diventa disponibile a livello generale. Non usare la tecnologia di anteprima nei sistemi di produzione.
>
> Per altre informazioni su questa funzionalità di anteprima, vedere [supporto del protocollo NFS 3,0 nell'archivio BLOB di Azure](../storage/blobs/network-file-system-protocol-support.md).

I requisiti dell'account di archiviazione sono diversi per una destinazione di archiviazione BLOB ADLS-NFS e per una destinazione di archiviazione BLOB standard. Per creare e configurare l'account di archiviazione abilitato per NFS, seguire le istruzioni riportate in [montare l'archiviazione BLOB usando il protocollo NFS (Network File System) 3,0](../storage/blobs/network-file-system-protocol-support-how-to.md) .

Si tratta di una panoramica generale dei passaggi:

1. Assicurarsi che le funzionalità necessarie siano disponibili nelle aree in cui si prevede di lavorare.

1. Abilitare la funzionalità del protocollo NFS per la sottoscrizione. Eseguire questa operazione *prima* di creare l'account di archiviazione.

1. Creare una rete virtuale protetta (VNet) per l'account di archiviazione. È consigliabile usare la stessa rete virtuale per l'account di archiviazione abilitato per NFS e per la cache HPC di Azure.

1. Creare l'account di archiviazione.

   * Invece di usare le impostazioni dell'account di archiviazione per un account di archiviazione BLOB standard, seguire le istruzioni riportate nella [documentazione relativa alle procedure](../storage/blobs/network-file-system-protocol-support-how-to.md). Il tipo di account di archiviazione supportato può variare in base all'area di Azure.

   * Nella sezione **rete** scegliere un endpoint privato nella rete virtuale protetta creata (scelta consigliata) oppure scegliere un endpoint pubblico con accesso limitato dal VNet protetto.

   * Non dimenticare di completare la sezione **Avanzate** , in cui è possibile abilitare l'accesso a NFS.

   * Assegnare all'applicazione cache l'accesso all'account di archiviazione di Azure, come indicato nelle [autorizzazioni](#permissions)sopra riportate. Questa operazione può essere eseguita la prima volta che si crea una destinazione di archiviazione. Seguire la procedura descritta in [aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) per assegnare alla cache i ruoli di accesso necessari.

     Se non si è il proprietario dell'account di archiviazione, chiedere al proprietario di eseguire questo passaggio.

## <a name="set-up-azure-cli-access-optional"></a>Configurare l'accesso dell'interfaccia della riga di comando di Azure (facoltativo)

Se si vuole creare o gestire la cache HPC di Azure dall'interfaccia della riga di comando di Azure, è necessario installare il software CLI e l'estensione della cache HPC. Seguire le istruzioni in [configurare l'interfaccia della riga di comando di Azure per la cache HPC di Azure](az-cli-prerequisites.md).

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'istanza di cache HPC di Azure](hpc-cache-create.md) dalla portale di Azure
