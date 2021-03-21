---
title: Modelli di prezzi & di fatturazione
description: Panoramica sul funzionamento dei modelli di prezzi e fatturazione in app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 2b37308bcbcd489876c21dce56878de7e0daf545
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699029"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Modelli di prezzi e fatturazione per app per la logica di Azure

[App](../logic-apps/logic-apps-overview.md) per la logica di Azure consente di creare ed eseguire flussi di lavoro di integrazione automatizzati che possono essere ridimensionati nel cloud. Questo articolo descrive il funzionamento dei modelli di fatturazione e prezzi per il servizio app per la logica e le risorse correlate. Per prezzi specifici, vedere [prezzi di app](https://azure.microsoft.com/pricing/details/logic-apps)per la logica. Per informazioni su come pianificare, gestire e monitorare i costi, vedere [pianificare e gestire i costi per le app per la logica di Azure](plan-manage-costs.md).

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>Prezzi multi-tenant

Un modello di prezzi a consumo con pagamento a consumo si applica alle app per la logica eseguite nel servizio pubblico di app per la logica multi-tenant "globale". Tutte le esecuzioni con esito positivo e negativo vengono misurate e fatturate.

Ad esempio, una richiesta eseguita da un trigger di polling viene ancora misurata come esecuzione anche se il trigger viene ignorato e non viene creata alcuna istanza del flusso di lavoro dell'app per la logica.

| Elementi | Descrizione |
|-------|-------------|
| Trigger e azioni [predefiniti](../connectors/apis-list.md#built-in) | Esecuzione a livello nativo nel servizio app per la logica e a consumo con il prezzo delle [ **azioni**](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>Ad esempio, il trigger HTTP e il trigger di richiesta sono trigger predefiniti, mentre l'azione di risposta e azione HTTP sono azioni predefinite. Anche le operazioni sui dati, le operazioni batch, le operazioni variabili e le [azioni di controllo del flusso di lavoro](../connectors/apis-list.md#control-workflow), ad esempio cicli, condizioni, Switch, rami paralleli e così via, sono azioni predefinite. |
| Trigger e azioni del [connettore standard](../connectors/apis-list.md#managed-connectors) <p><p>Trigger e azioni del [connettore personalizzato](../connectors/apis-list.md#custom) | A consumo con il [Prezzo standard del connettore](https://azure.microsoft.com/pricing/details/logic-apps/). |
| Trigger e azioni di [Enterprise Connector](../connectors/apis-list.md#managed-connectors) | A consumo con [Prezzo Enterprise Connector](https://azure.microsoft.com/pricing/details/logic-apps/). Tuttavia, durante l'anteprima pubblica, i connettori aziendali vengono misurati con il [prezzo *standard* del connettore](https://azure.microsoft.com/pricing/details/logic-apps/). |
| Azioni all'interno di [cicli](logic-apps-control-flow-loops.md) | Ogni azione eseguita in un ciclo viene misurata per ogni ciclo ciclo eseguito. <p><p>Si supponga, ad esempio, di disporre di un ciclo "for each" che include azioni che elaborano un elenco. Il servizio app per la logica misura ogni azione eseguita in quel ciclo moltiplicando il numero di elementi dell'elenco con il numero di azioni nel ciclo e aggiunge l'azione che avvia il ciclo. Il calcolo per un elenco di 10 elementi è quindi (10 * 1) + 1, che comporta 11 esecuzioni di azioni. |
| Tentativi | Per gestire le eccezioni e gli errori di base, è possibile configurare un [criterio di ripetizione](logic-apps-exception-handling.md#retry-policies) per i trigger e le azioni laddove supportati. Questi tentativi insieme alla richiesta originale vengono addebitati alle tariffe a seconda che il trigger o l'azione includa il tipo incorporato, standard o aziendale. Ad esempio, un'azione che viene eseguita con 2 tentativi viene addebitata per tre esecuzioni di azioni. |
| [Conservazione dei dati e utilizzo dello spazio di archiviazione](#data-retention) | A consumo con il prezzo di conservazione dei dati, disponibile nella [pagina prezzi di app](https://azure.microsoft.com/pricing/details/logic-apps/)per la logica, nella tabella **Dettagli prezzi** . |
|||

Per altre informazioni, vedere gli argomenti seguenti:

* [Visualizzare le metriche per le esecuzioni e il consumo di spazio di archiviazione](plan-manage-costs.md#monitor-billing-metrics)
* [Limiti nelle app per la logica di Azure](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>Non a consumo

* Trigger ignorati a causa di condizioni non soddisfatte
* Azioni non eseguite perché l'app per la logica è stata arrestata prima del completamento
* [App per la logica disabilitate](#disabled-apps)

### <a name="other-related-resources"></a>Altre risorse correlate

Le app per la logica funzionano con altre risorse correlate, ad esempio gli account di integrazione, i gateway dati locali e gli ambienti del servizio di integrazione (ISEs). Per informazioni sui prezzi di queste risorse, vedere le sezioni più avanti in questo argomento:

* [On-premises data gateway (Gateway dati locale)](#data-gateway)
* [Modello di determinazione prezzi dell'account di integrazione](#integration-accounts)
* [Modello di determinazione prezzi di ISE](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>Suggerimenti per la stima dei costi di utilizzo

Per stimare i costi di consumo più accurati, vedere i suggerimenti seguenti:

* Si consideri il possibile numero di messaggi o eventi che potrebbero arrivare in un determinato giorno, anziché basare i calcoli solo sull'intervallo di polling.

* Quando un evento o un messaggio soddisfa i criteri di trigger, molti trigger provano immediatamente a leggere tutti gli altri eventi in attesa o i messaggi che soddisfano i criteri. Questo comportamento significa che anche quando si seleziona un intervallo di polling più lungo, il trigger viene attivato in base al numero di eventi in attesa o di messaggi che soddisfano le condizioni per l'avvio dei flussi di lavoro. I trigger che seguono questo comportamento includono il bus di servizio di Azure e l'hub eventi di Azure.

  Si supponga, ad esempio, di configurare un trigger che controlla un endpoint ogni giorno. Quando il trigger controlla l'endpoint e trova 15 eventi che soddisfano i criteri, il trigger viene attivato ed esegue il flusso di lavoro corrispondente 15 volte. Il servizio app per la logica misura tutte le azioni eseguite da questi 15 flussi di lavoro, incluse le richieste di trigger.

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>Prezzi di ISE

Un modello di determinazione prezzi fisso si applica alle app per la logica eseguite in un [ *ambiente Integration Services* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Un ISE viene fatturato usando il [prezzo ambiente del servizio di integrazione](https://azure.microsoft.com/pricing/details/logic-apps), che dipende dal [livello ISE o dallo *SKU*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) creato. Questo prezzo è diverso dai prezzi multi-tenant, perché si paga per la capacità riservata e le risorse dedicate, indipendentemente dal fatto che vengano usate o meno.

| SKU ISE | Descrizione |
|---------|-------------|
| **Premium** | L'unità di base ha una [capacità fissa](logic-apps-limits-and-config.md#integration-service-environment-ise) e viene [fatturata a una tariffa oraria per lo SKU Premium](https://azure.microsoft.com/pricing/details/logic-apps). Se è necessaria una velocità effettiva maggiore, è possibile [aggiungere altre unità di scala](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) quando si crea ISE o in seguito. Ogni unità di scala viene fatturata a una [Tariffa oraria che è approssimativamente la metà della tariffa dell'unità di base](https://azure.microsoft.com/pricing/details/logic-apps). <p><p>Per informazioni sulla capacità e sui limiti, vedere [limiti di ISE in app per la logica di Azure](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| **Developer** | L'unità di base ha una [capacità fissa](logic-apps-limits-and-config.md#integration-service-environment-ise) e viene [fatturata a una tariffa oraria per lo SKU dello sviluppatore](https://azure.microsoft.com/pricing/details/logic-apps). Tuttavia, questo SKU non prevede alcun contratto di servizio (SLA), funzionalità di scalabilità verticale o ridondanza durante il riciclo, il che significa che potrebbero verificarsi ritardi o tempi di inattività. Gli aggiornamenti back-end potrebbero interrompere il servizio in modo intermittente. <p><p>**Importante**: assicurarsi di usare questo SKU solo per esplorazione, esperimenti, sviluppo e test, non per la produzione o il test delle prestazioni. <p><p>Per informazioni sulla capacità e sui limiti, vedere [limiti di ISE in app per la logica di Azure](logic-apps-limits-and-config.md#integration-service-environment-ise). |
|||

### <a name="included-at-no-extra-cost"></a>Incluso senza costi aggiuntivi

| Elementi | Descrizione |
|-------|-------------|
| Trigger e azioni [predefiniti](../connectors/apis-list.md#built-in) | Visualizzare l'etichetta **principale** ed eseguire nello stesso ISE delle app per la logica. |
| [Connettori Standard](../connectors/apis-list.md#managed-connectors) <p><p>[Connettori aziendali](../connectors/apis-list.md#enterprise-connectors) | -I connettori gestiti che visualizzano l'etichetta **ISE** sono appositamente progettati per funzionare senza il gateway dati locale ed eseguiti nello stesso ISE delle app per la logica. I prezzi di ISE includono tutte le connessioni aziendali desiderate. <p><p>-I connettori che non visualizzano l'etichetta ISE vengono eseguiti nel servizio app per la logica multi-tenant. I prezzi di ISE includono tuttavia queste esecuzioni per le app per la logica eseguite in ISE. |
| Azioni all'interno di [cicli](logic-apps-control-flow-loops.md) | I prezzi di ISE includono ogni azione che viene eseguita in un ciclo per ogni ciclo ciclo che esegue. <p><p>Si supponga, ad esempio, di disporre di un ciclo "for each" che include azioni che elaborano un elenco. Per ottenere il numero totale di esecuzioni di azioni, moltiplicare il numero di voci di elenco con il numero di azioni nel ciclo e aggiungere l'azione che avvia il ciclo. Il calcolo per un elenco di 10 elementi è quindi (10 * 1) + 1, che comporta 11 esecuzioni di azioni. |
| Tentativi | Per gestire le eccezioni e gli errori di base, è possibile configurare un [criterio di ripetizione](logic-apps-exception-handling.md#retry-policies) per i trigger e le azioni laddove supportati. I prezzi di ISE includono i tentativi insieme alla richiesta originale. |
| [Conservazione dei dati e utilizzo dello spazio di archiviazione](#data-retention) | Le app per la logica in ISE non comportano costi di conservazione e archiviazione. |
| [Account di integrazione](#integration-accounts) | Include l'utilizzo per un singolo livello di account di integrazione, basato sullo SKU ISE, senza costi aggiuntivi. |
|||

Per informazioni sui limiti, vedere [limiti di ISE in app per la logica di Azure](logic-apps-limits-and-config.md#integration-service-environment-ise).

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Account di integrazione

Un [account di integrazione](../logic-apps/logic-apps-pricing.md#integration-accounts) è una risorsa separata che è possibile creare e collegare alle app per la logica in modo da esplorare, compilare e testare soluzioni di integrazione B2B che usano le funzionalità di elaborazione [EDI](logic-apps-enterprise-integration-b2b.md) e [XML](logic-apps-enterprise-integration-xml.md) .

App per la logica di Azure offre i livelli o i livelli di account di integrazione che variano in base al modello [di prezzi](https://azure.microsoft.com/pricing/details/logic-apps/) e [fatturazione](logic-apps-pricing.md#integration-accounts), a seconda che le app per la logica siano basate sul consumo o su ISE:

| Livello | Descrizione |
|------|-------------|
| **Base** | Per gli scenari in cui si desidera solo la gestione dei messaggi o fungere da partner commerciale di piccole dimensioni con una relazione tra partner commerciali e un'entità di business più ampia. <p><p>Supportato dal contratto di contratto per app per la logica. |
| **Standard** | Per gli scenari in cui sono presenti relazioni B2B più complesse e un numero maggiore di entità che è necessario gestire. <p><p>Supportato dal contratto di contratto per app per la logica. |
| **Free** | Per gli scenari di esplorazione, non per gli scenari di produzione. Questo livello presenta limiti per la disponibilità, la velocità effettiva e l'utilizzo di un'area. Ad esempio, il livello gratuito è disponibile solo per le aree pubbliche in Azure, ad esempio Stati Uniti occidentali o Asia sudorientale, ma non per [Azure Cina 21ViaNet](/azure/china/overview-operations) o [Azure per enti](../azure-government/documentation-government-welcome.md)pubblici. <p><p>**Nota**: non supportato dal contratto di contratto con app per la logica. |
|||

Per informazioni sui limiti dell'account di integrazione, vedere [limiti e configurazione per app per la logica di Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits), ad esempio:

* [Limiti per gli account di integrazione per ogni sottoscrizione di Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [Limiti per i vari artefatti per ogni account di integrazione](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). Gli artefatti includono partner commerciali, contratti, mappe, schemi, assembly, certificati, configurazioni batch e così via.

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>Account di integrazione per app per la logica basate sul consumo

Gli account di integrazione vengono fatturati con un [prezzo dell'account di integrazione](https://azure.microsoft.com/pricing/details/logic-apps/) fisso basato sul livello di account usato.

### <a name="ise-based-logic-apps"></a>App per la logica basate su ISE

Senza costi aggiuntivi, ISE include un unico account di integrazione, basato sullo SKU di ISE. Per un costo aggiuntivo, è possibile creare altri account di integrazione per ISE da usare fino al [limite totale di ISE](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Per altre informazioni sul [modello di determinazione prezzi di ISE](#fixed-pricing) , vedere più indietro in questo argomento.

| SKU ISE | Account di integrazione incluso | Costo aggiuntivo |
|---------|------------------------------|-----------------|
| **Premium** | Un singolo account di integrazione [standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | Fino a un massimo di 19 account standard. Gli account gratuiti o Basic non sono consentiti. |
| **Developer** | Un singolo account di integrazione [gratuita](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | Fino a un massimo di 19 account standard se si ha già un account gratuito o 20 account standard totali se non si ha un account gratuito. Gli account Basic non sono consentiti. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>Conservazione dei dati e utilizzo dello spazio di archiviazione

Tutti gli input e gli output nella cronologia di esecuzione dell'app per la logica vengono archiviati e misurati in base alla durata dell'esecuzione dell'app [e al periodo di memorizzazione della cronologia](logic-apps-limits-and-config.md#run-duration-retention-limits).

* Per le app per la logica nel servizio app per la logica multi-tenant, il consumo di spazio di archiviazione viene fatturato a un prezzo fisso, disponibile nella [pagina prezzi di app](https://azure.microsoft.com/pricing/details/logic-apps)per la logica, nella tabella **Dettagli prezzi** .

* Per le app per la logica in ISEs, l'utilizzo dell'archiviazione non comporta costi di conservazione dei dati.

Per monitorare l'utilizzo del consumo di spazio di archiviazione, vedere [visualizzare le metriche per le esecuzioni e il consumo di archiviazione](plan-manage-costs.md#monitor-billing-metrics).

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>Gateway dati locale

Un [gateway dati locale](../logic-apps/logic-apps-gateway-install.md) è una risorsa separata creata in modo che le app per la logica possano accedere ai dati locali usando connettori specifici supportati dal gateway. Le operazioni sui connettori eseguite tramite il gateway comportano addebiti, ma il gateway stesso non comporta addebiti.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>App per la logica disabilitate

Le app per la logica disabilitate non vengono addebitate perché non è possibile creare nuove istanze mentre sono disabilitate. Dopo che un'app per la logica è stata disabilitata, l'arresto di eventuali istanze in esecuzione può richiedere del tempo.

## <a name="next-steps"></a>Passaggi successivi

* [Pianificare e gestire i costi per le app per la logica di Azure](plan-manage-costs.md)
