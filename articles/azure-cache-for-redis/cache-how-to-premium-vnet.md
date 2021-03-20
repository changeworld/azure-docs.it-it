---
title: Configurare una rete virtuale-cache di Azure di livello Premium per l'istanza di redis
description: Informazioni su come creare e gestire il supporto della rete virtuale per la cache di Azure di livello Premium per l'istanza di redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 94bbb9bb683f40d44d6649802b66bda6feeee218
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375273"
---
# <a name="configure-virtual-network-support-for-a-premium-azure-cache-for-redis-instance"></a>Configurare il supporto della rete virtuale per un'istanza Premium di cache di Azure per Redis

La distribuzione di [rete virtuale di Azure](https://azure.microsoft.com/services/virtual-network/) offre sicurezza e isolamento avanzati insieme a subnet, criteri di controllo degli accessi e altre funzionalità per limitare ulteriormente l'accesso. Quando una cache di Azure per l'istanza di redis viene configurata con una rete virtuale, non è indirizzabile pubblicamente ed è accessibile solo dalle macchine virtuali e dalle applicazioni all'interno della rete virtuale. Questo articolo descrive come configurare il supporto di una rete virtuale per una cache di Azure di livello Premium per l'istanza di Redis.

> [!NOTE]
> Cache di Azure per Redis supporta sia il modello di distribuzione classica sia le reti virtuali Azure Resource Manager.
> 

## <a name="set-up-virtual-network-support"></a>Configurare il supporto per le reti virtuali

Il supporto per la rete virtuale è configurato nel **nuovo riquadro cache di Azure per Redis** durante la creazione della cache.

1. Per creare una cache di livello Premium, accedere al [portale di Azure](https://portal.azure.com) e selezionare **Crea una risorsa**. Oltre a creare le cache nella portale di Azure, è anche possibile crearle usando modelli Gestione risorse, PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni su come creare una cache di Azure per l'istanza di redis, vedere [creare una cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Screenshot che mostra la creazione di una risorsa.":::
   
1. Nella pagina **nuovo** selezionare **database**. Quindi selezionare **cache di Azure per Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Screenshot che mostra la selezione di cache di Azure per Redis.":::

1. Nella pagina **nuova cache Redis** configurare le impostazioni per la nuova cache di livello Premium.
   
   | Impostazione      | Valore consigliato  | Descrizione |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Immettere un nome univoco globale. | Il nome della cache deve essere una stringa di lunghezza compresa tra 1 e 63 caratteri che contengono solo numeri, lettere o trattini. Il nome deve iniziare e terminare con un numero o una lettera e non può contenere trattini consecutivi. Il *nome host* dell'istanza della cache sarà *\<DNS name>.redis.cache.windows.net*. |
   | **Sottoscrizione** | Selezionare la sottoscrizione dall'elenco a discesa. | Sottoscrizione in cui creare la nuova istanza della cache di Azure per Redis. |
   | **Gruppo di risorse** | Selezionare un gruppo di risorse dall'elenco a discesa oppure selezionare **Crea nuovo** e immettere un nuovo nome per il gruppo di risorse. | Nome del gruppo di risorse in cui creare la cache e altre risorse. L'inserimento di tutte le risorse di un'app in un unico gruppo di risorse ne semplifica la gestione o l'eliminazione. |
   | **Posizione** | selezionare una località dall'elenco a discesa. | Selezionare un'[area](https://azure.microsoft.com/regions/) in prossimità di altri servizi che useranno la cache. |
   | **Tipo di cache** |Selezionare una cache di livello Premium dall'elenco a discesa per configurare le funzionalità del livello Premium. Per altre informazioni, vedere [Prezzi di Cache Redis di Azure](https://azure.microsoft.com/pricing/details/cache/). |  Il piano tariffario determina le dimensioni, le prestazioni e le funzionalità disponibili per la cache. Per altre informazioni, vedere [la Panoramica di cache di Azure per Redis](cache-overview.md). |

1. Selezionare la scheda **rete** oppure fare clic sul pulsante **rete** nella parte inferiore della pagina.

1. Nella scheda **rete** selezionare **reti virtuali** come metodo di connettività. Per usare una nuova rete virtuale, crearla prima di tutto seguendo i passaggi descritti in [creare una rete virtuale usando il portale di Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network) o [creare una rete virtuale (classica) usando l'portale di Azure](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal). Tornare quindi al **nuovo riquadro cache di Azure per Redis** per creare e configurare la cache di livello Premium.

   > [!IMPORTANT]
   > Quando si distribuisce cache di Azure per Redis in una rete virtuale Gestione risorse, la cache deve trovarsi in una subnet dedicata che non contiene altre risorse, ad eccezione di cache di Azure per le istanze di Redis. Se si tenta di distribuire una cache di Azure per l'istanza di redis in una Gestione risorse subnet della rete virtuale che contiene altre risorse, la distribuzione non riesce.
   > 
   > 

   | Impostazione      | Valore consigliato  | Descrizione |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Rete virtuale** | Selezionare la rete virtuale dall'elenco a discesa. | Selezionare una rete virtuale che si trova nella stessa sottoscrizione e nella stessa posizione della cache. |
   | **Subnet** | Selezionare la subnet dall'elenco a discesa. | L'intervallo di indirizzi della subnet deve essere in notazione CIDR (ad esempio, 192.168.1.0/24). Deve essere incluso nello spazio indirizzi della rete virtuale. |
   | **Indirizzo IP statico** | Opzionale Immettere un indirizzo IP statico. | Se non si specifica un indirizzo IP statico, viene scelto automaticamente un indirizzo IP. |

   > [!IMPORTANT]
   > Azure riserva alcuni indirizzi IP all'interno di ogni subnet e questi indirizzi non possono essere usati. Il primo e l'ultimo indirizzo IP delle subnet sono riservati per motivi di conformità al protocollo, insieme ad altri tre indirizzi usati per i servizi di Azure. Per altre informazioni, vedere [Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
   > 
   > Oltre agli indirizzi IP usati dall'infrastruttura di rete virtuale di Azure, ogni cache di Azure per l'istanza di redis nella subnet usa due indirizzi IP per partizione e un indirizzo IP aggiuntivo per il servizio di bilanciamento del carico. Una cache non cluster viene considerata una partizione.
   > 

1. Selezionare la scheda **Avanti: avanzate** oppure fare clic sul pulsante **Avanti: avanzate** nella parte inferiore della pagina.

1. Nella scheda **Avanzate** per un'istanza di cache di livello Premium configurare le impostazioni per la porta non TLS, il clustering e la persistenza dei dati.

1. Selezionare la scheda **Next: Tags (tag successivi** ) oppure fare clic sul pulsante **Next: Tags (tag** ) nella parte inferiore della pagina.

1. Facoltativamente, nella scheda **tag** immettere il nome e il valore se si vuole categorizzare la risorsa.

1. Selezionare **Rivedi e crea**. Si passa alla scheda **Rivedi e crea** in cui Azure convalida la configurazione.

1. Dopo che è stato visualizzato il messaggio di **convalida** verde, selezionare **Crea**.

La creazione della cache richiede un po' di tempo. È possibile monitorare lo stato di avanzamento nella pagina **Panoramica** della cache di Azure per Redis. Quando l'elemento **Stato** indica **In esecuzione**, la cache è pronta per l'uso. Dopo aver creato la cache, è possibile visualizzare la configurazione per la rete virtuale selezionando **rete virtuale** dal menu **delle risorse** .

![Rete virtuale][redis-cache-vnet-info]

Per connettersi all'istanza di cache di Azure per Redis quando si usa una rete virtuale, specificare il nome host della cache nella stringa di connessione, come illustrato nell'esempio seguente:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

## <a name="azure-cache-for-redis-virtual-network-faq"></a>Domande frequenti sulla rete virtuale di cache di Azure per Redis

Nell'elenco seguente sono fornite le risposte alle domande poste comunemente sulla rete virtuale di Cache Redis di Azure.

* Quali sono alcuni problemi comuni di configurazione errata della cache di Azure per Redis e reti virtuali?
* [Come è possibile verificare che la cache funzioni in una rete virtuale?](#how-can-i-verify-that-my-cache-is-working-in-a-virtual-network)
* Quando si tenta di connettersi alla cache di Azure per l'istanza di redis in una rete virtuale, perché viene visualizzato un errore che informa che il certificato remoto non è valido?
* [È possibile usare le reti virtuali con una cache standard o Basic?](#can-i-use-virtual-networks-with-a-standard-or-basic-cache)
* Perché la creazione di una cache di Azure per l'istanza di redis ha esito negativo in alcune subnet e non in altre?
* [Quali sono i requisiti di spazio per gli indirizzi di subnet?](#what-are-the-subnet-address-space-requirements)
* [Tutte le funzionalità della cache funzionano quando una cache è ospitata in una rete virtuale?](#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks"></a>Quali sono alcuni problemi comuni di configurazione errata della cache di Azure per Redis e reti virtuali?

Quando la cache di Azure per Redis è ospitata in una rete virtuale, vengono usate le porte nelle tabelle seguenti.

>[!IMPORTANT]
>Se le porte nelle tabelle seguenti sono bloccate, la cache potrebbe non funzionare correttamente. Il blocco di una o più di queste porte è il problema di configurazione più comune quando si usa cache di Azure per Redis in una rete virtuale.
> 

- [Requisiti delle porte in uscita](#outbound-port-requirements)
- [Requisiti delle porte in ingresso](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Requisiti delle porte in uscita

Sono previsti nove requisiti per le porte in uscita. Le richieste in uscita in questi intervalli sono in uscita con altri servizi necessari per il funzionamento della cache o interni alla subnet Redis per la comunicazione tra nodi. Per la replica geografica sono disponibili altri requisiti in uscita per la comunicazione tra subnet della cache primaria e di replica.

| Porte | Direction | Protocollo di trasporto | Scopo | IP locale | IP remoto |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |In uscita |TCP |Dipendenze Redis in archiviazione di Azure/PKI (Internet) | (Subnet Redis) |* <sup>4</sup> |
| 443 | In uscita | TCP | Dipendenza Redis da Azure Key Vault e monitoraggio di Azure | (Subnet Redis) | AzureKeyVault, AzureMonitor <sup>1</sup> |
| 53 |In uscita |TCP/UDP |Dipendenze redis su DNS (Internet/rete virtuale) | (Subnet Redis) | 168.63.129.16 e 169.254.169.254 <sup>2</sup> e qualsiasi server DNS personalizzato per la subnet <sup>3</sup> |
| 8443 |In uscita |TCP |Comunicazioni interne per Redis | (Subnet Redis) | (Subnet Redis) |
| 10221-10231 |In uscita |TCP |Comunicazioni interne per Redis | (Subnet Redis) | (Subnet Redis) |
| 20226 |In uscita |TCP |Comunicazioni interne per Redis | (Subnet Redis) |(Subnet Redis) |
| 13000-13999 |In uscita |TCP |Comunicazioni interne per Redis | (Subnet Redis) |(Subnet Redis) |
| 15000-15999 |In uscita |TCP |Comunicazioni interne per Redis e la replica geografica | (Subnet Redis) |(Subnet Redis) (Subnet peer di replica geografica) |
| 6379-6380 |In uscita |TCP |Comunicazioni interne per Redis | (Subnet Redis) |(Subnet Redis) |

<sup>1</sup> è possibile usare i tag di servizio AzureKeyVault e AzureMonitor con gestione risorse gruppi di sicurezza di rete (gruppi).

<sup>2</sup> questi indirizzi IP di proprietà di Microsoft vengono usati per gestire la macchina virtuale host che funge da DNS di Azure.

<sup>3</sup> queste informazioni non sono necessarie per le subnet senza server DNS personalizzato o cache Redis più recenti che ignorano il DNS personalizzato.

<sup>4</sup> per ulteriori informazioni, vedere [requisiti di connettività di rete virtuale aggiuntivi](#additional-virtual-network-connectivity-requirements).

#### <a name="geo-replication-peer-port-requirements"></a>Requisiti per le porte peer per la replica geografica

Se si usa la replica geografica tra le cache nelle reti virtuali di Azure, sbloccare le porte 15000-15999 per l'intera subnet nelle direzioni in ingresso *e* in uscita per entrambe le cache. Con questa configurazione, tutti i componenti di replica nella subnet possono comunicare direttamente tra loro, anche se esiste un futuro failover geografico.

#### <a name="inbound-port-requirements"></a>Requisiti delle porte in ingresso

Vi sono otto requisiti delle porte in ingresso. Le richieste in ingresso in questi intervalli sono in ingresso da altri servizi ospitati nella stessa rete virtuale o interne alle comunicazioni della subnet Redis.

| Porte | Direction | Protocollo di trasporto | Scopo | IP locale | IP remoto |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |In ingresso |TCP |Comunicazione tra client e Redis, bilanciamento del carico di Azure | (Subnet Redis) | (Subnet Redis), (subnet client), AzureLoadBalancer <sup>1</sup> |
| 8443 |In ingresso |TCP |Comunicazioni interne per Redis | (Subnet Redis) |(Subnet Redis) |
| 8500 |In ingresso |TCP/UDP |Bilanciamento del carico di Azure | (Subnet Redis) | AzureLoadBalancer |
| 10221-10231 |In ingresso |TCP |Comunicazione tra client e cluster Redis, comunicazioni interne per Redis | (Subnet Redis) |(Subnet Redis), AzureLoadBalancer, (subnet client) |
| 13000-13999 |In ingresso |TCP |Comunicazione tra client e cluster Redis, bilanciamento del carico di Azure | (Subnet Redis) | (Subnet Redis), (subnet client), AzureLoadBalancer |
| 15000-15999 |In ingresso |TCP |Comunicazione tra client e cluster Redis, bilanciamento del carico di Azure e replica geografica | (Subnet Redis) | (Subnet Redis), (subnet client), AzureLoadBalancer, (subnet peer di replica geografica) |
| 16001 |In ingresso |TCP/UDP |Bilanciamento del carico di Azure | (Subnet Redis) | AzureLoadBalancer |
| 20226 |In ingresso |TCP |Comunicazioni interne per Redis | (Subnet Redis) |(Subnet Redis) |

<sup>1</sup> è possibile usare il tag di servizio AzureLoadBalancer per Gestione risorse o AZURE_LOADBALANCER per il modello di distribuzione classica per la creazione delle regole NSG.

#### <a name="additional-virtual-network-connectivity-requirements"></a>Requisiti aggiuntivi per la connettività della rete virtuale

Esistono requisiti di connettività di rete per cache di Azure per Redis che potrebbero non essere inizialmente soddisfatti in una rete virtuale. Cache di Azure per Redis richiede che tutti gli elementi seguenti funzionino correttamente quando vengono usati in una rete virtuale:

* Connettività di rete in uscita per endpoint di archiviazione di Azure in tutto il mondo. Sono inclusi gli endpoint che si trovano nella stessa area della cache di Azure per l'istanza di redis e gli endpoint di archiviazione che si trovano in *altre* aree di Azure. Gli endpoint di archiviazione di Azure vengono risolti nei seguenti domini DNS: *Table.Core.Windows.NET*, *BLOB.Core.Windows.NET*, *queue.Core.Windows.NET* e *file.Core.Windows.NET*.
* Connettività di rete in uscita a *OCSP.DigiCert.com*, *CRL4.DigiCert.com*, *OCSP.msocsp.com*, *mscrl.Microsoft.com*, *crl3.DigiCert.com*, *cacerts.DigiCert.com*, *oneocsp.Microsoft.com* e *CRL.Microsoft.com*. Questa connettività è necessaria per supportare la funzionalità TLS/SSL.
* La configurazione DNS per la rete virtuale deve essere in grado di risolvere tutti gli endpoint e i domini indicati nei punti precedenti. Questi requisiti DNS possono essere soddisfatti garantendo che un'infrastruttura DNS valida venga configurata e mantenuta per la rete virtuale.
* Connettività di rete in uscita agli endpoint di monitoraggio di Azure seguenti, che vengono risolti nei seguenti domini DNS: *shoebox2-Black.shoebox2.Metrics.nsatc.NET*, *North-prod2.prod2.Metrics.nsatc.NET*, *azglobal-Black.azglobal.Metrics.nsatc.NET*, *shoebox2-Red.shoebox2.Metrics.nsatc.NET*, *East-prod2.prod2.Metrics.nsatc.NET*, *azglobal-Red.azglobal.Metrics.nsatc.NET*, *shoebox3.prod.microsoftmetrics.com*, *shoebox3-Red.prod.microsoftmetrics.com* e *shoebox3-Black.prod.microsoftmetrics.com*.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-virtual-network"></a>Come è possibile verificare che la cache funzioni in una rete virtuale?

>[!IMPORTANT]
>Quando ci si connette a una cache di Azure per l'istanza di redis ospitata in una rete virtuale, i client della cache devono trovarsi nella stessa rete virtuale o in una rete virtuale con peering di rete virtuale abilitato nella stessa area di Azure. Il peering di rete virtuale globale attualmente non è supportato. Questo requisito si applica a qualsiasi applicazione di test o strumenti di ping di diagnostica. Indipendentemente dalla posizione in cui è ospitata l'applicazione client, è necessario configurare gruppi o altri livelli di rete in modo che il traffico di rete del client sia autorizzato a raggiungere la cache di Azure per l'istanza di Redis.
>

Una volta configurati i requisiti della porta come descritto nella sezione precedente, è possibile verificare che la cache funzioni attenendosi alla procedura seguente:

- [Riavviare](cache-administration.md#reboot) tutti i nodi della cache. Se non è possibile raggiungere tutte le dipendenze della cache necessarie, come descritto nei requisiti della [porta in ingresso](cache-how-to-premium-vnet.md#inbound-port-requirements) e nei requisiti della [porta in uscita](cache-how-to-premium-vnet.md#outbound-port-requirements), la cache non potrà essere riavviata correttamente.
- Dopo il riavvio dei nodi della cache, come riportato dallo stato della cache nella portale di Azure, è possibile eseguire i test seguenti:
  - Eseguire il ping dell'endpoint della cache usando la porta 6380 da un computer che si trova all'interno della stessa rete virtuale della cache, usando [tcping](https://www.elifulkerson.com/projects/tcping.php). Ad esempio:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Se lo `tcping` strumento segnala che la porta è aperta, la cache è disponibile per la connessione dai client nella rete virtuale.

  - Un altro modo per eseguire il test è quello di creare un client della cache di test (che potrebbe essere una semplice applicazione della console tramite StackExchange.Redis) che si connette alla cache, aggiunge e recupera alcuni elementi dalla cache. Installare l'applicazione client di esempio in una VM che si trova nella stessa rete virtuale della cache. Eseguirlo quindi per verificare la connettività alla cache.


### <a name="when-i-try-to-connect-to-my-azure-cache-for-redis-instance-in-a-virtual-network-why-do-i-get-an-error-stating-the-remote-certificate-is-invalid"></a>Quando si tenta di connettersi alla cache di Azure per l'istanza di redis in una rete virtuale, perché viene visualizzato un errore che informa che il certificato remoto non è valido?

Quando si tenta di connettersi a una cache di Azure per l'istanza di redis in una rete virtuale, viene visualizzato un errore di convalida del certificato, ad esempio:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

La ragione potrebbe essere che ci si connette all'host tramite l'indirizzo IP. Si consiglia di usare il nome host. In altre parole, usare la stringa seguente:

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Evitare di usare l'indirizzo IP simile alla stringa di connessione seguente:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Se non si riesce a risolvere il nome DNS, alcune librerie client includono opzioni di configurazione come `sslHost` , fornite dal client stackexchange. Redis. Questa opzione consente di eseguire l'override del nome host utilizzato per la convalida del certificato. Ad esempio:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-virtual-networks-with-a-standard-or-basic-cache"></a>È possibile usare le reti virtuali con una cache standard o Basic?

Le reti virtuali possono essere usate solo con le cache di livello Premium.

### <a name="why-does-creating-an-azure-cache-for-redis-instance-fail-in-some-subnets-but-not-others"></a>Perché la creazione di una cache di Azure per l'istanza di redis ha esito negativo in alcune subnet e non in altre?

Se si distribuisce una cache di Azure per l'istanza di redis in una rete virtuale, la cache deve trovarsi in una subnet dedicata che non contiene altri tipi di risorse. Se viene effettuato un tentativo di distribuire un'istanza di cache di Azure per Redis in una subnet di rete virtuale Gestione risorse che contiene altre risorse, ad esempio applicazione Azure istanze del gateway e NAT in uscita, la distribuzione in genere avrà esito negativo. Prima di poter creare una nuova cache di Azure per l'istanza di redis, è necessario eliminare le risorse esistenti di altri tipi.

È inoltre necessario disporre di un numero sufficiente di indirizzi IP disponibili nella subnet.

### <a name="what-are-the-subnet-address-space-requirements"></a>Quali sono i requisiti di spazio per gli indirizzi di subnet?

Azure riserva alcuni indirizzi IP all'interno di ogni subnet e questi indirizzi non possono essere usati. Il primo e l'ultimo indirizzo IP delle subnet sono riservati per motivi di conformità al protocollo, insieme ad altri tre indirizzi usati per i servizi di Azure. Per altre informazioni, vedere [Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Oltre agli indirizzi IP usati dall'infrastruttura di rete virtuale di Azure, ogni cache di Azure per l'istanza di redis nella subnet usa due indirizzi IP per ogni partizione del cluster, oltre a indirizzi IP aggiuntivi per eventuali repliche aggiuntive. Per il servizio di bilanciamento del carico viene usato un indirizzo IP aggiuntivo. Una cache non cluster viene considerata una partizione.

### <a name="do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network"></a>Tutte le funzionalità della cache funzionano quando una cache è ospitata in una rete virtuale?

Quando la cache fa parte di una rete virtuale, solo i client nella rete virtuale possono accedere alla cache. Di conseguenza, le seguenti funzionalità di gestione della cache non funzionano in questo momento:

* **Console Redis**: poiché la console Redis viene eseguita nel browser locale, che in genere si trova in un computer di sviluppo che non è connesso alla rete virtuale, non può connettersi alla cache.

## <a name="use-expressroute-with-azure-cache-for-redis"></a>Usare ExpressRoute con Cache Redis di Azure

I clienti possono connettere un circuito [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) alla propria infrastruttura di rete virtuale. In questo modo, estendono la rete locale in Azure.

Per impostazione predefinita, un circuito ExpressRoute appena creato non esegue il tunneling forzato (annuncio di una route predefinita, 0.0.0.0/0) in una rete virtuale. Di conseguenza, la connettività Internet in uscita è consentita direttamente dalla rete virtuale. Le applicazioni client possono connettersi ad altri endpoint di Azure, che include una cache di Azure per l'istanza di Redis.

Una configurazione comune dei clienti prevede l'uso del tunneling forzato (annunciare una route predefinita), che forza il traffico Internet in uscita verso il flusso locale. Questo flusso di traffico interrompe la connettività con cache di Azure per Redis se il traffico in uscita viene quindi bloccato in locale in modo che la cache di Azure per l'istanza di redis non sia in grado di comunicare con le relative dipendenze.

La soluzione consiste nel definire una o più route definite dall'utente (UDR) nella subnet che contiene la cache di Azure per l'istanza di Redis. Una route UDR definisce le route specifiche della subnet che verranno accettate invece della route predefinita.

Se possibile, usare la configurazione seguente:

* La configurazione di ExpressRoute annuncia 0.0.0.0/0 e, per impostazione predefinita, impone il tunneling di tutto il traffico in uscita in locale.
* Il UDR applicato alla subnet che contiene la cache di Azure per l'istanza di redis definisce 0.0.0.0/0 con una route di lavoro per il traffico TCP/IP verso la rete Internet pubblica. Ad esempio, imposta il tipo di hop successivo su *Internet*.

L'effetto combinato di questi passaggi è che il UDR a livello di subnet ha la precedenza sul tunneling forzato di ExpressRoute, che garantisce l'accesso a Internet in uscita dalla cache di Azure per l'istanza di Redis.

La connessione a una cache di Azure per l'istanza di redis da un'applicazione locale tramite ExpressRoute non è uno scenario di utilizzo tipico per motivi di prestazioni. Per ottenere prestazioni ottimali, cache di Azure per i client Redis deve trovarsi nella stessa area della cache di Azure per l'istanza di Redis.

>[!IMPORTANT]
>Le route definite in un UDR *devono* essere sufficientemente specifiche per avere la precedenza su qualsiasi route annunciata dalla configurazione di ExpressRoute. Nell'esempio seguente viene usato l'intervallo di indirizzi ampio 0.0.0.0/0 e, di conseguenza, può essere accidentalmente sostituito dagli annunci di route che usano intervalli di indirizzi più specifici.

>[!WARNING]
>Cache di Azure per Redis non è supportato con le configurazioni di ExpressRoute che *annunciano erroneamente route dal percorso di peering pubblico al percorso di peering privato*. Le configurazioni di ExpressRoute per cui è configurato il peering pubblico ricevono gli annunci delle route da Microsoft per un elevato numero di intervalli di indirizzi IP di Microsoft Azure. Se questi intervalli di indirizzi vengono annunciati in modo non corretto nel percorso di peering privato, il risultato finale è che tutti i pacchetti di rete in uscita dalla subnet dell'istanza di Cache Redis di Azure verranno erroneamente sottoposti a tunneling forzato verso l'infrastruttura di rete locale del cliente. Questo flusso di rete interromperà Cache Redis di Azure. La soluzione a questo problema consiste nell'interrompere l'annuncio di più route dal percorso di peering pubblico al percorso di peering privato.

Informazioni di base su UdR sono disponibili in [routing del traffico di rete virtuale](../virtual-network/virtual-networks-udr-overview.md).

Per altre informazioni su ExpressRoute, vedere [Panoramica tecnica relativa a ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle funzionalità di cache di Azure per Redis.

* [Cache di Azure per i livelli di servizio di redis Premium](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
