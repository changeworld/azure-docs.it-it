---
title: Informazioni sulla migrazione per gli avvisi di monitoraggio di Azure
description: Informazioni sul funzionamento della migrazione degli avvisi e sulla risoluzione dei problemi.
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
author: yalavi
ms.openlocfilehash: 88b75c46898e1a74b33051d8996af96201f639c7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037711"
---
# <a name="understand-migration-options-to-newer-alerts"></a>Informazioni sulle opzioni di migrazione per gli avvisi più recenti

Gli avvisi classici sono [ritirati](./monitoring-classic-retirement.md) per gli utenti del cloud pubblico, anche se sono ancora in uso limitato fino al **31 maggio 2021**. Gli avvisi classici per il cloud di Azure per enti pubblici e Azure Cina 21Vianet si ritireranno il **29 febbraio 2024**.

Questo articolo illustra come funziona lo strumento migrazione manuale e migrazione volontaria, che verrà usato per eseguire la migrazione delle regole di avviso rimanenti. Vengono inoltre descritte le soluzioni per alcuni problemi comuni.

> [!IMPORTANT]
> Gli avvisi del log attività (inclusi gli avvisi di integrità del servizio) e gli avvisi del log non sono interessati dalla migrazione. La migrazione si applica solo alle regole di avviso classiche descritte di [seguito](./monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

> [!NOTE]
> Se le regole di avviso classiche non sono valide, vale a dire che si trovano su metriche o risorse [deprecate](#classic-alert-rules-on-deprecated-metrics) che sono state eliminate, non verranno migrate e non saranno disponibili dopo il ritiro del servizio.

## <a name="manually-migrating-classic-alerts-to-newer-alerts"></a>Migrazione manuale di avvisi classici a avvisi più recenti

I clienti interessati alla migrazione manuale dei rimanenti avvisi possono già farlo usando le sezioni seguenti. Include anche le metriche ritirate e pertanto non è possibile eseguirne la migrazione direttamente.

### <a name="guest-metrics-on-virtual-machines"></a>Metriche Guest nelle macchine virtuali

Prima di poter creare nuovi avvisi sulle metriche Guest, è necessario inviare le metriche Guest all'archivio dei log di monitoraggio di Azure. Seguire queste istruzioni per creare gli avvisi:

- [Abilitazione della raccolta di metriche Guest in log Analytics](../agents/agent-data-sources.md)
- [Creazione di avvisi del log in monitoraggio di Azure](./alerts-log.md)

Sono disponibili altre opzioni per la raccolta di metriche e avvisi per gli ospiti, [altre informazioni](../agents/agents-overview.md).

### <a name="storage-and-classic-storage-account-metrics"></a>Metriche di archiviazione e dell'account di archiviazione classico

È possibile eseguire la migrazione di tutti gli avvisi classici sugli account di archiviazione, eccetto gli avvisi relativi a queste metriche:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

È necessario eseguire la migrazione delle regole di avviso classiche sulla percentuale di metriche in base [al mapping tra la metrica di archiviazione precedente e quella nuova](../../storage/common/storage-metrics-migration.md#metrics-mapping-between-old-metrics-and-new-metrics). Le soglie dovranno essere modificate in modo appropriato perché la nuova metrica disponibile è assoluta.

Le regole di avviso classiche in AnonymousThrottlingError, SASThrottlingError e ThrottlingError devono essere suddivise in due nuovi avvisi perché non esiste alcuna metrica combinata che fornisce la stessa funzionalità. Le soglie dovranno essere adattate in modo appropriato.

### <a name="cosmos-db-metrics"></a>Metriche di Cosmos DB

È possibile eseguire la migrazione di tutti gli avvisi classici sulle metriche di Cosmos DB, eccetto gli avvisi relativi a queste metriche:

- Media richieste al secondo
- Livello di coerenza
- Http 2xx
- Http 3xx
- Numero massimo di RUPM utilizzati al minuto
- Numero massimo di ur al secondo
- Addebito per altre richieste di Mongo
- Frequenza di richieste altre
- Latenza di lettura osservata
- Latenza di scrittura osservata
- Disponibilità del servizio
- Capacità di archiviazione

Media di richieste al secondo, livello di coerenza, numero massimo di RUPM consumate al minuto, numero massimo di ur al secondo, latenza di lettura osservata, latenza di scrittura osservata e capacità di archiviazione non sono attualmente disponibili nel [nuovo sistema](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts).

Gli avvisi sulle metriche delle richieste come HTTP 2xx, HTTP 3xx e la disponibilità del servizio non vengono migrati perché il modo in cui vengono conteggiate le richieste è diverso tra le metriche classiche e le nuove metriche. Gli avvisi su queste metriche dovranno essere ricreati manualmente con le soglie regolate.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Regole di avviso classiche su metriche deprecate

Di seguito sono riportate le regole di avviso classiche sulle metriche che in precedenza erano supportate ma che erano deprecate. Una piccola percentuale di clienti potrebbe avere regole di avviso classiche non valide per tali metriche. Poiché queste regole di avviso non sono valide, non verranno migrate.

| Tipo di risorsa| Metriche deprecate |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, limitazione, dtu_consumption_percent, storage_used |
| Microsoft. Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft. Web/hostingEnvironments/dei pool | BytesReceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Modalità di creazione di nuovi gruppi di azione e regole di avviso equivalenti

Lo strumento di migrazione converte le regole di avviso classiche in nuovi gruppi di azione e regole di avviso equivalenti. Per la maggior parte delle regole di avviso classiche, le nuove regole di avviso equivalenti sono sulla stessa metrica con le stesse proprietà, ad esempio `windowSize` e `aggregationType` . Tuttavia, alcune regole di avviso classiche sono relative alle metriche con una metrica equivalente diversa nel nuovo sistema. I principi seguenti si applicano alla migrazione di avvisi classici, a meno che non sia specificato nella sezione seguente:

- **Frequency**: definisce la frequenza con cui una regola di avviso classica o nuova controlla la condizione. Le `frequency` regole di avviso classiche non sono configurabili dall'utente ed è sempre 5 minuti per tutti i tipi di risorse. Anche la frequenza delle regole equivalenti è impostata su 5 min.
- **Tipo di aggregazione**: definisce la modalità di aggregazione della metrica sulla finestra di interesse. `aggregationType`È anche lo stesso tra gli avvisi classici e i nuovi avvisi per la maggior parte delle metriche. In alcuni casi, poiché la metrica è diversa tra gli avvisi classici e i nuovi avvisi, viene usato il valore equivalente `aggregationType` o quello `primary Aggregation Type` definito per la metrica.
- **Unità**: proprietà della metrica in cui viene creato l'avviso. Alcune metriche equivalenti hanno unità diverse. La soglia viene modificata in modo appropriato in base alle esigenze. Se, ad esempio, la metrica originale presenta secondi come unità, ma la nuova metrica equivalente ha millisecondi come unità, la soglia originale viene moltiplicata per 1000 per garantire lo stesso comportamento.
- **Dimensioni finestra**: definisce la finestra su cui vengono aggregati i dati delle metriche per il confronto con la soglia. Per i valori standard, ad `windowSize` esempio 5 minuti, 15 minuti, 30 minuti, 1 ora, 3 ore, 6 ore, 12 ore, 1 giorno, non sono state apportate modifiche per una nuova regola di avviso equivalente. Per gli altri valori, viene usato il più vicino `windowSize` . Per la maggior parte dei clienti, questa modifica non ha alcun effetto. Per una piccola percentuale di clienti, potrebbe essere necessario modificare la soglia per ottenere lo stesso comportamento.

Nelle sezioni seguenti vengono illustrate in dettaglio le metriche con una metrica equivalente diversa nel nuovo sistema. Tutte le metriche rimanenti per le regole di avviso classiche e nuove non sono elencate. È possibile trovare un elenco delle metriche supportate nel nuovo sistema [qui](../essentials/metrics-supported.md).

### <a name="microsoftstoragestorageaccounts-and-microsoftclassicstoragestorageaccounts"></a>Microsoft. storage/storageAccounts e Microsoft. ClassicStorage/storageAccounts

Per i servizi dell'account di archiviazione come BLOB, tabelle, file e code, le metriche seguenti sono mappate alle metriche equivalenti, come illustrato di seguito:

| Metrica negli avvisi classici | Metrica equivalente nei nuovi avvisi | Commenti|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Metrica transazioni con dimensioni "ResponseType" = "AuthorizationError" e "autenticazione" = "anonima"| |
| AnonymousClientOtherError | Metrica transazioni con dimensioni "ResponseType" = "ClientOtherError" e "autenticazione" = "anonima" | |
| AnonymousClientTimeOutError| Metrica transazioni con dimensioni "ResponseType" = "ClientTimeOutError" e "autenticazione" = "anonima" | |
| AnonymousNetworkError | Metrica transazioni con dimensioni "ResponseType" = "NetworkError" e "autenticazione" = "anonima" | |
| AnonymousServerOtherError | Metrica transazioni con dimensioni "ResponseType" = "ServerOtherError" e "autenticazione" = "anonima" | |
| AnonymousServerTimeOutError | Metrica transazioni con dimensioni "ResponseType" = "ServerTimeOutError" e "autenticazione" = "anonima" | |
| AnonymousSuccess | Metrica transazioni con dimensioni "ResponseType" = "operazione riuscita" e "autenticazione" = "anonima" | |
| AuthorizationError | Metrica transazioni con dimensioni "ResponseType" = "AuthorizationError" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Capacità | BlobCapacity | Usare `aggregationType` ' Average ' invece di ' Last '. La metrica si applica solo ai servizi BLOB |
| ClientOtherError | Metrica transazioni con dimensioni "ResponseType" = "ClientOtherError"  | |
| ClientTimeoutError | Metrica transazioni con dimensioni "ResponseType" = "ClientTimeOutError" | |
| ContainerCount | ContainerCount | Usare `aggregationType` ' Average ' invece di ' Last '. La metrica si applica solo ai servizi BLOB |
| NetworkError | Metrica transazioni con dimensioni "ResponseType" = "NetworkError" | |
| ObjectCount | BlobCount| Usare `aggregationType` ' Average ' invece di ' Last '. La metrica si applica solo ai servizi BLOB |
| SASAuthorizationError | Metrica delle transazioni con dimensioni "ResponseType" = "AuthorizationError" e "Authentication" = "SAS" | |
| SASClientOtherError | Metrica delle transazioni con dimensioni "ResponseType" = "ClientOtherError" e "Authentication" = "SAS" | |
| SASClientTimeOutError | Metrica delle transazioni con dimensioni "ResponseType" = "ClientTimeOutError" e "Authentication" = "SAS" | |
| SASNetworkError | Metrica delle transazioni con dimensioni "ResponseType" = "NetworkError" e "Authentication" = "SAS" | |
| SASServerOtherError | Metrica delle transazioni con dimensioni "ResponseType" = "ServerOtherError" e "Authentication" = "SAS" | |
| SASServerTimeOutError | Metrica delle transazioni con dimensioni "ResponseType" = "ServerTimeOutError" e "Authentication" = "SAS" | |
| SASSuccess | Metrica Transactions con Dimensions "ResponseType" = "success" e "Authentication" = "SAS" | |
| ServerOtherError | Metrica transazioni con dimensioni "ResponseType" = "ServerOtherError" | |
| ServerTimeOutError | Metrica transazioni con dimensioni "ResponseType" = "ServerTimeOutError"  | |
| Operazione riuscita | Metrica transazioni con dimensioni "ResponseType" = "operazione riuscita" | |
| TotalBillableRequests| Transazioni | |
| TotalEgress | Egress | |
| TotalIngress | Dati in ingresso | |
| TotalRequests | Transazioni | |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Per Cosmos DB, le metriche equivalenti sono illustrate di seguito:

| Metrica negli avvisi classici | Metrica equivalente nei nuovi avvisi | Commenti|
|--------------------------|---------------------------------|---------|
| AvailableStorage | AvailableStorage||
| Dimensioni dei dati | DataUsage| |
| Conteggio documenti | DocumentCount||
| Dimensioni dell'indice | IndexUsage||
| Servizio non disponibile | ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||
| Richieste limitate | TotalRequests con Dimension "StatusCode" = "429"| Il tipo di aggregazione ' Average ' è stato corretto in ' count '|
| Errori interni server | TotalRequests con Dimension "StatusCode" = "500"}| Il tipo di aggregazione ' Average ' è stato corretto in ' count '|
| Http 401 | TotalRequests con Dimension "StatusCode" = "401"| Il tipo di aggregazione ' Average ' è stato corretto in ' count '|
| Http 400 | TotalRequests con Dimension "StatusCode" = "400"| Il tipo di aggregazione ' Average ' è stato corretto in ' count '|
| Totale richieste | TotalRequests| Il tipo di aggregazione ' Max ' è stato corretto in ' count '|
| Addebito richiesta numero Mongo| MongoRequestCharge con Dimension "CommandName" = "count"||
| Frequenza richieste conteggio Mongo | MongoRequestsCount con Dimension "CommandName" = "count"||
| Costo richiesta di eliminazione Mongo | MongoRequestCharge con Dimension "CommandName" = "Delete"||
| Mongo Delete Request Rate (Frequenza richieste di eliminazione Mongo) | MongoRequestsCount con Dimension "CommandName" = "Delete"||
| Costo richiesta di inserimento Mongo | MongoRequestCharge con Dimension "CommandName" = "Insert"||
| Mongo Insert Request Rate (Frequenza richieste di inserimento Mongo) | MongoRequestsCount con Dimension "CommandName" = "Insert"||
| Addebito richieste di query Mongo | MongoRequestCharge con Dimension "CommandName" = "Find"||
| Mongo Query Request Rate (Frequenza richieste di query Mongo) | MongoRequestsCount con Dimension "CommandName" = "Find"||
| Costo richiesta aggiornamento Mongo | MongoRequestCharge con Dimension "CommandName" = "Update"||
| Richieste di inserimento non riuscite di Mongo | MongoRequestCount con Dimensions "CommandName" = "Insert" e "status" = "failed"| Il tipo di aggregazione ' Average ' è stato corretto in ' count '|
| Richieste di query Mongo non riuscite | MongoRequestCount con Dimensions "CommandName" = "query" e "status" = "failed"| Il tipo di aggregazione ' Average ' è stato corretto in ' count '|
| Numero di richieste non riuscite di Mongo | MongoRequestCount con Dimensions "CommandName" = "count" e "status" = "failed"| Il tipo di aggregazione ' Average ' è stato corretto in ' count '|
| Richieste di aggiornamento Mongo non riuscite | MongoRequestCount con Dimensions "CommandName" = "Update" e "status" = "failed"| Il tipo di aggregazione ' Average ' è stato corretto in ' count '|
| Mongo altre richieste non riuscite | MongoRequestCount con Dimensions "CommandName" = "other" e "status" = "failed"| Il tipo di aggregazione ' Average ' è stato corretto in ' count '|
| Richieste di eliminazione Mongo non riuscite | MongoRequestCount con Dimensions "CommandName" = "Delete" e "status" = "failed"| Il tipo di aggregazione ' Average ' è stato corretto in ' count '|

### <a name="how-equivalent-action-groups-are-created"></a>Modalità di creazione di gruppi di azioni equivalenti

Le regole di avviso classiche avevano le azioni di posta elettronica, webhook, app per la logica e Runbook associate alla regola di avviso. Le nuove regole di avviso usano i gruppi di azioni che possono essere riutilizzati in più regole di avviso. Lo strumento di migrazione crea il gruppo di azioni singolo per le stesse azioni, indipendentemente dal numero di regole di avviso che usano l'azione. I gruppi di azioni creati dallo strumento di migrazione usano il formato di denominazione ' Migrated_AG *'.

> [!NOTE]
> Gli avvisi classici hanno inviato messaggi di posta elettronica localizzati in base alle impostazioni locali dell'amministratore classico quando vengono usati per notificare i ruoli di amministratore classico. I nuovi messaggi di avviso vengono inviati tramite gruppi di azioni e sono solo in lingua inglese.

## <a name="rollout-phases"></a>Fasi di implementazione

Lo strumento di migrazione sta implementando fasi per i clienti che usano le regole di avviso classiche. I proprietari delle sottoscrizioni riceveranno un messaggio di posta elettronica quando la sottoscrizione è pronta per la migrazione tramite lo strumento.

> [!NOTE]
> Poiché lo strumento viene implementato in fasi, è possibile notare che alcune sottoscrizioni non sono ancora pronte per essere migrate durante le fasi iniziali.

La maggior parte delle sottoscrizioni è attualmente contrassegnata come pronta per la migrazione. Solo le sottoscrizioni con avvisi classici sui tipi di risorse seguenti non sono ancora pronte per la migrazione.

- Microsoft. classicCompute/domainNames/Slots/Roles
- Microsoft. Insights/Components

## <a name="who-can-trigger-the-migration"></a>Chi può attivare la migrazione?

Tutti gli utenti che dispongono del ruolo predefinito di collaboratore del monitoraggio a livello di sottoscrizione possono attivare la migrazione. Gli utenti che dispongono di un ruolo personalizzato con le autorizzazioni seguenti possono anche attivare la migrazione:

- */lettura
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- Microsoft.AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> Oltre a disporre delle autorizzazioni precedenti, la sottoscrizione deve essere registrata anche con il provider di risorse Microsoft. AlertsManagement. Questa operazione è necessaria per eseguire correttamente la migrazione degli avvisi di anomalia degli errori in Application Insights. 

## <a name="common-problems-and-remedies"></a>Problemi comuni e soluzioni

Dopo l' [attivazione della migrazione](alerts-using-migration-tool.md), si riceverà un messaggio di posta elettronica presso gli indirizzi forniti per notificare che la migrazione è stata completata o se è necessaria un'azione da parte dell'utente. In questa sezione vengono descritti alcuni problemi comuni e le relative modalità di gestione.

### <a name="validation-failed"></a>Convalida non riuscita

A causa di alcune recenti modifiche apportate alle regole di avviso classiche nella sottoscrizione, non è possibile eseguire la migrazione della sottoscrizione. Questo problema è temporaneo. È possibile riavviare la migrazione dopo che lo stato della migrazione è stato spostato di nuovo **pronto per la migrazione** entro pochi giorni.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>Blocco dell'ambito che impedisce la migrazione delle regole

Come parte della migrazione, verranno creati nuovi avvisi per le metriche e nuovi gruppi di azioni, quindi verranno eliminate le regole di avviso classiche. Tuttavia, un blocco di ambito può impedire la creazione o l'eliminazione di risorse. A seconda del blocco dell'ambito, non è stato possibile eseguire la migrazione di alcune o di tutte le regole. È possibile risolvere il problema rimuovendo il blocco dell'ambito per la sottoscrizione, il gruppo di risorse o la risorsa, elencato nello [strumento di migrazione](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)e riattivando la migrazione. Il blocco dell'ambito non può essere disabilitato e deve essere rimosso durante il processo di migrazione. [Altre informazioni sulla gestione dei blocchi di ambito](../../azure-resource-manager/management/lock-resources.md#portal).

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>I criteri con effetto ' nega ' impediscono la migrazione delle regole

Come parte della migrazione, verranno creati nuovi avvisi per le metriche e nuovi gruppi di azioni, quindi verranno eliminate le regole di avviso classiche. Tuttavia, un'assegnazione di [criteri di Azure](../../governance/policy/index.yml) può impedire la creazione di risorse. A seconda dell'assegnazione dei criteri, non è stato possibile eseguire la migrazione di alcune o di tutte le regole. Le assegnazioni di criteri che bloccano il processo sono elencate nello [strumento di migrazione](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel). Per risolvere il problema, eseguire una delle operazioni seguenti:

- Esclusione di sottoscrizioni, gruppi di risorse o singole risorse durante il processo di migrazione dall'assegnazione dei criteri. [Altre informazioni sulla gestione degli ambiti di esclusione dei criteri](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion).
- Impostare la modalità di imposizione su **disabilitata** nell'assegnazione dei criteri. [Altre informazioni sulla proprietà enforcementMode dell'assegnazione dei criteri](../../governance/policy/concepts/assignment-structure.md#enforcement-mode).
- Impostare un'esenzione di criteri di Azure (anteprima) per le sottoscrizioni, i gruppi di risorse o le singole risorse nell'assegnazione dei criteri. [Altre informazioni sulla struttura di esenzione dei criteri di Azure](../../governance/policy/concepts/exemption-structure.md).
- La rimozione o la modifica dell'effetto in ' disabled ',' audit ',' append ' o ' Modify ' (che, ad esempio, può risolvere i problemi relativi ai tag mancanti). [Altre informazioni sulla gestione degli effetti dei criteri](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>Passaggi successivi

- [Come usare lo strumento di migrazione](alerts-using-migration-tool.md)
- [Preparare la migrazione](alerts-prepare-migration.md)