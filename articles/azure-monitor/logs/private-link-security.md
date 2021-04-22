---
title: Usare il collegamento privato di Azure per connettere in modo sicuro le reti a Monitoraggio di Azure
description: Usare il collegamento privato di Azure per connettere in modo sicuro le reti a Monitoraggio di Azure
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: b3bbd8510c47831c9568bb10c62e2a5ca751902a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863030"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Usare il collegamento privato di Azure per connettere in modo sicuro le reti a Monitoraggio di Azure

[Collegamento privato di Azure](../../private-link/private-link-overview.md) consente di collegare in modo sicuro i servizi PaaS di Azure alla rete virtuale usando endpoint privati. Per molti servizi, è sufficiente configurare un endpoint per ogni risorsa. Tuttavia, Monitoraggio di Azure è un insieme di diversi servizi interconnessi che interagiscono tra loro per monitorare i carichi di lavoro. Di conseguenza, è stata creata una risorsa denominata Monitoraggio di Azure Ambito collegamento privato (AMPLS). AMPLS consente di definire i limiti della rete di monitoraggio e connettersi alla rete virtuale. Questo articolo illustra quando usare e come configurare un ambito collegamento privato di Monitoraggio di Azure.

## <a name="advantages"></a>Vantaggi

Con il collegamento privato è possibile:

- Connettersi privatamente a Monitoraggio di Azure senza aprire alcun accesso alla rete pubblica
- Assicurarsi che l'accesso ai dati di monitoraggio avvenga solo tramite reti private autorizzate
- Impedire l’esfiltrazione di dati dalle reti private definendo specifiche risorse di Monitoraggio di Azure che si connettono tramite l'endpoint privato
- Connettere in modo sicuro la rete locale privata a Monitoraggio di Azure usando ExpressRoute e il collegamento privato
- Mantenere tutto il traffico all'interno della rete backbone di Microsoft Azure

Per altre informazioni, vedere [Vantaggi principali del collegamento privato](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Funzionamento

Monitoraggio di Azure ambito collegamento privato (AMPLS) connette gli endpoint privati (e le reti virtuali in cui sono contenuti) a una o più risorse di Monitoraggio di Azure: aree di lavoro Log Analytics e componenti Application Insights.

![Diagramma della topologia delle risorse di base](./media/private-link-security/private-link-basic-topology.png)

* L'endpoint privato nella rete virtuale consente di raggiungere gli endpoint Monitoraggio di Azure tramite indirizzi IP privati dal pool della rete, anziché usare per gli indirizzi IP pubblici di questi endpoint. Ciò consente di continuare a usare le Monitoraggio di Azure senza aprire la rete virtuale al traffico in uscita non necessario. 
* Il traffico dall'endpoint privato alle risorse Monitoraggio di Azure traffico passa attraverso il backbone Microsoft Azure e non viene instradato alle reti pubbliche. 
* È possibile configurare ogni area di lavoro o componente per consentire o negare l'inserimento e le query dalle reti pubbliche. In questo modo viene fornita una protezione a livello di risorsa, in modo che sia possibile controllare il traffico verso risorse specifiche.

> [!NOTE]
> Una singola risorsa di Monitoraggio di Azure può appartenere a più AMPLS, ma non è possibile connettere una singola rete virtuale a più di un AMPLS. 

## <a name="planning-your-private-link-setup"></a>Pianificazione della configurazione del collegamento privato

Prima di configurare la configurazione Monitoraggio di Azure collegamento privato, prendere in considerazione la topologia di rete e in particolare la topologia di routing DNS. 

### <a name="the-issue-of-dns-overrides"></a>Problema delle sostituzioni DNS
Alcuni Monitoraggio di Azure usano endpoint globali, ovvero servono richieste che hanno come destinazione qualsiasi area di lavoro/componente. Alcuni esempi sono l'endpoint Application Insights di inserimento dati e l'endpoint di query di Application Insights e Log Analytics.

Quando si configura una connessione collegamento privato, il DNS viene aggiornato per eseguire il mapping degli endpoint Monitoraggio di Azure agli indirizzi IP privati dall'intervallo IP della rete virtuale. Questa modifica sostituisce qualsiasi mapping precedente di questi endpoint, che può avere implicazioni significative, esaminato di seguito. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Monitoraggio di Azure collegamento privato si applica a tutte Monitoraggio di Azure risorse, è Tutto o Niente
Poiché alcuni Monitoraggio di Azure endpoint sono globali, non è possibile creare una connessione di collegamento privato per un componente o un'area di lavoro specifici. Quando invece si configura un collegamento privato a un singolo componente Application Insights o a  un'area di lavoro Log Analytics, i record DNS vengono aggiornati per tutti i Application Insights componenti. Qualsiasi tentativo di inserimento o query su un componente verrà eseguito tramite il collegamento privato ed eventualmente avrà esito negativo. Per quanto riguarda Log Analytics, gli endpoint di inserimento e configurazione sono specifici dell'area di lavoro, pertanto la configurazione del collegamento privato verrà applicata solo alle aree di lavoro specificate. L'inserimento e la configurazione di altre aree di lavoro verranno indirizzati agli endpoint di Log Analytics pubblici predefiniti.

![Diagramma delle sostituzioni DNS in una singola rete virtuale](./media/private-link-security/dns-overrides-single-vnet.png)

Questo vale non solo per una rete virtuale specifica, ma per tutte le reti virtuali che condividono lo stesso server DNS (vedere Il problema delle [sostituzioni DNS](#the-issue-of-dns-overrides)). Ad esempio, la richiesta di inserimento dei log a qualsiasi Application Insights componente verrà sempre inviata tramite la route di collegamento privato. I componenti non collegati all'AMPLS avranno esito negativo per la convalida del collegamento privato e non verranno emersi.

> [!NOTE]
> Per concludere: una volta impostata una connessione di collegamento privato a una singola risorsa, si applica alle risorse Monitoraggio di Azure rete. Per Application Insights risorse, si tratta di "All or Nothing". Ciò significa che è necessario aggiungere tutte Application Insights risorse della rete all'AMPLS o nessuna di esse.
> 
> Per gestire i rischi di esfiltrazione dei dati, è consigliabile aggiungere tutte le risorse di Application Insights e Log Analytics all'AMPLS e bloccare il più possibile il traffico in uscita delle reti.

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Monitoraggio di Azure collegamento privato si applica all'intera rete
Alcune reti sono costituite da più reti virtuali. Se le reti virtuali usano lo stesso server DNS, eseguiranno l'override dei mapping DNS reciproci ed eventualmente interromperanno la comunicazione reciproca con Monitoraggio di Azure (vedere Il problema delle sostituzioni [DNS](#the-issue-of-dns-overrides)). In definitiva, solo l'ultima rete virtuale sarà in grado di comunicare con Monitoraggio di Azure, perché il DNS esegue il mapping degli endpoint Monitoraggio di Azure agli indirizzi IP privati da questo intervallo di reti virtuali (che potrebbero non essere raggiungibili da altre reti virtuali).

![Diagramma delle sostituzioni DNS in più reti virtuali](./media/private-link-security/dns-overrides-multiple-vnets.png)

Nel diagramma precedente, la rete virtuale 10.0.1.x si connette prima ad AMPLS1 ed esegue il mapping degli endpoint globali Monitoraggio di Azure agli indirizzi IP dal relativo intervallo. In seguito, la rete virtuale 10.0.2.x si connette ad AMPLS2 ed esegue l'override del mapping DNS degli stessi *endpoint* globali con indirizzi IP del relativo intervallo. Poiché non è stato eseguito il peer di queste reti virtuali, la prima rete virtuale non riesce a raggiungere questi endpoint.

> [!NOTE]
> Per concludere, la configurazione di AMPLS influisce su tutte le reti che condividono le stesse zone DNS. Per evitare di eseguire l'override reciproco dei mapping degli endpoint DNS, è meglio configurare un singolo endpoint privato in una rete con peering (ad esempio una rete virtuale dell'hub) o separare le reti a livello di DNS (ad esempio usando server d'inoltro DNS o server DNS separati interamente).

### <a name="hub-spoke-networks"></a>Reti hub-spoke
Le topologie hub-spoke possono evitare il problema delle sostituzioni DNS impostando un collegamento privato nella rete virtuale hub (principale), anziché configurare un collegamento privato per ogni rete virtuale separatamente. Questa configurazione ha senso soprattutto se le Monitoraggio di Azure usate dalle reti virtuali spoke sono condivise. 

![Hub-and-spoke-single-PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> È preferibile creare intenzionalmente collegamenti privati separati per le reti virtuali spoke, ad esempio per consentire a ogni rete virtuale di accedere a un set limitato di risorse di monitoraggio. In questi casi, è possibile creare un endpoint privato dedicato e AMPLS per ogni rete virtuale, ma è anche necessario verificare che non condivivano le stesse zone DNS per evitare sostituzioni DNS.


### <a name="consider-limits"></a>Prendere in considerazione i limiti

Come indicato in [Restrizioni e limitazioni,](#restrictions-and-limitations)l'oggetto AMPLS presenta una serie di limiti, illustrati nella topologia seguente:
* Ogni rete virtuale si connette a **un solo** oggetto AMPLS.
* AMPLS B è connesso agli endpoint privati di due reti virtuali (VNet2 e VNet3), usando 2 delle 10 possibili connessioni endpoint privato.
* AMPLS A si connette a due aree di lavoro e a un componente application insight, usando 3 delle 50 possibili connessioni Monitoraggio di Azure risorse.
* Workspace2 si connette ad AMPLS A e AMPLS B, usando 2 delle 5 possibili connessioni AMPLS.

![Diagramma dei limiti AMPLS](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>Connessione di esempio

Per iniziare, creare una risorsa ambito collegamento privato di Monitoraggio di Azure.

1. Andare a **Crea una risorsa** nel portale di Azure e cercare **Ambito collegamento privato di Monitoraggio di Azure**.

   ![Trovare Monitoraggio di Azure ambito collegamento privato](./media/private-link-security/ampls-find-1c.png)

2. Selezionare **Crea**.
3. Selezionare un gruppo di sottoscrizioni e risorse.
4. Assegnare un nome alla risorsa AMPLS. È meglio usare un nome significativo e chiaro, ad esempio "AppServerProdTelem".
5. Selezionare **Rivedi e crea**. 

   ![Creare Monitoraggio di Azure ambito collegamento privato](./media/private-link-security/ampls-create-1d.png)

6. Lasciare che la convalida passi e quindi selezionare **Crea**.

### <a name="connect-azure-monitor-resources"></a>Connettere le risorse di Monitoraggio di Azure

Connettere Monitoraggio di Azure risorse (aree di lavoro Log Analytics e Application Insights componenti) all'AMPLS.

1. Nell'ambito Monitoraggio di Azure Collegamento privato selezionare Monitoraggio di Azure **risorse** nel menu a sinistra. Fare clic sul pulsante **Aggiungi**.
2. Aggiungere l'area di lavoro o il componente. Selezionando il **pulsante** Aggiungi viene visualizzata una finestra di dialogo in cui è possibile selezionare Monitoraggio di Azure risorse. È possibile esplorare le sottoscrizioni e i gruppi di risorse oppure digitarne il nome per filtrarli. Selezionare l'area di lavoro o il componente **e selezionare Applica** per aggiungerli all'ambito.

    ![Screenshot di selezione dell’esperienza utente di ambito](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> L'Monitoraggio di Azure risorse richiede prima di tutto di disconnetterle da tutti gli oggetti AMPLS a cui sono connesse. Non è possibile eliminare le risorse connesse a un'applicazione AMPLS.

### <a name="connect-to-a-private-endpoint"></a>Connettersi a un endpoint privato

Ora che sono disponibili delle risorse connesse alla risorsa AMPLS, creare un endpoint privato per connettere la rete. È possibile eseguire questa attività nel [Centro collegamento privato del portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints) o all'interno dell'ambito collegamento privato di Monitoraggio di Azure, come in questo esempio.

1. Nella risorsa ambito selezionare **Connessioni endpoint privato** nel menu della risorsa a sinistra. Selezionare **Endpoint privato per** avviare il processo di creazione dell'endpoint. È anche possibile approvare le connessioni avviate nel centro collegamenti privati selezionandole e selezionando **Approva**.

    ![Screenshot dell’esperienza utente di connessioni all'endpoint privato](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Selezionare la sottoscrizione, il gruppo di risorse, il nome dell'endpoint e l'area in cui deve risiedere. L'area deve essere la stessa area della rete virtuale a cui si connette.

3. Selezionare **Avanti: Risorsa**. 

4. Nella schermata Risorsa,

   a. Selezionare la **Sottoscrizione** che contiene la risorsa ambito privato di Monitoraggio di Azure. 

   b. Per il **tipo di risorsa**, scegliere **Microsoft.insights/privateLinkScopes**. 

   c. Dal menu a discesa **Risorsa** scegliere l’ambito collegamento privato creato in precedenza. 

   d. Selezionare **Avanti: Configurazione >**.
      ![Screenshot di selezione di Crea endpoint privato](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. Nel riquadro di configurazione,

   a.    Scegliere la **rete virtuale** e la **subnet** da connettere alle risorse di Monitoraggio di Azure. 
 
   b.    Scegliere **Sì** per **Integra con la zona DNS privato** e consentire la creazione automatica di una nuova zona DNS privato. Le zone DNS effettive possono essere diverse da quanto illustrato nello screenshot seguente. 
   > [!NOTE]
   > Se si sceglie **No** e si preferisce gestire manualmente i record DNS, completare prima di tutto la configurazione del collegamento privato, inclusi l'endpoint privato e la configurazione AMPLS. Quindi configurare il DNS seguendo le istruzioni riportate in [Configurazione del DNS dell'endpoint privato di Azure](../../private-link/private-endpoint-dns.md). Assicurarsi di non creare record vuoti come preparazione per la configurazione del collegamento privato. I record DNS creati possono sostituire le impostazioni esistenti e influire sulla connettività con Monitoraggio di Azure.
 
   c.    Selezionare **Rivedi e crea**.
 
   d.    Superare la convalida. 
 
   e.    Selezionare **Crea**. 

    ![Screenshot della selezione dei dettagli dell'endpoint privato.](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

A questo punto è stato creato un nuovo endpoint privato connesso a questo servizio AMPLS.

## <a name="review-and-validate-your-private-link-setup"></a>Esaminare e convalidare la configurazione del collegamento privato

### <a name="reviewing-your-endpoints-dns-settings"></a>Verifica delle impostazioni DNS dell'endpoint
L'endpoint privato creato dovrebbe ora avere quattro zone DNS configurate:

[![Screenshot delle zone DNS dell'endpoint privato.](./media/private-link-security/private-endpoint-dns-zones.png)](./media/private-link-security/private-endpoint-dns-zones-expanded.png#lightbox)

* privatelink-monitor-azure-com
* privatelink-oms-opinsights-azure-com
* privatelink-ods-opinsights-azure-com
* privatelink-agentsvc-azure-automation-net

> [!NOTE]
> Ognuna di queste zone esegue il mapping Monitoraggio di Azure endpoint specifici agli indirizzi IP privati dal pool di indirizzi IP della rete virtuale. Gli indirizzi IP illustrati nelle immagini seguenti sono solo esempi. La configurazione dovrebbe invece mostrare gli indirizzi IP privati della propria rete.

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-azure-com
Questa zona copre gli endpoint globali usati da Monitoraggio di Azure, ovvero questi endpoint servono le richieste considerando tutte le risorse, non una specifica. Per questa zona deve essere stato eseguito il mapping degli endpoint per:
* `in.ai` - Application Insights endpoint di inserimento (voce globale e locale)
* `api` - endpoint Application Insights'API di Log Analytics
* `live` - Application Insights endpoint delle metriche in tempo reale
* `profiler` - Endpoint Application Insights profiler
* `snapshot`- Application Insights dell'endpoint snapshot [ ![ screenshot DNS privato zone monitor-azure-com.](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-oms-opinsights-azure-com
Questa zona illustra il mapping specifico dell'area di lavoro agli endpoint OMS. Verrà visualizzata una voce per ogni area di lavoro collegata a AMPLS connessa a questo endpoint privato.
[![Screenshot della DNS privato oms-opinsights-azure-com.](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ods-opinsights-azure-com
Questa zona illustra il mapping specifico dell'area di lavoro agli endpoint ODS, l'endpoint di inserimento di Log Analytics. Verrà visualizzata una voce per ogni area di lavoro collegata a AMPLS connessa a questo endpoint privato.
[![Screenshot della DNS privato zona ods-opinsights-azure-com.](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-azure-automation-net
Questa zona illustra il mapping specifico dell'area di lavoro agli endpoint di automazione del servizio agente. Verrà visualizzata una voce per ogni area di lavoro collegata a AMPLS connessa a questo endpoint privato.
[![Screenshot dell DNS privato'agente di zona svc-azure-automation-net.](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

#### <a name="privatelink-blob-core-windows-net"></a>privatelink-blob-core-windows-net
Questa zona configura la connettività all'account di archiviazione dei pacchetti di soluzioni degli agenti globali. Tramite di esso, gli agenti possono scaricare pacchetti di soluzioni nuovi o aggiornati (noti anche come Management Pack). È necessaria una sola voce per gestire gli agenti di Log Analytics, indipendentemente dal numero di aree di lavoro usate.
[![Screenshot della DNS privato blob-core-windows-net della zona.](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net.png)](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net-expanded.png#lightbox)
> [!NOTE]
> Questa voce viene aggiunta solo alle configurazioni di collegamenti privati create in o dopo il 19 aprile 2021.


### <a name="validating-you-are-communicating-over-a-private-link"></a>Convalida della comunicazione tramite un collegamento privato
* Per verificare che le richieste siano ora inviate tramite l'endpoint privato e gli endpoint privati mappati all'IP, è possibile esaminarle con uno strumento di rilevamento della rete o anche con il browser. Ad esempio, quando si tenta di eseguire una query sull'area di lavoro o sull'applicazione, assicurarsi che la richiesta sia inviata all'indirizzo IP privato mappato all'endpoint API, in questo esempio *è 172.17.0.9.*

    Nota: alcuni browser possono usare altre impostazioni DNS (vedere [Impostazioni DNS del browser).](#browser-dns-settings) Assicurarsi che le impostazioni DNS siano valide.

* Per assicurarsi che l'area di lavoro o il componente non riceva richieste da reti pubbliche (non connesse tramite AMPLS), impostare i flag di query e inserimento pubblico della risorsa su *No,* come illustrato [in](#manage-access-from-outside-of-private-links-scopes)Gestire l'accesso dall'esterno degli ambiti dei collegamenti privati.

* Da un client nella rete protetta usare `nslookup` per uno qualsiasi degli endpoint elencati nelle zone DNS. Deve essere risolto dal server DNS agli indirizzi IP privati mappati anziché agli indirizzi IP pubblici usati per impostazione predefinita.


## <a name="configure-log-analytics"></a>Configurare Log Analytics

Accedere al portale di Azure. Nel menu delle risorse dell'area di lavoro Log Analytics è presente un elemento **denominato** Isolamento rete sul lato sinistro. Da questo menu è possibile controllare due stati diversi.

![Isolamento rete LA](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Ambiti Monitoraggio di Azure collegamento privato connessi
Tutti gli ambiti connessi all'area di lavoro vengono visualizzati in questa schermata. La connessione agli ambiti (AMPLSs) consente al traffico di rete dalla rete virtuale connessa a ogni servizio AMPLS di raggiungere questa area di lavoro. La creazione di una connessione tramite qui ha lo stesso effetto della configurazione nell'ambito, come è stato fatto in Connessione Monitoraggio di Azure [risorse](#connect-azure-monitor-resources). Per aggiungere una nuova connessione, selezionare **Aggiungi e** selezionare l'Monitoraggio di Azure Ambito collegamento privato. Selezionare **Applica** per connetterlo. Si noti che un'area di lavoro può connettersi a 5 oggetti AMPLS, come indicato in [Restrizioni e limitazioni.](#restrictions-and-limitations) 

### <a name="manage-access-from-outside-of-private-links-scopes"></a>Gestire l'accesso dall'esterno degli ambiti dei collegamenti privati
Le impostazioni nella parte inferiore di questa pagina controllano l'accesso dalle reti pubbliche, ovvero le reti non connesse tramite gli ambiti elencati (AMPLS). **L'impostazione Consenti l'accesso** alla rete pubblica per l'inserimento su **No** blocks ingestion of logs from machines outside the connected scopes (Nessun blocco di inserimento dei log da computer esterni agli ambiti connessi). **L'impostazione Consenti l'accesso alla rete pubblica per le query** su **No** blocca le query provenienti da computer esterni agli ambiti. Sono incluse le query eseguite tramite cartelle di lavoro, dashboard, esperienze client basate su API, informazioni dettagliate portale di Azure e altro ancora. Le esperienze eseguite all'portale di Azure e che eseguono query sui dati di Log Analytics devono essere eseguite anche all'interno della rete virtuale collegata privata.

### <a name="exceptions"></a>Eccezioni
La limitazione dell'accesso come illustrato in precedenza non si applica alla Azure Resource Manager e pertanto presenta le limitazioni seguenti:
* Accesso ai dati: durante il blocco o l'autorizzazione delle query da reti pubbliche si applica alla maggior parte delle esperienze di Log Analytics, alcune esperienze eccedono query sui dati tramite Azure Resource Manager e pertanto non saranno in grado di eseguire query sui dati a meno che non vengano applicate anche le impostazioni di collegamento privato al Resource Manager (funzionalità disponibile a breve). Esempi sono Monitoraggio di Azure soluzioni, cartelle di lavoro e informazioni dettagliate e il connettore LogicApp.
* Gestione dell'area di lavoro: le impostazioni dell'area di lavoro e le modifiche di configurazione (inclusa l'attivazione o la disattivazione di queste impostazioni di accesso) vengono gestite da Azure Resource Manager. Limitare l'accesso alla gestione dell'area di lavoro usando i ruoli, le autorizzazioni, i controlli di rete e il controllo appropriati. Per altre informazioni, vedere [Ruoli, autorizzazioni e sicurezza di Monitoraggio di Azure](../roles-permissions-security.md).

> [!NOTE]
> I log e le metriche caricati in un'area di lavoro tramite [Impostazioni di diagnostica](../essentials/diagnostic-settings.md) passano attraverso un canale Microsoft privato protetto e non sono controllati da queste impostazioni.

### <a name="log-analytics-solution-packs-download"></a>Download dei pacchetti di soluzioni di Log Analytics
Gli agenti di Log Analytics devono accedere a un account di archiviazione globale per scaricare i Pacchetti di soluzioni. Le configurazioni del collegamento privato create in o dopo il 19 aprile 2021 possono raggiungere l'archiviazione dei pacchetti di soluzioni degli agenti tramite il collegamento privato. Ciò è reso possibile tramite la nuova zona DNS creata [per](#privatelink-blob-core-windows-net)blob.core.windows.net .

Se la configurazione del collegamento privato è stata creata prima del 19 aprile 2021, non raggiungerà l'archiviazione dei Pacchetti di soluzioni tramite un collegamento privato. Per gestirlo, è possibile eseguire una delle operazioni seguenti:
* Ri-creare l'AMPLS e l'endpoint privato connessi
* Consentire agli agenti di raggiungere l'account di archiviazione tramite il relativo endpoint pubblico, aggiungendo le regole seguenti all'elenco elementi consentiti del firewall:

    | Ambiente cloud | Risorsa agente | Porte | Direction |
    |:--|:--|:--|:--|
    |Azure Public     | scadvisorcontent.blob.core.windows.net         | 443 | In uscita
    |Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  In uscita
    |21Vianet per Azure Cina      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | In uscita


## <a name="configure-application-insights"></a>Configura Application Insights

Accedere al portale di Azure. Nella risorsa Monitoraggio di Azure Application Insights componente di rete è una voce di menu Isolamento **rete** sul lato sinistro. Da questo menu è possibile controllare due stati diversi.

![Isolamento rete intelligenza artificiale](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Innanzitutto, è possibile connettere questa risorsa Application Insights agli ambiti collegamento privato di Monitoraggio di Azure a cui si ha accesso. Selezionare **Aggiungi** e selezionare l'Monitoraggio di Azure **Ambito collegamento privato**. Selezionare Applica per connetterlo. Tutti gli ambiti connessi vengono visualizzati in questa schermata. L'esecuzione di questa connessione consente al traffico di rete nelle reti virtuali connesse di raggiungere questo componente e ha lo stesso effetto della connessione dall'ambito come è stato fatto in Connessione Monitoraggio di Azure [risorse](#connect-azure-monitor-resources). 

È quindi possibile controllare il modo in cui questa risorsa può essere raggiunta dall'esterno degli ambiti di collegamento privato (AMPLS) elencati in precedenza. Se si imposta **Consenti** l'accesso alla rete pubblica per l'inserimento su **No,** i computer o gli SDK esterni agli ambiti connessi non possono caricare dati in questo componente. Se si imposta **Consenti l'accesso** alla rete pubblica per le query su **No,** i computer esterni agli ambiti non possono accedere ai dati in questa Application Insights risorsa. Questi dati includono l'accesso ai log APM, alle metriche e al flusso metriche attive, oltre a esperienze integrate come cartelle di lavoro, dashboard, esperienze client basate su API di query, informazioni dettagliate nel portale di Azure e altro ancora. 

> [!NOTE]
> Le esperienze di utilizzo non del portale devono essere eseguite anche nella rete virtuale collegata privata che include i carichi di lavoro monitorati.

Sarà necessario aggiungere le risorse che eseguono l’hosting dei carichi di lavoro monitorati al collegamento privato. Ad esempio, vedere [Uso di endpoint privati per l'app Web di Azure.](../../app-service/networking/private-endpoint.md)

Questo tipo di limitazione dell'accesso si applica solo ai dati nella risorsa Application Insights. Tuttavia, le modifiche di configurazione, inclusa l'attivazione o la disattivazione di queste impostazioni di accesso, vengono gestite da Azure Resource Manager. È quindi consigliabile limitare l'accesso Resource Manager usando i ruoli, le autorizzazioni, i controlli di rete e il controllo appropriati. Per altre informazioni, vedere [Ruoli, autorizzazioni e sicurezza di Monitoraggio di Azure](../roles-permissions-security.md).

> [!NOTE]
> Per proteggere in modo completo Application Insights basato sull'area di lavoro, è necessario bloccare l'accesso alla risorsa Application Insights e all'area di lavoro Log Analytics sottostante.
>
> La diagnostica a livello di codice (profiler/debugger) deve fornire [il proprio account di archiviazione per](../app/profiler-bring-your-own-storage.md) supportare il collegamento privato.

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>Gestione della natura all-or-Nothing dei collegamenti privati
Come illustrato [in](#planning-your-private-link-setup)Pianificazione della configurazione del collegamento privato, la configurazione di un collegamento privato anche per una singola risorsa influisce su tutte le risorse Monitoraggio di Azure in tale rete e in altre reti che condividono lo stesso DNS. Questo comportamento può rendere complesso il processo di onboarding. Valutare le opzioni seguenti:

* Tutto in: l'approccio più semplice e sicuro consiste nell'aggiungere tutti i componenti Application Insights componenti all'AMPLS. Per i componenti a cui si vuole ancora accedere anche da altre reti, lasciare i flag "Consenti accesso a Internet pubblico per inserimento/query" impostati su Sì (impostazione predefinita).
* Isolare le reti: se si è (o si è in grado di allinearsi) usando reti virtuali spoke, seguire le indicazioni nella topologia di rete [hub-spoke in Azure.](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) Configurare quindi impostazioni di collegamento privato separate nelle reti virtuali spoke pertinenti. Assicurarsi di separare anche le zone DNS, poiché la condivisione di zone DNS con altre reti spoke causerà [sostituzioni DNS.](#the-issue-of-dns-overrides)
* Usa zone DNS personalizzate per app specifiche: questa soluzione consente di accedere a componenti Application Insights selezionati tramite un collegamento privato, mantenendo tutto il traffico sulle route pubbliche.
    - Configurare una [zona DNS privata personalizzata](../../private-link/private-endpoint-dns.md)e assegnargli un nome univoco, ad esempio internal.monitor.azure.com
    - Creare un'istanza AMPLS e un endpoint privato e scegliere di **non** eseguire l'integrazione automatica con DNS privato
    - Passare a Endpoint privato -> configurazione DNS ed esaminare il mapping consigliato di FQDN.
    - Scegliere Aggiungi configurazione e selezionare la internal.monitor.azure.com appena creata
    - Aggiungere record per lo ![ screenshot precedente della zona DNS configurata](./media/private-link-security/private-endpoint-global-dns-zone.png)
    - Passare al componente Application Insights e copiarne la [stringa di connessione.](../app/sdk-connection-string.md)
    - Le app o gli script che vogliono chiamare questo componente tramite un collegamento privato devono usare la stringa di connessione con EndpointSuffix=internal.monitor.azure.com
* Eseguire il mapping degli endpoint tramite file host anziché DNS: per avere un accesso collegamento privato solo da un computer o una macchina virtuale specifica nella rete:
    - Configurare ampls e un endpoint privato e scegliere di **non** eseguire l'integrazione automatica con DNS privato 
    - Configurare i record A precedenti in un computer che esegue l'app nel file hosts


## <a name="use-apis-and-command-line"></a>Usare le API e la riga di comando

È possibile automatizzare il processo descritto in precedenza Azure Resource Manager modelli, REST e interfacce della riga di comando.

Per creare e gestire ambiti di collegamento privato, usare [l'API REST](/rest/api/monitor/privatelinkscopes(preview)/private%20link%20scoped%20resources%20(preview)) o l'interfaccia della riga di comando di [Azure (az monitor private-link-scope).](/cli/azure/monitor/private-link-scope)

Per gestire l'accesso alla rete, usare i flag `[--ingestion-access {Disabled, Enabled}]` e `[--query-access {Disabled, Enabled}]` in [Aree di lavoro Log Analytics](/cli/azure/monitor/log-analytics/workspace) o [Componenti Application Insights](/cli/azure/monitor/app-insights/component).

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>Raccogliere log personalizzati e log IIS tramite collegamento privato

Gli account di archiviazione vengono usati nel processo di inserimento dei log personalizzati. Per impostazione predefinita, vengono usati gli account di archiviazione gestiti dal servizio. Tuttavia, per inserire i log personalizzati nei collegamenti privati, è necessario usare gli account di archiviazione personali e associarli alle aree di lavoro Log Analytics. Per ulteriori informazioni su come configurare tali account, utilizzare la [riga di comando](/cli/azure/monitor/log-analytics/workspace/linked-storage).

Per altre informazioni sull'uso di account di archiviazione personali, vedere [Account di archiviazione di proprietà del cliente per l'inserimento dei log](private-storage.md)

## <a name="restrictions-and-limitations"></a>Restrizioni e limitazioni

### <a name="ampls"></a>AMPLS
L'oggetto AMPLS presenta una serie di limiti da considerare quando si pianifica la configurazione del collegamento privato:

* Una rete virtuale può connettersi solo a un oggetto AMPLS. Ciò significa che l'oggetto AMPLS deve fornire l'accesso a Monitoraggio di Azure risorse a cui la rete virtuale deve avere accesso.
* Una Monitoraggio di Azure di lavoro (area di lavoro o Application Insights) può connettersi al massimo a 5 AMPLS.
* Un oggetto AMPLS può connettersi al massimo a 50 Monitoraggio di Azure risorse.
* Un oggetto AMPLS può connettersi al massimo a 10 endpoint privati.

Per [una revisione](#consider-limits) più approfondita di questi limiti, vedere Considerare i limiti.

### <a name="agents"></a>Agenti

Le versioni più recenti degli agenti Windows e Linux devono essere usate per supportare l'inserimento sicuro nelle aree di lavoro Log Analytics. Le versioni precedenti non possono caricare i dati di monitoraggio su una rete privata.

**Agente Windows di Log Analytics**

Usare l'agente di Log Analytics versione 10.20.18038.0 o successiva.

**Agente Linux di Log Analytics**

Usare la versione dell'agente 1.12.25 o successiva. Se non è possibile, eseguire i comandi seguenti nella macchina virtuale.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Portale di Azure

Per usare le esperienze del portale di Monitoraggio di Azure, ad esempio Application Insights e Log Analytics, è necessario consentire l'accesso alle estensioni del portale di Azure e di Monitoraggio di Azure nelle reti private. Aggiungere i tag del servizio **AzureActiveDirectory**, **AzureResourceManager**, **AzureFrontDoor.FirstParty** e **AzureFrontdoor.Frontend** [al](../../firewall/service-tags.md) gruppo di sicurezza di rete.

### <a name="querying-data"></a>Query sui dati
[ `externaldata` L'operatore](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor) non è supportato tramite un collegamento privato, perché legge i dati dagli account di archiviazione, ma non garantisce che l'accesso alle risorse di archiviazione sia privato.

### <a name="programmatic-access"></a>Accesso a livello di codice

Per usare l'API [REST,](/cli/azure/monitor) l'interfaccia della riga di [](../../virtual-network/service-tags-overview.md)comando o PowerShell con Monitoraggio di Azure in reti private, aggiungere i tag di servizio **AzureActiveDirectory** e **AzureResourceManager** al firewall.  

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Download di SDK di Application Insights da una rete per la distribuzione di contenuti

Aggregare il codice JavaScript nello script in modo che il browser non tenti di scaricare il codice da una rete CDN. Un esempio è disponibile in [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>Impostazioni DNS del browser

Se ci si connette alle risorse Monitoraggio di Azure tramite un collegamento privato, il traffico verso queste risorse deve passare attraverso l'endpoint privato configurato nella rete. Per abilitare l'endpoint privato, aggiornare le impostazioni DNS come illustrato in [Connettersi a un endpoint privato.](#connect-to-a-private-endpoint) Alcuni browser usano le proprie impostazioni DNS invece di quelle impostate. Il browser potrebbe tentare di connettersi Monitoraggio di Azure endpoint pubblici e ignorare completamente il collegamento privato. Verificare che le impostazioni del browser non sostituiscono o memorizzare nella cache le impostazioni DNS meno avanzate. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni [sull'archiviazione privata](private-storage.md)
