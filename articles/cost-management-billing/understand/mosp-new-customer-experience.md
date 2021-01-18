---
title: Introduzione all'account di fatturazione di Azure aggiornato
description: Introduzione all'account di fatturazione di Azure aggiornato per illustrare le modifiche apportate alla nuova esperienza di fatturazione e gestione dei costi
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 01/11/2021
ms.author: banders
ms.openlocfilehash: f0645115246995c9605563626d99bbf6a76784e1
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133565"
---
# <a name="get-started-with-your-updated-azure-billing-account"></a>Introduzione all'account di fatturazione di Azure aggiornato

La gestione dei costi e delle fatture è uno dei componenti principali dell'esperienza cloud. Consente di controllare e conoscere la spesa per il cloud. Per semplificare la gestione dei costi e delle fatture, Microsoft sta aggiornando l'account di fatturazione di Azure in modo da includere funzionalità migliorate di fatturazione e gestione dei costi. Questo articolo descrive gli aggiornamenti dell'account di fatturazione e illustra le nuove funzionalità.

> [!IMPORTANT]
> L'account verrà aggiornato al ricevimento di un messaggio di posta elettronica da Microsoft che notifica gli aggiornamenti apportati all'account. La notifica viene inviata 60 giorni prima dell'aggiornamento dell'account.

## <a name="more-flexibility-with-your-new-billing-account"></a>Maggiore flessibilità con il nuovo account di fatturazione

Il diagramma seguente confronta l'account di fatturazione precedente e quello nuovo:

![Diagramma che illustra il confronto tra la gerarchia di fatturazione nell'account precedente e in quello nuovo](./media/mosp-new-customer-experience/comparison-old-new-account.png)

Il nuovo account di fatturazione contiene uno o più profili di fatturazione che consentono di gestire le fatture e i metodi di pagamento. Ogni profilo di fatturazione contiene una o più sezioni della fattura che consentono di organizzare i costi per la fattura del profilo di fatturazione.

![Diagramma che illustra la nuova gerarchia di fatturazione](./media/mosp-new-customer-experience/new-billing-account-hierarchy.png)

I ruoli nell'account di fatturazione hanno il livello di autorizzazioni più elevato. Questi ruoli dovranno essere assegnati agli utenti che devono visualizzare le fatture e tenere traccia dei costi per l'intero account, ad esempio ai responsabili finanziari o IT di un'organizzazione o alla persona che ha effettuato l'iscrizione per un account. Per altre informazioni, vedere [Ruoli e attività dell'account di fatturazione](../manage/understand-mca-roles.md#billing-account-roles-and-tasks). Quando l'account viene aggiornato, all'utente con ruolo di amministratore account nell'account di fatturazione precedente viene assegnato un ruolo di proprietario per il nuovo account.

## <a name="billing-profiles"></a>Profili di fatturazione

Un profilo di fatturazione consente di gestire la fattura e i metodi di pagamento. Viene generata una fattura mensile all'inizio del mese per ogni profilo di fatturazione incluso nell'account. La fattura contiene i rispettivi addebiti relativi al mese precedente per tutte le sottoscrizioni associate al profilo di fatturazione.

Quando l'account viene aggiornato, viene creato automaticamente un profilo di fatturazione per ogni sottoscrizione. Gli addebiti della sottoscrizione vengono fatturati al rispettivo profilo di fatturazione e visualizzati nella relativa fattura.

Ai ruoli nei profili di fatturazione sono assegnate le autorizzazioni per visualizzare e gestire fatture e metodi di pagamento. Questi ruoli dovranno essere assegnati agli utenti che pagano le fatture, ad esempio ai membri del team di contabilità di un'organizzazione. Per altre informazioni, vedere [Ruoli e attività del profilo di fatturazione](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

Quando l'account viene aggiornato, per ogni sottoscrizione per cui si è concessa ad altri l'autorizzazione per la [visualizzazione delle fatture](download-azure-invoice.md#allow-others-to-download-the-your-subscription-invoice), agli utenti con ruolo Proprietario, Collaboratore, Lettore o Fatturazione per lettore di Azure viene assegnato il ruolo di lettore per il rispettivo profilo di fatturazione.

## <a name="invoice-sections"></a>Sezioni della fattura

Una sezione della fattura consente di organizzare i costi nella fattura. Ad esempio, potrebbe essere necessaria una singola fattura pur volendo organizzare i costi per reparto, team o progetto. Questo scenario prevede un singolo profilo di fatturazione in cui viene creata una sezione della fattura per ogni reparto, team o progetto.

Quando l'account viene aggiornato, viene creata una sezione della fattura per ogni profilo di fatturazione e la sottoscrizione correlata viene assegnata alla sezione della fattura. Quando si aggiungono altre sottoscrizioni, è possibile creare sezioni aggiuntive e assegnare le sottoscrizioni alle sezioni della fattura. Le sezioni verranno visualizzate nella fattura del profilo di fatturazione e rispecchieranno l'utilizzo di ogni sottoscrizione assegnata.

I ruoli nella sezione della fattura dispongono delle autorizzazioni per controllare chi crea le sottoscrizioni di Azure. I ruoli dovranno essere assegnati agli utenti che configurano l'ambiente Azure per i team di un'organizzazione, ad esempio a responsabili e architetti tecnici. Per altre informazioni, vedere [Ruoli e attività della sezione della fattura](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="enhanced-features-in-your-new-experience"></a>Funzionalità migliorate nella nuova esperienza

La nuova esperienza consente di gestire facilmente i costi e le fatture grazie alle funzionalità di fatturazione e gestione dei costi seguenti.

#### <a name="invoice-management"></a>Gestione delle fatture

**Periodo di fatturazione mensile più prevedibile**: nel nuovo account, il periodo di fatturazione inizia il primo giorno del mese e termina l'ultimo giorno del mese, indipendentemente da quando viene effettuata l'iscrizione per l'uso di Azure. All'inizio di ogni mese verrà generata una fattura che conterrà tutti gli addebiti del mese precedente.

**Ricevere una singola fattura mensile per più sottoscrizioni**: è possibile ricevere una fattura mensile per ogni sottoscrizione oppure una singola fattura per più sottoscrizioni.

**Ricevere una singola fattura mensile per le sottoscrizioni di Azure, i piani di supporto e i prodotti di Azure Marketplace**: si riceverà un'unica fattura mensile per tutti gli addebiti, che includerà gli addebiti per l'utilizzo delle sottoscrizioni di Azure, i piani di supporto e gli acquisti in Azure Marketplace.

**Raggruppare i costi nella fattura in base alle esigenze**: è possibile raggruppare i costi nella fattura in base alle esigenze, per reparti, progetti o team.

**Impostare un numero di ordine d'acquisto facoltativo per la fattura**: per associare la fattura ai sistemi finanziari interni, impostare un numero di ordine d'acquisto. Potrà essere gestito e aggiornato in qualsiasi momento nel portale di Azure.

#### <a name="payment-management"></a>Gestione dei pagamenti

**Pagare le fatture immediatamente con una carta di credito**: non è necessario attendere l'addebito del pagamento automatico sulla carta di credito. È possibile usare qualsiasi carta di credito per pagare una fattura in scadenza o scaduta nel portale di Azure.

**Tenere traccia di tutti i pagamenti applicati all'account**: visualizzare tutti i pagamenti applicati all'account, con il metodo di pagamento usato, l'importo pagato e la data di pagamento, nel portale di Azure.

#### <a name="cost-management"></a>Gestione dei costi

**Pianificare esportazioni mensili dei dati di utilizzo in un account di archiviazione**: è possibile pubblicare automaticamente i dati relativi ai costi e all'utilizzo in un account di archiviazione su base giornaliera, settimanale o mensile.

#### <a name="account-and-subscription-management"></a>Gestione dell'account e delle sottoscrizioni

**Assegnare più amministratori per le operazioni di fatturazione**: assegnare le autorizzazioni per la fatturazione a più utenti che gestiranno la fatturazione per l'account. Per una maggiore flessibilità, concedere ad altri autorizzazioni di lettura, di scrittura o sia di lettura che di scrittura.

**Creare sottoscrizioni aggiuntive direttamente nel portale di Azure**: creare tutte le sottoscrizioni con una singola selezione nel portale di Azure.

#### <a name="api-support"></a>supporto API

**Eseguire operazioni di fatturazione e di gestione dei costi tramite API, SDK e PowerShell**: usare le API di gestione dei costi, fatturazione e utilizzo per eseguire il pull dei dati relativi ai costi e alla fatturazione negli strumenti di analisi dei dati preferiti.

**Eseguire tutte le operazioni relative alle sottoscrizioni tramite API, SDK e PowerShell**: usare le API per le sottoscrizioni di Azure per automatizzare la gestione delle sottoscrizioni di Azure, ad esempio la creazione, la ridenominazione e l'annullamento di una sottoscrizione.

## <a name="get-prepared-for-your-new-experience"></a>Preparazione per la nuova esperienza

Di seguito sono riportate le operazioni consigliate per prepararsi per la nuova esperienza.

**Periodo di fatturazione mensile e data di fatturazione diversa**

Nella nuova esperienza, la fattura verrà generata intorno al nono giorno del mese e conterrà tutti gli addebiti del mese precedente. Questa data potrebbe differire dalla data di generazione della fattura nell'account precedente. Se si condividono le fatture con altri, notificare il cambiamento della data.

**Nuove API di fatturazione e gestione dei costi**

Se si usano API di Gestione dei costi e fatturazione per eseguire query e aggiornare i dati relativi alla fatturazione o ai costi, è necessario usare le nuove API. La tabella seguente riporta l'elenco delle API che non funzioneranno con il nuovo account di fatturazione e le modifiche da apportare nel nuovo account di fatturazione.

|API | Modifiche  |
|---------|---------|
|[Account di fatturazione - Elenco](/rest/api/billing/2019-10-01-preview/billingaccounts/list) | Nell'API di elenco degli account di fatturazione il valore di agreementType per l'account di fatturazione precedente è **MicrosoftOnlineServiceProgram**, mentre per il nuovo account di fatturazione il valore di agreementType sarà **MicrosoftCustomerAgreement**. Se è presente una dipendenza da agreementType, aggiornarne il valore. |
|[Fatture - Elenco per sottoscrizione di fatturazione](/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | Questa API restituirà solo le fatture generate prima dell'aggiornamento dell'account. Per ottenere le fatture generate nel nuovo account di fatturazione, sarà necessario usare l'API illustrata in [Fatture - Elenco per account di fatturazione](/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount). |

## <a name="cost-management-updates-after-account-update"></a>Aggiornamenti di Gestione costi dopo l'aggiornamento dell'account

L'account di fatturazione di Azure aggiornato per il Contratto del cliente Microsoft consente di accedere a esperienze di Gestione costi nuove e ampliate nel portale di Azure che non erano disponibili nell'account con pagamento in base al consumo.

### <a name="new-capabilities"></a>Nuove funzionalità

Con l'account di fatturazione di Azure sono disponibili le nuove funzionalità seguenti.

#### <a name="new-billing-scopes"></a>Nuovi ambiti di fatturazione

L'account aggiornato include nuovi ambiti in Gestione dei costi e fatturazione. Oltre a facilitare l'organizzazione gerarchica e la fatturazione, questi ambiti consentono anche di visualizzare gli addebiti combinati di più sottoscrizioni sottostanti. Per altre informazioni sugli ambiti di fatturazione, vedere [Ambiti del Contratto del cliente Microsoft](../costs/understand-work-scopes.md#microsoft-customer-agreement-scopes).

È anche possibile accedere alle API di Gestione costi per ottenere visualizzazioni combinate dei costi in ambiti più elevati. Tutte le API di Gestione costi che usano l'ambito della sottoscrizione sono ancora disponibili con alcune modifiche minime nello schema. Per altre informazioni sulle API, vedere [API di Gestione costi di Azure](/rest/api/cost-management/) e [API sul consumo di Azure](/rest/api/consumption/).

#### <a name="cost-allocation"></a>Allocazione dei costi

Con l'account aggiornato, è possibile usare funzionalità di allocazione dei costi per distribuire quelli di servizi condivisi nell'organizzazione. Per altre informazioni sull'allocazione dei costi, vedere [Creare e gestire le regole di allocazione dei costi di Azure](../costs/allocate-costs.md).

#### <a name="power-bi"></a>Power BI

Il connettore Gestione costi di Azure per Power BI Desktop consente di creare visualizzazioni e report personalizzati dell'utilizzo e della spesa di Azure. È possibile accedere ai dati relativi a costi e utilizzo dopo la connessione all'account aggiornato. Per altre informazioni sul connettore Gestione costi di Azure per Power BI Desktop, vedere [Creare oggetti visivi e report con il connettore Gestione costi di Azure in Power BI Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management).

### <a name="updated-capabilities"></a>Funzionalità aggiornate

Con l'account di fatturazione di Azure sono disponibili le funzionalità aggiornate seguenti.

#### <a name="cost-analysis"></a>Analisi dei costi

È possibile continuare a visualizzare e tracciare i costi di utilizzo mensili e ora è anche possibile visualizzare i costi di prenotazioni e acquisti nel Marketplace nell'analisi dei costi.

Con l'account aggiornato, si riceve una singola fattura per tutti gli addebiti di Azure. È anche disponibile una visualizzazione semplificata del calendario mensile in sostituzione della visualizzazione dei periodi di fatturazione disponibile in precedenza.

Se ad esempio per l'account precedente il periodo di fatturazione era compreso tra il 24 novembre e il 23 dicembre, dopo l'aggiornamento il periodo diventa dal 1° novembre al 30 novembre, dal 1° dicembre al 31 dicembre e così via.

:::image type="content" source="./media/mosp-new-customer-experience/billing-periods.png" alt-text="Immagine che mostra un confronto tra i periodi di fatturazione precedenti e nuovi " lightbox="./media/mosp-new-customer-experience/billing-periods.png" :::

#### <a name="budgets"></a>Budget

È ora possibile creare budget per l'account di fatturazione, che consentono di tenere traccia dei costi tra le sottoscrizioni. Con i budget, è anche possibile tenersi aggiornati sugli addebiti per gli acquisti. Per altre informazioni sui budget, vedere [Creare e gestire i budget di Azure](../costs/tutorial-acm-create-budgets.md).

#### <a name="exports"></a>Esportazioni

Il nuovo account di fatturazione offre funzionalità di esportazione migliorate. Ad esempio, è possibile creare esportazioni per i costi effettivi che includono acquisti o costi ammortizzati (i costi di acquisto delle prenotazioni distribuiti durante il periodo di acquisto). È inoltre possibile creare un'esportazione per l'account di fatturazione per ottenere i dati relativi a utilizzo e addebiti in tutte le sottoscrizioni incluse. Per altre informazioni sulle esportazioni, vedere [Creare e gestire dati esportati](../costs/tutorial-export-acm-data.md).

> [!NOTE]
> Le esportazioni create prima dell'aggiornamento dell'account con l'opzione **Esportazione mensile dei costi dell'ultimo mese** includono i dati relativi all'ultimo mese di calendario, non all'ultimo periodo di fatturazione.

Ad esempio, per un periodo di fatturazione compreso dal 23 dicembre al 22 gennaio, il file CSV esportato includerà i dati relativi a costi e utilizzo per tale periodo. Dopo l'aggiornamento, l'esportazione conterrà i dati per il mese di calendario, ad esempio dal 1° gennaio al 31 gennaio e così via.

:::image type="content" source="./media/mosp-new-customer-experience/export-amortized-costs.png" alt-text="Immagine che mostra un confronto tra dati delle esportazioni precedenti e nuovi " lightbox="./media/mosp-new-customer-experience/export-amortized-costs.png" :::

## <a name="additional-information"></a>Informazioni aggiuntive

Altre informazioni sulla nuova esperienza sono disponibili nelle sezioni seguenti.

**Nessun tempo di inattività dei servizi**. L'esecuzione dei servizi di Azure nella sottoscrizione continuerà senza interruzioni. Verrà aggiornata soltanto l'esperienza di fatturazione, senza alcun impatto sulle risorse, sui gruppi di risorse o sui gruppi di gestione esistenti.

**Nessuna modifica alle risorse di Azure**. L'aggiornamento non influisce sull'accesso alle risorse di Azure impostate con Controllo degli accessi in base al ruolo di Azure.

**Disponibilità delle fatture precedenti nella nuova esperienza**. Le fatture generate prima dell'aggiornamento dell'account saranno ancora disponibili nel portale di Azure.

**Fatture per un account aggiornato a metà mese**. Se l'account viene aggiornato a metà mese, si riceverà una fattura per gli addebiti accumulati fino al giorno dell'aggiornamento dell'account. Per il resto del mese si riceverà un'altra fattura. Se un account con una sottoscrizione viene aggiornato il 15 settembre, ad esempio, si riceverà una fattura per gli addebiti accumulati fino al 15 settembre. Per il periodo compreso tra il 15 e il 30 settembre verrà emessa un'altra fattura. Dopo settembre, si riceverà un'unica fattura al mese.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'account di fatturazione, vedere gli articoli seguenti:

- [Informazioni sui ruoli amministrativi per il nuovo account di fatturazione](../manage/understand-mca-roles.md)
- [Creare una sottoscrizione di Azure aggiuntiva per il nuovo account di fatturazione](../manage/create-subscription.md)
- [Creare sezioni nella fattura per organizzare i costi](../manage/mca-section-invoice.md)