---
author: rothja
ms.service: app-service
ms.topic: include
ms.date: 03/17/2020
ms.author: msangapu
ms.openlocfilehash: 80d295d017b11d86df7a3fe4c14afc7a5665cd96
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612897"
---
| Risorsa | Gratuito | Condiviso | Basic | Standard | Premium (v1-v3) | Isolato </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [App Web, per dispositivi mobili o per le API](https://azure.microsoft.com/services/app-service/) per [piano di servizio app di Azure](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Illimitato<sup>2</sup> |Illimitato<sup>2</sup> |Illimitato<sup>2</sup> |Illimitato<sup>2</sup>|
| [Piano di servizio app](../articles/app-service/overview-hosting-plans.md) |10 per area |10 per gruppo di risorse. |100 per gruppo di risorse |100 per gruppo di risorse |100 per gruppo di risorse |100 per gruppo di risorse|
| Tipo di istanza di calcolo |Condiviso |Condiviso |Dedicato<sup>3</sup> |Dedicato<sup>3</sup> |Dedicato<sup>3</sup></p> |Dedicato<sup>3</sup>|
| [Aumento delle istanze](../articles/app-service/manage-scale-up.md) (numero massimo di istanze) |1 condivisa |1 condivisa |3 dedicati<sup>3</sup> |10 dedicati<sup>3</sup> | 20 dedicate per v1 e v2; 30 dedicate per v3.<sup>3</sup>|100 dedicati<sup>4</sup>|
| Archiviazione<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup> <br/><br/> Per dimensioni maggiori di 250 GB, inviare una richiesta di supporto. |1 TB<sup>5</sup> <br/><br/> La quota di archiviazione disponibile è di 999 GB. |
| Tempo di CPU (5 minuti)<sup>6</sup> |3 minuti |3 minuti |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a>|
| Tempo di CPU (giorno)<sup>6</sup> |60 minuti |240 minuti |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |Senza limiti, pagamento con [tariffe](https://azure.microsoft.com/pricing/details/app-service/) standard</a> |
| Memoria (1 ora) |1\.024 MB per ogni piano di servizio app |1\.024 MB per ogni app |N/D |N/D |N/D |N/D |
| Larghezza di banda |165 MB |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |Illimitato, [trasferimento dei dati tariffe](https://azure.microsoft.com/pricing/details/data-transfers/) applicate |
| Architettura dell'applicazione |32 bit |32 bit |32 bit/64 bit |32 bit/64 bit |32 bit/64 bit |32 bit/64 bit |
| Web Socket per ogni istanza<sup>7</sup> |5 |35 |350 |Nessuna limitazione |Nessuna limitazione |Nessuna limitazione |
| Connessioni IP in uscita per istanza | 600 | 600 | Dipende dalle dimensioni dell'istanza<sup>8</sup> | Dipende dalle dimensioni dell'istanza<sup>8</sup> | Dipende dalle dimensioni dell'istanza<sup>8</sup> | 16.000 |
| [Connessioni di debugger](../articles/app-service/troubleshoot-dotnet-visual-studio.md) simultanee per applicazione |1 |1 |1 |5 |5 |5 |
| Certificati del servizio app per sottoscrizione<sup>9</sup>| Non supportate | Non supportate |10 |10 |10 |10 |
| Domini personalizzati per applicazione</a> |0 (solo sottodominio azurewebsites.net)|500 |500 |500 |500 |500 |
| Supporto per il dominio personalizzato [SSL](../articles/app-service/configure-ssl-certificate.md) |Non supportato. Certificato con caratteri jolly per \* disponibile per impostazione predefinita|Non supportato. Certificato con caratteri jolly per \* disponibile per impostazione predefinita|Connessioni SNI SSL senza limiti |Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse |Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse | Connessioni SNI SSL senza limiti e 1 connessione IP SSL incluse|
| Connessioni ibride | | | 5 per piano | 25 per piano | 200 per app | 200 per app |
| [Integrazione della rete virtuale](../articles/app-service/web-sites-integrate-with-vnet.md) | | |   |  X |  X  |  X  |
| [Endpoint privati](../articles/app-service/networking/private-endpoint.md) | | |   |   |  100 per app  |    |
| Bilanciamento del carico integrato | |X |X |X |X |X<sup>10</sup> |
| [Restrizioni di accesso](../articles/app-service/networking-features.md#access-restrictions) | 512 regole per app | 512 regole per app | 512 regole per app | 512 regole per app | 512 regole per app | 512 regole per app |
| [Always On](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Backup pianificati](../articles/app-service/manage-backup.md) | | | | Backup pianificati ogni 2 ore, massimo 12 backup al giorno (manuali e pianificati) | Backup pianificati ogni ora, massimo 50 backup al giorno (manuali e pianificati) | Backup pianificati ogni ora, massimo 50 backup al giorno (manuali e pianificati) |
| [Autoscale](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [Processi Web](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Monitoraggio endpoint](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Slot di gestione temporanea](../articles/app-service/deploy-staging-slots.md) per app| | | |5 |20 |20 |
| [Testing in Production](../articles/app-service/deploy-staging-slots.md#route-traffic)| | | |X |X |X |
| [Log di diagnostica](../articles/app-service/troubleshoot-diagnostic-logs.md) | X | X | X | X | X | X |
| Kudu | X | X | X | X | X | X |
| [Autenticazione e autorizzazione](../articles/app-service/overview-authentication-authorization.md) | X | X | X | X | X | X |
| [Certificati gestiti del servizio app (anteprima pubblica)](https://azure.microsoft.com/updates/secure-your-custom-domains-at-no-cost-with-app-service-managed-certificates-preview/)<sup>12</sup> | |  | X | X | X | X |
| Contratto di servizio | |  |99,95%|99,95%|99,95%|99,95%|

<sup>1</sup> Le quote per app e archiviazione sono per piano di servizio app, se non diversamente specificato.

<sup>2</sup> Il numero di app effettivo che è possibile ospitare in questi computer dipende dall'attività delle app, dalle dimensioni delle istanze del computer e dall'uso delle risorse corrispondente.

<sup>3</sup> Le istanze dedicate possono essere di dimensioni diverse. Per altre informazioni, vedere [Prezzi del Servizio app](https://azure.microsoft.com/pricing/details/app-service/).

sono consentite altre <sup>4</sup> richieste su richiesta.

<sup>5</sup> il limite di archiviazione è la dimensione totale del contenuto in tutte le app nello stesso piano di servizio app. La dimensione totale del contenuto di tutte le app in tutti i piani di servizio app in un singolo gruppo di risorse e area non può superare 500 GB. La quota file system per le app ospitate nel servizio app è determinata dall'aggregazione dei piani di servizio app creati in un'area e in un gruppo di risorse.

<sup>6</sup> Queste risorse sono vincolate dalle risorse fisiche delle istanze dedicate (dimensione dell'istanza e numero di istanze).

<sup>7</sup> Se si ridimensiona a due istanze un'app nel livello Basic, sono presenti 350 connessioni simultanee per ognuna delle due istanze. Per il livello Standard e per i livelli superiori non esistono limiti teorici per i socket Web, ma altri fattori possono limitare il numero di socket Web. Ad esempio, il numero massimo di richieste simultanee consentite (definite da `maxConcurrentRequestsPerCpu`) è: 7.500 per macchina virtuale di piccole dimensioni, 15.000 per macchina virtuale media (7.500 x 2 core) e 75.000 per macchina virtuale di grandi dimensioni (18.750 x 4 core).

<sup>8</sup> le connessioni IP massime sono per istanza e dipendono dalle dimensioni dell'istanza: 1.920 per l'istanza B1/S1/P1V3, 3.968 per l'istanza B2/S2/P2V3, 8.064 per l'istanza B3/S3/P3V3.

<sup>9</sup> è possibile aumentare il limite di quota certificato del servizio app per sottoscrizione tramite una richiesta di supporto fino al limite massimo di 200.

<sup>10</sup> servizio app isolato SKU possono essere con bilanciamento del carico interno (ILB) con Azure Load Balancer, quindi non esiste una connettività pubblica da Internet. Di conseguenza, alcune funzionalità di un servizio app isolato con carico interno bilanciato devono essere usate da macchine virtuali con accesso diretto all'endpoint di rete con bilanciamento del carico interno.

<sup>11</sup> eseguire i file eseguibili e/o gli script personalizzati su richiesta, in base a una pianificazione o in modo continuo come attività in background nell'istanza del servizio app. Always On è richiesto per l'esecuzione continua di Processi Web. Non esiste un limite predefinito per il numero di processi Web che possono essere eseguiti in un'istanza del servizio app. Esistono però limiti pratici che dipendono dalle operazioni che il codice dell'applicazione sta provando a eseguire.

<sup>12</sup> domini nudi non sono supportati. Vengono emessi solo certificati standard (non sono disponibili certificati con caratteri jolly). Offerta limitata a un solo certificato gratuito per dominio personalizzato.
