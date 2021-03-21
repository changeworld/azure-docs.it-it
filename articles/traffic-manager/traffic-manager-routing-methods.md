---
title: Metodi di routing del traffico di Gestione traffico di Azure
description: Questo articolo consente di comprendere i diversi metodi di routing del traffico usati da Gestione traffico
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: eeebded128f636ecba2e4e0dab1bc2f0632aaa6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730973"
---
# <a name="traffic-manager-routing-methods"></a>Metodi di routing di Gestione traffico

Gestione traffico di Azure supporta sei metodi di routing del traffico per determinare la modalità di instradamento del traffico di rete ai vari endpoint di servizio. Gestione traffico applica il metodo di routing del traffico associato a qualsiasi profilo a ogni query DNS ricevuta. Il metodo di routing del traffico determina l'endpoint restituito nella risposta DNS.

In Gestione traffico sono disponibili i metodi di routing del traffico seguenti:

* **[Priorità](#priority-traffic-routing-method):** selezionare routing **priorità** quando si vuole avere un endpoint di servizio primario per tutto il traffico. È possibile fornire più endpoint di backup nel caso in cui il database primario o uno degli endpoint di backup non sia disponibile.
* **[Ponderato](#weighted):** Selezionare il routing **ponderato** quando si vuole distribuire il traffico in un set di endpoint in base al peso. Impostare il peso sullo stesso per distribuirlo in modo uniforme tra tutti gli endpoint.
* **[Prestazioni](#performance):** selezionare routing **prestazioni** quando gli endpoint si trovano in aree geografiche diverse e si vuole che gli utenti finali usino l'endpoint "più vicino" per la latenza di rete più bassa.
* **[Geografico](#geographic):** Selezionare il routing **geografico** per indirizzare gli utenti a endpoint specifici (Azure, esterno o annidato) in base alla posizione geografica delle query DNS. Con questo metodo di routing, è possibile essere in conformità con scenari quali i requisiti di sovranità dei dati, la localizzazione del contenuto & esperienza utente e la misurazione del traffico da aree diverse.
* **[Multivalore](#multivalue):** selezionare **Multivalore** per i profili di Gestione traffico che possono avere come endpoint solo indirizzi IPv4/IPv6. Quando viene ricevuta una query per profili di questo tipo, vengono restituiti tutti gli endpoint integri.
* **[Subnet](#subnet):** Selezionare il metodo di routing del traffico della **subnet** per eseguire il mapping di set di intervalli di indirizzi IP dell'utente finale a un endpoint specifico. Al ricevimento di una richiesta, verrà restituito l'endpoint mappato per l'indirizzo IP di origine della richiesta. 


Tutti i profili di gestione traffico hanno il monitoraggio dello stato e il failover automatico degli endpoint. Per altre informazioni, vedere [Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md). All'interno di un profilo di gestione traffico è possibile configurare un solo metodo di routing del traffico alla volta. È possibile selezionare in qualsiasi momento un metodo di routing del traffico diverso per il profilo. Le modifiche verranno applicate entro un minuto senza tempi di inattività. È possibile combinare i metodi di routing del traffico usando i profili annidati di gestione traffico. I profili di annidamento consentono configurazioni di routing del traffico sofisticate che soddisfano le esigenze di applicazioni più grandi e complesse. Per altre informazioni, vedere [nested Traffic Manager profiles](traffic-manager-nested-profiles.md)(Profili nidificati di Gestione traffico).

## <a name="priority-traffic-routing-method"></a>Metodo di routing del traffico Priorità

Spesso un'organizzazione vuole fornire affidabilità per i servizi. A tale scopo, distribuiscono uno o più servizi di backup nel caso in cui il database primario diventi inattivo. Il metodo di routing del traffico "Priorità" consente ai clienti di Azure di implementare facilmente questo modello di failover.

![Metodo di routing del traffico "Priorità" di Gestione traffico di Azure](media/traffic-manager-routing-methods/priority.png)

Il profilo di Gestione traffico contiene un elenco di endpoint del servizio con diverse priorità. Per impostazione predefinita, tutto il traffico viene inviato all'endpoint primario (con priorità più elevata). Se l'endpoint primario non è disponibile, Gestione traffico indirizza il traffico verso il secondo endpoint. In una situazione in cui gli endpoint primario e secondario non sono disponibili, il traffico viene indirizzato al terzo e così via. La disponibilità dell'endpoint si basa sullo stato configurato (abilitato o disabilitato) e sul monitoraggio degli endpoint in corso.

### <a name="configuring-endpoints"></a>Configurazione degli endpoint

In Azure Resource Manager la priorità degli endpoint viene configurata in modo esplicito, definendo la proprietà "priority" di ogni endpoint. Questa proprietà accetta un valore compreso tra 1 e 1000, Un valore inferiore rappresenta una priorità più alta. Gli endpoint non possono condividere i valori di priorità. La proprietà è facoltativa e, se viene omessa, viene usata una priorità predefinita in base all'ordine degli endpoint.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Metodo di routing del traffico Ponderato
Il metodo di routing del traffico "Ponderato" consente di distribuire il traffico tra tutti gli endpoint in modo uniforme o con un peso predefinito.

![Metodo di routing del traffico "Ponderato" di Gestione traffico di Azure](media/traffic-manager-routing-methods/weighted.png)

Nel metodo di routing del traffico "Ponderato" viene assegnato un peso a ogni endpoint come parte della configurazione del profilo di Gestione traffico. Ogni peso è un numero intero compreso tra 1 e 1000. Questo parametro è facoltativo. se omesso, viene usato il valore di peso predefinito "1". Maggiore è il peso, maggiore è la priorità.

Per ogni query DNS ricevuta, Gestione traffico sceglie casualmente un endpoint disponibile. La probabilità di scelta di un determinato endpoint dipende dai pesi assegnati a tutti gli endpoint disponibili. L'uso dello stesso peso in tutti gli endpoint comporta una distribuzione uniforme del traffico. Se vengono usati pesi superiori o inferiori in specifici endpoint, questi verranno restituiti più o meno frequentemente nelle risposte DNS.

Il metodo "Ponderato" abilita alcuni scenari utili:

* Aggiornamento graduale dell'applicazione: data una percentuale di traffico da indirizzare a un nuovo endpoint e aumentare gradualmente il traffico nel tempo al 100%.
* Migrazione dell'applicazione in Azure: creare un profilo con endpoint di Azure ed esterni. Regolare il peso degli endpoint per preferire i nuovi endpoint.
* Espansione del cloud per aumentare la capacità: espandere rapidamente una distribuzione locale nel cloud inserendola dietro un profilo di gestione traffico. In caso di necessità di capacità aggiuntiva nel cloud, si possono aggiungere o abilitare più endpoint e si può specificare la quantità di traffico da indirizzare a ogni endpoint.

È possibile configurare i pesi usando il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando o le API REST.

Un punto da ricordare è che le risposte DNS vengono memorizzate nella cache dai client. Vengono inoltre memorizzati nella cache dai server DNS ricorsivi utilizzati dai client per risolvere i nomi DNS. Questa memorizzazione nella cache può influire sulle distribuzioni del traffico ponderato. Se il numero di client e di server DNS ricorsivi è elevato, la distribuzione del traffico funziona come previsto. Se invece il numero di client o di server DNS ricorsivi è limitato, la memorizzazione nella cache può modificare significativamente la distribuzione del traffico.

I casi d'uso comuni in cui può verificarsi questa situazione includono:

* Ambienti di sviluppo e test
* Comunicazioni tra applicazioni
* Applicazioni destinate a una base utenti ristretta che condivide un'infrastruttura DNS ricorsiva comune, ad esempio i dipendenti di un'organizzazione che si connettono tramite proxy.

Questi effetti della memorizzazione nella cache DNS non riguardano solo Gestione traffico di Azure, ma sono comuni a tutti i sistemi di routing del traffico basati su DNS. In alcuni casi, la cancellazione della cache DNS in modo esplicito può risolvere il problema. Se il problema persiste, è possibile che un metodo di routing del traffico alternativo risulti più appropriato.

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>Metodo di routing del traffico Prestazioni

La distribuzione di endpoint in due o più posizioni in tutto il mondo può migliorare la velocità di risposta delle applicazioni. Con il metodo di routing del traffico "prestazioni", è possibile instradare il traffico alla località più vicina.

![Metodo di routing del traffico "Prestazioni" di Gestione traffico di Azure](media/traffic-manager-routing-methods/performance.png)

L'endpoint "più vicino" non è necessariamente più vicino a quanto misurato in base alla distanza geografica. Il metodo di routing del traffico "Prestazioni" determina invece l'endpoint più vicino in termini di latenza di rete. La tabella della latenza di Internet indica il tempo di round trip tra intervalli di indirizzi IP e ciascun data center di Azure.

Gestione traffico individua nella tabella la riga relativa all'indirizzo IP di origine della richiesta DNS in ingresso Gestione traffico sceglie quindi un endpoint disponibile nel Data Center di Azure con la latenza più bassa per l'intervallo di indirizzi IP. Quindi Traffic Manager restituisce l'endpoint nella risposta DNS.

Come illustrato nel [funzionamento di gestione traffico](traffic-manager-how-it-works.md), gestione traffico non riceve query DNS direttamente dai client. Al contrario, le query DNS provengono dal servizio DNS ricorsivo configurato per l'utilizzo da parte dei client. Di conseguenza, l'indirizzo IP usato per determinare l'endpoint "più vicino" non è l'indirizzo IP del client, ma si tratta dell'indirizzo IP del servizio DNS ricorsivo. Questo indirizzo IP è un proxy valido per il client.


Per adeguarsi alle modifiche nella rete Internet globale e all'aggiunta di nuove aree di Azure, Gestione traffico aggiorna regolarmente la tabella della latenza di Internet usata. Tuttavia, le prestazioni dell'applicazione variano in base alle variazioni del carico in tempo reale registrate in Internet. Il routing del traffico delle prestazioni non monitora il carico su un determinato endpoint di servizio. Se un endpoint diventa non disponibile, gestione traffico non lo includerà nelle risposte alle query DNS.


Punti da notare:

* Se il profilo contiene più endpoint nella stessa area di Azure, il traffico indirizzato all'area in questione viene distribuito in modo uniforme tra gli endpoint disponibili nell'area. Se si preferisce una distribuzione del traffico diversa all'interno di un'area, usare i [profili annidati di Gestione traffico](traffic-manager-nested-profiles.md).
* Se tutti gli endpoint abilitati nell'area di Azure più vicina sono danneggiati, Gestione traffico sposta il traffico sugli endpoint nella successiva area di Azure più vicina. Se si preferisce definire una sequenza di failover, usare i [profili annidati di Gestione traffico](traffic-manager-nested-profiles.md).
* Quando si usa il metodo di routing del traffico "Prestazioni" con endpoint esterni o endpoint annidati, è necessario specificare la posizione di tali endpoint. Scegliere l'area di Azure più vicina alla distribuzione specifica. Si tratta delle posizioni supportate dalla tabella della latenza di Internet.
* L'algoritmo che sceglie l'endpoint è deterministico. Le query DNS ripetute dallo stesso client vengono indirizzate allo stesso endpoint. Quando viaggiano, i client solitamente usano server DNS ricorsivi diversi, e possono pertanto essere indirizzati a un endpoint differente. Il routing potrebbe inoltre risentire degli aggiornamenti della tabella della latenza di Internet. Questo è il motivo per cui il metodo di routing del traffico delle prestazioni non garantisce che un client venga sempre indirizzato allo stesso endpoint.
* Quando viene aggiornata la tabella della latenza di Internet, è possibile notare che alcuni client vengono indirizzati a un endpoint diverso. Questa modifica del routing è più precisa in base ai dati di latenza correnti. Questi aggiornamenti sono essenziali per garantire la precisione del routing del traffico Prestazioni man mano che Internet si evolve.

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>Metodo di routing del traffico Geografico

I profili di gestione traffico possono essere configurati per usare il metodo di routing geografico in modo che gli utenti vengano indirizzati a endpoint specifici (Azure, esterno o annidato) in base alla posizione geografica da cui ha origine la query DNS. Con questo metodo di routing, è possibile rispettare i requisiti di sovranità dei dati, la localizzazione del contenuto & esperienza utente e la misurazione del traffico da aree diverse.
Quando un profilo è configurato per il routing geografico, ogni endpoint associato al profilo deve avere un set di aree geografiche assegnate. Un'area geografica può avere i seguenti livelli di granularità 
- Mondo: qualsiasi area
- Raggruppamento di aree: ad esempio Africa, Medio Oriente, Australia/Pacifico e così via. 
- Paese/area geografica: ad esempio Irlanda, Perù, Regione amministrativa speciale di Hong Kong e così via. 
- Stato/Provincia: ad esempio Stati Uniti-California, Australia-Queensland, Canada-Alberta e così via. Si noti che questo livello di granularità è supportato solo per gli stati e/o le province di Australia, Canada e Stati Uniti.

Quando un'area o un set di aree viene assegnato a un endpoint, tutte le richieste provenienti da tali aree vengono instradate solo a tale endpoint. Gestione traffico USA l'indirizzo IP di origine della query DNS per determinare l'area da cui un utente sta eseguendo una query. Comunemente trovato come indirizzo IP del resolver DNS locale che esegue la query per l'utente.  

![Metodo geografico di routing del traffico di Gestione traffico di Azure](./media/traffic-manager-routing-methods/geographic.png)

Gestione traffico legge l'indirizzo IP di origine della query DNS e decide da quale area geografica proviene. Verifica quindi se esiste un endpoint a cui è mappata questa area geografica. Questa ricerca inizia con il livello di granularità più basso (stato/provincia dove è supportato, altrimenti a livello di paese/area geografica) e arriva fino al livello più alto, ovvero il **mondo**. La prima corrispondenza trovata usando questo attraversamento viene scelta come endpoint da restituire nella risposta alla query. In caso di corrispondenza con un endpoint di tipo nidificato, viene restituito un endpoint all'interno di tale profilo figlio, in base al suo metodo di routing. I punti seguenti sono applicabili a questo comportamento:

- Un'area geografica può essere mappata a un solo endpoint in un profilo di Gestione traffico quando il tipo di routing è quello geografico. Questa restrizione garantisce che il routing degli utenti sia deterministico e i clienti possano abilitare scenari che richiedono limiti geografici non ambigui.
- Se l'area geografica di un utente è elencata nel mapping geografico di due endpoint diversi, gestione traffico seleziona l'endpoint con la granularità più bassa. Gestione traffico non prenderà in considerazione il routing delle richieste da tale area all'altro endpoint. Ad esempio, si consideri un profilo con il tipo di routing geografico e con due endpoint: Endpoint1 ed Endpoint2. L'Endpoint1 è configurato per ricevere il traffico dall'Irlanda e l'Endpoint2 è configurato per ricevere il traffico dall'Europa. Se una richiesta proviene dall'Irlanda, viene sempre indirizzata a endpoint1.
- Poiché un'area può essere mappata a un solo endpoint, gestione traffico restituisce una risposta se l'endpoint è integro o meno.

    >[!IMPORTANT]
    >È consigliabile che i clienti usino il metodo di routing geografico associato con endpoint di tipo nidificato e profili figlio contenenti almeno due endpoint ciascuno.
- Se viene trovata una corrispondenza di endpoint e l'endpoint è nello stato **Arrestato**, Gestione traffico restituisce una risposta NODATA. In questo caso, non vengono eseguite ulteriori ricerche più in alto nella gerarchia dell'area geografica. Questo comportamento è applicabile anche per i tipi di endpoint annidati quando il profilo figlio è nello stato **Arrestato** o **Disabilitato**.
- Se un endpoint si trova nello stato **Disabilitato**, non sarà incluso nel processo di ricerca di corrispondenza dell'area. Questo comportamento si applica anche per i tipi di endpoint annidati quando l'endpoint si trova nello stato **Disabilitato**.
- Se una query proviene da un'area geografica che non ha alcun mapping in quel profilo, Gestione traffico restituisce una risposta NODATA. Per questo motivo è consigliabile usare il routing geografico con un endpoint. Idealmente di tipo annidato con almeno due endpoint all'interno del profilo figlio, con l'area **mondo** assegnata. Questa configurazione assicura anche che vengano gestiti tutti gli indirizzi IP che non sono mappati a un'area.

Come illustrato nel [funzionamento di gestione traffico](traffic-manager-how-it-works.md), gestione traffico non riceve query DNS direttamente dai client. Le query DNS provengono dal servizio DNS ricorsivo configurato per l'uso da parte dei client. Questo è il motivo per cui l'indirizzo IP usato per determinare l'area non è l'indirizzo IP del client, ma piuttosto l'indirizzo IP del servizio DNS ricorsivo. Questo indirizzo IP è un proxy valido per il client.

### <a name="faqs"></a>Domande frequenti

* [Quali sono alcuni casi di uso in cui il routing geografico è utile?](./traffic-manager-faqs.md#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Come si decide se è consigliabile usare il metodo di routing relativo alle prestazioni o geografico?](./traffic-manager-faqs.md#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Quali sono le aree supportate da Gestione traffico per il routing geografico?](./traffic-manager-faqs.md#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [In che modo Gestione traffico determina da dove un utente sta eseguendo una query?](./traffic-manager-faqs.md#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [È garantito che in ogni caso Gestione traffico determini correttamente l'esatta posizione geografica dell'utente?](./traffic-manager-faqs.md#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Un endpoint deve trovarsi fisicamente nella stessa area in cui è configurato per il routing geografico?](./traffic-manager-faqs.md#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [È possibile assegnare aree geografiche agli endpoint in un profilo che non è configurato per eseguire il routing geografico?](./traffic-manager-faqs.md#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Perché si riceve un errore quando si tenta di cambiare il metodo di routing di un profilo esistente in geografico?](./traffic-manager-faqs.md#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Perché è decisamente consigliato che i clienti creino profili nidificati anziché endpoint in un profilo con il routing geografico abilitato?](./traffic-manager-faqs.md#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [Ci sono restrizioni sulla versione API che supporta questo tipo di routing?](./traffic-manager-faqs.md#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>Metodo di routing del traffico Multivalore
Il metodo di routing del traffico **Multivalore** consente di ottenere più endpoint integri in un'unica risposta a una query DNS. Questa configurazione consente al chiamante di eseguire tentativi sul lato client con altri endpoint nel caso in cui un endpoint restituito non risponda. Questo modello può aumentare la disponibilità di un servizio e ridurre la latenza associata a una nuova query DNS per ottenere un endpoint integro. Il metodo di routing Multivalore funziona solo se tutti gli endpoint di tipo Esterno sono specificati come indirizzi IPv4 o IPv6. Quando si riceve una query per profili di questo tipo, tutti gli endpoint integri vengono restituiti e sono soggetti a un numero massimo di restituzioni configurabili.

### <a name="faqs"></a>Domande frequenti

* [Quali sono alcuni casi di uso in cui il routing multivalore è utile?](./traffic-manager-faqs.md#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Quanti endpoint vengono restituiti quando si usa il routing multivalore?](./traffic-manager-faqs.md#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Si riceve lo stesso set di endpoint quando si usa il routing multivalore?](./traffic-manager-faqs.md#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>Metodo di routing del traffico Subnet
Il metodo di routing del traffico della **subnet** consente di eseguire il mapping di un set di intervalli di indirizzi IP degli utenti finali a endpoint specifici di un profilo. Se Traffic Manager riceve una query DNS per quel profilo, analizzerà l'indirizzo IP di origine della richiesta. Verrà quindi determinato l'endpoint a cui è stato eseguito il mapping e verrà restituito tale endpoint nella risposta alla query. Nella maggior parte dei casi, l'indirizzo IP di origine è il resolver DNS usato dal chiamante.

È possibile specificare l'indirizzo IP di cui eseguire il mapping a un endpoint come intervalli CIDR (ad esempio, 1.2.3.0/24) o come intervallo di indirizzi (ad esempio, 1.2.3.4-5.6.7.8). Gli intervalli IP associati a un endpoint devono essere univoci all'interno del profilo. L'intervallo di indirizzi non può sovrapporsi al set di indirizzi IP di un endpoint diverso nello stesso profilo.
Se si definisce un endpoint senza intervallo di indirizzi, questo funziona come fallback e accetta il traffico da qualsiasi subnet rimanente. Se non è incluso alcun endpoint di fallback, Gestione traffico invia una risposta NODATA per tutti gli intervalli non definiti. È consigliabile definire un endpoint di fallback per assicurarsi che tutti gli intervalli IP possibili siano specificati negli endpoint.

È possibile usare il metodo di routing Subnet per offrire un'esperienza diversa per gli utenti che si connettono da uno spazio di IP specifico. È ad esempio possibile fare in modo che tutte le richieste dall'ufficio aziendale siano indirizzate a un endpoint diverso. Questo metodo di routing è particolarmente utile se si sta provando a testare una versione solo interna dell'app. Un altro scenario può essere quello in cui si voglia offrire un'esperienza diversa agli utenti che si connettono da un ISP specifico, ad esempio per bloccare gli utenti di un determinato ISP.

### <a name="faqs"></a>Domande frequenti

* [Quali sono alcuni casi di uso in cui il routing della subnet è utile?](./traffic-manager-faqs.md#what-are-some-use-cases-where-subnet-routing-is-useful)

* [In che modo Gestione traffico riconosce l'indirizzo IP dell'utente finale?](./traffic-manager-faqs.md#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Come si possono specificare gli indirizzi IP quando si usa il routing Subnet?](./traffic-manager-faqs.md#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Come si può specificare un endpoint di fallback quando si usa il routing Subnet?](./traffic-manager-faqs.md#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [Cosa accade se un endpoint è disabilitato in un profilo con routing Subnet?](./traffic-manager-faqs.md#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come sviluppare applicazioni a disponibilità elevata tramite il [monitoraggio degli endpoint di gestione traffico](traffic-manager-monitoring.md)