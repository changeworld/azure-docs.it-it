---
title: Panoramica della sicurezza di Azure Key Vault
description: Panoramica delle funzionalità di sicurezza e delle procedure consigliate per Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: mbaldwin
ms.openlocfilehash: c4e9f0cd6a7421f5be29200816bb00a56b141367
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937768"
---
# <a name="azure-key-vault-security-overview"></a>Panoramica della sicurezza di Azure Key Vault

Si usa Azure Key Vault per proteggere le chiavi di crittografia e i segreti come certificati, stringhe di connessione e password nel cloud. Quando si archiviano dati sensibili e aziendali critici, è necessario eseguire le operazioni necessarie per ottimizzare la sicurezza degli insiemi di credenziali e i dati archiviati.

Questo articolo fornisce una panoramica delle funzionalità di sicurezza e delle procedure consigliate per Azure Key Vault. Per un elenco completo delle raccomandazioni, basate sul [benchmark di sicurezza di Azure](../../security/benchmarks/introduction.md), vedere la [baseline di sicurezza per Azure Key Vault](security-baseline.md).

## <a name="general-guidance"></a>Indicazioni generali

Si consiglia di usare un insieme di credenziali per ogni applicazione per ambiente (sviluppo, pre-produzione e produzione). Ciò consente di non condividere i segreti tra gli ambienti e di ridurre anche la minaccia in caso di violazione.

## <a name="network-security"></a>Sicurezza di rete

È possibile ridurre l'esposizione degli insiemi di credenziali specificando gli indirizzi IP che possono accedervi. Gli endpoint servizio di rete virtuale per Azure Key Vault consentono di limitare l'accesso a una rete virtuale specifica. Gli endpoint consentono anche di limitare l'accesso a un elenco di intervalli di indirizzi IPv4 (protocollo internet versione 4). L'accesso viene negato a tutti gli utenti che si connettono all'insieme di credenziali delle chiavi dall'esterno di tali origini.  Per informazioni dettagliate, vedere [endpoint del servizio rete virtuale per Azure Key Vault](overview-vnet-service-endpoints.md)

Quando le regole del firewall vengono applicate, gli utenti possono leggere i dati da Key Vault solo quando le loro richieste hanno origine da reti virtuali o intervalli di indirizzi IPv4 consentiti. Questo vale anche per l'accesso a Key Vault dal portale di Azure. Benché gli utenti possano accedere a un insieme di credenziali delle chiavi dal portale di Azure, potrebbero non essere in grado di elencare chiavi, segreti o certificati se il computer client in uso non è presente nell'elenco dei computer consentiti. Ciò influisce anche sul selettore dell'insieme di credenziali delle chiavi di altri servizi di Azure. Se le regole del firewall bloccano i computer client, gli utenti potrebbero essere in grado di visualizzare l'elenco degli insiemi di credenziali delle chiavi ma non di elencare le chiavi.  Per la procedura di implementazione, vedere [configurare i firewall e le reti virtuali di Azure Key Vault](network-security.md)

Il servizio Collegamento privato di Azure consente di accedere ad Azure Key Vault e ai servizi di clienti/partner ospitati in Azure tramite un endpoint privato nella rete virtuale. Un endpoint privato di Azure è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su Collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È possibile connettersi a un'istanza di una risorsa di Azure, garantendo il massimo livello di granularità nel controllo di accesso.  Per la procedura di implementazione, vedere [integrare Key Vault con il collegamento privato di Azure](private-link-service.md)

## <a name="tls-and-https"></a>TLS e HTTPS

- Il front-end Key Vault (piano dati) è un server multi-tenant. Questo significa che gli insiemi di credenziali delle chiavi di diversi clienti possono condividere lo stesso indirizzo IP pubblico. Per ottenere l'isolamento, ogni richiesta HTTP viene autenticata e autorizzata indipendentemente da altre richieste.
- È possibile identificare le versioni precedenti di TLS per segnalare le vulnerabilità, ma poiché l'indirizzo IP pubblico è condiviso, non è possibile per il team del servizio Key Vault disabilitare le versioni precedenti di TLS per gli insiemi di credenziali delle chiavi singoli a livello di trasporto.
- Il protocollo HTTPS consente al client di partecipare alla negoziazione TLS. I **client possono applicare la versione più recente di TLS** e, ogni volta che un client esegue questa operazione, l'intera connessione utilizzerà la protezione del livello corrispondente. Il fatto che Key Vault supporta ancora le versioni precedenti di TLS non compromettere la sicurezza delle connessioni usando versioni di TLS più recenti.
- Nonostante le vulnerabilità note nel protocollo TLS, non esiste alcun attacco noto che consentirebbe a un agente malintenzionato di estrarre tutte le informazioni dall'insieme di credenziali delle chiavi quando l'utente malintenzionato inizia una connessione con una versione di TLS con vulnerabilità. L'utente malintenzionato deve comunque eseguire l'autenticazione e l'autorizzazione e, a condizione che i client legittimi si connettano sempre alle versioni recenti di TLS, non è possibile che le credenziali siano state perse da vulnerabilità nelle versioni precedenti di TLS.

## <a name="identity-management"></a>Gestione delle identità

Quando si crea un insieme di credenziali delle chiavi in una sottoscrizione di Azure, questo viene automaticamente associato al tenant di Azure AD della sottoscrizione. Chiunque cerchi di gestire o recuperare il contenuto da un insieme di credenziali deve essere autenticato da Azure AD. In entrambi i casi, le applicazioni possono accedere a Key Vault in tre modi:

- **Solo applicazione**: l'applicazione rappresenta un'entità servizio o un'identità gestita. Questa identità è lo scenario più comune per le applicazioni che devono periodicamente accedere a certificati, chiavi o segreti dall'insieme di credenziali delle chiavi. Per il corretto funzionamento di questo scenario, `objectId` è necessario specificare l'oggetto dell'applicazione nei criteri di accesso `applicationId` e _non_ deve essere specificato o deve essere `null` .
- **Solo utente**: l'utente accede all'insieme di credenziali delle chiavi da qualsiasi applicazione registrata nel tenant. Azure PowerShell e il portale di Azure sono esempi di questo tipo di accesso. Per il corretto funzionamento di questo scenario, `objectId` è necessario specificare il parametro dell'utente nei criteri di accesso `applicationId` e _non_ deve essere specificato o deve essere `null` .
- **Application-Plus-User** (noto anche come _identità composta_): l'utente deve accedere all'insieme di credenziali delle chiavi da un'applicazione specifica _e_ l'applicazione deve usare il flusso di autenticazione (OBO) per rappresentare l'utente. Per il corretto funzionamento di questo scenario `applicationId` , `objectId` è necessario specificare sia che nei criteri di accesso. `applicationId`Identifica l'applicazione richiesta e `objectId` identifica l'utente. Attualmente questa opzione non è disponibile per il piano dati Azure RBAC (anteprima).

In tutti i tipi di accesso, l'applicazione esegue l'autenticazione con Azure AD. L'applicazione usa qualsiasi [metodo di autenticazione supportato](../../active-directory/develop/authentication-vs-authorization.md) in base al tipo di applicazione. L'applicazione acquisisce un token per una risorsa del piano per la concessione dell'accesso. La risorsa è un endpoint nel piano dati o di gestione, in base all'ambiente di Azure. L'applicazione usa il token e invia una richiesta API REST all'insieme di credenziali delle chiavi. Per altre informazioni, vedere l'[intero flusso di autenticazione](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Per informazioni dettagliate, vedere [nozioni di base sull'autenticazione Key Vault](authentication-fundamentals.md)

## <a name="privileged-access"></a>Accesso con privilegi

L'autorizzazione determina le operazioni che il chiamante può eseguire. L'autorizzazione in Key Vault usa una combinazione di [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md) e criteri di accesso Azure Key Vault.

L'accesso agli insiemi di credenziali avviene tramite due interfacce o piani. Questi piani sono il piano di gestione e il piano dati.

- Il *piano di gestione* consente di gestire Key Vault ed è l'interfaccia usata per creare ed eliminare gli insiemi di credenziali. È anche possibile leggere le proprietà degli insiemi di credenziali delle chiavi e gestire i criteri di accesso.
- Il *piano dati* consente di lavorare con i dati archiviati in un insieme di credenziali delle chiavi. È possibile aggiungere, eliminare e modificare chiavi, segreti e certificati.

Le applicazioni accedono ai piani tramite endpoint. I controlli di accesso per i due piani funzionano in maniera indipendente. Per concedere a un'applicazione l'accesso per usare le chiavi in un insieme di credenziali delle chiavi, l'accesso al piano dati viene concesso usando un criterio di accesso Key Vault o il controllo degli accessi in base al ruolo di Azure Per concedere a un utente l'accesso in lettura a Key Vault proprietà e tag, ma non l'accesso ai dati (chiavi, segreti o certificati), si concede l'accesso al piano di gestione con il controllo degli accessi in base al ruolo

La tabella seguente illustra gli endpoint per il piano dati e di gestione.

| Piano di&nbsp;accesso | Endpoint di accesso | Operazioni | Meccanismo di controllo&nbsp;di accesso |
| --- | --- | --- | --- |
| Piano di gestione | **Globale:**<br> management.azure.com:443<br><br> **21Vianet per Azure Cina:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Gov:**<br> management.usgovcloudapi.net:443<br><br> **Azure per la Germania:**<br> management.microsoftazure.de:443 | Creare, leggere, aggiornare ed eliminare insiemi di credenziali delle chiavi<br><br>Impostare i criteri di accesso dell'insieme di credenziali delle chiavi<br><br>Impostare tag per l'insieme di credenziali delle chiavi | Controllo degli accessi in base al ruolo di Azure |
| Piano dati | **Globale:**<br> &lt;nome-insiemecredenziali&gt;.vault.azure.net:443<br><br> **21Vianet per Azure Cina:**<br> &lt;nome-insiemecredenziali&gt;.vault.azure.cn:443<br><br> **Azure US Gov:**<br> &lt;nome-insiemecredenziali&gt;.vault.usgovcloudapi.net:443<br><br> **Azure per la Germania:**<br> &lt;nome-insiemecredenziali&gt;.vault.microsoftazure.de:443 | Chiavi: crittografia, decrittografia, wrapKey, unwrapKey, firma, verifica, recupero, elenco, creazione, aggiornamento, importazione, eliminazione, ripristino, backup, ripristino, eliminazione<br><br> Certificati: managecontacts, getissuers, listissuers, seemittenti, deleteissuers, manageissuers, Get, List, create, Import, Update, DELETE, Recover, backup, Restore, Purge<br><br>  Segreti: Get, List, set, DELETE, Recover, backup, Restore, Purge | Criteri di accesso Key Vault o RBAC di Azure (anteprima)|

### <a name="managing-administrative-access-to-key-vault"></a>Gestione dell'accesso amministrativo a Key Vault

Quando si crea un insieme di credenziali delle chiavi in un gruppo di risorse, si gestisce l'accesso usando Azure AD. È possibile consentire a utenti o gruppi di gestire gli insiemi di credenziali delle chiavi in un gruppo di risorse. È possibile concedere l'accesso a un determinato livello di ambito assegnando i ruoli di Azure appropriati. Per concedere l'accesso a un utente in modo che possa gestire insiemi di credenziali delle chiavi, assegnare all'utente un ruolo `key vault Contributor` predefinito in un ambito specifico. I livelli di ambito seguenti possono essere assegnati a un ruolo di Azure:

- **Sottoscrizione**: un ruolo di Azure assegnato a livello di sottoscrizione si applica a tutti i gruppi di risorse e le risorse all'interno della sottoscrizione.
- **Gruppo di risorse**: un ruolo di Azure assegnato a livello di gruppo di risorse si applica a tutte le risorse nel gruppo di risorse.
- **Risorsa specifica**: un ruolo di Azure assegnato a una risorsa specifica si applica a tale risorsa. In questo caso, la risorsa è un insieme di credenziali delle chiavi specifico.

Ci sono diversi ruoli predefiniti. Se un ruolo predefinito non soddisfa le specifiche esigenze, è possibile definire un ruolo personalizzato. Per altre informazioni, vedere controllo degli accessi [in base al ruolo di Azure: ruoli predefiniti](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Se un utente ha le autorizzazioni `Contributor` per un piano di gestione di un insieme di credenziali delle chiavi, l'utente può concedere a se stesso l'accesso al piano dati impostando criteri di accesso dell'insieme di credenziali delle chiavi. È necessario controllare attentamente chi ha accesso al ruolo `Contributor` per gli insiemi di credenziali delle chiavi. Assicurarsi che solo gli utenti autorizzati possano accedere e gestire gli insiemi di credenziali delle chiavi, le chiavi, i segreti e i certificati.

### <a name="controlling-access-to-key-vault-data"></a>Controllo dell'accesso ai dati di Key Vault

I criteri di accesso di Key Vault concedono autorizzazioni separate per chiavi, segreti o certificati. È possibile concedere a un utente l'accesso solo alle chiavi e non ai segreti. Le autorizzazioni di accesso per chiavi, segreti e certificati vengono gestite a livello di insieme di credenziali.

> [!IMPORTANT]
> I criteri di accesso dell'insieme di credenziali delle chiavi non supportano le autorizzazioni granulari a livello di oggetto, ad esempio una chiave, un segreto o un certificato specifico. Quando a un utente viene concessa l'autorizzazione per creare ed eliminare chiavi, può eseguire tali operazioni su tutte le chiavi dell'insieme di credenziali.

È possibile impostare i criteri di accesso per un insieme di credenziali delle chiavi usando il [portale di Azure](assign-access-policy-portal.md), l'interfaccia della riga di comando di [Azure](assign-access-policy-cli.md), [Azure PowerShell](assign-access-policy-powershell.md)o le [API REST di gestione Key Vault](/rest/api/keyvault/).

È possibile limitare l'accesso al piano dati usando gli [endpoint servizio di rete virtuale per Azure Key Vault](overview-vnet-service-endpoints.md). È possibile configurare [firewall e regole di rete virtuale](network-security.md) per un ulteriore livello di sicurezza.

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

La funzionalità di registrazione di Key Vault salva le informazioni sulle attività eseguite nell'insieme di credenziali. Per informazioni dettagliate, vedere [Key Vault registrazione](logging.md).

È possibile integrare Key Vault con griglia di eventi per ricevere una notifica quando lo stato di una chiave, un certificato o un segreto archiviato in Key Vault è stato modificato. Per informazioni dettagliate, vedere [Key Vault di monitoraggio con griglia di eventi di Azure](event-grid-overview.md)

È anche importante monitorare l'integrità dell'insieme di credenziali delle chiavi, per assicurarsi che il servizio funzioni come previsto. Per informazioni su come eseguire questa operazione, vedere [monitoraggio e avviso per Azure Key Vault](alert.md).

## <a name="backup-and-recovery"></a>Backup e ripristino

Azure Key Vault la protezione dell'eliminazione temporanea e della ripulitura consente di recuperare gli insiemi di credenziali eliminati e gli oggetti dell'insieme di credenziali. Per informazioni dettagliate, vedere [Azure Key Vault Panoramica dell'eliminazione](soft-delete-overview.md)temporanea.

È anche necessario eseguire backup regolari dell'insieme di credenziali per l'aggiornamento, l'eliminazione o la creazione di oggetti all'interno di un insieme di credenziali.  

Azure PowerShell comandi di backup:

* [Certificato di backup](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [Chiave di backup](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [Segreto di backup](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

Comandi di backup di Azure CLI

* [Certificato di backup](/cli/azure/keyvault/certificate#az-keyvault-certificate-backup)
* [Chiave di backup](/cli/azure/keyvault/key#az-keyvault-key-backup)
* [Segreto di backup](/cli/azure/keyvault/secret#az-keyvault-secret-backup)


## <a name="next-steps"></a>Passaggi successivi

- [Endpoint servizio di rete virtuale per Azure Key Vault](overview-vnet-service-endpoints.md)
- [Controllo degli accessi in base al ruolo Azure: ruoli predefiniti](../../role-based-access-control/built-in-roles.md)
