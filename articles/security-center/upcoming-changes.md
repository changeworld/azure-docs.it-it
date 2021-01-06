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
ms.date: 01/05/2021
ms.author: memildin
ms.openlocfilehash: 0656000a1d6449306e8afe538f846c55c79a31a2
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915287"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Modifiche importanti che interesseranno il Centro sicurezza di Azure

> [!IMPORTANT]
> Le informazioni contenute in questa pagina si riferiscono a prodotti o funzionalità non definitivi che possono subire modifiche sostanziali prima dell'eventuale rilascio della versione commerciale. Microsoft non rilascia alcuna garanzia, espressa o implicita, in merito alle informazioni fornite in questa pagina.

Questa pagina descrive le modifiche pianificate per il Centro sicurezza. Illustra le modifiche al prodotto previste che potrebbero influire su elementi come il punteggio di sicurezza o i flussi di lavoro.

Se si cercano le note sulla versione più recenti, vedere [Novità del Centro sicurezza di Azure](release-notes.md).


## <a name="planned-changes"></a>Modifiche pianificate

- [Le risorse "non applicabili" devono essere segnalate come "conformi" nelle valutazioni di Criteri di Azure](#not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments)
- [Aggiunte 35 raccomandazioni di anteprima per aumentare la copertura di Azure Security Benchmark](#35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark)

### <a name="not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments"></a>Le risorse "non applicabili" devono essere segnalate come "conformi" nelle valutazioni di Criteri di Azure

**Data stimata per la modifica:** gennaio 2021

Attualmente le risorse valutate per una raccomandazione e ritenute **non applicabili** vengono visualizzate in Criteri di Azure come "non conformi". Gli utenti non possono in alcun modo cambiare questo stato in "Conforme". A partire da questa modifica pianificata queste risorse verranno segnalate come "conformi" per una maggiore chiarezza.

L'unico effetto si vedrà in Criteri di Azure, dove il numero di risorse conformi aumenterà. Non ci sarà alcun impatto sul punteggio di sicurezza nel Centro sicurezza di Azure.

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