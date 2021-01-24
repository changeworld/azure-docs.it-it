---
title: Modifiche importanti previste per il Centro sicurezza di Azure
description: Prossime modifiche al Centro sicurezza di Azure che può essere opportuno conoscere e per le quali potrebbe essere necessaria una pianificazione
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 1b034c0f1c62eecf8139ed908a5a242060f3e886
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746561"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Modifiche importanti che interesseranno il Centro sicurezza di Azure

> [!IMPORTANT]
> Le informazioni contenute in questa pagina si riferiscono a prodotti o funzionalità non definitivi che possono subire modifiche sostanziali prima dell'eventuale rilascio della versione commerciale. Microsoft non rilascia alcuna garanzia, espressa o implicita, in merito alle informazioni fornite in questa pagina.

Questa pagina descrive le modifiche pianificate per il Centro sicurezza. Illustra le modifiche al prodotto previste che potrebbero influire su elementi come il punteggio di sicurezza o i flussi di lavoro.

Se si cercano le note sulla versione più recenti, vedere [Novità del Centro sicurezza di Azure](release-notes.md).


## <a name="planned-changes"></a>Modifiche pianificate

- [Le raccomandazioni per la protezione del carico di lavoro Kubernetes verranno presto rilasciate per la disponibilità generale (GA)](#kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga)
- [Due consigli dal controllo di sicurezza "Applica aggiornamenti del sistema" obsoleti](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Miglioramenti alla raccomandazione di classificazione dei dati SQL](#enhancements-to-sql-data-classification-recommendation)
- [Aggiunte 35 raccomandazioni di anteprima per aumentare la copertura di Azure Security Benchmark](#35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark)


### <a name="kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga"></a>Le raccomandazioni per la protezione del carico di lavoro Kubernetes verranno presto rilasciate per la disponibilità generale (GA)

**Data stimata per la modifica:** gennaio 2021

I consigli sulla protezione del carico di lavoro Kubernetes descritti in [proteggere i carichi di lavoro di Kubernetes](kubernetes-workload-protections.md) sono attualmente in anteprima. Mentre una raccomandazione è in anteprima, non esegue il rendering di una risorsa non integro e non è inclusa nei calcoli del Punteggio sicuro.

Questi consigli verranno presto rilasciati per la disponibilità generale (GA), quindi *verranno* inclusi nel calcolo del punteggio. Se non sono già stati corretti, questo potrebbe causare un lieve effetto sul punteggio sicuro.

Correggerli laddove possibile (informazioni su come correggere le [raccomandazioni nel centro sicurezza di Azure](security-center-remediate-recommendations.md)).

Le raccomandazioni per la protezione del carico di lavoro Kubernetes sono:

- Il componente aggiuntivo di criteri di Azure per Kubernetes deve essere installato e abilitato nei cluster
- È consigliabile applicare limiti per la CPU e la memoria dei contenitori
- I contenitori con privilegi devono essere evitati
- Per i contenitori deve essere imposto il file system radice non modificabile (di sola lettura)
- È consigliabile evitare i contenitori con escalation dei privilegi
- È consigliabile evitare l'esecuzione di contenitori come utente radice
- I contenitori che condividono spazi dei nomi host sensibili devono essere evitati
- Per i contenitori devono essere imposte le funzionalità Linux con privilegi minimi
- L'utilizzo dei montaggi dei volumi HostPath dei pod deve essere limitato a un elenco noto
- I contenitori devono essere in ascolto solo sulle porte consentite
- I servizi devono essere in ascolto solo sulle porte consentite
- L'utilizzo della rete host e delle porte deve essere limitato
- La sovrascrittura o la disabilitazione del profilo AppArmor dei contenitori deve essere limitata
- Le immagini del contenitore devono essere distribuite solo da registri attendibili             

Altre informazioni su questi consigli sono disponibili in [proteggere i carichi di lavoro Kubernetes](kubernetes-workload-protections.md).

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Due consigli dal controllo di sicurezza "Applica aggiornamenti del sistema" obsoleti 

**Data stimata per la modifica:** 2021 febbraio

Le due raccomandazioni seguenti sono state pianificate per essere deprecate nel 2021 febbraio:

- **Per applicare gli aggiornamenti del sistema, è necessario riavviare i computer**. Questo potrebbe causare un lieve effetto sul punteggio sicuro.
- **È necessario installare l'agente di monitoraggio nei computer**. Questa raccomandazione si riferisce solo ai computer locali e la relativa logica verrà trasferita a un'altra raccomandazione, **log Analytics problemi di integrità dell'agente devono essere risolti nei computer**. Questo potrebbe causare un lieve effetto sul punteggio sicuro.

Si consiglia di controllare le configurazioni di esportazione continua e di automazione del flusso di lavoro per verificare se tali raccomandazioni sono incluse. Inoltre, tutti i dashboard o altri strumenti di monitoraggio che potrebbero utilizzarli devono essere aggiornati di conseguenza.

Per ulteriori informazioni su questi consigli, vedere la pagina di riferimento relativa alle [raccomandazioni sulla sicurezza](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Miglioramenti alla raccomandazione di classificazione dei dati SQL

**Data stimata per la modifica:** Q2 2021

La versione corrente dei **dati sensibili delle raccomandazioni nei database SQL deve essere classificata** in applica il controllo di sicurezza per la **classificazione dei dati** verrà deprecata e sostituita con una nuova versione più allineata alla strategia di classificazione dei dati di Microsoft. Di conseguenza:

- La raccomandazione non influirà più sul punteggio sicuro
- Il controllo di sicurezza ("applica classificazione dati") non influirà più sul punteggio sicuro
- Anche l'ID raccomandazione cambierà (attualmente b0df6f56-862d-4730-8597-38c0fd4ebd59)


### <a name="35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark"></a>Aggiunte 35 raccomandazioni di anteprima per aumentare la copertura di Azure Security Benchmark

**Data stimata per la modifica:** gennaio 2021

Azure Security Benchmark è il set di linee guida specifiche di Azure create da Microsoft per le procedure consigliate per la sicurezza e la conformità basate su framework di conformità comuni. [Altre informazioni su Azure Security Benchmark](../security/benchmarks/introduction.md).

Le seguenti 35 raccomandazioni di anteprima verranno aggiunte al Centro sicurezza per aumentare la copertura del benchmark.

Le raccomandazioni in anteprima non contrassegnano una risorsa come non integra e non sono incluse nei calcoli del punteggio di sicurezza. Correggerle non appena possibile, in modo che possano contribuire al punteggio al termine del periodo di anteprima. Per altre informazioni su come rispondere a queste raccomandazioni, vedere [Correzione delle raccomandazioni nel Centro sicurezza di Azure](security-center-remediate-recommendations.md).

| Controllo di sicurezza                     | Nuove raccomandazioni                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Abilita la crittografia dei dati inattivi            | - Gli account Azure Cosmos DB devono usare chiavi gestite dal cliente per la crittografia dei dati inattivi<br>- Le aree di lavoro di Azure Machine Learning devono essere crittografate con una chiave gestita dal cliente<br>- La protezione dei dati BYOK (Bring Your Own Key) deve essere abilitata per i server MySQL<br>- La protezione dei dati BYOK (Bring Your Own Key) deve essere abilitata per i server PostgreSQL<br>- Per gli account di Servizi cognitivi è necessario abilitare la crittografia dei dati con chiave gestita dal cliente<br>- I registri contenitori devono essere crittografati con una chiave gestita dal cliente<br>- Le istanze gestite di SQL devono usare chiavi gestite dal cliente per la crittografia dei dati inattivi<br>- I server SQL devono usare chiavi gestite dal cliente per la crittografia dei dati inattivi<br>- Gli account di archiviazione devono usare la chiave gestita dal cliente per la crittografia                                                                                                                                                              |
| Implementa le procedure consigliate per la sicurezza    | - Per le sottoscrizioni deve essere impostato un indirizzo di posta elettronica di contatto per i problemi relativi alla sicurezza<br> - Il provisioning automatico dell'agente di Log Analytics deve essere abilitato nella sottoscrizione<br> - Le notifiche di posta elettronica devono essere abilitate per gli avvisi con gravità alta<br> - Le notifiche di posta elettronica al proprietario della sottoscrizione devono essere abilitate per gli avvisi con gravità alta<br> - Negli insiemi di credenziali delle chiavi deve essere abilitata la protezione dalla rimozione definitiva<br> - Negli insiemi di credenziali delle chiavi deve essere abilitata la funzionalità di eliminazione temporanea |
| Gestire l'accesso e le autorizzazioni        | - Per le app per le funzioni deve essere abilitata l'opzione 'Certificati client (certificati client in ingresso)' |
| Proteggi le applicazioni da attacchi DDoS | - Web Application Firewall (WAF) deve essere abilitato per il gateway applicazione<br> - Web Application Firewall (WAF) deve essere abilitato per il servizio Frontdoor di Azure |
| Limita l'accesso non autorizzato alla rete | - Il firewall deve essere abilitato in Key Vault<br> - L'endpoint privato deve essere configurato per Key Vault<br> - Configurazione app deve usare collegamenti privati<br> - La cache di Azure per Redis deve risiedere all'interno di una rete virtuale<br> - I domini di Griglia di eventi di Azure devono usare collegamenti privati<br> - Gli argomenti di Griglia di eventi di Azure devono usare collegamenti privati<br> - Le aree di lavoro di Azure Machine Learning devono usare collegamenti privati<br> - Il servizio Azure SignalR deve usare collegamenti privati<br> - Azure Spring Cloud deve usare l'aggiunta alla rete<br> - I registri contenitori non devono consentire l'accesso alla rete senza restrizioni<br> - I registri contenitori devono usare collegamenti privati<br> - L'accesso alla rete pubblica deve essere disabilitato per i server MariaDB<br> - L'accesso alla rete pubblica deve essere disabilitato per i server MySQL<br> - L'accesso alla rete pubblica deve essere disabilitato per i server PostgreSQL<br> - L'account di archiviazione deve usare una connessione collegamento privato<br> - Gli account di archiviazione devono limitare l'accesso alla rete usando regole di rete virtuale<br> - I modelli di Image Builder per macchine virtuali devono usare un collegamento privato|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Collegamenti correlati:

- [Altre informazioni su Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Altre informazioni su Database di Azure per MariaDB](../mariadb/overview.md)
- [Altre informazioni su Database di Azure per MySQL](../mysql/overview.md)
- [Altre informazioni su Database di Azure per PostgreSQL](../postgresql/overview.md)





## <a name="next-steps"></a>Passaggi successivi

Per tutte le modifiche recenti al prodotto, vedere [Novità del Centro sicurezza di Azure](release-notes.md).