---
title: Panoramica della sicurezza di Azure Key Vault
description: Panoramica delle funzionalità di sicurezza e delle procedure consigliate per Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: fe88933049ad39de57f879789e8c1b86ed7a54f5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819452"
---
# <a name="azure-key-vault-security"></a>Sicurezza di Azure Key Vault

Azure Key Vault protegge chiavi di crittografia e segreti (ad esempio certificati, stringhe di connessione e password) nel cloud. Quando si archiviano dati sensibili e business critical, tuttavia, è necessario adottare misure per ottimizzare la sicurezza degli insiemi di credenziali e dei dati archiviati in essi.

Questo articolo offre una panoramica delle funzionalità di sicurezza e delle procedure consigliate per Azure Key Vault.

> [!NOTE]
> Per un elenco completo delle raccomandazioni Azure Key Vault sicurezza, vedere [Baseline di sicurezza per](security-baseline.md)Azure Key Vault .

## <a name="network-security"></a>Sicurezza di rete

È possibile ridurre l'esposizione degli insiemi di credenziali specificando gli indirizzi IP che possono accedervi. Gli endpoint servizio di rete virtuale per Azure Key Vault consentono di limitare l'accesso a una rete virtuale specifica. Gli endpoint consentono anche di limitare l'accesso a un elenco di intervalli di indirizzi IPv4 (protocollo internet versione 4). L'accesso viene negato a tutti gli utenti che si connettono all'insieme di credenziali delle chiavi dall'esterno di tali origini.  Per informazioni dettagliate, vedere [Endpoint servizio di rete virtuale per Azure Key Vault](overview-vnet-service-endpoints.md)

Quando le regole del firewall vengono applicate, gli utenti possono leggere i dati da Key Vault solo quando le loro richieste hanno origine da reti virtuali o intervalli di indirizzi IPv4 consentiti. Questo vale anche per l'accesso a Key Vault dal portale di Azure. Benché gli utenti possano accedere a un insieme di credenziali delle chiavi dal portale di Azure, potrebbero non essere in grado di elencare chiavi, segreti o certificati se il computer client in uso non è presente nell'elenco dei computer consentiti. Per i passaggi di [implementazione, vedere Configurare Azure Key Vault firewall e reti virtuali](network-security.md)

Il servizio Collegamento privato di Azure consente di accedere ad Azure Key Vault e ai servizi di clienti/partner ospitati in Azure tramite un endpoint privato nella rete virtuale. Un endpoint privato di Azure è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su Collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È possibile connettersi a un'istanza di una risorsa di Azure, garantendo il massimo livello di granularità nel controllo di accesso.  Per i passaggi di [implementazione, vedere Integrare Key Vault con collegamento privato di Azure](private-link-service.md)

## <a name="tls-and-https"></a>TLS e HTTPS

- Il Key Vault front-end (piano dati) è un server multi-tenant. Ciò significa che gli insiemi di credenziali delle chiavi di clienti diversi possono condividere lo stesso indirizzo IP pubblico. Per ottenere l'isolamento, ogni richiesta HTTP viene autenticata e autorizzata indipendentemente dalle altre richieste.
- È possibile identificare le versioni precedenti di TLS per segnalare le vulnerabilità, ma poiché l'indirizzo IP pubblico è condiviso, il team del servizio dell'insieme di credenziali delle chiavi non può disabilitare le versioni precedenti di TLS per singoli insiemi di credenziali delle chiavi a livello di trasporto.
- Il protocollo HTTPS consente al client di partecipare alla negoziazione TLS. **I client possono applicare la versione più recente di TLS** e, ogni volta che un client esegue questa operazione, l'intera connessione userà la protezione di livello corrispondente. Il fatto che Key Vault supporta ancora le versioni precedenti di TLS non compromerà la sicurezza delle connessioni che usano le versioni più recenti di TLS.
- Nonostante le vulnerabilità note nel protocollo TLS, non esiste alcun attacco noto che consenta a un agente dannoso di estrarre informazioni dall'insieme di credenziali delle chiavi quando l'utente malintenzionato avvia una connessione con una versione di TLS con vulnerabilità. L'utente malintenzionato dovrà comunque autenticarsi e autorizzare se stesso e, purché i client legittimi si connettono sempre con le versioni recenti di TLS, non è possibile che le credenziali siano state trapelate dalle vulnerabilità nelle versioni precedenti di TLS.

## <a name="identity-management"></a>Gestione delle identità

Quando si crea un insieme di credenziali delle chiavi in una sottoscrizione di Azure, questo viene automaticamente associato al tenant di Azure AD della sottoscrizione. Chiunque cerchi di gestire o recuperare il contenuto da un insieme di credenziali deve essere autenticato da Azure AD. In entrambi i casi, le applicazioni possono accedere Key Vault in tre modi:

- **Solo applicazione: l'applicazione** rappresenta un'entità servizio o un'identità gestita. Questa identità è lo scenario più comune per le applicazioni che devono accedere periodicamente a certificati, chiavi o segreti dall'insieme di credenziali delle chiavi. Per il funzionamento di questo scenario, l'oggetto dell'applicazione deve essere specificato nei criteri di accesso e non deve essere `objectId` specificato o deve essere `applicationId`  `null` .
- **Solo utente:** l'utente accede all'insieme di credenziali delle chiavi da qualsiasi applicazione registrata nel tenant. Azure PowerShell e il portale di Azure sono esempi di questo tipo di accesso. Per il funzionamento di questo scenario, l'oggetto dell'utente deve essere specificato nei criteri di accesso e non deve essere `objectId` specificato o deve essere `applicationId`  `null` .
- **Application-plus-user** (a volte definito identità _composta):_ l'utente deve accedere  all'insieme di credenziali delle chiavi da un'applicazione specifica e l'applicazione deve usare il flusso di autenticazione on-behalf-of (OBO) per rappresentare l'utente. Per il funzionamento di questo scenario, è necessario specificare sia `applicationId` che nei criteri di `objectId` accesso. identifica `applicationId` l'applicazione richiesta e identifica `objectId` l'utente. Attualmente, questa opzione non è disponibile per il controllo degli accessi in base al ruolo di Azure del piano dati.

In tutti i tipi di accesso, l'applicazione esegue l'autenticazione con Azure AD. L'applicazione usa qualsiasi [metodo di autenticazione supportato](../../active-directory/develop/authentication-vs-authorization.md) in base al tipo di applicazione. L'applicazione acquisisce un token per una risorsa del piano per la concessione dell'accesso. La risorsa è un endpoint nel piano dati o di gestione, in base all'ambiente di Azure. L'applicazione usa il token e invia una richiesta API REST all'insieme di credenziali delle chiavi. Per altre informazioni, vedere l'[intero flusso di autenticazione](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Per informazioni dettagliate complete, vedere [Key Vault Di base sull'autenticazione](authentication-fundamentals.md)

## <a name="key-vault-authentication-options"></a>Key Vault di autenticazione

Quando si crea un insieme di credenziali delle chiavi in una sottoscrizione di Azure, questo viene automaticamente associato al tenant di Azure AD della sottoscrizione. Tutti i chiamanti in entrambi i piani devono essere registrati in questo tenant ed eseguire l'autenticazione per accedere all'insieme di credenziali delle chiavi. In entrambi i casi, le applicazioni possono accedere Key Vault in tre modi:

- **Solo applicazione: l'applicazione** rappresenta un'entità servizio o un'identità gestita. Questa identità è lo scenario più comune per le applicazioni che devono accedere periodicamente a certificati, chiavi o segreti dall'insieme di credenziali delle chiavi. Per il funzionamento di questo scenario, l'oggetto dell'applicazione deve essere specificato nei criteri di accesso e non deve essere `objectId` specificato o deve essere `applicationId`  `null` .
- **Solo utente: l'utente** accede all'insieme di credenziali delle chiavi da qualsiasi applicazione registrata nel tenant. Azure PowerShell e il portale di Azure sono esempi di questo tipo di accesso. Per il funzionamento di questo scenario, l'oggetto dell'utente deve essere specificato nei criteri di accesso e non deve essere `objectId` specificato o deve essere `applicationId`  `null` .
- **Application-plus-user** (a volte definito identità _composta):_ l'utente deve accedere  all'insieme di credenziali delle chiavi da un'applicazione specifica e l'applicazione deve usare il flusso di autenticazione on-behalf-of (OBO) per rappresentare l'utente. Per il funzionamento di questo scenario, `applicationId` è necessario specificare sia che nei criteri di `objectId` accesso. identifica `applicationId` l'applicazione richiesta e identifica `objectId` l'utente. Questa opzione non è attualmente disponibile per il piano dati Controllo degli accessi in base al ruolo di Azure (anteprima).

In tutti i tipi di accesso, l'applicazione esegue l'autenticazione con Azure AD. L'applicazione usa qualsiasi [metodo di autenticazione supportato](../../active-directory/develop/authentication-vs-authorization.md) in base al tipo di applicazione. L'applicazione acquisisce un token per una risorsa del piano per la concessione dell'accesso. La risorsa è un endpoint nel piano dati o di gestione, in base all'ambiente di Azure. L'applicazione usa il token e invia una richiesta API REST all'insieme di credenziali delle chiavi. Per altre informazioni, vedere l'[intero flusso di autenticazione](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Il modello con un singolo meccanismo di autenticazione per entrambi i piani presenta alcuni vantaggi:

- Le organizzazioni possono controllare l'accesso a tutti gli insiemi di credenziali delle chiavi in modo centralizzato.
- Se un utente lascia l'organizzazione, perde immediatamente l'accesso a tutti gli insiemi di credenziali delle chiavi all'interno dell'organizzazione.
- Le organizzazioni possono personalizzare l'autenticazione tramite le opzioni disponibili in Azure AD, ad esempio abilitando l'autenticazione a più fattori per una maggiore sicurezza.

## <a name="access-model-overview"></a>Panoramica del modello di accesso

L'accesso a un insieme di credenziali delle chiavi è controllato tramite due interfacce: il **piano di gestione** e il **piano dati**. Il piano di gestione consente di gestire l'insieme di credenziali delle chiavi stesso. Le operazioni in questo piano includono la creazione e l'eliminazione di insiemi di credenziali delle chiavi, il recupero delle proprietà dell'insieme di credenziali delle chiavi e l'aggiornamento dei criteri di accesso. Il piano dati consente di lavorare con i dati archiviati in un insieme di credenziali delle chiavi. È possibile aggiungere, eliminare e modificare chiavi, segreti e certificati.

Entrambi i piani usano Azure Active Directory [(Azure AD) per](../../active-directory/fundamentals/active-directory-whatis.md) l'autenticazione. Per l'autorizzazione, il piano di gestione usa il controllo degli accessi in base al ruolo di [Azure](../../role-based-access-control/overview.md) e il piano dati usa un criterio di accesso [Key Vault](./assign-access-policy-portal.md) e il controllo degli accessi in base al ruolo di [Azure](./rbac-guide.md)per le operazioni del piano dati Key Vault .

Per accedere a un insieme di credenziali delle chiavi in uno dei piani, tutti i chiamanti (utenti o applicazioni) devono disporre di autenticazione e autorizzazione appropriate. L'autenticazione stabilisce l'identità del chiamante. L'autorizzazione determina le operazioni che il chiamante può eseguire. L'autenticazione con Key Vault funziona in combinazione con [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md), che è responsabile dell'autenticazione dell'identità di una determinata **entità di sicurezza**.

Un'entità di sicurezza è un oggetto che rappresenta un utente, un gruppo, un servizio o un'applicazione che richiede l'accesso alle risorse di Azure. Azure assegna un **ID oggetto** univoco a ogni entità di sicurezza.

- L'entità di sicurezza di un **utente** identifica un soggetto che ha un profilo in Azure Active Directory.
- L'entità di sicurezza di un **gruppo** identifica un set di utenti creati in Azure Active Directory. Tutti i ruoli o le autorizzazioni assegnati al gruppo vengono concessi a tutti gli utenti all'interno del gruppo.
- **Un'entità servizio** è un tipo di entità di sicurezza che identifica un'applicazione o un servizio, ovvero un frammento di codice anziché un utente o un gruppo. L'ID oggetto di un'entità servizio è noto come **ID client** e funge da nome utente. Il certificato o il **segreto client dell'entità** **servizio** funge da password. Molti servizi di Azure supportano l'assegnazione [dell'identità gestita](../../active-directory/managed-identities-azure-resources/overview.md) con la gestione automatica **dell'ID client e** del **certificato.** L'identità gestita è l'opzione più sicura e consigliata per l'autenticazione all'interno di Azure.

Per altre informazioni sull'autenticazione Key Vault, vedere [Eseguire l'autenticazione Azure Key Vault](authentication.md)

## <a name="privileged-access"></a>Accesso con privilegi

L'autorizzazione determina le operazioni che il chiamante può eseguire. L'autorizzazione Key Vault usa una combinazione di controllo degli accessi in base al ruolo di [Azure](../../role-based-access-control/overview.md) e Azure Key Vault di accesso.

L'accesso agli insiemi di credenziali avviene tramite due interfacce o piani. Questi piani sono il piano di gestione e il piano dati.

- Il *piano di gestione* consente di gestire Key Vault ed è l'interfaccia usata per creare ed eliminare gli insiemi di credenziali. È anche possibile leggere le proprietà degli insiemi di credenziali delle chiavi e gestire i criteri di accesso.
- Il *piano dati* consente di lavorare con i dati archiviati in un insieme di credenziali delle chiavi. È possibile aggiungere, eliminare e modificare chiavi, segreti e certificati.

Le applicazioni accedono ai piani tramite endpoint. I controlli di accesso per i due piani funzionano in maniera indipendente. Per concedere a un'applicazione l'accesso per l'uso delle chiavi in un insieme di credenziali delle chiavi, concedere l'accesso al piano dati usando un criterio di accesso Key Vault o il controllo degli accessi in base al ruolo di Azure. Per concedere a un utente l'accesso in lettura Key Vault proprietà e tag, ma non ai dati (chiavi, segreti o certificati), si concede l'accesso al piano di gestione con il controllo degli accessi in base al ruolo di Azure.

La tabella seguente illustra gli endpoint per il piano dati e di gestione.

| Piano di&nbsp;accesso | Endpoint di accesso | Operazioni | Meccanismo di controllo&nbsp;di accesso |
| --- | --- | --- | --- |
| Piano di gestione | **Globale:**<br> management.azure.com:443<br><br> **21Vianet per Azure Cina:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Gov:**<br> management.usgovcloudapi.net:443<br><br> **Azure per la Germania:**<br> management.microsoftazure.de:443 | Creare, leggere, aggiornare ed eliminare insiemi di credenziali delle chiavi<br><br>Impostare i criteri di accesso dell'insieme di credenziali delle chiavi<br><br>Impostare tag per l'insieme di credenziali delle chiavi | Controllo degli accessi in base al ruolo di Azure |
| Piano dati | **Globale:**<br> &lt;nome-insiemecredenziali&gt;.vault.azure.net:443<br><br> **21Vianet per Azure Cina:**<br> &lt;nome-insiemecredenziali&gt;.vault.azure.cn:443<br><br> **Azure US Gov:**<br> &lt;nome-insiemecredenziali&gt;.vault.usgovcloudapi.net:443<br><br> **Azure per la Germania:**<br> &lt;nome-insiemecredenziali&gt;.vault.microsoftazure.de:443 | Chiavi: crittografare, decrittografare, wrapKey, unwrapKey, firmare, verificare, ottenere, elencare, creare, aggiornare, importare, eliminare, ripristinare, backup, ripristino, ripulitura<br><br> Certificati: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, get, list, create, import, update, delete, recover, backup, restore, purge<br><br>  Segreti: get, list, set, delete, recover, backup, restore, purge | Key Vault criteri di accesso remoto o controllo degli accessi in base al ruolo di Azure |

### <a name="managing-administrative-access-to-key-vault"></a>Gestione dell'accesso amministrativo a Key Vault

Quando si crea un insieme di credenziali delle chiavi in un gruppo di risorse, si gestisce l'accesso usando Azure AD. È possibile consentire a utenti o gruppi di gestire gli insiemi di credenziali delle chiavi in un gruppo di risorse. È possibile concedere l'accesso a un livello di ambito specifico assegnando i ruoli di Azure appropriati. Per concedere l'accesso a un utente in modo che possa gestire insiemi di credenziali delle chiavi, assegnare all'utente un ruolo `key vault Contributor` predefinito in un ambito specifico. I livelli di ambito seguenti possono essere assegnati a un ruolo di Azure:

- **Sottoscrizione:** un ruolo di Azure assegnato a livello di sottoscrizione si applica a tutti i gruppi di risorse e le risorse all'interno della sottoscrizione.
- **Gruppo di risorse:** un ruolo di Azure assegnato a livello di gruppo di risorse si applica a tutte le risorse in tale gruppo di risorse.
- **Risorsa specifica:** un ruolo di Azure assegnato per una risorsa specifica si applica a tale risorsa. In questo caso, la risorsa è un insieme di credenziali delle chiavi specifico.

Ci sono diversi ruoli predefiniti. Se un ruolo predefinito non soddisfa le specifiche esigenze, è possibile definire un ruolo personalizzato. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo di Azure: ruoli predefiniti.](../../role-based-access-control/built-in-roles.md)

> [!IMPORTANT]
> Se un utente ha le autorizzazioni `Contributor` per un piano di gestione di un insieme di credenziali delle chiavi, l'utente può concedere a se stesso l'accesso al piano dati impostando criteri di accesso dell'insieme di credenziali delle chiavi. È necessario controllare attentamente chi ha accesso al ruolo `Contributor` per gli insiemi di credenziali delle chiavi. Assicurarsi che solo gli utenti autorizzati possano accedere e gestire gli insiemi di credenziali delle chiavi, le chiavi, i segreti e i certificati.

### <a name="controlling-access-to-key-vault-data"></a>Controllo dell'accesso ai dati di Key Vault

I criteri di accesso di Key Vault concedono autorizzazioni separate per chiavi, segreti o certificati. È possibile concedere a un utente l'accesso solo alle chiavi e non ai segreti. Le autorizzazioni di accesso per chiavi, segreti e certificati vengono gestite a livello di insieme di credenziali.

> [!IMPORTANT]
> I criteri di accesso dell'insieme di credenziali delle chiavi non supportano le autorizzazioni granulari a livello di oggetto, ad esempio una chiave, un segreto o un certificato specifico. Quando a un utente viene concessa l'autorizzazione per creare ed eliminare chiavi, può eseguire tali operazioni su tutte le chiavi dell'insieme di credenziali.

È possibile impostare i criteri di accesso per un insieme di credenziali delle chiavi portale di Azure [,](assign-access-policy-portal.md)l'interfaccia della riga di comando di [Azure,](assign-access-policy-cli.md) [Azure PowerShell](assign-access-policy-powershell.md)o le API REST di [Key Vault Management](/rest/api/keyvault/).

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

La funzionalità di registrazione di Key Vault salva le informazioni sulle attività eseguite nell'insieme di credenziali. Per informazioni dettagliate complete, vedere [Key Vault registrazione .](logging.md)

È possibile integrare Key Vault con Griglia di eventi per ricevere una notifica quando lo stato di una chiave, un certificato o un segreto archiviato nell'insieme di credenziali delle chiavi è cambiato. Per informazioni dettagliate, vedere [Monitoraggio Key Vault con Griglia di eventi di Azure](event-grid-overview.md)

È anche importante monitorare l'integrità dell'insieme di credenziali delle chiavi per assicurarsi che il servizio funzioni come previsto. Per informazioni su come eseguire questa operazione, vedere [Monitoraggio e avvisi per Azure Key Vault](alert.md).

## <a name="backup-and-recovery"></a>Backup e ripristino

Azure Key Vault protezione dall'eliminazione e dall'eliminazione consente di ripristinare gli insiemi di credenziali e gli oggetti dell'insieme di credenziali eliminati. Per informazioni dettagliate, vedere [Azure Key Vault panoramica dell'eliminazione soft.](soft-delete-overview.md)

È anche consigliabile eseguire backup regolari dell'insieme di credenziali durante l'aggiornamento, l'eliminazione o la creazione di oggetti all'interno di un insieme di credenziali.  

## <a name="next-steps"></a>Passaggi successivi

- [Azure Key Vault baseline di sicurezza](security-baseline.md)
- [Procedure consigliate per Azure Key Vault](security-baseline.md)
- [Endpoint servizio di rete virtuale per Azure Key Vault](overview-vnet-service-endpoints.md)
- [Controllo degli accessi in base al ruolo Azure: ruoli predefiniti](../../role-based-access-control/built-in-roles.md)