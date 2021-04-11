---
title: Disponibilità e continuità
titleSuffix: Azure Cognitive Search
description: informazioni su come rendere il servizio di ricerca altamente disponibile e resiliente contro interruzioni del periodo o anche errori irreversibili.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: references_regions
ms.openlocfilehash: 493f6759f63f023572f38647076e04425acf9d6a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581522"
---
# <a name="availability-and-business-continuity-in-azure-cognitive-search"></a>Disponibilità e continuità aziendale in Azure ricerca cognitiva

In ricerca cognitiva la disponibilità avviene attraverso più repliche, mentre la continuità aziendale e il ripristino di emergenza vengono realizzati tramite più servizi di ricerca. Questo articolo fornisce indicazioni che è possibile usare come punto di partenza per lo sviluppo di una strategia che soddisfi i requisiti aziendali per la disponibilità e le operazioni continue.

<a name="scale-for-availability"></a>

## <a name="high-availability"></a>Disponibilità elevata

In ricerca cognitiva le repliche sono copie dell'indice. La presenza di più repliche consente ad Azure ricerca cognitiva di eseguire il riavvio e la manutenzione del computer su una replica, mentre l'esecuzione delle query continua su altre repliche. Per altre informazioni sull'aggiunta di repliche, vedere [aggiungere o ridurre le repliche e le partizioni](search-capacity-planning.md#adjust-capacity).

Per ogni singolo servizio di ricerca, Microsoft garantisce una disponibilità di almeno il 99,9% per le configurazioni che soddisfano i criteri seguenti: 

+ Due repliche per la disponibilità elevata di carichi di lavoro di sola lettura, vale a dire query.

+ Tre o più repliche per la disponibilità elevata dei carichi di lavoro di lettura e scrittura (query e indicizzazione) 

Per il livello gratuito non è previsto alcun contratto di licenza. Per altre informazioni, vedere [SLA per Azure ricerca cognitiva](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

<a name="availability-zones"></a>

## <a name="availability-zones"></a>Zone di disponibilità

[Zone di disponibilità](../availability-zones/az-overview.md) sono una funzionalità della piattaforma Azure che suddivide i Data Center di un'area in gruppi di percorsi fisici distinti per fornire disponibilità elevata, all'interno della stessa area. Se si usa zone di disponibilità per ricerca cognitiva, le singole repliche sono le unità per l'assegnazione di zona. Un servizio di ricerca viene eseguito all'interno di un'area; le repliche vengono eseguite in zone diverse.

È possibile usare zone di disponibilità con ricerca cognitiva di Azure aggiungendo due o più repliche al servizio di ricerca. Ogni replica verrà posizionata in una zona di disponibilità diversa all'interno dell'area. Se si dispone di più repliche rispetto a zone di disponibilità, le repliche verranno distribuite tra zone di disponibilità nel modo più uniforme possibile. Non esiste alcuna azione specifica da parte dell'utente, fatta eccezione per la [creazione di un servizio di ricerca](search-create-service-portal.md) in un'area che fornisce zone di disponibilità e quindi per configurare il servizio per l' [uso di più repliche](search-capacity-planning.md#adjust-capacity).

Azure ricerca cognitiva supporta attualmente zone di disponibilità per i servizi di ricerca di livello standard o superiore creati in una delle aree seguenti:

+ Australia orientale (creato il 30 gennaio 2021 o versione successiva)
+ Canada centrale (creato il 30 gennaio 2021 o versione successiva)
+ Stati Uniti centrali (creati il 4 dicembre 2020 o versione successiva)
+ Stati Uniti orientali (creati il 27 gennaio 2021 o versione successiva)
+ Stati Uniti orientali 2 (creati il 30 gennaio 2021 o versione successiva)
+ Francia centrale (creato il 23 ottobre 2020 o versione successiva)
+ Giappone orientale (creato il 30 gennaio 2021 o versione successiva)
+ Europa settentrionale (creata il 28 gennaio 2021 o versione successiva)
+ Sud Asia orientale (creato il 31 gennaio 2021 o versione successiva)
+ Regno Unito meridionale (creato il 30 gennaio 2021 o versione successiva)
+ Europa occidentale (creata il 29 gennaio 2021 o versione successiva)
+ Stati Uniti occidentali 2 (creati il 30 gennaio 2021 o versione successiva)

Zone di disponibilità non influiscano sul [contratto di servizio di ricerca cognitiva di Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Sono comunque necessarie tre o più repliche per la disponibilità elevata delle query.

## <a name="multiple-services-in-separate-geographic-regions"></a>Più servizi in aree geografiche separate

Sebbene la maggior parte dei clienti usi un solo servizio, la ridondanza del servizio potrebbe essere necessaria se i requisiti operativi includono i seguenti elementi:

+ [Continuità aziendale e ripristino di emergenza (BCdR)](../best-practices-availability-paired-regions.md) (ricerca cognitiva non fornisce il failover immediato in caso di interruzione).
+ Applicazioni distribuite a livello globale. Se le richieste di query e indicizzazione provengono da tutto il mondo, gli utenti più vicini all'host data center avranno prestazioni più veloci. La creazione di servizi aggiuntivi in aree con prossimità vicina a questi utenti può migliorare le prestazioni di tutti gli utenti.
+ Le [architetture multi-tenant](search-modeling-multitenant-saas-applications.md) talvolta richiedono due o più servizi.

Se sono necessari altri due servizi di ricerca, la loro creazione in aree diverse può soddisfare i requisiti dell'applicazione per la continuità e il ripristino, oltre a tempi di risposta più rapidi per una base utente globale.

Azure ricerca cognitiva attualmente non fornisce un metodo automatizzato per la replica geografica degli indici di ricerca tra le aree, ma sono disponibili alcune tecniche che possono rendere questo processo semplice da implementare e gestire. Queste tecniche vengono illustrate nelle prossime sezioni.

L'obiettivo di un set con distribuzione geografica di servizi di ricerca consiste nel disporre di due o più indici disponibili in due o più aree, in cui un utente viene indirizzato al servizio ricerca cognitiva di Azure che offre la latenza più bassa:

   ![Incrocio dei servizi per area][1]

È possibile implementare questa architettura creando più servizi e progettando una strategia per la sincronizzazione dei dati. Facoltativamente, è possibile includere una risorsa come gestione traffico di Azure per il routing delle richieste. Per altre informazioni, vedere [creare un servizio di ricerca](search-create-service-portal.md).

<a name="data-sync"></a>

### <a name="keep-data-synchronized-across-multiple-services"></a>Mantieni i dati sincronizzati tra più servizi

Sono disponibili due opzioni per mantenere sincronizzati due o più servizi di ricerca distribuiti, che includono l'uso dell' [indicizzatore ricerca cognitiva di Azure](search-indexer-overview.md) o l'API push (detta anche [API REST di Azure ricerca cognitiva](/rest/api/searchservice/)). 

#### <a name="option-1-use-indexers-for-updating-content-on-multiple-services"></a>Opzione 1: usare gli indicizzatori per aggiornare il contenuto su più servizi

Se si usa già l'indicizzatore in un servizio, è possibile configurare un secondo indicizzatore in un secondo servizio per usare lo stesso oggetto origine dati, estraendo i dati dallo stesso percorso. Ogni servizio in ogni area ha un proprio indicizzatore e un indice di destinazione (l'indice di ricerca non è condiviso, il che significa che i dati sono duplicati), ma ogni indicizzatore fa riferimento alla stessa origine dati.

Di seguito è riportato un oggetto visivo di alto livello dell'aspetto dell'architettura.

   ![Singola origine dati con indicizzatore distribuito e combinazioni di servizio][2]

#### <a name="option-2-use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Opzione 2: usare le API REST per eseguire il push degli aggiornamenti del contenuto su più servizi

Se si usa l'API REST di Azure ricerca cognitiva per eseguire il [push del contenuto nell'indice di ricerca](tutorial-optimize-indexing-push-api.md), è possibile gestire i vari servizi di ricerca sincronizzando le modifiche apportate a tutti i servizi di ricerca ogni volta che è necessario un aggiornamento. Nel codice, assicurarsi di gestire i casi in cui un aggiornamento a un servizio di ricerca ha esito negativo, ma ha esito positivo per altri servizi di ricerca.

### <a name="use-azure-traffic-manager-to-coordinate-requests"></a>Usare gestione traffico di Azure per coordinare le richieste

[Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md) consente di instradare le richieste a più siti Web con localizzazione geografica, supportati da più servizi di ricerca. Un vantaggio di gestione traffico è che può sondare Azure ricerca cognitiva per assicurarsi che sia disponibile e indirizzare gli utenti a servizi di ricerca alternativi in caso di tempi di inattività. Inoltre, se si esegue il routing delle richieste di ricerca tramite siti Web di Azure, gestione traffico di Azure consente di bilanciare il carico dei casi in cui il sito Web è attivo, ma non di Azure ricerca cognitiva. Di seguito è riportato un esempio di un'architettura che usa Gestione traffico.

   ![Incrocio dei servizi per area con Gestione traffico centrale][3]

## <a name="disaster-recovery-and-service-outages"></a>Interruzioni di servizio e ripristino di emergenza

Sebbene sia possibile recuperare i dati, Azure ricerca cognitiva non fornisce il failover immediato del servizio in caso di interruzione a livello di cluster o di data center. Se un cluster non funziona nel data center, il team operativo lo rileverà e provvederà a ripristinare il servizio. Si verificheranno tempi di inattività durante il ripristino del servizio ma è possibile richiedere i crediti del servizio per compensarne la non disponibilità in base al [Contratto di servizio (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Se un servizio continuo è necessario in caso di errori irreversibili che Microsoft non può controllare, è necessario [eseguire il provisioning di un servizio aggiuntivo](search-create-service-portal.md) in un'area diversa e implementare una strategia di replica geografica per garantire che gli indici siano completamente ridondanti in tutti i servizi.

I clienti che usano gli [indicizzatori](search-indexer-overview.md) per compilare e aggiornare gli indici possono gestire il ripristino di emergenza tramite gli indicizzatori specifici per l'area geografica che sfruttando la stessa origine dati. Due servizi in diverse aree, ognuno dei quali esegue un indicizzatore, possono indicizzare la stessa origine dati per ottenere la ridondanza geografica. Se si esegue l'indicizzazione da origini dati anche con ridondanza geografica, tenere presente che gli indicizzatori di Azure ricerca cognitiva possono eseguire solo l'indicizzazione incrementale (Unione di aggiornamenti da documenti nuovi, modificati o eliminati) dalle repliche primarie. In un evento di failover, assicurarsi di puntare di nuovo all'indicizzatore per la nuova replica primaria. 

Se non si usano gli indicizzatori, l'utente userà il codice dell'applicazione per eseguire il push sugli oggetti e i dati per diversi servizi in parallelo. Per altre informazioni, vedere [Mantieni i dati sincronizzati tra più servizi](#data-sync).

## <a name="back-up-and-restore-alternatives"></a>Eseguire il backup e il ripristino delle alternative

Poiché Azure ricerca cognitiva non è una soluzione di archiviazione dati primaria, Microsoft non fornisce un meccanismo formale per il backup e il ripristino self-service. Tuttavia, è possibile usare il codice di esempio **index-backup-restore** in questo [repository di esempio di Azure ricerca cognitiva .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples) per eseguire il backup della definizione dell'indice e dello snapshot in una serie di file JSON e quindi usare questi file per ripristinare l'indice, se necessario. Questo strumento può inoltre spostare gli indici tra i livelli di servizio.

In caso contrario, il codice dell'applicazione utilizzato per creare e popolare un indice è l'opzione di ripristino de facto se si elimina un indice per errore. Per ricompilare un indice, è necessario eliminarlo (supponendo che sia presente), ricreare l'indice nel servizio e ricaricare recuperando i dati dall'archivio dati primario.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui piani tariffari e sui limiti dei servizi, vedere [limiti del servizio](search-limits-quotas-capacity.md). Per altre informazioni sulle combinazioni di partizioni e repliche, vedere [pianificare la capacità](search-capacity-planning.md) .

Per informazioni sulle prestazioni e le dimostrazioni delle tecniche descritte in questo articolo, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png