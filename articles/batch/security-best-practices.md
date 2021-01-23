---
title: Procedure consigliate per la sicurezza e la conformità in batch
description: Informazioni sulle procedure consigliate e suggerimenti utili per migliorare la sicurezza con le soluzioni Azure Batch.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: 6ec4a1d89ebaa9318986fc0d51e832652ba51683
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98723813"
---
# <a name="batch-security-and-compliance-best-practices"></a>Procedure consigliate per la sicurezza e la conformità in batch

Questo articolo fornisce indicazioni e procedure consigliate per migliorare la sicurezza quando si usa Azure Batch.

Per impostazione predefinita, gli account Azure Batch hanno un endpoint pubblico e sono accessibili pubblicamente. Quando viene creato un pool di Azure Batch, viene effettuato il provisioning del pool in una subnet specificata di una rete virtuale di Azure. È possibile accedere alle macchine virtuali nel pool di batch tramite indirizzi IP pubblici creati da batch. I nodi di calcolo in un pool possono comunicare tra loro quando necessario, ad esempio per eseguire attività a istanze diverse, ma i nodi in un pool non possono comunicare con le macchine virtuali all'esterno del pool.

:::image type="content" source="media/security-best-practices/typical-environment.png" alt-text="Diagramma che mostra un ambiente batch tipico.":::

Sono disponibili molte funzionalità che consentono di creare una distribuzione Azure Batch più protetta. È possibile limitare l'accesso ai nodi e ridurre l'individuabilità dei nodi da Internet effettuando [il provisioning del pool senza indirizzi IP pubblici](batch-pool-no-public-ip-address.md). I nodi di calcolo possono comunicare in modo sicuro con altre macchine virtuali o con una rete locale effettuando [il provisioning del pool in una subnet di una rete virtuale di Azure](batch-virtual-network.md). È possibile abilitare [l'accesso privato dalle reti virtuali](private-connectivity.md) da un servizio basato sul collegamento privato di Azure.

:::image type="content" source="media/security-best-practices/secure-environment.png" alt-text="Diagramma che mostra un ambiente batch più sicuro.":::

## <a name="general-security-related-best-practices"></a>Procedure consigliate relative alla sicurezza generale

### <a name="pool-configuration"></a>Configurazione pool

Molte funzionalità di sicurezza sono disponibili solo per i pool configurati usando la [configurazione della macchina virtuale](nodes-and-pools.md#configurations)e non per i pool con configurazione dei servizi cloud. È consigliabile usare i pool di configurazione delle macchine virtuali, che usano i [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/overview.md), laddove possibile.

### <a name="batch-account-authentication"></a>Autenticazione dell'account batch

L'accesso all'account batch supporta due metodi di autenticazione: chiave condivisa e [Azure Active Directory (Azure ad)](batch-aad-auth.md).

Si consiglia di usare Azure AD per l'autenticazione dell'account batch. Alcune funzionalità di batch richiedono questo metodo di autenticazione, incluse molte delle funzionalità relative alla sicurezza illustrate qui.

### <a name="batch-account-pool-allocation-mode"></a>Modalità di allocazione pool di account batch

Quando si crea un account batch, è possibile scegliere tra due [modalità di allocazione pool](accounts.md#batch-accounts):

- **Servizio batch**: l'opzione predefinita, in cui le risorse del servizio cloud o del set di scalabilità di macchine virtuali sottostanti usate per allocare e gestire i nodi del pool vengono create nelle sottoscrizioni interne e non sono visibili direttamente nel portale di Azure. Sono visibili solo i pool di batch e i nodi. 
- **Sottoscrizione utente**: le risorse del servizio cloud o del set di scalabilità di macchine virtuali sottostanti vengono create nella stessa sottoscrizione dell'account batch. Queste risorse sono quindi visibili nella sottoscrizione, oltre alle risorse batch corrispondenti.

Con la modalità di sottoscrizione utente, le VM batch e altre risorse vengono create direttamente nella sottoscrizione quando viene creato un pool. La modalità di sottoscrizione utente è obbligatoria se si vuole creare pool di batch usando le istanze di VM riservate di Azure, usare criteri di Azure nelle risorse del set di scalabilità di macchine virtuali e/o gestire la quota di core nella sottoscrizione (condivisa tra tutti gli account batch nella sottoscrizione). Per creare un account Batch in modalità Sottoscrizione utente, è anche necessario registrare la sottoscrizione in Azure Batch e associare l'account ad Azure Key Vault.

## <a name="restrict-network-endpoint-access"></a>Limita accesso endpoint di rete

### <a name="batch-network-endpoints"></a>Endpoint di rete batch

Tenere presente che, per impostazione predefinita, gli endpoint con indirizzi IP pubblici vengono usati per comunicare con gli account batch, i pool di batch e i nodi del pool.

### <a name="batch-account-api"></a>API dell'account batch

 Quando viene creato un account batch, viene creato un endpoint pubblico usato per richiamare la maggior parte delle operazioni per l'account usando un' [API REST](/rest/api/batchservice/). L'endpoint dell'account ha un URL di base che usa il formato `https://{account-name}.{region-id}.batch.azure.com` . L'accesso all'account batch è protetto, con comunicazione con l'endpoint dell'account crittografato tramite HTTPS e ogni richiesta autenticata mediante l'autenticazione con chiave condivisa o Azure Active Directory (Azure AD).

### <a name="azure-resource-manager"></a>Azure Resource Manager

Oltre alle operazioni specifiche di un account batch, [le operazioni di gestione](/rest/api/batchmanagement/) si applicano a account batch singoli e multipli. È possibile accedere a queste operazioni di gestione tramite Azure Resource Manager.

Le operazioni di gestione batch tramite Azure Resource Manager vengono crittografate tramite HTTPS e ogni richiesta viene autenticata mediante l'autenticazione Azure AD.

### <a name="batch-pool-nodes"></a>Nodi del pool di batch

Il servizio batch comunica con un agente del nodo batch che viene eseguito in ogni nodo del pool. Il servizio, ad esempio, indica all'agente del nodo di eseguire un'attività, arrestare un'attività o ottenere i file per un'attività. La comunicazione con l'agente del nodo è abilitata da uno o più bilanciamenti del carico, il cui numero dipende dal numero di nodi in un pool. Il servizio di bilanciamento del carico inoltra la comunicazione al nodo desiderato, in modo che ogni nodo venga risolto da un numero di porta univoco. Per impostazione predefinita, i bilanciamenti del carico hanno indirizzi IP pubblici associati. È anche possibile accedere in remoto ai nodi del pool tramite RDP o SSH (questo accesso è abilitato per impostazione predefinita, con comunicazione tramite i bilanciamenti del carico).

### <a name="restricting-access-to-batch-endpoints"></a>Limitazione dell'accesso agli endpoint batch 

Sono disponibili diverse funzionalità per limitare l'accesso ai vari endpoint batch, soprattutto quando la soluzione USA una rete virtuale. 

#### <a name="use-private-endpoints"></a>Usare endpoint privati

Il [collegamento privato di Azure](../private-link/private-link-overview.md) consente l'accesso ai servizi PaaS di Azure e ai servizi di proprietà/partner del cliente ospitati in Azure tramite un endpoint privato nella rete virtuale. È possibile usare un collegamento privato per limitare l'accesso a un account batch dall'interno della rete virtuale o da qualsiasi rete virtuale con peering. Le risorse mappate al collegamento privato sono accessibili anche in locale tramite peering privato attraverso la VPN o Azure ExpressRoute.

Per usare gli endpoint privati, un account batch deve essere configurato in modo appropriato al momento della creazione; la configurazione di accesso alla rete pubblica deve essere disabilitata. Una volta creati, gli endpoint privati possono essere creati e associati all'account batch. Per altre informazioni, vedere [usare endpoint privati con account Azure batch](private-connectivity.md).

#### <a name="create-pools-in-virtual-networks"></a>Creare pool in reti virtuali

I nodi di calcolo in un pool di batch possono comunicare tra loro, ad esempio per eseguire attività a istanze diverse, senza la necessità di una rete virtuale (VNET). Tuttavia, per impostazione predefinita, i nodi in un pool non possono comunicare con le macchine virtuali esterne al pool in una rete virtuale e hanno indirizzi IP privati, ad esempio server licenze o file server.

Per consentire ai nodi di calcolo di comunicare in modo sicuro con altre macchine virtuali o con una rete locale, è possibile configurare un pool affinché si trovi in una subnet di una VNET di Azure.

Quando i pool hanno endpoint IP pubblici, la subnet deve consentire la comunicazione in ingresso dal servizio batch per poter pianificare le attività ed eseguire altre operazioni sui nodi di calcolo e le comunicazioni in uscita per comunicare con archiviazione di Azure o altre risorse in base alle esigenze del carico di lavoro. Per i pool nella configurazione della macchina virtuale, batch aggiunge gruppi di sicurezza di rete (gruppi) a livello di interfaccia di rete collegato ai nodi di calcolo. Questi gruppi sono dotati di regole per abilitare:

- Traffico TCP in ingresso dagli indirizzi IP del servizio batch
- Traffico TCP in ingresso per l'accesso remoto
- Traffico in uscita su qualsiasi porta per la rete virtuale (può essere modificato per ogni regola di NSG a livello di subnet)
- Traffico in uscita su qualsiasi porta verso Internet (può essere modificato per ogni regola di NSG a livello di subnet)

Non è necessario specificare gruppi a livello di subnet della rete virtuale, perché batch configura il proprio gruppi. Se si dispone di un NSG associato alla subnet in cui vengono distribuiti i nodi di calcolo batch o se si desidera applicare regole NSG personalizzate per sostituire le impostazioni predefinite applicate, è necessario configurare questo NSG con almeno le regole di sicurezza in ingresso e in uscita per consentire la comunicazione del servizio batch ai nodi del pool e alla comunicazione del nodo del pool con archiviazione di Azure.

Per altre informazioni, vedere [creare un pool di Azure batch in una rete virtuale](batch-virtual-network.md).

#### <a name="create-pools-with-static-public-ip-addresses"></a>Creare pool con indirizzi IP pubblici statici

Per impostazione predefinita, gli indirizzi IP pubblici associati ai pool sono dinamici; vengono creati quando viene creato un pool e gli indirizzi IP possono essere aggiunti o rimossi quando un pool viene ridimensionato. Quando le applicazioni dell'attività in esecuzione nei nodi del pool devono accedere a servizi esterni, potrebbe essere necessario limitare l'accesso a tali servizi a indirizzi IP specifici.  In questo caso, la presenza di indirizzi IP dinamici non sarà gestibile.

È possibile creare risorse di indirizzo IP pubblico statico nella stessa sottoscrizione dell'account batch prima della creazione del pool. È quindi possibile specificare questi indirizzi quando si crea il pool.

Per ulteriori informazioni, vedere [creare un pool di Azure batch con gli indirizzi IP pubblici specificati](create-pool-public-ip.md).

#### <a name="create-pools-without-public-ip-addresses"></a>Creare pool senza indirizzi IP pubblici

Per impostazione predefinita, a tutti i nodi di calcolo in un pool di configurazione Azure Batch macchina virtuale vengono assegnati uno o più indirizzi IP pubblici. Questi endpoint vengono usati dal servizio batch per pianificare le attività e per la comunicazione con i nodi di calcolo, incluso l'accesso in uscita a Internet.

Per limitare l'accesso a questi nodi e ridurre l'individuabilità di questi nodi da Internet, è possibile effettuare il provisioning del pool senza indirizzi IP pubblici.

Per altre informazioni, vedere [creare un pool senza indirizzi IP pubblici](batch-pool-no-public-ip-address.md).

#### <a name="limit-remote-access-to-pool-nodes"></a>Limitare l'accesso remoto ai nodi del pool

Per impostazione predefinita, batch consente a un utente del nodo con connettività di rete di connettersi esternamente a un nodo di calcolo in un pool di batch usando RDP o SSH.

Per limitare l'accesso remoto ai nodi, usare uno dei metodi seguenti:

- Configurare [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) per negare l'accesso. Il gruppo di sicurezza di rete (NSG) appropriato verrà associato al pool.
- Creare il pool [senza indirizzi IP pubblici](batch-pool-no-public-ip-address.md). Per impostazione predefinita, non è possibile accedere a questi pool all'esterno di VNet.
- Associare un NSG a VNet per negare l'accesso alle porte RDP o SSH.
- Non creare utenti nel nodo. Senza utenti del nodo, l'accesso remoto non sarà possibile.

## <a name="encrypt-data"></a>Crittografare i dati

### <a name="encrypt-data-in-transit"></a>Crittografa i dati in transito

Tutte le comunicazioni con l'endpoint dell'account batch (o tramite Azure Resource Manager) devono usare HTTPS. `https://`Per la connessione al servizio batch, è necessario utilizzare negli URL dell'account batch specificati nelle API.

I client che comunicano con il servizio batch devono essere configurati per l'uso di Transport Layer Security (TLS) 1,2.

### <a name="encrypt-batch-data-at-rest"></a>Crittografa dati di batch inattivi

Alcune delle informazioni specificate nelle API batch, ad esempio i certificati di account, i metadati di processi e attività e le righe di comando delle attività, vengono automaticamente crittografate quando vengono archiviate dal servizio batch. Per impostazione predefinita, questi dati vengono crittografati con Azure Batch chiavi gestite dalla piattaforma univoche per ogni account batch.

È anche possibile crittografare questi dati usando [chiavi gestite dal cliente](batch-customer-managed-key.md). [Azure Key Vault](../key-vault/general/overview.md) viene usato per generare e archiviare la chiave con l'identificatore di chiave registrato con l'account batch.

### <a name="encrypt-compute-node-disks"></a>Crittografare i dischi dei nodi di calcolo

Per impostazione predefinita, i nodi di calcolo batch hanno due dischi: un disco del sistema operativo e l'unità SSD temporanea locale. I [file e le directory](files-and-directories.md) gestiti da batch si trovano nell'unità SSD temporanea, ovvero il percorso predefinito per i file, ad esempio i file di output delle attività. Le applicazioni batch possono usare il percorso predefinito nell'unità SSD o nel disco del sistema operativo.

Per una maggiore sicurezza, crittografare questi dischi usando una di queste funzionalità di crittografia dischi di Azure:

- [Crittografia dei dischi gestiti inattivi con chiavi gestite dalla piattaforma](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Crittografia nell'host mediante una chiave gestita dalla piattaforma](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](disk-encryption.md)

## <a name="securely-access-services-from-compute-nodes"></a>Accedere in modo sicuro ai servizi dai nodi di calcolo

I nodi batch possono accedere in modo [sicuro alle credenziali e ai segreti](credential-access-key-vault.md) archiviati in [Azure Key Vault](../key-vault/general/overview.md), che possono essere usati dalle applicazioni delle attività per accedere ad altri servizi. Un certificato viene utilizzato per concedere ai nodi del pool l'accesso ai Key Vault.

## <a name="governance-and-compliance"></a>Governance e conformità

### <a name="compliance"></a>Conformità

Per consentire ai clienti di soddisfare i propri obblighi di conformità nei settori regolamentati e nei mercati in tutto il mondo, Azure gestisce un [ampio portfolio di offerte di conformità](https://azure.microsoft.com/overview/trusted-cloud/compliance). 

Queste offerte sono basate su diversi tipi di garanzie, incluse le certificazioni formali, le attestazioni, le convalide, le autorizzazioni e le valutazioni prodotte dalle aziende indipendenti di controllo di terze parti, nonché le modifiche contrattuali, le autovalutazioni e i documenti delle linee guida dei clienti prodotti da Microsoft. Esaminare la [panoramica completa delle offerte di conformità](https://aka.ms/AzureCompliance) per determinare quali possono essere rilevanti per le soluzioni batch.

### <a name="azure-policy"></a>Criteri di Azure

[Criteri di Azure](../governance/policy/overview.md) consente di applicare gli standard aziendali e di valutare la conformità su larga scala. I casi d'uso comuni per Criteri di Azure includono l'implementazione della governance per la coerenza delle risorse, la conformità alle normative, la sicurezza, i costi e la gestione.

A seconda della modalità di allocazione del pool e delle risorse a cui applicare un criterio, usare criteri di Azure con batch in uno dei modi seguenti:

- Direttamente, usando la risorsa Microsoft.Batch/batchAccounts. È possibile utilizzare un subset delle proprietà per un account batch. Ad esempio, i criteri possono includere aree account batch valide, modalità di allocazione pool consentita e se una rete pubblica è abilitata per gli account.
- Indirettamente, usando la risorsa Microsoft. Compute/virtualMachineScaleSets. Per gli account batch con modalità di allocazione pool di sottoscrizione utente è possibile impostare i criteri per le risorse del set di scalabilità di macchine virtuali create nella sottoscrizione dell'account batch. Ad esempio, le dimensioni delle macchine virtuali consentite e assicurarsi che alcune estensioni vengano eseguite in ogni nodo del pool.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare la [linea di base di sicurezza di Azure per batch](security-baseline.md).
- Leggere le [procedure consigliate per Azure batch](best-practices.md).