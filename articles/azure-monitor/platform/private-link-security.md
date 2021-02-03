---
title: Usare il collegamento privato di Azure per connettere in modo sicuro le reti a Monitoraggio di Azure
description: Usare il collegamento privato di Azure per connettere in modo sicuro le reti a Monitoraggio di Azure
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.subservice: ''
ms.openlocfilehash: a7464216649d6b482893693a1f182af5cf6e77ac
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508998"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Usare il collegamento privato di Azure per connettere in modo sicuro le reti a Monitoraggio di Azure

[Collegamento privato di Azure](../../private-link/private-link-overview.md) consente di collegare in modo sicuro i servizi PaaS di Azure alla rete virtuale usando endpoint privati. Per molti servizi, è sufficiente configurare un endpoint per ogni risorsa. Tuttavia, Monitoraggio di Azure è un insieme di diversi servizi interconnessi che interagiscono tra loro per monitorare i carichi di lavoro. Di conseguenza, è stata creata una risorsa denominata ambito di collegamento privato di monitoraggio di Azure (AMPLS). AMPLS consente di definire i limiti della rete di monitoraggio e di connettersi alla rete virtuale. Questo articolo illustra quando usare e come configurare un ambito collegamento privato di Monitoraggio di Azure.

## <a name="advantages"></a>Vantaggi

Con il collegamento privato è possibile:

- Connettersi privatamente a Monitoraggio di Azure senza aprire alcun accesso alla rete pubblica
- Assicurarsi che l'accesso ai dati di monitoraggio avvenga solo tramite reti private autorizzate
- Impedire l’esfiltrazione di dati dalle reti private definendo specifiche risorse di Monitoraggio di Azure che si connettono tramite l'endpoint privato
- Connettere in modo sicuro la rete locale privata a Monitoraggio di Azure usando ExpressRoute e il collegamento privato
- Mantenere tutto il traffico all'interno della rete backbone di Microsoft Azure

Per altre informazioni, vedere [Vantaggi principali del collegamento privato](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Funzionamento

L'ambito del collegamento privato di monitoraggio di Azure (AMPLS) connette gli endpoint privati (e i reti virtuali in essi contenuti) a una o più risorse di monitoraggio di Azure, Log Analytics aree di lavoro e componenti Application Insights.

![Diagramma della topologia di risorse di base](./media/private-link-security/private-link-basic-topology.png)

> [!NOTE]
> Una singola risorsa di Monitoraggio di Azure può appartenere a più AMPLS, ma non è possibile connettere una singola rete virtuale a più di un AMPLS. 

### <a name="the-issue-of-dns-overrides"></a>Il problema degli override DNS
Log Analytics e Application Insights usano gli endpoint globali per alcuni servizi, ovvero servono richieste destinate a qualsiasi area di lavoro/componente. Ad esempio, Application Insights usa un endpoint globale per l'inserimento dei log e sia Application Insights che Log Analytics usano un endpoint globale per le richieste di query.

Quando si configura una connessione a un collegamento privato, il DNS viene aggiornato per eseguire il mapping degli endpoint di monitoraggio di Azure a indirizzi IP privati dall'intervallo IP di VNet. Questa modifica sostituisce qualsiasi mapping precedente di questi endpoint, che può avere implicazioni significative, esaminato di seguito. 

## <a name="planning-based-on-your-network-topology"></a>Pianificazione basata sulla topologia di rete

Prima di configurare la configurazione del collegamento privato di monitoraggio di Azure, prendere in considerazione la topologia di rete e in particolare la topologia di routing DNS. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Il collegamento privato di monitoraggio di Azure si applica a tutte le risorse di monitoraggio di Azure. tutto o niente
Poiché alcuni endpoint di monitoraggio di Azure sono globali, non è possibile creare una connessione di collegamento privato per un componente o un'area di lavoro specifica. Al contrario, quando si configura un collegamento privato a un singolo componente Application Insights, i record DNS vengono aggiornati per **tutti i** componenti Application Insights. Qualsiasi tentativo di inserire o eseguire una query su un componente tenterà di passare attraverso il collegamento privato e probabilmente avrà esito negativo. Analogamente, se si configura un collegamento privato a una singola area di lavoro, tutte le query Log Analytics passano attraverso l'endpoint della query di collegamento privato (ma non le richieste di inserimento, che hanno endpoint specifici dell'area di lavoro).

![Diagramma degli override DNS in un singolo VNet](./media/private-link-security/dns-overrides-single-vnet.png)

Questo vale non solo per un VNet specifico, ma per tutti reti virtuali che condividono lo stesso server DNS (vedere [il problema degli override DNS](#the-issue-of-dns-overrides)). Quindi, ad esempio, la richiesta di inserimento dei log in qualsiasi Application Insights componente verrà sempre inviata tramite la route di collegamento privata. I componenti che non sono collegati a AMPLS non riusciranno a eseguire la convalida del collegamento privato e non passano attraverso.

**In realtà, è necessario connettere tutte le risorse di monitoraggio di Azure nella rete a un collegamento privato (aggiungerle a AMPLS) o nessuna di esse.**

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Il collegamento privato di monitoraggio di Azure si applica all'intera rete
Alcune reti sono costituite da più reti virtuali. Se questi reti virtuali usano lo stesso server DNS, eseguiranno l'override dei mapping DNS degli altri ed eventualmente interromperanno le comunicazioni con monitoraggio di Azure (vedere [il problema degli override DNS](#the-issue-of-dns-overrides)). Infine, solo l'ultimo VNet sarà in grado di comunicare con monitoraggio di Azure, poiché il DNS eseguirà il mapping degli endpoint di monitoraggio di Azure a indirizzi IP privati da questo intervallo di reti virtuali (che potrebbero non essere raggiungibili da altri reti virtuali).

![Diagramma degli override DNS in più reti virtuali](./media/private-link-security/dns-overrides-multiple-vnets.png)

Nel diagramma precedente, VNet 10.0.1. x si connette prima di tutto a AMPLS1 ed esegue il mapping degli endpoint globali di monitoraggio di Azure agli indirizzi IP dall'intervallo. Successivamente, VNet 10.0.2. x si connette a AMPLS2 ed esegue l'override del mapping DNS degli *stessi endpoint globali* con IP dal relativo intervallo. Poiché non viene eseguito il peering di questi reti virtuali, il primo VNet ora non riesce a raggiungere questi endpoint.

**È necessario eseguire il peering di reti virtuali che usano lo stesso DNS, direttamente o tramite un VNet Hub. Reti virtuali di cui non è stato usato il peering devono usare anche un server DNS diverso, server d'inoltri DNS o un altro meccanismo per evitare conflitti di DNS.**

### <a name="hub-spoke-networks"></a>Reti hub-spoke
Le topologie hub-spoke possono evitare il problema degli override DNS impostando un collegamento privato nell'hub (Main) VNet, invece di configurare un collegamento privato per ogni VNet separatamente. Questa configurazione ha senso soprattutto se le risorse di monitoraggio di Azure usate dal reti virtuali spoke sono condivise. 

![Hub-and-spoke-singolo PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> È possibile che si preferisca intenzionalmente creare collegamenti privati distinti per i reti virtuali spoke, ad esempio per consentire a ogni VNet di accedere a un set limitato di risorse di monitoraggio. In questi casi, è possibile creare un endpoint privato dedicato e AMPLS per ogni VNet, ma è necessario verificare anche che non condividono lo stesso server DNS per evitare sostituzioni DNS.


### <a name="consider-limits"></a>Considerare i limiti

Come elencato in [restrizioni e limitazioni](#restrictions-and-limitations), l'oggetto AMPLS presenta diversi limiti, descritti nella topologia seguente:
* Ogni VNet si connette a **un** solo oggetto AMPLS.
* AMPLS B è connesso a endpoint privati di due reti virtuali (VNet2 e VNet3), usando 2 delle 10 possibili connessioni a endpoint privato.
* AMPLS A si connette a due aree di lavoro e a un componente Application Insight, usando 3 delle 50 possibili connessioni di monitoraggio di Azure.
* Workspace2 si connette a AMPLS A e AMPLS B, usando 2 delle 5 connessioni AMPLS possibili.

![Diagramma dei limiti di AMPLS](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>Connessione di esempio

Per iniziare, creare una risorsa ambito collegamento privato di Monitoraggio di Azure.

1. Andare a **Crea una risorsa** nel portale di Azure e cercare **Ambito collegamento privato di Monitoraggio di Azure**.

   ![Trova ambito collegamento privato di monitoraggio di Azure](./media/private-link-security/ampls-find-1c.png)

2. Selezionare **Crea**.
3. Selezionare un gruppo di sottoscrizioni e risorse.
4. Assegnare un nome alla risorsa AMPLS. È preferibile usare un nome significativo e chiaro, ad esempio "AppServerProdTelem".
5. Selezionare **Rivedi e crea**. 

   ![Creare un ambito di collegamento privato di monitoraggio di Azure](./media/private-link-security/ampls-create-1d.png)

6. Consentire il passaggio della convalida, quindi selezionare **Crea**.

### <a name="connect-azure-monitor-resources"></a>Connettere le risorse di Monitoraggio di Azure

Connettere le risorse di monitoraggio di Azure (Log Analytics le aree di lavoro e i componenti Application Insights) a AMPLS.

1. Nell'ambito del collegamento privato di monitoraggio di Azure selezionare **risorse di monitoraggio di Azure** nel menu a sinistra. Fare clic sul pulsante **Aggiungi**.
2. Aggiungere l'area di lavoro o il componente. Se si seleziona il pulsante **Aggiungi** , viene visualizzata una finestra di dialogo in cui è possibile selezionare risorse di monitoraggio di Azure. È possibile esplorare le sottoscrizioni e i gruppi di risorse oppure digitarne il nome per filtrarli. Selezionare l'area di lavoro o il componente e selezionare **applica** per aggiungerli all'ambito.

    ![Screenshot di selezione dell’esperienza utente di ambito](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Per eliminare le risorse di monitoraggio di Azure, è necessario prima disconnetterle da qualsiasi oggetto AMPLS a cui sono connesse. Non è possibile eliminare le risorse connesse a un AMPLS.

### <a name="connect-to-a-private-endpoint"></a>Connettersi a un endpoint privato

Ora che sono disponibili delle risorse connesse alla risorsa AMPLS, creare un endpoint privato per connettere la rete. È possibile eseguire questa attività nel [Centro collegamento privato del portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints) o all'interno dell'ambito collegamento privato di Monitoraggio di Azure, come in questo esempio.

1. Nella risorsa ambito selezionare **connessioni endpoint privato** nel menu delle risorse a sinistra. Selezionare **endpoint privato** per avviare il processo di creazione dell'endpoint. È anche possibile approvare le connessioni avviate in centro collegamenti privati, selezionando le connessioni e selezionando **approva**.

    ![Screenshot dell’esperienza utente di connessioni all'endpoint privato](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Selezionare la sottoscrizione, il gruppo di risorse, il nome dell'endpoint e l'area in cui deve risiedere. L'area deve essere la stessa area della rete virtuale a cui verrà connesso.

3. Selezionare **Avanti: Risorsa**. 

4. Nella schermata Risorsa,

   a. Selezionare la **Sottoscrizione** che contiene la risorsa ambito privato di Monitoraggio di Azure. 

   b. Per il **tipo di risorsa**, scegliere **Microsoft.insights/privateLinkScopes**. 

   c. Dal menu a discesa **Risorsa** scegliere l’ambito collegamento privato creato in precedenza. 

   d. Selezionare **Next: Configuration >**.
      ![Screenshot di selezione di Crea endpoint privato](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. Nel riquadro di configurazione,

   a.    Scegliere la **rete virtuale** e la **subnet** da connettere alle risorse di Monitoraggio di Azure. 
 
   b.    Scegliere **Sì** per **Integra con la zona DNS privato** e consentire la creazione automatica di una nuova zona DNS privato. Le zone DNS effettive potrebbero essere diverse da quelle illustrate nella schermata seguente. 
   > [!NOTE]
   > Se si sceglie **No** e si preferisce gestire i record DNS manualmente, completare prima di tutto la configurazione del collegamento privato, inclusi questo endpoint privato e la configurazione AMPLS. Quindi configurare il DNS seguendo le istruzioni riportate in [Configurazione del DNS dell'endpoint privato di Azure](../../private-link/private-endpoint-dns.md). Assicurarsi di non creare record vuoti come preparazione per la configurazione del collegamento privato. I record DNS creati possono sostituire le impostazioni esistenti e influire sulla connettività con Monitoraggio di Azure.
 
   c.    Selezionare **Rivedi e crea**.
 
   d.    Superare la convalida. 
 
   e.    Selezionare **Crea**. 

    ![Screenshot di selezione di Crea endpoint privato 2](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

A questo punto è stato creato un nuovo endpoint privato connesso a questo AMPLS.

## <a name="configure-log-analytics"></a>Configurare Log Analytics

Accedere al portale di Azure. Nel menu delle risorse dell'area di lavoro Log Analytics è presente un elemento denominato **isolamento rete** sul lato sinistro. Da questo menu è possibile controllare due stati diversi.

![Isolamento rete LA](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Ambiti di collegamento privato di monitoraggio di Azure connessi
Tutti gli ambiti connessi a questa area di lavoro vengono visualizzati in questa schermata. La connessione agli ambiti (AMPLSs) consente il traffico di rete dalla rete virtuale connessa a ogni AMPLS per raggiungere questa area di lavoro. La creazione di una connessione tramite qui ha lo stesso effetto della configurazione nell'ambito, come è stato fatto per [connettere le risorse di monitoraggio di Azure](#connect-azure-monitor-resources). Per aggiungere una nuova connessione, selezionare **Aggiungi** e selezionare l'ambito del collegamento privato di monitoraggio di Azure. Selezionare **applica** per connettersi. Si noti che un'area di lavoro può connettersi a 5 oggetti AMPLS, come indicato in [restrizioni e limitazioni](#restrictions-and-limitations). 

### <a name="access-from-outside-of-private-links-scopes"></a>Accesso dall'esterno degli ambiti dei collegamenti privati
Le impostazioni nella parte inferiore di questa pagina controllano l'accesso dalle reti pubbliche, vale a dire reti non connesse tramite gli ambiti sopra elencati. **L'impostazione Consenti accesso alla rete pubblica per l'** inserimento **non** blocca l'inserimento di log da computer esterni agli ambiti connessi. **L'impostazione Consenti accesso alla rete pubblica per le query** **non** blocca le query provenienti da computer esterni agli ambiti. Sono incluse le query eseguite tramite cartelle di lavoro, dashboard, esperienze client basate su API, informazioni dettagliate nel portale di Azure e altro ancora. Le esperienze in esecuzione al di fuori dell'portale di Azure e la query Log Analytics dati devono essere in esecuzione anche all'interno di VNET collegati a privati.

### <a name="exceptions"></a>Eccezioni
La limitazione dell'accesso, come illustrato in precedenza, non si applica alla Azure Resource Manager e pertanto presenta le limitazioni seguenti:
* L'accesso ai dati, mentre il blocco/consentire le query dalle reti pubbliche si applica alla maggior parte dei Log Analytics esperienze, alcune esperienze eseguono query sui dati tramite Azure Resource Manager e pertanto non saranno in grado di eseguire query sui dati, a meno che non vengano applicate anche le impostazioni dei collegamenti privati al Gestione risorse (funzionalità presto disponibile). Sono incluse, ad esempio, le soluzioni di monitoraggio di Azure, le cartelle di lavoro e le informazioni dettagliate e il connettore LogicApp.
* Gestione dell'area Azure Resource Manager di lavoro: le modifiche apportate alla configurazione e all'impostazione dell'area di lavoro (inclusa l'attivazione o la disattivazione delle impostazioni di accesso Limitare l'accesso alla gestione delle aree di lavoro utilizzando i ruoli, le autorizzazioni, i controlli di rete e il controllo appropriati. Per altre informazioni, vedere [Ruoli, autorizzazioni e sicurezza di Monitoraggio di Azure](roles-permissions-security.md).

> [!NOTE]
> I log e le metriche caricati in un'area di lavoro tramite [Impostazioni di diagnostica](diagnostic-settings.md) passano attraverso un canale Microsoft privato protetto e non sono controllati da queste impostazioni.

### <a name="log-analytics-solution-packs-download"></a>Download di Log Analytics Solution Pack

Per consentire all'agente di Log Analytics di scaricare i pacchetti di soluzioni, aggiungere i nomi di dominio completi appropriati all'elenco di indirizzi consentiti del firewall. 


| Ambiente cloud | Risorsa agente | Porte | Direction |
|:--|:--|:--|:--|
|Azure Public     | scadvisorcontent.blob.core.windows.net         | 443 | In uscita
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  In uscita
|21Vianet per Azure Cina      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | In uscita

## <a name="configure-application-insights"></a>Configura Application Insights

Accedere al portale di Azure. Nel monitoraggio di Azure Application Insights risorsa componente, è una voce di menu **isolamento rete** sul lato sinistro. Da questo menu è possibile controllare due stati diversi.

![Isolamento rete intelligenza artificiale](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Innanzitutto, è possibile connettere questa risorsa Application Insights agli ambiti collegamento privato di Monitoraggio di Azure a cui si ha accesso. Selezionare **Aggiungi** e selezionare l' **ambito del collegamento privato di monitoraggio di Azure**. Selezionare Applica per connettersi. Tutti gli ambiti connessi vengono visualizzati in questa schermata. Se si effettua questa connessione, il traffico di rete nelle reti virtuali connesse raggiungerà il componente e avrà lo stesso effetto della connessione dall'ambito come per la [connessione delle risorse di monitoraggio di Azure](#connect-azure-monitor-resources). 

In secondo luogo, è possibile controllare il modo in cui questa risorsa può essere raggiunta dall'esterno degli ambiti collegamento privato elencati in precedenza. Se si imposta **Consenti l'accesso alla rete pubblica per l’inserimento** su **No**, i computer o gli SDK esterni agli ambiti connessi non possono caricare dati in questo componente. Se si imposta **Consenti l'accesso alla rete pubblica per le query** su **No**, i computer esterni agli ambiti non possono accedere ai dati in questa risorsa Application Insights. Questi dati includono l'accesso ai log APM, alle metriche e al flusso metriche attive, oltre a esperienze integrate come cartelle di lavoro, dashboard, esperienze client basate su API di query, informazioni dettagliate nel portale di Azure e altro ancora. 

Si noti che le esperienze di utilizzo esterne al portale devono essere eseguite anche all'interno della rete virtuale con collegamento privato che include i carichi di lavoro monitorati. 

Sarà necessario aggiungere le risorse che eseguono l’hosting dei carichi di lavoro monitorati al collegamento privato. La [documentazione](../../app-service/networking/private-endpoint.md) che illustra come eseguire questa operazione per Servizi app è disponibile qui.

Questo tipo di limitazione dell'accesso si applica solo ai dati nella risorsa Application Insights. Le modifiche alla configurazione, inclusa l'attivazione o la disattivazione di queste impostazioni di accesso, vengono gestite da Azure Resource Manager. In alternativa, limitare l'accesso a Resource Manager utilizzando i ruoli, le autorizzazioni, i controlli di rete e il controllo appropriati. Per altre informazioni, vedere [Ruoli, autorizzazioni e sicurezza di Monitoraggio di Azure](roles-permissions-security.md).

> [!NOTE]
> Per proteggere in modo completo Application Insights basato sull'area di lavoro, è necessario bloccare l'accesso alla risorsa Application Insights e all'area di lavoro Log Analytics sottostante.
>
> Per la diagnostica a livello di codice (Profiler/Debugger) è necessario fornire un proprio account di archiviazione per il supporto del collegamento privato. Ecco la [documentazione](../app/profiler-bring-your-own-storage.md) per informazioni su come eseguire questa operazione.

## <a name="use-apis-and-command-line"></a>Usare le API e la riga di comando

È possibile automatizzare il processo descritto in precedenza utilizzando modelli di Azure Resource Manager, REST e interfacce della riga di comando.

Per creare e gestire ambiti di collegamento privati, usare l' [API REST](/rest/api/monitor/private%20link%20scopes%20(preview)) o l'interfaccia della riga di comando di [Azure (AZ monitor private-link-scope)](/cli/azure/monitor/private-link-scope).

Per gestire l'accesso alla rete, usare i flag `[--ingestion-access {Disabled, Enabled}]` e `[--query-access {Disabled, Enabled}]` in [Aree di lavoro Log Analytics](/cli/azure/monitor/log-analytics/workspace) o [Componenti Application Insights](/cli/azure/ext/application-insights/monitor/app-insights/component).

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>Raccogli i log personalizzati e il collegamento di log su IIS privato

Gli account di archiviazione vengono usati nel processo di inserimento dei log personalizzati. Per impostazione predefinita, vengono usati gli account di archiviazione gestiti dal servizio. Tuttavia, per inserire i log personalizzati nei collegamenti privati, è necessario usare gli account di archiviazione personali e associarli alle aree di lavoro Log Analytics. Per ulteriori informazioni su come configurare tali account, utilizzare la [riga di comando](/cli/azure/monitor/log-analytics/workspace/linked-storage).

Per altre informazioni sull'uso di account di archiviazione personali, vedere [Account di archiviazione di proprietà del cliente per l'inserimento dei log](private-storage.md)

## <a name="restrictions-and-limitations"></a>Restrizioni e limitazioni

### <a name="ampls"></a>AMPLS
Quando si pianifica la configurazione del collegamento privato, l'oggetto AMPLS presenta diversi limiti da considerare:

* Un VNet può connettersi solo a un oggetto AMPLS. Ciò significa che l'oggetto AMPLS deve fornire l'accesso a tutte le risorse di monitoraggio di Azure a cui VNet deve avere accesso.
* Una risorsa di monitoraggio di Azure (area di lavoro o componente Application Insights) può connettersi al massimo 5 AMPLSs.
* Un oggetto AMPLS può connettersi alla maggior parte delle risorse di monitoraggio di Azure 50.
* Un oggetto AMPLS può connettersi al massimo da 10 endpoint privati.

Vedere [considerare i limiti](#consider-limits) per una revisione più approfondita di questi limiti e come pianificare la configurazione del collegamento privato di conseguenza.

### <a name="agents"></a>Agenti

Le versioni più recenti degli agenti Windows e Linux devono essere usate nelle reti private per consentire l'inserimento sicuro per le aree di lavoro Log Analytics. Le versioni precedenti non possono caricare i dati di monitoraggio in una rete privata.

**Agente Windows di Log Analytics**

Usare l'agente di Log Analytics versione 10.20.18038.0 o successiva.

**Agente Linux di Log Analytics**

Usare la versione dell'agente 1.12.25 o successiva. Se non è possibile, eseguire i comandi seguenti nella macchina virtuale.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Portale di Azure

Per usare le esperienze del portale di Monitoraggio di Azure, ad esempio Application Insights e Log Analytics, è necessario consentire l'accesso alle estensioni del portale di Azure e di Monitoraggio di Azure nelle reti private. Aggiungere i [tag del servizio](../../firewall/service-tags.md) **AzureActiveDirectory**, **AzureResourceManager**, **AzureFrontDoor. FirstParty** e **AzureFrontDoor. frontend** al gruppo di sicurezza di rete.

### <a name="programmatic-access"></a>Accesso a livello di codice

Per usare l’API REST, la [CLI](/cli/azure/monitor) o PowerShell con Monitoraggio di Azure nelle reti private,  aggiungere le [tag del servizio](../../virtual-network/service-tags-overview.md) **AzureActiveDirectory** e **AzureResourceManager** al firewall.

L'aggiunta di questi tag consente di eseguire azioni come l'esecuzione di query sui dati di log, la creazione e la gestione di aree di lavoro Log Analytics e di componenti di intelligenza artificiale.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Download di SDK di Application Insights da una rete per la distribuzione di contenuti

Integrare il codice JavaScript nello script in modo che il browser non tenti di scaricare il codice da una rete CDN. Un esempio è disponibile in [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>Impostazioni DNS del browser

Se ci si connette alle risorse di monitoraggio di Azure tramite un collegamento privato, il traffico verso queste risorse deve passare attraverso l'endpoint privato configurato nella rete. Per abilitare l'endpoint privato, aggiornare le impostazioni DNS come illustrato in [connettersi a un endpoint privato](#connect-to-a-private-endpoint). Alcuni browser usano le proprie impostazioni DNS anziché quelle impostate. Il browser potrebbe tentare di connettersi agli endpoint pubblici di monitoraggio di Azure e ignorare completamente il collegamento privato. Verificare che le impostazioni del browser non sostituiscano o memorizzano nella cache le impostazioni DNS precedenti. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull' [archiviazione privata](private-storage.md)