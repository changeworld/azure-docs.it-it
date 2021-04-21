---
title: Modelli di & di fatturazione
description: Panoramica del funzionamento dei modelli di prezzi e fatturazione in App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: a3c20dd85c94c359259cf69e25bb9083d56857fc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777150"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Modelli di prezzi e fatturazione per App per la logica di Azure

[App per la logica di Azure](../logic-apps/logic-apps-overview.md) consente di creare ed eseguire flussi di lavoro di integrazione automatizzati che possono essere ridimensionati nel cloud. Questo articolo descrive il funzionamento dei modelli di fatturazione e prezzi per il servizio App per la logica e le risorse correlate. Per tariffe specifiche, vedere Prezzi [di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps). Per informazioni su come pianificare, gestire e monitorare i costi, vedere Pianificare e gestire i [costi per](plan-manage-costs.md)App per la logica di Azure .

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>Prezzi multi-tenant

Un modello di prezzi a consumo con pagamento in base all'utilizzo si applica alle app per la logica eseguite nel servizio app per la logica pubblico, "globale" e multi-tenant. Tutte le esecuzioni riuscite e non riuscite vengono a consumo e fatturate.

Ad esempio, una richiesta inviata da un trigger di polling viene ancora misurata come esecuzione anche se tale trigger viene ignorato e non viene creata alcuna istanza del flusso di lavoro dell'app per la logica.

| Elementi | Descrizione |
|-------|-------------|
| [Trigger e](../connectors/built-in.md) azioni predefiniti | L'esecuzione viene eseguita in modo nativo nel servizio App per la logica e viene misurata usando il [ **prezzo delle** azioni](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>Ad esempio, il trigger HTTP e il trigger Richiesta sono trigger predefiniti, mentre l'azione HTTP e l'azione Risposta sono azioni incorporate. Anche le operazioni sui dati, [](../connectors/built-in.md)le operazioni batch, le operazioni variabili e le azioni di controllo del flusso di lavoro, ad esempio cicli, condizioni, commutatore, rami paralleli e così via, sono azioni incorporate. |
| [Trigger e](../connectors/managed.md) azioni del connettore standard <p><p>[Trigger e](../connectors/apis-list.md#custom-apis-and-connectors) azioni del connettore personalizzato | Misurato usando il [prezzo del connettore Standard.](https://azure.microsoft.com/pricing/details/logic-apps/) |
| [Trigger e](../connectors/managed.md) azioni di Enterprise Connector | A consumo usando il [prezzo di Enterprise Connector.](https://azure.microsoft.com/pricing/details/logic-apps/) Tuttavia, durante l'anteprima pubblica, i connettori Enterprise vengono misurati usando il [ *prezzo del connettore Standard.*](https://azure.microsoft.com/pricing/details/logic-apps/) |
| Azioni [all'interno di cicli](logic-apps-control-flow-loops.md) | Ogni azione eseguita in un ciclo viene misurata per ogni ciclo eseguito. <p><p>Si supponga, ad esempio, di avere un ciclo "for each" che include azioni che elaborano un elenco. Il servizio App per la logica misura ogni azione eseguita in tale ciclo moltiplicando il numero di elementi dell'elenco con il numero di azioni nel ciclo e aggiunge l'azione che avvia il ciclo. Il calcolo per un elenco di 10 elementi è quindi (10 * 1) + 1, che comporta 11 esecuzioni di azioni. |
| Tentativi | Per gestire le eccezioni e gli errori di base, è possibile configurare criteri di ripetizione [per](logic-apps-exception-handling.md#retry-policies) trigger e azioni, se supportati. Questi tentativi insieme alla richiesta originale vengono addebitati in base alle tariffe a seconda che il trigger o l'azione abbia un tipo predefinito, Standard o Enterprise. Ad esempio, un'azione eseguita con 2 tentativi viene addebitata per 3 esecuzioni di azioni. |
| [Conservazione dei dati e utilizzo dello spazio di archiviazione](#data-retention) | A consumo usando il prezzo di conservazione dei dati, che è possibile trovare nella pagina dei prezzi di App per la [logica](https://azure.microsoft.com/pricing/details/logic-apps/), nella **tabella Dettagli** prezzi. |
|||

Per altre informazioni, vedere gli argomenti seguenti:

* [Visualizzare le metriche per le esecuzioni e l'utilizzo dello spazio di archiviazione](plan-manage-costs.md#monitor-billing-metrics)
* [Limiti in App per la logica di Azure](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>Non a consumo

* Trigger ignorati a causa di condizioni non soddisfatte
* Azioni non eseguite perché l'app per la logica è stata arrestata prima del completamento
* [App per la logica disabilitate](#disabled-apps)

### <a name="other-related-resources"></a>Altre risorse correlate

Le app per la logica funzionano con altre risorse correlate, ad esempio account di integrazione, gateway dati locali e ambienti del servizio di integrazione (ISE). Per informazioni sui prezzi per tali risorse, vedere le sezioni seguenti più avanti in questo argomento:

* [On-premises data gateway (Gateway dati locale)](#data-gateway)
* [Modello tariffario dell'account di integrazione](#integration-accounts)
* [Modello tariffario ISE](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>Suggerimenti per la stima dei costi di consumo

Per stimare i costi di consumo più accurati, vedere questi suggerimenti:

* Prendere in considerazione il numero possibile di messaggi o eventi che potrebbero arrivare in un determinato giorno, anziché basare i calcoli solo sull'intervallo di polling.

* Quando un evento o un messaggio soddisfa i criteri di trigger, molti trigger provano immediatamente a leggere tutti gli altri eventi in attesa o i messaggi che soddisfano i criteri. Questo comportamento significa che anche quando si seleziona un intervallo di polling più lungo, il trigger viene attivato in base al numero di eventi in attesa o di messaggi che soddisfano le condizioni per l'avvio dei flussi di lavoro. I trigger che seguono questo comportamento includono il bus di servizio di Azure e l'hub eventi di Azure.

  Si supponga, ad esempio, di configurare un trigger che controlla un endpoint ogni giorno. Quando il trigger controlla l'endpoint e trova 15 eventi che soddisfano i criteri, il trigger viene attivato ed esegue il flusso di lavoro corrispondente 15 volte. Il servizio App per la logica misura tutte le azioni eseguite da questi 15 flussi di lavoro, incluse le richieste di trigger.

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>Prezzi di ISE

Un modello di prezzi fisso si applica alle app per la logica eseguite in un ambiente del [ *servizio di integrazione* (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) Un ISE viene fatturato usando il prezzo [ambiente del servizio di integrazione,](https://azure.microsoft.com/pricing/details/logic-apps)che dipende dal livello ISE o [dallo *SKU*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) creato. Questo prezzo differisce dai prezzi multi-tenant perché si paga per la capacità riservata e le risorse dedicate indipendentemente dal fatto che siano usate o meno.

| SKU ISE | Descrizione |
|---------|-------------|
| **Premium** | L'unità di base [ha capacità fissa](logic-apps-limits-and-config.md#integration-service-environment-ise) e viene fatturata a una tariffa [oraria per lo SKU Premium.](https://azure.microsoft.com/pricing/details/logic-apps) Se è necessaria una velocità effettiva maggiore, è possibile [aggiungere altre unità](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) di scala quando si crea l'ISE o successivamente. Ogni unità di scala viene fatturata a una tariffa oraria di circa la metà della [tariffa unitario di base.](https://azure.microsoft.com/pricing/details/logic-apps) <p><p>Per informazioni sulla capacità e sui limiti, vedere [Limiti ISE in App per la logica di Azure](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| **Developer** | L'unità di base [ha capacità fissa](logic-apps-limits-and-config.md#integration-service-environment-ise) e viene fatturata a una tariffa [oraria per lo SKU sviluppatore](https://azure.microsoft.com/pricing/details/logic-apps). Tuttavia, questo SKU non ha alcun contratto di servizio, capacità di scalabilità verticale o ridondanza durante il riciclo, il che significa che potrebbero verificarsi ritardi o tempi di inattività. Gli aggiornamenti back-end potrebbero interrompere il servizio in modo intermittente. <p><p>**Importante:** assicurarsi di usare questo SKU solo per l'esplorazione, gli esperimenti, lo sviluppo e i test, non per i test di produzione o delle prestazioni. <p><p>Per informazioni sulla capacità e sui limiti, vedere [Limiti ISE in App per la logica di Azure](logic-apps-limits-and-config.md#integration-service-environment-ise). |
|||

### <a name="included-at-no-extra-cost"></a>Incluso senza costi aggiuntivi

| Elementi | Descrizione |
|-------|-------------|
| [Trigger e](../connectors/built-in.md) azioni predefiniti | Visualizzare **l'etichetta Core** ed eseguire nello stesso ISE delle app per la logica. |
| [Connettori Standard](../connectors/managed.md) <p><p>[Connettori aziendali](../connectors/managed.md#enterprise-connectors) | - I connettori gestiti che visualizzano l'etichetta ISE sono appositamente progettati per funzionare senza il gateway dati locale ed essere eseguiti nello stesso **ISE** delle app per la logica. I prezzi di ISE includono tutte le connessioni Enterprise desiderate. <p><p>- I connettori che non visualizzano l'etichetta ISE vengono eseguiti nel servizio App per la logica multi-tenant. Tuttavia, i prezzi di ISE includono queste esecuzioni per le app per la logica eseguite in un ISE. |
| Azioni [all'interno di cicli](logic-apps-control-flow-loops.md) | I prezzi di ISE includono ogni azione eseguita in un ciclo per ogni ciclo eseguito. <p><p>Si supponga, ad esempio, di avere un ciclo "for each" che include azioni che elaborano un elenco. Per ottenere il numero totale di esecuzioni di azioni, moltiplicare il numero di elementi dell'elenco con il numero di azioni nel ciclo e aggiungere l'azione che avvia il ciclo. Il calcolo per un elenco di 10 elementi è quindi (10 * 1) + 1, che comporta 11 esecuzioni di azioni. |
| Tentativi | Per gestire le eccezioni e gli errori di base, è possibile configurare criteri di ripetizione [per](logic-apps-exception-handling.md#retry-policies) trigger e azioni, se supportati. I prezzi di ISE includono i tentativi insieme alla richiesta originale. |
| [Conservazione dei dati e utilizzo dello spazio di archiviazione](#data-retention) | Le app per la logica in un ISE non comportano costi di conservazione e archiviazione. |
| [Account di integrazione](#integration-accounts) | Include l'utilizzo per un singolo livello di account di integrazione, basato su SKU ISE, senza costi aggiuntivi. |
|||

Per informazioni sui limiti, vedere [Limiti isE in App per la logica di Azure](logic-apps-limits-and-config.md#integration-service-environment-ise).

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Account di integrazione

Un [account di](../logic-apps/logic-apps-pricing.md#integration-accounts) integrazione è una risorsa separata creata e collegata alle app per la logica in modo da poter esplorare, compilare e testare soluzioni di integrazione B2B che usano le funzionalità di elaborazione [EDI](logic-apps-enterprise-integration-b2b.md) [e XML.](logic-apps-enterprise-integration-xml.md)

App per la logica di Azure offre questi livelli o livelli di account di integrazione che variano [in](https://azure.microsoft.com/pricing/details/logic-apps/) base ai prezzi e al modello di [fatturazione,](logic-apps-pricing.md#integration-accounts)a seconda che le app per la logica siano basate sul consumo o su ISE:

| Livello | Descrizione |
|------|-------------|
| **Base** | Per gli scenari in cui si vuole solo la gestione dei messaggi o agire come partner commerciale di piccole dimensioni che ha una relazione tra partner commerciali con un'entità aziendale più grande. <p><p>Supportato dal contratto di servizio di App per la logica. |
| **Standard** | Per gli scenari in cui si hanno relazioni B2B più complesse e un numero maggiore di entità che è necessario gestire. <p><p>Supportato dal contratto di servizio di App per la logica. |
| **Gratuito** | Per gli scenari esplorativi, non per gli scenari di produzione. Questo livello ha limiti alla disponibilità dell'area, alla velocità effettiva e all'utilizzo. Ad esempio, il livello Gratuito è disponibile solo per le aree pubbliche in Azure, ad esempio Stati Uniti occidentali o Asia sud-orientale, ma non per Azure China (21Vianet) [o](/azure/china/overview-operations) [Azure per enti pubblici](../azure-government/documentation-government-welcome.md). <p><p>**Nota:** non supportato dal contratto di servizio di App per la logica. |
|||

Per informazioni sui limiti dell'account di integrazione, vedere [Limiti e](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)configurazione per App per la logica di Azure , ad esempio:

* [Limiti sugli account di integrazione per ogni sottoscrizione di Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [Limiti per vari elementi per ogni account di integrazione.](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) Gli elementi includono partner commerciali, contratti, mappe, schemi, assembly, certificati, configurazioni batch e così via.

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>Account di integrazione per app per la logica basate sull'utilizzo

Gli account di integrazione vengono fatturati usando un prezzo fisso [dell'account](https://azure.microsoft.com/pricing/details/logic-apps/) di integrazione basato sul livello di account in uso.

### <a name="ise-based-logic-apps"></a>App per la logica basate su ISE

Senza costi aggiuntivi, l'ISE include un singolo account di integrazione, in base allo SKU ISE. Per un costo aggiuntivo, è possibile creare più account di integrazione per l'ISE da usare fino al [limite totale di ISE.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) Altre informazioni sul modello [tariffario ISE sono](#fixed-pricing) più indietro in questo argomento.

| SKU ISE | Account di integrazione incluso | Costo aggiuntivo |
|---------|------------------------------|-----------------|
| **Premium** | Un singolo account [di integrazione Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | Fino a 19 account Standard in più. Gli account gratuiti o Basic non sono consentiti. |
| **Developer** | Un singolo account [di](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrazione gratuito | Fino a 19 account Standard se si ha già un account Gratuito o 20 account Standard totali se non si ha un account gratuito. Gli account Basic non sono consentiti. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>Conservazione dei dati e utilizzo dell'archiviazione

Tutti gli input e gli output nella cronologia di esecuzione dell'app per la logica vengono archiviati e a consumo in base alla durata di esecuzione e al periodo di conservazione [della cronologia dell'app.](logic-apps-limits-and-config.md#run-duration-retention-limits)

* Per le app per la logica nel servizio App per la logica multi-tenant, l'utilizzo dello spazio di archiviazione viene fatturato a un prezzo fisso, disponibile nella pagina prezzi app per la logica [,](https://azure.microsoft.com/pricing/details/logic-apps)nella tabella **Dettagli** prezzi.

* Per le app per la logica nelle ise, l'utilizzo dello spazio di archiviazione non comporta costi di conservazione dei dati.

Per monitorare l'utilizzo dell'archiviazione, vedere [Visualizzare le metriche per le esecuzioni e l'utilizzo dello spazio di archiviazione.](plan-manage-costs.md#monitor-billing-metrics)

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>Gateway dati locale

Un [gateway dati](../logic-apps/logic-apps-gateway-install.md) locale è una risorsa separata creata in modo che le app per la logica possano accedere ai dati locali usando connettori specifici supportati dal gateway. Le operazioni dei connettori che vengono eseguite tramite il gateway comportano costi, ma il gateway stesso non comporta addebiti.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>App per la logica disabilitate

Le app per la logica disabilitate non vengono addebitate perché non possono creare nuove istanze mentre sono disabilitate. Dopo che un'app per la logica è stata disabilitata, l'arresto di eventuali istanze in esecuzione può richiedere del tempo.

## <a name="next-steps"></a>Passaggi successivi

* [Pianificare e gestire i costi per App per la logica di Azure](plan-manage-costs.md)
