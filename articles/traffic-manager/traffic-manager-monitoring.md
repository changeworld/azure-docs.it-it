---
title: Monitoraggio degli endpoint di Gestione traffico di Azure | Microsoft Docs
description: Questo articolo illustra in che modo Gestione traffico usa il monitoraggio e il failover automatico degli endpoint per consentire ai clienti di Azure di distribuire applicazioni a disponibilità elevata
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2021
ms.author: duau
ms.openlocfilehash: 455eeb83ef5a9608c077de24b8d3d4722d26a822
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742710"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Monitoraggio degli endpoint di Gestione traffico

Gestione traffico di Azure include il monitoraggio degli endpoint e il failover automatico degli endpoint. Questa funzionalità consente di distribuire applicazioni a disponibilità elevata resilienti agli errori di endpoint, inclusi gli errori di area di Azure.

## <a name="configure-endpoint-monitoring"></a>Configurare il monitoraggio degli endpoint

Per configurare il monitoraggio degli endpoint è necessario specificare le seguenti impostazioni nel profilo di Gestione traffico:

* **Protocollo**. Scegliere HTTP, HTTPS o TCP come protocollo che Gestione traffico usa quando esegue il sondaggio dell'endpoint per verificarne l'integrità. Il monitoraggio HTTPS non verifica se il certificato TLS/SSL è valido. verifica solo che il certificato sia presente.
* **Porta**. scegliere la porta usata per la richiesta.
* **Path**. Questa impostazione di configurazione è valida solo per i protocolli HTTP e HTTPS, per i quali è necessario specificare l'impostazione del percorso. Se si specifica questa impostazione per il protocollo di monitoraggio TCP, viene generato un errore. Per i protocolli HTTP e HTTPS specificare il percorso relativo e il nome della pagina Web o il file a cui accede il monitoraggio. Una barra (/) è una voce valida per il percorso relativo. Questo valore implica che il file sia nella directory radice (impostazione predefinita).
* **Impostazioni dell'intestazione personalizzata**. Questa impostazione di configurazione consente di aggiungere intestazioni HTTP specifiche ai controlli di integrità inviati da Gestione traffico agli endpoint in un profilo. È possibile specificare le intestazioni personalizzate a livello di profilo (rendendole applicabili per tutti gli endpoint nel profilo) e/o a livello di endpoint (rendendole applicabili solo a tale endpoint). È possibile utilizzare intestazioni personalizzate per i controlli di integrità degli endpoint in un ambiente multi-tenant. In questo modo è possibile instradare correttamente la destinazione specificando un'intestazione host. È anche possibile usare questa impostazione mediante l'aggiunta di intestazioni univoche che possono essere usate per identificare le richieste HTTP(S) originate da Gestione traffico ed elaborarle in modo diverso. È possibile specificare fino a otto coppie intestazione: valore separate da una virgola. Ad esempio, "Header1: value1, header2: Value2". 
* **Intervalli di codice di stato previsti**. Questa impostazione consente di specificare più intervalli di codice con esito positivo nel formato 200-299, 301-301. Se questi codici di stato vengono ricevuti come risposta da un endpoint quando viene eseguito un controllo di integrità, gestione traffico contrassegna tali endpoint come integri. È possibile specificare un massimo di otto intervalli di codice di stato. Questa impostazione è applicabile solo al protocollo HTTP e HTTPS e a tutti gli endpoint. Questa impostazione è a livello di profilo di Gestione traffico e per impostazione predefinita è definito il valore 200 come il codice di stato di riuscita.
* **Intervallo di probe**. Questo valore specifica la frequenza con cui viene controllata l'integrità di un endpoint dall'agente di sondaggio di Gestione traffico. È possibile specificare due valori qui: 30 secondi (sondaggio normale) e 10 secondi (sondaggio veloce). Se non viene specificato alcun valore, il profilo imposta un valore predefinito di 30 secondi. Per altre informazioni sui prezzi per il sondaggio rapido, visitare la pagina dei [prezzi per Gestione traffico](https://azure.microsoft.com/pricing/details/traffic-manager).
* **Numero di errori tollerati**. Questo valore specifica il numero di errori tollerati da un agente di sondaggio di Gestione traffico prima di contrassegnare l'endpoint come non integro. Il valore può essere compreso tra 0 e 9. Un valore pari a 0 indica che un singolo errore di monitoraggio può far sì che l'endpoint venga contrassegnato come non integro. Se non si specifica alcun valore, viene usato il valore predefinito di 3.
* **Timeout del probe**. Questa proprietà specifica l'intervallo di tempo durante il quale l'agente di sondaggio di gestione traffico deve attendere prima di prendere in considerazione un errore di verifica di un probe di integrità. Se l'intervallo sondaggio è impostato su 30 secondi, è possibile impostare il valore di timeout tra 5 e 10 secondi. Se non si specifica alcun valore, viene usato il valore predefinito di 10 secondi. Se l'intervallo sondaggio è impostato su 10 secondi, è possibile impostare il valore di timeout tra 5 e 9 secondi. Se non si specifica alcun valore di timeout, viene usato il valore predefinito di 9 secondi.

    ![Monitoraggio degli endpoint di Gestione traffico](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Figura: monitoraggio degli endpoint di Gestione traffico**

## <a name="how-endpoint-monitoring-works"></a>Funzionamento del monitoraggio degli endpoint

Quando il protocollo di monitoraggio è impostato come HTTP o HTTPS, l'agente di sondaggio di gestione traffico esegue una richiesta GET all'endpoint usando il protocollo, la porta e il percorso relativo specificati. Un endpoint è considerato integro se l'agente di sondaggio riceve una risposta 200-OK o una qualsiasi delle risposte configurate negli **\* intervalli di codice di stato previsti**. Se la risposta è un valore diverso o se non viene ricevuta alcuna risposta entro il periodo di timeout, l'agente di sondaggio di gestione traffico tenterà di ritentare in base all'impostazione numero di errori tollerati. Se questa impostazione è 0, non viene eseguito alcun tentativo. L'endpoint viene contrassegnato come non integro se il numero di errori consecutivi è superiore all'impostazione del numero di errori tollerati.

Quando il protocollo di monitoraggio è TCP, l'agente di sondaggio di gestione traffico crea una richiesta di connessione TCP utilizzando la porta specificata. Se l'endpoint risponde alla richiesta con una risposta per stabilire la connessione, il controllo dell'integrità viene contrassegnato come riuscito. L'agente di sondaggio di gestione traffico Reimposta la connessione TCP. Nei casi in cui la risposta è un valore diverso o se non viene ricevuta alcuna risposta entro il periodo di timeout, l'agente di sondaggio di gestione traffico tenterà di ritentare in base all'impostazione relativa al numero di errori tollerati. Se questa impostazione è 0, non viene eseguito alcun tentativo. Se il numero di tentativi consecutivi non riusciti è superiore all'impostazione di Numero di errori tollerati, l'endpoint viene contrassegnato come non integro.

In tutti i casi, gestione traffico esegue il probe da più posizioni. L'errore consecutivo determina cosa accade all'interno di ogni area. Ecco perché gli endpoint ricevono Probe di integrità da Gestione traffico con una frequenza maggiore rispetto all'impostazione usata per l'intervallo di sondaggio.

>[!NOTE]
>Per il protocollo di monitoraggio HTTP o HTTPS, una pratica comune sul lato endpoint consiste nell'implementare una pagina personalizzata all'interno dell'applicazione, ad esempio /health.aspx. Usando questo percorso per il monitoraggio, è possibile eseguire controlli specifici dell'applicazione, ad esempio il controllo dei contatori delle prestazioni o la verifica della disponibilità del database. In base a questi controlli personalizzati, la pagina restituisce un codice di stato HTTP appropriato.

Tutti gli endpoint in un profilo di Gestione traffico condividono le impostazioni di monitoraggio. Se è necessario usare impostazioni di monitoraggio diverse per i vari endpoint, è possibile creare i [profili nidificati di Gestione traffico](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Stato di endpoint e profili

Gli endpoint e i profili di Gestione traffico possono essere abilitati e disabilitati. Tuttavia, potrebbe verificarsi una modifica dello stato dell'endpoint a causa delle impostazioni e dei processi automatici di gestione traffico.

### <a name="endpoint-status"></a>Stato endpoint

È possibile abilitare o disabilitare un endpoint specifico. Il servizio sottostante, che potrebbe essere ancora integro, non è interessato. La modifica dello stato dell'endpoint controlla la disponibilità dell'endpoint nel profilo di Gestione traffico. Quando lo stato di un endpoint è disabilitato, gestione traffico non ne verifica l'integrità e l'endpoint non viene incluso in una risposta DNS.

### <a name="profile-status"></a>Stato profilo

Usando l'impostazione dello stato del profilo è possibile abilitare o disabilitare un profilo specifico. Mentre lo stato dell'endpoint interessa un solo endpoint, lo stato del profilo interessa l'intero profilo, che include tutti gli endpoint. Quando si disabilita un profilo, gli endpoint non vengono controllati per verificare l'integrità e nessun endpoint viene incluso in una risposta DNS. Per la query DNS viene restituito un codice di risposta [NXDOMAIN](https://tools.ietf.org/html/rfc2308) .

### <a name="endpoint-monitor-status"></a>Endpoint monitor status (Stato monitoraggio endpoint)

Il valore relativo allo stato di monitoraggio dell'endpoint viene generato da Gestione traffico per indicare lo stato corrente dell'endpoint. Questa impostazione non può essere modificata manualmente. Lo stato del monitoraggio dell'endpoint è una combinazione dei risultati del monitoraggio dell'endpoint e dello stato dell'endpoint configurato. La tabella seguente indica i valori possibili dello stato di monitoraggio degli endpoint:

| Stato profilo | Stato endpoint | Endpoint monitor status (Stato monitoraggio endpoint) | Note |
| --- | --- | --- | --- |
| Disabled |Attivato |Inattivo |Il profilo è stato disabilitato. Anche se lo stato dell'endpoint è Enabled, se lo stato del profilo è Disabled, quest'ultimo avrà la precedenza. Gli endpoint nei profili disabilitati non vengono monitorati. Per la query DNS viene restituito un codice di risposta NXDOMAIN. |
| &lt;qualsiasi&gt; |Disabled |Disabled |L'endpoint è stato disabilitato. Gli endpoint disabilitati non vengono monitorati. L'endpoint non è incluso nelle risposte DNS, di conseguenza non riceve traffico. |
| Attivato |Attivato |Online |L'endpoint è monitorato e integro. È incluso nelle risposte DNS e può ricevere traffico. |
| Attivato |Attivato |Degraded |I controlli di integrità del monitoraggio dell'endpoint hanno esito negativo. L'endpoint non è incluso nelle risposte DNS e non riceve traffico. <br>Se tutti gli endpoint sono danneggiati, viene generata un'eccezione. In tal caso tutti gli elementi vengono considerati restituiti nella risposta alla query.</br>|
| Attivato |Attivato |CheckingEndpoint |L'endpoint è monitorato, ma i risultati del primo probe non sono stati ancora ricevuti. CheckingEndpoint è un stato temporaneo che in genere si verifica immediatamente dopo l'aggiunta o l'abilitazione di un endpoint nel profilo. Un endpoint con questo stato viene incluso nelle risposte DNS e può ricevere traffico. |
| Attivato |Attivato |Arrestato |L'app Web a cui punta l'endpoint non è in esecuzione. Controllare le impostazioni dell'app Web. Questo stato può verificarsi anche se l'endpoint è di tipo endpoint annidato e il profilo figlio viene disabilitato o non è attivo. <br>Un endpoint con stato arrestato non viene monitorato. Non è incluso nelle risposte DNS e non riceve traffico. Se tutti gli endpoint sono danneggiati, viene generata un'eccezione. In tal caso, tutti verranno considerati come restituiti nella risposta alla query.</br>|

Per informazioni dettagliate su come viene calcolato lo stato del monitoraggio degli endpoint nidificati, vedere [Profili nidificati di Gestione traffico](traffic-manager-nested-profiles.md).

>[!NOTE]
> Uno stato di monitoraggio dell'endpoint di tipo Interrotto può verificarsi nel servizio app se l'applicazione Web non è in esecuzione nel livello Standard o superiore. Per altre informazioni, vedere [Integrazione di Gestione traffico con il servizio app](../app-service/web-sites-traffic-manager.md).

### <a name="profile-monitor-status"></a>Stato monitoraggio profilo

Lo stato di monitoraggio del profilo è una combinazione dei valori relativi allo stato del profilo configurato e allo stato di monitoraggio di tutti gli endpoint. I valori possibili sono illustrati nella tabella seguente:

| Stato profilo (come configurato) | Endpoint monitor status (Stato monitoraggio endpoint) | Stato monitoraggio profilo | Note |
| --- | --- | --- | --- |
| Disabled |&lt;qualsiasi&gt; o un profilo senza endpoint definiti. |Disabled |Il profilo è stato disabilitato. |
| Abilitato |Almeno un endpoint è associato allo stato Degraded. |Degraded |Esaminare i valori di stato dei singoli endpoint per determinare quali endpoint richiedono attenzione. |
| Abilitato |Almeno un endpoint è associato allo stato Online. Nessun endpoint presenta lo stato Degraded. |Online |Il servizio sta accettando il traffico. Non è richiesta alcuna azione ulteriore. |
| Abilitato |Almeno un endpoint è associato allo stato CheckingEndpoint. Nessun endpoint presenta lo stato Online o Degraded. |CheckingEndpoints |Questo stato di transizione si verifica quando un profilo viene creato o abilitato. Viene verificata l'integrità dell'endpoint per la prima volta. |
| Abilitato |Tutti gli endpoint del profilo presentano lo stato Disabled o Stopped oppure nel profilo non sono definiti endpoint. |Inattivo |Non ci sono endpoint attivi, ma il profilo presenta lo stato Enabled. |

## <a name="endpoint-failover-and-recovery"></a>Failover e ripristino degli endpoint

Gestione traffico verifica periodicamente l'integrità di ogni endpoint, inclusi gli endpoint non integri. Rileva quando un endpoint diventa integro e lo reinserisce nella rotazione.

Un endpoint non è integro quando si verifica uno degli eventi seguenti:

- Se il protocollo di monitoraggio è HTTP o HTTPS:
    - Una risposta non 200 o una risposta che non include l'intervallo di stato specificato nell'impostazione degli **intervalli di codice di stato prevista** Get received. (Incluso un codice 2xx diverso o un reindirizzamento 301/302).
- Se il protocollo di monitoraggio è TCP: 
    - Una risposta diversa da ACK o SYN-ACK viene ricevuta in risposta alla richiesta SYN inviata da Gestione traffico per tentare di stabilire una connessione.
- Timeout. 
- Qualsiasi altro problema di connessione che determina l'irraggiungibilità dell'endpoint.

Per ulteriori informazioni su come risolvere i problemi relativi ai controlli non riusciti, vedere [Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md). 

La sequenza temporale nella figura seguente è una descrizione dettagliata del processo di monitoraggio dell'endpoint di gestione traffico con le impostazioni seguenti:

* Il protocollo di monitoraggio è HTTP.
* L'intervallo di sondaggio è 30 secondi.
* Il numero di errori tollerati è 3.
* Il valore di timeout è di 10 secondi.
* Il valore TTL DNS è 30 secondi.

![Sequenza di failover e failback degli endpoint di Gestione traffico](./media/traffic-manager-monitoring/timeline.png)

**Figura: Sequenza di failover e ripristino degli endpoint di Gestione traffico**

1. **Ottenere**. Per ogni endpoint, il sistema di monitoraggio di gestione traffico esegue una richiesta GET sul percorso specificato nelle impostazioni di monitoraggio.
2. **200 le impostazioni di monitoraggio del profilo di gestione traffico sono state specificate nell'intervallo di codice personalizzato OK**. Il sistema di monitoraggio prevede che un codice di stato HTTP 200 OK o un codice di stato nell'intervallo specificato nelle impostazioni di monitoraggio venga restituito entro 10 secondi. Alla ricezione della risposta, il sistema riconosce la disponibilità del servizio.
3. **30 secondi tra i controlli**. Il controllo di integrità dell'endpoint viene ripetuto ogni 30 secondi.
4. **Servizio non disponibile**. il servizio diventa non disponibile. Gestione traffico non saprà fino al successivo controllo di integrità.
5. **Tenta di accedere al percorso di monitoraggio**. Il sistema di monitoraggio esegue una richiesta GET, ma non riceve una risposta entro il periodo di timeout di 10 secondi. Il sistema esegue quindi altri tre tentativi a intervalli di 30 secondi. Se uno dei tentativi ha esito positivo, il conteggio viene azzerato.
6. **Stato impostato su Degraded**. al quarto errore consecutivo, il sistema di monitoraggio contrassegna lo stato dell'endpoint non disponibile come Degraded.
7. **Traffico deviato ad altri endpoint**. vengono aggiornati i server dei nomi DNS di Gestione traffico, che non restituisce più l'endpoint in risposta alle query DNS. Le nuove connessioni vengono indirizzate ad altri endpoint disponibili. Tuttavia, le risposte DNS precedenti che includono questo endpoint possono essere ancora memorizzate nella cache da server DNS e client DNS ricorsivi. I client continuano a usare l'endpoint fino alla scadenza della cache DNS. Quando la cache DNS scade i client eseguono nuove query DNS, indirizzate a endpoint diversi. La durata della cache è determinata dall'impostazione TTL definita nel profilo di Gestione traffico, ad esempio 30 secondi.
8. **I controlli di integrità proseguono**. Gestione traffico continua a controllare l'integrità dell'endpoint finché si trova nello stato Degraded. Gestione traffico rileva quando l'endpoint ritorna integro.
9. **Il servizio ritorna online**. il servizio diventa disponibile. L'endpoint mantiene lo stato danneggiato in gestione traffico fino a quando il sistema di monitoraggio esegue il successivo controllo di integrità.
10. **Ripresa del traffico al servizio**. Gestione traffico invia una richiesta GET e riceve una risposta di stato 200 OK, Il servizio è tornato a uno stato integro. I server dei nomi di Gestione traffico vengono aggiornati di nuovo e iniziano a distribuire il nome DNS del servizio nelle risposte DNS. Il traffico torna all'endpoint perché le risposte DNS memorizzate nella cache che restituiscono altri endpoint scadono e che terminano con le connessioni esistenti ad altri endpoint.

    > [!NOTE]
    > Poiché lavora a livello di DNS, Gestione traffico non può influenzare le connessioni esistenti verso qualsiasi endpoint. Quando indirizza il traffico tra gli endpoint, modificando le impostazioni del profilo oppure durante il failover o il failback, Gestione traffico indirizza le nuove connessioni agli endpoint disponibili. Tuttavia, altri endpoint possono continuare a ricevere il traffico tramite le connessioni esistenti finché tali sessioni non vengono terminate. Per consentire lo smaltimento del traffico dalle connessioni esistenti, le applicazioni devono limitare la durata della sessione usata con ogni endpoint.

## <a name="traffic-routing-methods"></a>Metodi di routing del traffico

Quando un endpoint ha uno stato danneggiato, non viene più restituito in risposta alle query DNS. Viene invece scelto e restituito un endpoint alternativo. Il metodo di routing del traffico configurato nel profilo determina il modo in cui viene scelto l'endpoint alternativo.

* **Priorità**. gli endpoint formano un elenco con priorità. Viene sempre restituito il primo endpoint disponibile nell'elenco. Se lo stato di un endpoint è Degraded, viene restituito il successivo endpoint disponibile.
* **Ponderato**. Qualsiasi endpoint disponibile viene scelto in modo casuale in base ai pesi assegnati e ai pesi degli altri endpoint disponibili.
* **Prestazioni**. Viene restituito l'endpoint più vicino all'utente finale. Se l'endpoint non è disponibile, Gestione traffico sposta il traffico sugli endpoint dell'area di Azure più vicina. È possibile configurare piani di failover alternativi per il routing del traffico con il metodo Prestazioni usando [profili di Gestione traffico nidificati](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* Area **geografica**. Viene restituito l'endpoint mappato per fornire la posizione geografica in base all'IP della richiesta di query. Se tale endpoint non è disponibile, non verrà selezionato un altro endpoint per eseguire il failover a, perché una posizione geografica può essere mappata solo a un endpoint in un profilo. Per ulteriori informazioni, consultare le [domande frequenti](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method). Come procedura consigliata, quando si usa il routing geografico, si consiglia ai clienti di usare profili di Gestione traffico annidati con più di un endpoint come endpoint del profilo.
* **MultiValue**. Vengono restituiti più endpoint associati agli indirizzi IPv4 e IPv6. Quando viene ricevuta una query per questo profilo, gli endpoint integri vengono restituiti in base al **numero massimo di record nel valore di risposta** specificato. Il numero predefinito di risposte è due endpoint.
* **Subnet**. Viene restituito l'endpoint mappato a un set di intervalli di indirizzi IP. Quando viene ricevuta una richiesta dall'indirizzo IP, l'endpoint restituito è quello mappato per tale indirizzo IP. 

Per altre informazioni, vedere [Metodi di routing del traffico di Gestione traffico](traffic-manager-routing-methods.md).

> [!NOTE]
> Un'eccezione al normale comportamento del routing del traffico si verifica quando lo stato di tutti gli endpoint idonei risulta Degraded. Gestione traffico effettua un tentativo e *risponde come se tutti gli endpoint con stato Degraded fossero in realtà Online*, una condizione preferibile all'alternativa di non restituire endpoint nella risposta DNS. Gli endpoint disabilitati o arrestati non vengono monitorati, di conseguenza non sono considerati idonei per il traffico.
>
> Questa condizione è in genere causata da una configurazione non corretta del servizio, ad esempio:
>
> * Un elenco di controllo di accesso [ACL] che blocca i controlli di integrità di Gestione traffico.
> * Una configurazione non corretta del protocollo o della porta di monitoraggio nel profilo di Gestione traffico.
>
> La conseguenza di questo comportamento è che se i controlli di integrità di Gestione traffico non sono configurati in modo appropriato, dal routing del traffico potrebbe sembrare che Gestione traffico *funzioni* correttamente. In questo caso il failover degli endpoint non viene tuttavia eseguito, con ripercussioni sulla disponibilità complessiva dell'applicazione. È importante verificare che il profilo indichi lo stato Online e non Degraded. Lo stato Online indica che i controlli di integrità di Gestione traffico funzionano come previsto.

Per altre informazioni su come risolvere i problemi relativi ai controlli di integrità non riusciti, vedere [Risoluzione dei problemi relativi allo stato danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md).

## <a name="faqs"></a>Domande frequenti

* [Gestione traffico è resiliente rispetto agli errori di area di Azure?](./traffic-manager-faqs.md#is-traffic-manager-resilient-to-azure-region-failures)

* [In che modo la scelta della posizione del gruppo di risorse si ripercuote su Gestione traffico?](./traffic-manager-faqs.md#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [Come si determina lo stato di integrità corrente di ogni endpoint?](./traffic-manager-faqs.md#how-do-i-determine-the-current-health-of-each-endpoint)

* [È possibile monitorare gli endpoint HTTPS?](./traffic-manager-faqs.md#can-i-monitor-https-endpoints)

* [Si usa un indirizzo IP o un nome DNS quando si aggiunge un endpoint?](./traffic-manager-faqs.md#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [Quali tipi di indirizzi IP è possibile usare quando si aggiunge un endpoint?](./traffic-manager-faqs.md#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [È possibile usare tipi di indirizzamento diversi per gli endpoint all'interno di un singolo profilo?](./traffic-manager-faqs.md#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [Cosa accade quando il tipo di record di una query in ingresso è diverso dal tipo di record associato al tipo di indirizzamento degli endpoint?](./traffic-manager-faqs.md#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [È possibile usare un profilo con endpoint con indirizzi IPv4 o IPv6 in un profilo annidato?](./traffic-manager-faqs.md#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Ho arrestato un endpoint dell'applicazione Web nel profilo di gestione traffico, ma non ricevo alcun traffico anche dopo il riavvio. Come si può risolvere il problema?](./traffic-manager-faqs.md#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [È possibile utilizzare Gestione traffico anche se l'applicazione non dispone del supporto per HTTP o HTTPS?](./traffic-manager-faqs.md#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [Quali sono le risposte specifiche richieste dall'endpoint quando si usa il monitoraggio TCP?](./traffic-manager-faqs.md#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [Con quale velocità Gestione traffico sposta gli utenti da un endpoint considerato non integro?](./traffic-manager-faqs.md#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [Come specificare diverse impostazioni di monitoraggio per i diversi endpoint in un profilo?](./traffic-manager-faqs.md#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [Come si assegnano le intestazioni HTTP ai controlli di integrità di Gestione traffico per gli endpoint?](./traffic-manager-faqs.md#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [Quali intestazione host viene usata per i controlli di integrità degli endpoint?](./traffic-manager-faqs.md#what-host-header-do-endpoint-health-checks-use)

* [Quali sono gli indirizzi IP da cui si originano i controlli di integrità?](./traffic-manager-faqs.md#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [Qual è il numero di controlli di integrità previsto per un endpoint da parte di Gestione traffico?](./traffic-manager-faqs.md#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [Come si possono ricevere notifiche se uno degli endpoint risulta inattivo?](./traffic-manager-faqs.md#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>Passaggi successivi

Informazioni [sul funzionamento di gestione traffico](traffic-manager-how-it-works.md)

Ulteriori informazioni sui [metodi di routing del traffico](traffic-manager-routing-methods.md) supportati da Gestione traffico

Informazioni su come [creare un profilo di Gestione traffico](traffic-manager-manage-profiles.md)

[Risoluzione dei problemi relativi allo stato Degraded](traffic-manager-troubleshooting-degraded.md) di un endpoint di Gestione traffico