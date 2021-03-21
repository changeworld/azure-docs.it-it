---
title: Gruppo Origin ed Origin in Azure front door standard/Premium
description: Questo articolo descrive il gruppo di origine e di origine che si trova in una configurazione di sportello anteriore di Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 42a9a0ea23faa481140a46ec52b2214431dd723e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100079"
---
# <a name="origin-and-origin-group-in-azure-front-door-standardpremium-preview"></a>Gruppo Origin ed Origin in Azure front door standard/Premium (anteprima)

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Questo articolo illustra i concetti relativi al funzionamento della distribuzione di applicazioni Web con Azure front door standard/Premium. Si apprenderà anche cosa è un gruppo *Origin* e *Origin* nella configurazione di Azure front door standard/Premium.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="origin"></a>Origine

L'origine standard/Premium di Azure front door indica il nome host o l'indirizzo IP pubblico dell'applicazione che fornisce le richieste del client. Azure front door standard/Premium supporta le risorse di Azure e non di Azure in un gruppo di origine. L'applicazione può anche essere ospitata nel Data Center locale o con un altro provider di servizi cloud. L'origine non deve essere confusa con il livello di database o il livello di archiviazione. L'origine deve essere visualizzata come endpoint pubblico per il back-end dell'applicazione. Quando si aggiunge un'origine a un gruppo di origine standard/Premium di Azure front door, è necessario aggiungere anche le informazioni seguenti:

* **Tipo di origine:** Tipo di risorsa che si desidera aggiungere. Lo sportello anteriore supporta l'individuazione automatica dei backend dell'applicazione dal servizio app, dal servizio cloud o dall'archiviazione. Se si vuole una risorsa diversa in Azure o anche in un back-end non di Azure, selezionare **host personalizzato**.

    >[!IMPORTANT]
    >Durante la configurazione, le API non vengono convalidate se l'origine non è accessibile dall'ambiente della porta anteriore. Assicurarsi che la porta anteriore possa raggiungere l'origine.

* **Nome host di sottoscrizione e di origine:** Se non è stato selezionato **host personalizzato** per il tipo di host back-end, selezionare il back-end scegliendo la sottoscrizione appropriata e il nome host back-end corrispondente.

* **Intestazione host di origine:** Valore dell'intestazione host inviato al back-end per ogni richiesta. Per altre informazioni, vedere [intestazione host di origine](#hostheader).

* **Priorità:** Assegnare le priorità alle diverse origini quando si vuole usare un'origine primaria per tutto il traffico. Inoltre, fornire backup se le origini primarie o di backup non sono disponibili. Per altre informazioni, vedere [Priority](#priority).

* **Peso:** Assegnare i pesi alle diverse origini per distribuire il traffico in un set di origini, in modo uniforme o in base ai coefficienti di peso. Per ulteriori informazioni, vedere [weights](#weighted).

### <a name="origin-host-header"></a><a name = "hostheader"></a>Intestazione host di origine

Le richieste che vengono inviate da Azure front door standard/Premium a un'origine includeranno un campo di intestazione host che l'origine USA per recuperare la risorsa di destinazione. Il valore di questo campo deriva in genere dall'URI di origine con l'intestazione e la porta dell'host.

Ad esempio, una richiesta effettuata per `www.contoso.com` avrà l'intestazione host `www.contoso.com` . Se si usa portale di Azure per configurare l'origine, il valore predefinito per questo campo è il nome host del back-end. Se l'origine è `contoso-westus.azurewebsites.net` , nella portale di Azure il valore popolato automaticamente per l'intestazione dell'host di origine sarà `contoso-westus.azurewebsites.net` . Tuttavia, se si usano Azure Resource Manager modelli o un altro metodo senza impostare in modo esplicito questo campo, lo sportello anteriore invierà il nome host in ingresso come valore per l'intestazione host. Se la richiesta è stata effettuata per `www.contoso.com` e l'origine è `contoso-westus.azurewebsites.net` dotata di un campo di intestazione vuoto, l'intestazione host verrà impostata come `www.contoso.com` .

La maggior parte dei back-end dell'app (app Web di Azure, archiviazione BLOB e servizi cloud) richiede che l'intestazione host corrisponda al dominio del back-end. Tuttavia, l'host front-end che instrada al back-end utilizzerà un nome host diverso, ad esempio `www.contoso.net` .

Se l'origine richiede che l'intestazione host corrisponda al nome host del back-end, assicurarsi che l'intestazione host backend includa il nome host del back-end.

#### <a name="configuring-the-origin-host-header-for-the-origin"></a>Configurazione dell'intestazione dell'host di origine per l'origine

Per configurare il campo di **intestazione dell'host di origine** per un'origine nella sezione gruppo di origine:

1. Aprire la risorsa front door e selezionare il gruppo Origin con l'origine da configurare.

2. Aggiungere un'origine, se non è già stata eseguita, o modificarne una esistente.

3. Impostare il campo intestazione host di origine su un valore personalizzato o lasciarlo vuoto. Il nome host per la richiesta in ingresso verrà usato come valore dell'intestazione host.

## <a name="origin-group"></a>Gruppo di origine

Un gruppo di origine in Azure front door standard/Premium si riferisce al set di origini che riceve traffico simile per la propria applicazione. In altre parole, si tratta di un raggruppamento logico di istanze dell'applicazione in tutto il mondo che ricevono lo stesso traffico e rispondono con un comportamento previsto. Queste origini possono essere distribuite in aree diverse o all'interno della stessa area. Tutte le origini possono essere in modalità di distribuzione attiva/attiva o definire una configurazione attiva/passiva.

Un gruppo di origine definisce il modo in cui le origini devono essere valutate tramite probe di integrità. Definisce anche il modo in cui viene eseguito il bilanciamento del carico tra di essi.

### <a name="health-probes"></a>Probe di integrità

Azure front door standard/Premium invia richieste di probe HTTP/HTTPS periodiche a ognuna delle origini configurate. Le richieste di probe determinano la vicinanza e l'integrità di ogni origine per il bilanciamento del carico delle richieste degli utenti finali. Le impostazioni del probe di integrità per un gruppo di origine definiscono come viene eseguito il polling dello stato di integrità dei backend dell'app. Per la configurazione del bilanciamento del carico sono disponibili le impostazioni seguenti:

* **Path**: URL usato per le richieste di probe per tutte le origini nel gruppo di origine. Ad esempio, se una delle origini è `contoso-westus.azurewebsites.net` e il percorso viene impostato su/probe/test.aspx, gli ambienti front-end, supponendo che il protocollo sia http, invierà richieste di probe di integrità a `http://contoso-westus.azurewebsites.net/probe/test.aspx` .

* **Protocollo**: definisce se inviare le richieste di probe di integrità dalla porta anteriore alle origini con il protocollo http o HTTPS.

* **Metodo**: metodo HTTP da usare per l'invio di probe di integrità. Le opzioni includono GET o HEAD (impostazione predefinita).
    > [!NOTE]
    > Per un carico inferiore e un costo sui backend, la porta anteriore consiglia di usare le richieste HEAD per i probe di integrità.

* **Intervallo (secondi)**: definisce la frequenza di probe di integrità per le origini o gli intervalli in cui ognuno degli ambienti della porta anteriore Invia un probe.

    >[!NOTE]
    >Per un failover più veloce, impostare l'intervallo su un valore inferiore. Più basso è il valore, maggiore è il volume del probe di integrità che i backend ricevono. Se, ad esempio, l'intervallo è impostato su 30 secondi, ad esempio, 100 la porta anteriore viene visualizzata a livello globale, ogni back-end riceverà circa 200 richieste di probe al minuto.

Per altre informazioni, vedere [Probe di integrità](concept-health-probes.md).

### <a name="load-balancing-settings"></a>Impostazioni di bilanciamento del carico

Le impostazioni di bilanciamento del carico per il gruppo Origin definiscono il modo in cui vengono valutati i probe di integrità. Queste impostazioni determinano se l'origine è integro o non integro. Viene inoltre controllato come bilanciare il carico del traffico tra origini diverse nel gruppo di origine. Per la configurazione del bilanciamento del carico sono disponibili le impostazioni seguenti:

* **Dimensioni campione:** Identifica il numero di campioni di probe di integrità che è necessario considerare per la valutazione dell'integrità di origine.

* **Dimensioni campione riuscite:** Definisce le dimensioni del campione come indicato in precedenza, il numero di campioni riusciti necessari per chiamare l'origine integro. Si supponga ad esempio che un intervallo di probe di integrità della porta anteriore sia 30 secondi, che le dimensioni del campione siano pari a 5 e che le dimensioni del campione siano 3. Ogni volta che si valutano i probe di integrità per l'origine, si esaminano gli ultimi cinque campioni oltre 150 secondi (5 x 30). Per dichiarare l'origine come integro sono necessari almeno tre Probe con esito positivo.

* **Sensibilità alla latenza (latenza aggiuntiva):** Definisce se si vuole che il front-end di Azure standard/Premium invii la richiesta all'origine entro l'intervallo di sensibilità della misurazione della latenza o inoltra la richiesta al back-end più vicino.

Per ulteriori informazioni, vedere [metodo di routing con minore latenza](#latency).

## <a name="routing-methods"></a>Metodi di routing

Azure front door standard/Premium supporta diversi tipi di metodi di routing del traffico per determinare come indirizzare il traffico HTTP/HTTPS a diversi endpoint di servizio. Quando il client richiede il raggiungimento della porta anteriore, viene applicato il metodo di routing configurato per assicurarsi che le richieste vengano inviate alla migliore istanza back-end. 

Sono disponibili quattro metodi di routing del traffico in Azure front door standard/Premium:

* **[Latenza](#latency):** il routing basato sulla latenza assicura che le richieste vengano inviate al back-end con la latenza più bassa accettabile all'interno di un intervallo di sensibilità. In pratica, le richieste degli utenti vengono inviate al set "più vicino" di backend rispetto alla latenza di rete.
* **[Priorità](#priority):** È possibile assegnare le priorità ai back-end quando si vuole configurare un back-end primario per il servizio di tutto il traffico. Il back-end secondario può essere un backup nel caso in cui il back-end primario diventi non disponibile.
* **[Ponderato](#weighted):** È possibile assegnare pesi ai backend quando si vuole distribuire il traffico in un set di backend. Se si desidera distribuire uniformemente o in base ai coefficienti di peso.

Tutte le configurazioni standard e Premium di Azure front door includono il monitoraggio dell'integrità back-end e il failover globale istantaneo automatico. Per ulteriori informazioni, vedere [monitoraggio back-end](concept-health-probes.md). La porta anteriore può funzionare in base a un singolo metodo di routing. Tuttavia, a seconda delle esigenze dell'applicazione, è anche possibile combinare più metodi di routing per creare una topologia di routing ottimale.

### <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Routing del traffico in base alla latenza più bassa

La distribuzione di backend in due o più posizioni in tutto il mondo può migliorare la velocità di risposta delle applicazioni indirizzando il traffico verso la destinazione più vicina agli utenti finali. Il metodo di routing del traffico predefinito per la configurazione della porta anteriore Invia le richieste dagli utenti finali al back-end più vicino dell'ambiente di sportello anteriore che ha ricevuto la richiesta. In combinazione con l'architettura Anycast di Azure front door, questo approccio garantisce che ogni utente finale ottenga le massime prestazioni personalizzate in base alla propria posizione.

Il back-end "più vicino" non è necessariamente più vicino a quanto misurato in base alla distanza geografica. Frontdoor determina invece il back-end più vicino misurando la latenza di rete.

Di seguito è illustrato il flusso complessivo delle decisioni:

| Back-end disponibili | Priorità | Segnale di latenza (basato sui probe di integrità) | Pesi |
|-------------| ----------- | ----------- | ----------- |
| Per prima cosa, selezionare tutti i backend abilitati e restituiti integri (200 OK) per il probe di integrità. Se sono presenti sei backend A, B, C, D, e e F e tra di essi C non è integro e e è disabilitato. L'elenco dei backend disponibili è A, B, D e F.  | Successivamente, vengono selezionati i backend con priorità superiore tra quelli disponibili. Se il back-end A, B e D hanno priorità 1 e il back-end F ha una priorità pari a 2. Quindi, i backend selezionati saranno A, B e D.| Selezionare i back-end con l'intervallo di latenza (latenza inferiore e sensibilità di latenza in ms specificata). Se il back-end A è 15 ms, B è 30 ms e D è 60 MS dall'ambiente della porta anteriore in cui la richiesta è stata sbarcata e la sensibilità di latenza è 30 ms, il pool di latenza più bassa è costituito da back-end A e B, perché D supera 30 ms dal back-end più vicino che è un. | Infine, Frontdoor eseguirà il round robin del traffico tra il pool selezionato finale di back-end nel rapporto di pesi specificati. Se il back-end A ha un peso di 5 e il back-end B ha un peso di 8, il traffico verrà distribuito in un rapporto di 5:8 tra i back-end A e B. |

>[!NOTE]
> Per impostazione predefinita, la proprietà di sensibilità di latenza è impostata su 0 ms, ovvero inoltrare sempre la richiesta al back-end più veloce disponibile.

### <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Routing del traffico basato sulla priorità

Spesso un'organizzazione vuole offrire la disponibilità elevata per i servizi distribuendo più di un servizio di backup nel caso in cui il database primario si arresti. A livello di settore, questa topologia è anche denominata topologia di distribuzione attiva/standby o attiva/passiva. Il metodo di routing del traffico "Priorità" consente ai clienti di Azure di implementare facilmente questo modello di failover.

Il sistema Frontdoor predefinito contiene un elenco con uguale priorità dei back-end. Per impostazione predefinita, Frontdoor invia il traffico solo ai back-end con la massima priorità (il valore più basso per la priorità), ovvero il set primario di back-end. Se i backend primari non sono disponibili, la porta anteriore instrada il traffico al set secondario di backend (secondo valore più basso per priorità). Se i backend primario e secondario non sono disponibili, il traffico viene indirizzato al terzo e così via. La disponibilità del back-end si basa sullo stato configurato (abilitato o disabilitato) e lo stato di integrità del back-end, determinato continuamente tramite i probe di integrità.

#### <a name="configuring-priority-for-backends"></a>Configurazione della priorità per i back-end

Ogni back-end nel pool back-end della configurazione della porta anteriore ha una proprietà denominata "Priority", che può essere un numero compreso tra 1 e 5. Con Azure front door è possibile configurare la priorità di back-end in modo esplicito usando questa proprietà per ogni back-end. Questa proprietà accetta un valore compreso tra 1 e 5, dove i valori più bassi rappresentano una priorità più elevata. I back-end possono condividere i valori di priorità.

### <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Metodo di routing del traffico Ponderato
Il metodo di routing del traffico "Ponderato" consente di distribuire il traffico tra tutti gli endpoint in modo uniforme o con un peso predefinito.

Nel metodo di routing del traffico "Ponderato" viene assegnato un peso a ogni back-end nella configurazione di Frontdoor del pool back-end. Ogni peso è un numero intero compreso tra 1 e 1000. Questo parametro usa un peso predefinito pari a "50".

Con l'elenco dei backend disponibili con una sensibilità di latenza accettabile, il traffico viene distribuito con un meccanismo Round Robin usando il rapporto tra i pesi specificati. Se la sensibilità di latenza viene impostata su 0 millisecondi, questa proprietà non viene applicata a meno che non siano presenti due backend con la stessa latenza di rete. 

Il metodo "Ponderato" abilita alcuni scenari utili:

* **Aggiornamento graduale dell'applicazione**: fornisce una percentuale di traffico da indirizzare a un nuovo back-end e aumenta gradualmente il traffico nel tempo per portarlo alla parità con altri back-end.
* **Migrazione dell'applicazione in Azure**: creare un pool back-end con back-end di Azure ed esterni. Regolare il peso dei back-end per preferire i nuovi back-end. È possibile applicare questa configurazione gradualmente, iniziando con i nuovi back-end disabilitati, quindi assegnando loro i pesi più bassi e aumentandoli lentamente fino ai livelli di massimo traffico. È infine possibile disabilitare i back-end con preferenza inferiore e rimuoverli dal pool.  
* **Espansione del cloud per capacità aggiuntiva**: espandere rapidamente una distribuzione locale nel cloud posizionandola dietro Frontdoor. In caso di necessità di capacità aggiuntiva nel cloud, si possono aggiungere o abilitare più back-end e si può specificare la quantità di traffico da indirizzare a ogni back-end.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare un front-end standard/Premium](create-front-door-portal.md)
