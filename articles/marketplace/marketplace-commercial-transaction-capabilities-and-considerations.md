---
title: Funzionalità di transazione del marketplace commerciale di Microsoft
description: Questo articolo illustra le considerazioni relative a prezzi, fatturazione e proventi delle transazioni per l'opzione di transazione del marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 9797401fd96bf052fdc9de0c2c2ceaf88b3d46b6
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504210"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Funzionalità di transazione del marketplace commerciale

Questo articolo descrive le considerazioni sui prezzi, la fatturazione, la fatturazione e i pagamenti per Microsoft Commercial Marketplace.

## <a name="transactions-by-listing-option"></a>Opzione Transactions by Listing

L'editore o Microsoft è responsabile della gestione delle transazioni delle licenze software per le offerte nel marketplace commerciale. L'opzione di elenco scelto per l'offerta determina chi gestisce la transazione. Per la disponibilità e le spiegazioni di ogni opzione di pubblicazione, vedere [Introduzione alle opzioni di elenco](determine-your-listing-type.md)

### <a name="contact-me-free-trial-and-byol-options"></a>Contattaci, versione di valutazione gratuita e opzioni di BYOL

Gli editori possono scegliere il _contatto_ e la _versione di valutazione gratuita_, le opzioni per scopi promozionali e di acquisizione utente. Per alcuni tipi di offerte, i publisher possono scegliere l'opzione _Bring your own License_ (BYOL) per consentire ai clienti di acquistare una sottoscrizione all'offerta usando una licenza acquistata direttamente dall'utente. Con queste opzioni, Microsoft non partecipa direttamente alle transazioni di licenza software dell'editore e non viene addebitata alcuna tariffa per le transazioni, quindi i Publisher mantengono tutti i ricavi.

Con queste opzioni, i Publisher sono responsabili del supporto di tutti gli aspetti della transazione di licenza software. Sono inclusi, ad esempio, l'ordine, l'evasione, la misurazione, la fatturazione, la fatturazione, il pagamento e la raccolta. Con l'opzione Contact me Listing, i Publisher mantengono il 100% dei costi di licenza software dell'editore raccolti dal cliente.

### <a name="transact-publishing-option"></a>Opzione di pubblicazione delle transazioni

La scelta di vendere tramite Microsoft sfrutta le funzionalità di Microsoft Commerce e offre un'esperienza end-to-end, da individuazione e valutazione all'acquisto e all'implementazione. Un'offerta _transazionale_ è una delle quali Microsoft semplifica lo scambio di denaro per una licenza software per conto dell'editore. Le offerte Transact vengono fatturate in base a una sottoscrizione Microsoft o a una carta di credito esistente, consentendo a Microsoft di ospitare transazioni del Marketplace cloud per conto dell'editore.

È possibile scegliere l'opzione relativa alle transazioni quando si crea una nuova offerta nel Centro per i partner. Questa opzione verrà visualizzata solo se Transact è disponibile per il tipo di offerta.

## <a name="transact-overview"></a>Panoramica sulle transazioni

Quando si usa l'opzione Transact, Microsoft consente la vendita di software di terze parti e la distribuzione di alcuni tipi di offerta alla sottoscrizione di Azure del cliente. Quando si seleziona un modello di prezzi per un'offerta, l'editore deve considerare la fatturazione delle tariffe di infrastruttura e i costi di licenza software.

L'opzione di pubblicazione delle transazioni è attualmente supportata per i tipi di offerte seguenti:

| Tipo di offerta | Cadenza di fatturazione | Fatturazione a consumo | Modello di prezzi |
| ------------ | ------------- | ------------- | ------------- |
| Applicazione di Azure<br>(Applicazione gestita) | Ogni mese | Sì | Basato sull'utilizzo |
| Macchina virtuale di Azure | Mensile | No | BYOL basato sull'utilizzo |
| Software come un servizio (SaaS, Software as a Service) | Mensile e annuale | Sì | Velocità fissa, per utente, basata sull'utilizzo. |
|||||

`*` Macchine virtuali di Azure offre supporto per piani di fatturazione basati sull'utilizzo. Questi piani vengono fatturati mensilmente per l'uso orario della sottoscrizione in base al core, alla dimensione core o al mercato e all'utilizzo delle dimensioni core.

### <a name="metered-billing"></a>Fatturazione a consumo

Il _servizio di misurazione del Marketplace_ ti permette di specificare addebiti con pagamento in base al consumo, oltre ai costi mensili o annuali inclusi nel contratto (diritto). È possibile addebitare i costi di utilizzo per le dimensioni del servizio di misurazione del Marketplace specificate, ad esempio larghezza di banda, ticket o messaggi di posta elettronica elaborati. Per altre informazioni sulla fatturazione a consumo per le offerte SaaS, vedere [fatturazione a consumo per Saas usando il servizio di misurazione del Marketplace commerciale](./partner-center-portal/saas-metered-billing.md). Per ulteriori informazioni sulla fatturazione a consumo per applicazione Azure offerte, vedere [fatturazione a consumo delle applicazioni gestite](./partner-center-portal/azure-app-metered-billing.md).

### <a name="billing-infrastructure-costs"></a>Fatturazione dei costi per l'infrastruttura

Per le **macchine virtuali** e **le applicazioni Azure**, le tariffe di utilizzo dell'infrastruttura di Azure vengono fatturate alla sottoscrizione di Azure del cliente. Nella fattura del cliente i costi di utilizzo dell'infrastruttura vengono addebitati e visualizzati separatamente dai costi di licenza del provider software.

Per le **app Saas**, l'editore deve tenere conto delle tariffe di utilizzo dell'infrastruttura di Azure e delle tariffe di licenza software come singolo costo. presentata al cliente come tariffa mensile fissa. L'utilizzo dell'infrastruttura di Azure viene gestito e fatturato direttamente all'editore. I costi effettivi di utilizzo dell'infrastruttura di Azure non sono visibili al cliente. Gli editori scelgono in genere di accorpare i costi di utilizzo dell'infrastruttura di Azure nei prezzi di licenza software. Le tariffe per le licenze software non vengono misurate o in base al consumo dell'utente.

## <a name="pricing-models"></a>Modelli di determinazione dei prezzi

A seconda dell'opzione di transazione utilizzata, gli addebiti per la sottoscrizione sono i seguenti:

- **Ottienilo ora (gratuito)**: nessun addebito per le licenze software. Le offerte gratuite non possono essere convertite in un'offerta a pagamento. I clienti devono ordinare un'offerta a pagamento.
- **Bring your own License** (BYOL): se un'offerta è elencata nel Marketplace commerciale, eventuali addebiti applicabili per le licenze software vengono gestiti direttamente tra l'editore e il cliente. Microsoft addebita solo i costi di utilizzo dell'infrastruttura di Azure applicabili all'account della sottoscrizione di Azure del cliente.
- **Prezzi per le sottoscrizioni**: le tariffe per le licenze software sono presentate come una tariffa mensile o annuale, una tariffa di sottoscrizione ricorrente fatturata come tariffa fissa o per postazione. I costi di sottoscrizione ricorrenti non vengono rivalutati in modo prolungato per gli annullamenti dei clienti a metà termine o per i servizi non usati Le tariffe di sottoscrizione ricorrenti possono essere rivalutate se il cliente aggiorna o esegue il downgrade della sottoscrizione al centro del periodo di validità della sottoscrizione.
- **Prezzi basati sull'utilizzo**: per le offerte di macchine virtuali di Azure, i clienti vengono addebitati in base al livello di utilizzo dell'offerta. Per le immagini di macchine virtuali, ai clienti viene addebitata una tariffa oraria di Azure Marketplace, impostata dall'editore, per l'uso di macchine virtuali distribuite dalle immagini di VM. La tariffa oraria può essere uniforme o diversificata in base alle dimensioni delle macchine virtuali. Le ore parziali vengono addebitate in base ai minuti effettivi. I piani vengono fatturati mensilmente.
- **Prezzi** a consumo: per offerte applicazione Azure e offerte SaaS, gli editori possono usare il [servizio di misurazione del Marketplace](./partner-center-portal/marketplace-metering-service-apis.md) per fatturare l'utilizzo in base alle dimensioni di misurazione personalizzate che configurano. Queste modifiche sono in aggiunta agli addebiti mensili o annuali inclusi nel contratto (diritto). Esempi di dimensioni dei contatori personalizzati sono la larghezza di banda, i ticket o i messaggi di posta elettronica elaborati Gli editori possono definire una o più dimensioni a consumo per ogni piano, ma un massimo di 30 per ogni offerta. Gli editori sono responsabili del monitoraggio dell'utilizzo dei singoli clienti, con ogni contatore definito nell'offerta. Gli eventi devono essere segnalati a Microsoft entro un'ora. Il costo che Microsoft addebita ai clienti si basa sulle informazioni sull'utilizzo comunicate dagli editori per il periodo di fatturazione applicabile.
- **Versione di valutazione gratuita**: nessun addebito per le licenze software che variano da 30 giorni fino a sei mesi, a seconda del tipo di offerta. Se i Publisher forniscono una versione di valutazione gratuita per più piani all'interno della stessa offerta, i clienti possono passare a una versione di valutazione gratuita in un altro piano, ma il periodo di valutazione non viene riavviato. Per le offerte di macchine virtuali, ai clienti vengono addebitati i costi di infrastruttura di Azure per l'uso dell'offerta durante un periodo di valutazione. Alla scadenza del periodo di valutazione, ai clienti viene addebitato automaticamente l'ultimo piano effettuato in base alle tariffe standard, a meno che non vengano annullate prima della fine del periodo di valutazione.

> [!NOTE]
> Le offerte fatturate in base all'utilizzo non sono idonee per i rimborsi dopo l'uso di una soluzione.

Gli editori che desiderano modificare le tariffe di utilizzo associate a un'offerta devono prima rimuovere l'offerta (o il piano specifico all'interno dell'offerta) dal Marketplace commerciale. La rimozione deve essere eseguita in base ai requisiti del [Contratto Microsoft Publisher](https://go.microsoft.com/fwlink/?LinkID=699560). Il server di pubblicazione può quindi pubblicare una nuova offerta o pianificare un'offerta che include i nuovi costi di utilizzo. Per informazioni sulla rimozione di un'offerta o di un piano, vedere [interrompere la vendita di un'offerta o di un piano](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>Prezzi gratuiti, contattaci e Bring your own License (BYOL)

Quando si pubblica un'offerta con l'opzione Get it Now (gratuita), Contact me o BYOL, Microsoft non svolge alcun ruolo per facilitare la transazione di vendita per i costi di licenza software. Il server di pubblicazione mantiene il 100% dei costi delle licenze software.

### <a name="usage-based-and-subscription-pricing"></a>Prezzi di sottoscrizione e di utilizzo

Quando si pubblica un'offerta come transazione basata sull'utilizzo o sottoscrizione, Microsoft fornisce la tecnologia e i servizi per elaborare gli acquisti, i ritorni e i costi di licenza software. In questo scenario l'editore autorizza Microsoft ad agire in veste di agente per questi scopi. Il server di pubblicazione consente a Microsoft di facilitare la transazione di gestione licenze software. Il server di pubblicazione, conserva la designazione come venditore, provider, distributore e concessore di licenze.

Microsoft consente ai clienti di ordinare, concedere in licenza e usare il proprio software, imponendo il rispetto dei termini e delle condizioni sia del marketplace commerciale di Microsoft che del contratto di licenza con l'utente finale. È necessario fornire il proprio contratto di licenza con l'utente finale o selezionare il [contratto standard](./standard-contract.md) durante la creazione dell'offerta.

### <a name="free-software-trials"></a>Versioni di valutazione gratuite del software

Per gli scenari di pubblicazione di transazioni, è possibile rendere disponibile gratuitamente una licenza software da 30 a 120 giorni, a seconda della sottoscrizione. I clienti verranno modificati per l'utilizzo dell'infrastruttura di Azure applicabile.

### <a name="examples-of-pricing-and-store-fees"></a>Esempi di prezzi e tariffe per i negozi

**Basato sull'utilizzo**

I prezzi basati sull'utilizzo hanno la struttura dei costi seguente:

| **Costo della licenza** | **€ 1,00 all'ora** |
|---------|---------|
| Costo dell'utilizzo di Azure (D1/1-Core) | € 0,14 all'ora |
| _Importo addebitato da Microsoft al cliente_ | _€ 1,14 all'ora_ |
||

In questo scenario Microsoft addebita 1,14 euro all'ora per l'uso dell'immagine di VM pubblicata.

| **Microsoft addebita** | **€ 1,14 all'ora**  |
|---------|---------|
| Microsoft paga l'80% del costo della licenza | € 0,80 all'ora |
| Microsoft trattiene il 20% del costo della licenza  |  € 0,20 all'ora |
| Microsoft trattiene il 100% del costo dell'utilizzo di Azure | € 0,14 all'ora |
||

**Bring Your Own License (BYOL)**

Con BYOL la struttura dei costi è la seguente:

| **Costo della licenza** | **Il costo della licenza viene concordato e addebitato dall'utente** |
|---------|---------|
|Costo dell'utilizzo di Azure (D1/1-Core)    |   € 0,14 all'ora     |
| _Importo addebitato da Microsoft al cliente_ | _€ 0,14 all'ora_ |
||

In questo scenario Microsoft addebita 0,14 euro all'ora per l'uso dell'immagine di VM pubblicata.

| **Microsoft addebita** | **€ 0,14 all'ora** |
|---------|---------|
| Microsoft trattiene il costo dell'utilizzo di Azure | € 0,14 all'ora |
| Microsoft trattiene lo 0% del costo della licenza | € 0,00 all'ora |
||

**Sottoscrizione app SaaS**

Il prezzo delle sottoscrizioni SaaS può essere a una tariffa fissa o a un utente su base mensile o annuale. Se si Abilita l'opzione  **Vendi tramite Microsoft** per un'offerta SaaS, si avrà la seguente struttura di costo:

| **Costo della licenza** | **$ 100,00 al mese** |
|--------------|---------|
| Costo dell'utilizzo di Azure (D1/1-Core) | Costi addebitati direttamente all'editore, non al cliente |
| _Importo addebitato da Microsoft al cliente_ | _$ 100,00 al mese (l'editore deve calcolare i costi di infrastruttura sostenuti o trasferirli nei costi di licenza)_ |
||

In questo scenario Microsoft addebita $100,00 per la licenza software e paga $80,00 o $90,00 a seconda che l'offerta sia idonea a una tariffa ridotta del servizio di archiviazione.

| **Microsoft addebita** | **$ 100,00 al mese** |
|---------|---------|
| Microsoft paga l'80% del costo della licenza <br> \* Per le app SaaS idonee, Microsoft paga il 90% del costo di licenza | $ 80,00 al mese <br> \* $ 90,00 al mese |
| Microsoft trattiene il 20% del costo della licenza <br> \* Per le app SaaS idonee, Microsoft trattiene il 10% del costo di licenza. | $ 20,00 al mese <br> \* 10,00 $ |

### <a name="commercial-marketplace-service-fees"></a>Tariffe del servizio Marketplace commerciale

Viene addebitata una tariffa per il servizio di archiviazione standard del 20% quando i clienti acquistano l'offerta di transazione dal Marketplace commerciale. Per informazioni dettagliate su questa tariffa, vedere la sezione 5C del [Contratto Microsoft Publisher](https://go.microsoft.com/fwlink/?LinkID=699560).

Per alcune offerte di transazione pubblicate sul Marketplace commerciale, è possibile che si disponga di una tariffa per il servizio di archiviazione ridotta pari al 10%. Affinché un'offerta venga qualificata, deve essere stata designata da Microsoft come _co-selling IP di Azure incentivate_. L'idoneità deve essere soddisfatta per almeno cinque giorni lavorativi prima della fine di ogni mese di calendario per ricevere la tariffa ridotta del servizio Marketplace. Una volta soddisfatta l'idoneità, la tariffa del servizio ridotta viene assegnata a tutte le transazioni effettive il primo giorno del mese successivo fino a quando lo stato _incentivate della co-Selle IP di Azure_ non viene perso. Per informazioni dettagliate sull'idoneità di co-selling IP, vedere [requisiti per lo stato di co-selling](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status).

La tariffa ridotta del servizio Marketplace si applica alle app SaaS incentivate, alle VM, alle app gestite e a qualsiasi altra soluzione IaaS transazionale qualificata resa disponibile tramite il Marketplace commerciale. Le offerte SaaS a pagamento associate a un'app Microsoft teams o almeno due Microsoft 365 componenti aggiuntivi (Excel, PowerPoint, Word, Outlook e SharePoint) e pubblicate in Microsoft AppSource possono anche essere valide per questo sconto.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Fatturazione cliente, pagamento e riscossioni

**Fatturazione e pagamento**: è possibile usare il metodo di fatturazione preferito del cliente per fornire i costi delle licenze software [consumo](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) o della sottoscrizione.

**Contratto Enterprise**: se il metodo di fatturazione preferito del cliente è Microsoft contratto Enterprise, i costi delle licenze software verranno fatturati usando questo metodo di fatturazione come costo dettagliato, separato da eventuali costi di utilizzo specifici di Azure.

**Carte di credito e fattura mensile**: i clienti possono pagare usando una carta di credito e una fattura mensile. In questo caso, le tariffe della licenza software verranno fatturate come nello scenario Enterprise Agreement, ovvero come costo dettagliato, separato da eventuali costi di utilizzo specifici di Azure.

**Crediti gratuiti e impegno monetario**: alcuni clienti scelgono di pagare in anticipo Azure con un impegno monetario nel contratto Enterprise o hanno fornito crediti gratuiti da usare per l'utilizzo di Azure. Anche se questi crediti possono essere usati per pagare l'utilizzo di Azure, non possono essere usati per pagare costi di licenza del software dell'editore.

**Fatturazione e raccolte**: la fatturazione delle licenze software dell'editore viene presentata usando il metodo di fatturazione selezionato dal cliente e segue la sequenza temporale di fatturazione. Per i clienti senza un contratto Enterprise Agreement appropriato le licenze del software del marketplace vengono fatturate mensilmente. Per i clienti con Enterprise Agreement viene emessa una fattura mensile inviata ogni trimestre.

Quando si selezionano i modelli di prezzi di sottoscrizione o con pagamento in base al consumo (detti anche basati sull'utilizzo), Microsoft funge da agente dell'editore ed è responsabile di tutti gli aspetti della fatturazione, dei pagamenti e della raccolta.

### <a name="publisher-payout-and-reporting"></a>Proventi dell'editore e creazione di report

Eventuali costi di licenza software riscossi da Microsoft in funzione di agente sono soggetti a una commissione sulle transazioni del 20%, se non diversamente specificato, e vengono dedotti al momento del pagamento dell'editore.

I clienti effettuano in genere gli acquisti tramite il contratto Enterprise Agreement o un contratto con pagamento in base al consumo abilitato per le carte di credito. Il tipo di contratto determina le date di fatturazione, riscossione e pagamento.

>[!NOTE]
>Tutti i report e le informazioni dettagliate per l'opzione di pubblicazione delle transazioni sono disponibili nella sezione di analisi del Centro per i partner.

#### <a name="billing-questions-and-support"></a>Domande sulla fatturazione e supporto

Per ulteriori informazioni e criteri legali, vedere il [contratto di Microsoft Publisher](https://go.microsoft.com/fwlink/?LinkID=699560). Per informazioni sulle domande di fatturazione, contattare il supporto per gli [editori del Marketplace commerciale](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Requisiti delle transazioni

In questa sezione vengono analizzati i requisiti delle transazioni per i diversi tipi di offerte.

### <a name="requirements-for-all-offer-types"></a>Requisiti per tutti i tipi di offerte

- Per l'opzione di pubblicazione delle transazioni, sono necessari un account Microsoft e informazioni finanziarie, indipendentemente dal modello di determinazione prezzi dell'offerta.
- Le informazioni finanziarie obbligatorie includono l'account di pagamento e il profilo fiscale.

Per ulteriori informazioni sulla configurazione di questi account, vedere [gestire l'account del Marketplace commerciale nel centro per i partner](partner-center-portal/manage-account.md).

### <a name="requirements-for-specific-offer-types"></a>Requisiti per tipi di offerte specifici

La possibilità di eseguire transazioni tramite Microsoft è disponibile solo per i tipi di offerta del Marketplace commerciale seguenti. Questo elenco include i requisiti per rendere questi tipi di offerta transazionali nel Marketplace commerciale.

- **Applicazione Azure (modello di soluzione e piani di applicazioni gestite**: in alcuni casi, le spese di utilizzo dell'infrastruttura di Azure vengono passate al cliente separatamente dai costi delle licenze software, ma con lo stesso rendiconto di fatturazione). Tuttavia, se si configura un piano di app gestite per gli addebiti per l'infrastruttura ISV, le risorse di Azure vengono fatturate all'editore e il cliente riceve una tariffa fissa che include il costo dell'infrastruttura, le licenze software e i servizi di gestione.

- **Macchina virtuale di Azure**: consente di scegliere tra modelli di prezzo gratuito, BYOL o basato sull'utilizzo. Nella fattura di Azure del cliente Microsoft indica i costi della licenza del software dell'editore separatamente dai costi dell'infrastruttura di Azure sottostante. Le tariffe dell'infrastruttura di Azure sono basate sull'uso del software dell'editore.

- **Applicazione SaaS**: deve essere una soluzione multi-tenant, usare [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) per l'autenticazione e integrarsi con le [API di evasione Saas](partner-center-portal/pc-saas-fulfillment-api-v2.md). L'utilizzo dell'infrastruttura di Azure viene gestito e fatturato direttamente all'utente (editore), quindi è necessario tenere conto delle tariffe di utilizzo dell'infrastruttura di Azure e delle tariffe di licenza software come singolo costo. Per istruzioni dettagliate, vedere [How to plan an Saas offer for the Commercial Marketplace](plan-saas-offer.md#plans).

## <a name="private-plans"></a>Piani privati

È possibile creare un piano privato per un'offerta, completando i prezzi negoziati, specifici per l'offerta o le configurazioni personalizzate.

I piani privati consentono di fornire prezzi superiori o inferiori a clienti specifici rispetto all'offerta pubblicamente disponibile. I piani privati possono essere usati per scontare o aggiungere un Premium a un'offerta. I piani privati possono essere resi disponibili per uno o più clienti elencando la sottoscrizione di Azure a livello di piano.

## <a name="next-steps"></a>Passaggi successivi

- Per esempi su come la soluzione è mappata a un tipo di offerta e a una configurazione, vedere la pagina relativa ai modelli di pubblicazione tramite Online Store.
- [Guida alla pubblicazione in base al tipo di offerta](publisher-guide-by-offer-type.md).
