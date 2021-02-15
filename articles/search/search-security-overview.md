---
title: Panoramica della sicurezza
titleSuffix: Azure Cognitive Search
description: Informazioni sulle funzionalità di sicurezza di Azure ricerca cognitiva per proteggere gli endpoint, il contenuto e le operazioni.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/04/2021
ms.custom: references_regions
ms.openlocfilehash: 46f2035e5f8409cd38faeb9c327b88b06fc7d7a0
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097637"
---
# <a name="security-overview-for-azure-cognitive-search"></a>Panoramica della sicurezza per Azure ricerca cognitiva

Questo articolo descrive le funzionalità di sicurezza di Azure ricerca cognitiva che proteggono il contenuto e le operazioni.

Per le richieste in ingresso effettuate a un servizio di ricerca, si verifica una progressione di misure di sicurezza che proteggono l'endpoint del servizio di ricerca: dalle chiavi API della richiesta, alle regole in ingresso nel firewall, agli endpoint privati che proteggono completamente il servizio dalla rete Internet pubblica.

Per le richieste in uscita eseguite ad altri servizi, la richiesta predominante viene effettuata dagli indicizzatori che leggono il contenuto da origini esterne. È possibile specificare le credenziali nella stringa di connessione. In alternativa, è possibile configurare un'identità gestita per eseguire la ricerca in un servizio attendibile quando si accede ai dati da archiviazione di Azure, SQL di Azure, Cosmos DB o altre origini dati di Azure. Un'identità gestita sostituisce le credenziali o le chiavi di accesso per la connessione. Per altre informazioni su questa funzionalità, vedere [connettersi a un'origine dati usando un'identità gestita](search-howto-managed-identities-data-sources.md).

Le operazioni di scrittura in servizi esterni sono poche: un servizio di ricerca scrive nei file di log e scrive in archiviazione di Azure durante la creazione di archivi delle informazioni, salvando in modo permanente gli arricchimenti memorizzati nella cache e salvando in modo permanente le sessioni di debug. Altre chiamate da servizio a servizio, ad esempio servizi cognitivi, vengono effettuate sulla rete interna.

Guarda questo video veloce per una panoramica dell'architettura della sicurezza e di ogni categoria di funzionalità.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="network-security"></a>Sicurezza di rete

<a name="service-access-and-authentication"></a>

Le funzionalità di sicurezza in ingresso proteggono l'endpoint del servizio di ricerca aumentando i livelli di sicurezza e complessità. Per prima cosa, per tutte le richieste è necessaria una chiave API per l'accesso autenticato. In secondo luogo, è possibile impostare facoltativamente le regole del firewall che limitano l'accesso a indirizzi IP specifici. Per la protezione avanzata, una terza opzione consiste nell'abilitare il collegamento privato di Azure per schermare l'endpoint di servizio da tutto il traffico Internet.

### <a name="public-access-using-api-keys"></a>Accesso pubblico tramite chiavi API

Per impostazione predefinita, è possibile accedere a un servizio di ricerca tramite il cloud pubblico, usando l'autenticazione basata su chiavi per l'amministratore o l'accesso a query all'endpoint del servizio di ricerca. L'invio di una chiave valida è considerato come prova che la richiesta ha origine da un'entità attendibile. L'autenticazione basata su chiavi viene trattata nella sezione successiva.

### <a name="configure-ip-firewalls"></a>Configurare i firewall IP

Per controllare ulteriormente l'accesso al servizio di ricerca, è possibile creare regole del firewall in ingresso che consentano l'accesso a un indirizzo IP specifico o a un intervallo di indirizzi IP. Tutte le connessioni client devono essere effettuate tramite un indirizzo IP consentito o la connessione viene negata.

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="diagramma dell'architettura di esempio per l'accesso con restrizioni IP":::

Per [configurare l'accesso in ingresso](service-configure-firewall.md), è possibile usare il portale.

In alternativa, è possibile usare le API REST di gestione. A partire dall'API versione 2020-03-13, con il parametro [IpRule](/rest/api/searchmanagement/services/createorupdate#iprule) , è possibile limitare l'accesso al servizio identificando gli indirizzi IP, singolarmente o in un intervallo, a cui si vuole concedere l'accesso al servizio di ricerca.

### <a name="network-isolation-through-a-private-endpoint-no-internet-traffic"></a>Isolamento rete tramite un endpoint privato (nessun traffico Internet)

È possibile stabilire un [endpoint privato](../private-link/private-endpoint-overview.md) per Azure ricerca cognitiva consente a un client in una [rete virtuale](../virtual-network/virtual-networks-overview.md) di accedere in modo sicuro ai dati in un indice di ricerca tramite un [collegamento privato](../private-link/private-link-overview.md).

L'endpoint privato usa un indirizzo IP dello spazio di indirizzi della rete virtuale per le connessioni al servizio di ricerca. Il traffico di rete tra il client e il servizio di ricerca attraversa la rete virtuale e un collegamento privato sulla rete dorsale Microsoft, eliminando l'esposizione dalla rete Internet pubblica. Un VNET consente la comunicazione sicura tra le risorse, con la rete locale e Internet.

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="diagramma dell'architettura di esempio per l'accesso agli endpoint privati":::

Anche se questa soluzione è la più sicura, l'uso di servizi aggiuntivi è un costo aggiunto, quindi è necessario assicurarsi di avere una chiara comprensione dei vantaggi prima di immergersi in. Per ulteriori informazioni sui costi, vedere la [pagina](https://azure.microsoft.com/pricing/details/private-link/)relativa ai prezzi. Per ulteriori informazioni sull'interazione tra questi componenti, guardare il video nella parte superiore di questo articolo. Il code coverage dell'opzione di endpoint privato inizia da 5:48 nel video. Per istruzioni su come configurare l'endpoint, vedere [creare un endpoint privato per Azure ricerca cognitiva](service-create-private-endpoint.md).

## <a name="authentication"></a>Authentication

Per le richieste in ingresso al servizio di ricerca, l'autenticazione viene tramite una [chiave API obbligatoria](search-security-api-keys.md) (una stringa composta da numeri e lettere generati in modo casuale) che dimostra che la richiesta provenga da un'origine attendibile. Ricerca cognitiva attualmente non supporta l'autenticazione Azure Active Directory per le richieste in ingresso.

Le richieste in uscita effettuate da un indicizzatore sono soggette all'autenticazione da parte del servizio esterno. Il sottoservizio indicizzatore in ricerca cognitiva può essere reso un servizio attendibile in Azure, connettendosi ad altri servizi usando un'identità gestita. Per altre informazioni, vedere [configurare una connessione dell'indicizzatore a un'origine dati usando un'identità gestita](search-howto-managed-identities-data-sources.md).

## <a name="authorization"></a>Autorizzazione

Ricerca cognitiva fornisce modelli di autorizzazione diversi per la gestione dei contenuti e la gestione dei servizi. 

### <a name="authorization-for-content-management"></a>Autorizzazione per la gestione dei contenuti

L'autorizzazione al contenuto e alle operazioni correlate al contenuto è l'accesso in scrittura, come conferito attraverso la [chiave API](search-security-api-keys.md) fornita nella richiesta. La chiave API è un meccanismo di autenticazione, ma autorizza anche l'accesso a seconda del tipo di chiave API.

+ Chiave amministratore (consente l'accesso in lettura/scrittura per le operazioni di creazione-lettura-aggiornamento-eliminazione nel servizio di ricerca), creato quando viene eseguito il provisioning del servizio

+ Chiave di query (consente l'accesso in sola lettura alla raccolta documenti di un indice), creata in base alle esigenze e progettata per le applicazioni client che inviano query

Nel codice dell'applicazione specificare l'endpoint e una chiave API per consentire l'accesso al contenuto e alle opzioni. Un endpoint può essere il servizio, la raccolta di indici, un indice specifico, una raccolta di documenti o un documento specifico. Una volta concatenati, l'endpoint, l'operazione (ad esempio, una richiesta di creazione o aggiornamento) e il livello di autorizzazione (diritti completi o di sola lettura in base alla chiave) costituiscono la formula di sicurezza che protegge il contenuto e le operazioni.

### <a name="controlling-access-to-indexes"></a>Controllo dell'accesso agli indici

In ricerca cognitiva di Azure, un singolo indice non è un oggetto a protezione diretta. L'accesso a un indice viene invece determinato a livello di servizio (accesso in lettura o in scrittura in base alla chiave API fornita), insieme al contesto di un'operazione.

Per l'accesso in sola lettura, è possibile strutturare richieste di query per connettersi usando una [chiave di query](search-security-rbac.md)e includere l'indice specifico usato dall'app. In una richiesta di query non è previsto il join degli indici o l'accesso a più indici simultaneamente, quindi tutte le richiesta specificano come destinazione un solo indice per definizione. Di conseguenza, la costruzione della richiesta della query (una chiave più un singolo indice di destinazione) definisce di per sé il limite di sicurezza.

L'accesso amministratore e sviluppatore agli indici è indifferenziato: entrambi richiedono l'accesso in scrittura per creare, eliminare e aggiornare gli oggetti gestiti dal servizio. Chiunque disponga di una [chiave amministratore](search-security-rbac.md) per il servizio può leggere, modificare o eliminare qualsiasi indice nello stesso servizio. Per proteggersi dall'eliminazione accidentale o intenzionale degli indici, il controllo del codice sorgente interno per gli asset del codice è la risoluzione che consente di invertire l'eliminazione o la modifica indesiderata di un indice. Azure ricerca cognitiva dispone di failover all'interno del cluster per garantire la disponibilità, ma non archivia o esegue il codice proprietario usato per creare o caricare gli indici.

Le soluzioni multi-tenancy che richiedono limiti di sicurezza a livello di indice includono in genere un livello intermedio, che i clienti usano per gestire l'isolamento degli indici. Per altre informazioni sul caso d'uso multi-tenant, vedere [modelli di progettazione per applicazioni SaaS multi-tenant e ricerca cognitiva di Azure](search-modeling-multitenant-saas-applications.md).

### <a name="controlling-access-to-documents"></a>Controllo dell'accesso ai documenti

Se è necessario un controllo granulare per utente sui risultati della ricerca, è possibile creare filtri di sicurezza nelle query, restituendo i documenti associati a una determinata identità di sicurezza. 

Concettualmente equivalente a "sicurezza a livello di riga", l'autorizzazione al contenuto all'interno dell'indice non è supportata in modo nativo utilizzando ruoli predefiniti o assegnazioni di ruolo che vengono mappate alle entità in Azure Active Directory. Le autorizzazioni utente per i dati nei sistemi esterni, ad esempio Cosmos DB, non vengono trasferite con tali dati come indicizzate da ricerca cognitiva.

Le soluzioni alternative per le soluzioni che richiedono la "sicurezza a livello di riga" includono la creazione di un campo nell'origine dati che rappresenta un gruppo di sicurezza o un'identità utente e quindi l'uso di filtri in ricerca cognitiva per ritagliare selettivamente i risultati della ricerca di documenti e contenuti in base alle identità. La tabella seguente descrive due approcci per limitare i risultati della ricerca di contenuto non autorizzato.

| Approccio | Descrizione |
|----------|-------------|
|[Limitazione per motivi di sicurezza in base ai filtri delle identità](search-security-trimming-for-azure-search.md)  | Documenta il flusso di lavoro di base per implementare il controllo di accesso dell'identità utente. Illustra l'aggiunta di ID di sicurezza a un indice e quindi illustra l'applicazione di filtri a tale campo per limitare i risultati di contenuto non consentito. |
|[Limitazione per motivi di sicurezza in base alle identità di Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | In questo articolo viene ampliato l'articolo precedente, che fornisce i passaggi per il recupero delle identità da Azure Active Directory (Azure AD), uno dei [servizi gratuiti](https://azure.microsoft.com/free/) nella piattaforma cloud di Azure. |

### <a name="authorization-for-service-management"></a>Autorizzazione per la gestione dei servizi

Le operazioni di gestione dei servizi sono autorizzate tramite il [controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md). Il controllo degli accessi in base al ruolo di Azure è un sistema di autorizzazione basato su [Azure Resource Manager](../azure-resource-manager/management/overview.md) per il provisioning delle risorse 

In ricerca cognitiva di Azure, Gestione risorse viene usato per creare o eliminare il servizio, gestire le chiavi API e ridimensionare il servizio. Di conseguenza, le assegnazioni di ruolo di Azure determineranno chi può eseguire tali attività, indipendentemente dal fatto che stiano usando il [portale](search-manage.md), [PowerShell](search-manage-powershell.md)o le [API REST di gestione](/rest/api/searchmanagement/search-howto-management-rest-api).

Per l'amministrazione del servizio di ricerca sono definiti [tre ruoli di base](search-security-rbac.md#management-tasks-by-role) . Le assegnazioni di ruolo possono essere effettuate usando qualsiasi metodologia supportata (portale, PowerShell e così via) e vengono rispettate a livello di servizio. I ruoli proprietario e collaboratore possono eseguire un'ampia gamma di funzioni di amministrazione. È possibile assegnare il ruolo lettore agli utenti che visualizzano solo le informazioni essenziali.

> [!Note]
> Usando i meccanismi a livello di Azure, è possibile bloccare una sottoscrizione o una risorsa per evitare l'eliminazione accidentale o non autorizzata del servizio di ricerca da parte degli utenti con diritti di amministratore. Per altre informazioni, vedere [bloccare le risorse per impedire l'eliminazione imprevista](../azure-resource-manager/management/lock-resources.md).

<a name="encryption"></a>

## <a name="data-protection"></a>Protezione dei dati

A livello di archiviazione, la crittografia dei dati è incorporata per tutti i contenuti gestiti dal servizio salvati su disco, inclusi indici, mappe sinonime e definizioni di indicizzatori, origini dati e skillsets. Facoltativamente, è possibile aggiungere chiavi gestite dal cliente (CMK) per la crittografia supplementare del contenuto indicizzato. Per i servizi creati dopo l'agosto 1 2020, la crittografia CMK si estende ai dati nei dischi temporanei per una "crittografia doppia" completa del contenuto indicizzato.

### <a name="data-in-transit"></a>Dati in transito

In Azure ricerca cognitiva la crittografia inizia con connessioni e trasmissioni e si estende ai contenuti archiviati su disco. Per i servizi di ricerca sulla rete Internet pubblica, Azure ricerca cognitiva è in ascolto sulla porta HTTPS 443. Tutte le connessioni da client a servizio utilizzano la crittografia TLS 1,2. Le versioni precedenti (1,0 o 1,1) non sono supportate.

### <a name="encrypted-data-at-rest"></a>Dati inattivi crittografati

Per i dati gestiti internamente dal servizio di ricerca, nella tabella seguente vengono descritti i [modelli di crittografia dei dati](../security/fundamentals/encryption-models.md). Alcune funzionalità, ad esempio archivio informazioni, arricchimento incrementale e indicizzazione basata su indicizzatore, lettura o scrittura in strutture di dati di altri servizi di Azure. Questi servizi hanno i propri livelli di supporto della crittografia separati da Azure ricerca cognitiva.

| Modello | Chiavi&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Requisiti&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Restrizioni | Si applica a |
|------------------|-------|-------------|--------------|------------|
| crittografia lato server | chiavi gestite da Microsoft | Nessuno (predefinito) | Nessuno, disponibile in tutti i livelli, in tutte le aree, per il contenuto creato dopo il 24 2018 gennaio. | Contenuto (mappe indici e sinonimi) e definizioni (indicizzatori, origini dati, skillsets) |
| crittografia lato server | chiavi gestite dal cliente | Insieme di credenziali chiave di Azure | Disponibile nei livelli fatturabili, in tutte le aree, per il contenuto creato dopo il 2019 gennaio. | Contenuto (mappe indici e sinonimi) sui dischi dati |
| crittografia doppia lato server | chiavi gestite dal cliente | Insieme di credenziali chiave di Azure | Disponibile su livelli fatturabili, in aree selezionate, nei servizi di ricerca dopo il 1 2020 agosto. | Contenuto (mappe indici e sinonimi) su dischi dati e dischi temporanei |

### <a name="service-managed-keys"></a>Chiavi gestite dal servizio

La crittografia gestita dal servizio è un'operazione interna di Microsoft, basata su [crittografia del servizio di archiviazione di Azure](../storage/common/storage-service-encryption.md), che usa la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)a 256 bit. Si verifica automaticamente in tutte le indicizzazione, inclusi gli aggiornamenti incrementali degli indici che non sono completamente crittografati (creati prima del gennaio 2018).

### <a name="customer-managed-keys-cmk"></a>Chiavi gestite dal cliente (CMK)

Le chiavi gestite dal cliente richiedono un servizio fatturabile aggiuntivo, Azure Key Vault, che può trovarsi in un'area diversa, ma nella stessa sottoscrizione di Azure ricerca cognitiva. L'abilitazione della crittografia CMK aumenterà le dimensioni degli indici e diminuirà le prestazioni di query. In base alle osservazioni date, è possibile prevedere un aumento del 30%-60% nei tempi di esecuzione delle query, anche se le prestazioni effettive variano a seconda della definizione dell'indice e dei tipi di query. A causa di questo effetto sulle prestazioni, si consiglia di abilitare questa funzionalità solo negli indici che lo richiedono effettivamente. Per altre informazioni, vedere [configurare le chiavi di crittografia gestite dal cliente in Azure ricerca cognitiva](search-security-manage-encryption-keys.md).

<a name="double-encryption"></a>

### <a name="double-encryption"></a>Crittografia doppia

In Azure ricerca cognitiva la crittografia doppia è un'estensione di CMK. Si tratta di una crittografia a due riduzioni (una volta per CMK e di nuovo da chiavi gestite dal servizio) e di un ambito completo, che include l'archiviazione a lungo termine scritta in un disco dati e l'archiviazione a breve termine scritta nei dischi temporanei. La differenza tra CMK prima del 1 2020 agosto e dopo e che rende CMK una doppia funzionalità di crittografia in Azure ricerca cognitiva è la crittografia aggiuntiva dei dati inattivi sui dischi temporanei.

La crittografia doppia è attualmente disponibile nei nuovi servizi creati in queste aree dopo il 1 ° agosto:

+ Stati Uniti occidentali 2
+ Stati Uniti orientali
+ Stati Uniti centro-meridionali
+ US Gov Virginia
+ US Gov Arizona

## <a name="security-management"></a>Gestione della sicurezza

### <a name="api-keys"></a>Chiavi API

Affidarsi all'autenticazione basata su chiave API significa che è necessario avere un piano per rigenerare la chiave amministratore a intervalli regolari, per le procedure consigliate per la sicurezza di Azure. Sono disponibili al massimo due chiavi amministrative per servizio di ricerca. Per altre informazioni sulla protezione e la gestione delle chiavi API, vedere [creare e gestire](search-security-api-keys.md)le chiavi API.

#### <a name="activity-and-diagnostic-logs"></a>Log attività e di diagnostica

Ricerca cognitiva non registra le identità utente, pertanto non è possibile fare riferimento ai log per ottenere informazioni su un utente specifico. Il servizio esegue tuttavia il log delle operazioni di creazione, lettura, aggiornamento ed eliminazione, che potrebbero essere in grado di correlare con altri log per comprendere l'agenzia di azioni specifiche.

Usando gli avvisi e l'infrastruttura di registrazione in Azure, è possibile prelevare i picchi del volume di query o altre azioni che deviano dai carichi di lavoro previsti. Per altre informazioni sulla configurazione dei log, vedere [raccogliere e analizzare i dati di log](search-monitor-logs.md) e [monitorare le richieste di query](search-monitor-queries.md).

### <a name="certifications-and-compliance"></a>Certificazioni e conformità

Azure ricerca cognitiva partecipa a controlli regolari ed è stato certificato in base a una serie di standard globali, regionali e specifici del settore sia per il cloud pubblico che per Azure per enti pubblici. Per l'elenco completo, scaricare il [white paper sulle **offerte di conformità Microsoft Azure**](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) dalla pagina dei report di controllo ufficiale.

Per garantire la conformità, è possibile usare i [criteri di Azure](../governance/policy/overview.md) per implementare le procedure consigliate per la sicurezza elevata del benchmark di [sicurezza di Azure](../security/benchmarks/introduction.md). Il benchmark di sicurezza di Azure è una raccolta di consigli sulla sicurezza, codificati in controlli di sicurezza che si riferiscono a azioni chiave che è necessario intraprendere per attenuare le minacce ai servizi e ai dati. Attualmente sono presenti 11 controlli di sicurezza, tra cui la [sicurezza della rete](../security/benchmarks/security-control-network-security.md), la [registrazione e il monitoraggio](../security/benchmarks/security-control-logging-monitoring.md)e la [protezione dei dati](../security/benchmarks/security-control-data-protection.md) , per citarne alcuni.

Criteri di Azure è una funzionalità incorporata in Azure che consente di gestire la conformità per più standard, inclusi quelli del benchmark di sicurezza di Azure. Per i benchmark noti, i criteri di Azure forniscono definizioni predefinite che forniscono entrambi i criteri, nonché una risposta praticabile che risolve la mancata conformità.

Per ricerca cognitiva di Azure, esiste attualmente una definizione predefinita. Per la registrazione diagnostica. Con questa funzionalità incorporata, è possibile assegnare un criterio che identifichi qualsiasi servizio di ricerca privo di registrazione diagnostica e quindi lo attiva. Per altre informazioni, vedere [controlli di conformità normativi di criteri di Azure per ricerca cognitiva di Azure](security-controls-policy.md).

## <a name="see-also"></a>Vedi anche

+ [Concetti fondamentali della sicurezza di Azure](../security/fundamentals/index.yml)
+ [Sicurezza di Azure](https://azure.microsoft.com/overview/security)
+ [Centro sicurezza di Azure](../security-center/index.yml)