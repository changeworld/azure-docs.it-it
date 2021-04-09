---
title: Configurare le impostazioni della cache HPC di Azure
description: Viene illustrato come configurare impostazioni aggiuntive per la cache, ad esempio MTU, configurazione NTP e DNS personalizzate, e come accedere agli snapshot rapidi dalle destinazioni di archiviazione BLOB di Azure.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/17/2021
ms.author: v-erkel
ms.openlocfilehash: 6e1e1283cb82dcb900da6473de65ef087a5cea82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773233"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Configurare altre impostazioni della cache HPC di Azure

La pagina **rete** nel portale di Azure dispone di opzioni per la personalizzazione di diverse impostazioni. La maggior parte degli utenti non è necessario modificare queste impostazioni dai valori predefiniti.

Questo articolo descrive anche come usare la funzionalità snapshot per le destinazioni di archiviazione BLOB di Azure. La funzionalità snapshot non contiene impostazioni configurabili.

Per visualizzare le impostazioni, aprire la pagina **rete** della cache nel portale di Azure.

![screenshot della pagina di rete in portale di Azure](media/networking-page.png)

> [!NOTE]
> Una versione precedente di questa pagina includeva un'impostazione di squash radice a livello di cache, ma questa impostazione è stata spostata nei [criteri di accesso client](access-policies.md).

<!-- >> [!TIP]
> The [Managing Azure HPC Cache video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) shows the networking page and its settings. -->

## <a name="adjust-mtu-value"></a>Regolazione del valore MTU
<!-- linked from troubleshoot-nas article -->

È possibile selezionare le dimensioni massime dell'unità di trasmissione per la cache usando il menu a discesa con etichetta **dimensioni MTU**.

Il valore predefinito è 1500 byte, ma è possibile modificarlo in 1400.

> [!NOTE]
> Se si riducono le dimensioni MTU della cache, assicurarsi che i client e i sistemi di archiviazione che comunicano con la cache abbiano la stessa impostazione MTU o un valore inferiore.

L'abbassamento del valore MTU della cache può essere utile per aggirare le restrizioni relative alle dimensioni dei pacchetti nel resto della rete della cache. Alcune VPN, ad esempio, non possono trasmettere correttamente i pacchetti a 1500 byte a dimensione intera. La riduzione delle dimensioni dei pacchetti inviati tramite la VPN potrebbe eliminare il problema. Si noti tuttavia che un'impostazione di MTU inferiore della cache significa che tutti gli altri componenti che comunicano con la cache, inclusi i client e i sistemi di archiviazione, devono avere anche un'impostazione MTU inferiore per evitare problemi di comunicazione.

Se non si desidera modificare le impostazioni MTU su altri componenti di sistema, è consigliabile non abbassare l'impostazione MTU della cache. Sono disponibili altre soluzioni per aggirare le restrizioni relative alle dimensioni del pacchetto VPN. Per altre informazioni sulla diagnosi e la risoluzione di questo problema, vedere [modificare le restrizioni sulle dimensioni del pacchetto VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) nell'articolo sulla risoluzione dei problemi di NAS.

Per altre informazioni sulle impostazioni MTU nelle reti virtuali di Azure, vedere [ottimizzazione delle prestazioni TCP/IP per le macchine virtuali di Azure](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="customize-ntp"></a>Personalizzare NTP

Per impostazione predefinita, la cache usa il server di tempo basato su Azure time.microsoft.com. Se si vuole che la cache usi un server NTP diverso, specificarlo nella sezione **configurazione NTP** . Usare un nome di dominio completo o un indirizzo IP.

## <a name="set-a-custom-dns-configuration"></a>Impostare una configurazione DNS personalizzata

> [!CAUTION]
> Non modificare la configurazione DNS della cache se non è necessario. Gli errori di configurazione possono avere conseguenze terribili. Se la configurazione non riesce a risolvere i nomi dei servizi di Azure, l'istanza della cache HPC diventerà irraggiungibile in modo permanente.

La cache HPC di Azure viene configurata automaticamente per l'uso del sistema DNS di Azure sicuro e comodo. Tuttavia, alcune configurazioni insolite richiedono che la cache usi un sistema DNS locale separato invece del sistema Azure. La sezione **configurazione DNS** della pagina **rete** viene utilizzata per specificare questo tipo di sistema.

Contattare i rappresentanti di Azure o consultare il servizio Microsoft e il supporto tecnico per determinare se è necessario o meno usare una configurazione DNS della cache personalizzata.

Se si configura un sistema DNS locale per la cache HPC di Azure da usare, è necessario assicurarsi che la configurazione sia in grado di risolvere i nomi degli endpoint di Azure per i servizi di Azure. È necessario configurare l'ambiente DNS personalizzato per l'invio di determinate richieste di risoluzione dei nomi a DNS di Azure o a un altro server in base alle esigenze.

Verificare che la configurazione DNS possa risolvere correttamente questi elementi prima di usarla per una cache HPC di Azure:

* ``*.core.windows.net``
* Download dell'elenco di revoche di certificati (CRL) e servizi di verifica del protocollo di stato del certificato online (OCSP). Un elenco parziale viene fornito nell' [elemento regole del firewall](../security/fundamentals/tls-certificate-changes.md#will-this-change-affect-me) alla fine di questo [articolo di Azure TLS](../security/fundamentals/tls-certificate-changes.md), ma è necessario consultare un rappresentante tecnico Microsoft per comprendere tutti i requisiti.
* Il nome di dominio completo del server NTP (time.microsoft.com o un server personalizzato)

Se è necessario impostare un server DNS personalizzato per la cache, usare i campi specificati:

* **Dominio di ricerca DNS** (facoltativo): immettere il dominio di ricerca, ad esempio ``contoso.com`` . È consentito un solo valore oppure è possibile lasciarlo vuoto.
* **Server DNS** : immettere fino a tre server DNS. Specificarli in base all'indirizzo IP.

<!-- 
  > [!NOTE]
  > The cache will use only the first DNS server it successfully finds. -->

Provare a usare una cache di test per controllare e perfezionare la configurazione DNS prima di usarla in un ambiente di produzione.

### <a name="refresh-storage-target-dns"></a>Aggiornare DNS di destinazione di archiviazione

Se il server DNS Aggiorna gli indirizzi IP, le destinazioni di archiviazione NFS associate diventeranno temporaneamente non disponibili. Leggere le informazioni su come aggiornare gli indirizzi IP di sistema DNS personalizzati in [Modifica destinazioni di archiviazione](hpc-cache-edit-storage.md#update-ip-address-custom-dns-configurations-only).

## <a name="view-snapshots-for-blob-storage-targets"></a>Visualizzare gli snapshot per le destinazioni di archiviazione BLOB

Cache HPC di Azure Salva automaticamente gli snapshot di archiviazione per le destinazioni di archiviazione BLOB di Azure. Gli snapshot forniscono un punto di riferimento rapido per il contenuto del contenitore di archiviazione back-end.

Gli snapshot non sono sostitutivi dei backup dei dati e non includono informazioni sullo stato dei dati memorizzati nella cache.

> [!NOTE]
> Questa funzionalità snapshot è diversa dalla funzionalità di snapshot inclusa nel software di archiviazione NetApp o Isilon. Queste implementazioni di snapshot scaricano le modifiche dalla cache al sistema di archiviazione back-end prima di acquisire lo snapshot.
>
> Per maggiore efficienza, lo snapshot della cache HPC di Azure non Scarica prima le modifiche e registra solo i dati che sono stati scritti nel contenitore BLOB. Questo snapshot non rappresenta lo stato dei dati memorizzati nella cache, pertanto potrebbe non includere le modifiche recenti.

Questa funzionalità è disponibile solo per le destinazioni di archiviazione BLOB di Azure e la relativa configurazione non può essere modificata.

Gli snapshot vengono effettuati ogni otto ore, alle ore UTC 0:00, 08:00 e 16:00.

Cache HPC di Azure archivia snapshot giornalieri, settimanali e mensili fino a quando non vengono sostituiti con quelli nuovi. I limiti di conservazione degli snapshot sono:

* Fino a 20 snapshot giornalieri
* Fino a 8 snapshot settimanali
* Fino a 3 snapshot mensili

Accedere agli snapshot dalla `.snapshot` directory nella radice della destinazione di archiviazione BLOB montata.
