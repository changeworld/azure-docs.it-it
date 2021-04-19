---
title: Cache HPC di Azure prerequisiti
description: Prerequisiti per l'uso di Cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/14/2021
ms.author: v-erkel
ms.openlocfilehash: 3cddbba3dca64561d7e2b7b27715152a26a8c9e9
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717586"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Prerequisiti per Cache HPC di Azure

Prima di usare il portale di Azure per creare un nuovo Cache HPC di Azure, assicurarsi che l'ambiente soddisfi questi requisiti.

## <a name="video-overviews"></a>Panoramica dei video

Guardare questi video per una rapida panoramica dei componenti del sistema e di ciò che serve per lavorare insieme.

Fare clic sull'immagine video o sul collegamento da guardare.

* [Funzionamento: spiega](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) come Cache HPC di Azure con l'archiviazione e i client

  [![immagine di anteprima video: Cache HPC di Azure: Come funziona (fare clic per visitare la pagina del video)](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [Prerequisiti: descrive](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) i requisiti per l'archiviazione NAS, l'archiviazione BLOB di Azure, l'accesso alla rete e l'accesso client

  [![immagine di anteprima video: Cache HPC di Azure: Prerequisiti (fare clic per visitare la pagina del video)](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

Per consigli specifici, leggere il resto di questo articolo.

## <a name="azure-subscription"></a>Sottoscrizione di Azure

È consigliabile una sottoscrizione a pagamento.

## <a name="network-infrastructure"></a>Infrastruttura di rete

Prima di poter usare la cache, è necessario configurare due prerequisiti correlati alla rete:

* Subnet dedicata per l'istanza Cache HPC di Azure
* Supporto DNS in modo che la cache possa accedere all'archiviazione e ad altre risorse

### <a name="cache-subnet"></a>Subnet della cache

Il Cache HPC di Azure richiede una subnet dedicata con queste qualità:

* La subnet deve avere almeno 64 indirizzi IP disponibili.
* La subnet non può ospitare altre macchine virtuali, anche per i servizi correlati come i computer client.
* Se si usano più Cache HPC di Azure, ognuna necessita di una propria subnet.

La procedura consigliata consiste nel creare una nuova subnet per ogni cache. È possibile creare una nuova rete virtuale e una nuova subnet come parte della creazione della cache.

### <a name="dns-access"></a>Accesso DNS

La cache necessita di DNS per accedere alle risorse esterne alla rete virtuale. A seconda delle risorse in uso, potrebbe essere necessario configurare un server DNS personalizzato e configurare l'inoltro tra il server e i server DNS di Azure seguenti:

* Per accedere agli endpoint di archiviazione BLOB di Azure e ad altre risorse interne, è necessario il server DNS basato su Azure.
* Per accedere all'archiviazione locale, è necessario configurare un server DNS personalizzato in grado di risolvere i nomi host di archiviazione. È necessario eseguire questa operazione prima di creare la cache.

Se si usa solo l'archiviazione BLOB, è possibile usare il server DNS predefinito fornito da Azure per la cache. Tuttavia, se è necessario accedere all'archiviazione o ad altre risorse all'esterno di Azure, è necessario creare un server DNS personalizzato e configurarlo per inoltrare le richieste di risoluzione specifiche di Azure al server DNS di Azure.

Per usare un server DNS personalizzato, è necessario eseguire questi passaggi di configurazione prima di creare la cache:

* Creare la rete virtuale che ospiterà il Cache HPC di Azure.
* Creare il server DNS.
* Aggiungere il server DNS alla rete virtuale della cache.

  Seguire questa procedura per aggiungere il server DNS alla rete virtuale nel portale di Azure:

  1. Aprire la rete virtuale nel portale di Azure.
  1. Scegliere Server DNS dal menu Impostazioni nella barra laterale.
  1. Selezionare Personalizzata
  1. Immettere l'indirizzo IP del server DNS nel campo .

Un server DNS semplice può essere usato anche per bilanciare il carico delle connessioni client tra tutti i punti di montaggio della cache disponibili.

Per altre informazioni sulle reti virtuali di Azure e sulle configurazioni del server DNS, vedere [Risoluzione dei nomi per le risorse nelle reti virtuali di Azure.](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

## <a name="permissions"></a>Autorizzazioni

Controllare questi prerequisiti correlati alle autorizzazioni prima di iniziare a creare la cache.

* L'istanza della cache deve essere in grado di creare interfacce di rete virtuali. L'utente che crea la cache deve disporre di privilegi sufficienti nella sottoscrizione per creare schede di interfaccia di rete.

* Se si usa l'archiviazione BLOB, Cache HPC di Azure'autorizzazione per accedere all'account di archiviazione. Usare il controllo degli accessi in base al ruolo di Azure per concedere alla cache l'accesso all'archiviazione BLOB. Sono necessari due ruoli: Collaboratore account di archiviazione e Collaboratore dati BLOB di archiviazione.

  Seguire le istruzioni in [Aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) per aggiungere i ruoli.

## <a name="storage-infrastructure"></a>Infrastruttura di archiviazione
<!-- heading is linked in create storage target GUI as aka.ms/hpc-cache-prereq#storage-infrastructure - make sure to fix that if you change the wording of this heading -->

La cache supporta contenitori BLOB di Azure, esportazioni di archiviazione hardware NFS e contenitori BLOB ADLS montati su NFS (attualmente in anteprima). Aggiungere destinazioni di archiviazione dopo aver creato la cache.

Ogni tipo di archiviazione ha prerequisiti specifici.

### <a name="blob-storage-requirements"></a>Requisiti di archiviazione BLOB

Se si vuole usare l'archiviazione BLOB di Azure con la cache, è necessario un account di archiviazione compatibile e un contenitore BLOB vuoto o un contenitore popolato con dati formattati Cache HPC di Azure come descritto in Spostare i dati in Archiviazione BLOB di [Azure.](hpc-cache-ingest.md)

> [!NOTE]
> Requisiti diversi si applicano all'archiviazione BLOB montata su NFS. Per informazioni [dettagliate, vedere Requisiti di archiviazione di ADLS-NFS.](#nfs-mounted-blob-adls-nfs-storage-requirements-preview)

Creare l'account prima di provare ad aggiungere una destinazione di archiviazione. È possibile creare un nuovo contenitore quando si aggiunge la destinazione.

Per creare un account di archiviazione compatibile, usare una delle combinazioni seguenti:

| Prestazioni | Type | Replica | Livello di accesso |
|--|--|--|--|
| Standard | Archiviazione V2 (utilizzo generico v2)| Archiviazione con ridondanza locale (LRS) o archiviazione con ridondanza della zona (ZRS) | Accesso frequente |
| Premium | BLOB in blocchi | Archiviazione con ridondanza locale | Accesso frequente |

L'account di archiviazione deve essere accessibile dalla subnet privata della cache. Se l'account usa un endpoint privato o un endpoint pubblico limitato a reti virtuali specifiche, assicurarsi di abilitare l'accesso dalla subnet della cache. Non è consigliabile un endpoint pubblico aperto.

È consigliabile usare un account di archiviazione nella stessa area di Azure della cache.

È anche necessario concedere all'applicazione cache l'accesso all'account di archiviazione di Azure, come indicato in Autorizzazioni in [precedenza.](#permissions) Seguire la procedura descritta in [Aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) per assegnare alla cache i ruoli di accesso necessari. Se non si è il proprietario dell'account di archiviazione, fare in modo che il proprietario esega questo passaggio.

### <a name="nfs-storage-requirements"></a>Requisiti di archiviazione NFS
<!-- linked from configuration.md -->

Se si usa un sistema di archiviazione NFS, ad esempio un sistema NAS hardware locale, assicurarsi che soddisfi questi requisiti. Potrebbe essere necessario collaborare con gli amministratori di rete o i gestori firewall per il sistema di archiviazione (o data center) per verificare queste impostazioni.

> [!NOTE]
> La creazione della destinazione di archiviazione avrà esito negativo se l'accesso della cache al sistema di archiviazione NFS non è sufficiente.

Per altre informazioni, vedere Risolvere i problemi relativi alla configurazione NAS e alla destinazione [di archiviazione NFS.](troubleshoot-nas.md)

* Connettività di rete: il Cache HPC di Azure richiede l'accesso alla rete con larghezza di banda elevata tra la subnet della cache e le risorse del sistema NFS data center. [È consigliabile expressroute](../expressroute/index.yml) o un accesso simile. Se si usa una VPN, potrebbe essere necessario configurarla per bloccare TCP MSS a 1350 per assicurarsi che i pacchetti di grandi dimensioni non siano bloccati. Leggere Restrizioni [relative alle dimensioni dei pacchetti VPN per](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) altre informazioni sulla risoluzione dei problemi relativi alle impostazioni VPN.

* Accesso alla porta: la cache deve accedere a porte TCP/UDP specifiche nel sistema di archiviazione. Tipi diversi di archiviazione hanno requisiti di porta diversi.

  Per controllare le impostazioni del sistema di archiviazione, seguire questa procedura.

  * Eseguire un `rpcinfo` comando per il sistema di archiviazione per controllare le porte necessarie. Il comando seguente elenca le porte e formatta i risultati pertinenti in una tabella. Usare l'indirizzo IP del sistema al posto del *<storage_IP>* termine.

    È possibile eseguire questo comando da qualsiasi client Linux in cui è installata l'infrastruttura NFS. Se si usa un client all'interno della subnet del cluster, è anche possibile verificare la connettività tra la subnet e il sistema di archiviazione.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Assicurarsi che tutte le porte restituite dalla query consentano il traffico senza restrizioni ``rpcinfo`` dalla subnet Cache HPC di Azure della rete.

  * Se non è possibile usare il comando , assicurarsi che queste porte di uso comune consentano `rpcinfo` il traffico in ingresso e in uscita:

    | Protocollo | Porta  | Servizio  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | montato   |
    | TCP/UDP  | 4047  | status   |

    Alcuni sistemi usano numeri di porta diversi per questi servizi. Per informazioni, consultare la documentazione del sistema di archiviazione.

  * Controllare le impostazioni del firewall per assicurarsi che consentano il traffico su tutte queste porte necessarie. Assicurarsi di controllare i firewall usati in Azure e i firewall locali nel data center.

* Accesso radice (lettura/scrittura): la cache si connette al sistema back-end come ID utente 0. Controllare queste impostazioni nel sistema di archiviazione:
  
  * Abilitare `no_root_squash`. Questa opzione garantisce che l'utente radice remoto possa accedere ai file di proprietà della radice.

  * Controllare i criteri di esportazione per assicurarsi che non includano restrizioni sull'accesso radice dalla subnet della cache.

  * Se l'archiviazione include esportazioni che sono sottodirectory di un'altra esportazione, assicurarsi che la cache abbia accesso radice al segmento più basso del percorso. Per [informazioni dettagliate, vedere Accesso](troubleshoot-nas.md#allow-root-access-on-directory-paths) radice nei percorsi di directory nell'articolo sulla risoluzione dei problemi della destinazione di archiviazione NFS.

* L'archiviazione back-end NFS deve essere una piattaforma hardware/software compatibile. Per informazioni dettagliate, contattare il team di Cache HPC di Azure.

### <a name="nfs-mounted-blob-adls-nfs-storage-requirements-preview"></a>Requisiti di archiviazione blob montati con NFS (ADLS-NFS) (ANTEPRIMA)

Cache HPC di Azure possibile usare un contenitore BLOB montato con il protocollo NFS come destinazione di archiviazione.

> [!NOTE]
> Il supporto del protocollo NFS 3.0 per Archiviazione BLOB di Azure è disponibile in anteprima pubblica. La disponibilità è limitata e le funzionalità potrebbero cambiare tra il momento in cui la funzionalità diventa disponibile a livello generale. Non usare la tecnologia di anteprima nei sistemi di produzione.
>
> Per altre informazioni su questa funzionalità di anteprima, vedere [Supporto del protocollo NFS 3.0 in Archiviazione BLOB di Azure.](../storage/blobs/network-file-system-protocol-support.md)

I requisiti dell'account di archiviazione sono diversi per una destinazione di archiviazione BLOB ADLS-NFS e per una destinazione di archiviazione BLOB standard. Seguire attentamente le istruzioni in Montare l'archiviazione BLOB usando il protocollo [NFS (Network File System) 3.0](../storage/blobs/network-file-system-protocol-support-how-to.md) per creare e configurare l'account di archiviazione abilitato per NFS.

Questa è una panoramica generale dei passaggi. Questi passaggi potrebbero cambiare, quindi fare sempre riferimento alle istruzioni [di ADLS-NFS](../storage/blobs/network-file-system-protocol-support-how-to.md) per i dettagli correnti.

1. Assicurarsi che le funzionalità necessarie siano disponibili nelle aree in cui si prevede di lavorare.

1. Abilitare la funzionalità del protocollo NFS per la sottoscrizione. Eseguire questa operazione *prima* di creare l'account di archiviazione.

1. Creare una rete virtuale sicura per l'account di archiviazione. È consigliabile usare la stessa rete virtuale per l'account di archiviazione abilitato per NFS e per l'Cache HPC di Azure. Non usare la stessa subnet della cache.

1. Creare l'account di archiviazione.

   * Invece di usare le impostazioni dell'account di archiviazione per un account di archiviazione BLOB standard, seguire le istruzioni riportate nel [documento sulle procedure.](../storage/blobs/network-file-system-protocol-support-how-to.md) Il tipo di account di archiviazione supportato può variare in base all'area di Azure.

   * Nella sezione Rete scegliere un endpoint privato nella rete virtuale sicura creata (scelta consigliata) oppure scegliere un endpoint pubblico con accesso limitato dalla rete virtuale protetta.

   * Non dimenticare di completare la sezione Avanzate, in cui si abilita l'accesso NFS.

   * Concedere all'applicazione cache l'accesso all'account di archiviazione di Azure, come indicato in [Autorizzazioni](#permissions), sopra. È possibile eseguire questa operazione la prima volta che si crea una destinazione di archiviazione. Seguire la procedura descritta in [Aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) per assegnare alla cache i ruoli di accesso necessari.

     Se non si è il proprietario dell'account di archiviazione, fare in modo che il proprietario eseega questo passaggio.

Per altre informazioni sull'uso delle destinazioni di archiviazione ADLS-NFS con Cache HPC di Azure, vedere Usare l'archiviazione BLOB montata su [NFS con Cache HPC di Azure](nfs-blob-considerations.md).

## <a name="set-up-azure-cli-access-optional"></a>Configurare l'accesso all'interfaccia della riga di comando di Azure (facoltativo)

Se si vuole creare o gestire Cache HPC di Azure dall'interfaccia della riga di comando di Azure, è necessario installare il software dell'interfaccia della riga di comando e l'estensione hpc-cache. Seguire le istruzioni in [Configurare l'interfaccia della riga di comando di Azure per Cache HPC di Azure](az-cli-prerequisites.md).

## <a name="next-steps"></a>Passaggi successivi

* [Creare un Cache HPC di Azure'istanza dal](hpc-cache-create.md) portale di Azure
