---
title: Continuità aziendale e ripristino di emergenza
description: Progettare la strategia per proteggere i dati, recuperare rapidamente da eventi di interruzione, ripristinare le risorse richieste dalle funzioni aziendali critiche e mantenere la continuità aziendale per App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: f974a99c59b19b5df7bf6ffcc66c2dc133743f0a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790540"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Continuità aziendale e ripristino di emergenza per App per la logica di Azure

Per ridurre l'impatto e gli effetti che gli eventi imprevedibili hanno sull'azienda e sui clienti, assicurarsi di avere [  una](https://en.wikipedia.org/wiki/Disaster_recovery) soluzione di ripristino di emergenza in modo da poter proteggere i dati, ripristinare rapidamente le risorse che supportano le funzioni aziendali critiche e mantenere in esecuzione le operazioni per mantenere la [ *continuità* aziendale .](https://en.wikipedia.org/wiki/Business_continuity_planning) Ad esempio, le interruzioni possono includere interruzioni, perdite nell'infrastruttura sottostante o componenti come risorse di archiviazione, rete o calcolo, errori irreversibili delle applicazioni o persino una perdita completa del data center. Con una soluzione di continuità aziendale e ripristino di emergenza (BCDR), l'azienda o l'organizzazione può rispondere più rapidamente alle interruzioni, pianificate o non pianificate e ridurre i tempi di inattività per i clienti.

Questo articolo fornisce indicazioni e strategie bcdr che è possibile applicare quando si compilano flussi di lavoro automatizzati [usando](../logic-apps/logic-apps-overview.md)App per la logica di Azure . I flussi di lavoro delle app per la logica consentono di integrare e orchestrare più facilmente i dati tra app, servizi cloud e sistemi locali riducendo la quantità di codice da scrivere. Quando si pianifica bcdr, assicurarsi di prendere in considerazione non solo le app per la logica, ma anche le risorse di Azure usate con le app per la logica:

* [Connessioni](../connectors/apis-list.md) create da app per la logica ad altre app, servizi e sistemi. Per altre informazioni, vedere [Connessioni alle risorse più](#resource-connections) avanti in questo argomento.

* [Gateway dati locali che](../logic-apps/logic-apps-gateway-connection.md) sono risorse di Azure create e usate nelle app per la logica per accedere ai dati nei sistemi locali. Ogni risorsa gateway rappresenta [un'installazione separata del gateway dati](../logic-apps/logic-apps-gateway-install.md) in un computer locale. Per altre informazioni, vedere [Gateway dati locali](#on-premises-data-gateways) più avanti in questo argomento.

* [Account di](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) integrazione in cui si definiscono e archiviano gli artefatti che le app per la logica usano per scenari di integrazione aziendale [business-to-business (B2B).](../logic-apps/logic-apps-enterprise-integration-overview.md) Ad esempio, è possibile configurare [il ripristino di emergenza tra aree](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)per gli account di integrazione .

* [Ambienti del servizio di integrazione (ISE) in cui](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) si creano app per la logica che vengono eseguite in un'istanza di runtime di App per la logica isolata all'interno di una rete virtuale di Azure. Queste app per la logica possono quindi accedere alle risorse protette da un firewall in tale rete virtuale.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Località primarie e secondarie

Ogni app per la logica deve specificare il percorso da usare per la distribuzione. Questa località è un'area pubblica in Azure multi-tenant globale, ad esempio "Stati Uniti occidentali" o un ambiente del servizio di integrazione (ISE) creato e distribuito in precedenza in una rete virtuale di Azure. L'esecuzione di app per la logica in un ISE è simile all'esecuzione di app per la logica in un'area di Azure globale, il che significa che la strategia di ripristino di emergenza può essere applicata a entrambi gli scenari. Tuttavia, gli ISE hanno altre considerazioni, ad esempio la configurazione dell'accesso alle risorse disponibili solo per gli ISE.

> [!NOTE]
> Se l'app per la logica funziona anche con elementi B2B, ad esempio partner commerciali, contratti, schemi, mappe e certificati, archiviati in un account di integrazione, sia l'account di integrazione che le app per la logica devono specificare la stessa posizione.

Questa strategia di ripristino di emergenza è incentrata sulla configurazione dell'app per la logica primaria per il [*failover*](https://en.wikipedia.org/wiki/Failover) in un'app per la logica di standby o di backup in una posizione alternativa in cui App per la logica di Azure è disponibile. In questo modo, se il database primario subisce perdite, interruzioni o errori, il database secondario può assumere il lavoro. Questa strategia richiede che l'app per la logica secondaria e le risorse dipendenti siano già distribuite e pronte nella posizione alternativa.

Se si seguono procedure DevOps [](../azure-resource-manager/management/overview.md) ottimali, si usano già Azure Resource Manager per definire e distribuire le app per la logica e le relative risorse dipendenti. Resource Manager modelli consentono di usare una singola definizione di distribuzione e quindi di usare i file di parametri per fornire i valori di configurazione da usare per ogni destinazione di distribuzione. Questa funzionalità significa che è possibile distribuire la stessa app per la logica in ambienti diversi, ad esempio sviluppo, test e produzione. È anche possibile distribuire la stessa app per la logica in aree o ISE di Azure diverse, che supporta strategie di ripristino di emergenza che usano [aree abbinate.](../best-practices-availability-paired-regions.md)

Per la strategia di failover, le app per la logica e i percorsi devono soddisfare questi requisiti:

* L'istanza dell'app per la logica secondaria ha accesso alle stesse app, servizi e sistemi dell'istanza dell'app per la logica primaria.

* Entrambe le istanze dell'app per la logica hanno lo stesso tipo di host. Quindi, entrambe le istanze vengono distribuite in aree in Azure multi-tenant globale oppure entrambe le istanze vengono distribuite in ISE, che consentono alle app per la logica di accedere direttamente alle risorse in una rete virtuale di Azure. Per le procedure consigliate e altre informazioni sulle aree abbinate per BCDR, vedere Continuità aziendale e ripristino di emergenza [: Aree abbinate di Azure](../best-practices-availability-paired-regions.md).

  Ad esempio, sia il percorso primario che quello secondario devono essere ISE quando l'app per la logica primaria viene eseguita in un ise e usa connettori con versione [ISE,](../connectors/managed.md#ise-connectors)azioni HTTP per chiamare le risorse nella rete virtuale di Azure o entrambi. In questo scenario, anche l'app per la logica secondaria deve avere una configurazione simile nella posizione secondaria come app per la logica primaria.

  > [!NOTE]
  > Per scenari più avanzati, è possibile combinare sia Azure multi-tenant che ISE come località. Tuttavia, assicurarsi di considerare e comprendere le differenze tra l'esecuzione delle app per la logica in un ambiente ISE e [multi-tenant di Azure.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)

* Se si usano ise, [assicurarsi che](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) siano scalati orizzontalmente o che la capacità sia sufficiente per gestire il carico.

#### <a name="example-multi-tenant-azure"></a>Esempio: Azure multi-tenant

Questo esempio illustra le istanze dell'app per la logica primaria e secondaria, distribuite in aree separate in Azure multi-tenant globale per questo scenario. Un singolo modello [Resource Manager definisce](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) sia le istanze dell'app per la logica che le risorse dipendenti richieste da tali app per la logica. I file di parametri separati specificano i valori di configurazione da usare per ogni percorso di distribuzione:

![Istanze dell'app per la logica primaria e secondaria in posizioni separate](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Esempio: Ambiente del servizio di integrazione

Questo esempio illustra le istanze precedenti dell'app per la logica primaria e secondaria, ma distribuite in ISE separate. Un singolo modello Resource Manager definisce sia le istanze dell'app per la logica, le risorse dipendenti richieste da tali app per la logica che gli ISE come percorsi di distribuzione. I file di parametri separati definiscono i valori di configurazione da usare per la distribuzione in ogni posizione:

![App per la logica primaria e secondaria in posizioni diverse](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Connessioni alle risorse

App per la logica di Azure offre [trigger](../connectors/apis-list.md) e azioni predefiniti, oltre a centinaia di connettori gestiti che l'app per la logica può usare per lavorare con altre app, servizi, sistemi e altre risorse, ad esempio account Archiviazione di Azure, database SQL Server, account di posta elettronica aziendali o dell'istituto di istruzione e così via. Se l'app per la logica deve accedere a queste risorse, si creano connessioni che autenticano l'accesso a queste risorse. Ogni connessione è una risorsa di Azure separata che esiste in una posizione specifica e non può essere usata dalle risorse in altre posizioni.

Per la strategia di ripristino di emergenza, prendere in considerazione le posizioni in cui sono presenti risorse dipendenti rispetto alle istanze dell'app per la logica:

* L'istanza primaria e le risorse dipendenti esistono in posizioni diverse. In questo caso, l'istanza secondaria può connettersi alle stesse risorse o agli stessi endpoint dipendenti. È tuttavia consigliabile creare connessioni specifiche per l'istanza secondaria. In questo modo, se la posizione primaria diventa non disponibile, le connessioni secondarie non sono interessate.

  Si supponga, ad esempio, che l'app per la logica principale si connetta a un servizio esterno, ad esempio Salesforce. In genere, la disponibilità e la posizione del servizio esterno sono indipendenti dalla disponibilità dell'app per la logica. In questo caso, l'istanza secondaria può connettersi allo stesso servizio, ma deve avere una propria connessione.

* Sia l'istanza primaria che le risorse dipendenti sono presenti nella stessa posizione. In questo caso, le risorse dipendenti devono avere backup o versioni replicate in un percorso diverso in modo che l'istanza secondaria possa comunque accedere a tali risorse.

  Si supponga, ad esempio, che l'app per la logica primaria si connetta a un servizio nella stessa località o area, ad esempio database SQL di Azure. Se l'intera area non è più disponibile, è probabile che database SQL di Azure servizio in tale area non sia disponibile. In questo caso, si vuole che l'istanza secondaria usi un database replicato o di backup insieme a una connessione separata a tale database.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>Gateway dati locali

Se l'app per la logica viene eseguita in Azure multi-tenant e deve accedere alle risorse locali, ad esempio i database SQL Server, è necessario installare il gateway dati [locale](../logic-apps/logic-apps-gateway-install.md) in un computer locale. È quindi possibile creare una risorsa gateway dati nel portale di Azure in modo che l'app per la logica possa usare il gateway quando si crea una connessione alla risorsa.

La risorsa gateway dati è associata a una località o a un'area di Azure, proprio come la risorsa dell'app per la logica. Nella strategia di ripristino di emergenza assicurarsi che il gateway dati rimanga disponibile per l'uso da parte dell'app per la logica. È possibile [abilitare la disponibilità elevata per il gateway](../logic-apps/logic-apps-gateway-install.md#high-availability) quando sono presenti più installazioni del gateway.

> [!NOTE]
> Se l'app per la logica viene eseguita in un ambiente del servizio di integrazione (ISE) e usa solo connettori con versione ISE per le origini dati locali, non è necessario il gateway dati perché i connettori ISE forniscono l'accesso diretto alla risorsa locale.
>
> Se non è disponibile alcun connettore con versione ISE per la risorsa locale desiderata, l'app per la logica può comunque creare la connessione usando un connettore non ISE, che viene eseguito in Azure multi-tenant globale, non nell'ISE. Questa connessione richiede tuttavia il gateway dati locale.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Ruoli attivo-attivo e attivo-passivo

È possibile configurare i percorsi primario e secondario in modo che le istanze dell'app per la logica in queste posizioni possano svolgere questi ruoli:

| Ruolo primario-secondario | Descrizione |
|------------------------|-------------|
| *Attivo-attivo* | Le istanze dell'app per la logica primaria e secondaria in entrambe le posizioni gestiscono attivamente le richieste seguendo uno di questi modelli: <p><p>- *Bilanciamento del* carico: entrambe le istanze sono in ascolto su un endpoint e bilanciano il carico del traffico verso ogni istanza in base alle esigenze. <p>- *Consumer concorrenti:* è possibile fare in modo che entrambe le istanze fungono da consumer concorrenti in modo che le istanze competono per i messaggi provenienti da una coda. Se un'istanza ha esito negativo, l'altra istanza assume il carico di lavoro. |
| *Modalità attiva-passiva* | L'istanza dell'app per la logica primaria gestisce attivamente l'intero carico di lavoro, mentre l'istanza secondaria è passiva (disabilitata o inattiva). Il database secondario attende un segnale che il database primario non è disponibile o non funziona a causa di un'interruzione o di un errore e assume il carico di lavoro come istanza attiva. |
| Combinazione | Alcune app per la logica svolgono un ruolo attivo-attivo, mentre altre app per la logica svolgono un ruolo attivo-passivo. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Esempi di attività attive

Questi esempi illustrano la configurazione attivo-attivo in cui entrambe le istanze dell'app per la logica gestiscono attivamente richieste o messaggi. Un altro sistema o servizio distribuisce le richieste o i messaggi tra istanze, ad esempio una delle opzioni seguenti:

* Un servizio di bilanciamento del carico "fisico", ad esempio un componente hardware che indirizza il traffico

* Un servizio di bilanciamento del carico "soft", ad esempio [Azure Load Balancer](../load-balancer/load-balancer-overview.md) [o Azure API Management](../api-management/api-management-key-concepts.md). Con API Management, è possibile specificare criteri che determinano come bilanciare il carico del traffico in ingresso. In caso contrario, è possibile usare un servizio che supporta il rilevamento dello [stato,](../service-bus-messaging/service-bus-messaging-overview.md)ad esempio bus di servizio di Azure .

  Anche se questo esempio illustra Azure Load Balancer, è possibile usare l'opzione più adatta alle esigenze dello scenario:

  ![Configurazione "attiva-attiva" che usa un servizio di bilanciamento del carico o con stato](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Ogni istanza dell'app per la logica funge da consumer e ha entrambe le istanze in competizione per i messaggi provenienti da una coda:

  ![Configurazione "attiva-attiva" che usa "consumer concorrenti"](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Esempi di tipo attivo-passivo

Questo esempio illustra la configurazione attiva-passiva in cui l'istanza dell'app per la logica primaria è attiva in una posizione, mentre l'istanza secondaria rimane inattiva in un'altra posizione. Se si verifica un'interruzione o un errore nel database primario, è possibile fare in modo che un operatore eserviti uno script che attivi il database secondario per il carico di lavoro.

![Configurazione "attiva-passiva" che usa "consumer concorrenti"](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Combinazione con attivo-attivo e attivo-passivo

Questo esempio illustra una configurazione combinata in cui la posizione primaria ha entrambe le istanze di app per la logica attive, mentre la posizione secondaria ha istanze di app per la logica attivo-passivo. Se si verifica un'interruzione o un errore nella posizione primaria, l'app per la logica attiva nella posizione secondaria, che sta già gestendo un carico di lavoro parziale, può assumere il controllo dell'intero carico di lavoro.

* Nella posizione primaria, un'app per la logica attiva è in ascolto di una coda bus di servizio di Azure per i messaggi, mentre un'altra app per la logica attiva controlla la presenza di messaggi di posta elettronica usando un trigger di polling di Office 365 Outlook.

* Nella posizione secondaria un'app per la logica attiva funziona con l'app per la logica nella posizione primaria, in ascolto e in competizione per i messaggi dalla stessa coda del bus di servizio. Nel frattempo, un'app per la logica passiva inattiva attende in  standby per verificare la presenza di messaggi di posta elettronica quando la posizione primaria non è più disponibile, ma è disabilitata per evitare la rilettura dei messaggi di posta elettronica.

![Combinazione "attivo-passivo" e "attivo-passivo" che usa trigger di ricorrenza](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Stato e cronologia dell'app per la logica

Quando l'app per la logica viene attivata e viene avviata l'esecuzione, lo stato dell'app viene archiviato nello stesso percorso in cui l'app è stata avviata e non è trasferibile in un'altra posizione. Se si verifica un errore o un'interruzione, tutte le istanze del flusso di lavoro in corso vengono abbandonate. Quando sono state impostate una posizione primaria e una secondaria, le nuove istanze del flusso di lavoro iniziano a essere eseguite nella posizione secondaria.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Ridurre le istanze in corso abbandonate

Per ridurre al minimo il numero di istanze del flusso di lavoro in corso abbandonate, è possibile scegliere tra vari modelli di messaggio che è possibile implementare, ad esempio:

* [Modello di fisso per lo slittamento del routing](/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Questo modello di messaggio aziendale suddivide un processo aziendale in fasi più piccole. Per ogni fase, si configura un'app per la logica che gestisce il carico di lavoro per tale fase. Per comunicare tra loro, le app per la logica usano un protocollo di messaggistica asincrona, ad esempio bus di servizio di Azure code o argomenti. Quando si divide un processo in fasi più piccole, si riduce il numero di processi aziendali che potrebbero rimanere bloccati in un'istanza di app per la logica non riuscita. Per informazioni più generali su questo modello, vedere [Modelli di integrazione aziendale - Distinta di routing.](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html)

  Questo esempio illustra un modello di distribuzione in cui ogni app per la logica rappresenta una fase e usa una coda del bus di servizio per comunicare con l'app per la logica successiva nel processo.

  ![Suddividere un processo aziendale in fasi rappresentate da app per la logica, che comunicano tra loro usando bus di servizio di Azure code](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Se entrambe le istanze dell'app per la logica primaria e [](/azure/architecture/patterns/competing-consumers) secondaria seguono lo stesso modello di distribuzione nella propria posizione, è possibile implementare il modello di consumer concorrenti configurando ruoli [attivo-attivo](#roles) per tali istanze.

* [Modello di gestione dei processi (broker)](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Blocco di visualizzazione senza modello di timeout](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Accesso alla cronologia di trigger ed esecuzioni

Per ottenere altre informazioni sulle esecuzioni del flusso di lavoro precedenti dell'app per la logica, è possibile esaminare il trigger e la cronologia delle esecuzioni dell'app. La cronologia di esecuzione della cronologia di un'app per la logica viene archiviata nella stessa posizione o area in cui è stata eseguita l'app per la logica, pertanto non è possibile eseguire la migrazione di questa cronologia in un percorso diverso. Se l'istanza primaria esegue il fails over in un'istanza secondaria, è possibile accedere solo al trigger di ogni istanza ed eseguire la cronologia nelle rispettive posizioni in cui sono stati eseguiti tali istanze. Tuttavia, è possibile ottenere informazioni indipendenti dalla posizione sulla cronologia dell'app per la logica configurando le app per la logica per inviare eventi di diagnostica a un'area di lavoro Di Azure Log Analytics. È quindi possibile esaminare l'integrità e la cronologia tra le app per la logica eseguite in più posizioni.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Indicazioni sul tipo di trigger

Il tipo di trigger che si usa nelle app per la logica determina le opzioni per la configurazione delle app per la logica in più posizioni nella strategia di ripristino di emergenza. Ecco i tipi di trigger disponibili che è possibile usare nelle app per la logica:

* [Trigger di ricorrenza](#recurrence-trigger)
* [Trigger di polling](#polling-trigger)
* [Trigger di richiesta](#request-trigger)
* [Trigger webhook](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Trigger Recurrence

Il trigger **Ricorrenza** è indipendente da qualsiasi servizio o endpoint specifico e viene attivato esclusivamente in base a una pianificazione specificata e ad altri criteri, ad esempio:

* Frequenza e intervallo fissi, ad esempio ogni 10 minuti
* Una pianificazione più avanzata, ad esempio l'ultimo lunedì di ogni mese alle 17:00

Quando l'app per la logica viene avviata con un trigger Ricorrenza, è necessario configurare le istanze dell'app per la logica primaria e secondaria con i [ruoli attivo-passivo.](#roles) Per ridurre  l'obiettivo del tempo di ripristino (RTO), che fa riferimento alla durata di destinazione per il ripristino di un processo aziendale dopo un'interruzione o un'emergenza, è possibile configurare le istanze dell'app per la logica con una combinazione di ruoli [attivo-passivo](#roles) e ruoli [passivo-attivo.](#roles) In questa configurazione la pianificazione viene suddivisa tra più posizioni.

Si supponga, ad esempio, di avere un'app per la logica che deve essere eseguita ogni 10 minuti. È possibile configurare le app per la logica e i percorsi in modo che se la posizione primaria diventa non disponibile, la posizione secondaria può assumere il controllo del lavoro:

![Combinazione "Attivo-passivo" e "Passivo-attivo" che usa i trigger di ricorrenza](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* Nella posizione primaria configurare i ruoli [attivo-passivo per](#roles) queste app per la logica:

  * Per  l'app per la logica abilitata attiva, impostare il trigger Ricorrenza in modo che inizi all'inizio dell'ora e si ripeta ogni 20 minuti, ad esempio alle 9:00, alle 9:20 e così via.

  * Per  l'app per la logica passiva disabilitata, impostare il trigger Ricorrenza sulla stessa pianificazione, ma iniziare da 10 minuti dopo l'ora e ripetere ogni 20 minuti, ad esempio 9:10, 9:30 e così via.

* Nella posizione secondaria configurare [passivo-attivo per](#roles) queste app per la logica:

  * Per  l'app per la logica passiva disabilitata, impostare il trigger Ricorrenza sulla stessa pianificazione dell'app per la logica attiva nella posizione primaria, che si trova all'inizio dell'ora e ripetere ogni 20 minuti, ad esempio 9:00 AM, 9:10 AM e così via.

  * Per  l'app per la logica abilitata attiva, impostare il trigger Ricorrenza sulla stessa pianificazione dell'app per la logica passiva nella posizione primaria, che deve iniziare a 10 minuti dall'ora e ripetere ogni 20 minuti, ad esempio alle 9:10, alle 9:20 e così via.

A questo punto, se si verifica un evento di interruzione nella posizione primaria, attivare l'app per la logica passiva nella posizione alternativa. In questo modo, se la ricerca dell'errore richiede tempo, questa configurazione limita il numero di ricorrenze perse durante tale ritardo.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Trigger di polling

Per controllare regolarmente se i nuovi dati per l'elaborazione sono disponibili da un servizio o un endpoint specifico, l'app per la logica può usare un trigger di *polling* che chiama ripetutamente il servizio o l'endpoint in base a una pianificazione di ricorrenza fissa. I dati forniti dal servizio o dall'endpoint possono avere uno dei tipi seguenti:

* Dati statici, che descrivono i dati sempre disponibili per la lettura
* Dati volatili, che descrivono i dati che non sono più disponibili dopo la lettura

Per evitare di leggere ripetutamente gli stessi dati, l'app per la logica deve ricordare quali dati sono stati letti in precedenza mantenendo lo stato sul lato client o sul lato server, servizio o sistema.

* Le app per la logica che usano lo stato sul lato client usano trigger che possono mantenere lo stato.

  Ad esempio, un trigger che legge un nuovo messaggio da una posta in arrivo richiede che il trigger possa ricordare il messaggio letto più di recente. In questo modo, il trigger avvia l'app per la logica solo quando arriva il messaggio successivo non letto.

* Le app per la logica che funzionano con il server, il servizio o lo stato sul lato sistema usano valori di proprietà o impostazioni presenti sul lato server, servizio o sistema.

  Ad esempio, un trigger basato su query che legge una riga da un database richiede che la riga abbia una colonna `isRead` impostata su `FALSE` . Ogni volta che il trigger legge una riga, l'app per la logica aggiorna tale riga modificando la `isRead` colonna da `FALSE` a `TRUE` .

  Questo approccio lato server funziona in modo analogo per le code o gli argomenti del bus di servizio con semantica di accodamento in cui un trigger può leggere e bloccare un messaggio mentre l'app per la logica elabora il messaggio. Al termine dell'elaborazione dell'app per la logica, il trigger elimina il messaggio dalla coda o dall'argomento.

Dal punto di vista del ripristino di emergenza, quando si configurano le istanze primarie e secondarie dell'app per la logica, assicurarsi di tenere conto di questi comportamenti a seconda che l'app per la logica tiene traccia dello stato sul lato client o sul lato server:

* Per un'app per la logica che funziona con lo stato lato client, assicurarsi che l'app per la logica non letta lo stesso messaggio più di una volta. Solo una posizione può avere un'istanza di app per la logica attiva in un momento specifico. Assicurarsi che l'istanza dell'app per la logica nel percorso alternativo sia inattiva o disabilitata fino a quando non viene eseguito il backup dell'istanza primaria nella posizione alternativa.

  Ad esempio, il trigger di Office 365 Outlook mantiene lo stato lato client e tiene traccia del timestamp del messaggio di posta elettronica letto più di recente per evitare la lettura di un duplicato.

* Per un'app per la logica che funziona con lo stato lato server, è possibile configurare le istanze dell'app per la logica per svolgere ruoli [attivo-attivo](#roles) in cui funzionano come consumer concorrenti o ruoli [attivo-passivo](#roles) in cui l'istanza alternativa attende fino a quando l'istanza primaria non viene eseguita nel percorso alternativo.

  Ad esempio, la lettura da una coda di messaggi, ad esempio una coda bus di servizio di Azure, usa lo stato sul lato server perché il servizio di accodamento mantiene i blocchi sui messaggi per impedire ad altri client di leggere gli stessi messaggi.

  > [!NOTE]
  > Se l'app per la logica deve leggere i messaggi in un ordine specifico, ad esempio da una coda del bus di servizio, è possibile usare il modello di consumer concorrente, ma solo in combinazione con le sessioni del bus di servizio, noto anche come modello di flusso [  sequenziale.](/azure/architecture/patterns/sequential-convoy) In caso contrario, è necessario configurare le istanze dell'app per la logica con i ruoli attivo-passivo.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Trigger di richiesta

Il trigger **Richiesta** rende l'app per la logica chiamabile da altre app, servizi e sistemi e viene in genere usata per fornire queste funzionalità:

* UN'API REST diretta per l'app per la logica che altri utenti possono chiamare

  Ad esempio, usare il trigger Richiesta per avviare l'app per la logica in modo che altre app per la logica possano chiamare il trigger usando l'azione Chiama flusso di **lavoro - App per la** logica.

* Un [webhook o](#webhook-trigger) un meccanismo di callback per l'app per la logica

* Modo in cui è possibile eseguire manualmente operazioni utente o routine per chiamare l'app per la logica, ad esempio usando uno script di PowerShell che esegue un'attività specifica

Dal punto di vista del ripristino di emergenza, il trigger richiesta è un ricevitore passivo perché l'app per la logica non fa alcuna operazione e attende fino a quando un altro servizio o sistema non chiama in modo esplicito il trigger. Come endpoint passivo, è possibile configurare le istanze primarie e secondarie nei modi seguenti:

* [Attivo-attivo:](#roles)entrambe le istanze gestiscono attivamente richieste o chiamate. Il chiamante o il router bilancia o distribuisce il traffico tra tali istanze.

* [Attivo-passivo:](#roles)solo l'istanza primaria è attiva e gestisce tutto il lavoro, mentre l'istanza secondaria attende fino a quando l'istanza primaria non verifica un'interruzione o un errore. Il chiamante o il router determina quando chiamare l'istanza secondaria.

Come architettura consigliata, è possibile usare Azure API Management come proxy per le app per la logica che usano trigger di richiesta. API Management offre resilienza predefinita tra più impostazioni internazionali e la possibilità di instradare [il traffico tra più endpoint.](../api-management/api-management-howto-deploy-multi-region.md)

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Trigger di webhook

Un *trigger webhook* consente all'app per la logica di sottoscrivere un servizio passando un *URL di callback* a tale servizio. L'app per la logica può quindi restare in ascolto e attendere che si verifica un evento specifico nell'endpoint di servizio. Quando si verifica l'evento, il servizio chiama il trigger webhook usando l'URL di callback, che quindi esegue l'app per la logica. Se abilitato, il trigger webhook sottoscrive il servizio. Se disabilitato, il trigger annulla la sottoscrizione al servizio.

Dal punto di vista del ripristino di emergenza, configurare istanze primarie e secondarie che usano trigger webhook per svolgere ruoli attivo-passivo perché solo un'istanza deve ricevere eventi o messaggi dall'endpoint sottoscritto.

## <a name="assess-primary-instance-health"></a>Valutare l'integrità dell'istanza primaria

Per il funzionamento della strategia di ripristino di emergenza, la soluzione deve essere in grado di eseguire queste attività:

* [Controllare la disponibilità dell'istanza primaria](#check-primary-availability)
* [Monitorare l'integrità dell'istanza primaria](#monitor-primary-health)
* [Attivare l'istanza secondaria](#activate-secondary)

Questa sezione descrive una soluzione che è possibile usare in modo definitivo o come base per la propria progettazione. Ecco una panoramica visiva di alto livello per questa soluzione:

![Creare un'app per la logica watchdog che monitora un'app per la logica di controllo dell'integrità nella posizione primaria](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Controllare la disponibilità dell'istanza primaria

Per determinare se l'istanza primaria è disponibile, in esecuzione e in grado di funzionare, è possibile creare un'app per la logica "controllo integrità" che si trova nella stessa posizione dell'istanza primaria. È quindi possibile chiamare questa app di controllo integrità da una posizione alternativa. Se l'app di controllo integrità risponde correttamente, l'infrastruttura sottostante per il servizio App per la logica di Azure in tale area è disponibile e funzionante. Se l'app di controllo integrità non risponde, è possibile presupporre che la posizione non sia più integra.

Per questa attività, creare un'app per la logica di controllo dell'integrità di base che esegua queste attività:

1. Riceve una chiamata dall'app watchdog usando il trigger Richiesta.

1. Rispondere con uno stato che indica se l'app per la logica selezionata funziona ancora usando l'azione Risposta.

   > [!IMPORTANT]
   > L'app per la logica di controllo dell'integrità deve usare un'azione Risposta in modo che l'app risponda in modo sincrono, non in modo asincrono.

1. Facoltativamente, per determinare ulteriormente se la posizione primaria è integra, è possibile fattore di integrità di tutti gli altri servizi che interagiscono con l'app per la logica di destinazione in questa posizione. È sufficiente espandere l'app per la logica di controllo dell'integrità per valutare l'integrità anche per questi altri servizi.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Creare un'app per la logica watchdog

Per monitorare l'integrità dell'istanza primaria e chiamare l'app per la logica di controllo dell'integrità, creare un'app per la logica "watchdog" in un *percorso alternativo.* Ad esempio, è possibile configurare l'app per la logica watchdog in modo che se la chiamata alla logica di controllo dell'integrità ha esito negativo, il watchdog può inviare un avviso al team operativo in modo che possa analizzare l'errore e il motivo per cui l'istanza primaria non risponde.

> [!IMPORTANT]
> Assicurarsi che l'app per la logica watchdog si trova in una *posizione diversa da quella primaria.* Se si verificano problemi nel servizio App per la logica nella posizione primaria, l'app per la logica watchdog potrebbe non essere eseguita.

Per questa attività, nella posizione secondaria creare un'app per la logica watchdog che esegua queste attività:

1. Eseguire in base a una ricorrenza fissa o pianificata usando il trigger Ricorrenza.

   È possibile impostare la ricorrenza su un valore inferiore al livello di tolleranza per l'obiettivo RTO (Recovery Time Objective).

1. Chiamare l'app per la logica di controllo dell'integrità nel percorso primario usando l'azione HTTP, ad esempio:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Attivare l'istanza secondaria

Per attivare automaticamente l'istanza secondaria, è possibile creare un'app per la logica che chiama l'API di gestione, ad esempio il connettore [Azure Resource Manager,](/connectors/arm/) per attivare le app per la logica appropriate nella posizione secondaria. È possibile espandere l'app watchdog per chiamare questa app per la logica di attivazione dopo un numero specifico di errori.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Raccogliere dati di diagnostica

È possibile configurare la registrazione per le esecuzioni dell'app per la logica e inviare i dati di diagnostica risultanti a servizi come Archiviazione di Azure, Hub eventi di Azure e Azure Log Analytics per altre operazioni di gestione ed elaborazione.

* Se si vogliono usare questi dati con Azure Log Analytics, è possibile rendere disponibili i dati  sia per la posizione primaria che per quella secondaria configurando le impostazioni di diagnostica dell'app per la logica e inviando i dati a più aree di lavoro Log Analytics. Per altre informazioni, vedere [Configurare i log Monitoraggio di Azure e raccogliere dati di diagnostica per App per la logica di Azure](../logic-apps/monitor-logic-apps-log-analytics.md).

* Se si vogliono inviare i dati a Archiviazione di Azure o Hub eventi di Azure, è possibile rendere disponibili i dati sia per la posizione primaria che per la posizione secondaria configurando la ridondanza geografica. Per altre informazioni, vedere questi articoli:<p>

  * [Archiviazione BLOB di Azure ripristino di emergenza e failover dell'account](../storage/common/storage-disaster-recovery-guidance.md)
  * [Hub eventi di Azure ripristino di emergenza geografico](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica della resilienza per Azure](/azure/architecture/framework/resiliency/overview)
* [Elenco di controllo per la resilienza per servizi di Azure specifici](/azure/architecture/checklist/resiliency-per-service)
* [Gestione dei dati per la resilienza in Azure](/azure/architecture/framework/resiliency/data-management)
* [Backup e ripristino di emergenza per le applicazioni Azure](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Eseguire il ripristino dopo un'interruzione di servizio a livello di area](/azure/architecture/resiliency/recovery-loss-azure-region)
* [Contratti di servizio Microsoft per i servizi di Azure](https://azure.microsoft.com/support/legal/sla/)
